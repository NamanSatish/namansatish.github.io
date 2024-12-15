---
layout: distill
title: Neural Radiance Fields
description: "Part of CS180 : Intro to Computer Vision and Computational Photography"
img: assets/img/cs180/projfinal/output/test_images.gif
importance: 5
category: school
bibliography: empty.bib
toc:
  - name: "Neural Radiance Fields"
  - name: "2D Case"
  - name: "3D NeRF"
---

# Neural Radiance Fields

This project was my final project for the course CS180: Intro to Computer Vision and Computational Photography. The project was to implement Neural Radiance Fields, which is a method for synthesizing novel views of complex scenes. The method works by learning a mapping from input images to the volumetric radiance field, which is then used to render novel views.

# 2D Case

We begin with the humble 2D case for Neural Radiance Fields. The task is to learn a mapping from a 2D image to a 2D radiance field. The radiance field is represented as a 2D grid of colors, where each color represents the radiance at that point. The mapping is learned using a neural network, which takes in the 2D image coordinate as input and outputs the radiance field/RGB value. The network is trained using randomly sampled points from the image, and once trained the model is capable of reproducing the image via just the coordinates. The model is in essence overfitting to the training data to produce our image.

Let us take a look at our two images

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/cs180/projfinal/fox.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/cs180/projfinal/bayer.jpg" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
    Our two images consist of a fox and a bayer pattern.
</div>

<!--
assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_32_FC_2_LF_15_iter_3000_data_fixed_iter_1.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_32_FC_2_LF_15_iter_3000_data_fixed_iter_300.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_32_FC_2_LF_15_iter_3000_data_fixed_iter_600.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_32_FC_2_LF_15_iter_3000_data_fixed_iter_900.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_32_FC_2_LF_15_iter_3000_data_fixed_iter_1200.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_32_FC_2_LF_15_iter_3000_data_fixed_iter_1500.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_32_FC_2_LF_15_iter_3000_data_fixed_iter_1800.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_32_FC_2_LF_15_iter_3000_data_fixed_iter_2100.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_32_FC_2_LF_15_iter_3000_data_fixed_iter_2400.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_32_FC_2_LF_15_iter_3000_data_fixed_iter_2700.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_32_FC_2_LF_15_iter_3000_data_fixed_iter_3000.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_32_FC_2_LF_15_iter_3000.png
assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_256_FC_2_LF_10_iter_3000_data_fixed_iter_1.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_256_FC_2_LF_10_iter_3000_data_fixed_iter_300.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_256_FC_2_LF_10_iter_3000_data_fixed_iter_600.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_256_FC_2_LF_10_iter_3000_data_fixed_iter_900.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_256_FC_2_LF_10_iter_3000_data_fixed_iter_1200.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_256_FC_2_LF_10_iter_3000_data_fixed_iter_1500.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_256_FC_2_LF_10_iter_3000_data_fixed_iter_1800.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_256_FC_2_LF_10_iter_3000_data_fixed_iter_2100.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_256_FC_2_LF_10_iter_3000_data_fixed_iter_2400.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_256_FC_2_LF_10_iter_3000_data_fixed_iter_2700.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_256_FC_2_LF_10_iter_3000_data_fixed_iter_3000.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_256_FC_2_LF_10_iter_3000.png
assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_256_FC_2_LF_20_iter_3000_data_fixed_iter_1.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_256_FC_2_LF_20_iter_3000_data_fixed_iter_300.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_256_FC_2_LF_20_iter_3000_data_fixed_iter_600.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_256_FC_2_LF_20_iter_3000_data_fixed_iter_900.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_256_FC_2_LF_20_iter_3000_data_fixed_iter_1200.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_256_FC_2_LF_20_iter_3000_data_fixed_iter_1500.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_256_FC_2_LF_20_iter_3000_data_fixed_iter_1800.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_256_FC_2_LF_20_iter_3000_data_fixed_iter_2100.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_256_FC_2_LF_20_iter_3000_data_fixed_iter_2400.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_256_FC_2_LF_20_iter_3000_data_fixed_iter_2700.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_256_FC_2_LF_20_iter_3000_data_fixed_iter_3000.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_256_FC_2_LF_20_iter_3000.png
assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_256_FC_6_LF_10_iter_3000_data_fixed_iter_1.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_256_FC_6_LF_10_iter_3000_data_fixed_iter_300.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_256_FC_6_LF_10_iter_3000_data_fixed_iter_600.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_256_FC_6_LF_10_iter_3000_data_fixed_iter_900.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_256_FC_6_LF_10_iter_3000_data_fixed_iter_1200.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_256_FC_6_LF_10_iter_3000_data_fixed_iter_1500.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_256_FC_6_LF_10_iter_3000_data_fixed_iter_1800.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_256_FC_6_LF_10_iter_3000_data_fixed_iter_2100.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_256_FC_6_LF_10_iter_3000_data_fixed_iter_2400.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_256_FC_6_LF_10_iter_3000_data_fixed_iter_2700.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_256_FC_6_LF_10_iter_3000_data_fixed_iter_3000.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_256_FC_6_LF_10_iter_3000.png
assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_512_FC_2_LF_10_iter_3000_data_fixed_iter_1.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_512_FC_2_LF_10_iter_3000_data_fixed_iter_300.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_512_FC_2_LF_10_iter_3000_data_fixed_iter_600.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_512_FC_2_LF_10_iter_3000_data_fixed_iter_900.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_512_FC_2_LF_10_iter_3000_data_fixed_iter_1200.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_512_FC_2_LF_10_iter_3000_data_fixed_iter_1500.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_512_FC_2_LF_10_iter_3000_data_fixed_iter_1800.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_512_FC_2_LF_10_iter_3000_data_fixed_iter_2100.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_512_FC_2_LF_10_iter_3000_data_fixed_iter_2400.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_512_FC_2_LF_10_iter_3000_data_fixed_iter_2700.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_512_FC_2_LF_10_iter_3000_data_fixed_iter_3000.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_512_FC_2_LF_10_iter_3000.png
assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_512_FC_6_LF_20_iter_3000_data_fixed_iter_1.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_512_FC_6_LF_20_iter_3000_data_fixed_iter_300.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_512_FC_6_LF_20_iter_3000_data_fixed_iter_600.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_512_FC_6_LF_20_iter_3000_data_fixed_iter_900.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_512_FC_6_LF_20_iter_3000_data_fixed_iter_1200.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_512_FC_6_LF_20_iter_3000_data_fixed_iter_1500.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_512_FC_6_LF_20_iter_3000_data_fixed_iter_1800.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_512_FC_6_LF_20_iter_3000_data_fixed_iter_2100.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_512_FC_6_LF_20_iter_3000_data_fixed_iter_2400.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_512_FC_6_LF_20_iter_3000_data_fixed_iter_2700.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_512_FC_6_LF_20_iter_3000_data_fixed_iter_3000.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_512_FC_6_LF_20_iter_3000.png
-->

