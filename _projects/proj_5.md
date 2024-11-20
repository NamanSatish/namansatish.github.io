---
layout: distill
title: Diffusion Models
description: "Part of CS180 : Intro to Computer Vision and Computational Photography"
img: assets/img/cs180/proj5/output/2_5/epoch_20_4nums.gif
importance: 5
category: school
bibliography: empty.bib
toc:
  - name: "Diffusion Model Outputs"
  - name: "Sampling Loops"
  - name: "Classical Denoising"
  - name: "One-Step Denoising"
  - name: "Iterative Denoising"
  - name: "Diffusion Model Sampling"
  - name: "Classifier Free Guidance"
  - name: "Image to Image Translation"
  - name: "Hand Drawn and Web Images"
  - name: "Inpainting"
  - name: "Text Conditioned Image to Image Translation"
  - name: "Visual Anagrams"
  - name: "Hybrid Images"
  - name: "Bells and Whistles"
  - name: "Training a Single Step Denoising Model"
  - name: "Training a Diffusion Model"
  - name: "Training a Class Conditional Diffusion Model"
---

# Diffusion Model Outputs

Using DeepFloyd, we can first test out how generating images using the diffusion model works. As well as how the two stages of the model work together to generate images, and how the number of steps affects the quality of the generated images.

In our further testing, we will be using Berkeley's Sather Tower (Campanile) as our test image.
{% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/input/campanile.jpg" alt="Sather Tower" caption="Sather Tower" %}

<div class="row l-page mx-auto">
{% details **Image Generation** %}
<div class="row l-page">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/image_generation20vs64/stage_2_output_20_20_a man wearing a hat.png" alt="A Man Wearing a Hat" caption="Stage 1: 20 Steps, Stage 2: 20 Steps" %}
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/image_generation20vs64/stage_2_output_20_20_a rocket ship.png" alt="A Rocket Ship" caption="Stage 1: 20 Steps, Stage 2: 20 Steps" %}
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/image_generation20vs64/stage_2_output_20_20_an oil painting of a snowy mountain village.png" alt="An Oil Painting of a Snowy Mountain Village" caption="Stage 1: 20 Steps, Stage 2: 20 Steps" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/image_generation20vs64/stage_2_output_20_64_a man wearing a hat.png" alt="A Man Wearing a Hat" caption="Stage 1: 20 Steps, Stage 2: 64 Steps" %}
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/image_generation20vs64/stage_2_output_20_64_a rocket ship.png" alt="A Rocket Ship" caption="Stage 1: 20 Steps, Stage 2: 64 Steps" %}
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/image_generation20vs64/stage_2_output_20_64_an oil painting of a snowy mountain village.png" alt="An Oil Painting of a Snowy Mountain Village" caption="Stage 1: 20 Steps, Stage 2: 64 Steps" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/image_generation20vs64/stage_2_output_64_64_a man wearing a hat.png" alt="A Man Wearing a Hat" caption="Stage 1: 64 Steps, Stage 2: 64 Steps" %}
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/image_generation20vs64/stage_2_output_64_64_a rocket ship.png" alt="A Rocket Ship" caption="Stage 1: 64 Steps, Stage 2: 64 Steps" %}
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/image_generation20vs64/stage_2_output_64_64_an oil painting of a snowy mountain village.png" alt="An Oil Painting of a Snowy Mountain Village" caption="Stage 1: 64 Steps, Stage 2: 64 Steps" %}
    </div>
</div>
{% enddetails %}
</div>

We can notice that when stage 1's steps remain the same, the stage 2's steps chance the details of the image. The more steps in stage 2, the more detailed the image becomes. However, when more steps are added to stage 1, the image is denoised to a different image, such that the two results of stage 1 may not look the same anymore.

# Sampling Loops

First let us start by implementing the forward process of diffusion, adding noise.

