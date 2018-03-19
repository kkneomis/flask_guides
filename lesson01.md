# Lesson 1 - Introduction to Flask - Hello World with variable

## Learning Objectives
* Create a basic Flask application

## The Walkthrough
1. Create a Flask Application
	* Name it FlaskApp_01

2. Edit the main python file (FlaskApp_01.py)
	* Make it look like the following

```python
from flask import Flask

app = Flask(__name__)

@app.route('/')
def hello_world():
    return "Greetings, universe!"

if __name__ == '__main__':
    app.run()
```

3. Run your application
	* To run the application click the run button in the upper right hand corner:

4. View your Application
	* Open Google Chrome and type in the URL http://localhost:5000 and hit enter. You should see this

![Running your first Flask Application](img/lesson01.png)

## What is Going On

In Line 1 of FlaskApp_01.py, we are importing the Flask class into our project to make the @app.route decorator work.

In Line 2, we create a new instance of the flask class. 

@app.route indicates what path or endpoint the user will visit. The function following this annotation (in this case, the **hello_world** function) is run each time that the path is accessed.
Here, the default path is the only path that is specified out.

We use app.run() to run our app on a local server (default port 5000).
