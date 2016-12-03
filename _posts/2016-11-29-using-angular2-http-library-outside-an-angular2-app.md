----------
* auto-gen TOC:
{:toc}

----------

## Introduction
There are many AJAX/Http client libraries for javascript which can be made use of to communicate with the server from a browser.
You can find their reviews/comparisons over the net.

1. <http://andrewhfarmer.com/ajax-libraries/>
2. <https://www.sitepoint.com/comparison-javascript-http-libraries/>

When working on angular2 projects, we all are introduced with the @angular/http module. This module is used in angular2 applications to make 
server side api calls(Server communication). It provides us with a very powerful and easy-to-use http client api which makes use of RxJS observables 
and its operator methods.
<https://angular.io/docs/ts/latest/guide/server-communication.html>

@angular/http supports both XMLHttpRequest and JSONP HTTP based apis.

In this journal I am trying to document the steps required to make use of this library outside an Angular2 application.
I know that including @angular2/http and loading all of its dependencies in an application which is not at all an angular2 app may seem a absurd idea.

## Reasons to try out this experiment
1. It will be useful if your developing an app which may get migrated to an Angular2 app and you want to start writing service layer which conform
to the Angular2 specifications.

2. It can be also one of those crazy scenarios which we face while integrating Angular2 specific code with other javascrit applications.

3. Purely for the fun. :)

4. Because we can. (This can be a good example to show the re-usablility of Angular2 modules outside an Angular2 app context.)

The final source code for this experimnent can be found at <https://github.com/rmchndrng/NG2HttpClientSimpleTSApp>

----------

## Pre-requisites
I am assuming that the reader has a fair knowledget of Typescript, Angular2 and has used @angular2/http. If not I would recommend to go through the Angular2 Quickstart.
<https://angular.io/docs/ts/latest/quickstart.html>

My dev environment is based on Windows OS. The IDE/editor which I am going to use is Microsoft Visual Studio Code(VSCode)

----------


## Project Initialization
1. Create a folder with the name of your project (eg:NG2HttpClientSimpleTSApp)

2. Open the project in VSCode.(ie. Open the folder in VSCode)

3. Open the terminal inside VSCode(Ctrl+~) and run the command "npm init" <https://docs.npmjs.com/cli/init](https://docs.npmjs.com/cli/init>

4. Install Typescript if already not installed. (Run the command "npm install typescript -g --save-dev")

5. Initialze the project to be a typescript project by adding a new config file "tsconfig.json"
	
	**tsconfig.json**
	
		{
			"compilerOptions": {
				"outDir": "./dist/",
				"sourceMap": true,
				"noImplicitAny": true,
				"module": "commonjs",
				"target": "es5",			
				"experimentalDecorators": true,			
				"emitDecoratorMetadata": true        
			},
			"include": [			
				"src/**/*"        
			]
		}	

	Here we are placing all the typescript code of the app inside the "src" folder and the transpiled javascript will be placed in "dist" folder.

6. Add the folders "src" and "dist" under the root of the project.

7. Add a typescript file "app.ts" inside "src"

	**app.ts**
	
		class App {
			public message: string;
		}
		let app = new App();
		app.message = "<h1>Hello world!!</h1>";
		document.write(app.message);
	
8. Add a VSCode task to build the project(Transpile TS code into JS) 

	(Press Ctrl+Shift+B, Click on "Configure Task Runner", Select "Typescript - tsconfig").
	
	This will add a "tasks.json" config file under the folder ".vscode" with default values. We can add "-w" into the arguments list which will watch for TS file changes during our development.
	We are setting the showOutput to be "always", which will open the output panel in VSCode during build and show the messages.

	**tasks.json**
	
		{
			// See https://go.microsoft.com/fwlink/?LinkId=733558
			// for the documentation about the tasks.json format
			"version": "0.1.0",
			"command": "tsc",
			"isShellCommand": true,
			"args": ["-p", ".", "-w"],
			"showOutput": "always",
			"problemMatcher": "$tsc"
		}
	
	Press "Ctrl+Shift+B", this will run the task we have configure. (Which transpiles the TS code and places it in the dist folder.)
9.  Add index.html to the root. This will load the typescript app.

	**index.html**
	
	<html>
	<head>  
		<script src="./dist/app.js"></script>
		<body>
		</body>
	</html>

	
