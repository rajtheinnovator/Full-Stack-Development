#### First of all make sure that SQLAlchemy and Python(2.7) are installed
First move to `/vagrant` directory by typing: `$ cd /vagrant`
Then check if Python is installed by running: `$ python --version`
To install Python: `$ sudo apt-get install python-pip`
If not installed properly, Python can be updated: `$ python -m pip install --upgrade pip`
To install SQLAlchemy: `$ python -m pip install sqlalchemy --user`

Once, both Python and SQLAlchemy are setup properly, run Python by typing `$ python`
#### Now import required packages one by one, to do CRUD operaions

>>> from sqlalchemy.orm import sessionmaker
>>> from database_setup import Base, Restaurant, MenuItem
>>> engine = create_engine('sqlite:///restaurantmenu.db')
>>> Base.metadata.bind = engine
>>> DBSession = sessionmaker(bind = engine)
>>> session = DBSession()

#### Create a Restaurant in database

>>> myFirstRestaurant = Restaurant(name = "Pizza Palace")
>>> session.add(myFirstRestaurant)
>>> session.commit()
>>> session.query(Restaurant).all()

#### Create a MenuItem in database

>>> cheesepizza = MenuItem(name = "Cheese Pizza", description = "Made with all natural ingredients and fresh mozzarella", course = "Entree", price = "$8.99", restaurant = myFirstRestaurant)
>>> session.add(cheesepizza)
>>> session.commit()
>>> session.query(MenuItem).all()
