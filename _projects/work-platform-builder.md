---
title: But It Works On My Computer? 
layout: default
description: How I designed a system to support a unified compute environment.  
image: /assets/my-cool-project.png
---
# But It Works On My Computer? 
My work on our scientific compute backend. 

## Background 
Finding a way to keep working fast while being able to share the results of 
your work is a constant problem in shared codebases. 
At [Generate Biomedicines](https://generatebiomedicines.com/) 
I recognized that my team was being slowed down by our environment 
build process 
and that others could benefit from our CI infrastructure. 
I proposed this service as a solution to a nasty dependency 
resolution problem we suffered. 

At its core we maintain 2 types of code repositories on scientific 
compute orchestration. The first is a **research** repo. 
These are bloated with extraneous packages, have numerous feature branches a
nd are not published or versioned. We also have **production** repos which 
are managed by the engineering teams. These differing standards make it hard 
to know if any given docker image will work with all the functions in a repo 
because of the numerous overlapping and unmanaged package dependencies. 

Because we don't own the **research** repos so we cant enforce our coding 
standards, and todo so might slow down the pace of research code. 


# Platform Builder 

## What 
Platform builder is a suite of CI templates combined with a pipeline builder. 
This tool allows users to define environments with package dependencies and branches. 
All the user needs to do is provide a single fle and then it will build a 
production ready CI image. This image will be checked by running all of the 
various lib's unit tests on it and only if they all pass, 
will the image be pushed to an image registry. 

This tool is still used to manage our production images. 
It also takes environment management out of the hands of our users. 
This allows them to focus on the real problems and makes it easier 
to keep things compatible. 

### Skills 
During this project I used the following skill areas: 
- Python 
- Docker 
- Kubernetes 
- Gitlab CI 
- Security 