10. Add the "lite-server" module which will run a light weight node server which serves the index.html file.
	
	Run the command "npm install lite-server --save-dev"

	Add an npm command "start" which will start the "lite-server". 
	
	Run the command "npm start".
	
	This will open up your browser with index.html.

	**package.json**

		...
		"scripts": {    	
			"start": "lite-server"
		},
		...

Now you have a basic typescript application loaded in your index.html.

## Add @angular/http module

1. Run the command "npm install @angular/http --save"

2. Install all the dependant modules.

	"npm install rxjs@5.0.0-beta.12 --save"

	"npm install @angular/core@2.2.4 --save"

	"npm install @angular/platform-browser@2.2.4 --save"

	"npm install @angular/common@2.2.4 --save"

	"npm install zone.js@^0.6.21 --save"
	
Now your typescript application is all set to make use of @angular/http module.

## Demo App - List Github repositories of a user.

We will try to make a simple application which makes an http api call to the Github public api endpoint and retrieve all the repositories for a particular user.

We are not going to make use of any client side libraries for to represent the view. Will make use of plain old html and javascript.(Not even jquery.)

1. Add the necessary html markup to capture the username, submit the api request and to display the results into index.html

	**index.html**

		<body>
			<div>
				Username:<input type="text" id="username" /><a href="#" onclick="app.buttonClick()">Submit</a>
			</div>
			<div id="results">
			</div>
		</body>

2. We have added a call to the function "app.buttonClick" in the onclick event of the submit link. This should be defined in the app.js.
An instance of App should be set to a "window" global variable by the name app. So that its method is accessible in the dom.

	**app.ts**

		let app = new App();
		window.app = app;

3. Implement a service class.

	Angular makes use of reusable classes called Services. These classes will be injected into the Angular2 components which need it via constructor.
	Data access is generally refactored into service classes. We will also create a data access service class for the github api calls.

	Add a new file "github.service.ts"

	Will add a method "getRepos(userName:string):Observable<any[]>". This method accepts the username and returns an observable of an array of objects. 
	Since we are not concerned with the data models being used, the objects are of type "any".

		import { Observable } from "rxjs";

	We will follow the Obseravables section in <https://angular.io/docs/ts/latest/tutorial/toh-pt6.html> and implement the service.

	Inorder to make an api call, we need an instance of the class "Http" from @angular/http. Services in an Angular2 app will get an instance via constructor dependancy injection.
	We have to create an instance of our own.

		import { Http } from "@angular/http";

	Adding a private variable to hold the Http instance.

		private http: Http

	Intializing the Http instance in the constructor of the service.

	The constructor of Http takes in 2 parameters. [constructor(_backend: ConnectionBackend, _defaultOptions: RequestOptions);] 
	So first we have to create instance of these types with default values. The following code will successfully create an instance of Http with default values.	

		import { XHRBackend, BrowserXhr, ResponseOptions, CookieXSRFStrategy, RequestOptions} from '@angular/http'

		constructor() {        
			let browserXhr: BrowserXhr = new BrowserXhr();
			let baseResponseOptions: ResponseOptions = new ResponseOptions();
			let xsrfStrategy: CookieXSRFStrategy = new CookieXSRFStrategy();
			let backend: XHRBackend = new XHRBackend(browserXhr, baseResponseOptions, xsrfStrategy);
			let requestOptions: RequestOptions = new RequestOptions();
			let http: Http = new Http(backend, requestOptions);
			this.http = http;
		}

	Implement the "getRepos" function	

		public getRepos(userName: string): Observable<any[]> {
			return this.http
				.get(`https://api.github.com/orgs/${userName}/repos`)
				.map((r: Response) =>
					r.json() as any[]);
		}

4. Implement the app.

	The app will make use of an instance of the GitHubService class to call the getRepos method on submit. 
	In Angular2 components, the instance of a service is injected via constructor, here we have to create an instance for ourselves.

		import { GithubService } from './github.service';

		private githubService: GithubService;
		constructor() {
			this.githubService = new GithubService();
		}

	Add the search() method which will be called by the onSubmit method, when the user clicks the link.

		import { Response } from '@angular/http';

		search(username: string): void {
			let resultHtml: string = 'No results found.'
			this.githubService.getRepos(username).subscribe(response => {
				let repos: any[] = response as any[];
				if (repos && repos.length > 0) {
					resultHtml =
						`<table>
							<tr>
								<th>No.</th>
								<th>Name</th>
								<th>Url</th>
							</tr>`;
					repos.forEach((repo,index) => {
						resultHtml +=
							`<tr>
								<td>${index}</td>
								<td>${repo.name}</td>
								<td>${repo.html_url}</td>
							</tr>`
					});
					resultHtml += `</table>`
				}
				document.getElementById('results').innerHTML = '';
				document.getElementById('results').innerHTML = resultHtml;
			});
		}

	The above implementation of search method will subscribe to GithubService.getRepos method. Once the response is received, will construct an html by looping through the result set.
	This html is set into the innerHtml of the "results" div.

