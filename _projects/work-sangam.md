---
title: Cutting Cost by normalizing data.  
layout: default
description: Smart archiving so that data is still useful.    
image: /assets/sangam_0.png
---
# Cutting Cost by normalizing data.

Smart archiving so that data is still useful.

## Background 

A Sangam is a place where the Ganges river joins with the waters of the Jumna. The water from the wide, turbulent, and slow Ganges must be integrated with the uniform fast flow of the Jumna. 

Similarly the Sql ANalytics Gathering Model that I built is a E.T.L. which integrates non-normalized data from many different places with many different shapes. Yes. I know its a stretch :) 

This project came about because we notices we were spending a lot on data storage. This came about because We initially chose a NO SQL db offering to store our job queue data. 
Initially this was a great choice. It let us move fast and develop a schema that worked for our usecase. It also was very efficent for fast writes which we need to support for flows with 10K + concurrent submissions. 

However
