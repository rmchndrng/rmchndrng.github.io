----------
* auto-gen TOC:
{:toc}

----------

## Introduction
This is Part 3 of a series of journals (Express Angular2 application using Typescript)

The code can be found here. [https://github.com/rmchndrng/XprssNg2TSSeed](https://github.com/rmchndrng/XprssNg2TSSeed)

Part 1 - [Hello World]({{site.baseurl}}/2016/04/23/express-angular2-application-using-typescript-server-setup.html)
Part 2 - [Angular2 App]({{site.baseurl}}/2016/05/18/express-angular2-application-using-typescript-Angular2-application.html)


----------

## Common code

----------
One of the main advantages of using NodeJS for web application development is writting both the server and client logic in the same language, Javascript.
We are trying to implement both Client(Angular2 app) and the Server(ExpressJS app) using javascript/typescript. 
There will be a lot of scenarios where we find ourselves writting the same code which will be used in both the applications. eg. Data models, Utility or helper methods etc
Implementing the correct workflow for sharing the code, will really boost the development process.

### Adding the common folder

We will place all the code which is being shared in a folder by the name "common"

Now the folder structure of the whole project will look like this.

![Folder structure]({{site.baseurl}}/assets/imgs/xpressng2tsseed/9.JPG)
	
### Copy the code to server and client

Current client application build strategy

	ng build -w --output-path ../server/client

This will build in watch mode and places the output inside server/client folder

Current server application build strategy

	gulp.watch('server/**/*.ts', ['build:server']);
	
Using gulp watch, watch over all the typescript files in the server folder and build/transpile whenever a change is detected.

Since we are already transpiling the typescript code inside both client and server folders, we just need to copy the typescript files added inside the common folder into both client and server folders.
This can be achieved using a simple gulp task.

	gulp.task('copy:common', function () {
    	gulp.src('common/**/*.ts')        
    	.pipe(gulp.dest('server/common'))
    	
		gulp.src('common/**/*.ts')        
    	.pipe(gulp.dest('client/src/common'))
	});
	
	gulp.task('watch-common', function () {
    	gulp.watch('common/**/*.ts', ['copy:common']);
	});
	
Modify the build task

	gulp.task('build', function (callback) {
    	runSequence('copy:common','build:server','watch-common','watch-server','build:client:watch', callback);
	});
	
Run the build task, Ctrl+Shift+B in VS Code.

### Adding a common code

We will add a simple data model class "Student.ts" which is going to be used by both the client and the server.

	common/models/student.ts
	export class Student{
    	private _Name : string;
    	public get Name() : string {
			return this._Name;
    	}
    	public set Name(v : string) {
			this._Name = v;
    	}    
		private _Age : number;
		public get Age() : number {
			return this._Age;
		}
		public set Age(v : number) {
			this._Age = v;
		}
	}
	
If the build task is running.(Watchers are watching over the common folder, server folder and the client folder.)
	
As soon as you save the file, it will be copied to both server and client folder.(By the gulp watcher on common folder.)
Build/transpile the new typescript file into javascript inside the server folder(By the gulp watcher on server typescript files, which calls the build:server task)
Build/transpile the new typescript file into javascript inside the client folder and places it in server/client folder(By the ng build command in watch mode)

### Using the common code

In the server app

	server/serverApp.ts
	
	//Import the student class
	import {Student} from './common/models/student'
	
	//Dummy code to show the usage, in the constructor
	constructor() {
		this._App = express();
		var testStudent = new Student();
		testStudent.Age = 29;
		testStudent.Name = "Ram";
		testStudent.Sex = "M";
    }
	
In the client app

	client/src/app/client.component.ts
	
	//Import the student class
	import {Student} from '../common/models/student'
	
	//Dummy code to show the usage, in the constructor
	constructor() {
		var testStudent = new Student();
		testStudent.Age = 29;
		testStudent.Name = "Ram";
		testStudent.Sex = "M";
	}

### Hiding the copied folders from VSCode workspace

The common code which was copied to both the client and server folders will be visible in the the VSCode project explorer. We might accidently go ahead and edit the typescript files inside the copied folder.
To avoid this, we can hide them from the workspace using the VSCode Workspace level settings.

Add the following to the files.exclude property

	"client/src/common":true,
	"server/common":true,
	"server/client":true
	
----------

## Conclusion

**Succesfully setup a workflow to share common Typescript code across Server(ExpressJS) and Client(Angular2) typescript applications!!!**

----------

**TODOs**

In the following journals, I will try to document the steps to achieve the following goals.

1. Live reloading of the application.
2. Debugging the ExpressJS Server application using VS Code
3. Debugging the Angular2 Client application using VS Code

## References

1. [Code in Real-world TypeScript with Node.js, Step by Step](https://vane.life/2015/11/22/code-in-real-world-typescript-step-by-step/)
2. [Practical TypeScript Development with Gulp and Sublime Text 3](https://www.airpair.com/typescript/posts/typescript-development-with-gulp-and-sublime-text)
3. [Angular2 TypeScript Gulp and ExpressJS](http://blog.edenmsg.com/angular2-typescript-gulp-and-expressjs/)