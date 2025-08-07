## How Does a Model Match with the Collection in MongoDB?

### Key Points:

- **Model Name**: The name you give to the model (`User`, `Person`, etc.) does **not** have to match the collection name.
- **Default Collection Name**: If no custom collection name is provided, Mongoose **pluralizes** and **lowercases** the model name to create the collection name.
- **Custom Collection Name**: You can explicitly define the collection name using the `collection` option in the schema to ensure the model is linked to the correct collection.

###  In General:
You have flexibility in naming models and collections. The key is either:

1. Relying on **Mongoose's automatic pluralization**, or  
2. **Explicitly setting** the collection name in the schema.

### Example:

```js
const mongoose = require('mongoose');

// Define a schema for the 'users' collection, but with a different model name 'Person'
const userSchema = new mongoose.Schema({
  name: String,
  email: String
}, { collection: 'users' });  // Explicitly set collection to 'users'

// Create a model named 'Person', which does not match the collection name
const Person = mongoose.model('Person', userSchema); // Model name is 'Person'

// Mongoose will still use the 'users' collection because it's specified in the schema
Person.find({}).then((docs) => {
  console.log(docs);  // Queries the 'users' collection
}).catch((err) => {
  console.error(err);
});
```

##  Difference Between PUT and PATCH

| Aspect                    | PUT                                           | PATCH                                                  |
|---------------------------|-----------------------------------------------|--------------------------------------------------------|
| **Definition**            | Replaces the entire resource                  | Partially updates the resource                         |
| **Idempotency**           | Always idempotent                             | May or may not be idempotent                           |
| **Request Body**          | Requires the full resource                    | Allows partial data (only the fields to update)        |
| **Use Case**              | When replacing or fully updating a resource   | When updating only part of a resource                  |
| **Handling Multiple Requests** | Same data = same result, no change after first request | Same request = same result if idempotent (otherwise, different result) |
| **Example (increment)**   | Not suitable for incrementing operations      | Can be used for operations like increment (`$inc`)     |
| **Response Behavior**     | Replaces entire document with given data      | Only updates the specified fields                      |

---

## What is Idempotency?

**Idempotency** refers to the property that multiple identical requests should have the same effect as a single request.  
When a request is **idempotent**, repeating it does **not** change the outcome after the first application.

---

## Use Case Summary

1. **Use `PUT`** when you want to **replace the entire resource** with new data, and the request is **idempotent**.
2. **Use `PATCH`** when you need to **partially update** a resource, such as incrementing values or changing specific fields.

> ⚠️ Note: `PATCH` may **not always be idempotent**, especially when performing operations that change the resource incrementally.

---

## Learn More

Explore all options in [`Model.findOneAndUpdate()`](https://mongoosejs.com/docs/api/model.html#Model.findOneAndUpdate)


## Notes:

while sending a json data through body in request to a api, ans accessing it(json data) like req.body ---> will get *undefined*

need to parse the josn in the request by using a middleware given by express framework - **express.json**
```js
app.use(express.json());   //converst json to javascript object
```

this middleware will be run by all the apis

---

##  Difference Between JSON and JavaScript Objects

| Aspect                 | JavaScript Object                                  | JSON (JavaScript Object Notation)                    |
|------------------------|----------------------------------------------------|------------------------------------------------------|
| **Definition**         | A complex data structure used in JavaScript        | A lightweight data format for storing and transferring data |
| **Syntax**             | Can contain functions, variables, undefined, etc.  | Strict syntax: key-value pairs, double-quoted strings only |
| **Quotes on Keys**     | Keys can be unquoted                               | Keys **must** be in double quotes                    |
| **Data Types Allowed** | Strings, numbers, objects, arrays, booleans, functions, undefined | Strings, numbers, objects, arrays, booleans, null |
| **Usage**              | Used for logic, computation, and structure in JS code | Used for transmitting data between client-server     |
| **Parsing**            | Native to JavaScript                               | Needs to be parsed using `JSON.parse()`              |
| **Stringify**          | Not required                                       | Must use `JSON.stringify()` to convert to JSON string |

---

### Key Points:

- JSON is a **string-based format** used for **data exchange**, especially in APIs.
- JavaScript Objects are **in-memory representations** used during program execution.

---

### Example: JavaScript Object

```js
const person = {
  name: 'Alice',
  age: 25,
  greet: function() {
    console.log("Hello!");
  },
  isStudent: undefined
};
```
## Invalid in JSON

- Functions (e.g., `greet`)
- `undefined` values
- Unquoted keys

---

##  Example: JSON

```json
{
  "name": "Alice",
  "age": 25,
  "isStudent": true
}
```

## Converting Between JSON and JavaScript Object

```js
const jsonString = JSON.stringify(person); // Object JSON string
const jsObject = JSON.parse(jsonString);   // JSON string JS Object
```