- Mass assignment happens when a web framework automatically binds client-supplied input (JSON/form data) directly to internal objects or database models — without restricting _which_ fields are allowed to be set. If a developer builds an object from `request.body`

- suppose that we have this web app , we register a new account . After login in, we get the message `Account is pending approval` 

![i](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/113/mass_assignment/pending.png)

- After login in, we get the message `Account is pending approval`. The administrator of this web app must approve our registration. Reviewing the python code of the `/opt/asset-manager/app.py` file reveals the following snippet.

```python
for i,j,k in cur.execute('select * from users where username=? and password=?',(username,password)):
  if k:
    session['user']=i
    return redirect("/home",code=302)
  else:
    return render_template('login.html',value='Account is pending for approval')
```

- We can see that the application is checking if the value `k` is set. If yes, then it allows the user to log in. In the code below, we can also see that if we set the `confirmed` parameter during registration, then it inserts `cond` as `True` and allows us to bypass the registration checking step.

```python
try:
  if request.form['confirmed']:
    cond=True
except:
      cond=False
with sqlite3.connect("database.db") as con:
  cur = con.cursor()
  cur.execute('select * from users where username=?',(username,))
  if cur.fetchone():
    return render_template('index.html',value='User exists!!')
  else:
    cur.execute('insert into users values(?,?,?)',(username,password,cond))
    con.commit()
    return render_template('index.html',value='Success!!')
```

- we can capture the HTTP POST request to the `/register` page and set the parameters `username=new&password=test&confirmed=test`

![i](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/113/mass_assignment/mass_hidden.png)

- And here we entre 

![i](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/113/mass_assignment/loggedin.png)