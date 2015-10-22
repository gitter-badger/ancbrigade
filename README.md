ancfinder
==========

[![Join the chat at https://gitter.im/mcwhittemore/ancbrigade](https://badges.gitter.im/Join%20Chat.svg)](https://gitter.im/mcwhittemore/ancbrigade?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)

A website about DC's Advisory Neighborhood Commission system.

Contributing
------------

If you plan to contribute to the repo, you should set up your own fork and open pull requests with any commits you make. Please note that all contributions are made under a [CC0 license](LICENSE.md).

If you're not familiar with forking, Github has a [useful guide](https://help.github.com/articles/fork-a-repo).

Installation
------------

Get the repo running on your machine:

	git clone --recursive https://github.com/codefordc/ancfinder
	cd ./ancfinder
	git submodule init
	git submodule update

Note --recursive on 'git clone' to get the submodule dependencies.

If you're using a virtual environment, set that up:

	virtualenv .env # Maybe one day we'll use '-p python3' but dotcloud makes that harder
	. .env/bin/activate

Then, install dependencies and set up the site database:

	sudo apt-get install libxslt1-dev # Not necessary on OSX
	pip install -r requirements.txt
	./manage.py syncdb

If you are missing libxml on OSX run `xcode-select --install`

Get our latest data files:

	wget -O static/ancs.json http://ancfinder.org/static/ancs.json
	wget -O static/meetings.json http://ancfinder.org/static/meetings.json

If it seems like you're missing stuff, also try

    git submodule update

Running the Site
----------------

Just run:

	. .env/bin/activate
	./manage.py runserver

Then view the site in your browser at the address shown to you.

Updating the Code
-----------------

As we make code changes you may need to run:

	git pull --rebase
	git submodule update --init
	./manage.py syncdb

If we modify the database schema, you may need to delete ancfindersite/database.sqlite and run `./manage.py syncdb` to recreate your database from scratch since we don't currently have a way to upgrade database schemas.

Updating Static Data
--------------------

The ANC/SMD metadata is stored statically in `static/ancs.json`. To update this file
from our Google Doc, our scrapers, and other external data sources, run:

	python3 scripts/update_anc_database.py

You will need to provide your Google email & password and a Census API key, which you can get at http://www.census.gov/developers/tos/key_request.html. For convenience, you can also store these credentials in a file named "update_anc_database_creds.py" in this (root) directory and put in it:

	google_email="your.address@gmail.com"
	google_password="your google password"
	census_api_key="your Census API key"

You can also selectively update just some of the data (because updating some takes a long time) using command line arguments:

	python3 scripts/update_anc_database.py [--base] [--terms] [--gis] [--neighborhoods] [--census] [--census-analysis]

And to fetch the latest ANC meetings calendar (note that it currently requires Python 2):

	python scripts/update_meeting_database.py

There are also several scripts to grab data for use in `update_anc_database.py`. They are:

        scripts/update_abra.py # Liquor licenses
        scripts/update_building_permits.py # Building permits
        scripts/update_terms.py # Terms served by commissioners
		scripts/update_311.py # 311 requests
		scripts/update_crimes.py # Doesn't really do anything right now

All of the scripts should be run occasionally to make sure the data shown on the site is up-to-date.

Deployment
----------

When deploying the code to the live server, remember to update the static files:

	./manage.py collectstatic
