---
layout: post
title:  "GeometryFX"
categories: dissertation uni 
tags: dissertation graphics
---
Following last week's work on my framework and further research, I have now
started exploring the main source of inspiration for Wolfgang's version of
triangle culling and filtering: [GeometryFX] by AMD.

AMD have recently started the GPUOpen initiative, where they release their
code open-source using github. One of these tools is GeometryFX, a library
written using C++ and D3D11 for compute-based triangle culling and filtering
 (actually the culling is run on the CPU).
Having read the source code extensively, I feel very confident about knowing
how it is implemented and how they use indirect draw calls. Hence I am
getting closer and closer to implementing my own version using Vulkan.

I will need to read up how to use compute with Vulkan but due to its
compute-graphics hybrid nature it should be fairly straightforward.

The idea is to have different pipeline objects that I will bind as
necessary, with some of them being opaque to a potential user of my
framework and some others, such as the pipe objects per-materials,
exposed.

I have also found out that there is another excellent tool as part of the
GPUOpen project, called [CodeXL]. It's a performance analysing tool for
AMD-based machines and since I am planning on acquiring an AMD-based
computer, it will be great to be able to use it. Apparently it was part
of a suit of tools that AMD would give to developers when their code was
still closed-source, which means that the tool is of professional quality.
Moreover, it runs on Linux too, and with Vulkan!

I will continue with the investigation into the GeometryFX library and
I will keep on searching for references and bibliography to support my
thesis.

[GeometryFX]: http://gpuopen.com/gaming-product/geometryfx/
