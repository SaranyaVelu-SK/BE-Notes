# bcrypt

## 1. What is bcrypt?
- `bcrypt` is a **password-hashing function** designed to be **slow and computationally expensive** (on purpose).  
- This makes it resistant to:
  - **Brute-force attacks**  
  - **Rainbow table attacks**  
- It is widely used in authentication systems to store passwords **safely** instead of in plain text.

## 2. Why use bcrypt?
- **Plaintext password storage = dangerous**  
- Hashing with `bcrypt` ensures:
  - Passwords are **irreversible** (cannot recover original password from hash).  
  - Each password gets a **unique salt** (random string added before hashing).  
  - Same passwords produce **different hashes** → prevents attackers from spotting patterns.  


## 3. Installation
```bash
npm install bcrypt
```

```js
const bcrypt = require("bcrypt");

async function hashPassword(password) {
  const saltRounds = 10; // higher = slower but more secure
  const hashedPassword = await bcrypt.hash(password, saltRounds);
  console.log("Hashed:", hashedPassword);
}

hashPassword("mySecret123");
```

**Verifying a Password**
```js
const bcrypt = require("bcrypt");

async function verifyPassword(password, hash) {
  const match = await bcrypt.compare(password, hash);
  if (match) {
    console.log("✅ Password is correct!");
  } else {
    console.log("❌ Invalid password.");
  }
}

verifyPassword("mySecret123", "$2b$10$Q9s4oaqpn9Yt9oOPyRBFHeXoQq2aW4y2KxU7LZ/d7Yqa6jF92xvCq");
```

## 5. How bcrypt Works Under the Hood

1. **Generate Salt** → a random string unique for each password.  
2. **Hash** → bcrypt applies multiple rounds of hashing with the salt.  
3. **Store** → the final hash includes:
   - Algorithm info  
   - Salt  
   - Hash result  

### Example bcrypt hash

$2b$10$Q9s4oaqpn9Yt9oOPyRBFHeXoQq2aW4y2KxU7LZ/d7Yqa6jF92xvCq

#### Table View
| Part   | Example | Meaning |
|--------|---------|---------|
| `$2b$` | `2b`    | Algorithm version (`bcrypt v2b`) |
| `10$`  | `10`    | Cost factor → `2^10 = 1024 rounds` |
| `Q9s4oaqpn9Yt9oOPyRBFHe` | (random) | **Salt** (unique per password) |
| `XoQq2aW4y2KxU7LZ/d7Yqa6jF92xvCq` | (hash) | Final hashed password |

---

## 6. Best Practices

- Always use `bcrypt.hash()` with **salt rounds ≥ 10**.  
- Never store raw passwords, only store **hashes**.  
- Always verify using `bcrypt.compare()`.  
- For modern security needs, consider **argon2** (a newer, stronger algorithm).  

---

# Authentication with JWT and Cookies in Node.js

## 1. What is JWT?
- **JWT (JSON Web Token)** is a compact way to securely transmit information between client and server.  
- It has 3 parts:
header.payload.signature

- **Header** → algorithm & token type  
- **Payload** → user data (e.g., userId, role)  
- **Signature** → verifies the token wasn’t tampered with  

**Example:**
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...


## 2. Why use Cookies with JWT?
- Storing JWT in **localStorage** is vulnerable to **XSS attacks**.  
- Storing JWT in **HTTP-only cookies** is safer because:
  - JavaScript cannot directly access HTTP-only cookies.  
  - They are automatically sent with every request to the server.  

## 3. Setup
Install dependencies:
```bash
npm install express jsonwebtoken cookie-parser bcrypt
```

**CODE**:
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


```js
const jwt = require('jsonwebtoken');
const { UserModel } = require('../Models/userModel');
const userAuth = async (req, res, next) => {
   try {
      const { jwtToken } = req.cookies;
      if (!jwtToken) {
         throw new Error("Not a valid token")
      }
      const decodedTokenData = await jwt.verify(jwtToken, process.env.JWT_PRIVATE_KEY);
      const userData = await UserModel.findById(decodedTokenData._id);
      if (!userData) {
         throw new Error("User not found")
      }
      req.userData = userData;
      next();
   } catch (err) {
      res.status(400).send(err.message);
      console.log(err)
   }
}
module.exports = {
   userAuth
}
```


```js
app.get('/profile',userAuth, async (req, res) => {
 const userData = req.userData;
 console.log(userData);
 res.send(userData)
    
})
```