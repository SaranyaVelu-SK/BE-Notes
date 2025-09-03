# Express `app` vs `router`

## **app**
```js
const app = express();
```
- Calls the Express function, which internally creates an instance of an Express application object.
- This object is essentially a function that also has methods (like .get(), .post(), .use(), .listen() etc.).
- Internally, Express builds on Node’s http.Server — when you call app.listen(), it wraps Node’s http.createServer(app).

## router
```js
const router = express.Router();
```
- Calls the Router constructor inside Express.
- A router is a mini instance of middleware + routing system.
- It behaves like app in how it handles .get(), .post(), .use(), etc., but it cannot start a server (no .listen()).
- You must mount it into an app or another router.

## Purpose

| Aspect               | `app` (Express Application) | `router` (Express Router)              |
|----------------------|-----------------------------|-----------------------------------------|
| **Scope**            | Whole application           | Subset of routes / feature module       |
| **Start server**     | ✅ Yes (`app.listen()`)      | ❌ No                                   |
| **Middleware usage** | Global to entire app         | Scoped to routes defined in this router |
| **Reusability**      | Single instance usually      | Many routers can be created and mounted |
| **Best use**         | Entry point of project       | Organizing related routes (users, products, auth, etc.) |
| **Nesting**          | Rare (not typical)           | ✅ Can be nested inside other routers   |

---

# What is a Server?

A **server** is a computer program or machine that **listens for requests** (from clients like browsers, apps, or other systems) and **responds with data or services**.

---

## 1. Two Meanings of "Server"

### **Hardware (Physical or Virtual Machine)**
- A powerful computer designed to run services 24/7.  
- **Example:** A data center machine running websites, APIs, databases.  

### **Software (Program running on hardware)**
- An application that waits for requests and sends responses.  
- **Example:** An Express.js app (`app.listen(3000)`), Apache HTTP server, or Nginx.  

In modern usage, **"server" usually means the software**, but it always runs on some **hardware**.

---

#  What Happens When You Enter a URL in the Browser?

1. **DNS Resolution**  
   - Browser checks cache → OS cache → DNS resolver → Root / TLD / Authoritative DNS server.  
   - Converts domain name (e.g., `google.com`) into an IP address.  

2. **Browser initiates TCP connection**  
   - Uses IP + port (default 80 for HTTP, 443 for HTTPS).  
   - Handshake process (TCP 3-way handshake).  

3. **TLS/SSL Handshake (if HTTPS)**  
   - Browser and server agree on encryption keys.  
   - Certificates are verified.  

4. **HTTP Request is sent**  
   - Browser sends a request (e.g., `GET /`) with headers (cookies, user-agent, etc.).  

5. **Server processes request**  
   - Web server (Nginx, Apache, Express.js, etc.) receives it.  
   - May query databases, run backend logic, or fetch other resources.  

6. **Server sends HTTP Response**  
   - Includes status code, headers, and body (HTML, JSON, etc.).  

7. **Browser renders content**  
   - Parses HTML, CSS, and JavaScript.  
   - Renders the DOM and applies styles.  
   - Executes scripts, loads images, fonts, APIs.  

8. **Additional requests**  
   - Browser requests linked resources (CSS, JS, images, etc.).  
   - Repeats request/response cycle for each.  

9. **Page fully loaded**  
   - User sees the rendered webpage.  
   - Background requests (AJAX, APIs, WebSockets) may continue.

# ⚡ Quick Steps: What Happens When You Enter a URL?

1. **DNS Lookup** – Domain name is converted to an IP address.  
2. **TCP/TLS Handshake** – Connection established (secure if HTTPS).  
3. **HTTP Request Sent** – Browser sends a request to the server.  
4. **Server Response** – Server sends back HTML, CSS, JS, data, etc.  
5. **Browser Renders** – Browser builds the page and loads resources.  

---

# res.status().send()  vs res.status().json():
## res.status().send()

**Purpose**: Sends back anything → text, HTML, Buffer, or even an object.
**Issue**: If you send an object, Express may or may not set the response type as JSON depending on context.

**Example 1 (Text response)**
```js
res.status(200).send("Hello World");
```
*Browser sees plain text (Content-Type: text/html by default).*

**Example 2 (Object response)**
```js
res.status(200).send({ msg: "Hello" });
```

- Express converts it into JSON → {"msg":"Hello"}
- But sometimes the Content-Type header may not strictly be application/json.

## res.status().json()

**Purpose**: Always sends response in JSON format.
**Guarantees:**
- Automatically converts objects/arrays into JSON string.
- Always sets header → Content-Type: application/json.

**Example**
```js
res.status(200).json({ msg: "Hello" });
```

- Browser/API client sees JSON response with correct JSON headers.

### Key Difference (Simple)

- res.send() → can send text, HTML, or JSON (not strict).
- res.json() → only for JSON responses (strict & safe).

**Which should you use?**

- For APIs → always use res.json() (clear + consistent).
- For sending HTML/text → use res.send().
- send = "send anything".
- json = "send JSON, and make sure client knows it’s JSON".