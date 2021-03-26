---
layout: showcase-item
title: "Academic Merge Sort"
priority: 1
author:
    name: Sean Raven
    github: dragon0
description: "A demonstration of Merge Sort to show to Computer Science students"
link: https://github.com/dragon0/mergesort
---

### Background

While working as a tutor for Saddleback College, one of the assignments that our students would be asked to complete is to implement the merge sort algorithm on a linked list.

One of the limitations I have noticed with Saddleback's curriculum is that the concept of dividing parts of code into functions is not covered in great depth.
As a result, students often struggle with understanding how to use functions and identify which parts of their code should be moved into separate functions.
For example, when trying to find a node in a linked list, students will often copy and paste the code that traverses the list rather than write a dedicated search function.
When their traversal code inevitably contained a bug, the students then had to fix that bug in every copy of the traversal code.

I have also noticed that students have a mistaken assumption that object-oriented programming cannot be done in C, only C++.

### Purpose of this project

I approached this project with the intention of addressing the above problems.
I would show students this project after they have attempted their own implementation, and try to explain the best practices exemplified here.
I consider this project to be an example of good program structure, with an emphasis on clearly-defined single-purpose functions.

I also wanted this to be a demonstration of how to do object-oriented programming using only features available in C.
The core data model is a group of three structs whose members are not accessed outside of functions dedicated to doing so.
Almost all functions accept a pointer to one of these structs, with the exception of the constructor functions, which are responsible for allocating dynamic memory and initializing the members of those structs.

One concern that a reader may have is the lack of comments in the code.
This is by deliberate choice.
I adhere to the principal that code should be as self-documenting as possible, and that comments should be used only to relate information that the code cannot.
To emphasize this, I chose to even omit the API documentation comments that I would normally include.

