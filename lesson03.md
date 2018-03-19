# Lesson 3 - Handling Form Values 

## Learning Objectives
* Capturing form values in the controller
* Using the Flask request method
* Creating a GET/POST route

## The Walkthrough
1. Create a Flask Application
	* Name it FlaskApp_03

2. Edit the main python file (FlaskApp_03.py)
	* Make it look like the following

```python
from flask import Flask, render_template, request

app = Flask(__name__)


@app.route('/loadform')
def login():
    return render_template("login.html")


@app.route('/processform', methods=['POST'])
def processform():
    user = request.form['user']
    return render_template("index.html", username=user)

if __name__ == '__main__':
    app.run()

```

3. Create an index page
	* In the template folder, create an index.html file
	* Make it look like the following

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <strong>Logged in as <b>{{ username }}</b></strong>
</body>
</html>
```

3. Create a form page
	* In the template folder, create an login.html file
	* Make it look like the following

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <form action="{{ url_for('processform') }}" method="POST" >
        Login: <input type="text" name="user" >
        <input type="submit" value="Submit" >
    </form>
</body>
</html>
```

If it is done properly, when you run your application, you will be able to navigate to localhost:5000/loadform and see this:

![Running your first Flask Application](img/lesson03a.png)

Once you submit the form, you should see this:

![Running your first Flask Application](img/lesson03b.png)
## What is Going On

The **“/loadform”** path displays the template "login.html" to the user. The form on this page sends the user input (whatever gets typed in the form and submitted) in a POST request to the ```processform()``` function. 

Within the html template, ```action="{{ url_for('processform') }}"``` tells the template where to send the data. When rendering the html form, Jinja looks for the url mapped to the "processform" function. In this case, it is **"/processform"**. SO when the template is rendered, it will actually show up as ```action="/processform"```. Thus, if we wanted, we could instead use the latter method to direct the form data.  

```method="POST"``` tells the form to send the data in a post request. When the information is sent as part of an HTTP request, the form information will be stored in the request body. It will be (mostly) invisible to the application user. If, instead, we used a GET request, the information sent would be visible in the url. [Learn more about GET and POST requests here](https://www.w3schools.com/tags/ref_httpmethods.asp).

The ```processform()``` function only  processes post requests. It takes the value of the "user" input of incoming requests and renders it on the page.

In the template, ```{{ username }}``` indicates value of the variable we rendered in the template.