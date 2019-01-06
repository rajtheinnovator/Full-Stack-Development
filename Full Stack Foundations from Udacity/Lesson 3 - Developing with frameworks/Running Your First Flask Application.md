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
The contents of `projects.py` at this stage will look like(don't forget to import **render_template** from flask:
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