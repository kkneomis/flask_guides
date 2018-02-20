# Lesson 11 - Using Database Relationships with Flask/SQLalchemy - One to One

## Learning Objectives
* Create one to many relationships using SQLalchemy

## The Walkthrough
1. Create a Flask Application
	* Name it FlaskApp_09

2. Edit the main python file (FlaskApp_09.py)
	* Make it look like the following
    
```python

from flask import Flask, render_template, request, redirect, url_for
from flask_sqlalchemy import SQLAlchemy

app = Flask(__name__)
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///flaskr.db'
db = SQLAlchemy(app)


@app.route('/')
def index():
    posts = Post.query.all()
    categories = Category.query.all()
    return render_template("index.html",
                           posts=posts,
                           categories=categories)

tags = db.Table('tags',
    db.Column('category_id', db.Integer, db.ForeignKey('category.id')),
    db.Column('post_id', db.Integer, db.ForeignKey('post.id'))
)

class Post(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    content = db.Column(db.String(140))

    tags = db.relationship('Category', secondary=tags,
                           backref=db.backref('posts', lazy='dynamic'))

    def __init__(self, content):
        self.content = content

    def __repr__(self):
        return '<Post %r>' % self.content


class Category(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    name = db.Column(db.String(50))

    def __init__(self, name):
        self.name = name

    def __repr__(self):
        return '<Category %r>' % self.name


@app.route('/addcategory', methods=['POST', 'GET'])
def addcategory():
    name = request.form['name']
    category = Category(name)
    db.session.add(category)
    db.session.commit()
    return redirect(url_for('index'))

@app.route('/addpost', methods=['POST', 'GET'])
def addpost():
    content = request.form['content']
    post = Post(content)
    db.session.add(post)
    db.session.commit()
    return redirect(url_for('index'))

@app.route('/addtags', methods=['POST', 'GET'])
def addtags():
    tags = request.form.getlist('tags')
    print tags
    post_id = request.form['post']
    post = db.session.query(Post).get(post_id)
    for tag in tags:
        category = db.session.query(Category).get(tag)
        post.tags.append(category)
    db.session.commit()
    return redirect(url_for('index'))


if __name__ == '__main__':
    db.create_all()
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


    {% for post in posts %}
        <div >
            <h3>{{ post.content }} --
                <small>
                    {% for tag in post.tags %}
                        {{ tag.name }} /
                    {% endfor %}
                </small>
            </h3>


            <form method="POST" action="/addtags" >
                <select multiple name="tags">
                    {% for category in categories %}
                        <option value="{{ category.id }}">{{ category.name }}</option>
                    {% endfor %}
                </select>
                <input type="hidden" value="{{ post.id }}" name="post">
                <input type="submit" value="Submit">
            </form>

        </div>
    {% endfor %}

    <h3>Add post</h3>
    <form method="POST" action="/addpost" >
        <textarea rows="3"  placeholder="What's on your mind?" name="content"></textarea>
        <input type="submit" value="Submit">
    </form>

    <h3>Add category</h3>
    <form method="POST" action="/addcategory" >
        <input type="text" name="name" placeholder="Category name">
        <input type="submit" value="Submit">
    </form>


</body>
</html>

```

If it is done properly, when you run your application, you will be able to navigate to localhost:5000 and see this:

![Using Database Relationships with Flask/SQLalchemy - One to One](img/lesson11.png)

## What is Going On