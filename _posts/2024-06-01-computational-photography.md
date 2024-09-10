---
layout: post
title: Computational Photography
date: 2024-06-01
description: Experimenting with computational photography on vacation!
tags: vacation images photomerge photography
categories: personal
thumbnail: assets/img/japan_trip/Fushimi_Inari_Taisha.jpg
images:
  compare: true
  slider: true
---

I'm currently set to take a course in CV/Computational Photography (CS 180) at Berkeley this Fall. To get a better understanding of the field, I've been experimenting with some of the techniques used in computational photography, such as image stitching, and used my recent trip to Japan as an opportunity to practice.

### Image Quality

Some of the resulting images are quite large (2GB), which is not really feasible for a blog post. But at the same time, compression is not ideal for showcasing the quality of the images. While I've tried to strike a balance, the images may take a while to load. To understand how much detail is lost in the compression, I've included a comparison slider for a ~500MB image below. It was compressed to a 9.2 MB JPG and to a 127KB WebP.

<img-comparison-slider>
  {% include figure.liquid path="assets/img/japan_trip/Fushimi_Inari_Taisha.jpg" loading="eager" class="img-fluid rounded z-depth-1" slot="first" %}
  {% include figure.liquid path="assets/img/japan_trip/Fushimi_Inari_Taisha.jpg" loading="eager" class="img-fluid rounded z-depth-1" slot="second" %}
</img-comparison-slider>

Here is a picture of Mount Fuji.

{% include figure.liquid path="assets/img/japan_trip/Mount_Fuji.jpg" loading="eager" class="img-fluid rounded z-depth-1" alt="Mount Fuji"%}
