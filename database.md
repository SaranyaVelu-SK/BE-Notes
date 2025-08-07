# MONGODB ATLAS:
- In mongoDB Atlas, after login,create a new FREE cluster
- get the connection string for the cluster created
- now ,download mongoDB Compass(GUI) to interact(or see whats happening) with the cluster
- Within cluster, we can create databases

### CONNECTING APPLICATION TO DATABASE:
- install mongoose - an npm package to interact with mongoDB database
- connect application to DB by
```js 
const connectDB = async() =>{
    await mongoose.connect("mongodb+srv://SaranyaSK:<password>@clustersk.5vwcf.mongodb.net/")
 }
connectDB().then(()=>{
        console.log("DB connected");
        app.listen(7777,()=>{
                console.log("server listening in 7777.....")
            })

}).catch((err)=>{
        console.log(err)
})
```
here, **db should be connected before listening on the server 7777**.......... v.v.imp

--- 

## Schema:

A **Schema** is a blueprint or structure that defines how documents in a MongoDB collection should look.

**It defines:**
- What fields exist in a document.
- The data types of each field.
- Optional: default values, validation rules, whether a field is required, etc.

**Example Schema in Mongoose:**

```js
const mongoose = require('mongoose');

const userSchema = new mongoose.Schema({
  name: { type: String, required: true },
  age: { type: Number },
  email: { type: String, unique: true }
});
```
---

## Model:

"A constructor function based on the schema, which gives you the power to work with actual documents in MongoDB."

In Mongoose, a **Model** is a constructor function created from a **Schema**. It represents a MongoDB **collection** and provides an interface to interact with that collection.

Models are used to:
- Create and save documents.
- Query documents.
- Update or delete documents.

A Model combines the structure defined in the schema with the MongoDB methods to perform database operations.


```js
const mongoose = require('mongoose');

// Step 1: Define the schema
const userSchema = new mongoose.Schema({
  name: String,
  age: Number
});

// Step 2: Create the model
const User = mongoose.model('User', userSchema);

// Step 3: Use the model to create a new user
const newUser = new User({ name: 'John', age: 30 });
newUser.save()
  .then(() => console.log('User saved!'))
  .catch(err => console.log(err));
```