<div class="row l-page mx-auto">
{% details **Noise Addition** %}
<!-- 
assets/img/cs180/proj5/output/forwardnoise/t_250.png
assets/img/cs180/proj5/output/forwardnoise/t_500.png
assets/img/cs180/proj5/output/forwardnoise/t_750.png
-->
<div class="row l-page mx-auto">
    {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/forwardnoise/t_250.png" alt="Sather Tower" caption="t = 250" %}
    {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/forwardnoise/t_500.png" alt="Sather Tower" caption="t = 500" %}
    {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/forwardnoise/t_750.png" alt="Sather Tower" caption="t = 750" %}
</div>
{% enddetails %}
</div>

# Classical Denoising

The classical way we could attempt to denoise noisy images is to apply gaussian blurs. Lets us attempt that on our noised images.

<div class="row l-page mx-auto">
{% details **Gaussian Blur** %}
<!--
assets/img/cs180/proj5/output/classical_denoising/denoised_t_250.png
assets/img/cs180/proj5/output/classical_denoising/denoised_t_500.png
assets/img/cs180/proj5/output/classical_denoising/denoised_t_750.png
-->
<div class="row l-page mx-auto">
    {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/classical_denoising/denoised_t_250.png" alt="Sather Tower" caption="t = 250" %}
</div>
<div class="row l-page mx-auto">
    {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/classical_denoising/denoised_t_500.png" alt="Sather Tower" caption="t = 500" %}
</div>
<div class="row l-page mx-auto">
    {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/classical_denoising/denoised_t_750.png" alt="Sather Tower" caption="t = 750" %}
</div>
{% enddetails %}
</div>

# One-Step Denoising

Now we can try using our UNet to predict the noise in the image, and predict $$x_{0}$$, the original image from the noised image $$x_{t}$$ and our prediced noise epsilon $$\epsilon$$.

<div class="row l-page mx-auto">
{% details **One-Step Denoising** %}
<!--
assets/img/cs180/proj5/output/one_step_denoising/denoised_t_250.png
assets/img/cs180/proj5/output/one_step_denoising/denoised_t_500.png
assets/img/cs180/proj5/output/one_step_denoising/denoised_t_750.png
assets/img/cs180/proj5/output/one_step_denoising/noisy_t_250.png
assets/img/cs180/proj5/output/one_step_denoising/noisy_t_500.png
assets/img/cs180/proj5/output/one_step_denoising/noisy_t_750.png
-->
<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/one_step_denoising/noisy_t_250.png" alt="Sather Tower" caption="t = 250" %}
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/one_step_denoising/denoised_t_250.png" alt="Sather Tower" caption="t = 250" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/one_step_denoising/noisy_t_500.png" alt="Sather Tower" caption="t = 500" %}
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/one_step_denoising/denoised_t_500.png" alt="Sather Tower" caption="t = 500" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/one_step_denoising/noisy_t_750.png" alt="Sather Tower" caption="t = 750" %}
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/one_step_denoising/denoised_t_750.png" alt="Sather Tower" caption="t = 750" %}
    </div>
</div>
{% enddetails %}
</div>

We can see that the UNet is able to denoise the image, and the denoised image is very similar to the original image, however as the noise increases, the denoised image becomes less representative of the original image.

# Iterative Denoising

Now instead of just denoising the image once, we can denoise the image multiple times, and by doing so, we can see that the denoised image becomes more representative of the original image.

<div class="row l-page mx-auto">
{% details **Iterative Denoising** %}
<!--
assets/img/cs180/proj5/output/iterative_denoise_start_10/gaussian_denoised.png
assets/img/cs180/proj5/output/iterative_denoise_start_10/iter_10_t_660.png
assets/img/cs180/proj5/output/iterative_denoise_start_10/iter_15_t_510.png
assets/img/cs180/proj5/output/iterative_denoise_start_10/iter_20_t_360.png
assets/img/cs180/proj5/output/iterative_denoise_start_10/iter_25_t_210.png
assets/img/cs180/proj5/output/iterative_denoise_start_10/iter_30_t_60.png
assets/img/cs180/proj5/output/iterative_denoise_start_10/iteratively_cleaned.png
assets/img/cs180/proj5/output/iterative_denoise_start_10/noisy.png
assets/img/cs180/proj5/output/iterative_denoise_start_10/one_step.png
-->

<!--
One row with all 5 iter with each in its own column div
One row with the noisy, one_step, and iteratively cleaned
-->
<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/iterative_denoise_start_10/iter_10_t_660.png" alt="Sather Tower" caption="t = 660" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/iterative_denoise_start_10/iter_15_t_510.png" alt="Sather Tower" caption="t = 510" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/iterative_denoise_start_10/iter_20_t_360.png" alt="Sather Tower" caption="t = 360" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/iterative_denoise_start_10/iter_25_t_210.png" alt="Sather Tower" caption="t = 210" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/iterative_denoise_start_10/iter_30_t_60.png" alt="Sather Tower" caption="t = 60" %}
    </div>
</div>
<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/iterative_denoise_start_10/noisy.png" alt="Sather Tower" caption="Noisy" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/iterative_denoise_start_10/one_step.png" alt="Sather Tower" caption="One Step" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/iterative_denoise_start_10/iteratively_cleaned.png" alt="Sather Tower" caption="Iteratively Cleaned" %}
    </div>
</div>
{% enddetails %}
</div>

# Diffusion Model Sampling

Now we can pass in noise, and start at maximum noise to denoise the image iteratively all the way from the noisy image to an "original image", we will use the prompt embedding "a high quality photo".

<!--
assets/img/cs180/proj5/output/diffusion_model_sampling/iter_0.png
assets/img/cs180/proj5/output/diffusion_model_sampling/iter_1.png
assets/img/cs180/proj5/output/diffusion_model_sampling/iter_2.png
assets/img/cs180/proj5/output/diffusion_model_sampling/iter_3.png
assets/img/cs180/proj5/output/diffusion_model_sampling/iter_4.png
-->

<div class="row l-page mx-auto">
{% details **Diffusion Model Sampling** %}
<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/diffusion_model_sampling/iter_0.png" alt="Iter 0" caption="Iter 0" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/diffusion_model_sampling/iter_1.png" alt="Iter 1" caption="Iter 1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/diffusion_model_sampling/iter_2.png" alt="Iter 2" caption="Iter 2" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/diffusion_model_sampling/iter_3.png" alt="Iter 3" caption="Iter 3" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/diffusion_model_sampling/iter_4.png" alt="Iter 4r" caption="Iter 4" %}
    </div>
</div>
{% enddetails %}
</div>

# Classifier Free Guidance

To improve our images, we can calculate the noise estimation for our timestep with a unconditional classifier, and use that to guide our denoising process.

<!--
assets/img/cs180/proj5/output/cfg_diffusion_sampling/iter_0_upsampled.png
assets/img/cs180/proj5/output/cfg_diffusion_sampling/iter_0.png
assets/img/cs180/proj5/output/cfg_diffusion_sampling/iter_1_upsampled.png
assets/img/cs180/proj5/output/cfg_diffusion_sampling/iter_1.png
assets/img/cs180/proj5/output/cfg_diffusion_sampling/iter_2_upsampled.png
assets/img/cs180/proj5/output/cfg_diffusion_sampling/iter_2.png
assets/img/cs180/proj5/output/cfg_diffusion_sampling/iter_3_upsampled.png
assets/img/cs180/proj5/output/cfg_diffusion_sampling/iter_3.png
assets/img/cs180/proj5/output/cfg_diffusion_sampling/iter_4_upsampled.png
assets/img/cs180/proj5/output/cfg_diffusion_sampling/iter_4.png
-->

<!--
One row each for upsampled and denoised
-->
<div class="row l-page mx-auto">
{% details **Classifier Free Guidance** %}
<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/cfg_diffusion_sampling/iter_0_upsampled.png" alt="Iter 0 Upsampled" caption="Iter 0 Upsampled" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/cfg_diffusion_sampling/iter_0.png" alt="Iter 0" caption="Iter 0" %}
    </div>
</div>
<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/cfg_diffusion_sampling/iter_1_upsampled.png" alt="Iter 1 Upsampled" caption="Iter 1 Upsampled" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/cfg_diffusion_sampling/iter_1.png" alt="Iter 1" caption="Iter 1" %}
    </div>
</div>
<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/cfg_diffusion_sampling/iter_2_upsampled.png" alt="Iter 2 Upsampled" caption="Iter 2 Upsampled" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/cfg_diffusion_sampling/iter_2.png" alt="Iter 2" caption="Iter 2" %}
    </div>
