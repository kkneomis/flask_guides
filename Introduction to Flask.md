# Introduction to Flask

In this exercise, you will see how to use how to create a basic flask application.

## What you should do

1. Create a new Flask application called and a hello_world.py file, that looks like this:

```python
from flask import Flask

app = Flask(__name__)

@app.route('/')
def hello_world():
    name = "Jon Snow"
    return "Hello world! My name is " + name

if __name__ == '__main__':
    app.run()
```
If it is done properly, when you run your application, you will be able to navigate to localhost:5000 and see this:

