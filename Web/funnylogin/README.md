![image](https://github.com/petriQore/DiceCTF-2024-Quals/assets/123587287/0ab318cd-ce21-45a4-8c95-040842667929)

This time we're met with a login page

![image](https://github.com/petriQore/DiceCTF-2024-Quals/assets/123587287/e0122bf8-0266-4528-ad63-1dcd8d0f0562)

Let's jump to the app.js file provided with the task.

This part of the code creates 100000 random users, with random usernames and passwords and inserts them to the database

```
const users = [...Array(100_000)].map(() => ({ user: `user-${crypto.randomUUID()}`, pass: crypto.randomBytes(8).toString("hex") }));
db.exec(`INSERT INTO users (id, username, password) VALUES ${users.map((u,i) => `(${i}, '${u.user}', '${u.pass}')`).join(", ")}`);
```

Then one of these users is chosen at random to become the admin

```
const isAdmin = {};
const newAdmin = users[Math.floor(Math.random() * users.length)];
isAdmin[newAdmin.user] = true;
```

And this is the interesting part

```
const query = `SELECT id FROM users WHERE username = '${user}' AND password = '${pass}';`;
.
.
if (users[id] && isAdmin[user]) {
            return res.redirect("/?flag=" + encodeURIComponent(FLAG));
        }

```

This looks like we need an sql injection, after tinkering around I found this ``` ' OR id = 2 ; -- ```  This injection can validate the first part of the if condition,
since it's only checking for the existence of the id, so as long as we choose an id between 0 and 99999 we should bypass that.

Next we have to somehow input the admin username in the user form, with the amount of randomness involved, it's impossible to find it so there has to be a trick.

Honestly I couldn't find the answer at first and I wasted a bit of time searching on the internet, until one of Securinets technical team members ```fir3cr4ckers``` told me 
to read the [Object](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object) type documentation.

![image](https://github.com/petriQore/DiceCTF-2024-Quals/assets/123587287/67ba31bf-90f8-4039-bc14-61c83365e58a)

This seemed promising, if we passed \_\_proto__ as the username, it would evaluate to true!

So let's try this: 

```
Username: __proto__
Password: ' OR id = 5 ; --
```

![image](https://github.com/petriQore/DiceCTF-2024-Quals/assets/123587287/514a85f5-5628-4181-9c7f-578324121cd0)

Perfect!

``` 
dice{i_l0ve_java5cript!}
```

