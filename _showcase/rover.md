---
layout: showcase-item
title: "Browser-Controlled RC Car"
priority: 2
author:
    name: Sean Raven
    github: dragon0
description: "A remote-controlled car that can be controlled from a web browser. Intended to be part of a curriculum for middle- to high-school students"
link: https://github.com/CSSAMEUGV001
---

## Background

This project was organized by Professor Larry Perez at Saddleback College.
The ultimate goal is to design a curriculum for middle-school and high-school students to work on a cross-disciplinary team and gain experience with concepts in mechanical engineering, electrical engineering, and software development.
They would accomplish this by assembling a software-controlled RC car.
The students would learn about mechanical engineering and automotive engineering while assembling the car itself.
Then, they would learn electrical engineering while adding the motors and other electronics.
Finally, they would learn software development by writing code to control the car.
The software would enable the car to be controlled remotely via a web browser, using a video feed from an onboard camera.
Once the car is completed, it would be taken to an obstacle course several miles away from campus, while the students completed exercises by controlling the car from their classroom.

To begin the initial phase of this project, Professor Perez recruited myself and several other Saddleback students to design and build a proof-of-concept prototype.


## Purpose of this project

The purpose of this project is to reverse-engineer the RC car provided to us by Professor Perez, equip it with a programmable brain, and implement the onboard and server software to enable the car to be controlled remotely.

## System Design

I was responsible for designing the software, while another student, Farid, was responsible for designing the hardware.
The interaction between the car, the server, and the browser is performed by passing messages encoded as JSON objects over WebSockets.
The car's onboard software is responsible for connecting to the server, sending video data from the camera to the server, and accepting control messages to be sent to the hardware.
The client-side code running in a web browser receives and displays the video and provides a UI for controlling the car, sending control messages as the user interacts with it.
The server is primarily responsible for serving the client-side code to the browser and relaying messages between the browser and the car.

## Implementation

Professor Perez provided us with an RC car designed for offroad use, and tasked Farid and some of the other students with determining how its electronics functioned and choosing an appropriate microcontroller to act as the car's new brain.
Farid and his team determined that the car uses pulse-width modulation (PWM) signals to interact with its included controller.
He decided to use both an Arduino and a Raspberry Pi to control the car, which would communicate with each other using I2C.
The Arduino was necessary because the Raspberry Pi does not have included hardware or software to send PWM signals, which would have required us to write very precise timing code.
Farid wrote all of the code on the Arduino as well as the I2C communication code on the Raspberry Pi.

I designed the server and its interaction with the car.
I decided to use the Flask web framework, written in Python, on the server.
The decision to use Python was due to it being what we were using on the Raspberry Pi, and the rest of the team was either familiar with it or interested in learning it.
I chose the Flask framework because I had previously done personal projects with it and knew it to be a very easy-to-use framework.
I wrote WebSocket scaffolding code both on the server and on the Raspberry Pi and allowed my less experienced teammates to fill out the rest of the interaction code.

I also wrote the client-side code, which consisted of very simple HTML and JavaScript.
I used an existing library that provided a mouse or touch controlled virtual joystick, and wrote code to send control messages based on the joystick's position as well as receive and display the video feed.

## Result

We successfully completed a fully functional car that streams video to and receives control inputs from a remote server, a browser client that can display the video feed and accept user input to control the car, and a server to relay the video and control messages between the car and the client.
We have demonstrated our vehicle driving around a small section of the Saddleback College campus.

## Next Steps

There are limitations in our implementation which need to be addressed in the next version.
The car, server, and browser must all be connected to the same local network.
The video stream also has unacceptably poor performance, with several seconds of delay.
Although the other students are no longer involved in this project, Professor Perez and I intend to continue development.

