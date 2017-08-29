# Using a template with FLask

In this exercise, you will see how to flask render_template and loop through a list.

## What you should do

1. Create a new Flask application called and a hello_world.py file, that looks like this:

```python
from flask import Flask, render_template

app = Flask(__name__)


@app.route('/')
def hello_world():
    favorite_character = "Jon Snow"
    others = ["Danny", "The hound", "Arya", "Night King"]
    return render_template("index.html",
                           favorite_character = favorite_character,
                           others = others)


if __name__ == '__main__':
    app.run()
```

2. In the template folder, create a file called index.html that looks like this:

```html5
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <p>My favorite character is <b>{{ favorite_character }}</b>.</p>
    I also like:
    <ul>
        {% for character in others %}
            <li>{{ character }} </li>
        {% endfor %}
    </ul>

</body>
</html>
```

If it is done properly, when you run your application, you will be able to navigate to localhost:5000 and see this:

