---
layout: post
title:  "Prediction.IO Sport Predictions Tutorial"
date:   2014-10-06 16:50:00
categories: ml
---

#Sport predictions with prediction.io
##Setup
###Install sportdb
In this tutorial we will be using the great opensource project **sportdb**, to retrieve the sport data we are interested in to make predictions. Sportdb is a collection of freely avaialble sport statistics, it also comes with its own database service (sportkit), that you can use to easily query the database. Sportkit comes in several flavours, if you are interested have a look here: https://github.com/sportkit, but in this tutorial we will be using the node.js server.

There are also several ways how you can intall sportdb. The recommended way in this tutorial is to follow the tutorial here: https://github.com/openfootball/build-shell
Basically, you'll have to clone the repository and run the downloadAndBuild.sh script. Make sure you have sportdb installed though, which can be done with the following command: ‘gem install sportdb‘ (you might need root priviledges, i.e. sudo).

Once sportdb has been installed and you ran downloadAndBuild.sh, a file called *football.db* will be created in the *temp* subdirectory of the folder you ran downloadAndBuild.sh in. Copy *football.db* into the root folder of *sport.db.starter.node.js* and then in that directory type: ‘npm install’ (this shouldn't require root priveldges, if it does, there is probably something wrong with you npm installation) intially to install the node module. This only needs to be done once, then every time you want to start the server type: ‘node ./server’ in the same directory. This should start your local sportdb server, that you can query with HTTP GET requests, for example, if you go to *http://localhost:9292/events* with your favourite web browser, you should see a JSON response in your browser such as:

[
  {
    "key": "world.quali.africa.2014",
    "title": "World Cup Quali Africa 2014"
  },
  {
    "key": "world.quali.asia.2014",
    "title": "World Cup Quali Asia (w/ Australia) 2014"
  },
  ...
]

It is important to get this set up before we can continue with the example.
