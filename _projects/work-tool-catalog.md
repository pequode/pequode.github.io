---
title: Access To AI  
layout: default
description: My project to give users a consistent way to contribute AI compatible micro services.  
image: /assets/TC_0.png
---

# Access To AI  
My project to give users a consistent way to contribute sandbox'd, AI compatible micro services.

## Background
"Hey chatgpt, send me a birthday email"

This seems like a easy enough request to satisfy but it turns out there are some issues with even this simple request. 

ChatGPT, Google gemini, Claude Sonnet are all example of Large Language Models(LLMs). These models get trained with lots general data from the internet and are able of producing text output when given text input. This seems simple but it runs into the issue that These models lack the ability to use existing software tools and update their information about the world. 
In short chatgpt cant send you a birthday email because: 
- It doesn't know who you are. 
- It doesn't know when your birthday is 
- It doesn't know what day it is now so it cant tell if it should just send it or schedule an email for a future date. 
- It cant remember what you just said to it so it doesn't know if this is a role play or an actual request. 
- It cant access an email UI. 

What we need is the ability for these LLMs to call other software in a text based format. These software callables we can call Tools.  

Enter Agents. Agents are LLMs+Tools in a loop. Any given Agent will have access to some grouping of tools which it can use to perform software tasks. It is also able to iterate on itself and decide when to return to the user or to keep working on the problem. Here we can imagine that LLM's are the brains of the agent and Tools make up its body. 


Now when we ask the LLM: "Send me an email on my birthday"
it can:
- Use its Calendar tool to figure out what day it is. 
- Use its memory tool to figure out if you are being serious(Well call this tool Context)
- Use the LLM to write an email body. 
- Use the Email tool to attach the email body to the email and send it. 


When we think about how to contribute tools often time it makes sense to convert an REST API into a tool. These are good candidates for tool conversion because they share similar challenges to LLM's. In the early days of the internet compatibility was key so the HTTP protocol was made to accept a standard text based input and output for network communication. New standards built on HTTP are similarly cross compatible and text based. For this reason API's that are available on the internet are already able to be interpreted as text->text which makes integration with LLMs more straightforward. However we need schemas to make all of this work together. 

Schemas are basically a description of fields that a user can fill out when submitting something. In an email this might be the **TO** Field or the **FROM** field. Schemas also describe what types of inputs these take. IE you should put an email in the **TO** and **FROM** fields but not in the **BODY** field. By giving an LLM a schema for a tool and a way to submit requests via text you can essentially allow it to: 
- Decide it wants to send an email. 
- Figure out how to send an email. 
- Formate the email so it can be sent and then send it. 


