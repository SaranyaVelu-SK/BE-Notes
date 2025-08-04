## Introduction: 

- app.use("/routePath", rH1,rH2,rH3,rH4)
- To handle a request of app.use (or) app.{any route method}, we can have **any** no.of request handlers
- to move from on rH to another rH - need of next() function
- Route handlers can be in the form of a function, an array of functions, or combinations of both.
- *using next() in the last rH function will give **" cannot GET /user"** error, because there is no request handler to handle after that*


```js
    const cb0 = function (req, res, next) {
    console.log('CB0')
    next()
    }
    const cb1 = function (req, res, next) {
    console.log('CB1')
    next()
    }
    app.get('/example/d', [cb0, cb1], (req, res, next) => {
    console.log('the response will be sent by the next function ...')
    next()
    }, (req, res) => {
    res.send('Hello from D!')
    })
```

---

# Middlewares:
- in a sequence of rHs, only the last rH that gives response (res.send) is a request handler - others are middlewares
-  a function that actually sends a response is a request handler and other functions that are chained in between are called middlewares
- “In Express.js, middleware is a function that gets executed between the time a request is received and the time a response is sent. 
- "Middleware in Express.js is a function that has access to the incoming request (req), the outgoing response (res), and the next() function that passes control to the next middleware in the stack. Middleware is used to perform various tasks such as logging, authentication, validation, error handling, or modifying the request/response. It is executed in the order it is defined, and it can either terminate the request-response cycle or pass control to the next middleware using next()."

#### why do we need middleware?: 
1. "We need middleware because it helps us handle common tasks in a web application in a clean and organized way. Middleware allows us to write reusable code for things like logging, authentication, error handling, and parsing data. Without middleware, we would have to repeat the same logic in every route, making the code messy and harder to maintain."

# Erro handling Middlewares
- In Express.js, **Error-Handling Middleware** is a special function designed to catch and handle errors during the request-response cycle. When an error occurs in a route or middleware, Express can gracefully handle it and send an appropriate response to the client.

It is recognized by Express only if it has **four parameters**:
```
(err, req, res, next)
```

---

####  Syntax
```
app.use((err, req, res, next) => {
    // Handle the error here
});
```
- Must have **4 parameters**.
- Express will only treat it as an **error-handler** if defined with `(err, req, res, next)`.

---

#### How Express Handles Errors — Flow
1. HTTP request comes in.
2. Normal middlewares and route handlers are executed in the order they are defined.
3. If an error occurs (by `throw new Error()` or `next(err)`):
   - Express **skips the remaining route handlers and middlewares**.
   - Searches for the next middleware with 4 parameters `(err, req, res, next)`.
4. Executes that error-handling middleware.
5. If no error handler is defined, Express will respond with a **default 500 Internal Server Error**.

---

####  Path Matching Rule in Error-Handling Middleware
| Middleware Type                        | Path Filter Applied? |
|----------------------------------------|----------------------|
| `app.use('/path', (req, res, next) => {})` | ✅ YES (Path must match request URL) |
| `app.use('/path', (err, req, res, next) => {})` | ✅ YES (Path must match request URL) |

- Even for **error-handling middleware**, if you specify a path, Express applies **path filtering**.
- Example:
    ```js
    app.use('/admin', (err, req, res, next) => {
        // Will only run if original request URL starts with /admin
    });
    ```
- **If the original request path doesn't match, Express skips this error-handler.**

---

####  Global Error Handler
If you want an error handler to catch **errors from all routes**, define it without a path:
```js
app.use((err, req, res, next) => {
    res.status(500).send(err.message);
});
```

---

#### Key Rules to Remember
1. **4 parameters** are mandatory → `(err, req, res, next)`.
2. Use `next(err)` or `throw` to pass an error into Express’s error flow.
3. Error-handling middlewares execute **in the order they are defined**.
4. If a path is given (`/admin`), the error handler will **only handle errors from that route path**.
5. If you want a **global error handler**, do not specify a path.

---

####  Example — Basic Error Handler Flow
```js
const express = require('express');
const app = express();

app.get('/user', (req, res, next) => {
    throw new Error('Something broke in /user');
});

app.use((err, req, res, next) => {
    res.status(500).send('Error Caught: ' + err.message);
});

app.listen(3000, () => console.log('Server running on port 3000'));
```

---

#### Example — Path-Based Error Handling
```js
app.get('/admin', (req, res, next) => {
    next(new Error('Admin route error'));
});

// Will only handle errors for URLs starting with /admin
app.use('/admin', (err, req, res, next) => {
    res.status(500).send('Admin Specific Error Handler: ' + err.message);
});

// Global error handler (catch-all)
app.use((err, req, res, next) => {
    res.status(500).send('Global Error Handler: ' + err.message);
});
```
- If a request to `/admin` errors, Express will call the **/admin error handler first**.
- If you request `/user` and it errors, the `/admin` error handler is skipped, and Express moves to the **global error handler**.

---

