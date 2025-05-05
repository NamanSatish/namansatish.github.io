---
layout: distill
title: "Final Report, Apollo: Augmenting Light Functions to Simulate Wave-Based Diffraction"
description: "Final Project in CS184 : Foundations of Computer Graphics"
img: assets/img/cs184/final/render.gif
importance: 13
date: 2025-05-04
category: school
bibliography: 184final.bib
toc:
  - name: "Abstract"
  - name: "Initial Analysis"
  - name: "Technical Approach"
  - name: "Implementation"
  - name: "Problems Encountered"
  - name: "Learning Outcomes"
  - name: "Results"
  - name: "Goals"
  - name: "References"
  - name: "Contributions"
authors:
  - name: Cashus Karladan Puhvel
    affiliations:
      name: UC Berkeley
  - name: Kevin Tseng
    affiliations:
      name: UC Berkeley
  - name: Naman Satish
    affiliations:
      name: UC Berkeley
  - name: Nicholas Dang-Dat Tran
    affiliations:
      name: UC Berkeley
_styles: >
  .fake-img {
    background: #bbb;
    border: 1px solid rgba(51, 48, 215, 0.1);
    box-shadow: 0 0px 4px rgba(0, 0, 0, 0.1);
    margin-bottom: 12px;
  }
  .fake-img p {
    font-family: monospace;
    color: white;
    text-align: left;
    margin: 12px 0;
    text-align: center;
    font-size: 16px;
  }
  #inspirational_quote {
    font-style: italic;
    color: #555;
    text-align: center;
    margin: 20px 0;
    /* background-color: #f9f9f9; */
  }
---

**Team Number 54**

