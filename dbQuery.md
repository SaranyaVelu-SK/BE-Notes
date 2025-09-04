1. To assign type of any field as id(as same as in mongodb _id) ---> **type:mongoose.Schema.Types.ObjectId**
2. enum - restrict user to decide certain values
3. Schema.pre(method,function expression)

---

# Logical Queries:

1. **$or**:
    - work on array of expressions  -  satisfy atleast one of the conditions
    - *Syntax* :  { $or: [ { <expression1> }, { <expression2> }, ... , { <expressionN> } ] }
    - *example* : db.inventory.find( { $or: [ { quantity: { $lt: 20 } }, { price: 10 } ] } )

2. **$and**:
    - work on array of expressions  -  satisfy all the conditions
    - *Syntax* : { $and: [ { <expression1> }, { <expression2> } , ... , { <expressionN> } ] }
    - *example* : db.example.find( { $and: [{ x: { $ne: 0 } },{ $expr: { $eq: [ { $divide: [ 1, "$x" ] }, 3 ] } }]} )

3. **$not**:
    - works on specified <operator-expression> - selects the documents that do not match the <operator-expression>. This includes documents that do not contain the field.
    - *Syntax* : { field: { $not: { <operator-expression> } } }
    - *example* : db.inventory.find( { price: { $not: { $gt: 1.99 } } } )

4. **$nor**:
    - work on array of expressions - fails all the conditions
    - *Syntax* : { $nor: [ { <expression1> }, { <expression2> }, ...  { <expressionN> } ] }
    - *example* : db.inventory.find( { $nor: [ { price: 1.99 }, { sale: true } ]  } )
    - *explanation* : This query will return all documents that:
        - contain the *price* field whose value is not equal to *1.99* and contain the *sale* field whose value is not equal to true **or**
        - contain the *price* field whose value is not equal to *1.99* but do not contain the *sale* field **or**
        - do not contain the *price* field but contain the *sale* field whose value is not equal to true **or**
        - do not contain the *price* field and do not contain the *sale* field

---

# Imp :
# `.save()` in Mongoose vs MongoDB

## Where to Find `.save()`
- **MongoDB Docs**  
  - You **won’t** find `.save()` in the MongoDB documentation.  
  - MongoDB only documents raw methods like `insertOne`, `updateOne`, `replaceOne`, etc.  

- **Mongoose Docs**  
  - `.save()` belongs to **Mongoose**, not MongoDB.  
  - Official docs:  
    🔗 [Mongoose API – Document.prototype.save()](https://mongoosejs.com/docs/api/document.html#Document.prototype.save)

## ⚙️ What `.save()` Does in Mongoose
- **Insert** → Creates a new document if it doesn’t exist yet.  
- **Update** → Persists changes if the document was fetched from the DB.  
- **Validation** → Runs schema validations before saving.  
- **Middleware** → Triggers `pre('save')` and `post('save')` hooks.  
- **Return** → A **promise** resolving to the saved document.

## 🆚 Difference From MongoDB’s `save()`
- `db.collection.save()` → **Mongo Shell method** (deprecated since MongoDB 4.2).  
- `doc.save()` (Mongoose) → **ODM method** with validation, middleware, and change tracking.  

`.save()` in your code comes from **Mongoose**, not MongoDB.  
If you were using **raw MongoDB**, you’d call `insertOne()` or `updateOne()` instead.

---

# Summary: How `.save()` Works in Mongoose (v.v.imp)

- **Model (`User`)** → Represents a MongoDB **collection** (`users` by default).
- **`findOne()`** → Fetches a document as a **Mongoose Document instance**, not a plain object.
- **Modify field** → Changes are tracked in memory; the field is marked as **dirty**.
- **`.save()`** →
  - If the document is **new** → issues `insertOne`.
  - If the document **already exists** → issues `updateOne` with only the modified fields (`$set`).
- **Result** → The actual MongoDB document in the `users` collection is updated.

✅ **TL;DR**:  
`User` = collection, `user` = document instance, `.save()` = persists your in-memory changes into MongoDB.

---

# Ref and populate in mongoose

1. **Posts Collection (without ref)**
```js
{ "_id": "p1", "title": "Hello World", "author": "u1" }
```
  - Here, "author": "u1" is just a string (or an ObjectId).
  - MongoDB itself doesn’t know "u1" belongs to the Users collection — it’s just stored as raw data.

2. **When You Add ref in Mongoose**
```js
  author: {
    type: mongoose.Schema.Types.ObjectId,
    ref: "User"
  }
```

3. **Now you’re telling Mongoose:**

- The author field in Posts stores an _id from the User collection.
- **Important**: MongoDB still doesn’t care — it only stores the ObjectId.
- ref is just *metadata* for Mongoose, so that when you later call populate(), it knows which collection to look in.

4. **Internally, Posts Still Looks Like This**
```js
{ "_id": "p1", "title": "Hello World", "author": ObjectId("64f...") }
```

**Query Without populate**
```js
await Post.find();
```


*Output:*
```js
{
  "_id": "p1",
  "title": "Hello World",
  "author": "64f123abc..."   // just ObjectId
}
```

- Mongoose just returns the raw ObjectId — no relationship is resolved.

5. **Query With populate**
```js
await Post.find().populate("author");
```

## Behind the scenes steps:

  1. Fetch post(s) from the posts collection.

  ```js
  { "_id": "p1", "title": "Hello World", "author": ObjectId("64f123abc...") }
  ```

  2. Look at the schema → sees ref: "User" on the author field.

  3. Perform a second query in the users collection:

  ```js
  db.users.find({ _id: ObjectId("64f123abc...") })
  ```

  4. Replace the ObjectId with the actual user document:

  ```js
  {
    "_id": "p1",
    "title": "Hello World",
    "author": {
      "_id": "64f123abc...",
      "name": "Alice"
    }
  }
  ```
### Key Points
- ref → Just a pointer telling Mongoose which collection to use.
- populate → Executes an extra query and swaps the ObjectId with the referenced document.
- MongoDB itself doesn’t know relationships → It’s Mongoose that simulates them.

---

# Quick Comparison Table between GET and POST API

| Feature        | GET                        | POST                           |
|----------------|----------------------------|--------------------------------|
| Purpose        | Retrieve data              | Send data (create/update)      |
| Data Location  | URL query string           | Request body                   |
| Visibility     | Visible in URL             | Hidden in body                 |
| Caching        | Can be cached              | Not cached                     |
| Idempotent     | Yes                        | No                             |
| Use Case       | Reading data               | Creating/Updating data         |

---

