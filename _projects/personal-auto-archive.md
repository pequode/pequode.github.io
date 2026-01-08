---
title: Piles of Paper go Poof!  
layout: default
description: A personal project for easy digitization of paper records.  
image: /assets/auto_archiver/aa_0.png
---
# Piles of Paper go Poof!  
A personal project for easy digitization of paper records. 

## Background 
I decided to work on this project because of an ongoing situation at my family member's house. They are currently plagued by a room full of old tax documents and business records, which might have some interest but are un-categorized and mostly bland. They are currently going through these documents by hand and reviewing them before discarding them. The process is also limited by their specific knowledge of a given document's importance, so it is very hard for others to assist.


Although their process is likely not going to change because of this project, the impetus for this project came from that idea. 


Today, we have access to really powerful OCR and LLMs, and text embedding models. It seems like saving paper records should be a thing of the past. What this project aims to do is provide a way of uploading scanned documents or images to a self-hosted database and allowing a user to then semantically search for those documents. Of course, there are existing efforts from Apple and Google to do this already with their various clouds, but they are not as searchable for document content as might be preferred. It also seemed like a cool project to pick up! 


The alternative right now is to manually digitize the document, which is labor-intensive because pictures need to be put on a sorted cloud and given additional metadata. It also makes something approachable for someone with less of a tech-heavy background.


The desired product of the problem is simple; basically, all a user needs to do is upload an image, and then they should be able to search for it and related documents later. 

## Architecture

To make this work, my approach was to first take in an image format given in bytes. 


I then convert that image into a JPG. This is done because JPGs compress more efficiently than PNG's and for a scanned document DB, the alpha channel is not important.  Then we load this jpg image into a natural language image classifier model. I used `dandelin/vilt-b32-finetuned-vqa`
This model then provides a natural language-based description for an image's content. However, it is not a good OCR model, so it is not sufficient for searching later. We use this output to discriminate with a structured output LLM call. I used `gpt-5-nano` for the initial implementation, but I hope to port this code over to Ollama for the final prototype. 
If the LLM decided that this image is worth performing OCR on. I use Tesseract to extract the text from the image. I then create a context from the initial description and all extracted text. 
Next, the same LLM creates a summary guess for the image based on the context. 
Finally, it uses the summary to give the file a new name that matches its description. 
I also support PDFs, and the process is mostly the same except that there is an added step for extracting the existing images and text from the PDF and creating a large context for the LLM. 
![glutton](/assets/auto_archiver/glutton.png)



This summary and the context all get embedded in a vector database, and the image itself gets stored, along with some metadata and generated metadata, in a traditional DB. ( This can also be done with a traditional DB with a vector plugin.)
![ingestion](/assets/auto_archiver/ingestion_overview.png)


## Results 
![search](/assets/auto_archiver/search_overview.png)

When a user wants to retrieve the data, they are able to go to the data engine and search with either semantic search or structured search. In the case of semantic search, we use cosine similarity with the vector database, and traditional techniques for structured search apply for structured search. This allows us to create a really interesting hybrid search paradigm and enables us to build bigger systems on top of this base
![query](/assets/auto_archiver/querypipeline.png)


Here are the example images I used: 
![tax_in](/assets/auto_archiver/tax_doc.png)
![tax_out_text](/assets/auto_archiver/upload_tax_doc.png)

![maze_in](/assets/auto_archiver/maze(1).png)
![maze_out_text](/assets/auto_archiver/return_description.png)


I also added 5 more images to confuse the DB. They were a mix of scanned documents and regular images. 
I then was able to search and get back the following result:
![return_proof](/assets/auto_archiver/return_proof.png)

This project was a blast to work on and really cemented my familiarity with incorporating RAG into my workflows. 

### Skills 
During this project I used the following skill areas: 
- Python 
- Docker 
- RAG 
- Langchain
- SQLlite 
- FastAPI
