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