## Use case 
Scientist who can write python at [Generate Biomedicines](https://generatebiomedicines.com/) often contribute generally useful code. But this code never leaves their sphere of influence. It has to be run in the enviornment they developed it on. It can usually only be used by them because no one knows it exists or how to use it. However, if we made this code accessible as Tools to an agent, even a non-scientist non-coder could use an LLM to do science workload. So what we need is some way of: 
- converting science code into tools. 
- saving tools in a common place. 
- exposing tools for agents and users.

Additionally ask the field keeps moving we are constantly needing to onboard open-source research tools to benchmark our models and improve our generated results. These come with lots of dependencies which often conflict with our Software monolith. 


# Tool Catalog 
# What 
Our users came to me and asked how they could contribute tools which could be called by agents. 
I met with several users and figured out that they: 
- wanted something simple 
- wanted to reuse existing code 
- needed the ability to host these Tools as both API's and Agentic tools. 

Tool Catalog was my solution to this problem. We needed a way of taking science code and making it accessible to agents. 

When starting work on this project I also realized some important implications of this system: 

- We already have many standard contributed units of work contributed as steps in our workflows for `generate-flow` (our in house execution platform). 
- Making tools Agentic-ly compatible would allow us to other useful things with `generate-flow`
- Our `Tasks` from generate-flow could be converted into [OpenAPI Schemas](https://swagger.io/specification/)
- We could host generic API's that could call any `Task`

Moving forward with these realizations I was able to contribute a system that allowed us to dynamically host APIS that were compatible with Agentic use. These API's also let me improve the efficiency of our existing tools and make our existing tools easy to find and use. 

## Components 
### Task Conversion
I created a simple converter that took Tasks and turned them into REST API's with the ability to return their call Schemas. I did this by first converting our pythonic tasks into FastAPI endpoints. With auto-generated pydantic models for input and output types. Doing this allowed me to create APIs that worked with our existing contributed user code. 

I also contributed code which automatically created compatible docker environments that could run their api's.

### Shared Tool Index 
The next step was taking these schemas and saving them along with other metadata about how to host the service in a shared, internally accessible, postgres database. I used sql-alchemy as my interface layer and fully normalized the tool schema. This index or (Catalog) allowed our users to upload all their Dockerize'd API's to be publicly viewable or deployable. 

### Common API interface 
I wrote common endpoint conventions for all these synthetic API's that let them either: 
- run work locally on the Hosted API or (Think a calculator)
- Put heavier work on a job queue for asynchronous processing.(Think an AI protein alignment where many different confirmations need to be tested.) 

This also made the next step easy. 

### Easy Client creation 
By creating a consistent api structure I was able to create generic API clients which could use any tool converted in this process as if it were being run locally. 

### MCP and agentic integration  
Next came Agentic integration. Even though OpenAPI schema can tell an agent how to use a tool it can be overly verbose. It also can make it hard for agents to figure out what is a tool and what is not. Enter Model Context Protocol(MCP). 
You can think of MCP as more agent friendly way of describing what tools are available and how to call them. An agent can then communicate this an api Server that is setup to accept MCP requests and process these results. I created a way for the auto generated apis I created to automatically be available via MCP. 

Because MCP is a standard this meant that all of the tools contributed by users could now be used by standard LLM clients like chatGPT's local apps or Cursor or Claude code. 

### RAG and NLP based tool search 
Although converting these tools was a challenge a different challenge had to do with visibility once hosted. Because I reused the queuing logic from a previous project [generate-flow](/projects/work-generate-flow.html). All of the views from the UI companion app  [generate-flow-UI](/projects/work-generate-flow-ui.html) still worked for users to monitor their tools. However, finding the tools would still be a challenge. 

Wouldn't it be nice if we could store all the documentation for these contributed tools alongside the tools themselves and then use an LLM to find relevant tools base on a search?

Enter Retrieval Augmented Generation or RAG. Very basically, part of all LLMs is converting text into compressed representation of text called tokens. These tokens are actually high dimensional numbers which sit close to other tokens that represent similar words. 

I.E. If I created a token for Queen. It would be close to the token for King. 

Using this basic concept we can tokenize all of the Tool Descriptions we save and then search over them based on a users request. 

For example a user asking for "please send me a message on gmail" would have a set of tokens that were close to ["request","email","send"]. This would then be close to a tool whose description was "This tool allows users to **request** to **send**/receive **emails**."

This let me provide a way of finding relevant tools for agents.

For this I used simple text embeddings with the PGVector plugin and cosign similarity search. 

### Hosting
I created a system that could automatically spin up K8s deployments and services for these API's on request and on registration. This allowed users to immediately share their tools with other users and agents. 

### Simply Secure 
Perviously to do things like this we needed users to use 5 different auth methods for the different sub services we relied on. I integrated SSO into this app and provided sensible and secure default behavior. In practice after 1 login users can submit and deploy tools. 

I also created a method for securely passing secrets between tools so that agents never see the secret and centralized access with a API gateway that allows for access control and auditing. 

Security is hard to do right. You need to balance compliance, easy of use and auditability. The system I created for Tool Catalog allows users to remain secure without having to worry about multiple logins or writing secure code. We take care of that for them. 

We also implemented a AuthN and AuthZ stack integration so that these tools can talk with our other internal DB's

### UX diagram 
After contributing all these components, all users have to do to make a tool available for an agent is: 
- create a Task object. 
- register the task object. 
- deploy the registered task. 
All of which can be done in 3 lines of code. 
![Tool Catalog UX](/assets/TC_1.svg)

# Impact 
To date this project has had the greatest impact on of my projects. It has allowed 40+ scientist to contribute tools which allow us to manipulate proteins with natural language. Before the Tool Catalog existed it was normal to have 6 + people involved in any given experiment. Now a single code illiterate scientist can ask their local chatGPT client to "improve binding on 6hji.cif to 100 nM while maintaining structural symmetry" and it has the ability to make that happen.  

Currently it has been used to automatically generate 32 different drug pipeline candidates (all in-silico 20 of these are on the backlog to be grown in the wet lab) and supports 200+ different protein design tools. 

# Skills 
- FastAPI 
- OpenAPI Schema 
- Pydantic 
- SQL 
- Langchain 
- RAG 
- Docker 
- Kubernetes 
- Security 
- Python 
- Gitlab CICD 
- MCP 