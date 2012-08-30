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