**Final Report** : [https://satish.dev/projects/184_final/](https://satish.dev/projects/184_final/)

**Final Video** : [https://youtu.be/q\_\_vekWq_io](https://youtu.be/q__vekWq_io)

**Final Presentation** : [Canva](https://www.canva.com/design/DAGmJ78nsGk/yrQAxFzjsaKED1NO3l5eig/view?utm_content=DAGmJ78nsGk&utm_campaign=designshare&utm_medium=link2&utm_source=uniquelinks&utlId=hc64ecb1ad7)

**Github Repo** : [Github](https://github.com/NamanSatish/cs184_apollo/)

---

**Milestone** : https://satish.dev/projects/184_milestone/

**Slides** : https://docs.google.com/presentation/d/1Xtp83b7pyEKYRISCwhU9ZiQJkb9-J2A-jG7GtQ50qKQ/edit?usp=sharing

**Video Link**: https://youtu.be/FEWRREKrkVo

---

**Proposal Link** : https://satish.dev/projects/184_proposal/

# Abstract

In this project, we explore the simulation of light diffraction using a pathtracer. We implement a method to simulate Young's double-slit experiment as well as shaped aperture diffraction patterns. Our approach is based on the paper "Rendering Wave Effects with Augmented Light Field"<d-cite key="oh2010renderingwaveeffects"></d-cite> and "Augmenting Light Field to model Wave Optics effects" <d-cite key="oh2009augmentinglightfieldmodel"></d-cite> by Se Baek Oh et al. The paper proposes a method to augment the classic light field with "virtual light projectors" which can create variable radiance values, including negative radiance. We extend the pathtracer to support these virtual light projectors, allowing us to simulate diffraction patterns and interference effects.

We also modified the pathtracer to perform a full wave simulation of rays, allowing us to calculate the phase of each ray based on its wavelength, and switching from a scalar radiance summation to a complex amplitude summation. This enables us to accurately simulate the interference patterns produced by multiple light sources, without the use of virtual light projectors.

Our results show that the pathtracer is capable of accurately simulating these phenomena, providing a powerful tool for exploring the wave nature of light.

<div class="row mx-auto" style="text-align: center;">
  <div class="col-sm mt-3 mt-md-0" id="inspirational_quote">
    “[The double-slit experiment] has in it the heart of quantum mechanics. In reality, it contains the only mystery. —Richard Feynman
  </div>
</div>

# Initial Analysis

We began with an analysis of the pathtracer from the end of Homework 3, testing out some of the features that we would need to implement and seeing how they rendered prior to implementation. This exposed some unique intricacies of the pathtracer that we did not initially expect!

To set up the basis for our experimentation, we constructed a double slit experiment in Blender using 3 planes (2 long skinny ZY planes mirroring a short ZY plane in the Y-Axis) to test the interference patterns of our 3 differently valued lights. The lights were arranged in a horizontal line and from left to right, were applied with light strengths of 1, -0.3, and 0.1.

</div>

The closer darker green rectangle is our slit plane, and the background with the colors is a plane further in the X-Axis to capture their interference patterns. The variety of colors that was reflected on the wall was very interesting and not initially expected. While negative radiance is not a property of normal physics, the pathtracer handled negative lights in an understandable way, subtracting the contribution instead of adding. With the knowledge that there was no deeply embedded prevention of negative radiances, we knew we were on a good path to implementing our diffraction simulation.

<div class="container l-page">
    <div class="row align-items-center text-center">
        <div class="col">
            {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs184/final/slit_colors.png" alt="Slit Colors" %}
            <div class="caption">Initial Slit Coloring</div>
        </div>
        <div class="col">
            {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs184/final/slit_blender.png" alt="Slit Blender" %}
            <div class="caption">Blender Slit Setup</div>
        </div>
        <div class="col">
            {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs184/final/negative_0p3_1.png" alt="Negative 0.3 1" %}
            <div class="caption">View 1</div>
        </div>
      </div>
      <div class="row align-items-center text-center">
        <div class="col">
            {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs184/final/negative_0p3_2.png" alt="Negative 0.3 2" %}
            <div class="caption">View 2</div>
        </div>
        <div class="col">
            {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs184/final/negative_0p3_3.png" alt="Negative 0.3 3" %}
            <div class="caption">View 3</div>
        </div>
    </div>
</div>

# Technical Approach

## Introduction

Upon reading both papers, we realized that the simulation of diffraction was not as simple as we had initially thought. Our normal pathtracer treats light as incoherent, "a superposition of infinite numbers of plane waves with random phase delays" <d-cite key="oh2009augmentinglightfieldmodel"></d-cite>. This means that the light reaches the surface with completely random phase delays, and thus the light rays do not interfere with each other. This is a good approximation for most cases, but it does not accurately capture the wave nature of light.

In order to simulate interference patterns, we need to understand the coherence of light: the property of light rays that enables them to interfere with each other. The most intuitive way to represent this in our pathtracer was to use point sources, which can be treated as a coherent light emitter, where each wavefront from the source shares the same phase.

Our implementation in the pathtracer consists of two complementary modes of simulating diffraction and interference, a wave based integrator and an augmented light field with negative lights. There is a flag in the program to select between both branches. Both strategies rely on similar ideas of computing the wave intensity equation, but how they approximate the physical complexities of light through apertures and over distances is different.

In our classic Light Field, we project the u-axis down onto X-Axis to compute the intensity at that location. As we can see, in the WDF, there is a 3rd "virtual projector" that contains a changing phase value, which when projected down onto the X-Axis at the screen, does not give us a constant intensity value, but rather a periodic function that oscillates between 0 and a maximum value. The "virtual projector" is a point source that emits light with radiance values that mimic this periodic function, while our wave based integrator calculates the phase information to compute the intensity at each point in space.

<div class="container l-page">
    <div class="row align-items-center text-center">
        <div class="col">
            {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs184/final/WDFvsLF.png" alt="WDFvsLF" %}
            <div class="caption">WDF vs LF</div>
        </div>
        <div class="col">
            {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs184/final/WDF_Wavefront.png" alt="WDF_Wavefront" %}
            <div class="caption">WDF of Wavefront</div>
        </div>
    </div>
</div>

## Implementation

The implementation of the wave based integrator and the augmented light field with negative lights was done in the `pathtracer.cpp` file. The most impactful changes were made in the `PathTracer::estimate_direct_lighting_importance` function, where we compute the radiance of our ray-scene intersection.

When `waveMode == true`, each ray carries a complex amplitude instead of a radiance value. After intersecting with the scene, we initialize two accumulators to store the real and imaginary components of the electric field at the camera pixel. The radiance is then converted into field amplitude `A = L_i * amplitude_scaling`, where `amplitude_scaling` is user-defined to reveal the diffraction pattern. The wave number `k = 2π/λ` is computed by reading the light’s wavelength in nanometers with `light->nanometer()`, scaling it into scene units with `nanometer_scaling`, and multiplying by `M_PI`. The accumulated optical phase for propagation is then `φ = k * distToLight`. We then split this into real and imaginary contributions using Euler's formula, which initializes `E_re = A * cos(φ) * (invR)` and `E_im = A * sin(φ) * (invR)`. Although E decays as 1/r, we hold `invR` at 1 for image demonstrations. To integrate over all paths, we weight each field sample by the BSDF, geometry term, and PDF. After summing `accum_re` and `accum_im` across all lights and samples, the final radiance output is reconstructed as `I = |E|² = re² + im²` per color channel. This direct phase accumulation yields high-fidelity and fast rendering fringes that respect both Fresnel and Fraunhofer regimes.

When `waveMode == false`, the code falls back to an ALF-style convolution using “negative” lights to create the interference patterns. First, in our scene creation, we create negative lights between every pair of positive lights. This is done by injecting additional `SceneObject::PointLights` into the lights used to render the scene. These Virtual Projectors together represent an approximation of the WDF. In the double pinhole case, this approximation is accurate, but requires high density for approximations when mimicking apertures. In this branch if `light->is_negative()` returns true, the code treats that sample as a virtual projector. The virtual projector’s radiance is calculated by extending the 1D term from the paper `2cos(2π[a-b](θ/λ)`<d-cite key="oh2009augmentinglightfieldmodel"></d-cite> to 3 dimensions. It first computes `δ = dot(light->diff(), w_in)`, where `light->diff()` encodes the vector between the two real lights the virtual projector is between. Multiplying by the wave number `k` yields the phase difference between rays passing through each slit (`phase = k * δ`). The amplitude modulation term `mod = 2·cos(phase)` results in a variable modulation to our applied radiance. We then form a signed radiance vector `Lm = L_i * mod * amplitude_scaling` and subtract it (`-Lm`) in the outgoing sum, effectively carving out the occluded geometry while leaving the diffracted wave intact.

## Problems Encountered

The scenes we chose to model (double slit and various shaped apertures), were chosen both as a goal for us to render but additionally as feasible with both our wave based accumulator and our virtual projectors. We encountered issues with understanding the complex nature of light, and the challenges the paper was trying to overcome. In our initial attempt, we directly implemented the wave accumulator and defined our virtual projectors in Blender, which worked for simple scenes. Upon reflection at 3AM on Sunday, a difference was noted with our double slit pattern and the actual solution, and when removing the virtual projector, the correct pattern emerged. This was an indication that we had incorrectly understood the significance of the virtual projector, and upon further examination we were able to change more sections of the codebase to implement the ideas of the virtual projector and retain our wave accumulator to compare between both patterns. We found they had extremely similar solutions, which differed based on our modifications to nanometer scaling or amplitude scaling (which in the ALF increased contrast, and increased overall light in Wave based). Unfortunately, the ALF did not offer any speed improvements for us, as the number of virtual projectors quadratically increased with the higher densities required for aperture diffraction. However, the overall quality of the diffraction pattern was significantly better and much more vibrant thanks to the amplitude scaling affecting the contrast.

There were many challenges in injecting our virtual projectors as well, requiring us to step between the DAE parsing and SceneObject creation to insert new objects. Unfortunately, certain classes were very similarly named creating difficulty in understanding initially the flow of information to create the scene. After a few hours, the entire process was understood and additional variables were added to keep track of necessary parameters for the Augmented Light Field’s virtual projectors and the nanometer of the light.

The difficulty of the project prevented us from going further in the ALF implementation, however we did attempt to implement wave-based BSDF sampling as described in <d-cite key="cuypers2011raybaseddiffraction"></d-cite>. This was a very interesting idea which built atop ALF, but we were unable to implement it in time.

## Learning Outcomes

Overall, these code additions faithfully implement light diffraction within the standard path-tracing framework. We learned a lot about the nature of light and the complexities of simulating it and the numerous ways to approach the problem. Extending the scene creation of the pathtracer additionally helped teach us the importance of understanding the flow of information, to keep track of variables, classes, and function calls, and the importance of clear naming conventions. Our issues with the virtual projectors were a great learning opportunity to consistently check our assumptions and question whether our understanding was correct, Oh et al.'s paper continued to reveal more information with every read, and because of our insistence to challenge our own understandings, we were able to catch our mistakes and correct them.

# Results

The results of our implementation are shown below. We included details regarding the scaling of the wavelength and amplitude, as well as the time taken to render each image. The first row shows the results of the double slit experiment, where we can see the interference pattern created by the two slits. The following rows show the results of various shaped apertures, including a square, circle, and octagon. Notably, the circular aperture produces a very accurate Airy disk pattern, which is a well-known diffraction pattern produced by circular apertures. The square aperture additionally aligns with the expected diffraction pattern.

<div class="container l-page">
    <div class="row align-items-center text-center">
        <div class="col">
            {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs184/final/slit_green_screenshot_wave_nm_5_am_1_0.13s.png" alt="Slit Green Screenshot Wave NM 5 AM 1" %}
            <div class="caption">Slit - Wave, NM 5, AM 1, 0.13s</div>
        </div>
        <div class="col">
            {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs184/final/slit_green_screenshot_alf_nm_5_am_1_0.14s.png" alt="Slit Green Screenshot ALF NM 5 AM 1" %}
            <div class="caption">Slit - ALF, NM 5, AM 1, 0.14s</div>
        </div>
        <div class="col">
            {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs184/final/slit_all_screenshot_wave_nm_5_am_4_0.57s.png" alt="Slit All Screenshot Wave NM 5 AM 4" %}
            <div class="caption">Slit - Wave, NM 5, AM 4, 0.57s</div>
        </div>
        <div class="col">
            {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs184/final/slit_all_screenshot_alf_nm_5_am_1_0.6s.png" alt="Slit All Screenshot ALF NM 5 AM 1" %}
            <div class="caption">Slit - ALF, NM 5, AM 1, 0.6s</div>
        </div>
    </div>
    <div class="row align-items-center text-center">
        <div class="col">
            {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs184/final/square_all_screenshot_alf_nm_4.5_am_75_1894s.png" alt="Square All Screenshot ALF NM 4.5 AM 75" %}
            <div class="caption">Square - ALF, NM 4.5, AM 75, 1894s</div>
        </div>
        <div class="col">
            {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs184/final/square_all_screenshot_wave_nm_4.5_am_40_26s.png" alt="Square All Screenshot Wave NM 4.5 AM 40" %}
            <div class="caption">Square - Wave, NM 4.5, AM 40, 26s</div>
        </div>
        <div class="col">
            {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs184/final/square_red_far_screenshot_alf_nm_5_am_75_470s.png" alt="Square Red Far Screenshot ALF NM 5 AM 75" %}
            <div class="caption">Square - ALF, NM 5, AM 75, 470s</div>
        </div>
        <div class="col">
            {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs184/final/square_red_far_screenshot_wave_nm_5_am_79_7s.png" alt="Square Red Far Screenshot Wave NM 5 AM 79" %}
            <div class="caption">Square - Wave, NM 5, AM 79, 7s</div>
        </div>
        <div class="col">
            {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs184/final/square_green_screenshot_alf_nm_5_am_50_398s.png" alt="Square Green Screenshot ALF NM 5 AM 50" %}
            <div class="caption">Square - ALF, NM 5, AM 50, 398s</div>
        </div>
        <div class="col">
            {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs184/final/square_green_screenshot_wave_nm_5_am_50_19s.png" alt="Square Green Screenshot Wave NM 5 AM 50" %}
            <div class="caption">Square - Wave, NM 5, AM 50, 19s</div>
        </div>
    </div>
    <div class="row align-items-center text-center">
        <div class="col">
            {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs184/final/circle_green_screenshot_nowave_nm_4_5_am_25_3375s.png" alt="Circle Green Screenshot No Wave NM 4.5 AM 25" %}
            <div class="caption">Circle - ALF, NM 4.5, AM 25, 3375s</div>
        </div>
        <div class="col">
            {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs184/final/circle_green_screenshot_wave_nm_4_5_am_25_53s.png" alt="Circle Green Screenshot Wave NM 4.5 AM 25" %}
            <div class="caption">Circle - Wave, NM 4.5, AM 25, 53s</div>
        </div>
        <div class="col">
            {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs184/final/circle_all_wave_nm_4.5_am_20_11s.png" alt="Circle All Wave NM 4.5 AM 20" %}
            <div class="caption">Circle - Wave, NM 4.5, AM 20, 11s</div>
        </div>
        <div class="col">
            {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs184/final/circle_all_alf_nm_4.5_am_50_1073s.png" alt="Circle All ALF NM 4.5 AM 50" %}
            <div class="caption">Circle - ALF, NM 4.5, AM 50, 1073s</div>
        </div>
      </div>
      <div class="row align-items-center text-center">
        <div class="col">
            {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs184/final/octogon_green_wave_nm_4.5_am_15_11s.png" alt="Octagon Green Wave NM 4.5 AM 15" %}
            <div class="caption">Octagon - Wave, NM 4.5, AM 15, 11s</div>
        </div>
        <div class="col">
            {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs184/final/octogon_green_alf_nm_4.5_am_100_812s.png" alt="Octagon Green ALF NM 4.5 AM 100" %}
            <div class="caption">Octagon - ALF, NM 4.5, AM 100, 812s</div>
        </div>
        <div class="col">
            {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs184/final/octogon_all_wave_nm_4.5_am_10_41s.png" alt="Octagon All Wave NM 4.5 AM 10" %}
            <div class="caption">Octagon - Wave, NM 4.5, AM 10, 41s</div>
        </div>
        <div class="col">
            {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs184/final/octogon_all_alf_nm_4_am_125_3773s.png" alt="Octagon All ALF NM 4 AM 125" %}
            <div class="caption">Octagon - ALF, NM 4, AM 125, 3773s</div>
        </div>
      </div>
      <div class="row align-items-center text-center">
        <div class="col">
            {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs184/final/triangle_green_screenshot_wave_nm_5_am_10_854s.png" alt="Triangle Green Screenshot Wave NM 5 AM 10" %}
            <div class="caption">Triangle - Wave, NM 5, AM 10, 854s</div>
        </div>
        <div class="col">
            {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs184/final/triangle_green_screenshot_nowave_nm_5_am_10_854s.png" alt="Triangle Green Screenshot No Wave NM 5 AM 10" %}
            <div class="caption">Triangle - No Wave, NM 5, AM 10, 854s</div>
        </div>
        <div class="col">
            {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs184/final/horizontal_green_screenshot_wave_nm_5_am_5_75s.png" alt="Horizontal Green Screenshot Wave NM 5 AM 5" %}
            <div class="caption">Horizontal - Wave, NM 5, AM 5, 75s</div>
        </div>
        <div class="col">
            {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs184/final/horizontal_green_screenshot_nowave_nm_5_am_5_11s.png" alt="Horizontal Green Screenshot No Wave NM 5 AM 5" %}
            <div class="caption">Horizontal - No Wave, NM 5, AM 5, 11s</div>
        </div>
      </div>
</div>

Here is a gif of the rendering process.

<div class="container l-page">
    <div class="row align-items-center text-center">
        <div class="col">
            {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs184/final/render.gif" alt="Render" %}
            <div class="caption">Rendering Process</div>
        </div>
    </div>
</div>

## Goals

Our reference diffraction patterns were the following:

<div class="container l-page">
    <div class="row align-items-center text-center">
        <div class="col">
            {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs184/final/farzone.png" alt="Farzone" %}
            <div class="caption">Square Aperture</div>
        </div>
        <div class="col">
            {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs184/final/Airy_disk.png" alt="Airy Disk" %}
            <div class="caption">Circular Aperture</div>
        </div>
      </div>
    <div class="row align-items-center text-center">
        <div class="col">
            {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs184/final/white_diffraction.jpg" alt="White Diffraction" %}
            <div class="caption">White Diffraction</div>
        </div>
        <div class="col">
            {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs184/final/wave.png" alt="Wave" %}
            <div class="caption">All color square diffraction</div>
        </div>
    </div>
</div>

# References

1. Oh, Se Baek, George Barbastathis, and Ramesh Raskar. _Augmenting Light Field to model Wave Optics effects_. Preprint, Massachusetts Institute of Technology, July 2009. [https://arxiv.org/abs/0907.1545](https://arxiv.org/abs/0907.1545)

2. Oh, Se Baek, Sriram Kashyap, Rohit Garg, Sharat Chandran, and Ramesh Raskar. _Rendering Wave Effects with Augmented Light Field_. _Computer Graphics Forum_, vol. 29, no. 2, pp. 507–516, June 2010. DOI: [10.1111/j.1467-8659.2009.01620.x](https://doi.org/10.1111/j.1467-8659.2009.01620.x). [https://dspace.mit.edu/handle/1721.1/66536](https://dspace.mit.edu/handle/1721.1/66536)

3. Cuypers, Tom, Se Baek Oh, Tom Haber, Philippe Bekaert, and Ramesh Raskar. _Ray-Based Reflectance Model for Diffraction_. Preprint, 2011. [http://arxiv.org/abs/1101.5490](http://arxiv.org/abs/1101.5490)

# Contributions

- **Naman Satish** - Developed the Wave and ALF simulation and adaption from 1D to 3D, injection of negative projector creation, and initial Blender scene creation and scripting.

- **Nicholas Tran** - Blender scene generation scripting, scene creation, Wave BSDF attempt, presentation slides.

- **Kevin Tseng** - Wave BSDF replication attempt, mathematical research, video script.

- **Cashus Puhvel** - Pathtracer infrastructure exploration, extensive wave BSDF attempt which ended up being incompatible with existing pathtracer code, helped with writeup, video editor.