5. Add a method by the name "buttonClick", which will be called by link onclick event.

	**app.ts**

		buttonClick(e: Event) {
    		let username = document.getElementById("username").getAttribute("value");
	        this.search(username);
	    }


5. When you try to build the project, you will be able to see the following errors.

	node_modules/@angular/http/src/headers.d.ts(52,59): error TS2304: Cannot find name 'Map'.
	node_modules/@angular/http/src/url_search_params.d.ts(46,16): error TS2304: Cannot find name 'Map'.
	node_modules/rxjs/Observable.d.ts(10,66): error TS2304: Cannot find name 'Promise'.
	node_modules/rxjs/Observable.d.ts(66,60): error TS2304: Cannot find name 'Promise'.
	node_modules/rxjs/Observable.d.ts(66,70): error TS2304: Cannot find name 'Promise'.
	node_modules/rxjs/observable/PromiseObservable.d.ts(40,31): error TS2304: Cannot find name 'Promise'.
	node_modules/rxjs/observable/PromiseObservable.d.ts(41,26): error TS2304: Cannot find name 'Promise'.
	node_modules/rxjs/operator/toPromise.d.ts(7,59): error TS2304: Cannot find name 'Promise'.
	node_modules/rxjs/operator/toPromise.d.ts(7,69): error TS2304: Cannot find name 'Promise'.
	node_modules/rxjs/operator/toPromise.d.ts(9,9): error TS2304: Cannot find name 'Promise'.
	node_modules/rxjs/operator/toPromise.d.ts(10,26): error TS2304: Cannot find name 'Promise'.
	node_modules/rxjs/operator/toPromise.d.ts(10,36): error TS2304: Cannot find name 'Promise'.

	This is because the typescript compiler does not know the definitions for the methods listed. eg. Map, Promise. These are ES6 operators.
	You need to specify the built in typescript definition libraries to be included during build. This can be done by adding the following entry in tsconfig.json

		"lib": [
			"es6",
			"dom"
		]

	This will inclued the type definitions for all the types provided by es6 and dom modules.

6. When you load the app in the browser, you will end up with the following errors. (Check the console.)

		app.ts:1 Uncaught ReferenceError: require is not defined(…)(anonymous function) @ app.ts:1

	We have used ES6 imports in our app.ts and github.service.ts. We have specified the typescript module format as "commonjs" and the target as "es5" in tsconfig.json.
	During transpilation, typescript will convert the files into es5 compatible commonjs modules which makes use of "require" method to resolve dependencies.

	Now our application requires a module loader. For this example, I am going to use SystemJS. All the modules are lazy loaded as and when required.

7. Configuring SystemJS to load the application.

	Add a file "system.config.ts"

	**system.config.ts**

		declare var System: any;

		let map = {
			"@angular": "node_modules/@angular",
			"rxjs": "node_modules/rxjs",
		};

		let packages: any = {
			"dist": {
				defaultExtension: "js"
			},
			"node_modules": {
				defaultExtension: "js"
			}
		};

		let ngPackageNames = [
			"common",
			"core",
			"http",
			"platform-browser"
		];

		ngPackageNames.forEach(
			function iterator(packageName) {
				packages["@angular/" + packageName] = {
					main: ("bundles/" + packageName + ".umd.js")
				};
			}
		);

		System.config({
			map: map,
			packages: packages,
		});

		System
			.import('dist/app.js')
			.then(null, console.error.bind(console));

	Add "System.js" library and the "system.config.js" file generated on to the index.html

		<script src="./node_modules/systemjs/dist/system.js"></script>    
    	<script src="./dist/system.config.js"></script>

	With the introduction of SystemJS in our app, the imports of rxjs operators needs to be modified accordingly in "github.service.ts"

		import { Observable } from "rxjs/Observable";
		import 'rxjs/add/operator/map';
		import "rxjs/add/operator/catch";
		import 'rxjs/add/observable/of';

	This will load the app.js and its dependencies in the correct order as and when required.

