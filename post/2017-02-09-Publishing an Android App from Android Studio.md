{"description":"The details and reasons of how to properly prepare a Android App for release in Android Studio."}

## What

The documentation on how to publish an Android app to the Google Play store isn't particularly informative.

This article explains all the tools and concepts involved and **why**.

## Configuring your Application for Release

### Removing Log Calls

There are two big reasons that Google demands that Log calls are removed from the release version of an app. 
The first is security, it is very easy to leak sensitive data via log calls.
The second reason is performance, the user has no use for these calls.

Manually removing Log calls is silly and it is not how any good programmer operates.
The default project configuration does not automatically remove Log calls but it can easily be done using Proguard.

The Proguard tool is commonly used to shrink, optimise and obfuscate Android apps (and many other Java programs).
By default the app `build.gradle` is set to use Proguard on release build with a config that turns optimisation off.
Even with optimisation off Proguard requires plenty the contents of the config to avoid breaking the app during its shrink step.

```groovy
buildTypes {
    release {
        minifyEnabled false
        proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
    }
}
```

The default file can be found at `\android-sdk\tools\proguard\proguard-android.txt`.

In order to use Proguard to remove Log calls optimize needs to be enabled, 
this is done by changing the default file to  `proguard-android-optimize.txt`.
Again these default files are important because they have a lot of config that prevents Proguard from breaking the app.

Next step is to open the `proguard-rules.pro` file in the app directory and add the rules to remove Log calls.
The []Proguard manual](https://www.guardsquare.com/en/proguard/manual/examples#logging) has the rules needed to do this as an example with some useful explanation.

```
-assumenosideeffects class android.util.Log { 
    public static boolean isLoggable(java.lang.String, int); 
    public static int v(...); 
    public static int i(...); 
    public static int w(...); 
    public static int d(...); 
    public static int e(...); 
} 
```

This rule tells Proguard that these methods don't have side effects.
As a result Proguard will see this code as useless and remove it as part of optimisation.
The only scenario that proguard would keep the Log call is if the return value is kept and used, 
however this should never by used.


### debuggable versionName and versionCode

The documentation tells you to remove `android:debuggable` and set `android:versionName` and `android:versionCode` in the manifest.
This is not helpful advice in modern Android apps.

The `android:debuggable` attribute is no longer set manually, it is automatically added by Android Studio when debugging.
This means that the attribute should never be manually set in your manifest.
It may need to be removed manually if you are working with old code.

The `android:debuggable` and set `android:versionName` attributes are now normally set in the `build.gradle`, 
which automatically sets it in the manifest, overwriting anything set there manually.
So that too should not be manually set in the manifest because it will only lead to confusion.
 
## Building and Signing a Release Version of your Application

Whenever an app is updated, the system will check to see if the app is signed by the same credentials.
If the signatures don't match, the update will be refused.

All builds that are run are signed, including debug builds.
The debug builds use a default debug signature `~/.android/debug.keystore`, which cannot be used for release.

Creating a signature, building and signing is done through a dialog, 
click on the _Build_ menu in Android Studio and then click on _Generate Signed APK..._.
The [documentation on how to make a keys](https://developer.android.com/studio/publish/app-signing.html#release-mode) is not bad so read it for instructions on how to make a new key.

Important points:

* __You must keep the keystore safe.__ If the keystore is lost the app cannot be updated. A new app would have to be made.
* The keystore can be kept in the project repository, but if it is public the password must be long, it is very vulnerable to brute force attacks.
* If the details are entered into the _Project Structure - Signing_ dialog **the password will appear in the build.gradle as plain text**.
This is a massive security problem if the app is open-source or the repositories are not secure. Best avoided.
* If the key expires, new versions of the app cannot be signed. The key expiry should be as long as the app might need updating.

I personally recommend storing the keystore outside of the project repository (especially if you are planning to open source) but keeping it backed up externally.

> I imported a previously used keystore from Xamarin, the keystore password and the key password are the same.
This is why Xamarin only asks for one password, but Android studio asks for 2.


## Debugging The Release Build

The release build uses Proguard to shrink, optimise and obfuscate the code.
Although unlikely to be an issue for small apps, this is a hazardous process and it may break the app.

Editing the debug build to include Proguard is silly, it'll significantly slow down debug runs.
Instead it makes sense to create a new build, specifically for debugging release builds:

```
        release {
            minifyEnabled true
            proguardFiles getDefaultProguardFile('proguard-android-optimize.txt'), 'proguard-rules.pro'
        }
        debug_release {
            debuggable true
            minifyEnabled true
            proguardFiles getDefaultProguardFile('proguard-android-optimize.txt'), 'proguard-rules.pro'
            signingConfig signingConfigs.debug
        }
```

The default debug build isn't specified in the _build.gradle_ but its main features are setting _debuggable true_ and the dbug signing config.
The debug signing config is also something that seems to be generated mysteriously automatically.

> I only use my debug_release build for verifying that I didn't do something catastrophically wrong. 
If you need to do some real debugging you may want to turn Proguard obfuscation off and fiddle with other features.

## What's Next

Next step requires a Google Developer account.
Find the official documentation, follow the steps and upload the signed APK that was created.

Most of the rest of the process is mostly boring.

Tips:

* Start with a Alpha / Beta release to catch any issues before releasing to public.
* Don't bother with proper screenshots for the first upload, they'll probably change by the time the production release is ready.
    * Pressing ctrl+s in the Android emulator will take screenshots and save them to the desktop.
