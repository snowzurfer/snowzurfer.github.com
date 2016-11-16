---
layout: post
title:  "First post"
categories: dissertation uni 
tags: intro dissertation graphics
---
I've setup a new blogging system, `Jekyll`, which allows me to blog very easily;
all I have to do is writing the blog posts as a plain text file and the engine
will take care of creating web pages. Much better than Wordpress, and I can use
Vim!

## Now, the dissertation
I have settled on the topic to explore for my dissertation. It will be about how
this new rendering technique called Culled and Filtered Visibility Buffer can
improve the performance of real-time 3D graphics applications when scaled to
4K and high resolutions.

This will more or less be the title of the thesis, which I will redefine as the
time goes; I believe there is much room for improvement on it.

Regarding the actual topic, I came up with this idea thanks to my mentor at AMD
Steven Tovey, who suggested it as a topic I could explore in exchange of being
sponsored by AMD. I was very happy to discover this relatively new technique 
(more why _relatively_ later), especially because it was something not many 
people have worked on and so it feels refreshing.
The main source for the implementation is the [GDC talk] by Wolfgang Engel on
how they developed this kind of renderer at his company 
Confetti.

Having read the slides and watched the video, I backtraced his claims and read
some of the supporting papers to their presentation. It turns out that Burns, 
Warren [first proposed this technique in 2013], but without the heavy compute-based
triangle filtering (and CPU based culling), hence only proposing the use of
the Visibility buffer.

What Wolfgang and his team have done is to improve over it and the results they
got were very exciting and interesting, because this new method could allow
scaling of performance at very high resolution without paying for the high
memory bandwidth necessary if using a deferred renderer.

They used D3D12 but I am going to use Vulkan, mainly for personal preference
and because I would like to work on Linux.

I have already started developing a framework with Vulkan and by now I feel
that I have a very solid understanding of how Vulkan works and I am confident
I will be able to create a demo fairly quickly.
My repo is hosted on [github].

Until next time!

[GDC talk]: http://www.confettispecialfx.com/gdce-2016-the-filtered-and-culled-visibility-buffer-2/
[first proposed this technique in 2013]: http://www.confettispecialfx.com/gdce-2016-the-filtered-and-culled-visibility-buffer-2/
[github]: www.github.com/snowzurfer/vulkan-sagres

