---
title: "MMD via OpenGL"
date: 2023-06-16T01:18:59-08:00
draft: false
author: ["Lucas Li"]
tags: ["Projects"]
featured_image: "/images/others/mmd_cover.png"
description: "explore integration of OpenGL & real-time music synch for MMD"
toc: true
---

This [MMD (MikuMikuDance) project](https://github.com/GelzoneXUnsas/MMD-OpenGL), is an exploration into the realms of OpenGL, the [Saba](https://github.com/benikabocha/saba) library, and real-time music synchronization. This project revolves around creating a captivating and seamless MMD animation experience. <!--more-->

![](https://github.com/GelzoneXUnsas/MMD-OpenGL/raw/main/saba/mmd_OpenGL/cover.png)

## Overview

MikuMikuDance (MMD) is a popular and free 3D animation software that empowers users to create and animate virtual characters. It gained widespread recognition, especially in the Vocaloid community, where users focus on animating characters associated with Vocaloid, a singing synth technology. The iconic character Hatsune Miku often takes the spotlight in these animated creations.

[![MMD Viewer Preview](Link to Your MMD Viewer Image)](Link to Your Demo Video)

_Click on the image above for a demo._

### File Formats

#### PMX Format (Polygon Model eXtended)

![](https://learnmmd.com/wp-content/uploads/2020/05/Center-bone-OK.jpg)

In the realm of MMD animation, the choice of file format for 3D character models is crucial. While standard formats like OBJ and FBX exist, my project utilizes the PMX format. PMX is specialized for MMD, catering to the specific requirements of animating 3D character models. It includes essential elements such as bone structure, facial expressions, and physics settings, offering a comprehensive solution for character animation.

#### VMD Format (Vocaloid Motion Data)
Unlike traditional animation formats like FBX, MMD separates model and motion files for enhanced customization and community collaboration. Motion data, often stored in the VMD (Vocaloid Motion Data) format, goes beyond ordinary bone movements. It encompasses features crucial to MMD animations, including facial expressions, lip-syncing, and, most notably, camera movements. This separation allows for a more dynamic and collaborative animation creation process within the MMD community.


### Music Synchronization

Achieving precise music synchronization in MMD animation is crucial for an immersive experience. I incorporated real-time audio playback using the midiaudio library. The absolute timer, controlled by the engine based on the global time in PCM frames, facilitated seamless synchronization between the animation and music. Every beat and motion harmoniously blend to enhance the overall immersive experience.

### GLFW Transparency

Aesthetic presentation is a key focus of this project. Leveraging the GLFW library, I implemented transparency for both the background and the window bar. With the assistance of OpenGL shaders, this transparency creates a visually stunning presentation. The integration of the MMD animation into the slides is seamless, highlighting meticulous attention to detail and enhancing the overall visual appeal.

### Saba Library Integration

The choice of the Saba library played a pivotal role in the success of the MMD viewer project. Saba, a versatile model loader and viewer, supports the PMX format essential for MMD. During the initial phase, I experimented with Saba using ImGui and built-in commands, allowing dynamic model and animation adjustments. As the project progressed, I seamlessly integrated Saba with OpenGL, combining its powerful capabilities with the flexibility of OpenGL shaders. This integration resulted in a customized file reader for loading PMX and VMD files, forming a robust foundation for the MMD viewer.

### Conclusion

In summary, the MMD viewer project exemplifies the harmonious integration of OpenGL, the Saba library, and real-time music synchronization. The meticulous attention to detail in achieving GLFW transparency, coupled with the expressive capabilities of the Saba library, results in a visually captivating experience.

Stay tuned for the upcoming demonstration, where I'll showcase the impact of these technical elements and the creative flair they bring to the world of MMD animation.
