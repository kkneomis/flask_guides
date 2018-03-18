# Lesson 8 - Uploading Images to Spring Boot with Cloudinary

## Learning Objectives
* Upload images to Flask using Cloudinary

## The Walkthrough
1. Create a Flask Application
	* Name it FlaskApp_08
    
2. Sign up for a Cloudinary account (www.cloudinary.com) if you do not already have one 

3. Install Cloudinary's module 
	* ```Pycharm > Preferences > Project Interpreter > Click plus > search cloudinary > Install package```

4. Import the werkzeug module
	* ```Pycharm > Preferences > Project Interpreter > Click plus > search werkzeug > Install package```

3. Create a file in the root directory named config.py
    * Put your environment variables in the config.py file like so:
    * You can get your keys from cloudinary
    
```python
CLOUDINARY_CLOUD_NAME="mycloudname"
CLOUDINARY_API_KEY="myapikey"
CLOUDINARY_API_SECRET="myapisecret"
```


4. Edit the main python file (FlaskApp_08.py)
	* Make it look like the following
    
```python
from flask import Flask, render_template, request, redirect, url_for
from flask_sqlalchemy import SQLAlchemy
from werkzeug import secure_filename
from config import *
import os
import cloudinary
import cloudinary.uploader
import cloudinary.api


app = Flask(__name__)
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///flaskr.db'
app.config['UPLOAD_FOLDER'] = '/tmp/'
db = SQLAlchemy(app)


cloudinary.config(
    cloud_name = CLOUDINARY_CLOUD_NAME,
    api_key = CLOUDINARY_API_KEY,
    api_secret = CLOUDINARY_API_SECRET
)


@app.route('/')
def index():
    posts = Post.query.all()
    return render_template("index.html", posts=posts)


@app.route('/add')
def add():
    return render_template("form.html")


@app.route('/processform', methods=['GET','POST'])
def processform():
    content = request.form['content']
    f = request.files['image']
    
    filepath = os.path.join(app.config['UPLOAD_FOLDER'], secure_filename(f.filepath))
    f.save(filename)
    
    res = cloudinary.uploader.upload_large(filename, resource_type="auto")
    img_url = res['secure_url']
    post = Post(content, img_url)
    
    db.session.add(post)
    db.session.commit()
    
    # cleanup
    os.remove(filename)

    return redirect(url_for('index'))


class Post(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    content = db.Column(db.String(50))
    image = db.Column(db.String(50))

    def __init__(self, content, image):
        self.content = content
        self.image = image


if __name__ == '__main__':
    db.create_all()
    app.run()
```

3. Create a form page
	* In the template folder, create an form.html file
	* Make it look like the following
    
```python
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <form action="/processform" method="POST" enctype="multipart/form-data" >
        Content: <textarea rows="3" name="content" ></textarea><br>
        Image: <input type="file" name="image"><br>
        <input type="submit" value="submit">
    </form>
</body>
</html>
```

4. Create a index page
	* In the template folder, create an index.html file
	* Make it look like the following
    
```python
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <h2>My posts</h2>
    {% for post in posts %}
        <div>
            <h4>{{ post.content }}</h4>
            <img src="{{ post.image }}">
        </div>
    {% endfor %}

<a href="/add" >Add a new post</a>
</body>
</html>
```
If it is done properly, when you run your application, you will be able to navigate to localhost:5000/add and see this:

![Uploading Images to Spring Boot with Cloudinary](img/lesson08b.png)

Once you submit your form, you will be redirected to your image on the main page.

![Uploading Images to Spring Boot with Cloudinary](img/lesson08a.png)



## What is Going On

Congratulations! If you've made it this far, you're ready to start using your first third party API. Cloudinary is an application that allows you to upload and transform images, so you can allow your users to filter images, re-shape them, add text, and have a lot of fun using the additional options that come for 'free' with this application. 

This is a sample of things you can do with Cloudinary (http://www.cloudinary.com/cookbook)
Have fun!

All you need to do is upload the image to the Cloudianry server, and apply as many styles as you would like to to transform the image. 

You will need to add the Cloudinary dependency so that you can create an instance of the Cloudinary class. This creates a way for your code to access all of the methods it needs to be able to 'talk to' the Cloudinary server. 

In this example, we're creating a database of posts with images, so we need to have some content, and a link to the corresponding images, which will be uploaded via our application and saved to the Cloudinary server.

### Config.py
In this file, we store our cloudinary api keys and secrets. We can still access them from our main python file. By keeping this out of our main python file, we can choose hide it from the public when committing the project to a version control system (e.g. github).


### The Post Model 
This is a simple class that saves information about the post. 

## The View
### index.html
This displays post's content and image. The image is displayed using an image tag. 

### form.html 
This form allows a user to upload files. 

```
<input type="file">
```
indicates that a file is expected, and 

```
 enctype="multipart/form-data"
```
indicates that additional data is going to be posted with the form (i.e. the picture). This means that the post variables for the form will include the file which is uploaded. The uploaded file can be processed, as you will see in the processform method.

## The Controllers

### @app.route("/")
This route lists all of the posts in the database and displays their images using index.html. 

### @app.route("/add")
This route allowes users input posts details through form.html. The data is sent in a POST request to the processform() function.


### @app.route("/processform")
This is where the magic happens. Once a user clicks 'submit' on form.html, then the details are submitted to the processform function. Here we take the file from the request form body and save it to a local **/tmp/** (temporary) folder. We then upload the file from that folder to cloudinary, which returns to us the uploaded image's cloudinary url.

Using this new image url and the post content from the request, we create a new Post object and commit it to the database.

Finally, we delete the temporary image we saved.

The user is then returned to the default route and should see the list of posts and their images. 