8. On clicking the link, we get the following error.

	http.umd.js:1122 Uncaught TypeError: Cannot read property 'getCookie' of null
    at CookieXSRFStrategy.configureRequest (http://localhost:3000/node_modules/@angular/http/bundles/http.umd.js:1122:91)
    at XHRBackend.createConnection (http://localhost:3000/node_modules/@angular/http/bundles/http.umd.js:1162:32)
    at httpRequest (http://localhost:3000/node_modules/@angular/http/bundles/http.umd.js:1497:24)
    at Http.request (http://localhost:3000/node_modules/@angular/http/bundles/http.umd.js:1592:38)
    at Http.get (http://localhost:3000/node_modules/@angular/http/bundles/http.umd.js:1603:25)
    at GithubService.getRepos (http://localhost:3000/dist/github.service.js:20:14)
    at App.search (http://localhost:3000/dist/app.js:9:28)
    at <anonymous>:1:5


	Now we need to dive into the Angular2 source code and find exactly what are the initialization methods to be called before executing a DOM related operation.

	Backtracking from where the error occured in Angular2 source code.

	**xhr_backend.ts**
			
		const xsrfToken = __platform_browser_private__.getDOM().getCookie(this._cookieName);

	getDOM() is returning null.  __platform_browser_private__ is defined in private_exports of platform-browser
	
	**platform-browser/private_exports.ts**

		getDOM: dom_adapter.getDOM,

	dom_adapter is imported using import * as dom_adapter from './dom/dom_adapter';

	**dom_adapter.ts**

		export function getDOM() {
			return _DOM;		
		}

	The private variable _DOM is only set via 2 methods exposed setDOM and setRootDomAdapter. There is no reference for setDOM. setRootDomAdapter is being called in BrowserDomAdapter.

	**browser_adapter.ts**

		static makeCurrent() { setRootDomAdapter(new BrowserDomAdapter()); }

	makeCurrent() is being called in browser.ts

	**browser.ts**

		export function initDomAdapter() {
			BrowserDomAdapter.makeCurrent();
			BrowserGetTestability.init();
		}

	initDomAdapter method is exposed in the object  __platform_browser_private__

	So before any DOM related helper method is called, we need to initialize the dom adapter by calling __platform_browser_private__.initDomAdapter();

	Adding it into the service class constructor.

	**github.service.ts**

		import { __platform_browser_private__ } from "@angular/platform-browser"

		constructor(){
			...
			__platform_browser_private__.initDomAdapter();
		}

The demo app is fully functionaly. Enter a username and click on search. It will list the user's repositories.


## Conclusion

Through this experiment we were able to make use of @angular/http module to execute an API call outside an Angular2 application. 
Angular2 takes care of all the tasks involved in providing initialized DOMAdapters and instances of Http via dependency injection. 
Had to go through the Angular2 source code to find out which method needs to be called for initializing the Browser DOM Adapter.

The code which is of importance is

	Constructing the Http instance

		import { XHRBackend, BrowserXhr, ResponseOptions, CookieXSRFStrategy, RequestOptions} from '@angular/http'

		let browserXhr: BrowserXhr = new BrowserXhr();
		let baseResponseOptions: ResponseOptions = new ResponseOptions();
		let xsrfStrategy: CookieXSRFStrategy = new CookieXSRFStrategy();
		let backend: XHRBackend = new XHRBackend(browserXhr, baseResponseOptions, xsrfStrategy);
		let requestOptions: RequestOptions = new RequestOptions();
		let http: Http = new Http(backend, requestOptions);

	Initializing the DOM Adapter

		import { __platform_browser_private__ } from "@angular/platform-browser"

		__platform_browser_private__.initDomAdapter();

The solution can be imporved by refactoring the Http calls into a utility class, cache the http instance, initalize the DOM Adapter only once, etc

This approach is not at all recommended since we might have missed other initializations which are taken care by Angular2 when the library is used inside a complete Angular2 application.

Similarly another interesting feature which @angular comprises of is its dependency injection framework. We can make use of the library seperately and resolve dependencies or inject them easily.

Angular2 rocks..!!!








	