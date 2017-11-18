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
- [x] Create an entry point object
- [x] Use the Dependency Analyser for detect and add the dependencies to BaselineOf or ConfigurationOf
- [x] Create a BaselineOf or ConfigurationOf
- [x] Load your application on the minimal image
- [ ] Disable code loading
- [x] Remove the changes file
- [x] Remove the sources file
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
 
### If We get the minimal image and use the VM that we was used to development process wont work
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


If you try do the same thing with PharoConsole in Windows but doesn't work.
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

```st
Metacello new
baseline:'ToDoApplication';
repository: 'github://despotadesdibujau/seaside-todo-application:master';
load.
SmalltalkImage current   saveImageInFileNamed:'myApp.image'.
SmalltalkImage current snapshot: true andQuit: true
```
This should work but some dependencies of filetree aren't loaded in the minimal image, so you need load these dependencies.
When you was loaded these dependencies execute Smalltalk recompile and save the image.
Rename the image and try to load your application.
### Dependencies of filetree in order of dependency (from botton to top)

  - [STON-Core](https://ci.inria.fr/pharo-contribution/job/EnterprisePharoBook/lastSuccessfulBuild/artifact/book-result/STON/STON.html)
  - [MonticelloFileTree-Core](https://gist.github.com/despotadesdibujau/c4e60ba35430f650ae1d3a9d52dc8788)
  - [Metacello-GitBasedRepository](https://gist.github.com/despotadesdibujau/172b75e19970b3ceb5ba2337e53c3088)
  - [Metacello-GitHub](https://gist.github.com/despotadesdibujau/053dcd8ff12bf84977aaf862b70fac76)
  
 [With this script](https://gist.github.com/despotadesdibujau/804115f5be69554a047ad2e3ecd0b201) you can prepare the minimal image to load your application from github and filetree (proven)
 ### Example for Windows
 [script-to-prepare-minimal-image.st](https://gist.github.com/despotadesdibujau/804115f5be69554a047ad2e3ecd0b201)
 ```bash
 PharoConsole.exe Pharo-minimal.image script-to-prepare-minimal-image.st
PharoConsole.exe minimal-ready.image loadMyApplication.st
PharoConsole.exe myApplication.image
 ```

 ### Disable the reads to sources and changes
 [Wiht this script you can disable the calls to changes and sources](https://gist.github.com/despotadesdibujau/5c26d74763005a7e6bcf67954de811ef)
 
 
### Full example
Firts of all:
- create a directory for the application
- go to directory just created and create a file called loadMetacello.st and paste this [code](https://gist.github.com/despotadesdibujau/804115f5be69554a047ad2e3ecd0b201)
- create a file called loadApplication.st and paste this [code](https://gist.github.com/despotadesdibujau/27e07f8fee24007675d747da22da3aaa)
- create a file called disableChangesAndSources.st and paste this [code](https://gist.github.com/despotadesdibujau/5c26d74763005a7e6bcf67954de811ef)
- create a file called welcome.st and paste this code:
```st
2+2
```
- and then paste the below in a terminal
 ```sh
curl get.pharo.org/vm61 | bash \
&& curl get.pharo.org/61-minimal | bash \
&& ./pharo Pharo.image loadMetacello.st \
&& ./pharo minimal-ready.image loadApplication.st \
&& ./pharo todoApplication.image disableChangesAndSources.st \
&& rm *.changes \
&& rm pharo-vm/lib/pharo/5.0-201707201942/PharoV60.sources \
&& ./pharo todoApplication.image welcome.st
 ```
In the browser go to http://localhost:8080/examples/todo
If everithing is ok you should to see a todoApplication of Seaside's examples, if not send me an email despotadesdibujau@gmail.com


 ### Next and last step:
 - Unload the posibilities to change the application in production
 
