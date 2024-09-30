---
title: "Development of a Digital Twin-based In-situ Wear Testing System"
date: 2022-09-01
author: ["Xiaoke Qin", "Chenhao Wu", "yuxuan Chen"]
description: "A digital twin-based in-situ wear testing system for the development of advanced materials"
longImage: "longpic.png"
showImage: "showpic.png"
---

## Abstract

Measurement methods are typically one of the important factors affecting the results of **wear tests**, divided into in-situ and ex-situ measurements. In-situ measurements do not require changing the contact conditions of the test samples, which can reduce the risk of experimental data being affected, and thus are gradually being applied in more and more research.

**Digital twin technology** achieves the integration of virtual and physical entities through consistency and synchronization. 

This project has developed an in-situ wear testing system based on digital twins. The system can collect in-situ visual data in real-time, that is, the morphology of the wear surface, thereby showing the evolution process of wear through changes in visual images. At the same time, it is equipped with a digital control platform that can dynamically display experimental results and monitor and control the experimental process.


## Physical Model

The main body of the loading module is composed of the profile support body, the balance arm and the sample fixture, wherein the profile support body and the cantilever beam are connected through the shaft-bearing mode; The balance arm and the upper sample fixture are threaded through a force sensor. In addition, the base plate of the loading module is designed to facilitate its fixation to the experimental bench

<center><img src=pic1.png width=60%></center>

## Circuit design

The whole circuit is divided into two modules: control module and controlled module, as shown in Figure 5.18. In the control module, STM32 MCU low computing power platform is used as the control unit of the entity system, which is used for motor speed and direction control, OLED display, ultrasonic ranging, photoelectric gate detection and sensor data receiving and transmitting. The high computing power platform of the upper computer is used as the data processing platform to analyze the data of the force sensor, reconstruct the 3D shape and run the digital twin system. Various communication protocols such as serial port and network are used to connect the platforms.

<center><img src=pic2.png width=60%></center>

## Digital Twin Model

The digital twin platform adopts Unity and PyQt co-development, programming languages are C# and Python, runs on the upper computer of the computer with high computing power, and communicates with the MCU platform with low computing power through serial port. The digital twin platform is divided into two parts: the control interface and the model interface. The control interface is used to control the single chip computer, receive the data and analyze the processing; The model interface is used to display the running state of the real machine in real time, and network communication is adopted between the control interface and the model interface. 

<center><img src=pic3.png width=60%></center>

## Video

{{< bilibili 703769413>}}
