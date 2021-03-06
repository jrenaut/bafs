# BikeArlington Freezing Saddles

**IMPORTANT**
This is just a development snapshot.  Expect it to be very broken.

The BikeArlington Freezing Saddles ("bafs") project is a python web application that integrates with Strava APIs to 
provide charting (and intended to provide other reports too) for the Freezing Saddles cycling competition organized
on BikeArlington forums.

## Dependencies
 
* Python 2.6+.  (This will not currently work with Python 3.)
* Setuptools/Distribute
* Virtualenv

## Installation

Eventually this will be installable via setuptools/distribute/pip; however, currently you must 
download / clone the source and run the python setup.py install command.

Here are some instructions for setting up a development environment, which is more appropriate
at this juncture:

	shell$ git clone https://github.com/hozn/bafs.git
	shell$ cd bafs
	shell$ python -m virtualenv --no-site-packages --distribute env
	shell$ source env/bin/activate
    (env) shell$ python setup.py develop

We will assume for all subsequent shell examples that you are running in that activated virtualenv.  (This is denoted by using 
the "(env) shell$" prefix before shell commands.)    
   
### Database Setup

This application requires MySQL.  I know, MySQL is a horrid database, but since I have to host this myself (and my shared hosting
provider only supports MySQL), it's what we're doing.

You should create a database and create a user that can access the database.  Something like this should work in the default case:

	shell$ mysql -uroot
	mysql> create database bafs;
	mysql> grant all on bafs.* to bafs@localhost;

## Basic Usage

### Configuration

Configuration files are Python files (since we're using Flask framework).  There is a default one configured for the 2013 BAFS competition;
if you want to do something else you'll need to specify an environment variable that points to your config file:

	(env) shell$ BAFS_SETTINGS=/path/to/settings.cfg <bafs-script>
	
Probably the most likely thing you'll want to configure in your settings.cfg file is the database connection information.
```python

# The SQLALchemy connection URL for your MySQL database.
SQLALCHEMY_DATABASE_URI = 'mysql://bafs@localhost/bafs'
```
	
### Synchronizing

You need to pull data from Strava (and any other APIs) into the local database for reporting.

	(env) shell$ BAFS_SETTINGS=/path/to/settings.cfg bafs-sync
	
It is slow the first time because it does not parallelize the work.  By default it will only pull down rides that aren't
already in the system.  Periodically (daily?) you probably also want to clear things out and pull down everything (e.g. in
case someone edited a ride, etc.)

	(env) shell$ BAFS_SETTINGS=/path/to/settings.cfg bafs-sync --clear

### Running the (Development) Server

You can start up the Flask development server for testing using the `bafs-server` command.

	(env) shell$ BAFS_SETTINGS=/path/to/settings.cfg bafs-server
	