<!-- Show 6 columns of images, increase the iteration number as you go down -->

<div class="row l-page mx-auto">
{% details **2D Training** %}
<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">HD:32,FC:2,LF:15</div>
    <div class="col-sm mt-3 mt-md-0">HD:256,FC:2,LF:10</div>
    <div class="col-sm mt-3 mt-md-0">HD:256,FC:2,LF:20</div>
    <div class="col-sm mt-3 mt-md-0">HD:256,FC:6,LF:10</div>
    <div class="col-sm mt-3 mt-md-0">HD:512,FC:2,LF:10</div>
    <div class="col-sm mt-3 mt-md-0">HD:512,FC:6,LF:20</div>
</div>
<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_32_FC_2_LF_15_iter_3000_data_fixed_iter_1.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_256_FC_2_LF_10_iter_3000_data_fixed_iter_1.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_256_FC_2_LF_20_iter_3000_data_fixed_iter_1.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_256_FC_6_LF_10_iter_3000_data_fixed_iter_1.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_512_FC_2_LF_10_iter_3000_data_fixed_iter_1.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_512_FC_6_LF_20_iter_3000_data_fixed_iter_1.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
</div>
<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_32_FC_2_LF_15_iter_3000_data_fixed_iter_300.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_256_FC_2_LF_10_iter_3000_data_fixed_iter_300.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_256_FC_2_LF_20_iter_3000_data_fixed_iter_300.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_256_FC_6_LF_10_iter_3000_data_fixed_iter_300.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_512_FC_2_LF_10_iter_3000_data_fixed_iter_300.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_512_FC_6_LF_20_iter_3000_data_fixed_iter_300.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
</div>
<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_32_FC_2_LF_15_iter_3000_data_fixed_iter_600.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_256_FC_2_LF_10_iter_3000_data_fixed_iter_600.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_256_FC_2_LF_20_iter_3000_data_fixed_iter_600.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_256_FC_6_LF_10_iter_3000_data_fixed_iter_600.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_512_FC_2_LF_10_iter_3000_data_fixed_iter_600.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_512_FC_6_LF_20_iter_3000_data_fixed_iter_600.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
</div>
<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_32_FC_2_LF_15_iter_3000_data_fixed_iter_900.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_256_FC_2_LF_10_iter_3000_data_fixed_iter_900.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_256_FC_2_LF_20_iter_3000_data_fixed_iter_900.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_256_FC_6_LF_10_iter_3000_data_fixed_iter_900.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_512_FC_2_LF_10_iter_3000_data_fixed_iter_900.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_512_FC_6_LF_20_iter_3000_data_fixed_iter_900.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
</div>
<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_32_FC_2_LF_15_iter_3000_data_fixed_iter_1200.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_256_FC_2_LF_10_iter_3000_data_fixed_iter_1200.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_256_FC_2_LF_20_iter_3000_data_fixed_iter_1200.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_256_FC_6_LF_10_iter_3000_data_fixed_iter_1200.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_512_FC_2_LF_10_iter_3000_data_fixed_iter_1200.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_512_FC_6_LF_20_iter_3000_data_fixed_iter_1200.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
</div>
<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_32_FC_2_LF_15_iter_3000_data_fixed_iter_1500.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_256_FC_2_LF_10_iter_3000_data_fixed_iter_1500.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_256_FC_2_LF_20_iter_3000_data_fixed_iter_1500.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_256_FC_6_LF_10_iter_3000_data_fixed_iter_1500.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_512_FC_2_LF_10_iter_3000_data_fixed_iter_1500.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_512_FC_6_LF_20_iter_3000_data_fixed_iter_1500.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
</div>
<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_32_FC_2_LF_15_iter_3000_data_fixed_iter_1800.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_256_FC_2_LF_10_iter_3000_data_fixed_iter_1800.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_256_FC_2_LF_20_iter_3000_data_fixed_iter_1800.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_256_FC_6_LF_10_iter_3000_data_fixed_iter_1800.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_512_FC_2_LF_10_iter_3000_data_fixed_iter_1800.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_512_FC_6_LF_20_iter_3000_data_fixed_iter_1800.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
</div>
<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_32_FC_2_LF_15_iter_3000_data_fixed_iter_2100.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_256_FC_2_LF_10_iter_3000_data_fixed_iter_2100.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_256_FC_2_LF_20_iter_3000_data_fixed_iter_2100.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_256_FC_6_LF_10_iter_3000_data_fixed_iter_2100.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_512_FC_2_LF_10_iter_3000_data_fixed_iter_2100.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_512_FC_6_LF_20_iter_3000_data_fixed_iter_2100.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
</div>
<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_32_FC_2_LF_15_iter_3000_data_fixed_iter_2400.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_256_FC_2_LF_10_iter_3000_data_fixed_iter_2400.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_256_FC_2_LF_20_iter_3000_data_fixed_iter_2400.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_256_FC_6_LF_10_iter_3000_data_fixed_iter_2400.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_512_FC_2_LF_10_iter_3000_data_fixed_iter_2400.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_512_FC_6_LF_20_iter_3000_data_fixed_iter_2400.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
</div>
<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_32_FC_2_LF_15_iter_3000_data_fixed_iter_2700.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_256_FC_2_LF_10_iter_3000_data_fixed_iter_2700.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_256_FC_2_LF_20_iter_3000_data_fixed_iter_2700.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_256_FC_6_LF_10_iter_3000_data_fixed_iter_2700.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_512_FC_2_LF_10_iter_3000_data_fixed_iter_2700.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_512_FC_6_LF_20_iter_3000_data_fixed_iter_2700.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
</div>
<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_32_FC_2_LF_15_iter_3000_data_fixed_iter_3000.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_256_FC_2_LF_10_iter_3000_data_fixed_iter_3000.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_256_FC_2_LF_20_iter_3000_data_fixed_iter_3000.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_256_FC_6_LF_10_iter_3000_data_fixed_iter_3000.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_512_FC_2_LF_10_iter_3000_data_fixed_iter_3000.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_512_FC_6_LF_20_iter_3000_data_fixed_iter_3000.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
</div>
<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_32_FC_2_LF_15_iter_3000.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_256_FC_2_LF_10_iter_3000.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_256_FC_2_LF_20_iter_3000.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_256_FC_6_LF_10_iter_3000.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_512_FC_2_LF_10_iter_3000.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d/fox_IC_2_OC_3_HD_512_FC_6_LF_20_iter_3000.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
{% enddetails %}
</div>