</div>
<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/cfg_diffusion_sampling/iter_3_upsampled.png" alt="Iter 3 Upsampled" caption="Iter 3 Upsampled" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/cfg_diffusion_sampling/iter_3.png" alt="Iter 3" caption="Iter 3" %}
    </div>
</div>
<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/cfg_diffusion_sampling/iter_4_upsampled.png" alt="Iter 4 Upsampled" caption="Iter 4 Upsampled" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/cfg_diffusion_sampling/iter_4.png" alt="Iter 4" caption="Iter 4" %}
    </div>
</div>
{% enddetails %}
</div>

# Image to Image Translation

Now we can take an image, add noise, then denoise it to change the image to a different image.

<div class="row l-page mx-auto">
{% details **Image to Image Translation** %}
<!--
assets/img/cs180/proj5/output/image_to_image_translation/start_index_1.png
assets/img/cs180/proj5/output/image_to_image_translation/start_index_3.png
assets/img/cs180/proj5/output/image_to_image_translation/start_index_5.png
assets/img/cs180/proj5/output/image_to_image_translation/start_index_7.png
assets/img/cs180/proj5/output/image_to_image_translation/start_index_10.png
assets/img/cs180/proj5/output/image_to_image_translation/start_index_20.png
assets/img/cs180/proj5/output/image_to_image_translation/web1_image.png
assets/img/cs180/proj5/output/image_to_image_translation/web1_start_index_1.png
assets/img/cs180/proj5/output/image_to_image_translation/web1_start_index_3.png
assets/img/cs180/proj5/output/image_to_image_translation/web1_start_index_5.png
assets/img/cs180/proj5/output/image_to_image_translation/web1_start_index_7.png
assets/img/cs180/proj5/output/image_to_image_translation/web1_start_index_10.png
assets/img/cs180/proj5/output/image_to_image_translation/web1_start_index_20.png
assets/img/cs180/proj5/output/image_to_image_translation/web2_image.png
assets/img/cs180/proj5/output/image_to_image_translation/web2_start_index_1.png
assets/img/cs180/proj5/output/image_to_image_translation/web2_start_index_3.png
assets/img/cs180/proj5/output/image_to_image_translation/web2_start_index_5.png
assets/img/cs180/proj5/output/image_to_image_translation/web2_start_index_7.png
assets/img/cs180/proj5/output/image_to_image_translation/web2_start_index_10.png
assets/img/cs180/proj5/output/image_to_image_translation/web2_start_index_20.png
-->

<!--
First row, start_index_1 to start_index_20
Second row, web1_image then web1_start_index_1 to web1_start_index_20
Third row, web2_image then web2_start_index_1 to web2_start_index_20
-->
<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/image_to_image_translation/start_index_1.png" alt="Start Index 1" caption="Start Index 1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/image_to_image_translation/start_index_3.png" alt="Start Index 3" caption="Start Index 3" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/image_to_image_translation/start_index_5.png" alt="Start Index 5" caption="Start Index 5" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/image_to_image_translation/start_index_7.png" alt="Start Index 7" caption="Start Index 7" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/image_to_image_translation/start_index_10.png" alt="Start Index 10" caption="Start Index 10" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/image_to_image_translation/start_index_20.png" alt="Start Index 20" caption="Start Index 20" %}
    </div>
</div>
<div class = "row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/image_to_image_translation/web1_image.png" alt="Web1 Image" caption="Web1 Image" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/image_to_image_translation/web1_start_index_1.png" alt="Web1 Start Index 1" caption="Web1 Start Index 1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/image_to_image_translation/web1_start_index_3.png" alt="Web1 Start Index 3" caption="Web1 Start Index 3" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/image_to_image_translation/web1_start_index_5.png" alt="Web1 Start Index 5" caption="Web1 Start Index 5" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/image_to_image_translation/web1_start_index_7.png" alt="Web1 Start Index 7" caption="Web1 Start Index 7" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/image_to_image_translation/web1_start_index_10.png" alt="Web1 Start Index 10" caption="Web1 Start Index 10" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/image_to_image_translation/web1_start_index_20.png" alt="Web1 Start Index 20" caption="Web1 Start Index 20" %}
    </div>
</div>
<div class = "row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/image_to_image_translation/web2_image.png" alt="Web2 Image" caption="Web2 Image" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/image_to_image_translation/web2_start_index_1.png" alt="Web2 Start Index 1" caption="Web2 Start Index 1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/image_to_image_translation/web2_start_index_3.png" alt="Web2 Start Index 3" caption="Web2 Start Index 3" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/image_to_image_translation/web2_start_index_5.png" alt="Web2 Start Index 5" caption="Web2 Start Index 5" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/image_to_image_translation/web2_start_index_7.png" alt="Web2 Start Index 7" caption="Web2 Start Index 7" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/image_to_image_translation/web2_start_index_10.png" alt="Web2 Start Index 10" caption="Web2 Start Index 10" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/image_to_image_translation/web2_start_index_20.png" alt="Web2 Start Index 20" caption
        ="Web2 Start Index 20" %}
    </div>
</div>
{% enddetails %}
</div>

# Hand Drawn and Web Images

Let us do the same for hand drawn images and web images.

