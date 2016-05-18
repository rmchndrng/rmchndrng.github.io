----------
* auto-gen TOC:
{:toc}

----------

## Introduction
This is Part 2 of a series of journals (Express Angular2 application using Typescript)

The code can be found here. [https://github.com/rmchndrng/XprssNg2TSSeed](https://github.com/rmchndrng/XprssNg2TSSeed)

In the previous post, we have setup a simple ExpressJS server which serves [Hello World]({{site.baseurl}}/2016/04/23/express-angular2-application-using-typescript-server-setup.html)


----------

## Get a simple Angular2 app up and running


----------
With the release of Angular2 CLI, it is very easy to start off with a basic Angular2 application.

### Install Angular2 CLI

https://github.com/angular/angular-cli

	npm install angular-cli -g
	
### Creating an Angular2 app

We will create the basic application using angular-cli. Since we are planning to keep all the client application code inside a folder named "client", we will run the angular-cli to create a new project by the name "client"

	ng new client
	
This will add all the required modules for a basic angular app to work inside the client folder.

	cd client
	ng build

This will build the application and will be placed in an output folder "dist".
Need to run "ng build" as an administrator in Windows
	
	ng serve
	
This will start a LiveReload server and the application will be served.

### Moving the client code to the server
We are trying to build an expressjs node application which serves an angular2 application. So we need to move the client app that we just created into the expressjs app, so that it can be served by the node server.

We will add a gulp task to build the client application whenever a file has been chaged and copy it over to the server folder.

We do this with the help of a gulp package called "gulp-shell"

	npm install gulp-shell --save-dev
	
Add the following task into gulpfile.js

	gulp.task('build:client:watch', function () {
    	return gulp.src('')
    	.pipe(
			shell(['ng build -w' --output-path ../server/client'],{cwd:'client'})
    	)
	});

Modify the existing build task

	gulp.task('build',['build:server','build:client:watch']);
	
Try pressing the Build command of VS Code [Ctrl+Shift+B]

This will trigger the gulp tasks and the built code of client application will be copied into the "client" folder inside "server" folder.
Note: Since we are calling the "ng build" command, VS Code needs to be open as an administrator in Windows.
----------

## Configuring the ExpressJS application

### Watch server file changes and build

Whenever a typescript file has been changed under the server folder, we need to transpile them. To achieve this we will add a new gulp watch task.

	gulp.task('watch-server', ['build:server'], function () {
    	gulp.watch('server/**/*.ts', ['build:server']);
	});

Modify the existing build task

	gulp.task('build', function (callback) {
		runSequence('watch-server','build:client:watch', callback);
	});
	
### Serve static files

Now we need to configure our express application to serve all the static files under the client folder.

	server/serverApp.ts
	
	//Add an import to path module
	import * as path from 'path';
	
	//Add a method to render index.html on path '/'
	private _RenderIndex(req: express.Request, res: express.Response) {
		res.sendFile(path.resolve(__dirname, 'client/index.html'));
    }
	
	//Modify the setRoutes to serve static files and index.html for the path '/'
	public setRoutes() {
		this._App.use(express.static('client'));
		this._App.get('/', this._RenderIndex);
    }
	
Try running the express application.

	cd server
	node index.js
	
## Conclusion

**The Angular2 ExpressJS application using Typescript is up and running...**

----------

**TODOs**

In the following journals, I will try to document the steps to achieve the following goals.

1. Workflow to share Typescript code across Server and Client applications.
2. Incremental compilation and Live reloading of the application.
3. Debugging the ExpressJS Server application using VS Code
4. Debugging the Angular2 Client application using VS Code

## References

1. [Code in Real-world TypeScript with Node.js, Step by Step](https://vane.life/2015/11/22/code-in-real-world-typescript-step-by-step/)
2. [Practical TypeScript Development with Gulp and Sublime Text 3](https://www.airpair.com/typescript/posts/typescript-development-with-gulp-and-sublime-text)
3. [Angular2 TypeScript Gulp and ExpressJS](http://blog.edenmsg.com/angular2-typescript-gulp-and-expressjs/)