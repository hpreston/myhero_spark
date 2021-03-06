# MyHero Spark Bot

This is the a Spark Bot for a basic microservice demo application.
This provides an interactive chat service for a voting system where users can vote for their favorite movie superhero.

Details on deploying the entire demo to a Mantl cluster can be found at

* MyHero Demo - [hpreston/myhero_demo](https://github.com/hpreston/myhero_demo)

The application was designed to provide a simple demo for Cisco Mantl.  It is written as a simple Python Flask application and deployed as a docker container.

**NOTE: To leverage the Spark Bot Service, your Mantl Cluster MUST be configured for deployed applications to be accessible from the public Internet.  This is because it relies on the Spark Cloud to be able to send a WebHook to the myhero_spark application you run in Mantl***

Other services are:

* Data - [hpreston/myhero_data](https://github.com/hpreston/myhero_data)
* App - [hpreston/myhero_app](https://github.com/hpreston/myhero_app)
* Web - [hpreston/myhero_web](https://github.com/hpreston/myhero_web)
* UI - [hpreston/myhero_ui](https://github.com/hpreston/myhero_ui)
* Ernst - [hpreston/myhero_ernst](https://github.com/hpreston/myhero_ernst)
  * Optional Service used along with an MQTT server when App is in "queue" mode
* Spark Bot - [hpreston/myhero_spark](https://github.com/hpreston/myhero_spark)
  * Optional Service that allows voting through IM/Chat with a Cisco Spark Bot
* Tropo App - [hpreston/myhero_tropo](https://github.com/hpreston/myhero_tropo)
  * Optional Service that allows voting through TXT/SMS messaging


The docker containers are available at

* Data - [hpreston/myhero_data](https://hub.docker.com/r/hpreston/myhero_data)
* App - [hpreston/myhero_app](https://hub.docker.com/r/hpreston/myhero_app)
* Web - [hpreston/myhero_web](https://hub.docker.com/r/hpreston/myhero_web)
* UI - [hpreston/myhero_ui](https://hub.docker.com/r/hpreston/myhero_ui)
* Ernst - [hpreston/myhero_ernst](https://hub.docker.com/r/hpreston/myhero_ernst)
  * Optional Service used along with an MQTT server when App is in "queue" mode
* Spark Bot - [hpreston/myhero_spark](https://hub.docker.com/r/hpreston/myhero_spark)
  * Optional Service that allows voting through IM/Chat with a Cisco Spark Bot
* Tropo App - [hpreston/myhero_tropo](https://hub.docker.com/r/hpreston/myhero_tropo)
  * Optional Service that allows voting through TXT/SMS messaging

# Spark Developer Account Requirement

In order to use this service, you will need a Cisco Spark Account to use for the bot.  The bot is built for ease of use, meaning any message to the account used to create the Bot will be acted on and replied to.  This means you'll need to create a new Spark account for the demo.  

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

	```
	python myhero_spark/myhero_spark.py \
	  --app "http://myhero-app.server.com" \
	  --appkey "APP AUTH KEY" \
	  --secret "BOT AUTH KEY" \
	  --boturl "http://myhero-spark.server.com" \
	  --botemail "myhero.demo@server.com" \
	  --token "HAAKJ1231KFSDFKJSDF1232132"
	```
  
* As environment variables

	```
	export myhero_app_server=http://myhero-app.server.com`
	export myhero_app_key=APP AUTH KEY`
	export myhero_spark_bot_email=myhero.demo@server.com`
	export spark_token=HAAKJ1231KFSDFKJSDF1232132`
	export myhero_spark_bot_url=http://myhero-spark.server.com`
	export myhero_spark_bot_secret="BOT AUTH KEY"`
	python myhero_spark/myhero_spark.py`
	```

* As raw input when the application is run

	```
	python myhero_app/myhero_app.py`
	What is the app server address? http://myhero-app.server.com`
	App Server Key: APP AUTH KEY`
	 .
	 .
	
	```

A command line argument overrides an environment variable, and raw input is only used if neither of the other two options provide needed details.

# Accessing

Upon startup, the service registers a webhook to send all new messages to the service address.


## Interacting with the Spark Bot
The Spark Bot is a very simple interface that is designed to make it intuitive to use.  Simply send any message to the Spark Bot Email Address to have the bot reply back with some instructions on how to access the features.

The bot is deisgned to look for commands to act on, and provide the basic help message for anything else.  The commands are:

* /options
  * return a list of the current available options to vote on
* /results
  * list the current status of voting results
* /vote {{ option }} 
  * Place a vote for the 'option'
* /help 
  * Provide a help message

## REST APIs

# /

The main service API is at the root of the applciation and is what is used for the Spark Webhooks.

# /hello/:email 

There is an API call that can be leveraged to have the Spark Bot initiate a chat session with a user.  This API responds to GET requests and then will send a Spark message to the email provided.  

Example usage

```
curl http://myhero-spark.domain.local/hello/user@email.com 
```

# /health 

This is an API call that can be used to test if the Spark Bot service is functioning properly.
  
```
curl -v http://myhero-spark.domain.local/health 

*   Trying...
* Connected to myhero-spark.domain.local (x.x.x.x)
> GET /health HTTP/1.1
> Host: myhero-spark.domain.local
> User-Agent: curl/7.43.0
> Accept: */*
> 
* HTTP 1.0, assume close after body
< HTTP/1.0 200 OK
< Connection: close
< 
* Closing connection 0
Service up. 
```

# Local Development with Vagrant

I've included the configuration files needed to do local development with Vagrant in the repo.  Vagrant will still use Docker for local development and requires the following be installed on your laptop: 

* [Vagrant 2.0.1 or higher](https://www.vagrantup.com/downloads.html)
* [Docker](https://www.docker.com/community-edition)

Before running `vagrant up` you will need to finish the Vagrant file configuration by adding the Spark Account Email and Token to the environment variables used by the container.  To do this:

* Make a copy of Vagrantfile.sample to use
  * `cp Vagrantfile.sample Vagrantfile`
* Edit `Vagrantfile` and add your details where indicated
  * `vim Vagrantfile`
  * Change the value for `myherospark_bot_email` and `spark_token` in the `docker.env` hash

To start local development run:
* `vagrant up`
* Now you can interact with the API or interface at localhost:15001 (configured in Vagrantfile)
  - example:  from your local machine `curl -H "key: DevBot" http://localhost:15003/demoroom/members`
  - Environment Variables are configured in Vagrantfile for development

Each of the services in the application (i.e. myhero_web, myhero_app, and myhero_data) include Vagrant support to allow working locally on all three simultaneously.