<!--
assets/img/cs180/proj5/output/hand_and_web_editing/draw1_image.png
assets/img/cs180/proj5/output/hand_and_web_editing/draw1_start_index_1_noisy.png
assets/img/cs180/proj5/output/hand_and_web_editing/draw1_start_index_1_upsampled.png
assets/img/cs180/proj5/output/hand_and_web_editing/draw1_start_index_1.png
assets/img/cs180/proj5/output/hand_and_web_editing/draw1_start_index_3_noisy.png
assets/img/cs180/proj5/output/hand_and_web_editing/draw1_start_index_3_upsampled.png
assets/img/cs180/proj5/output/hand_and_web_editing/draw1_start_index_3.png
assets/img/cs180/proj5/output/hand_and_web_editing/draw1_start_index_5_noisy.png
assets/img/cs180/proj5/output/hand_and_web_editing/draw1_start_index_5_upsampled.png
assets/img/cs180/proj5/output/hand_and_web_editing/draw1_start_index_5.png
assets/img/cs180/proj5/output/hand_and_web_editing/draw1_start_index_7_noisy.png
assets/img/cs180/proj5/output/hand_and_web_editing/draw1_start_index_7_upsampled.png
assets/img/cs180/proj5/output/hand_and_web_editing/draw1_start_index_7.png
assets/img/cs180/proj5/output/hand_and_web_editing/draw1_start_index_10_noisy.png
assets/img/cs180/proj5/output/hand_and_web_editing/draw1_start_index_10_upsampled.png
assets/img/cs180/proj5/output/hand_and_web_editing/draw1_start_index_10.png
assets/img/cs180/proj5/output/hand_and_web_editing/draw1_start_index_20_noisy.png
assets/img/cs180/proj5/output/hand_and_web_editing/draw1_start_index_20_upsampled.png
assets/img/cs180/proj5/output/hand_and_web_editing/draw1_start_index_20.png
assets/img/cs180/proj5/output/hand_and_web_editing/draw2_image.png
assets/img/cs180/proj5/output/hand_and_web_editing/draw2_start_index_1_noisy.png
assets/img/cs180/proj5/output/hand_and_web_editing/draw2_start_index_1_upsampled.png
assets/img/cs180/proj5/output/hand_and_web_editing/draw2_start_index_1.png
assets/img/cs180/proj5/output/hand_and_web_editing/draw2_start_index_3_noisy.png
assets/img/cs180/proj5/output/hand_and_web_editing/draw2_start_index_3_upsampled.png
assets/img/cs180/proj5/output/hand_and_web_editing/draw2_start_index_3.png
assets/img/cs180/proj5/output/hand_and_web_editing/draw2_start_index_5_noisy.png
assets/img/cs180/proj5/output/hand_and_web_editing/draw2_start_index_5_upsampled.png
assets/img/cs180/proj5/output/hand_and_web_editing/draw2_start_index_5.png
assets/img/cs180/proj5/output/hand_and_web_editing/draw2_start_index_7_noisy.png
assets/img/cs180/proj5/output/hand_and_web_editing/draw2_start_index_7_upsampled.png
assets/img/cs180/proj5/output/hand_and_web_editing/draw2_start_index_7.png
assets/img/cs180/proj5/output/hand_and_web_editing/draw2_start_index_10_noisy.png
assets/img/cs180/proj5/output/hand_and_web_editing/draw2_start_index_10_upsampled.png
assets/img/cs180/proj5/output/hand_and_web_editing/draw2_start_index_10.png
assets/img/cs180/proj5/output/hand_and_web_editing/draw2_start_index_20_noisy.png
assets/img/cs180/proj5/output/hand_and_web_editing/draw2_start_index_20_upsampled.png
assets/img/cs180/proj5/output/hand_and_web_editing/draw2_start_index_20.png
assets/img/cs180/proj5/output/hand_and_web_editing/web_image.png
assets/img/cs180/proj5/output/hand_and_web_editing/web_start_index_1_noisy.png
assets/img/cs180/proj5/output/hand_and_web_editing/web_start_index_1_upsampled.png
assets/img/cs180/proj5/output/hand_and_web_editing/web_start_index_1.png
assets/img/cs180/proj5/output/hand_and_web_editing/web_start_index_3_noisy.png
assets/img/cs180/proj5/output/hand_and_web_editing/web_start_index_3_upsampled.png
assets/img/cs180/proj5/output/hand_and_web_editing/web_start_index_3.png
assets/img/cs180/proj5/output/hand_and_web_editing/web_start_index_5_noisy.png
assets/img/cs180/proj5/output/hand_and_web_editing/web_start_index_5_upsampled.png
assets/img/cs180/proj5/output/hand_and_web_editing/web_start_index_5.png
assets/img/cs180/proj5/output/hand_and_web_editing/web_start_index_7_noisy.png
assets/img/cs180/proj5/output/hand_and_web_editing/web_start_index_7_upsampled.png
assets/img/cs180/proj5/output/hand_and_web_editing/web_start_index_7.png
assets/img/cs180/proj5/output/hand_and_web_editing/web_start_index_10_noisy.png
assets/img/cs180/proj5/output/hand_and_web_editing/web_start_index_10_upsampled.png
assets/img/cs180/proj5/output/hand_and_web_editing/web_start_index_10.png
assets/img/cs180/proj5/output/hand_and_web_editing/web_start_index_20_noisy.png
assets/img/cs180/proj5/output/hand_and_web_editing/web_start_index_20_upsampled.png
assets/img/cs180/proj5/output/hand_and_web_editing/web_start_index_20.png
-->

<!--
First show web_image.
Next row, web_start_index_1_noisy to web_start_index_20_noisy
Next row, web_start_index_1.png to web_start_index_20.png
Next row, web_start_index_1_upsampled to web_start_index_20_upsampled
Next show draw1_image.
Next row, draw1_start_index_1_noisy to draw1_start_index_20_noisy
Next row, draw1_start_index_1.png to draw1_start_index_20.png
Next row, draw1_start_index_1_upsampled to draw1_start_index_20_upsampled
Next show draw2_image.
Next row, draw2_start_index_1_noisy to draw2_start_index_20_noisy
Next row, draw2_start_index_1.png to draw2_start_index_20.png
Next row, draw2_start_index_1_upsampled to draw2_start_index_20_upsampled
-->
<div class="row l-page mx-auto">
{% details **Hand Drawn and Web Images** %}
<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/hand_and_web_editing/web_image.png" alt="Web Image" caption="Web Image" %}
    </div>
