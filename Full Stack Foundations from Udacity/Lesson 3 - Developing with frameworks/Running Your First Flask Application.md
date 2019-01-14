# First of all install flask on vagrant:
Change to the directory `/vagrant` by typing `$ cd /vagrant`
And then install **flask** for the current user by typing `$ python -m pip install flask --user`

# Querying menu items for a single restaurant:

```
from flask import Flask

from database_setup import Base, Restaurant, MenuItem
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker

# Create session and connect to DB ##
engine = create_engine('sqlite:///restaurantmenu.db')
Base.metadata.bind = engine

DBSession = sessionmaker(bind=engine)
session = DBSession()


app = Flask(__name__)

@app.route('/hello')
def HelloWorld():
	restaurant = session.query(Restaurant).first()
	items = session.query(MenuItem).filter_by(restaurant_id = restaurant.id)
	output =''
	for i in items:
		output += i.name
		output += '</br'
	return output

if __name__ == '__main__':
	app.debug = True
	app.run(host = '0.0.0.0', port = 5000)
```

### To solve the issue of queries running in different threads, on reload of page, crete the session from within the function

```
from flask import Flask

from database_setup import Base, Restaurant, MenuItem
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker

# Create session and connect to DB ##
engine = create_engine('sqlite:///restaurantmenu.db')
Base.metadata.bind = engine

app = Flask(__name__)

@app.route('/')
@app.route('/hello')
def HelloWorld():
	DBSession = sessionmaker(bind=engine)
	session = DBSession()
	restaurant = session.query(Restaurant).first()
	items = session.query(MenuItem).filter_by(restaurant_id = restaurant.id)
	output =''
	for i in items:
		output += i.name
		output += '</br'
		output += i.price
		output += '</br'
		output += i.description
		output += '</br'
		output += '</br'
	return output

if __name__ == '__main__':
	app.debug = True
	app.run(host = '0.0.0.0', port = 5000)
```

# Routing:

#### Add different routes to take the user to a restaurant menu through their ID
#### Urls/routes can be created inside a `<>` block in which you first assign the type of the argument and then its value, like `<int: 5.`
```
@app.route('/')
@app.route('/restaurants/<int:restaurant_id>/')
def restaurantMenu(restaurant_id):
	DBSession = sessionmaker(bind=engine)
	session = DBSession()
	restaurant = session.query(Restaurant).filter_by(id = restaurant_id).one()
	items = session.query(MenuItem).filter_by(restaurant_id = restaurant_id)
	output =''
	for i in items:
		output += i.name
		output += '</br>'
		output += i.price
		output += '</br>'
		output += i.description
		output += '</br>'
		output += '</br>'
	return output
```

### Creating routes for adding new menu item and editing and deleting existing ones:
```
from flask import Flask

from database_setup import Base, Restaurant, MenuItem
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker

app = Flask(__name__)
# Create session and connect to DB ##
engine = create_engine('sqlite:///restaurantmenu.db')
Base.metadata.bind = engine

@app.route('/')
@app.route('/restaurants/<int:restaurant_id>/')
def restaurantMenu(restaurant_id):
    DBSession = sessionmaker(bind=engine)
    session = DBSession()
    restaurant = session.query(Restaurant).filter_by(id = restaurant_id).one()
    items = session.query(MenuItem).filter_by(restaurant_id = restaurant_id)
    output =''
    for i in items:
        output += i.name
        output += '</br>'
        output += i.price
        output += '</br>'
        output += i.description
        output += '</br>'
        output += '</br>'
    return output

# Task 1: Create route for newMenuItem function here

@app.route('/restaurant/<int:restaurant_id>/new/')
def newMenuItem(restaurant_id):
    return "page to create a new menu item. Task 1 complete!"

# Task 2: Create route for editMenuItem function here

@app.route('/restaurant/<int:restaurant_id>/<int:menu_id>/edit/')
def editMenuItem(restaurant_id, menu_id):
    return "page to edit a menu item. Task 2 complete!"

# Task 3: Create a route for deleteMenuItem function here

@app.route('/restaurant/<int:restaurant_id>/<int:menu_id>/delete/')
def deleteMenuItem(restaurant_id, menu_id):
    return "page to delete a menu item. Task 3 complete!"


if __name__ == '__main__':
	app.debug = True
	app.run(host = '0.0.0.0', port = 5000)
```

### Loading html from a template:
For separating the html code from python code, we need to first create a html template file, suppose we name it `menu.html` and then place it inside a `/templates` directory inside the root directory where the python file `project.py` is located.

