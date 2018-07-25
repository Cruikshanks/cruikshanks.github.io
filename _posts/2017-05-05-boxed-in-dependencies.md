---
layout: post
title: "Boxed in dependencies"
description: "Using Vagrant to manage dependencies in a dev environment"
date: 2017-05-05
permalink: blog/boxed-in-dependencies/
---

*First a clarification. I refer to my [Vagrant](https://www.vagrantup.com/) images as boxes, but you should be aware [box](https://www.vagrantup.com/docs/boxes.html) has a specific meaning in Vagrant land. I'll highlight when I'm using the Vagrant version.*

Sometime back I learnt of Vagrant and having had a play, immediately got stuck in and now use it daily. Initially I had grand plans to base all my development on Vagrant boxes, but in practise this never materialised.

Version and package managers seem to do all the heavy lifting, so I don't feel the need to build a box for every project I've got going on. I've also found a reticence from other dev's to using them, and like any part of an evolving project Vagrant files need to be fed and watered so there's an overhead. Plus there's the elephant in the room, otherwise known as [Docker](https://www.docker.com/).

## Custom boxes

So after listing a bunch of reasons for not using them, how *do* I use them daily? Well I use them for my general dependencies, databases being the primary example. Rather than install database ***x*** directly on my host machine, I instead stick it in a box.

You could create a box specific to each project, but I tend to share mine among them. So for example I'll stick [PostgreSQL](https://www.postgresql.org/) in an box, and use it for all my projects that require it.

## Big fish, little fish, [Vagrant box!](https://www.youtube.com/watch?v=SD1ENnVnMXM)

So what steps are involved?

### Installation

Obviously start with installing Vagrant. Next you'll need to decide on your '[provider](https://www.vagrantup.com/docs/providers/)' and install it. This is basically the app used to create and run the [VM's](https://en.wikipedia.org/wiki/Virtual_machine) you are going to install your dependencies on.

Vagrant uses [VirtualBox](https://www.virtualbox.org/) as its default, and its what I use as its free and simple.

### Initialise

With those two things installed, next create a new folder (named after the thing you intend to install), drop into it and run `vagrant init` from your command line.

This adds to the folder the files you'll need to get started with Vagrant, the most important one being `Vagrantfile`.

As I want to make my boxes reusable, I also tend to run `git init` and add things like a `.gitignore` file, a `README` and a `LICENSE`, before pushing it to GitHub.

### Vagrantfile

In here you'll find the bare bones for configuring and setting up your box, which by default will be a VirtualBox. The first decision you need to make is what [box](https://www.vagrantup.com/docs/boxes.html) (*the Vagrant kind!*) to base your box on. Basically what OS to use. There is a [publicly accessible list](https://vagrantcloud.com/boxes/search) to pick from, but if it helps I tend to use [ubuntu/trusty64](https://app.vagrantup.com/ubuntu/boxes/trusty64), and based on the download numbers so does everybody else.

I'm not going to get into the details, I encourage you to check out the [documentation](https://www.vagrantup.com/docs/index.html). But in essence the `Vagrantfile` is divided into two sections; configuration, and provisioning i.e. how to install your dependency.

## Getting the install right

When getting started with a new box, I tend to begin with a standard bit of installation

```ruby
# General provisioning of the box. Ensure time is set correctly and do an
# initial apt-get update, plus install packages commonly needed by all boxes
config.vm.provision "shell", name: "common", inline: <<-SHELL
  timedatectl set-timezone Europe/London
  apt-get update > /dev/null
  apt-get install -y git build-essential tcl8.5 wget curl make libqt4-dev
SHELL
```

I'll then run `vagrant up`, which when complete will give me an empty box. I then access it using `vagrant ssh` and once on, begin following whatever install process I've managed to Google.

I'll record all the commands I use and update the provisioning details when done. I'll then exit the box and call

- `vagrant halt`
- `vagrant destroy`
- `vagrant up`

This allows me to test that when I create this box in future I know my provisioning commands will work

## Benefits?

I see plenty. I now have a reproducible way to get something like `PostgreSQL` on the machine I'm using.

Not only do I have a project that will automate the process, its also documents what's involved.

I only need to run these services when using them. It's easy to halt a box when its not being used.

I reduce bloat on my dev machine, and the risk of dependencies conflicting.

## Examples

I have some existing boxes you can check out, copy from, or use.

- [MongoDb](https://github.com/Cruikshanks/mongo-box)
- [MySQL](https://github.com/Cruikshanks/mysql-box)
- [PostgreSQL](https://github.com/Cruikshanks/postgresql94)
- [Redis](https://github.com/Cruikshanks/redis-box)