</div>
<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/hand_and_web_editing/web_start_index_1_noisy.png" alt="Web Start Index 1 Noisy" caption="Web Start Index 1 Noisy" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/hand_and_web_editing/web_start_index_3_noisy.png" alt="Web Start Index 3 Noisy" caption="Web Start Index 3 Noisy" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/hand_and_web_editing/web_start_index_5_noisy.png" alt="Web Start Index 5 Noisy" caption="Web Start Index 5 Noisy" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/hand_and_web_editing/web_start_index_7_noisy.png" alt="Web Start Index 7 Noisy" caption="Web Start Index 7 Noisy" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/hand_and_web_editing/web_start_index_10_noisy.png" alt="Web Start Index 10 Noisy" caption="Web Start Index 10 Noisy" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/hand_and_web_editing/web_start_index_20_noisy.png" alt="Web Start Index 20 Noisy" caption="Web Start Index 20 Noisy" %}
    </div>
</div>
<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/hand_and_web_editing/web_start_index_1.png" alt="Web Start Index 1" caption="Web Start Index 1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/hand_and_web_editing/web_start_index_3.png" alt="Web Start Index 3" caption="Web Start Index 3" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/hand_and_web_editing/web_start_index_5.png" alt="Web Start Index 5" caption="Web Start Index 5" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/hand_and_web_editing/web_start_index_7.png" alt="Web Start Index 7" caption="Web Start Index 7" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/hand_and_web_editing/web_start_index_10.png" alt="Web Start Index 10" caption="Web Start Index 10" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/hand_and_web_editing/web_start_index_20.png" alt="Web Start Index 20" caption="Web Start Index 20" %}
    </div>
</div>
<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/hand_and_web_editing/web_start_index_1_upsampled.png" alt="Web Start Index 1 Upsampled" caption="Web Start Index 1 Upsampled" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/hand_and_web_editing/web_start_index_3_upsampled.png" alt="Web Start Index 3 Upsampled" caption="Web Start Index 3 Upsampled" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/hand_and_web_editing/web_start_index_5_upsampled.png" alt="Web Start Index 5 Upsampled" caption="Web Start Index 5 Upsampled" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/hand_and_web_editing/web_start_index_7_upsampled.png" alt="Web Start Index 7 Upsampled" caption="Web Start Index 7 Upsampled" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/hand_and_web_editing/web_start_index_10_upsampled.png" alt="Web Start Index 10 Upsampled" caption="Web Start Index 10 Upsampled" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/hand_and_web_editing/web_start_index_20_upsampled.png" alt="Web Start Index 20 Upsampled" caption="Web Start Index 20 Upsampled" %}
    </div>
</div>
<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/hand_and_web_editing/draw1_image.png" alt="Draw1 Image" caption="Draw1 Image" %}
    </div>
</div>
<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/hand_and_web_editing/draw1_start_index_1_noisy.png" alt="Draw1 Start Index 1 Noisy" caption="Draw1 Start Index 1 Noisy" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/hand_and_web_editing/draw1_start_index_3_noisy.png" alt="Draw1 Start Index 3 Noisy" caption="Draw1 Start Index 3 Noisy" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/hand_and_web_editing/draw1_start_index_5_noisy.png" alt="Draw1 Start Index 5 Noisy" caption="Draw1 Start Index 5 Noisy" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/hand_and_web_editing/draw1_start_index_7_noisy.png" alt="Draw1 Start Index 7 Noisy" caption="Draw1 Start Index 7 Noisy" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/hand_and_web_editing/draw1_start_index_10_noisy.png" alt="Draw1 Start Index 10 Noisy" caption="Draw1 Start Index 10 Noisy" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/hand_and_web_editing/draw1_start_index_20_noisy.png" alt="Draw1 Start Index 20 Noisy" caption="Draw1 Start Index 20 Noisy" %}
    </div>
</div>
<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/hand_and_web_editing/draw1_start_index_1.png" alt="Draw1 Start Index 1" caption="Draw1 Start Index 1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/hand_and_web_editing/draw1_start_index_3.png" alt="Draw1 Start Index 3" caption="Draw1 Start Index 3" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/hand_and_web_editing/draw1_start_index_5.png" alt="Draw1 Start Index 5" caption="Draw1 Start Index 5" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/hand_and_web_editing/draw1_start_index_7.png" alt="Draw1 Start Index 7" caption="Draw1 Start Index 7" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/hand_and_web_editing/draw1_start_index_10.png" alt="Draw1 Start Index 10" caption="Draw1 Start Index 10" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/hand_and_web_editing/draw1_start_index_20.png" alt="Draw1 Start Index 20" caption="Draw1 Start Index 20" %}
    </div>
</div>
<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/hand_and_web_editing/draw1_start_index_1_upsampled.png" alt="Draw1 Start Index 1 Upsampled" caption="Draw1 Start Index 1 Upsampled" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/hand_and_web_editing/draw1_start_index_3_upsampled.png" alt="Draw1 Start Index 3 Upsampled" caption="Draw1 Start Index 3 Upsampled" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/hand_and_web_editing/draw1_start_index_5_upsampled.png" alt="Draw1 Start Index 5 Upsampled" caption="Draw1 Start Index 5 Upsampled" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/hand_and_web_editing/draw1_start_index_7_upsampled.png" alt="Draw1 Start Index 7 Upsampled" caption="Draw1 Start Index 7 Upsampled" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/hand_and_web_editing/draw1_start_index_10_upsampled.png" alt="Draw1 Start Index 10 Upsampled" caption="Draw1 Start Index 10 Upsampled" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/hand_and_web_editing/draw1_start_index_20_upsampled.png" alt="Draw1 Start Index 20 Upsampled" caption="Draw1 Start Index 20 Upsampled" %}
    </div>
