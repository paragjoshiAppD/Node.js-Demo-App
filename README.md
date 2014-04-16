ACME Wine Cellar
=============

"ACME Wine Cellar" is a sample CRUD application built with with [Backbone.js](http://backbonejs.org/), [Twitter Bootstrap](http://getbootstrap.com/), [Node.js](http://nodejs.org/), [Express](http://expressjs.com/), [Redis](http://redis.io/) and [MongoDB](https://www.mongodb.org/). The application allows you to browse through a list of wines, as well as add, update, and delete wines.

These instructions will help you get the application up, running and instrumented with [AppDynamics' Node.js agent](). 

For any questions/concerns, please reach out to Omed Habib, Dynamic Languages Product Manager, at omed@appdynamics.com.

Requirements
-----------------
* root access
* AppDynamics 3.8+ Controller
* An AppDynamics [supported environment](http://docs.appdynamics.com/display/PRO14S/Supported+Environments+and+Versions+for+Node.js)
* [Git](http://git-scm.com/)


Installation
-----------------

Confirm you're running as root:

    $ sudo -i

Git clone the demo-app repo to a web directory:

    $ git clone git@github.com:Appdynamics/acme-wine-cellar.git acme-wine-cellar


Install [Nodejs.org](http://nodejs.org/download/). [See here](https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager) for instructions on your specific OS. 

Install [MongoDB](http://docs.mongodb.org/manual/installation/) and start Mongodb:

    $ service mongodb start

Install [Redis](http://redis.io/download) and start Redis. For all *nix systems, use [these](http://redis.io/download) instructions. For Ubuntu, try this:

    $ sudo apt-get install -y python-software-properties
    $ sudo add-apt-repository -y ppa:rwky/redis
    $ sudo apt-get update
    $ sudo apt-get install -y redis-server

Install dependent Node.js modules based on package.json declaration

    $ npm install  <in-your-winecellar-home>

Install Node.js Agent
-----------------

Follow the instructions on [installing the AppDynamics Node.js Agent](http://docs.appdynamics.com/display/PRO14S/Install+the+App+Agent+for+Node.js).

Be sure to have a 3.8+ controller and insert your variables into the necessary javascript in the head of your front controller. 

```javascript
require("appdynamics").profile({
  controllerHostName: <controller host name>,
  controllerPort: <controller port number>,
  accountName: <AppDynamics account name>, //Required for a controller running in multi-tenant mode.
  accountAccessKey: <AppDynamics account key>, //Required for a controller running in multi-tenant mode.
  applicationName: <app_name>,
  tierName: <tier_name>,
  nodeName:<node_name>, //Prefix to the full node name.
  debug: true //Debug is optional; defaults to false.
 });

```

Run Node Cellar App
-----------------

For a single process

    $ node server.js
    
For a 5 process cluster

    $ node server-cluster.js

To keep your server up, run the aforementioned commands with "forever" instead:

    $ forever start server.js
    $ forever start server-cluster.js
    
You can view the Node.js server running via

    $ forever list

Access http://[hostname]

FAQs
-----------------

Q: After going through all the install steps, I still cannot load the Wine Cellar website

A: Port 80 might be blocked. Try the following:

    iptables -I INPUT -p tcp --dport 80 -j ACCEPT
    sudo service iptables save
    
Q: I just opened up port 80 in the iptables, yet still no luck. I'm running on EC2. 

A: Port 80 might still be blocked on EC2. You must go to your EC2 management console and assign a [Security Group](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-network-security.html) to your EC2 instance that has port 80 opened. 

Q: When I tried to clone the repository, I received a fatal error "Could not read from remote repository". 

A: You must generate an SSH key and add it to your Github account. [See here for instructions on how.](https://help.github.com/articles/generating-ssh-keys)  Make sure the key is associated with the user you are using - if you are root, create the key as root.


Q: When I try to run 'forever start server.js', I get the following error "/usr/bin/env: node: No such file or directory".

A: On Ubuntu, Node.js is invoked via 'nodejs'. A simple soft link will help:

    $ ln -s /usr/bin/nodejs /usr/bin/node
