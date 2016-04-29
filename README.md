# MyHero Spark Bot

This is the a Spark Bot for a basic microservice demo application.
This provides an interactive chat service for a voting system where users can vote for their favorite movie superhero.

Details on deploying the entire demo to a Mantl cluster can be found at
* MyHero Demo - [hpreston/myhero_demo](https://github.com/hpreston/myhero_demo)

The application was designed to provide a simple demo for Cisco Mantl.  It is written as a simple Python Flask application and deployed as a docker container.

Other services are:
* Data - [hpreston/myhero_data](https://github.com/hpreston/myhero_data)
* App - [hpreston/myhero_app](https://github.com/hpreston/myhero_app)
* Web - [hpreston/myhero_web](https://github.com/hpreston/myhero_web)
* Spark Bot - [hpreston/myhero_spark](https://github.com/hpreston/myhero_spark)

The docker containers are available at
* Data - [hpreston/myhero_data](https://hub.docker.com/r/hpreston/myhero_data)
* App - [hpreston/myhero_app](https://hub.docker.com/r/hpreston/myhero_app)
* Web - [hpreston/myhero_web](https://hub.docker.com/r/hpreston/myhero_web)
* Spark Bot - [hpreston/myhero_spark](https://hub.docker.com/r/hpreston/myhero_spark)

# Spark Developer Account Requirement
In order to use this service, you will need a Cisco Spark Account to use for the bot.  You can leverage your personal Spark Account or create a new one to be used by the service.  I recommend creating a new one to make testing easier (i.e. if you use your own it will be hard to chat with yourself).

Creating an account is free and only requires a working email account (each Spark Account needs a unique email address).  Visit [http://www.ciscospark.com](http://www.ciscospark.com) to signup for an account.

Developer access to Spark is also free and information is available at [http://developer.ciscospark.com](http://developer.ciscospark.com).

In order to access the APIs of Spark, this bot needs the Developer Token for your account.  To find it:

* Go to [http://developer.ciscospark.com](http://developer.ciscospark.com) and login with the credentials for your account.
* In the upper right corner click on your picture and click `Copy` to copy your Access Token to your clipboard
* Make a note of this someplace for when you need it later in the setup
  * **If you save this in a file, such as in the `Vagrantfile` you create later, be sure not to commit this file.  Otherwise your credentials will be availabe to anyone who might look at your code later on GitHub.**

## Basic Application Details

Required

* flask
* ArgumentParser
* requests

# Environment Installation

    pip install -r requirements.txt

# Basic Usage

In order to run, the service needs several pieces of information to be provided:
* App Server Address
* App Server Authentication Key to Use
* Spark Bot Authentication Key to Require in API Calls
* Spark Bot URL
* Spark Account Details
  * Spark Account Email
  * Spark Account Token

These details can be provided in one of three ways.
* As a command line argument
  - `python myhero_spark/myhero_spark.py --app "http://myhero-app.server.com" --appkey "APP AUTH KEY" --secret "BOT AUTH KEY"
  --boturl "http://myhero-spark.server.com" --botemail "myhero.demo@server.com" --token "HAAKJ1231KFSDFKJSDF1232132"`
* As environment variables
  - `export myhero_app_server=http://myhero-app.server.com`
  - `export myhero_app_key=APP AUTH KEY`
  - `export myhero_spark_bot_email=myhero.demo@server.com`
  - `export spark_token=HAAKJ1231KFSDFKJSDF1232132`
  - `export myhero_spark_bot_url=http://myhero-spark.server.com`
  - `export myhero_spark_bot_secret="BOT AUTH KEY"`
  - `python myhero_spark/myhero_spark.py`

* As raw input when the application is run
  - `python myhero_app/myhero_app.py`
  - `What is the app server address? http://myhero-app.server.com`
  - `App Server Key: APP AUTH KEY`
  - etc

A command line argument overrides an environment variable, and raw input is only used if neither of the other two options provide needed details.

# Accessing

Upon startup, the service should create a new Spark Room called "MyHero Demo" if one doesn't already exist for the given Spark User.  It also registers a webhook with the room to send all new messages to the service address.

Other users can be added to the room in one of the following ways.
* As a command line argument at startup
  * `python myhero_spark/myhero_spark.py --demoemail user@server.com`
* Making a REST API call
  * `curl -X PUT -H "key: BOT AUTH KEY" http://localhost:5000/demoroom/members -d '{"email":"user@server.com"}'`
* By sending a message to the room
  * `Add email user@server.com`

## Interacting with the Spark Bot
The Spark Bot is a very simple interface that is designed to make it intuitive to use.  Once in the room, simply say "hello", "help" (or anything else) to have the bot reply back with some instructions on how to access the features.

The bot is deisgned to look for key words to act on, and provide the basic help message for anything else.  The key words are:

* options
  * return a list of the current available options to vote on
* results
  * list the current status of voting results
* vote
  * send a private message to the sender to start a voting session
  * in the private room typing the name of one of the options will register a vote and end the session

An additional keyword of "add email" is also looked for.  This will have the bot add new users to the room.  Examples are:
* `add email joe@domain.intra`
  * add single user to the room
* `add emails joe@domain.intra, jane@domain.intra, bob@domain.intra`
  * add each of the three listed emails to the room


## REST APIs

The main service API is at the root of the applciation and is what is used for the Spark Webhooks.

There is an API available to view and add users to the Room.
* View list of users in the room
  * `curl -X GET -H "key: BOT AUTH KEY" http://localhost:5000/demoroom/members`
* Add a user to the room
  * `curl -X PUT -H "key: BOT AUTH KEY" http://localhost:5000/demoroom/members -d '{"email":"user@server.com"}'`

# Local Development with Vagrant

I've included the configuration files needed to do local development with Vagrant in the repo.  Vagrant will still use Docker for local development and is configured to spin up a CentOS7 host VM for running the container.

Before running `vagrant up` you will need to finish the Vagrant file configuration by adding the Spark Account Email and Token to the environment variables used by the container.  To do this:

* Make a copy of Vagrantfile.sample to use
  * `cp Vagrantfile.sample Vagrantfile`
* Edit `Vagrantfile` and add your details where indicated
  * `vim Vagrantfile`
  * Change the value for `myherospark_bot_email` and `spark_token` in the `docker.env` hash

To start local development run:
* `vagrant up`
  - You may need to run this twice.  The first time to start the docker host, and the second to start the container.
* Now you can interact with the API or interface at localhost:15001 (configured in Vagrantfile and Vagrantfile.host)
  - example:  from your local machine `curl -H "key: DevBot" http://localhost:15003/demoroom/members`
  - Environment Variables are configured in Vagrantfile for development

Each of the services in the application (i.e. myhero_web, myhero_app, and myhero_data) include Vagrant support to allow working locally on all three simultaneously.
