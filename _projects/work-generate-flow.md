---
title: Science At Scale. 
layout: default
description: My work on our scientific compute backend. 
image: /assets/gflow_0.png
---
# Science At Scale. 
My work on our scientific compute backend. 

## Background 
In order to solve hard problems like Protein design you need to be able to think outside the box. 
In often when writing code there a fastest algorithm or most conventional way to make build an app. However, in research, this is not the case. 
The field evolves.
The models are fuzzy. 
The best results come from many experts in different fields working together. 

To do succeed in research while in industry you need to:
- repeatability
- scale
- flexibility 
- standards 

At [Generate Biomedicines](https://generatebiomedicines.com/) my team made the unified python platform which powered our research efforts.

# G: Flow 
![image of glow_ui](/assets/gflow_1.png "Turning research code into production code.")
## What 
generate-flow is a platform for defining work. You define "Tasks" by wrapping python functions and these can then be scheduled on a job queue to be run asynchronously. We also define a set of common objects that can hold the state of a protein experiment. These our our **standards**. 

Users who are experts in ML can write Tasks which create proteins. Users who are experts in Comp Bio can write tasks which test how believable the experiment is. 

These tasks can then be run individually or compiled into a pythonic workflow. 
Tasks and Flows are another one of our **standards**. 

These pythonic Tasks and flows can be translated into any other executor. e.g. AWS Batch, Dask, Volcano on K8s, some custom mix of executors. This is where we get our **flexibility**.

For our probabilistic tasks we needed to be able to scale to 10k concurrent jobs. We optimized our backend to support efficient and scalade job orchestration. This gave us **scale**.

Each Task or flow that is submitted gets built into a docker image. This allows us to replay a successful flow many months in the future. This brings us **repeatability**. 

### My Impact. 
I was a core developer and maintainer of this project. I helped write interfaces. Improved testing practices. Designed components. Helped onboard users. This project allowed us to provide users with the ability to:

- define work locally. 
- work out the kinks. 
- submit to remote scale without changing their workflows. 

This project was so useful that groups outside of the team we support started adopting it and now it is a standard for how to write code within the research group.

### Skills 
During this project I used the following skill areas: 
- Python 
- Docker 
- Kubernetes 
- AWS 
- Security 
- Graphql 
- Mongodb 
- Redis
- SQL (SQLlite)
- pytest 
- extensive use of distributed systems programming and trade-offs 
