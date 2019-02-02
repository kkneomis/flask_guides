# Lesson 12 - Using Page Fragments with Jinja

## Learning Objectives
* Learn how to dynamically load different page fragments (e.g. header and footer) to build page.


## The Walkthrough
1. Create a Flask Application
	* Name it FlaskApp_12

2. Edit the main python file (FlaskApp_12.py)
	* Make it look like the following

```python

from flask import Flask, render_template, flash

app = Flask(__name__)

#We need this for flashing errors
app.secret_key = 'some_secret'

@app.route('/')
def page1():
    return render_template("page1.html")

@app.route('/2')
def page2():
    return render_template("page2.html")

@app.route('/3')
def page3():
    flash("You can alert the user to an error like this.")
    return render_template("page3.html")

if __name__ == '__main__':
    app.run()
```

3. Create an index page
	* In the template folder, create an base.html file
	* Make it look like the following

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Index</title>
</head>
<body>
    <div class="navlinks">
        <a href="/">index</a> - <a href="/2">Page 2</a> - <a href="/3">Page 3</a>
    </div>


    {% for message in get_flashed_messages() %}
         {{ message }}
    {% endfor %}

    {% block body %}{% endblock %}

    <div class="footer">copyright 2019</div>
</body>
</html>
```

4. Create a new page
	* In the template folder, create an page1.html file
	* Make it look like the following

```html

{% extends "base.html" %}
{% block body %}

    <h2>Page 1</h2>

{% endblock %}

```

5. Create a new page
	* In the template folder, create an page2.html file
	* Make it look like the following

```html
{% extends "base.html" %}
{% block body %}
    
    <h2>Page 2</h2>

{% endblock %}
```


6. Create a new page
	* In the template folder, create an page3.html file
	* Make it look like the following

```html

{% extends "base.html" %}
{% block body %}
    
    <h2>Page 3</h2>

{% endblock %}
```

If it is done properly, when you run your application, you will be able to navigate to localhost:5000 and see this:

![Handling Form Values ](img/lesson12a.png)

![Handling Form Values ](img/lesson12b.png)

![Handling Form Values ](img/lesson12c.png)

## What is Going On


On almost any web application, there are pieces of the web page that are duplicated from one page to another. Generally, a site has similar footers, headers and navbars on every page. Building and maintaining a site like this can be challenging when code has to be edited in the exact same way on several different pages.

Page fragments are a way to minimize the confusion. By defining one footer as the footer that is used on every page, it means that all the footers start out the same. It also means that when you change that footer fragment, that the footers on all pages change automatically.

### base.html

In **base.html** we are defining out default layouts which our other pages will inherit. 

```
{% for message in get_flashed_messages() %}
    {{ message }}
{% endfor %}
```
This is the jinja syntax for flashing messages to the user. We can use this to display error and warning messages to the user whenever something goes wrong. That way, our user doesn't have to wonder why the application is not behaving as expected.

```
{% block body %}{% endblock %}
```
Here, we are letting jinja know that we will replace with content with our page content from another template.

### page{1..3}.html
Our other pages can now inherit the layout of our base template. This include all the stylesheet, javascript files, and general page layout. 


First, we include ```{% extends "base.html" %}``` to let jinja know which template we want to extend. Jinja will them render all the content inside our 
```{% block body %} {% endblock %}``` tags into the congruent tags in the base template.

We can have as many templates as we want inheriting any given base layout. We can also create as many base layouts as we want, or even a tree of layout inheritance.


