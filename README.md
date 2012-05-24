Web2Py on OpenShift
===================

This git repository helps you get up and running quickly w/ a web2py installation
on OpenShift.  The web2py project name used in this repo is 'web2py'
but you can feel free to change it.  Right now the backend is sqlite3 and the
database runtime is @ $OPENSHIFT_DATA_DIR/databases/sqlite3.storage.

$OPENSHIFT_DATA_DIR isn't necessary when using the db cartridges as all data is 
stored in the relational or nosql database.

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

That's it, you can now checkout your application at (admin application currently does not work in cloud):

    http://web2py-$yournamespace.rhcloud.com

The default web2py application that loads will be the "welcome" application. You can change this by modifying
the routes as you usually would with web2py.

