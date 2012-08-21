Django Sentry on Heroku
=======================

Get this up and runnnig by::

    heroku create --stack cedar
    git push heroku
    heroku addons:add piggyback_ssl
    heroku addons:add shared-database:5mb
    heroku config:add SENTRY_KEY=some-random-access-key
    heroku config:add SENTRY_CONF=sentry_conf.py
    heroku scale web=1

If you want e-mails start with this::

    heroku addons:add sendgrid:starter
    heroku config:set MAIL_TO=you@example.com

And finally go to heroku.com, find your app and the SendGrid addon,
and complete the account setup.