</div>
<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/hand_and_web_editing/draw2_image.png" alt="Draw2 Image" caption="Draw2 Image" %}
    </div>
</div>
<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/hand_and_web_editing/draw2_start_index_1_noisy.png" alt="Draw2 Start Index 1 Noisy" caption="Draw2 Start Index 1 Noisy" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/hand_and_web_editing/draw2_start_index_3_noisy.png" alt="Draw2 Start Index 3 Noisy" caption="Draw2 Start Index 3 Noisy" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/hand_and_web_editing/draw2_start_index_5_noisy.png" alt="Draw2 Start Index 5 Noisy" caption="Draw2 Start Index 5 Noisy" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/hand_and_web_editing/draw2_start_index_7_noisy.png" alt="Draw2 Start Index 7 Noisy" caption="Draw2 Start Index 7 Noisy" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/hand_and_web_editing/draw2_start_index_10_noisy.png" alt="Draw2 Start Index 10 Noisy" caption="Draw2 Start Index 10 Noisy" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/hand_and_web_editing/draw2_start_index_20_noisy.png" alt="Draw2 Start Index 20 Noisy" caption="Draw2 Start Index 20 Noisy" %}
    </div>
</div>
<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/hand_and_web_editing/draw2_start_index_1.png" alt="Draw2 Start Index 1" caption="Draw2 Start Index 1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/hand_and_web_editing/draw2_start_index_3.png" alt="Draw2 Start Index 3" caption="Draw2 Start Index 3" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/hand_and_web_editing/draw2_start_index_5.png" alt="Draw2 Start Index 5" caption="Draw2 Start Index 5" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/hand_and_web_editing/draw2_start_index_7.png" alt="Draw2 Start Index 7" caption="Draw2 Start Index 7" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/hand_and_web_editing/draw2_start_index_10.png" alt="Draw2 Start Index 10" caption="Draw2 Start Index 10" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/hand_and_web_editing/draw2_start_index_20.png" alt="Draw2 Start Index 20" caption="Draw2 Start Index 20" %}
    </div>
</div>
<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/hand_and_web_editing/draw2_start_index_1_upsampled.png" alt="Draw2 Start Index 1 Upsampled" caption="Draw2 Start Index 1 Upsampled" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/hand_and_web_editing/draw2_start_index_3_upsampled.png" alt="Draw2 Start Index 3 Upsampled" caption="Draw2 Start Index 3 Upsampled" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/hand_and_web_editing/draw2_start_index_5_upsampled.png" alt="Draw2 Start Index 5 Upsampled" caption="Draw2 Start Index 5 Upsampled" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/hand_and_web_editing/draw2_start_index_7_upsampled.png" alt="Draw2 Start Index 7 Upsampled" caption="Draw2 Start Index 7 Upsampled" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/hand_and_web_editing/draw2_start_index_10_upsampled.png" alt="Draw2 Start Index 10 Upsampled" caption="Draw2 Start Index 10 Upsampled" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/hand_and_web_editing/draw2_start_index_20_upsampled.png" alt="Draw2 Start Index 20 Upsampled" caption="Draw2 Start Index 20 Upsampled" %}
    </div>
</div>
{% enddetails %}
</div>

# Inpainting

Now if we noise a part of the image and try to inpaint it, we get the following results.

<!--
assets/img/cs180/proj5/output/inpainting/inpainted_upsampled.png
assets/img/cs180/proj5/output/inpainting/inpainted.png
assets/img/cs180/proj5/output/inpainting/mask.png
assets/img/cs180/proj5/output/inpainting/to_replace.png
assets/img/cs180/proj5/output/inpainting/web1_image.png
assets/img/cs180/proj5/output/inpainting/web1_inpainted_upsampled.png
assets/img/cs180/proj5/output/inpainting/web1_inpainted.png
assets/img/cs180/proj5/output/inpainting/web2_image.png
assets/img/cs180/proj5/output/inpainting/web2_inpainted_upsampled.png
assets/img/cs180/proj5/output/inpainting/web2_inpainted.png
-->

<!--
First show to_replace.png and mask.png.
Next row, inpainted.png and inpainted_upsampled.png
Next row, web1_image.png, web1_inpainted.png and web1_inpainted_upsampled.png
Next row, web2_image.png, web2_inpainted.png and web2_inpainted_upsampled.png
-->

<div class="row l-page mx-auto">
{% details **Inpainting** %}
<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/inpainting/to_replace.png" alt="To Replace" caption="To Replace" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/inpainting/mask.png" alt="Mask" caption="Mask" %}
    </div>
</div>
<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/inpainting/inpainted.png" alt="Inpainted" caption="Inpainted" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/inpainting/inpainted_upsampled.png" alt="Inpainted Upsampled" caption="Inpainted Upsampled" %}
    </div>
</div>
<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/inpainting/web1_image.png" alt="Web1 Image" caption="Web1 Image" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/inpainting/web1_inpainted.png" alt="Web1 Inpainted" caption="Web1 Inpainted" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/inpainting/web1_inpainted_upsampled.png" alt="Web1 Inpainted Upsampled" caption="Web1 Inpainted Upsampled" %}
    </div>
</div>
<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/inpainting/web2_image.png" alt="Web2 Image" caption="Web2 Image" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/inpainting/web2_inpainted.png" alt="Web2 Inpainted" caption="Web2 Inpainted" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/inpainting/web2_inpainted_upsampled.png" alt="Web2 Inpainted Upsampled" caption="Web2 Inpainted Upsampled" %}
    </div>
</div>
{% enddetails %}
</div>

# Text Conditioned Image to Image Translation

Now let us add some prompt embeddings while denoising these sections.