- In our LF:20, we can see that it learns the initial fox a little slower, however it gains finer details in the whisker for a very small cost in model size, and performs better than the standard at the same iteration count.
- In our FC:6, we can see that it learns the initial fox aorund the same rate as LF:20, however fine color details are not yet present. This may be due to the larger model size and the need for more iterations to learn the finer details, as we see great color details once trained for 3000 iterations
- In our HD:512, we can see that it learns the initial fox around the same rate as LF:20, and the output quality is similar to FC:6.

Next, I tried training an image with very visible patterns to get an understanding of different model parameters.

<!--
assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_128_FC_1_LF_5_iter_3000_data_fixed_iter_1.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_128_FC_1_LF_5_iter_3000_data_fixed_iter_300.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_128_FC_1_LF_5_iter_3000_data_fixed_iter_600.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_128_FC_1_LF_5_iter_3000_data_fixed_iter_900.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_128_FC_1_LF_5_iter_3000_data_fixed_iter_1200.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_128_FC_1_LF_5_iter_3000_data_fixed_iter_1500.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_128_FC_1_LF_5_iter_3000_data_fixed_iter_1800.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_128_FC_1_LF_5_iter_3000_data_fixed_iter_2100.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_128_FC_1_LF_5_iter_3000_data_fixed_iter_2400.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_128_FC_1_LF_5_iter_3000_data_fixed_iter_2700.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_128_FC_1_LF_5_iter_3000_data_fixed_iter_3000.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_128_FC_1_LF_5_iter_3000.png
assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_256_FC_2_LF_10_iter_3000_data_fixed_iter_1.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_256_FC_2_LF_10_iter_3000_data_fixed_iter_300.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_256_FC_2_LF_10_iter_3000_data_fixed_iter_600.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_256_FC_2_LF_10_iter_3000_data_fixed_iter_900.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_256_FC_2_LF_10_iter_3000_data_fixed_iter_1200.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_256_FC_2_LF_10_iter_3000_data_fixed_iter_1500.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_256_FC_2_LF_10_iter_3000_data_fixed_iter_1800.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_256_FC_2_LF_10_iter_3000_data_fixed_iter_2100.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_256_FC_2_LF_10_iter_3000_data_fixed_iter_2400.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_256_FC_2_LF_10_iter_3000_data_fixed_iter_2700.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_256_FC_2_LF_10_iter_3000_data_fixed_iter_3000.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_256_FC_2_LF_10_iter_3000.png
assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_256_FC_2_LF_20_iter_3000_data_fixed_iter_1.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_256_FC_2_LF_20_iter_3000_data_fixed_iter_300.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_256_FC_2_LF_20_iter_3000_data_fixed_iter_600.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_256_FC_2_LF_20_iter_3000_data_fixed_iter_900.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_256_FC_2_LF_20_iter_3000_data_fixed_iter_1200.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_256_FC_2_LF_20_iter_3000_data_fixed_iter_1500.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_256_FC_2_LF_20_iter_3000_data_fixed_iter_1800.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_256_FC_2_LF_20_iter_3000_data_fixed_iter_2100.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_256_FC_2_LF_20_iter_3000_data_fixed_iter_2400.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_256_FC_2_LF_20_iter_3000_data_fixed_iter_2700.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_256_FC_2_LF_20_iter_3000_data_fixed_iter_3000.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_256_FC_2_LF_20_iter_3000.png
assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_512_FC_4_LF_15_iter_3000_data_fixed_iter_1.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_512_FC_4_LF_15_iter_3000_data_fixed_iter_300.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_512_FC_4_LF_15_iter_3000_data_fixed_iter_600.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_512_FC_4_LF_15_iter_3000_data_fixed_iter_900.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_512_FC_4_LF_15_iter_3000_data_fixed_iter_1200.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_512_FC_4_LF_15_iter_3000_data_fixed_iter_1500.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_512_FC_4_LF_15_iter_3000_data_fixed_iter_1800.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_512_FC_4_LF_15_iter_3000_data_fixed_iter_2100.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_512_FC_4_LF_15_iter_3000_data_fixed_iter_2400.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_512_FC_4_LF_15_iter_3000_data_fixed_iter_2700.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_512_FC_4_LF_15_iter_3000_data_fixed_iter_3000.jpg
assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_512_FC_4_LF_15_iter_3000.png
-->

<div class="row l-page mx-auto">
{% details **Bayer 2D Training** %}
<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">HD:128, FC:1, LF:5</div>
    <div class="col-sm mt-3 mt-md-0">HD:256, FC:2, LF:10</div>
    <div class="col-sm mt-3 mt-md-0">HD:256, FC:2, LF:20</div>
    <div class="col-sm mt-3 mt-md-0">HD:512, FC:4, LF:15</div>
