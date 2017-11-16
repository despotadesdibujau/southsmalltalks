# Build your application from the Pharo-minimal image.
Create a deployable image just  loading the application on the minimal image and disabling the command line handlers.
### Tools necessaries for this approach
- Dependency Analyser
- Minimal Image
- Metacello
- ConfigurationOf or BaselineOf is needed
- ConfigurationCommandLineHandler 
- Smalltalk scripts
### The minimal image process
- Create an entry point object
- Use the Dependency Analyser for detect and add the dependencies to BaselineOf or ConfigurationOf
- Create a BaselineOf or ConfigurationOf
- Disable code loading
- Remove the changes file
- Remove the sources file
### Registering the Entry Point Object

```st
MyApplicationEntryPoint class>>#initialize
    SessionManager default
            register: (ClassSessionHandler forClassNamed: 
         self name)
            inCategory: SessionManager default 
                      userCategory
```


### StartUp and ShutDown
```st
MyApplicationEntryPoint class>>startUp: arg1
    MyApplicationManager instance startAlgorithm


MyApplicationEntryPoint class>>shutDown: arg1
    MyApplicationManager instance stopAlgorithm
 ```
 
### Get the minimal image and use the VM that we was used to development process
(linux)
```sh
curl get.pharo.org/61-minimal
./pharo Pharo-minimal.image
This interpreter (vers. 68021) cannot read image file (vers. 6521).
Press CR to quit...

```
 ### Get the minimal image and use the 32 bits VM
 
```sh
 curl get.pharo.org/vm61 | bash
curl get.pharo.org/61-minimal | bash
./pharo Pharo-minimal.image eval “2+2”
4
```
 ### Order your ideas
 # Your aplication environment

- Create a development directory
- Create a exe-factory directory
- Create the deploy directory
- Save the minimal image in the exe-factory directory and in the deploy directory
- Save the 32 bits VM exe-factory directory and in the deploy directory
- Proof that all works
.

 * development
   * myApplication.image
   * myApplication.changes
   * sources
   * vm
 * exe-factory
   * myApplication.image
   * myApplication.changes
   * sources
   * scripts_to_load_code
 * deployment
   * myApplication.image
   * vm


### Load your application in the minimal image
The next script doesn't work with GitHub and Filetree repositories. 
With SmalltalkHub works.
```sh
./pharo Pharo-minimal.image config \ repositoryOfMyApplication\ BaselineOfMyApplication\ --install=baseline

```


If you try do the same thing with PharoConsole in Windows doesn't work.
```sh
For Windows you can load your application using Smalltalks's scripts
 PharoConsole.exe Pharo-minimal.image loadMyApplication.st
```
### Script Example

```st
Metacello new
baseline: 'MyApplication';
filetreeDirectory:'pathToMyApplication';
load.
SmalltalkImage current   saveImageInFileNamed:'myApp.image'.
SmalltalkImage current snapshot: true andQuit: true
```
This should work but some dependencies of filetree aren't loaded in the minimal image, so you need load these dependencies.
When you was loaded these dependencies execute Smalltalk recompile and save the image.
Rename the image and try to load your application.
### Dependencies of filetree in order of dependency (from botton to top)

  - [STON-Core](https://ci.inria.fr/pharo-contribution/job/EnterprisePharoBook/lastSuccessfulBuild/artifact/book-result/STON/STON.html)
  - [MonticelloFileTree-Core](http://smalltalkhub.com/mc/Pharo/Pharo60/MonticelloFileTree-Core-TheIntegrator.152.mcz)
  - [Metacello-GitBasedRepository](http://smalltalkhub.com/mc/Pharo/Pharo60/Metacello-GitBasedRepository-TheIntegrator.24.mcz)
  - [Metacello-GitHub](http://smalltalkhub.com/mc/Pharo/Pharo60/Metacello-GitHub-TheIntegrator.58.mcz)
  
  I recomend to extract them from the full image.
  
 ### Workaround 
 -Load your application using MCCacheRepository
 ```st
 Gofer it repository: MCCacheRepository default ;
	 package: #'MyApplication-package';
	 load.
SmalltalkImage current changeImagePathTo:'myApplication.image';
		snapshot: true andQuit:true
```
 -Push your code on [smalltalkHub](http://smalltalkhub.com/) and load from there 
