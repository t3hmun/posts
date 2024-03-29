# A Serverless Website

This article goes through my thought process going from static pages on GitHub, to a server and then back to static pages but this time generated by cloud functions. 
I do a lot of re-inventing the wheel here,
I like to rediscover good practice, the experience provides mental context. 

## How does the term *serverless* make sense? 

I've never liked the term _serverless_, but now its beginning to make sense.
In my mind the default approach to making a website begins with making a server.
In my day-job I write line of business applications, so starting with a big fat ASP MVC server makes sense.
However a static website doesn't need to do any thinking to serve pages.
With Azure I can simple create a [blob storage account and expose it to the internet](https://docs.microsoft.com/en-us/azure/storage/blobs/storage-blob-static-website).
I do not have to *write* the web server hence to me, in terms of work, it is serverless.


## My Previous Thinking Content Generation


### Offline Static Site Generator

I could go back to using a [offline static website generator](https://github.com/t3hmun/t3h-static-site-generator) to build the content.
That was my original approach, using a [GitHub repo](https://github.com/t3hmun/t3hmun.github.io) to serve the pages via Github Pages.
However that results in a tedious and finicky process to publishing new posts, install the generator, regenerate and push.


### A Server

The current approach I am using to have the site generate the pages on the fly.
This is silly inefficient, was simple an excuse to get started with .Net Core 3.
It is nice that all I have to do is push the markdown for the new post and things regenerate.
Well actually Docker Hub rebuilds the entire server for every publish, which is nuts.
Also content should not be mixed up with the server, separate concerns, different reasons to change.

I could fix the design of this server by using a database to store the markdown and a webhook to automatically update when new a new markdown post is pushed.
This separates the blog posts publishing from the server publishing.
Add a bit of caching to the ASP sever and it isn't terrible.

I could go a bit further and make the Razor Pages generate to file, outputting to blob storage and serve content directly from there.
There might be better templating languages for that the (pug?) but I have not looking into that recently.
With fully static content served from blob storage, I don't have to worry about basic caching or the terrible cold start time of a free Azure instance.
The ASP server ony exists to respond to Git webhooks and write static content to the storage. 


## Design with Total Separation of Concerns

There are a few different concerns in my website project that could all be separated out.

* Serving content
* Converting markdown to HTML
* Applying the site layout
* SASS / CSS generation
* Discovering updates in markdown content

All the pieces can be abstracted out into easily swappable modules.
For example I could decide to replace SASS with Stylus.
It'd be a case of swapping out the SASS module.
It should be possible to abstract the Hook module to accept a list of things that process changes, so modules for CSS, SASS, LESS and anything else can run in parallel.

Here's a diagram re[resenting my first draft of this design:

> Note: I need to add nomnoml js to make the diagram appear.

```nomnoml

[<actor>visitor]
[<actor>me]

[<input>Article Change Hook]
[<input>Layout Change Hook]
[<input>Style Change Hook]
[<input>Article HTML Change Hook]

[<database>CSS git repo]
[<database>Article git repo]
[<database>Template git repo]

[<database>Article HTML Table Store]

[Generate SASS]
[Copy CSS]
[Update Static Files]

[Article Engine|
  [Markdown to HTML]->[Extract Metadata]
]

[Layout Engine|
  [Rebuild Changed Pages]->[Generate TOC]
]

[<database> Template git repo]

[Content Server|
[<database> Blob Storage]
]

[visitor]<-[Content Server]

[me]->[CSS git repo]
[me]->[Article git repo]
[me]->[Template git repo]

[CSS git repo]-> [Style Change Hook]
[Article git repo]->[Article Change Hook]
[Template git repo]->[Layout Change Hook]

[Style Change Hook]->[Generate SASS]
[Style Change Hook]->[Copy CSS]
[Generate SASS]->[Content Server]
[Copy CSS]->[Content Server]

[Article Change Hook]->[Update Static Files]
[Article Change Hook]->[Article Engine]
[Article Engine]->[Article HTML Table Store]
[Update Static Files]->[Content Server]


[Article HTML Table Store]->[Layout Engine]
[Layout Change Hook]->[Layout Engine]

[Content Server]<-[Layout Engine]
```

I imagine each node on the diagram is a cloud function or set of functions.
I've never written a cloud function.
It feels like I've discovered the Unix philosophy, 
writing many small programs that do a great job.