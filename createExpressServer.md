creating express server:
- creating repository
- initialize the repository
- what are node_modules, package.json and package-lock.json
- install express
- create server
- write request handlers
- listen to port 7777
- install nodemon and update scripts in package.json
- what are dependencies
- what is the use of "-g" in npm install
- difference between caret and tilde in dependencies


NOTES:
npm ---> package manager for node - storage repository for all the packages

# Initializing Repository:
- 	initializing the repository by "npm init"
- purpose of npm init:
        1.Running npm init is an essential step in setting up a new Node.js project or package. 
		2.The primary purpose of npm init is to generate a package.json file, which is used to manage the metadata and dependencies of your project. 
		3.This file is crucial for maintaining and managing the configuration and behavior of the project. 
		4.Without it, npm won't be able to track dependencies, scripts, or other important project settings.
		5.Why npm init Is Recommended
			Dependency Management: With package.json, npm can automatically handle your project’s dependencies. Running npm install will install the packages you’ve listed as dependencies in package.json.
			Version Control: When you initialize your project with npm init, you create a structured way to define the versions of the packages you are using. This helps you avoid potential issues with incompatible versions.
			Collaboration: If you share your project or push it to version control (like GitHub), other developers can run npm install to install the dependencies you’ve specified in package.json. Without it, they wouldn’t know what dependencies are required.
			Scripts: Defining scripts in the package.json file makes it easy to automate tasks. For example, you could define a build script, a test script, or a start script, and anyone working on the project can run them via npm run <script-name>.

# Node Modules
when installed express , node modules are created
1.	node_modules is a folder that contains all of the dependencies and subdependencies installed for a Node.js project.
2.	It is automatically created and managed by npm when you run npm install.

REMEMBER : Exclude node_modules from being pushed to github by
1.	create a ".gitignore" file by "touch .gitignore" in gitbash
2.	add "node_modules/" in .gitignore file
3.	why to exclude : because of its large size(dependecies have dependencies)

# package.json:
It is a file created by npm when initialized a project - used to manage dependencies, metadata, scripts and other settings
1.	Tracks Dependencies: Specifies which packages your project needs, both for runtime (dependencies) and development (devDependencies).
2.	Defines Scripts: Defines custom commands like npm start and npm test to automate tasks.
3.	Contains Project Metadata: Includes project information like name, version, description, author, and license.
4.	Ensures Compatibility: Manages compatible Node.js and npm versions, and helps ensure dependency compatibility with peerDependencies.
5.	Reproducibility: With package-lock.json, it ensures consistent installs across environments.

# package-lock.json:
 It is a lock file that records the exact version of each installed package, including the versions of any dependencies and subdependencies. This ensures that your project’s dependencies are installed consistently across different environments, machines, or when other developers clone the project.
*When Does package-lock.json Get Updated?*
1.	When you run npm install: If you install new packages or update existing ones, npm will automatically update the package-lock.json file to reflect the changes.
2.	When you add or remove dependencies: If you add a new package via npm install <package-name>, the package-lock.json will be updated with the new package and its exact version.
3.	When you run npm update: If you run npm update <package-name>, npm will update the lock file to reflect the new versions of the dependencies.
REMEMBER: (difference between package.json and package-lock.json)
- package.json is for defining what dependencies your project needs and their version ranges.
- package-lock.json is for ensuring exact versions of dependencies are installed, providing consistency and reproducibility across different environments or machines.


# EXPRESS:
A layer built on top of Node.js that simplifies server creation, routing, middleware handling, and more — without writing everything from scratch.
Express.js is a minimal and flexible Node.js web application framework that provides a robust set of features for building web and mobile applications.
*Why Not Just Use Node.js?*
While Node.js provides low-level modules (http, fs, etc.), Express.js simplifies everything with cleaner, readable syntax and extra features.
Express.js is called a framework built on top of Node.js because it internally uses Node's http module and core features, but offers a cleaner, simpler, and more powerful API for building web applications.

