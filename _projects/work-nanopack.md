---
title: NanoPack 
layout: default
description: A senior design project automating microchip packaging with computer vision and CNC robotics.
image: /assets/nanopack/full_1.jpg
personal: true
---
# NanoPack

## Background

A company called NanoView Biosciences (acquired by Unchained Labs, later acquired by the Carlyle Group) came to my university with a manufacturing problem. They manufactured a delicate silicon chip used for biological assays (exosome characterization). These chips had to be thoroughly quality tested by hand and then packaged into small, irregular containers which had already been purchased by their manufacturing team. They needed an automated process that would move the chips from a testing rig to these irregular containers. Because of human process fuzziness on both sides of the manufacturing pipeline, the test rig would contain multiple different orders' worth of chips and would not guarantee chip placement beforehand. Additionally, chips were marked with a human-readable id number, not a barcode.

My team built the automation solution to package these delicate silicon wafers in a messy, non-automated production pipeline.

![The NanoPack machine](/assets/nanopack/full_1.jpg "The NanoPack machine")
![NanoPack assembly](/assets/nanopack/full_2.jpg "NanoPack assembly")

## What

My team decided to approach this problem by modifying a CNC pick-and-place machine for embedded components. The NanoView team would use a CSV output by the QA process with a .NET app that we created to drive the automated packaging of their surviving chips. This process involved many components but I will highlight the ones I worked on:

- Image preprocessing and training pipelines for robots
- Fine-tuning YOLOv5 on our data for image segmentation and robot localization
- Pathing algorithm for chip placement
- Integrating a physical controller, Python-based machine learning libraries, and a .NET frontend

![NanoPack system flowchart](/assets/nanopack/flowchart.svg "System flowchart")

## Preprocessing and Training

![ML pipeline](/assets/nanopack/ml_1.png "ML pipeline")

For image preprocessing it's important to understand that one of the issues we faced was that our image was very hard to normalize to greyscale. We were taking images of reflective silicon wafers, sitting on a reflective metal tray, sitting on a reflective aluminum plate. We also needed to be very close to the bed because of where our camera was mounted, which made localization of the images tricky. To solve this I first put sterile green fabric over the aluminum plate. I then took all of the image fragments and stitched them together with hard-coded offsets tuned on the machine's movements. Then I used the tray's symmetry to create a simple segmentation algorithm which rotated the image to be square around the tray then did crops on each well. I took the greyscale chip images and did a quick round of standard computer vision filtering techniques to create a greyscale image with the digits for the chip id visible. Once that was done, I cropped the images into a list of 3-digit sub-images and padded those to be square for the CNN.

![Digit extraction](/assets/nanopack/ml_2.png "Digit extraction")
![Chip imaging](/assets/nanopack/ml_3.jpeg "Chip imaging")

For our second camera, which could see the whole assembly, I did an image warping so that the coordinate system of the image — which was slightly fish-eye and mounted precariously — could be converted into a space that we could easily translate for future movements. I used the edges of the green fabric as reference for relative positioning in the real world.

For both trainings, we were dealing with sparse training data.

For the digit recognition problem, I used the existing chip numbers we had to create a library of synthetic images. These used the digits from the 6 test chips to create 1000 fake chip images with all the possible numbers. Then I added another 1000 where I purposely added noise on the image to slightly obscure the digits.

![ML training results](/assets/nanopack/ml_4.png "Training results")

For the image segmentation problem, I took 200 pictures and labeled them with a mix of all the items we wanted to classify. I then rotated, flipped, and noised these images with the label translated based on the augmentation to generate a retraining set of 1000.

## Fine-Tuning YOLOv5

![YOLO detection](/assets/nanopack/ml_5.jpg "YOLO detection")

To solve localization, I used YOLOv5 retrained with our custom objects to get a bounding box for our objects. We then found the centroid of that bounding box and moved our arm to it after converting image space to real space. This process was relatively turn-key, but the localization involved a lot of tuning to be robust to camera changes and belt slippage.

![Detection output](/assets/nanopack/ml_6.jpg "Detection output")

## Algorithms

![Path selection](/assets/nanopack/path_selection.png "Path selection")

I also worked on the chip placement logic. We would identify how many chips belonged in a group of final packaging containers (clamshells) based on which customers the CSV described. I wrote an algorithm to do a naive optimization over which clamshells were closest together and how many chips needed to be packaged. Then my code would move the arm to the general place that the wide-angle camera and YOLOv5 identified where it was, and use the close camera to localize it on the grid of chips. It would then pick up the chip and move it to the clamshell, where we would do a similar process to localize the clamshell. After that, a teammate wrote a process to detach the chip from the pick-and-place head and put it in the container.

## Integration

![Control flow](/assets/nanopack/control_1.png "Control flow")
![NanoPack UI](/assets/nanopack/nanopack_ui_0.png "NanoPack UI")

I was also in charge of integrating all the pieces. I took trained models and wrote inference classes to help call them. I created our Python package and an async backend Python process that held our state. I created a Win32 pipe-based command protocol to facilitate multiprocess communication and wrote helpers around the serial communication bus we used to talk to the CNC controller.

![Build process](/assets/nanopack/build_1.jpg "Build process")
![Final machine](/assets/nanopack/full_3.jpg "Final machine")

## Links
- [GitHub Repository](https://github.com/NanoPackBU/NanoPack)
- [Project Wiki](https://github.com/NanoPackBU/NanoPack/wiki)

### Skills
During this project I used the following skill areas:
- Python
- PyTorch
- OpenCV
- YOLOv5
- Serial Communication
- Win32 IPC
- CNC/Robotics
