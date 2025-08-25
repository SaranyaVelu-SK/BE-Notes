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
---
# Mongoose Schema Methods

Mongoose allows you to define **custom methods** on schemas.  
These methods can be called directly on **document instances** created from that schema.

---

## 1. Instance Methods
Instance methods operate on **individual documents**.

```js
const mongoose = require("mongoose");

const userSchema = new mongoose.Schema({
  username: String,
  email: String,
  password: String,
});
```

```js
// Adding an instance method
userSchema.methods.sayHello = function () {
  return `Hello, my name is ${this.username}`;
};

const User = mongoose.model("User", userSchema);

(async () => {
  const user = new User({ username: "Alice" });
  console.log(user.sayHello()); // Output: Hello, my name is Alice
})();
```

## 2. Static Methods

Static methods operate on the **model itself**, not individual documents.

```js
userSchema.statics.findByEmail = function (email) {
  return this.findOne({ email });
};
```

```js
const User = mongoose.model("User", userSchema);

(async () => {
  const foundUser = await User.findByEmail("alice@example.com");
  console.log(foundUser);
})();
```
**Use schema.statics for utility/helper functions at the Model level.**

### Example: Password Comparison with bcrypt

```js
    userSchema.methods.getJWT = async function(){             // never use arrow function strictly
        const user = this;

        const jwtToken = await jwt.sign({_id:user._id},process.env.JWT_PRIVATE_KEY);
        return jwtToken;
    }

    userSchema.methods.compareUserPassword = async function(userGivenPassword){
        const passwordInDb = this.password
        const isPasswordMatched = await bcrypt.compare(userGivenPassword, passwordInDb);

        return isPasswordMatched;
    }
```

```js
app.get('/signIn', async (req, res) => {
    try {
        const { email, password } = req.body;
        let userData;
        if (validator.isEmail(email)) {
            userData = await UserModel.findOne({ email: email });
        }
        if (userData) {
            let isValidPassword = await userData.compareUserPassword;
            if (isValidPassword) {
                const token = await userData.getJWT();
                res.cookie("jwtToken", token,{expires: new Date(Date.now() + (5 * 24 * 3600000))})   //5 days
                res.send("signin successfully")
            } else {
                throw new Error("wrong credentials")
            }
        }
    } catch (err) {
        res.status(400).send(err.message);
        console.log(err)
    }
})
```