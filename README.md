Django Sentry Heroku MultiAccounts
==================================

Forked from https://github.com/doptio/django-sentry-on-heroku

Assuming you have gits and files all over the place, some things that might help. Go to your code folder and clone this. Remember from terminal this will create the folder for you. Tower.app? Not so much so be careful. Just use terminal for now….

	git clone git@github.com:eschipul/django-sentry-on-heroku.git

Edit your .git/config file in the root of your project to reflect your new master repository. For me it was

	[remote "heroku"]
	url = git@heroku.com:xyz-sentry-server.git

If you are using multiple accounts on heroku you may need to change the "git@heroku.com:" part to something like "@heroku.sentry.com:" or whatever you named it. You can check this in ~/ssh/config

Double check your settings in sentry_conf.py (note the underscore in the filename). And don't worry about the mail server for now - we'll use sendgrid to get started with that.

    heroku create xyz-sentry-server
    git push heroku master

you should see something like

"http://xyz-sentry-server.herokuapp.com deployed to Heroku" - great! Next:
	
	heroku addons:add heroku-postgresql:dev --app xyz-sentry-server
	
this will give you a database name like "HEROKU_POSTGRESQL_BLACK" - now promote it.

	heroku pg:promote HEROKU_POSTGRESQL_BLACK --app xyz-sentry-server
	
Now generate an ugly 64 bit key. From https://github.com/LiiquOy/sentry-on-heroku he suggests the following from a Python prompt:

	>>> import base64
	>>> import os
	>>> base64.b64encode(os.urandom(40))
	'nIumxPtjDuHunpX2D+LP27l8WX967DgjBRiSLz/XrfAp491bu3pnzw=='

Put the ugly random number in place of the {$generated-key} part below
	
	heroku config:add SENTRY_KEY={$generated-key} --app xyz-sentry-server
	heroku config:add SENTRY_CONF=sentry_conf.py --app xyz-sentry-server
	heroku addons:add piggyback_ssl --app xyz-sentry-server
	heroku config:add URL_PREFIX=https://xyz-sentry-server --app xyz-sentry-server
	heroku run sentry --config=sentry_conf.py createsuperuser --app xyz-sentry-server
	
It'll ask for a username and confirm passwords. Follow the prompts. If you named it "xyzSuperUser" Then the next line would be:
	
	heroku run sentry --config=sentry_conf.py repair --owner=xyzSuperUser --app xyz-sentry-server
	
Almost done - keep on trucking.
	
	heroku addons:add sendgrid:starter --app xyz-sentry-server
	heroku config:set MAIL_TO=you@example.com --app xyz-sentry-server
	heroku scale web=1 --app xyz-sentry-server
	heroku open --app xyz-sentry-server
	
Now login to your new sentry server that should have popped up using the superuser you created above. Start creating your projects and then you are on to configuring your webs to post to the remote URL.