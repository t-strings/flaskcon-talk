---
theme: default
transition: slide-left
title: "Flask + Template Strings = Heart"
colorSchema: dark
layout: default
mdc: true
---
<center><h1>Flask + <strong>Template&nbsp;Strings</strong> = ❤️</h1></center>

---

# Hi, we're **Paul** and **Dave**

<div v-click><p><strong>Paul Everitt</strong> heads developer advocacy at JetBrains.</p></div>

<div v-click><p><strong>Dave Peck</strong> is an indie dev in ☀️ Seattle.</p></div>


---

# We co-authored **PEP 750**

<div v-click><p>Better known as <strong>Template Strings</strong></p></div>
<div v-click><p>...<i>also</i> known as <strong>t-strings</strong></p></div>
<div v-click><p>(it takes a village)</p></div>



---

# **T-strings**: coming soon!

<div v-click><p>They'll ship in <strong>Python 3.14</strong>...</p></div>
<div v-click><p>...later this year.</p></div>
<div v-click><p>(if you've got <strong>3.14 beta</strong>, you've got t-strings)</p></div>


---

# Today!

<div v-click class="tighter"><p>&ndash; <strong>What</strong> t-strings are</p></div>
<div v-click class="tighter"><p>&ndash; What they <strong>aren't</strong></p></div>
<div v-click class="tighter"><p>&ndash; <strong>Why</strong> t-strings are</p></div>
<div v-click class="tighter"><p>&ndash; T-strings, html, and <strong>Flask</strong> ❤️</p></div>
<div v-click class="tighter"><p>&ndash; Tooling for t-strings</p></div>
<div v-click class="tighter"><p>&ndash; Some extra <i>sizzle</i></p></div>


---

# What are **t-strings**?

<div v-click><p>New feature shipping in <strong>Python 3.14</strong></p></div>
<div v-click><p>They <strong>generalize</strong> f-strings</p></div>
<div v-click><p>They help make f-strings <strong>safer</strong></p></div>
<div v-click><p>They help make f-strings more <strong>flexible</strong></p></div>


---
layout: cover
---

# **Generalizing** f-strings

---

# The **same syntax**

````md magic-move
```python314
name = "World"
greeting = f"Hello, {name}!"
```
```python314
name = "World"
greeting = t"Hello, {name}!"
```
````

---

# Really, the **same syntax**

````md magic-move
```python314
price = 14.95
caption = f"For only ${price:.2f}!"
```
```python314
price = 14.95
caption = t"For only ${price:.2f}!"
```
````

---

# Both are **eagerly evaluated**

````md magic-move
```python314
friend = "World"
greeting = f"Hello, {friend}!"
```
```python314
# friend = "World"
greeting = f"Hello, {friend}!" # 💣
```
```python314
# friend = "World"
template = t"Hello, {friend}!" # 💣
```
```python314
friend = "World"
template = t"Hello, {friend}!" # 😊
```
````

---

# Naming things is hard (act 1)

<div v-click><p>t-strings are <strong>eagerly evaluated</strong></p></div>

<div v-click><p>You do <i>not</i> keep calling <code>render_template()</code> on them</p></div>

<div v-click><p>Are <code>Template</code>s... <i>templates</i>?</p></div>

---

# T-strings **aren't** like jinja <span class="slide-count">(2)</span>

````md magic-move
```python314
def greeting(name: str) -> Template:
	return t"Hello, {name}!"
```
```python314
def greeting(name: str) -> Template:
	return t"Hello, {name}!"

template_1 = greeting("Paul")
template_2 = greeting("Dave")
```
````


---

# T-strings are **not** like f-strings:

````md magic-move
```python314
name = "world"
type(f"Hello, {name}!")
```
```python314
name = "world"
type(f"Hello, {name}!")
# <class 'str'>
```
```python314
name = "world"
type(f"Hello, {name}!")
# <class 'str'>
type(t"Hello, {name}!")
```
```python314
name = "world"
type(f"Hello, {name}!")
# <class 'str'>
type(t"Hello, {name}!")
# <class 'string.templatelib.Template'>
```
````

