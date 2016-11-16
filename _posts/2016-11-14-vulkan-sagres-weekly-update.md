---
layout: post
title: "vulkan sagres weekly update"
description: ""
category: vulkan-sagres
tags: vulkan-sagres updates vulkan [deferred shading]
---
### Deferred renderer
This last week I have mainly worked on the implementation of the deferred
renderer and made sure that that worked solidly.
Here are some pictures from the g-buffer:

![Normals](http://i.imgur.com/rJSjVzA.jpg)

![Light accumulation](http://i.imgur.com/RRQh6pc.jpg)

![Specular albedo](http://i.imgur.com/VmdWEAu.jpg)

![Diffuse specular](http://i.imgur.com/LdcdNIK.jpg)

![Positions](http://i.imgur.com/bLtRjaE.jpg)

![Final composition](http://i.imgur.com/txcOr6h.png)

I added normal mapping and modified the model loader to account for
bitangents and tangents when models have symmetrical UV coordinates as
explained here
<http://www.opengl-tutorial.org/intermediate-tutorials/tutorial-13-normal-mapping/>
It
flips the direction of the tangent if the handedness is not right handed.
This allows to use the same shader code also in models which have 
symmetrical UV coords.

Another issue came up where I was erroneously setting the uniform buffer for
model matrices by updating the relative buffer, per mesh, expecting the update
to remain per-draw call, whereas the update to be used would have been the
latest buffer update at the time the command buffer was dispatched by the queue.
To solve this I did some research and found this
<https://www.reddit.com/r/vulkan/comments/4ibeoh/how_to_set_per_objects_uniform/>
and successively this
<https://github.com/nvpro-samples/gl_vk_threaded_cadscene/blob/master/doc/vulkan_uniforms.md>
which helped me to understand how to approach this.

My solution was to have each model create its own buffer to store an array of
model matrices, one per mesh. Each model also creates its own descriptor set
for a dynamic UBO, getting the layout from the main renderer, since the layout
does not depend on the model but it's an application-wide design decision.
Once it creates the descriptor set, it updates is with the contents of the
buffer array, and the meshes are updated to store the offset of their
matrix within the model buffer.\\
These are then used in conjunction with `vkCmdBindDescriptorSets` to tell
Vulkan where to fetch the matrix.\\
Another problem arose where the offset passed to the aforementioned method was
not a multiple of the minimum required offset alignment.
After some research, I resolved to looking up the LunarG github repo for the
validation layers, looking for the error message I was getting, and found out
where it was generating from:
<https://github.com/LunarG/VulkanSamples/blob/master/layers/core_validation.cpp#L7697>
This allowed me to come up with a solution where I account for this minimum size
and align the memory properly.

#### Structure
This implementation uses four colour render targets and one depth buffer,
organised like this:

*PICTURE HERE HAND DRAWN*

The diffuse and specular albedo buffers are `VK_FORMAT_R8G8B8A8_UNORM`,
while the position, normal and light accumulation buffers are
`VK_FORMAT_R16B16G16A16_SFLOAT`.\\
I have tried using different formats for the buffers which require more
precision, such as `VK_FORMAT_A2R10G10B10_SSCALED_PACK32` but my machine
did not support that so I reverted to a format which was supported.

#### Features
The deferred renderer implements gamma correction and tonemapping.

It uses a pipeline as explained on
<https://mtnphil.wordpress.com/2014/04/30/cleaning-up-the-hdr-pipeline/>:

![hdr_pipeline_img](https://mtnphil.files.wordpress.com/2014/04/pipeline.jpg)

I have tried using the sRGB texture format for my intermediary buffers to avoid
having to convert from gamma to linear in the shaders when performing the light
subpass but my machine did not support that so I had to code that into the 
shaders when reading from the g-buffer.
However, my machine does support sRGB for the swapchain images so I took
advantage of that and used the `VK_FORMAT_R8G8B8A8_SRGB` for those images.

The tonemapping operator I am using is the Reinhard
(<https://www.cs.utah.edu/~reinhard/cdrom/tonemap.pdf>). I am aware of the
other ones available as explained by Pettineo here
<https://mynameismjp.wordpress.com/2010/04/30/a-closer-look-at-tone-mapping/>
and I would like to experiment with the Uncharted 2 filmic once.
Moreover my implementation of the Reinhard uses a hard-coded value for the
exposure; I am aware of it and I want to change that to use a uniform so
that it can be tweaked or better, calculate an average of the scene by
downsampling. I would also like to explore adaptive tonemapping.

The framework has been built from scratch, and while it still needs lots of
work in terms of consistency and class design, of which I am aware, it gets
the job done for now. It will be improved with time.

#### Assimp
For the loader, I am using Assimp; when loading an .obj file it would
never retrieve the associated .mtl file and initially I assumed the issue
was in my code since assimp is such a popular and mature library for model
loading. However, after some debugging with gdb I found out that the issue
was in assimp's source code.
Hence I fixed up the function, checked that it worked, and then I forked
the original repo, fixed it there, and issued a pull request which has
now been accepted.
I also modified their CMakeLists to include one of the features which was
most requested on assimp's github page: building only certain importers
instead of always building all the importers and manually excluding the
not-needed ones.

### Papers and reading
Having read the Reyes paper, I got interested into stochastic sampling and
where the name comes from, so I researched into it and read up about
stochastic methods and Monte Carlo methods, and why they are used in
raytracing.
It was very interesting and I plan on doing more research into this.

I have also read some more of Real-Time Rendering and that led me to
read up once again the physically based rendering course slides
by Hoffman from Siggraph. With the background gathered from Real-Time
Rendering it was fairly straightforward to understand the equations
presented, even though I still need to get a firm grasp of
hemisphere integrals. I have recently bought a calculus book which I
plan to dive in asap.

### Git
I have started to port the application from using git submodules to
git subtrees. The main reason for this is that I can fix the external
libraries if needed and push those changes to my main repo, keeping them.
It also allows me to activate pull requests if I want.
All of this would be much more difficult with submodules.

I have emailed Github to remove the data of the fork I had made to start
learning Vulkan; this way the commits history is clean. I was not really
using any code from that repo anyway.
