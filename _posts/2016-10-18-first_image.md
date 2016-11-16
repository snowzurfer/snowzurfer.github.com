---
layout: post
title:  "First image!"
categories: dissertation uni 
tags: dissertation graphics
---
Finally, after so long of coding my framework, I have displayed an image!
It's black, it doesn't show anything, but it proves that my framework works as
expected and that the presentation engine has been setup correctly.

To double test it, I have changed a few times the clear colour from black to
orange or blue, and it worked fine.

I made many changes to the framework because of bugs I found out and because
I see more and more things I can improve, as I go.
Moreover I have implemented a camera class, an input class and a camera 
controller class, which is in charge of controlling the camera; this way the
camera behaviour can be changed at run-time and is decoupled from the camera.
It's just like in real life: you have a camera and then you decide how to
use it, but it's not in the camera's abilities to be able to move around.

In order for the input class to register key strokes and mouse movements and
clicks, I have setup static member methods which are assigned as callbacks
for GLFW. Since the state is contained within the class though, and the
methods are static (hence you don't have the __this__ pointer), I researched
into the GLFW documentation and found the function
__glfwSetWindowUserPointer(GLFWwindow *window, void *user_data);__
which I used to pass the pointer to the input manager class instantiation
and then retrieve it in the callbacks to be able to call the member
methods of the input manager class.

I also read up about Shadow Maps and more specifically about Cascaded Shadow
Maps to refresh my knowledge on the topic. Microsoft has [a great article]
about this and since I wasn't completely confident on shadow aliasing
(e.g. perspective aliasing) I researched some more about that topic too and
found [a paper which explains the issue in detail].

Finally, I have understood fairly well the way the GeometryFX library
works and I am confident I will be able to reproduce it when I get to that
phase of the development.

[a great article]: https://msdn.microsoft.com/en-gb/library/windows/desktop/ee416307.aspx
[a paper which explains the issue in detail]: http://igm.univ-mlv.fr/~biri/Enseignement/IMAC3/Donnees/PSM.pdf