The file `menu.html` looks like:
```
<html>

<body>

<h1>{{restaurant.name}}</h1>


{% for i in items %}

<div>

<p>{{i.name}}</p>

<p>{{i.description}}</p>

<p> {{i.price}} </p>

</div>


{% endfor %}
</body>

</html>
```
The contents of `projects.py` at this stage will look like(don't forget to import **render_template** from flask):
```
from flask import Flask

from database_setup import Base, Restaurant, MenuItem
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker
#Import render template
from flask import request, render_template

app = Flask(__name__)
# Create session and connect to DB ##
engine = create_engine('sqlite:///restaurantmenu.db')
Base.metadata.bind = engine

@app.route('/')
@app.route('/restaurants/<int:restaurant_id>/')
def restaurantMenu(restaurant_id):
    DBSession = sessionmaker(bind=engine)
    session = DBSession()
    restaurant = session.query(Restaurant).filter_by(id = restaurant_id).one()
    items = session.query(MenuItem).filter_by(restaurant_id=restaurant_id)
    return render_template('menu.html', restaurant=restaurant, items=items)

# Task 1: Create route for newMenuItem function here

@app.route('/restaurant/<int:restaurant_id>/new/')
def newMenuItem(restaurant_id):
    return "page to create a new menu item. Task 1 complete!"

# Task 2: Create route for editMenuItem function here

@app.route('/restaurant/<int:restaurant_id>/<int:menu_id>/edit/')
def editMenuItem(restaurant_id, menu_id):
    return "page to edit a menu item. Task 2 complete!"

# Task 3: Create a route for deleteMenuItem function here

@app.route('/restaurant/<int:restaurant_id>/<int:menu_id>/delete/')
def deleteMenuItem(restaurant_id, menu_id):
    return "page to delete a menu item. Task 3 complete!"


if __name__ == '__main__':
	app.debug = True
	app.run(host = '0.0.0.0', port = 5000)
```

### Adding url_for to add **Edit** and **Delete** link(only change would be in the template or html file):
```
<html>

<body>

<h1>{{restaurant.name}}</h1>


{% for i in items %}

<div>

<p>{{i.name}}</p>

</br>

<p>{{i.description}}</p>

</br>

<p> {{i.price}} </p>

</br>

<a href="{{url_for('editMenuItem', restaurant_id = restaurant.id, menu_id = i.id) }}">Edit</a>

</br>

<a href = "{{url_for('deleteMenuItem', restaurant_id = restaurant.id, menu_id = i.id ) }}">Delete</a>

</div>


{% endfor %}
</body>

</html>
```

### Adding GET and POST methods in method/routing definition:

To add capability to handle GET/POST, we first need to add another argument in the routing which goes like:
```
@app.route('/restaurant/<int:restaurant_id>/new/', methods = ['GET', 'POST'])
def newMenuItem(restaurant_id):
    return "page to create a new menu item. Task 1 complete!"
```

Then, make a form in the html file to accept users input:

`newmenuitem.html`:
```
<html>
<body>
<h1> New Menu Item </h1>

<form action="{{url_for('newMenuItem', restaurant_id=restaurant_id )}}" method = 'POST'>

<p>Name:</p>

<input type='text' size='30' name='name'>

<input type='submit' value='Create'>

</form>

</body>
</html>
```
And now, to finally make the form work, import `request` from `flask` in python file. Also make use of `redirect` method to redirect the page to menu items after new menu is created and hence import also `redirect` and `urel_for` from `flask`:

```
from flask import Flask, render_template, request, redirect, url_for

...
@app.route('/restaurant/<int:restaurant_id>/new/', methods=['GET', 'POST'])
def newMenuItem(restaurant_id):
    if request.method == 'POST':
        newItem = MenuItem(
            name=request.form['name'], restaurant_id=restaurant_id)
        session.add(newItem)
        session.commit()
        return redirect(url_for('restaurantMenu', restaurant_id=restaurant_id))
    else:
        return render_template('newmenuitem.html', restaurant_id=restaurant_id)
```
### Editing menu item using GET/POST method:

The html file required for editing `editmenuitem.html` is:
```
<html>
<body>
<form action= "{{url_for('editMenuItem', restaurant_id = restaurant_id, menu_id=item.id)}}" method = 'POST'>

<p>Name:</p>
<input type = 'text' size='30' name = 'name' placeholder = '{{item.name}}'>

<input type='submit' value='Edit'>
</form>
<a href= '{{url_for('restaurantMenu', restaurant_id= item.restaurant_id)}}'>Cancel</a>
</body>
</html>
```

And to edit the menu items, we'll get the IDs of menu and the restaurant and will handle them using `render_template` for `GET` method and `redirect` and `url_for` for `POST`:

```
@app.route('/restaurants/<int:restaurant_id>/<int:menu_id>/edit',
           methods=['GET', 'POST'])
def editMenuItem(restaurant_id, menu_id):
    editedItem = session.query(MenuItem).filter_by(id=menu_id).one()
    if request.method == 'POST':
        if request.form['name']:
            editedItem.name = request.form['name']
        session.add(editedItem)
        session.commit()
        return redirect(url_for('restaurantMenu', restaurant_id=restaurant_id))
    else:
        # USE THE RENDER_TEMPLATE FUNCTION BELOW TO SEE THE VARIABLES YOU
        # SHOULD USE IN YOUR EDITMENUITEM TEMPLATE
        return render_template(
            'editmenuitem.html', restaurant_id=restaurant_id, menu_id=menu_id, item=editedItem)
```

### Deleting a selected menu item using GET/POST method:

The html file required for editing `deletemenuitem.html` is:

```
<html>

<body>
<h1> Are you sure you want to delete {{item.name}}? </h1>

<form action="{{ url_for('deleteMenuItem', restaurant_id=item.restaurant_id, menu_id=item.id)}}" method = 'post'>

<input type='submit', value = 'Delete'>




</form>

<a href = "{{ url_for('restaurantMenu', restaurant_id = item.restaurant_id)}}"> Cancel </a>
</body>

</html>
```

And for deleting function, we'll need both restaurant and menu IDs:

```
@app.route('/restaurants/<int:restaurant_id>/<int:menu_id>/delete',
           methods=['GET', 'POST'])
def deleteMenuItem(restaurant_id, menu_id):
    itemToDelete = session.query(MenuItem).filter_by(id=menu_id).one()
    if request.method == 'POST':
        session.delete(itemToDelete)
        session.commit()
        return redirect(url_for('restaurantMenu', restaurant_id=restaurant_id))
    else:
        return render_template('deleteconfirmation.html', item=itemToDelete)
```

### Adding message flashing to notify user that their response has been listened/handled:

First import `flash` from `flask` and then add a special `secret_key` to make it work properly:

```
from flask import Flask, render_template, request, redirect, url_for, flash

....
if __name__ == '__main__':
    app.secret_key = 'super_secret_key'
    app.debug = True
    app.run(host='0.0.0.0', port=5000)
```

And then to add a message, simply call `flash()` passing-in the message:

```
@app.route('/restaurants/<int:restaurant_id>/new', methods=['GET', 'POST'])
def newMenuItem(restaurant_id):

    if request.method == 'POST':
        newItem = MenuItem(name=request.form['name'], description=request.form[
                           'description'], price=request.form['price'], course=request.form['course'], restaurant_id=restaurant_id)
        session.add(newItem)
        session.commit()
        flash("new menu item created!")
        return redirect(url_for('restaurantMenu', restaurant_id=restaurant_id))
    else:
        return render_template('newmenuitem.html', restaurant_id=restaurant_id)
```
And now get that message to be displayed from python file and display using html file, where `menu.html` looks like:

```
<html>

<body>

<h1>{{restaurant.name}}</h1>

<!--MESSAGE FLASHING EXAMPLE -->
{% with messages = get_flashed_messages() %}
{% if messages %}

<ul>
{% for message in messages %}
  <li><strong>{{message}}</strong></li>
  {% endfor %}
</ul>
{% endif %}
{% endwith %}


{% for i in items %}

<div>

<p>{{i.name}}</p>

<p>{{i.description}}</p>

<p> {{i.price}} </p>

</div>


{% endfor %}
</body>

</html>
```

### Adding styles to stylyze the html output:

Flask can read and apply CSS/JavaScript/Media files if they're put under a director named `static` at the same location as the python file.

And then, we need to simply apply styles in our html file using:

```
<head>
	<link rel=stylesheet type=text/css href="{{ url_for('static', filename='styles.css') }}">
</head>
```

### Adding API endpoints to respond with JSON response:

First of all, we need to add a decorative method in our `database_setup` file so that it can return a JSON form of data:

Inside `database_setup.py`:

```
class MenuItem(Base):
    __tablename__ = 'menu_item'

    name = Column(String(80), nullable=False)
    id = Column(Integer, primary_key=True)
    description = Column(String(250))
    price = Column(String(8))
    course = Column(String(250))
    restaurant_id = Column(Integer, ForeignKey('restaurant.id'))
    restaurant = relationship(Restaurant)

# We added this serialize function to be able to send JSON objects in a
# serializable format
    @property
    def serialize(self):

        return {
            'name': self.name,
            'description': self.description,
            'id': self.id,
            'price': self.price,
            'course': self.course,
        }

```

And now, import `jsonify` from flask:

```
from flask import Flask, render_template, request, redirect, url_for, jsonify
```

Then create a endpoint method to return JSON response and `serialize` data before returning them:

```
@app.route('/restaurants/<int:restaurant_id>/menu/JSON')
def restaurantMenuJSON(restaurant_id):
    restaurant = session.query(Restaurant).filter_by(id=restaurant_id).one()
    items = session.query(MenuItem).filter_by(
        restaurant_id=restaurant_id).all()
    return jsonify(MenuItems=[i.serialize for i in items])
```

