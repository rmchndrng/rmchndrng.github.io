----------
* auto-gen TOC:
{:toc}

----------

## Trying out NativeScript with Angular2 - HelloWorld Notes

Ref - http://docs.nativescript.org/angular/tutorial/ng-chapter-0

## Installation

Ref - http://docs.nativescript.org/angular/start/quick-setup

### Installing NativeScript

        npm install -g NativeScript

### Setting up the environment for Android development on Windows

        @powershell -NoProfile -ExecutionPolicy Bypass -Command "iex ((new-object net.webclient).DownloadString('https://www.nativescript.org/setup/win'))"

### Verifying development environment

        tns doctor

## Creating a blank application with Angular2 template

Ref - http://docs.nativescript.org/angular/tutorial/ng-chapter-1

        tns create --ng TnsNg2

### Adding Android development platform

        tns platform add android

### Setting up Android Virtual Device(AVD)

Ref -http://developer.telerik.com/featured/using-android-emulator-hybrid-mobile-apps-telerik-appbuilder/#android

>Add the path of your Android SDK tools folder to the Path Environment variable.

>To open Android SDK manager, run the following in cmd
        
        android

>To manage AVDs, run the following in cmd

        android avd
        
> Add a new AVD

#### Install Intel HAXM

>On modern machine with an Intel chip that supports Intel VT-x, EM64T, and Execute Disable Bit functionality, you can take advantage of Intel’s Hardware Accelerated Execution Manager (HAXM). HAXM dramatically speeds up your Android emulator environment to a point where it makes it actually usable.
>Ensure Intel Virtualization Technology is enabled on the machine.
>Run the installation.

#### Running an x86 avd is 10x faster

>Download the x86 Atom System Images and the Intel Hardware Accelerated Execution Manager Driver using Android SDK Manager.

Ref - http://stackoverflow.com/a/13856866/2382414

### Running the application

        tns run android --emulator 

## Github repository for this project

        git clone https://github.com/rmchndrng/tnsng2.git

### Branch

        git checkout 1helloworld