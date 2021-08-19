# Script-injections-safety-issues

## Definition
- Cross-site Scripting (XSS) is a client-side code injection attack. The attacker aims to execute malicious scripts in a web browser of the victim by including malicious code in a legitimate web page or web application. The actual attack occurs when the victim visits the web page or web application that executes the malicious code. The web page or web application becomes a vehicle to deliver the malicious script to the user’s browser. Vulnerable vehicles that are commonly used for Cross-site Scripting attacks are forums, message boards, and web pages that allow comments.
![](https://i.imgur.com/wNExyvp.png)


JS injection is running javascript from the client-side invoked by the client. You can do it in a browser or in console like in chrome


### Example scenario

Take a typical login form consisting of a user and email field and a password field. After the login info is submitted, it is combined with an SQL query on your web server.

It is sent to the server to verify if it was given a valid username with a corresponding password. A username "james" with the "1111" password would result in this command:

		
```SELECT * FROM users WHERE username='james' AND password='1111'```

		
But if they put something like "james’;--", the query would look like this:

		
```SELECT * FROM users WHERE username='james'; -- ' AND password='1111'```

		
In this scenario, the attacker is using SQL comment syntax. The remaining code after the double-dash (--) sequence will not run. Meaning an SQL would be:

		
```SELECT * FROM users WHERE username='james'; ```

		
It will then return user data that was entered in the password field. This move could allow the login screen to be bypassed.

An attacker can also go further by adding another Select condition, "OR 1=1", that will result in the following query:

		
```SELECT * FROM users WHERE username='james' OR 1=1; ```

		
The query returns a non-empty dataset for any potential login with the entire "users" table database.

##### [Example Video of SQL Injection](https://www.youtube.com/watch?v=wcaiKgQU6VE)


### How to prevent script injections?
* Do not allow any dynamic code execution in the application. This means you should avoid language constructs like eval and code strings passed to setTimeout() or the Function constructor.
* Secondly, avoid serialization which could be vulnerable to injection attacks that execute code in the serialization process.
* Lastly, perform dependency scanning to ensure that your application isn’t susceptible to this attack due to third-party open source components.
* Furthermore, if you use a static code analysis tool like Snyk Code, you can find these potential code injection security vulnerabilities in your or your colleagues’ code.



## parametrize 
The use of prepared statements with variable binding (aka parameterized queries) is how all developers should first be taught how to write database queries. They are simple to write, and easier to understand than dynamic queries. Parameterized queries force the developer to first define all the SQL code, and then pass in each parameter to the query later. This coding style allows the database to distinguish between code and data, regardless of what user input is supplied.

Prepared statements ensure that an attacker is not able to change the intent of a query, even if SQL commands are inserted by an attacker. In the safe example below, if an attacker were to enter the userID of tom' or '1'='1, the parameterized query would not be vulnerable and would instead look for a username which literally matched the entire string tom' or '1'='1.


### how to write parametrized query in node.js using node-postgress module:

``` js
const text = 'INSERT INTO users(name, email) VALUES($1, $2) RETURNING *'
const values = ['brianc', 'brian.m.carlson@gmail.com']
// callback
client.query(text, values, (err, res) => {
  if (err) {
    console.log(err.stack)
  } else {
    console.log(res.rows[0])
    // { name: 'brianc', email: 'brian.m.carlson@gmail.com' }
  }
})
// promise
client
  .query(text, values)
  .then(res => {
    console.log(res.rows[0])
    // { name: 'brianc', email: 'brian.m.carlson@gmail.com' }
  })
  .catch(e => console.error(e.stack))
// async/await
try {
  const res = await client.query(text, values)
  console.log(res.rows[0])
  // { name: 'brianc', email: 'brian.m.carlson@gmail.com' }
} catch (err) {
  console.log(err.stack)
}
```