<!--
assets/img/cs180/proj5/output/text_conditioned_image_to_image/test_image_start_index_1.png
assets/img/cs180/proj5/output/text_conditioned_image_to_image/test_image_start_index_3.png
assets/img/cs180/proj5/output/text_conditioned_image_to_image/test_image_start_index_5.png
assets/img/cs180/proj5/output/text_conditioned_image_to_image/test_image_start_index_7.png
assets/img/cs180/proj5/output/text_conditioned_image_to_image/test_image_start_index_10.png
assets/img/cs180/proj5/output/text_conditioned_image_to_image/test_image_start_index_20.png
assets/img/cs180/proj5/output/text_conditioned_image_to_image/web1_image.png
assets/img/cs180/proj5/output/text_conditioned_image_to_image/web1_start_index_1.png
assets/img/cs180/proj5/output/text_conditioned_image_to_image/web1_start_index_3.png
assets/img/cs180/proj5/output/text_conditioned_image_to_image/web1_start_index_5.png
assets/img/cs180/proj5/output/text_conditioned_image_to_image/web1_start_index_7.png
assets/img/cs180/proj5/output/text_conditioned_image_to_image/web1_start_index_10.png
assets/img/cs180/proj5/output/text_conditioned_image_to_image/web1_start_index_20.png
assets/img/cs180/proj5/output/text_conditioned_image_to_image/web2_image.png
assets/img/cs180/proj5/output/text_conditioned_image_to_image/web2_start_index_1.png
assets/img/cs180/proj5/output/text_conditioned_image_to_image/web2_start_index_3.png
assets/img/cs180/proj5/output/text_conditioned_image_to_image/web2_start_index_5.png
assets/img/cs180/proj5/output/text_conditioned_image_to_image/web2_start_index_7.png
assets/img/cs180/proj5/output/text_conditioned_image_to_image/web2_start_index_10.png
assets/img/cs180/proj5/output/text_conditioned_image_to_image/web2_start_index_20.png
-->

<!--
First show test_image_start_index_1.png to test_image_start_index_20.png
Next row show web1_image.png then web1_start_index_1.png to web1_start_index_20.png
Next row show web2_image.png then web2_start_index_1.png to web2_start_index_20.png
-->

<div class="row l-page mx-auto">
{% details **Text Conditioned Image to Image Translation** %}
<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/text_conditioned_image_to_image/test_image_start_index_1.png" alt="Test Image Start Index 1" caption="Test Image Start Index 1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/text_conditioned_image_to_image/test_image_start_index_3.png" alt="Test Image Start Index 3" caption="Test Image Start Index 3" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/text_conditioned_image_to_image/test_image_start_index_5.png" alt="Test Image Start Index 5" caption="Test Image Start Index 5" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/text_conditioned_image_to_image/test_image_start_index_7.png" alt="Test Image Start Index 7" caption="Test Image Start Index 7" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/text_conditioned_image_to_image/test_image_start_index_10.png" alt="Test Image Start Index 10" caption="Test Image Start Index 10" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/text_conditioned_image_to_image/test_image_start_index_20.png" alt="Test Image Start Index 20" caption="Test Image Start Index 20" %}
    </div>
</div>
<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/text_conditioned_image_to_image/web1_image.png" alt="Web1 Image" caption="Web1 Image" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/text_conditioned_image_to_image/web1_start_index_1.png" alt="Web1 Start Index 1" caption="Web1 Start Index 1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/text_conditioned_image_to_image/web1_start_index_3.png" alt="Web1 Start Index 3" caption="Web1 Start Index 3" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/text_conditioned_image_to_image/web1_start_index_5.png" alt="Web1 Start Index 5" caption="Web1 Start Index 5" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/text_conditioned_image_to_image/web1_start_index_7.png" alt="Web1 Start Index 7" caption="Web1 Start Index 7" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/text_conditioned_image_to_image/web1_start_index_10.png" alt="Web1 Start Index 10" caption="Web1 Start Index 10" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/text_conditioned_image_to_image/web1_start_index_20.png" alt="Web1 Start Index 20" caption="Web1 Start Index 20" %}
    </div>
</div>
<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/text_conditioned_image_to_image/web2_image.png" alt="Web2 Image" caption="Web2 Image" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/text_conditioned_image_to_image/web2_start_index_1.png" alt="Web2 Start Index 1" caption="Web2 Start Index 1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/text_conditioned_image_to_image/web2_start_index_3.png" alt="Web2 Start Index 3" caption="Web2 Start Index 3" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/text_conditioned_image_to_image/web2_start_index_5.png" alt="Web2 Start Index 5" caption="Web2 Start Index 5" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/text_conditioned_image_to_image/web2_start_index_7.png" alt="Web2 Start Index 7" caption="Web2 Start Index 7" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/text_conditioned_image_to_image/web2_start_index_10.png" alt="Web2 Start Index 10" caption="Web2 Start Index 10" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/text_conditioned_image_to_image/web2_start_index_20.png" alt="Web2 Start Index 20" caption="Web2 Start Index 20" %}
    </div>
</div>
{% enddetails %}
</div>

# Visual Anagrams

By using the prompt embeddings and some flipping, we can generate visual anagrams by combining our noise estimates!

<!--
assets/img/cs180/proj5/output/visual_anagrams/anagram_amalfi_man.png
assets/img/cs180/proj5/output/visual_anagrams/anagram_old_man_campfire.png
assets/img/cs180/proj5/output/visual_anagrams/anagram_waterfalls_skull.png
-->

<div class="row l-page mx-auto">
{% details **Visual Anagrams** %}
<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/visual_anagrams/anagram_amalfi_man.png" alt="An anagram of the amalfi coast and a man" caption="Amalfi Coast and Man" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/visual_anagrams/anagram_old_man_campfire.png" alt="An anagram of an old man and a campfire" caption="Old Man and Campfire" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/visual_anagrams/anagram_waterfalls_skull.png" alt="An anagram of waterfalls and a skull" caption="Waterfalls and Skull" %}
    </div>
</div>
{% enddetails %}
</div>

# Hybrid Images

Finally, we can create hybrid images by combining the low-frequency components of one image with the high-frequency components of another image.

<!--
assets/img/cs180/proj5/output/hybrids/hybrid_dog_amalfi.png
assets/img/cs180/proj5/output/hybrids/hybrid_old_man_amalfi.png
assets/img/cs180/proj5/output/hybrids/hybrid_waterfalls_skull.png
-->