</div>
<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_128_FC_1_LF_5_iter_3000_data_fixed_iter_1.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_256_FC_2_LF_10_iter_3000_data_fixed_iter_1.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_256_FC_2_LF_20_iter_3000_data_fixed_iter_1.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_512_FC_4_LF_15_iter_3000_data_fixed_iter_1.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
</div>
<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_128_FC_1_LF_5_iter_3000_data_fixed_iter_300.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_256_FC_2_LF_10_iter_3000_data_fixed_iter_300.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_256_FC_2_LF_20_iter_3000_data_fixed_iter_300.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_512_FC_4_LF_15_iter_3000_data_fixed_iter_300.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
</div>
<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_128_FC_1_LF_5_iter_3000_data_fixed_iter_600.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_256_FC_2_LF_10_iter_3000_data_fixed_iter_600.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_256_FC_2_LF_20_iter_3000_data_fixed_iter_600.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_512_FC_4_LF_15_iter_3000_data_fixed_iter_600.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
</div>
<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_128_FC_1_LF_5_iter_3000_data_fixed_iter_900.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_256_FC_2_LF_10_iter_3000_data_fixed_iter_900.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_256_FC_2_LF_20_iter_3000_data_fixed_iter_900.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_512_FC_4_LF_15_iter_3000_data_fixed_iter_900.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
</div>
<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_128_FC_1_LF_5_iter_3000_data_fixed_iter_1200.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_256_FC_2_LF_10_iter_3000_data_fixed_iter_1200.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_256_FC_2_LF_20_iter_3000_data_fixed_iter_1200.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_512_FC_4_LF_15_iter_3000_data_fixed_iter_1200.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
</div>
<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_128_FC_1_LF_5_iter_3000_data_fixed_iter_1500.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_256_FC_2_LF_10_iter_3000_data_fixed_iter_1500.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_256_FC_2_LF_20_iter_3000_data_fixed_iter_1500.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_512_FC_4_LF_15_iter_3000_data_fixed_iter_1500.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
</div>
<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_128_FC_1_LF_5_iter_3000_data_fixed_iter_1800.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_256_FC_2_LF_10_iter_3000_data_fixed_iter_1800.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_256_FC_2_LF_20_iter_3000_data_fixed_iter_1800.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_512_FC_4_LF_15_iter_3000_data_fixed_iter_1800.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
</div>
<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_128_FC_1_LF_5_iter_3000_data_fixed_iter_2100.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_256_FC_2_LF_10_iter_3000_data_fixed_iter_2100.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_256_FC_2_LF_20_iter_3000_data_fixed_iter_2100.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_512_FC_4_LF_15_iter_3000_data_fixed_iter_2100.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
</div>
<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_128_FC_1_LF_5_iter_3000_data_fixed_iter_2400.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_256_FC_2_LF_10_iter_3000_data_fixed_iter_2400.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_256_FC_2_LF_20_iter_3000_data_fixed_iter_2400.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_512_FC_4_LF_15_iter_3000_data_fixed_iter_2400.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
</div>
<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_128_FC_1_LF_5_iter_3000_data_fixed_iter_2700.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_256_FC_2_LF_10_iter_3000_data_fixed_iter_2700.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_256_FC_2_LF_20_iter_3000_data_fixed_iter_2700.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_512_FC_4_LF_15_iter_3000_data_fixed_iter_2700.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
</div>
<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_128_FC_1_LF_5_iter_3000_data_fixed_iter_3000.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_256_FC_2_LF_10_iter_3000_data_fixed_iter_3000.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_256_FC_2_LF_20_iter_3000_data_fixed_iter_3000.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_512_FC_4_LF_15_iter_3000_data_fixed_iter_3000.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
</div>
<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_128_FC_1_LF_5_iter_3000.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_256_FC_2_LF_10_iter_3000.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_256_FC_2_LF_20_iter_3000.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/checkpoints/nerf2d_bayer/bayer_IC_2_OC_3_HD_512_FC_4_LF_15_iter_3000.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
</div>
{% enddetails %}
</div>

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include video.liquid path="assets/video/cs180/projfinal/output_bayer.mp4" class="img-fluid rounded z-depth-1" controls=true autoplay=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include video.liquid path="assets/video/cs180/projfinal/output_fox.mp4" class="img-fluid rounded z-depth-1" controls=true autoplay=true%}
    </div>
</div>
<div class="caption">
    Here are the two models being trained!
</div>

# 3D NeRF

Part 2.1
To begin, I had to write functions that would convert my pixel on an image to a ray. To do so, we need to know the camera intrinsics, the camera extrinsics, and the pixel location. The camera intrinsics are the focal length, the principal point, and the image resolution. The camera extrinsics are the camera pose and the camera rotation. Then we can know our ray origin via the camera pose, and compute the direction via computing a point for a pixel on the image plane with a depth in the world space, and using the origin and coordinate to compute a unit vector. This required me to also implement a function to compute a world coordinate from a pixel on the image, and a function to convert from camera coordinates to world coordinates.

Part 2.2
Now we can being sampling, we take some random indices from our training cameras, and sample some random coordinates on those cameras. We compute their rays, and then we can sample points on those rays to get points that travel through a 3D space on the ray.

Part 2.3
Visualized, this looks like:

<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/cs180/projfinal/Viser.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>

Part 2.4
Now we can construct our MLP in a very similar fashion to above, but we need to make sure to concatenate our positional encodings deeper into the network to get better performance.

Part 2.5
Now we can implement the core voluming equation, which goes over the ray and adds small iterations of each step's color to the final output color.

With that we can construct our NeRF model and train it on our Lego dataset! We sample 10,000 points each iteration, with 64 samples on each ray, and after computing the expected color for each point, we compute the cumulative color of the ray and use that to compute the loss. We then backpropagate and update our model.

Using this, we can achieve some impressive results!

