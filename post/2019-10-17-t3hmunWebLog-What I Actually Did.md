# t3hmun.WebLog - What I Actully Did

https://github.com/t3hmun/WebLog

* Made a simple ASP .net Core 3 site that generates pages from MD
    * The markdown posts live in a sub-repo
    * https://github.com/t3hmun/posts
* Tried to use Azure Pipeline and Azure for build and deployment
    * Discovered Microsoft don't do Core 3 on Azure / Azure DevOps yet ><
    * Realised that this is the excuse to learn some docker I needed 
* Shoved it in Docker for build and deployment
    * BuildWebCompiler isn't linux docker friendly so I dropped it
    * Replaced it with SassC called as a separate build step in docker
    * Docker is surprisingly easy
* Linked docker hub directly to the GitHubRepo
    * __Docker Hub is now my build server__
    * Azure pipelines is a waste of space, Docker is the future
    * https://hub.docker.com/r/t3hmun/t3hmun
* Told Azure to publish the Docker container
    * https://t3hmun.azurewebsites.net/
    * Free tier
    * It just works, pretty sweet
    * The cold server start-up is horrific
    * The page loads are instant _nice_


At this point I was meant to move onto simple ASP caching but I decided that publishing posts needed to be easier.
While struggling with moving parts of using Git for storing, accessing and updating data, I had an epiphany.
That is what databases are for.
__Then I jumped down the rabbit hole of cloud tech.__

In short I'm going to re-write everything as a set of completely decoupled compute functions.
Somehow I'm going to architect it so it isn't tied down to Azure too.
I'll write another article when I know what this actually means.
Sounds totally achievable :)

In the mean time this Azure published site will replace the old site. Primitive but functional.