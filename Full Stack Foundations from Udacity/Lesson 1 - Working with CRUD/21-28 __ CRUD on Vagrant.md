#### First of all make sure that SQLAlchemy and Python(2.7) are installed
First move to `/vagrant` directory by typing: `$ cd /vagrant`
Then check if Python is installed by running: `$ python --version`
To install Python: `$ sudo apt-get install python-pip`
If not installed properly, Python can be updated: `$ python -m pip install --upgrade pip`
To install SQLAlchemy: `$ python -m pip install sqlalchemy --user`

Once, both Python and SQLAlchemy are setup properly, run Python by typing `$ python`
#### Now import required packages one by one, to do CRUD operaions


```
>>> from sqlalchemy.orm import sessionmaker
>>> from database_setup import Base, Restaurant, MenuItem
>>> engine = create_engine('sqlite:///restaurantmenu.db')
>>> Base.metadata.bind = engine
>>> DBSession = sessionmaker(bind = engine)
>>> session = DBSession()
```

#### Create a Restaurant in database

```
>>> myFirstRestaurant = Restaurant(name = "Pizza Palace")
>>> session.add(myFirstRestaurant)
>>> session.commit()
>>> session.query(Restaurant).all()
```

### CRUD-Create:
#### Create a MenuItem in database

```
>>> cheesepizza = MenuItem(name = "Cheese Pizza", description = "Made with all natural ingredients and fresh mozzarella", course = "Entree", price = "$8.99", restaurant = myFirstRestaurant)
>>> session.add(cheesepizza)
>>> session.commit()
>>> session.query(MenuItem).all()
```

### CRUD-Read:
#### Read single item:
```
>>> firstResult = session.query(Restaurant).first()
>>> firstResult.name
```

#### Add lots of menu items, from the file `lotsofmenus.py` by running `$ python lotsofmenus.py`
#### Read all menu items

```
>>> items = session.query(MenuItem).all()
>>> for item in items:
>>> 	print item.name
```

### CRUD-Update:

#### Filter item of interest by name first
```
>>> veggieBurgers = session.query(MenuItem).filter_by(name = 'Veggie Burger')
>>> for veggieBurger in veggieBurgers:
...     print veggieBurger.id
...     print veggieBurger.price
...     print veggieBurger.restaurant.name
...     print "\n"

```

Then choose one particular item by id, to be updated
```
>>> urbanVeggieBurger = session.query(MenuItem).filter_by(id = 8).one()
>>> print urbanVeggieBurger.price
```

#### Update single item:
Then update the selected item:
```
>>> urbanVeggieBurger.price = '$2.99'
>>> session.add(urbanVeggieBurger)
>>> session.commit()
```

Now verify if the update was done successfully:
```
>>> veggieBurgers = session.query(MenuItem).filter_by(name = 'Veggie Burger')
>>> for veggieBurger in veggieBurgers:
...     print veggieBurger.id
...     print veggieBurger.restaurant.name
...     print veggieBurger.price
...     print "\n"
```

#### Update multiple items:

```
>>> for veggieBurger in veggieBurgers:
...     if veggieBurger.price != '$2.99':
...         veggieBurger.price = '$2.99'
...         session.add(veggieBurger)
...         session.commit()
...
>>> for veggieBurger in veggieBurgers:
...     print veggieBurger.id
...     print veggieBurger.price
...     print veggieBurger.restaurant.name
...     print "\n"
```

### CRUD-Delete:
#### Delete a single item:
```
>>> spinach = session.query(MenuItem).filter_by(name = 'Spinach Ice Cream').one()
>>> print spinach.restaurant.name
Auntie Ann's Diner'
>>> session.delete(spinach)
>>> session.commit()
```
#### Check if the item was actually deleted or not by quetying for it. If it was deleted, python will throw an error that row doesn't exist
```
>>> spinach = session.query(MenuItem).filter_by(name = 'Spinach Ice Cream').one()
```