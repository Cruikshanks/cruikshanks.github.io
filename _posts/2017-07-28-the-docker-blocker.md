---
layout: post
title: "The Docker blocker"
description: "Squaring Docker's no need for an O/S with FROM ubuntu:latest"
date: 2017-07-28
permalink: blog/the-docker-blocker/
---

When you get started with [Docker](https://docs.docker.com/get-started/), you'll come across [bumpff](http://www.phrases.org.uk/bulletin_board/62/messages/94.html) telling you that **Docker** containers are ***not*** virtual machines, and this will be emphasised by pictures showing containers not requiring a guest O/S.

|-----------------+---+---------------|
| Container       |   |Virtual machine|
|:---------------:|:-:|:-------------:|
| <img src="/assets/images/docker_container_layers.png" alt="Docker container architecture" width="300"> |   | <img src="/assets/images/docker_virtual_machine_layers.png" alt="Virtual machine architecture" width="300"> |

*Images taken from <https://www.docker.com/what-container>*

Nice.

For those of us who don't need to go deep on a subject before we have a play, you'll accept this and move on. Next step is typically putting together your first [Docker file](https://docs.docker.com/engine/reference/builder/). And the first statement in a `Dockerfile` is `FROM foo`.

The problem is `foo`, will actually be something like [Alpine](https://hub.docker.com/_/alpine/), [Debian](https://hub.docker.com/_/debian/), or [Ubuntu](https://hub.docker.com/_/ubuntu/). So your image will be based on an O/S image.

Huh?

## The blocker

I'm guessing others have either done the research first, or just accept this and move on. I can't explain why, but for some reason this blocked me. I could see the benefits of **Docker**, but I couldn't engage with it because of this [conundrum](https://www.merriam-webster.com/dictionary/conundrum).

And having spoken to others, I'm not the only one. It seems to be an incongruity that niggles enough at the back of your mind that you can't focus on progressing.

So why when the docs talk about containers not needing an O/S, do [so many examples include one](https://docs.docker.com/engine/reference/builder/#dockerfile-examples).

So this post is the result of me learning *just enough* to answer this question for myself. I've put it out there in the hope it can help others.

## Batman vs Superman

Or in our case containers versus virtual machines. The first thing I found helpful was thinking of **Docker** as *"an abstraction at the application layer"*, and virtual machines as *"an abstraction at the hardware layer"* (thanks [Docker docs!](https://www.docker.com/what-container))

A virtual machine will get dedicated resources and run isolated from everything else. The virtual manager and hypervisor will take this isolation down to the metal, and it's why for all intents and purposes a virtual machine is separate and distinct from the host.

Containers aim to reuse the resources of the host, whilst still keeping things isolated at the app layer. The main way it does this is by sharing the underlying kernel.

## A kernel of an idea

Though we have the [Linux kernel](https://en.wikipedia.org/wiki/Linux_kernel) and the [Windows kernel](https://en.wikipedia.org/wiki/Architecture_of_Windows_NT), [kernels](https://en.wikipedia.org/wiki/Kernel_(operating_system)) are more a concept than a specific thing.

<img src="/assets/images/kernel_layout.svg" alt="Docker container architecture" width="300"><br>
*By Bobbo - Own work, CC BY-SA 3.0, <https://commons.wikimedia.org/w/index.php?curid=4392180>*

The kernel is the bit of the operating system that connects the application software to the underlying hardware of the machine. Typically it gets loaded into protected memory when the system starts up, and this is known as [kernel space](https://en.wikipedia.org/wiki/User_space). Everything it does happens here, which is taking system calls from processes for access to resources. Everything else like working with documents, applications, the file and window system happens in [user space](https://en.wikipedia.org/wiki/User_space).

The linux family of operating systems use the same linux kernel; what makes them different what they do within the user space.

## Back to the bottom

So we've gone a little deeper on differences between a container and a virtual machine, and we understand that an operating system is made up of 2 parts; the kernel and everything else!

So if **Docker** containers get a kernel, but no O/S what do we do for a file structure and system? How do we pull down the binaries our projects depend on when building the image? How do we get system dependencies we depend on?

That's what `FROM ubuntu` is doing. These are container specific images built to provide just the **user space** stuff you need when deploying and running your project. It's not a guest O/S in the same way as a virtual machine, its just filling in for the bits you probably need but which the kernel doesn't cover.

## Doing it from scratch

To prove a point, you really don't need that O/S layer in your image. All **Docker** images derive from the [base image called scratch](https://docs.docker.com/engine/userguide/eng-image/baseimages/#creating-a-simple-parent-image-using-scratch).

You can build an image `FROM scratch` using a pre-compiled **C** binary

```dockerfile
FROM scratch
ADD my_binary /
CMD ["/my_binary"]
```

You could theoretically do the same for a Ruby project, if you were willing to manually create the file system, and load all the binaries you depend on, and your gems depend on etc.

So yeah, never gonna happen!

Hence most projects will have a `FROM` statement that either directly uses an O/S image, or somewhere down the chain will have.

## Move along please. Nothing to see here

**Docker** containers don't have a guest O/S, if we define an O/S as everything plus the kernel. They also don't have everything we need to work productively with **Docker** when creating an image, hence we use parent images to bring in the bits that make our lives easier.

And they're not the same as virtual machines. So can we move along please?