<!--
assets/img/cs180/projfinal/output/test_images.gif
assets/img/cs180/projfinal/output/test_view_1_iter_5000.png
assets/img/cs180/projfinal/output/test_view_2_iter_5000.png
assets/img/cs180/projfinal/output/test_view_3_iter_5000.png
assets/img/cs180/projfinal/output/test_view_4_iter_5000.png
assets/img/cs180/projfinal/output/test_view_5_iter_5000.png
assets/img/cs180/projfinal/output/test_view_6_iter_5000.png
assets/img/cs180/projfinal/output/test_view_7_iter_5000.png
assets/img/cs180/projfinal/output/test_view_8_iter_5000.png
assets/img/cs180/projfinal/output/test_view_9_iter_5000.png
assets/img/cs180/projfinal/output/test_view_10_iter_5000.png
assets/img/cs180/projfinal/output/test_view_11_iter_5000.png
assets/img/cs180/projfinal/output/test_view_12_iter_5000.png
assets/img/cs180/projfinal/output/test_view_13_iter_5000.png
assets/img/cs180/projfinal/output/test_view_14_iter_5000.png
assets/img/cs180/projfinal/output/test_view_15_iter_5000.png
assets/img/cs180/projfinal/output/test_view_16_iter_5000.png
assets/img/cs180/projfinal/output/test_view_17_iter_5000.png
assets/img/cs180/projfinal/output/test_view_18_iter_5000.png
assets/img/cs180/projfinal/output/test_view_19_iter_5000.png
assets/img/cs180/projfinal/output/test_view_20_iter_5000.png
assets/img/cs180/projfinal/output/test_view_21_iter_5000.png
assets/img/cs180/projfinal/output/test_view_22_iter_5000.png
assets/img/cs180/projfinal/output/test_view_23_iter_5000.png
assets/img/cs180/projfinal/output/test_view_24_iter_5000.png
assets/img/cs180/projfinal/output/test_view_25_iter_5000.png
assets/img/cs180/projfinal/output/test_view_26_iter_5000.png
assets/img/cs180/projfinal/output/test_view_27_iter_5000.png
assets/img/cs180/projfinal/output/test_view_28_iter_5000.png
assets/img/cs180/projfinal/output/test_view_29_iter_5000.png
assets/img/cs180/projfinal/output/test_view_30_iter_5000.png
assets/img/cs180/projfinal/output/test_view_31_iter_5000.png
assets/img/cs180/projfinal/output/test_view_32_iter_5000.png
assets/img/cs180/projfinal/output/test_view_33_iter_5000.png
assets/img/cs180/projfinal/output/test_view_34_iter_5000.png
assets/img/cs180/projfinal/output/test_view_35_iter_5000.png
assets/img/cs180/projfinal/output/test_view_36_iter_5000.png
assets/img/cs180/projfinal/output/test_view_37_iter_5000.png
assets/img/cs180/projfinal/output/test_view_38_iter_5000.png
assets/img/cs180/projfinal/output/test_view_39_iter_5000.png
assets/img/cs180/projfinal/output/test_view_40_iter_5000.png
assets/img/cs180/projfinal/output/test_view_41_iter_5000.png
assets/img/cs180/projfinal/output/test_view_42_iter_5000.png
assets/img/cs180/projfinal/output/test_view_43_iter_5000.png
assets/img/cs180/projfinal/output/test_view_44_iter_5000.png
assets/img/cs180/projfinal/output/test_view_45_iter_5000.png
assets/img/cs180/projfinal/output/test_view_46_iter_5000.png
assets/img/cs180/projfinal/output/test_view_47_iter_5000.png
assets/img/cs180/projfinal/output/test_view_48_iter_5000.png
assets/img/cs180/projfinal/output/test_view_49_iter_5000.png
assets/img/cs180/projfinal/output/test_view_50_iter_5000.png
assets/img/cs180/projfinal/output/test_view_51_iter_5000.png
assets/img/cs180/projfinal/output/test_view_52_iter_5000.png
assets/img/cs180/projfinal/output/test_view_53_iter_5000.png
assets/img/cs180/projfinal/output/test_view_54_iter_5000.png
assets/img/cs180/projfinal/output/test_view_55_iter_5000.png
assets/img/cs180/projfinal/output/test_view_56_iter_5000.png
assets/img/cs180/projfinal/output/test_view_57_iter_5000.png
assets/img/cs180/projfinal/output/test_view_58_iter_5000.png
assets/img/cs180/projfinal/output/test_view_59_iter_5000.png
assets/img/cs180/projfinal/output/test_view_60_iter_5000.png
assets/img/cs180/projfinal/output/training_loss_and_psnr.png
assets/img/cs180/projfinal/output/validation_view_1_iter_0.png
assets/img/cs180/projfinal/output/validation_view_1_iter_250.png
assets/img/cs180/projfinal/output/validation_view_1_iter_500.png
assets/img/cs180/projfinal/output/validation_view_1_iter_750.png
assets/img/cs180/projfinal/output/validation_view_1_iter_1000.png
assets/img/cs180/projfinal/output/validation_view_1_iter_1250.png
assets/img/cs180/projfinal/output/validation_view_1_iter_1500.png
assets/img/cs180/projfinal/output/validation_view_1_iter_1750.png
assets/img/cs180/projfinal/output/validation_view_1_iter_2000.png
assets/img/cs180/projfinal/output/validation_view_1_iter_2250.png
assets/img/cs180/projfinal/output/validation_view_1_iter_2500.png
assets/img/cs180/projfinal/output/validation_view_1_iter_2750.png
assets/img/cs180/projfinal/output/validation_view_1_iter_3000.png
assets/img/cs180/projfinal/output/validation_view_1_iter_3250.png
assets/img/cs180/projfinal/output/validation_view_1_iter_3500.png
assets/img/cs180/projfinal/output/validation_view_1_iter_3750.png
assets/img/cs180/projfinal/output/validation_view_1_iter_4000.png
assets/img/cs180/projfinal/output/validation_view_1_iter_4250.png
assets/img/cs180/projfinal/output/validation_view_1_iter_4500.png
assets/img/cs180/projfinal/output/validation_view_1_iter_4750.png
assets/img/cs180/projfinal/output/validation_view_1_iter_5000.png
assets/img/cs180/projfinal/output/validation_view_2_iter_0.png
assets/img/cs180/projfinal/output/validation_view_2_iter_250.png
assets/img/cs180/projfinal/output/validation_view_2_iter_500.png
assets/img/cs180/projfinal/output/validation_view_2_iter_750.png
assets/img/cs180/projfinal/output/validation_view_2_iter_1000.png
assets/img/cs180/projfinal/output/validation_view_2_iter_1250.png
assets/img/cs180/projfinal/output/validation_view_2_iter_1500.png
assets/img/cs180/projfinal/output/validation_view_2_iter_1750.png
assets/img/cs180/projfinal/output/validation_view_2_iter_2000.png
assets/img/cs180/projfinal/output/validation_view_2_iter_2250.png
assets/img/cs180/projfinal/output/validation_view_2_iter_2500.png
assets/img/cs180/projfinal/output/validation_view_2_iter_2750.png
assets/img/cs180/projfinal/output/validation_view_2_iter_3000.png
assets/img/cs180/projfinal/output/validation_view_2_iter_3250.png
assets/img/cs180/projfinal/output/validation_view_2_iter_3500.png
assets/img/cs180/projfinal/output/validation_view_2_iter_3750.png
assets/img/cs180/projfinal/output/validation_view_2_iter_4000.png
assets/img/cs180/projfinal/output/validation_view_2_iter_4250.png
assets/img/cs180/projfinal/output/validation_view_2_iter_4500.png
assets/img/cs180/projfinal/output/validation_view_2_iter_4750.png
assets/img/cs180/projfinal/output/validation_view_2_iter_5000.png
assets/img/cs180/projfinal/output/validation_view_3_iter_0.png
assets/img/cs180/projfinal/output/validation_view_3_iter_250.png
assets/img/cs180/projfinal/output/validation_view_3_iter_500.png
assets/img/cs180/projfinal/output/validation_view_3_iter_750.png
assets/img/cs180/projfinal/output/validation_view_3_iter_1000.png
assets/img/cs180/projfinal/output/validation_view_3_iter_1250.png
assets/img/cs180/projfinal/output/validation_view_3_iter_1500.png
assets/img/cs180/projfinal/output/validation_view_3_iter_1750.png
assets/img/cs180/projfinal/output/validation_view_3_iter_2000.png
assets/img/cs180/projfinal/output/validation_view_3_iter_2250.png
assets/img/cs180/projfinal/output/validation_view_3_iter_2500.png
assets/img/cs180/projfinal/output/validation_view_3_iter_2750.png
assets/img/cs180/projfinal/output/validation_view_3_iter_3000.png
assets/img/cs180/projfinal/output/validation_view_3_iter_3250.png
assets/img/cs180/projfinal/output/validation_view_3_iter_3500.png
assets/img/cs180/projfinal/output/validation_view_3_iter_3750.png
assets/img/cs180/projfinal/output/validation_view_3_iter_4000.png
assets/img/cs180/projfinal/output/validation_view_3_iter_4250.png
assets/img/cs180/projfinal/output/validation_view_3_iter_4500.png
assets/img/cs180/projfinal/output/validation_view_3_iter_4750.png
assets/img/cs180/projfinal/output/validation_view_3_iter_5000.png
assets/img/cs180/projfinal/output/validation_view_4_iter_0.png
assets/img/cs180/projfinal/output/validation_view_4_iter_250.png
assets/img/cs180/projfinal/output/validation_view_4_iter_500.png
assets/img/cs180/projfinal/output/validation_view_4_iter_750.png
assets/img/cs180/projfinal/output/validation_view_4_iter_1000.png
assets/img/cs180/projfinal/output/validation_view_4_iter_1250.png
assets/img/cs180/projfinal/output/validation_view_4_iter_1500.png
assets/img/cs180/projfinal/output/validation_view_4_iter_1750.png
assets/img/cs180/projfinal/output/validation_view_4_iter_2000.png
assets/img/cs180/projfinal/output/validation_view_4_iter_2250.png
assets/img/cs180/projfinal/output/validation_view_4_iter_2500.png
assets/img/cs180/projfinal/output/validation_view_4_iter_2750.png
assets/img/cs180/projfinal/output/validation_view_4_iter_3000.png
assets/img/cs180/projfinal/output/validation_view_4_iter_3250.png
assets/img/cs180/projfinal/output/validation_view_4_iter_3500.png
assets/img/cs180/projfinal/output/validation_view_4_iter_3750.png
assets/img/cs180/projfinal/output/validation_view_4_iter_4000.png
assets/img/cs180/projfinal/output/validation_view_4_iter_4250.png
assets/img/cs180/projfinal/output/validation_view_4_iter_4500.png
assets/img/cs180/projfinal/output/validation_view_4_iter_4750.png
assets/img/cs180/projfinal/output/validation_view_4_iter_5000.png
assets/img/cs180/projfinal/output/validation_view_5_iter_0.png
assets/img/cs180/projfinal/output/validation_view_5_iter_250.png
assets/img/cs180/projfinal/output/validation_view_5_iter_500.png
assets/img/cs180/projfinal/output/validation_view_5_iter_750.png
assets/img/cs180/projfinal/output/validation_view_5_iter_1000.png
assets/img/cs180/projfinal/output/validation_view_5_iter_1250.png
assets/img/cs180/projfinal/output/validation_view_5_iter_1500.png
assets/img/cs180/projfinal/output/validation_view_5_iter_1750.png
assets/img/cs180/projfinal/output/validation_view_5_iter_2000.png
assets/img/cs180/projfinal/output/validation_view_5_iter_2250.png
assets/img/cs180/projfinal/output/validation_view_5_iter_2500.png
assets/img/cs180/projfinal/output/validation_view_5_iter_2750.png
assets/img/cs180/projfinal/output/validation_view_5_iter_3000.png
assets/img/cs180/projfinal/output/validation_view_5_iter_3250.png
assets/img/cs180/projfinal/output/validation_view_5_iter_3500.png
assets/img/cs180/projfinal/output/validation_view_5_iter_3750.png
assets/img/cs180/projfinal/output/validation_view_5_iter_4000.png
assets/img/cs180/projfinal/output/validation_view_5_iter_4250.png
assets/img/cs180/projfinal/output/validation_view_5_iter_4500.png
assets/img/cs180/projfinal/output/validation_view_5_iter_4750.png
assets/img/cs180/projfinal/output/validation_view_5_iter_5000.png
assets/img/cs180/projfinal/output/validation_view_6_iter_0.png
assets/img/cs180/projfinal/output/validation_view_6_iter_250.png
assets/img/cs180/projfinal/output/validation_view_6_iter_500.png
assets/img/cs180/projfinal/output/validation_view_6_iter_750.png
assets/img/cs180/projfinal/output/validation_view_6_iter_1000.png
assets/img/cs180/projfinal/output/validation_view_6_iter_1250.png
assets/img/cs180/projfinal/output/validation_view_6_iter_1500.png
assets/img/cs180/projfinal/output/validation_view_6_iter_1750.png
assets/img/cs180/projfinal/output/validation_view_6_iter_2000.png
assets/img/cs180/projfinal/output/validation_view_6_iter_2250.png
assets/img/cs180/projfinal/output/validation_view_6_iter_2500.png
assets/img/cs180/projfinal/output/validation_view_6_iter_2750.png
assets/img/cs180/projfinal/output/validation_view_6_iter_3000.png
assets/img/cs180/projfinal/output/validation_view_6_iter_3250.png
assets/img/cs180/projfinal/output/validation_view_6_iter_3500.png
assets/img/cs180/projfinal/output/validation_view_6_iter_3750.png
assets/img/cs180/projfinal/output/validation_view_6_iter_4000.png
assets/img/cs180/projfinal/output/validation_view_6_iter_4250.png
assets/img/cs180/projfinal/output/validation_view_6_iter_4500.png
assets/img/cs180/projfinal/output/validation_view_6_iter_4750.png
assets/img/cs180/projfinal/output/validation_view_6_iter_5000.png
assets/img/cs180/projfinal/output/validation_view_7_iter_0.png
assets/img/cs180/projfinal/output/validation_view_7_iter_250.png
assets/img/cs180/projfinal/output/validation_view_7_iter_500.png
assets/img/cs180/projfinal/output/validation_view_7_iter_750.png
assets/img/cs180/projfinal/output/validation_view_7_iter_1000.png
assets/img/cs180/projfinal/output/validation_view_7_iter_1250.png
assets/img/cs180/projfinal/output/validation_view_7_iter_1500.png
assets/img/cs180/projfinal/output/validation_view_7_iter_1750.png
assets/img/cs180/projfinal/output/validation_view_7_iter_2000.png
assets/img/cs180/projfinal/output/validation_view_7_iter_2250.png
assets/img/cs180/projfinal/output/validation_view_7_iter_2500.png
assets/img/cs180/projfinal/output/validation_view_7_iter_2750.png
assets/img/cs180/projfinal/output/validation_view_7_iter_3000.png
assets/img/cs180/projfinal/output/validation_view_7_iter_3250.png
assets/img/cs180/projfinal/output/validation_view_7_iter_3500.png
assets/img/cs180/projfinal/output/validation_view_7_iter_3750.png
assets/img/cs180/projfinal/output/validation_view_7_iter_4000.png
assets/img/cs180/projfinal/output/validation_view_7_iter_4250.png
assets/img/cs180/projfinal/output/validation_view_7_iter_4500.png
assets/img/cs180/projfinal/output/validation_view_7_iter_4750.png
assets/img/cs180/projfinal/output/validation_view_7_iter_5000.png
assets/img/cs180/projfinal/output/validation_view_8_iter_0.png
assets/img/cs180/projfinal/output/validation_view_8_iter_250.png
assets/img/cs180/projfinal/output/validation_view_8_iter_500.png
assets/img/cs180/projfinal/output/validation_view_8_iter_750.png
assets/img/cs180/projfinal/output/validation_view_8_iter_1000.png
assets/img/cs180/projfinal/output/validation_view_8_iter_1250.png
assets/img/cs180/projfinal/output/validation_view_8_iter_1500.png
assets/img/cs180/projfinal/output/validation_view_8_iter_1750.png
assets/img/cs180/projfinal/output/validation_view_8_iter_2000.png
assets/img/cs180/projfinal/output/validation_view_8_iter_2250.png
assets/img/cs180/projfinal/output/validation_view_8_iter_2500.png
assets/img/cs180/projfinal/output/validation_view_8_iter_2750.png
assets/img/cs180/projfinal/output/validation_view_8_iter_3000.png
assets/img/cs180/projfinal/output/validation_view_8_iter_3250.png
assets/img/cs180/projfinal/output/validation_view_8_iter_3500.png
assets/img/cs180/projfinal/output/validation_view_8_iter_3750.png
assets/img/cs180/projfinal/output/validation_view_8_iter_4000.png
assets/img/cs180/projfinal/output/validation_view_8_iter_4250.png
assets/img/cs180/projfinal/output/validation_view_8_iter_4500.png
assets/img/cs180/projfinal/output/validation_view_8_iter_4750.png
assets/img/cs180/projfinal/output/validation_view_8_iter_5000.png
assets/img/cs180/projfinal/output/validation_view_9_iter_0.png
assets/img/cs180/projfinal/output/validation_view_9_iter_250.png
assets/img/cs180/projfinal/output/validation_view_9_iter_500.png
assets/img/cs180/projfinal/output/validation_view_9_iter_750.png
assets/img/cs180/projfinal/output/validation_view_9_iter_1000.png
assets/img/cs180/projfinal/output/validation_view_9_iter_1250.png
assets/img/cs180/projfinal/output/validation_view_9_iter_1500.png
assets/img/cs180/projfinal/output/validation_view_9_iter_1750.png
assets/img/cs180/projfinal/output/validation_view_9_iter_2000.png
assets/img/cs180/projfinal/output/validation_view_9_iter_2250.png
assets/img/cs180/projfinal/output/validation_view_9_iter_2500.png
assets/img/cs180/projfinal/output/validation_view_9_iter_2750.png
assets/img/cs180/projfinal/output/validation_view_9_iter_3000.png
assets/img/cs180/projfinal/output/validation_view_9_iter_3250.png
assets/img/cs180/projfinal/output/validation_view_9_iter_3500.png
assets/img/cs180/projfinal/output/validation_view_9_iter_3750.png
assets/img/cs180/projfinal/output/validation_view_9_iter_4000.png
assets/img/cs180/projfinal/output/validation_view_9_iter_4250.png
assets/img/cs180/projfinal/output/validation_view_9_iter_4500.png
assets/img/cs180/projfinal/output/validation_view_9_iter_4750.png
assets/img/cs180/projfinal/output/validation_view_9_iter_5000.png
assets/img/cs180/projfinal/output/validation_view_10_iter_0.png
assets/img/cs180/projfinal/output/validation_view_10_iter_250.png
assets/img/cs180/projfinal/output/validation_view_10_iter_500.png
assets/img/cs180/projfinal/output/validation_view_10_iter_750.png
assets/img/cs180/projfinal/output/validation_view_10_iter_1000.png
assets/img/cs180/projfinal/output/validation_view_10_iter_1250.png
assets/img/cs180/projfinal/output/validation_view_10_iter_1500.png
assets/img/cs180/projfinal/output/validation_view_10_iter_1750.png
assets/img/cs180/projfinal/output/validation_view_10_iter_2000.png
assets/img/cs180/projfinal/output/validation_view_10_iter_2250.png
assets/img/cs180/projfinal/output/validation_view_10_iter_2500.png
assets/img/cs180/projfinal/output/validation_view_10_iter_2750.png
assets/img/cs180/projfinal/output/validation_view_10_iter_3000.png
assets/img/cs180/projfinal/output/validation_view_10_iter_3250.png
assets/img/cs180/projfinal/output/validation_view_10_iter_3500.png
assets/img/cs180/projfinal/output/validation_view_10_iter_3750.png
assets/img/cs180/projfinal/output/validation_view_10_iter_4000.png
assets/img/cs180/projfinal/output/validation_view_10_iter_4250.png
assets/img/cs180/projfinal/output/validation_view_10_iter_4500.png
assets/img/cs180/projfinal/output/validation_view_10_iter_4750.png
assets/img/cs180/projfinal/output/validation_view_10_iter_5000.png
-->