**Install express:**
npm i express
express.js: web application frmaework for node.js
similar other frameworks like express for node.js : koa.js, Hapi.js,NestJS

*creating server:*
const express =  require("express");
const app = express();

The Express application object (or instance) is the object returned by calling express().(which is app)
It represents your entire web server and provides methods to define routes, use middleware, configure settings, and more.

app.listen(port,cb());

*Request Handlers:*
app.use(path,middlewarefunc());
When you call const app = express();, you get an object that is both a function and an object 
it can handle requests like a server function, and it also has a bunch of useful methods and properties attached to it.


It’s technically:
A function object with properties and methods attached.
That means:
typeof app === 'function'; // true
But it also behaves like this:
app.get()
app.use()
app.listen()
Because Express adds all those methods to it.

**Internally, it's like this (simplified view):**
`
function app(req, res, next) {
  // This is the main request handler
  // Express runs the middleware stack here
}

app.get = function (path, handler) { /*...*/ }
app.post = function (path, handler) { /*...*/ }
app.use = function (middleware) { /*...*/ }
app.listen = function (port, cb) {
  const server = http.createServer(app); // the app itself is the handler!
  return server.listen(port, cb);
};

app.set = function (key, value) { /*...*/ }
app.locals = {}; // app-wide variables
// ...and many more
`

So app is:
- ✅ A callable function (used as the request handler)
- ✅ A container of routing and middleware logic
- ✅ An event emitter
- ✅ An interface for app-level settings
________________________________________

`const app = express();
console.log(Object.getOwnPropertyNames(app));`

[
  'init',
  'defaultConfiguration',
  'lazyrouter',
  'handle',
  'use',
  'route',
  'engine',
  'set',
  'get',
  'post',
  'put',
  'delete',
  'listen',
  'mountpath',
  'locals',
  'settings',
  ...
]
These are methods and properties that Express attaches to the app function to give it all the power you use.



## nodemon:
Nodemon is a utility for Node.js applications that automatically restarts the server when changes are detected in the source code. 
update scripts in package.json:
	`scripts:{
	"dev":"nodemon src/app.js",                   --->for development purpose; run "npm run dev"
	"start":"node src/app.js"                          ---> run " npm run start"
	}`

## Dependencies:
- dependencies are the packages that are installed for the project with the version ranges
- Dependencies are external libraries or packages that your application requires in order to function properly.
- Production dependencies are necessary for your application to run in production, while development dependencies are only needed during development.
- Dependencies are listed in the package.json file and are installed via npm.

###  -g:
when installing any package, including -g will install them globally which means the packages are installed in the system globally that can be used across projects whereas packages that are not installed globally will be available only to that particular repository

Warning : project specific packages should not be installed globally

# caret(^) vs tilde(~) in dependencies:
### *caret:*
- Syntax: ^<major>.<minor>.<patch>
- Behavior: It allows updates to the minor and patch versions while locking the major version
### *tilde:*
- Syntax: ~<major>.<minor>.<patch>
- Behavior: It allows updates to the patch version only, but it will not update the minor or major versions.
    -  ^ is the most commonly used for public libraries and tools, allowing for more flexibility with minor version updates.
    -  ~ is more commonly used when you want to stick strictly to patch updates, such as for bug fixes or small improvements.
 
# **Backward Compatibility**
- Backward compatibility (also called backwards compatible) means that a **newer version of a software or package can still work with things made using the older version.
- In simple words: "Old code still works even after the update."
- Imagine you’re using version 1.0.0 of a package in your project.
    - Now the developers release 1.1.0.
        If 1.1.0 is backward compatible, it means:
        - Your code that worked with 1.0.0 will still work with 1.1.0
        - No need to change your code after updating
        But if it's not backward compatible (also called a breaking change), then:
        - Your existing code may break
        - You might need to rewrite parts of your app
                **Minor and patch are backwards compatible**