<div class="row l-page mx-auto">
{% details **Hybrid Images** %}
<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/hybrids/hybrid_dog_amalfi.png" alt="Hybrid of a dog and the Amalfi coast" caption="Dog and Amalfi Coast" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/hybrids/hybrid_old_man_amalfi.png" alt="Hybrid of an old man and the Amalfi coast" caption="Old Man and Amalfi Coast" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/hybrids/hybrid_waterfalls_skull.png" alt="Hybrid of waterfalls and a skull" caption="Waterfalls and Skull" %}
    </div>
</div>
{% enddetails %}
</div>

# Bells and Whistles

I wanted to design a course logo, so I generated prompt embeddings for a photo of an eye, and a photo of a cpu, and generated a hybrid image of the two. The result is shown below.

<!--
assets/img/cs180/proj5/output/b_w/class_logo.png
-->

<div class="row l-page mx-auto">
{% details **Bells and Whistles** %}
<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/b_w/class_logo.png" alt="Class Logo" caption="Class Logo" %}
    </div>
</div>
{% enddetails %}
</div>

# Training a Single Step Denoising Model

Now we can attempt to train a single step denoising model, we will be using the MNIST dataset for this task.

<!--
assets/img/cs180/proj5/output/1_2_1/denoising_results_epoch_1.png
assets/img/cs180/proj5/output/1_2_1/denoising_results_epoch_5.png
assets/img/cs180/proj5/output/1_2_1/loss_over_time.png
-->
<!--
First show the loss_over_time.png
Next row, denoising_results_epoch_1.png and denoising_results_epoch_5.png
-->

<div class="row l-page mx-auto">
{% details **Training a Single Step Denoising Model** %}
<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/1_2_1/loss_over_time.png" alt="Loss Over Time" caption="Loss Over Time" %}
    </div>
</div>
<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/1_2_1/denoising_results_epoch_1.png" alt="Denoising Results Epoch 1" caption="Denoising Results Epoch 1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/1_2_1/denoising_results_epoch_5.png" alt="Denoising Results Epoch 5" caption="Denoising Results Epoch 5" %}
    </div>
</div>
{% enddetails %}
</div>

The results are pretty good, however what happens when we give the model an image with noise levels that it was not trained on?

<!--
assets/img/cs180/proj5/output/1_2_2/out_of_distribution_testing.png
-->

<div class="row l-page mx-auto">
{% details **Out of Distribution Testing** %}
<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/1_2_2/out_of_distribution_testing.png" alt="Out of Distribution Testing" caption="Out of Distribution Testing" %}
    </div>
</div>
{% enddetails %}
</div>
That was not as good. Let us see if we can improve the model.

# Training a Diffusion Model

Now we will train a UNet to iteratively denoise the image, we will be using the MNIST dataset for this task.

<!--
assets/img/cs180/proj5/output/2_2/intermediate_frames_epoch_5.gif
assets/img/cs180/proj5/output/2_2/intermediate_frames_epoch_20.gif
assets/img/cs180/proj5/output/2_2/loss_over_time.png
assets/img/cs180/proj5/output/2_2/samples_epoch_5.png
assets/img/cs180/proj5/output/2_2/samples_epoch_20.png
-->

<!--
First show the loss_over_time.png
Next row, samples_epoch_5.png and samples_epoch_20.png
Next row, intermediate_frames_epoch_5.gif and intermediate_frames_epoch_20.gif
-->

<div class="row l-page mx-auto">
{% details **Training a Diffusion Model** %}
<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/2_2/loss_over_time.png" alt="Loss Over Time" caption="Loss Over Time" %}
    </div>
</div>
<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/2_2/samples_epoch_5.png" alt="Samples Epoch 5" caption="Samples Epoch 5" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/2_2/samples_epoch_20.png" alt="Samples Epoch 20" caption="Samples Epoch 20" %}
    </div>
</div>
<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/2_2/intermediate_frames_epoch_5.gif" alt="Intermediate Frames Epoch 5" caption="Intermediate Frames Epoch 5" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/2_2/intermediate_frames_epoch_20.gif" alt="Intermediate Frames Epoch 20" caption="Intermediate Frames Epoch 20" %}
    </div>
</div>
{% enddetails %}
</div>

The results are much better than the single step model, however we cannot control what number class the model generates. Let us see if we can improve the model.

# Training a Class Conditional Diffusion Model

Now we will provide the model with the class label one-hot encoded, and perform the same task as before.

<!--
assets/img/cs180/proj5/output/2_5/intermediate_frames_epoch_5.gif
assets/img/cs180/proj5/output/2_5/intermediate_frames_epoch_20.gif
assets/img/cs180/proj5/output/2_5/loss_over_time.png
assets/img/cs180/proj5/output/2_5/samples_epoch_5.png
assets/img/cs180/proj5/output/2_5/samples_epoch_20.png
-->

<!--
First show the loss_over_time.png
Next row, samples_epoch_5.png and samples_epoch_20.png
Next row, intermediate_frames_epoch_5.gif and intermediate_frames_epoch_20.gif
-->

<div class="row l-page mx-auto">
{% details **Training a Class Conditional Diffusion Model** %}
<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/2_5/loss_over_time.png" alt="Loss Over Time" caption="Loss Over Time" %}
    </div>
</div>
<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/2_5/samples_epoch_5.png" alt="Samples Epoch 5" caption="Samples Epoch 5" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/2_5/samples_epoch_20.png" alt="Samples Epoch 20" caption="Samples Epoch 20" %}
    </div>
</div>
<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/2_5/intermediate_frames_epoch_5.gif" alt="Intermediate Frames Epoch 5" caption="Intermediate Frames Epoch 5" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj5/output/2_5/intermediate_frames_epoch_20.gif" alt="Intermediate Frames Epoch 20" caption="Intermediate Frames Epoch 20" %}
    </div>
</div>
{% enddetails %}
</div>