<div class="row l-page mx-auto">
{% details **Lego NeRF Training** %}
<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">View 1</div>
    <div class="col-sm mt-3 mt-md-0">View 2</div>
    <div class="col-sm mt-3 mt-md-0">View 3</div>
    <div class="col-sm mt-3 mt-md-0">View 4</div>
    <div class="col-sm mt-3 mt-md-0">View 5</div>
    <div class="col-sm mt-3 mt-md-0">View 6</div>
    <div class="col-sm mt-3 mt-md-0">View 7</div>
    <div class="col-sm mt-3 mt-md-0">View 8</div>
    <div class="col-sm mt-3 mt-md-0">View 9</div>
    <div class="col-sm mt-3 mt-md-0">View 10</div>
</div>
<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_1_iter_0.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_2_iter_0.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_3_iter_0.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_4_iter_0.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_5_iter_0.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_6_iter_0.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_7_iter_0.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_8_iter_0.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_9_iter_0.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_10_iter_0.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
</div>

<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_1_iter_250.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_2_iter_250.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_3_iter_250.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_4_iter_250.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_5_iter_250.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_6_iter_250.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_7_iter_250.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_8_iter_250.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_9_iter_250.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_10_iter_250.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
</div>

<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_1_iter_500.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_2_iter_500.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_3_iter_500.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_4_iter_500.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_5_iter_500.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_6_iter_500.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_7_iter_500.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_8_iter_500.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_9_iter_500.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_10_iter_500.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
</div>

