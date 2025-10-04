---
title: Visibility Maters 
layout: default
description: My work on supporting the frontend application for our workflows. 
image: /assets/gflowui_0.png
---
# Visibility Maters 
My work on our scientific compute backend. 

## Background 
Controlling distributed systems in realtime is hard. Many people at [Generate Biomedicines](https://generatebiomedicines.com/) prefer using generate-flow to other workflow managers. It allows them to, say, use 10 different machines to run predictions on rather than their one macbook. However, now there are 10 different places that can have errors. What if they use 100 different machines by accident. How can they predict how long a new flow will take if they cant see the time the old flow took? Generate-Flow-UI is a package that I prototyped and help support ongoing development of. It is the UI for the platform [generate-flow](/projects/work-generate-flow.html) 

# Generate-Flow UI
![an image of generate flow ui](/assets/gflowui_1.png "The GFlow UI I wrote by myself.")
## What 
I designed and released a internal web application which took advantage of our clearly defined interfaces for database backends. This allowed me to
create Workflow(DAG) visualizations for Flows with many different configurations. 

I created a clear separation of responsibility between frontend code and backend api's. My focus is primarily supporting Applied Machine Learning and distributed compute but I wrote the first version of this UI in vanilla JS. It served as a functional touch point for users until we hired a UX engineer. 

I provided full CICD for this project. This allowed developers to see their new features hosted in a mater of minutes. This UI is feature rich. It is build with React and a serenes of pluggable backend apis. Including components for:
- viewing projects, 
- individual workflows,
- logs, 
- controlling execution,
- control check pointing, 
- displaying costs 
- visualizing a protein for protein specific flows. 
- getting summary metrics for workflows. 
- even accessing the terminal in the browser.

### Skills 
During this project I used the following skill areas: 
- Python 
- Docker 
- Kubernetes 
- JS 
- HTML 
- CSS 
- React 
- FastAPI
- HELM
- OAuth
