# Custom MongoDB cartridge for OpenShift

![mongodb-openshift](https://cloud.githubusercontent.com/assets/581999/13374624/f9509bc2-dd92-11e5-8068-a87c9c3f6312.png)

This is a custom OpenShift cartridge providing MongoDB 3.2.x + WiredTiger storage engine.
This is a fork from joevin22's cartridge, itself a fork from icflorescu's cartridge.
This integrates variables environment from cartridge openshift-origins for mongodb and installs all mongo binaries.

## Why

Because the standard OpenShift MongoDB cartridge is stuck at 2.4.

## When to use

When you need a quick and unsophisticated solution to run your application with the latest MongoDB version.

## How to

### Using [web console](https://openshift.redhat.com/app/console/applications)

Open your application in the [web console](https://openshift.redhat.com/app/console/applications), go to **"See the list of cartridges you can add"**, paste the URL below in **"Install your own cartridge"** textbox at the bottom of the page and click "Next".

    https://raw.github.com/ekynoxe/openshift-cartridge-mongodb/master/metadata/manifest.yml

Then you can use `OPENSHIFT_MONGODB_DB_URL` environment variable to connect from an application running in the main web cartridge.

To keep things flexible, the connection string stored in `OPENSHIFT_MONGODB_DB_URL` ends with `/` and **does not include a database name**. Use your application logic to name/create your database(s) as needed.

For instance, here's how you'd do it in a Node.js application using [Mongoose ODM](http://mongoosejs.com/):

    var mongoose = require('mongoose');
    ...
    // Mongoose will create database_name if necessary
    mongoose.connect(process.env.OPENSHIFT_MONGODB_DB_URL + 'database_name', { db: { nativeParser: true } });

### Using the command line

Make sure you have `rhc` installed (see [here](https://developers.openshift.com/en/managing-client-tools.html)).

If you want to **add** a MongoDB instance based on this cartridge to an existing application:

    rhc cartridge-add https://raw.githubusercontent.com/ekynoxe/openshift-cartridge-mongodb/master/metadata/manifest.yml \
      --app appname

...where `appname` is the name of your application.

To **create** an app based on the standard Node.js v0.10 cartridge and this one, run:

    rhc app create appname \
      nodejs-0.10 \
      https://raw.githubusercontent.com/ekynoxe/openshift-cartridge-mongodb/master/metadata/manifest.yml

If you want to use **the latest Node.js version**, you'll have to use a [custom Node.js cartridge](https://github.com/icflorescu/openshift-cartridge-nodejs) as well:

    rhc app create appname \
      https://raw.githubusercontent.com/icflorescu/openshift-cartridge-nodejs/master/metadata/manifest.yml \
      https://raw.githubusercontent.com/ekynoxe/openshift-cartridge-mongodb/master/metadata/manifest.yml

## Notes

- Can't guarantee this cartridge is production-ready. Some people use it though (on **their own responsibility**).
- This is a lean cartridge. To save space, just `mongod` is installed. No client libraries, no `mongo` console. **If you need external access to your data, use `rhc port-forward`**.
- Can't think of a safe way to make this cartridge auto-updatable. For now we'll just have to use `mongodump`, destroy the cartridge, install the new version, then do a `mongorestore`.

## Sources

  * openshift-cartridge-mongodb from icflorescu :  http://github.com/icflorescu/openshift-cartridge-mongodb
  * openshift-cartridge-mongodb from joevin22 :  https://github.com/joevin22/openshift-cartridge-mongodb
  * openshift-cartridge-mongodb from simonlopez :  https://github.com/simonlopez/openshift-cartridge-mongodb
  * openshift-cartridge-mongodb from openshift-origins : https://github.com/openshift/origin-server/tree/master/cartridges/openshift-origin-cartridge-mongodb

## License

The [ISC License](http://github.com/ekynoxe/openshift-cartridge-mongodb/blob/master/LICENSE).
