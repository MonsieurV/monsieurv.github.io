---
title: "Home item recognition using neural networks"
categories: [Neural networks]
tags: [Neural networks, Machine learning]
draft: true
---

A working implementation of my real-time activity recognition system:

How does it work?
The set up involves two iPhones: one is worn on my forearm (iPhone 4s) to capture motion, and the other (iPhone 6 - right corner of the video) displays the predicted activity.

The iPhone 4s is the brains of the operation. It captures my motion through the accelerometer and gyroscope sensors, transforms this raw data into meaningful input vectors, feeds them into a multi-layer neural network which then outputs a prediction. This value is then sent and displayed on the iPhone 6 via bluetooth. This whole process repeats every second to produce a local and real-time* activity recognition system.

The recognition is not perfectly real-time. There is an inherent lag of ~3 to 4 seconds. This is because the system uses a 4-second window (with a sliding length of 1 second) to compute the input vectors for the neural network. Thus the system predicts the correct label approximately 3 to 4 seconds after an activity has been started.

http://www.dilpreetsingh.me/activity-recognition
