Web2Py on OpenShift
===================

This git repository helps you get up and running quickly w/ a web2py installation
on OpenShift.  The web2py project name used in this repo is 'web2py'

When you access this application for the first time, the sqlite database is
initialized from the defined DAL in your model. This is the stock database that is created
for the welcome application in web2py.

Running on OpenShift
----------------------------

Create an account at http://openshift.redhat.com/

Create a python-2.6 application

    rhc app create -a web2py -t python-2.6

Add this upstream repo

    cd web2py
    git remote add upstream -m master git://github.com/prelegalwonder/openshift_web2py.git
    git pull -s recursive -X theirs upstream master
    
Then push the repo upstream

    git push

That's it, you can now checkout your application at:

    http://web2py-$yournamespace.rhcloud.com

The default web2py application that loads will be the "welcome" application. You can change this by modifying
the routes as you usually would with web2py.

For the admin app to work you must put your password hash in parameters_8080.py in wsgi/web2py/. 

~~**Update:** Currently if you use the IDE in the cloud it doesn't save it to the remote git repo. Every time the in-cloud app is started its copied to a separate runtime directory. Definitely backup your changes. I'm thinking of how to handle this for people who want to use the in-cloud IDE. Keep in mind you can also use the openshift deployer in master from a seperate local vanilla instance.~~


**Update:** Using the IDE should now save changes in the gear instance data dir. The caveat here is that when you do a git pull, it will not pull your changes back locally. Continued investigation is needed here. 

Database Setup
-----------------------------
**Enabling db cartridges:**
If you want to use something beyond the sqlite provider that is default for applications, you'll need to install one of
Openshift's db cartridges. You can see what's available with the following command.

    $> rhc app cartridge
    
    List of supported embedded cartridges:

    Obtaining list of cartridges (please excuse the delay)...
    mongodb-2.0, cron-1.4, mysql-5.1, postgresql-8.4, haproxy-1.4, 10gen-mms-agent-0.1, phpmyadmin-3.4, metrics-0.1, rockmongo-1.1, jenkins-client-1.4
    
Once you've identified which db cartridge you'd like to use you can install it with the following command.

    $> rhc app cartridge add -c <cartridge_name> -a <your_app>

This will output some info regarding how to connect to your db. You can record this somewhere if you like, however all of this
information is available within the gear and you'll see how to access it in the next section.

**Connecting to db:**
Openshift makes using it's available database cartridges very simple by providing a list of various environment variables 
you can use depending on the connection format you want to use.

Openshift provides two classes of db cartridge; RDBMS and NOSQL. RDBMS uses the environment variable OPENSHIFT\_DB\_\* where NOSQL uses OPENSHIFT\_NOSQL\_DB\_\* 

MySQL and PostgreSQL are RDBM's style of database, where MongoDB is a NOSQL style database. 

So if you wanted to get the host of your mysql environment, you'd use OPENSHIFT\_DB\_HOST vs OPENSHIFT\_NOSQL\_DB\_HOST for mongodb. You can get a list of available variables by loging into your gear and executing:

    openshift_gear$> env | grep DB

Using web2py, the most useful variable will most likely be the URL when defining the DAL in the model. Here's a mongodb example for your model (db.py):
    
    from os import environ as myenv
    DB_URL = myenv['OPENSHIFT_NOSQL_DB_URL']+myenv['OPENSHIFT_APP_NAME']

    db = DAL(DB_URL)
    
    (Note: If using postgresql, put the following after the DB_URL line and before the db = DAL line. Thanks srochy)
    DB_URL = DB_URL.replace("postgresql://", "postgres://")

If you want to view your data directly you can do this via the console tools logged directly into the Openshift gear, or install the cooresponding web management cartridge (ie. rockmongo, phpmyadmin).

For more information on Openshift and how to accomplish various tasks, see: https://access.redhat.com/knowledge/docs/OpenShift/

Now with NewRelic Config
------------------------------

If you want to monitor the performance, user-experience and usage statistics, you can sign up for an account at newrelic.com.
After signing up, simply put your license key in wsgi/web2py/newrelic.ini

    license_key = <your_license_key_from_your_newrelic_account>

And whatever you want the app labeled as in your newrelic account can be set via the name property.
    app_name = web2py

If you don't intend to use NewRelic or want to turn it off temporarily, simply comment out everything after #NewRelic Monitoring 
in the wsgi/application file. 

    #NewRelic Monitoring                                                      
    import newrelic.agent              
    newrelic.agent.initialize(NEWRELIC_INI)  
                                                                                                                                                        
    application = newrelic.agent.wsgi_application()(application)