<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_1_iter_750.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_2_iter_750.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_3_iter_750.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_4_iter_750.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_5_iter_750.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_6_iter_750.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_7_iter_750.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_8_iter_750.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_9_iter_750.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_10_iter_750.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
</div>

<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_1_iter_1000.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_2_iter_1000.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_3_iter_1000.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_4_iter_1000.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_5_iter_1000.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_6_iter_1000.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_7_iter_1000.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_8_iter_1000.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_9_iter_1000.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_10_iter_1000.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
</div>

<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_1_iter_1250.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_2_iter_1250.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_3_iter_1250.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_4_iter_1250.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_5_iter_1250.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_6_iter_1250.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_7_iter_1250.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_8_iter_1250.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_9_iter_1250.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_10_iter_1250.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
</div>

<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_1_iter_1500.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_2_iter_1500.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_3_iter_1500.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_4_iter_1500.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_5_iter_1500.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_6_iter_1500.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_7_iter_1500.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_8_iter_1500.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_9_iter_1500.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_10_iter_1500.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
</div>

<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_1_iter_1750.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_2_iter_1750.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_3_iter_1750.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_4_iter_1750.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_5_iter_1750.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_6_iter_1750.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_7_iter_1750.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_8_iter_1750.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_9_iter_1750.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_10_iter_1750.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
</div>

