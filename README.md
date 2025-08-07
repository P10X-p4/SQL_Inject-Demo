# A Guide to SQL Injection with InsecureBank.js

This document serves as a guide to the **InsecureBank.js** application, a simulated web service designed to help you understand different types of SQL injection vulnerabilities from a hacker's perspective. By exploring these vulnerabilities, you can better understand how to defend against them.

The application, while looking like a real banking portal, uses a simple JavaScript array to simulate a database. The intentional vulnerabilities in its code allow you to practice three key types of SQL injection attacks.

---

### In-band SQL Injection (Classic Login Bypass)

This is the most common type of SQL injection. The attacker uses the same channel to both send the attack and receive the results. In this application, it's the weakness in the login form.

**The Vulnerability:** The login function takes your username and password and directly inserts them into a JavaScript query string.

**The Payload:** The goal is to modify the code's logic to always be `true`.

- **Username:** `admin`
- **Password:** `' || '1'=='1`

**How it Works:** The payload modifies the login check from `user.password === 'your-password'` to `user.password === '' || '1'=='1'`. Since `'1'=='1'` is always true, the entire condition becomes true, and the application logs you in as the `admin` user without needing their password.

---

### Blind SQL Injection (Time-Based Attack)

Blind SQL injection is a more advanced technique where the attacker does not receive direct output. Instead, they infer information by observing the application's behavior, such as a time delay.

**The Vulnerability:** The "Test Condition" function in the app executes a payload and, if the condition is `true`, it intentionally delays the response.

**The Payload:** The goal is to build a condition that reveals a piece of information about the database data, like a password's length.

- **Target Username:** `admin`
- **Payload:** `LENGTH(password) == 16`

**How it Works:**
- If the admin's password is exactly 16 characters long, the condition `LENGTH(password) == 16` evaluates to `true`. The application then pauses for 3 seconds before responding.
- If the password is not 16 characters long, the condition is `false`, and the response is immediate.
- You can use this method to systematically guess the length of the password, and even its characters, by modifying the payload.

---

### Union-Based SQL Injection

A Union-based attack allows an attacker to combine the results of their own injected query with the original query. This is a powerful way to retrieve data from other parts of the database that you shouldn't have access to.

**The Vulnerability:** The "Retrieve User Info" field takes an ID and directly uses it to find a user. The vulnerability allows you to inject additional logic that pulls information from the database's `users` array.

**The Payload:** The goal is to use a logical operator to force the application to retrieve data that isn't intended for display.

- **User ID to retrieve:** `' || users.find(u => u.id === 2)`

**How it Works:** This payload, when executed by the application's code, will return the full user object for the user with an `id` of `2` (John Doe), including their username and password. You can change the ID to `1` or `3` to retrieve information on other users.

---

### Conclusion

The **InsecureBank.js** application is a safe environment to practice and understand the core principles of SQL injection. The most important lesson is not memorizing the payloads themselves but understanding how they manipulate the underlying code to bypass security checks and retrieve unauthorized data. This knowledge is crucial for building applications that are secure by design.