<div v-click><p>(Wait, <strong>what's this</strong>?)</p></div>

---

# T-strings are **not strings**

<div v-click><p>You write them like they <i>are</i>...</p></div>
<div v-click>
```python314
t"This is not a string"
```
</div>
<div v-click><p>But they evaluate to a new type, <code>Template</code></p></div>

---

# T-strings are **not strings** <span class="slide-count">(2)</span>

````md magic-move
```python314
str(t"Please be a string!")
```
```python314
str(t"Please be a string!")
# "Template(
#    strings=('Please be a string!',),
#    interpolations=(),
# )"
```
````

<div v-click><p>You have to <strong>process</strong> templates to use them</p></div>

---

# Processing templates

<div v-click>
<p>Templates are <strong>normal</strong> Python objects</p>
</div>
<div v-click>
<p>You can write or call code to:</p>
</div>
<div v-click>
<p>&ndash; Turn them into a <code>str</code></p>
</div>
<div v-click>
<p>&ndash; Turn them into any <i>other</i> type</p>
</div>

---

# Yes, but **why**?

<div v-click><p>Let's talk about f-strings...</p></div>

---

# F-strings get used a **lot**!

---
layout: image-right
image: /img/f-strings-awesome.png
backgroundSize: contain
---

# F-strings **rock**:

&ndash; Powerful

&ndash; Readable

&ndash; Elegant syntax


---
layout: image-right
image: /img/f-strings-dangerous.png
backgroundSize: contain
---

# F-strings get **misused**:

&ndash; SQL injection

&ndash; XSS in HTML

&ndash; etc.

---
layout: image
image: /img/bobby-tables-from-xkcd-by-randall-munroe.png
backgroundSize: contain
---

<div class="bottom-out"><center><p>(with apologies to randall munroe)</p></center></div>

---

# Little Bobby Tables

<div class="smaller">
````md magic-move
```python314
def get_query(name: str):
	return f"SELECT * FROM students WHERE name = '{name}'"
```
```python314
def get_query(name: str):
	return f"SELECT * FROM students WHERE name = '{name}'"

query = get_query("Robert'); DROP TABLE students;--")
execute(query)  # ☠️
```
```python314
def get_query(name: str):
	return f"SELECT * FROM students WHERE name = '{name}'"

def execute(query: str):  # ☠️
	# We're already sunk because query is just a string
	# and we don't know if they really want Robert');
	...

query = get_query("Robert'); DROP TABLE students;--")
execute(query)  # ☠️
```
```python314
def get_query(name: str):
	return f"SELECT * FROM students WHERE name = '{name}'"

@app.route('/user/<str:name>')
def user_data(name: str):
	query = get_query(name)
	return execute(query)  # ☠️
```
````
</div>

---

# Little Bobby, uh, HTML-bles

<div class="smaller">
````md magic-move
```python314
def render_user(name: str):
	return f"<div class='user'>{name}</div>"
```
```python314
def render_user(name: str):
	return f"<div class='user'>{name}</div>"

render_user("<script>alert('pwned')</script>")  # ☠️
```
```python314
def render_user(name: str):
	return f"<div class='user'>{name}</div>"

@app.route("/user/<str:name>")
def user_html(name: str):
	return render_user(name)  # ☠️
```
````
</div>


---

# T-strings make strings **safer**

<div v-click><p>With <code>Template</code> you can know:</p></div>

<div v-click><p>&ndash; Which parts of the string are <strong>static</strong></p></div>
<div v-click><p>&ndash; Which parts of the string are <strong>dynamic</strong></p></div>

<div v-click><p>You <i>can't</i> do this with f-strings</p></div>

---

# T-strings make strings **safer** <span class="slide-count">(2)</span>

<div class="smaller">
````md magic-move
```python314
name = "world"
template = t"<div>{name}</div>"
```
```python314
name = "world"
template = t"<div>{name}</div>"
list(template)
# ["<div>", Interpolation(value="world"), "</div>"]
```
```python314
name = "world"
template = t"<div>{name}</div>"
template.strings
# ("<div>", "</div>")
```
```python314
name = "world"
template = t"<div>{name}</div>"
template.interpolations
# (Interpolation(value="world"),)
```
```python314
name = "world"
template = t"<div>{name}</div>"
template.interpolations
# (Interpolation(
#    value="world",
#    expression="name",
#    conversion=None,
#    format_spec="",
# ))
```
```python314
name = "world"
template = t"<div>{name!s:>10}</div>"
template.interpolations
# (Interpolation(
#    value="world",
#    expression="name",
#    conversion="s",
#    format_spec=">10",
# ))
```
```python314
name = "world"
template = t"<div>{name}</div>"
list(template)
# ["<div>", Interpolation("world"), "</div>"]
```
```python314
name = "world"
template = t"<div>{name}</div>"
for item in template:
	...
```
```python314
name = "world"
template = t"<div>{name}</div>"
for item in template:
	if isinstance(item, str):
		print("static:", item)
```
```python314
name = "world"
template = t"<div>{name}</div>"
for item in template:
	if isinstance(item, str):
		print("static:", item)
	else:
		print("dynamic:", item.value)
```
```python314
name = "world"
template = t"<div>{name}</div>"
for item in template:
	if isinstance(item, str):
		print("static:", item)
	else:
		print("dynamic:", item.value)
# static: <div>
# dynamic: world
# static: </div>
```
```python314
name = "world"
template = t"<div>{name}</div>"
for item in template:
	if isinstance(item, str):
		print("static:", item)
	else:
		print("dynamic:", item.value)
```
```python314
name = "world"
template = t"<div>{name}</div>"
parts = []
for item in template:
	if isinstance(item, str):
		print("static:", item)
	else:
		print("dynamic:", item.value)
```
```python314
name = "world"
template = t"<div>{name}</div>"
parts = []
for item in template:
	if isinstance(item, str):
		parts.append(item)
	else:
		print("dynamic:", item.value)
```
```python314
name = "world"
template = t"<div>{name}</div>"
parts = []
for item in template:
	if isinstance(item, str):
		parts.append(item)
	else:
		parts.append(escape(item.value))
```
```python314
name = "world"
template = t"<div>{name}</div>"
parts = []
for item in template:
	if isinstance(item, str):
		parts.append(item)
	else:
		parts.append(escape(item.value))
result = "".join(parts)
# "<div>world</div>"
```
```python314
name = "<script>alert('pwned')</script>"
template = t"<div>{name}</div>"
parts = []
for item in template:
	if isinstance(item, str):
		parts.append(item)
	else:
		parts.append(escape(item.value))
result = "".join(parts)
# "<div>&lt;script&gt;alert('pwned')&lt;/script&gt;</div>" ❤️
```
```python314
from some_library import html

name = "<script>alert('pwned')</script>"
template = t"<div>{name}</div>"
result = html(template)
# "<div>&lt;script&gt;alert('pwned')&lt;/script&gt;</div>" ❤️
```
```python314
from some_library import html

name = "<script>alert('pwned')</script>"
template = t"<div>{name}</div>"
result = html(template)
# <class 'HTMLElement'>
```
```python314
from some_library import html

name = "<script>alert('pwned')</script>"
template = t"<div>{name}</div>"
result = html(template)
# <class 'HTMLElement'>
str(result)
# "<div>&lt;script&gt;alert('pwned')&lt;/script&gt;</div>" ❤️
```
````
</div>

---
transition: fade
---

# T-strings make strings **flexible**

<div class="smaller">
````md magic-move
```python314
from some_library import html
```
```python314
from some_library import html

name = "world"
element = html(t"<div>{name}</div>")
str(element)
# "<div>world</div>"
```
```python314
from some_library import html

name = "world"
uid = "user-1"
element = html(t"<div>{name}</div>")
str(element)
# "<div>world</div>"
```
```python314
from some_library import html

name = "world"
uid = "user-1"
element = html(t"<div id={uid}>{name}</div>")
str(element)
# "<div id='user-1'>world</div>"
```
```python314
from some_library import html

name = "world"
uid = "user-1"
classes = ["user", "active"]
element = html(t"<div id={uid} class={classes}>{name}</div>")
str(element)
# "<div id='user-1' class='user active'>world</div>"
```
```python314
from some_library import html

name = "world"
attribs = {"id": "user-1", "class": ["user", "active"]}
element = html(t"<div {attribs}>{name}</div>")
str(element)
# "<div id='user-1' class='user active'>world</div>"
```
````
</div>


---

# T-strings make strings **flexible**

<div class="smallest">
````md magic-move
```python314
from some_library import html
```
```python314
from some_library import html

def user_image(user: User) -> Template:
	return t"<img src={user.image_url} alt={user.name} />"
```
```python314
from some_library import html

def user_image(user: User) -> Template:
	return t"<img src={user.image_url} alt={user.name} />"

def user_details(user: User, attribs: dict | None = None) -> Template:
	return t"<span {attribs}>{user.name}{user_image(user)}</span>"
```
```python314
from some_library import html

def user_image(user: User) -> Template:
	return t"<img src={user.image_url} alt={user.name} />"

def user_details(user: User, attribs: dict | None = None) -> Template:
	return t"<span {attribs}>{user.name}{user_image(user)}</span>"

@app.route("/user/<str:uid>")
def user_page(uid: str):
	user = get_user_from_db(uid)
	attribs = {"id": uid, "class": ["user", "active"]}
	return html(user_details(user, attribs))
```
```python314
from some_library import html

def user_image(user: User) -> Template:
	return t"<img src={user.image_url} alt={user.name} />"

def user_details(user: User, attribs: dict | None = None) -> Template:
	return t"<span {attribs}>{user.name}{user_image(user)}</span>"

@app.route("/user/<str:uid>")
def user_page(uid: str):
	user = get_user_from_db(uid)
	attribs = {"id": uid, "class": ["user", "active"]}
	return html(user_details(user, attribs))  # 🪄🪄🪄🪄🪄🪄🪄
```
````
</div>

---

# Down the rabbit hole

<div class="smaller">
```python314
def html(template: Template) -> HTMLElement:
	...
```
</div>

How does this _work_?


---

# Down the rabbit hole <span class="slide-count">(2)</span>

We saw a simple implementation:

<div class="smallest">
```python314
parts = []
for item in template:
	if isinstance(item, str):
		parts.append(item)
	else:
		parts.append(escape(item.value))
result = "".join(parts)
# "<div>&lt;script&gt;alert('pwned')&lt;/script&gt;</div>" ❤️
```
</div>

---

# Down the rabbit hole <span class="slide-count">(3)</span>

And something fancier:

<div class="smallest">
```python314
name = "world"
attribs = {"id": "user-1", "class": ["user", "active"]}
element = html(t"<div {attribs}>{name}</div>")
str(element)
# "<div id='user-1' class='user active'>world</div>"
```
</div>

---

# Down the rabbit hole <span class="slide-count">(4)</span>

<div v-click><p>The <code>html()</code> function is doing a lot:</p></div>

<div v-click class="tight"><p>&ndash; <strong>Parsing</strong> the <code>Template</code></p></div>
<div v-click class="tight"><p>&ndash; Examining each substitution's <strong>type</strong> and <strong>position</strong> in the underlying <strong>grammar</strong></p></div>
<div v-click class="tight"><p>&ndash; Deciding how to <strong>render</strong> each value</p></div>

---

# Flask + t-strings = ❤️

---
layout: image
image: /img/flask-quickstart-quote.png
backgroundSize: contain
transition: fade
---

&nbsp;


---
layout: image
image: /img/flask-t-strings.png
backgroundSize: contain
---

&nbsp;

---

# Flask + t-strings = ❤️

Enough slides; let's code.


---

# In closing

We're **excited** about t-strings!

Lots of cool new **libraries** and **tools** are coming.

Get involved!

---

# Say hello

Open space: **Saturday** in **ROOM 320** at **11 AM**

`@pauleveritt@fosstodon.org`

`@davepeck@davepeck.org`