<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_1_iter_2000.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_2_iter_2000.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_3_iter_2000.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_4_iter_2000.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_5_iter_2000.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_6_iter_2000.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_7_iter_2000.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_8_iter_2000.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_9_iter_2000.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_10_iter_2000.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
</div>

<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_1_iter_2250.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_2_iter_2250.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_3_iter_2250.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_4_iter_2250.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_5_iter_2250.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_6_iter_2250.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_7_iter_2250.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_8_iter_2250.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_9_iter_2250.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_10_iter_2250.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
</div>

<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_1_iter_2500.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_2_iter_2500.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_3_iter_2500.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_4_iter_2500.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_5_iter_2500.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_6_iter_2500.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_7_iter_2500.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_8_iter_2500.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_9_iter_2500.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_10_iter_2500.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
</div>

<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_1_iter_2750.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_2_iter_2750.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_3_iter_2750.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_4_iter_2750.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_5_iter_2750.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_6_iter_2750.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_7_iter_2750.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_8_iter_2750.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_9_iter_2750.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_10_iter_2750.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
</div>

<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_1_iter_3000.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_2_iter_3000.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_3_iter_3000.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_4_iter_3000.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_5_iter_3000.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_6_iter_3000.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_7_iter_3000.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_8_iter_3000.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_9_iter_3000.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_10_iter_3000.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
</div>

<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_1_iter_3250.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_2_iter_3250.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_3_iter_3250.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_4_iter_3250.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_5_iter_3250.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_6_iter_3250.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_7_iter_3250.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_8_iter_3250.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_9_iter_3250.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_10_iter_3250.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
</div>

<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_1_iter_3500.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_2_iter_3500.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_3_iter_3500.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_4_iter_3500.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_5_iter_3500.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_6_iter_3500.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_7_iter_3500.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_8_iter_3500.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_9_iter_3500.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_10_iter_3500.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
</div>

<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_1_iter_3750.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_2_iter_3750.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_3_iter_3750.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_4_iter_3750.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_5_iter_3750.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_6_iter_3750.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_7_iter_3750.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_8_iter_3750.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_9_iter_3750.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_10_iter_3750.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
</div>

<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_1_iter_4000.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_2_iter_4000.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_3_iter_4000.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_4_iter_4000.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_5_iter_4000.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_6_iter_4000.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_7_iter_4000.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_8_iter_4000.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_9_iter_4000.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_10_iter_4000.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
</div>

<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_1_iter_4250.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_2_iter_4250.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_3_iter_4250.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_4_iter_4250.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_5_iter_4250.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_6_iter_4250.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_7_iter_4250.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_8_iter_4250.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_9_iter_4250.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_10_iter_4250.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
</div>

<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_1_iter_4500.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_2_iter_4500.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_3_iter_4500.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_4_iter_4500.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_5_iter_4500.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_6_iter_4500.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_7_iter_4500.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_8_iter_4500.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_9_iter_4500.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_10_iter_4500.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
</div>

<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_1_iter_4750.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_2_iter_4750.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_3_iter_4750.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_4_iter_4750.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_5_iter_4750.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_6_iter_4750.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_7_iter_4750.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_8_iter_4750.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_9_iter_4750.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_10_iter_4750.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
</div>

<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_1_iter_5000.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_2_iter_5000.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_3_iter_5000.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_4_iter_5000.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_5_iter_5000.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_6_iter_5000.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_7_iter_5000.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_8_iter_5000.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_9_iter_5000.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/validation_view_10_iter_5000.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
</div>
{% enddetails %}

</div>

<div class="row l-page mx-auto">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/training_loss_and_psnr.png" class="img-fluid rounded z-depth-1" zoomable=true%}
</div>

Here is the rendering of a novel view not in the training dataset!

<div class="row l-page mx-auto">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/test_images.gif" class="img-fluid rounded z-depth-1" zoomable=true%}
</div>

# Bells and Whistles

And when we make some changes to our rendering to use our opacity to interpret when rays are not hitting any objects, we can get the following:

<div class="row l-page mx-auto">
    {% include figure.liquid path="assets/img/cs180/projfinal/output/test_images_color.gif" class="img-fluid rounded z-depth-1" zoomable=true%}
</div>
