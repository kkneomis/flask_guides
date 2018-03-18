# Lesson 7 - Completing the CRUD cycle

## Learning Objectives
* Connect to a sqlite database
* Create an object model using SQLalchemy
* Create, read, update and delete objects from the view

## The Walkthrough
1. Create a Flask Application
	* Name it FlaskApp_07

2. Edit the main python file (FlaskApp_07.py)
	* Make it look like the following

```python
from flask import Flask, render_template, request, redirect, url_for
from flask_sqlalchemy import SQLAlchemy

app = Flask(__name__)
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///flaskr.db'
db = SQLAlchemy(app)

@app.route('/')
def index():
    people = Person.query.all()
    return render_template("index.html", people = people)

@app.route('/create')
def add():
    return render_template("form.html", person=None)

@app.route('/show/<int:id>')
def show(id):
    person = Person.query.get(id)
    return render_template("show.html", person = person)


@app.route('/update/<int:id>')
def edit(id):
    person = Person.query.get(id)
    return render_template("form.html", person=person)

@app.route('/delete/<int:id>')
def delete(id):
    person = Person.query.get(id)
    db.session.delete(person)
    db.session.commit()
    return redirect(url_for('index'))

@app.route('/process', methods=['GET','POST'])
def process():

    id = request.form['id']
    firstname = request.form['first']
    lastname = request.form['last']

    if id:
        # updating the person 
        person = Person.query.get(id)
        person.firstname = firstname
        person.lastname = lastname
    else:
        # adding a new person 
        person = Person(firstname,lastname)
        db.session.add(person)

    db.session.commit()
    return redirect(url_for('index'))

class Person(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    firstname = db.Column(db.String(50))
    lastname = db.Column(db.String(50))

    def __init__(self, firstname, lastname):
        self.firstname = firstname
        self.lastname = lastname


if __name__ == '__main__':
    app.run()
```
3. Create a form page
	* In the template folder, create an form.html file
	* Make it look like the following

```html
<!DOCTYPEhtml>
<htmllang="en">
<head>
    <metacharset="UTF-8">
    <title>Title</title>
</head>
<body>
        <form action="/process" method="POST">
            <input type="hidden" value="{{ person.id }}" name="id">
            FirstName: <input type="text" name="first" value="{{ person.firstname }}"><br>
            LastName: <input type="text" name="last" value="{{ person.lastname }}"><br>
            <input type="submit" value="submit">
        </form>
</body>
```

3. Create an index page
	* In the template folder, create an index.html file
	* Make it look like the following

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Index</title>
</head>
<body>
    <h4>These are the coolest people</h4>
    <ol>
    {% for person in people %}
        <li>{{ person.firstname}}, {{ person.lastname}}
            <a href="show/{{ person.id }}">show</a>/
            <a href="edit/{{ person.id }}">edit</a>/
            <a href="delete/{{ person.id }}">delete</a>
        </li>
    {% endfor %}
    </ol>

    <a href="/add">Add a person</a>
</body>
</html>
```


4. Create a show page
	* In the template folder, create an show.html file
	* Make it look like the following
	
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Home</title>
</head>
<body>
    <h4>These are the coolest people</h4>
    <ol>
    {% for person in people %}
            <li>{{ person.firstname}}, {{ person.lastname}}
                <a href="show/{{ person.id }}">show</a>/
                <a href="update/{{ person.id }}">edit</a>/
                <a href="delete/{{ person.id }}" >delete</a>
            </li>
    {% endfor %}
    </ol>

    <a href="/create">Add a person</a>
</body>
</html>
```
If it is done properly, when you run your application, you will be able to navigate to localhost:5000/create and see this:

![Running your first Flask Application](img/lesson07a.png)

If you navigate to localhost:5000, you will see all the people you have added. You should also be able to view, edit, or remove each of them.

![Running your first Flask Application](img/lesson07b.png)

## What is Going On


Congratulations on your first 'full' application! You can now add data to the database, as well as review, modify and delete it. 

### Model 
Like the previous exercises, the Person class acts as the database model for the data that going to be saved. We can create multiple instance of this class and commit them to our databse. The Person class inherits from```db.Model```, which is the baseless class for all SQLalchemy data models. We use **Column** to define the columns in our database table (in this case, the Person table). We can specify which column serves as the primary key, in addition to the column types (String, text, DateTime, etc.) 


### The Controller 
This is where the action happens. Routes are mapped out for each action - Creating, Reading, Updating and Deleting data (CRUD). 

#### The routes 

##### Default Route (“/”)
When the user visits this route, the user will see a list of all the person entries that have been made. 
This is because index function queries the database for all people using the```.query.all()``` and store them in the **people** variable. We then loop through each **person** in **people** and render them in the the view.



#### Add route ("/create")
When a user visits this route, they have access to a form which allows them to enter a first and last name for a person. This information is sent in a POST request to the ```process()``` function. 

 Notice that in the form, we have have a hidden input field with ```{{ person.id }}``` as its name. However, the rendered person is actually None. That's because this field will be used later when we are updating our data. In the spirit of DRY (Don't Repeat Yourself), we are going to use one html form for both functions. 

#### Update route ("/update/<int:id>")
When a user wants to modify a record, that user can retrieve the details of that record by opening up http://localhost:5000/update, and adding the ID of the user whose record is being modified. The **{id}** parameter in this case is a primary key that exists in the database, because there should be only one record that matches this criterion.

We use ```Person.query.get(id)``` to pull up that record, and it's {id} is passed to the form's hidden field. 

#### Process route ("/process")
This routes receives a POST request form object from **form.html**. This occurs either after a new record is create or an existing object is update. When a new record is created, person is None in the form, so the request has no id attribute. But when a record is being updated, we pass in a valid person id (for the record we want to update). 

So we can discern the appropriate case by checking id an if **{id}** exists in the request form object. If it does, we simply update the object using the new values in the form. If there is no **{id}**, we create a new object instead. 

We use ```db.session.commit()``` to save all our database modifications.


##### Show Route (“/show/<int:id>”)
When the user visits this route, they will see a specific record based on the id specified in the url. Fask expects the id in that url pattern to be an *integer* 

#### Delete 
The delete route follows the same pattern, but instead of showing the record, it is immediately deleted from the database. When the user is re-directed to the default route, this will show in the list that is displayed, as that record will not be shown in the list.
