# Routing

##### Route:
- route is a defined path with the method and the handler function upon accessing that path

`
app.get('/login', (req, res) => {
  res.send('Login Page');
});
`
- The path: /login

- The method: GET

- The handler: the function (req, res) => { ... }

- **endpoint :** In API documentation, "endpoint" often just means "GET /users" — the combination of method + path, no need to show handler.

##### Route Path:
- just the path(URL part)  eg:'/home','/singIn'

##### Routing:
- Routing is the process of defining how your server should respond to different client requests (based on URL + HTTP method).
- basically , a process of defining a route

--- 

# Order of Routing:
In routing, ORDER OF THE ROUTES MATTER A LOT becoz,for eg, "/" will match all the path that contains /, to be simpler
-    "/" will match for "/","/hello","/anything" and **the handler for '/' runs for all the matching routes**
-   "/hello" will match for all the routes that have"/hello/" ---it will match for "/hello","/hello/1","/hello/saran" etc
	so, if "/" route is written first , it will handle for both "/" and "/hello". if it is written reversed, it will first check for"/hello" and then "/"

so the correct order will be

---> app.use('/contact',(req,res)=>{
       res.send("9876543210")
       })

---> app.use('/user',(req,res)=>{
        res.send("Hello Saranya")
       })

---> app.use('/end',(req,res)=>{
       res.send("Thankyou")
       })

---> app.use("/",(req,res)=>{
        res.send("Welcome")
        })

---

**Note:** - 	app.use will match for all the route methods for a particular route path. For .eg: irrespective of the methods(get,put,patch,post or delete), request handler will be called for a route path in app.use in postman


##### Route paths based on string patterns

`app.get('/ab?cd', (req, res) => {
  res.send('ab?cd')
})`

- This route path will match acd and abcd. **[? –> b is optional]**

`app.get('/ab+cd', (req, res) => {
  res.send('ab+cd')
})`

- This route path will match abcd, abbcd, abbbcd, and so on. **[+ -> add as many b(only) as possible]**

`app.get('/ab*cd', (req, res) => {
  res.send('ab*cd')
})`

- This route path will match abcd, abxcd, abRANDOMcd, ab123cd, and so on.**[* -> add anything between ab and cd but it should start with ab and end with cd]**

`app.get('/ab(cd)?e', (req, res) => {
  res.send('ab(cd)?e')
})`

- This route path will match /abe and /abcde. **[() -> anything within parantheses is grouped and checked]**

##### Route paths based on regular expressions

This route path will match anything with an “a” in it.
`app.get(/a/, (req, res) => {
  res.send('/a/')
})`

This route path will match butterfly and dragonfly, but not butterflyman, dragonflyman, and so on.
`app.get(/.*fly$/, (req, res) => {
  res.send('/.*fly$/')
})`


# Route parameters
Route parameters are named URL segments that are used to capture the values specified at their position in the URL. The captured values are populated in the req.params object, with the name of the route parameter specified in the path as their respective keys.

Route path: /users/:userId/books/:bookId

Request URL: http://localhost:3000/users/34/books/8989

req.params: { "userId": "34", "bookId": "8989" }

To define routes with route parameters, simply specify the route parameters in the path of the route as shown below.

`app.get('/users/:userId/books/:bookId', (req, res) => {
  res.send(req.params)
})`

The name of route parameters must be made up of “word characters” ([A-Za-z0-9_]).

Since the hyphen (-) and the dot (.) are interpreted literally, they can be used along with route parameters for useful purposes.

`Route path: /flights/:from-:to
Request URL: http://localhost:3000/flights/LAX-SFO
req.params: { "from": "LAX", "to": "SFO" }`

`Route path: /plantae/:genus.:species
Request URL: http://localhost:3000/plantae/Prunus.persica
req.params: { "genus": "Prunus", "species": "persica" }`

To have more control over the exact string that can be matched by a route parameter, you can append a regular expression in parentheses (()):
`Route path: /user/:userId(\d+)
Request URL: http://localhost:3000/user/42
req.params: {"userId": "42"}`

**Use *req.params* when the value is part of the URL structure (like /user/:id).**

**Use *req.query* when the value is passed as a query string (like /search?q=hello).**
