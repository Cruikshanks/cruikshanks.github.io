---
layout: post
title: "Docker for development"
description: "Why Docker for development is different, with examples"
date: 2017-10-03
permalink: blog/docker-for-development
---

Using [Docker](https://www.docker.com/) for local development is different to actually building images that you expect to be deployed and run as containers elsewhere.

The aim for local development is to simplify creating the necessary environment for running your app.

So for example the main docker tutorial assumes I have Python and PIP installed locally so that I can then grab the project's dependencies. The `Dockerfile` focuses on copying the project including dependencies into an image, and specifiying its startup behaviour.

For local development I'm more interested in starting a container that contains everything I need to build and run my app, whilst still be able to make changes to the project, and have that reflected in the running application (assuming you're building in dynamic rather than a static language).

This post details simple examples; one for [Ruby](https://www.ruby-lang.org) development, and the other for [Node.js](https://nodejs.org/).

## Docker for Ruby

Here we demonstrate using **Docker** to provide an environment for developing a basic ruby [Sinatra](http://www.sinatrarb.com/) application.

Specifically we have an application with a simple root `GET`, which returns `Hello world` as we want to demonstrate a very basic app that

- has a dependency and so needs a `Gemfile`
- has something we can interact with from the host
- is basic enough that it won't complicate what we're trying to show in **Docker**

### The project (ruby)

First create the `Gemfile`

```ruby
source 'http://rubygems.org'

# Sinatra is a DSL for quickly creating web applications in Ruby with minimal
# effort
gem 'sinatra'

# Restarts an app when the file system changes
gem 'rerun'

```

Then create a file called `server.js`

```ruby
require 'sinatra'

set :port, 4567
set :bind, '0.0.0.0' # Required to bind to all interfaces

get '/' do
  'Hello world'
end

```

Finally create `Dockerfile`

```docker
# Sets the base image
FROM ruby:2.4.1

# Use an env var to hold what will be the main directory within the container.
# As we need to refer to it a few times it can be easier to set an env var
# and then refer to it in subsequent commands
ENV APP_HOME /app

# This will execute any commands in a new layer on top of the current image and
# commit the results. In this case we create the /app directory
RUN mkdir $APP_HOME

# Sets the working directory for any RUN, CMD, ENTRYPOINT, COPY and ADD
# instructions that follow it
WORKDIR $APP_HOME

# Copies new files or directories from <src> and adds them to the filesystem of
# the container at the path <dest>
COPY . $APP_HOME

# Here we tell docker to download our projects dependencies in another layer
# that then gets committed
RUN bundle install

# Informs Docker that the container should listen on the specified network port
# at runtime. EXPOSE does not make the ports of the container accessible to the
# host. To do that, you must use still use the `docker run` -p flag
EXPOSE 4567

# There can only be one CMD instruction in a Dockerfile. If you list more than one CMD then only the last CMD will take effect.

# Purpose of a CMD is to provide defaults for an executing container. In this
# case it sets the command and arguments to be executed when running the image
CMD ["rerun", "--background", "server.rb"]

```

If this is a bit too much hassle, feel free to make use of one [I made earlier](https://github.com/Cruikshanks/docker-for-dev/tree/master/ruby).

### Build (ruby)

Now we've got our project, we need to create an image from it. Call

```bash
docker build -t docker-for-ruby-dev .
```

Don't forget the `.` at the end there! You can call it something *docker-for-ruby-dev*. This is just what I have named it for my example. If we ran this on a machine clean of other **Docker** images, running `docker images` should display something like this

```bash
REPOSITORY            TAG                 IMAGE ID            CREATED             SIZE
docker-for-ruby-dev   latest              5ec4ed806769        3 minutes ago       713MB
ruby                  2.4.1               3630c02d3d1b        3 weeks ago         679MB
```

### Run (ruby)

So we've built an image, now we need to create and run a container from it

```bash
docker run --rm -v "$(pwd)":/app -p 4567:4567 docker-for-ruby-dev
```

Breaking down this command we have

- `docker run` first creates a writeable container layer over the specified image, and then starts it using the specified command
- `--rm` automatically removes the container on exit
- `-v "$(pwd)":/app` mounts the local folder to `/app` in the container
- `-p 4567:4567` bind port 4567 on the host to 4567 in the container <host:container>
- `docker-for-ruby-dev` the specified image to create and run a container from

Running in this way ensures we see any output from our app. Add the `-d` flag if you prefer the container to run as a daemon.

### Develop (ruby)

Now we have a container we want to be able to make changes. As we have mounted the project's root folder to `/app` in the container we can open it in our preferred editor, make changes, and see them reflected in the app.

To prove this using our demo project first run `curl -XGET http://localhost:4567` from the host. The response should be `Hello world`.

Edit `server.rb` and change line 7 to be `'Hello docker world'`. Running the same `curl` command again should result in `Hello docker world`.


## Docker for node.js

Now we'll demonstrate using **Docker** to provide an environment for developing a basic [Express](https://expressjs.com/) app.

Again we just want an application with a simple root `GET`, which returns `Hello world`, and covers the following

- has a dependency and so needs a `package.json`
- has something we can interact with from the host
- is basic enough that it won't complicate what we're trying to show in **Docker**

### The project (node)

Create the `package.json` first

```json
{
  "name": "docker-for-node-dev",
  "version": "0.1.0",
  "description": "Example site used to demo Docker for node development",
  "homepage": "https://github.com/Cruikshanks/docker-for-dev",
  "license": "MIT",
  "author": {
    "name": "Alan Cruikshanks",
    "url": "https://github.com/Cruikshanks"
  },
  "private": true,
  "scripts": {
    "start": "nodemon server.js"
  },
  "dependencies": {
    "express": "^4.15.4"
  },
  "devDependencies": {
    "nodemon": "^1.11.0"
  }
}

```

Next create the app in `server.js`

```javascript
const express = require('express')
const app = express()

app.get('/', function (req, res) {
  res.send('Hello world')
})

app.listen(3000, function () {
  console.log('Example app listening on port 3000')
})

```

Finally we add our `Dockerfile`. Take note of the change we've had to make to allow for where npm wants to install the dependencies.

```docker
# Sets the base image
FROM node:8.4.0

# Use an env var to hold what will be the main directory within the container.
# As we need to refer to it a few times it can be easier to set an env var
# and then refer to it in subsequent commands
ENV APP_HOME /app

# This will execute any commands in a new layer on top of the current image and
# commit the results. In this case we create the /app directory
RUN mkdir $APP_HOME

# Sets the working directory for any RUN, CMD, ENTRYPOINT, COPY and ADD
# instructions that follow it
WORKDIR $APP_HOME

# Copies new files or directories from <src> and adds them to the filesystem of
# the container at the path <dest>
COPY ./package.json $APP_HOME

# Here we tell docker to download our projects dependencies, then move that
# folder to the root (why explained below) in another layer that then gets
# committed
RUN npm install \
    && mv $APP_HOME/node_modules /node_modules

# ##############################################################################
# Npm unlike Bundler defaults to installing dependencies within the local
# project. That's fine when developing on the host, but as we want to install
# them as part of the image and have them present in the container when we start
# developing we face a problem.
#
# To use the container for development we mount the project folder to /app when
# calling `docker run`. If the container's app folder still held `node_modules`,
# the bind would cause it to become hidden as it doesn't exist on the host. We
# therefore need to move the `node_modules` somewhere else, in this case the
# root.
#
# In doing so we take advantage of
# https://nodejs.org/api/modules.html#modules_loading_from_node_modules_folders
# and the way Node.js traverses the directory tree to locate dependencies.
# ##############################################################################

# Informs Docker that the container should listen on the specified network port
# at runtime. EXPOSE does not make the ports of the container accessible to the
# host. To do that, you must use still use the `docker run` -p flag
EXPOSE 3000

# There can only be one CMD instruction in a Dockerfile. If you list more than
# one CMD then only the last CMD will take effect.

# Purpose of a CMD is to provide defaults for an executing container. In this
# case it sets the command and arguments to be executed when running the image
CMD ["/node_modules/.bin/nodemon", "server.js"]

```

### Build (node)

Next step; create the image.

```bash
docker build -t docker-dev-node .
```

This time if we run `docker images` we'll see something like

```bash
REPOSITORY            TAG                 IMAGE ID            CREATED             SIZE
docker-for-node-dev   latest              c7c5150e2d55        2 minutes ago       677MB
node                  8.4.0               5e553613f1d8        17 hours ago        669MB
```

### Run (node)

With the image built, now we need a container

```bash
docker run --rm -v "$(pwd)":/app -w /app -p 3000:3000 docker-for-node-dev
```

The breakdown is exactly the same as the ruby app, we've just changed the port and name we want to use.

### Develop (ruby)

And development works in the same way as the ruby app. Open the code on the host in your preferred IDE, make changes, and see them reflected in the app.

You can test it by running `curl -XGET http://localhost:3000` from the host. Edit `server.js` and change line 5 to be `res.send('Hello docker world')`. Running the same `curl` command again should result in `Hello docker world`.

## All done

Using **Docker** for development will help you and your team avoid *it works on my machine* issues, and help get new members up and running quickly. The **Dockerfile** is also great at documenting what you actually need to run the app.

The examples here are very basic though, and its likely you will also depend on other services and tools. But pretty much every other example of using **Docker** for development immediately launches into using [Compose](https://docs.docker.com/compose/), so I wanted to provide a stripped back example. I hope it helps those like me who just wanted to get to grips with what you need to conisder just getting 'your' app up and running.

Finally you will have to get used to calling **Docker** to start your apps, rather than your typical `ruby server.js` or `npm start`. And as you've seen the command can be complex so bear in mind how you can keep repeating it.

