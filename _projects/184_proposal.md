---
layout: distill
title: "Apollo: Augmenting Light Functions to Simulate Wave-Based Diffraction"
description: "Final Project in CS184 : Foundations of Computer Graphics"
img: assets/img/cs184/final/double_slit.jpg
importance: 11
date: 2024-12-18
category: school
bibliography: empty.bib
toc:
  - name: "Summary"
  - name: "Problem Description"
  - name: "Goals and Deliverables"
  - name: "Schedule"
  - name: "Resources"
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

https://satish.dev/projects/184_proposal/

# Summary

Our project, Apo**ll**o, will aim to upgrade the Homework 3’s ray tracing with the functionality that will allow it to represent the wave nature of light, specifically wave-based diffraction. In doing so, we aim to demonstrate the capability of ray tracing to demonstrate the wave nature of light via the double slit diffraction experiment.

<div class="row mx-auto" style="text-align: center;">
  <div class="col-sm mt-3 mt-md-0" id="inspirational_quote">
    “[The double-slit experiment] has in it the heart of quantum mechanics. In reality, it contains the only mystery. —Richard Feynman
  </div>
</div>

# Problem Description

In classical computer graphics and light transport, ray-based light fields (LF) are widely used to represent and simulate the flow of light. These functions assume that light propagates in straight lines and carries only positive radiance, which makes them efficient and intuitive for modeling many real-world lighting scenarios. However, this ray-based formulation inherently limits LF's ability to model phase-sensitive wave phenomena—most notably, diffraction.

To address this limitation, we aim to extend the classical LF framework by incorporating concepts from the Wigner Distribution Function (WDF), a wave-based light representation capable of encoding both amplitude and phase information. This hybrid model, which we call the Augmented Light Field (ALF), retains the simplicity and compatibility of classical LF while enabling it to simulate wave effects.

While one could directly use the WDF for full wave-based simulation, its complexity and deviation from standard ray-based representations make it less suitable for integration into existing LF pipelines. In contrast, ALF offers a practical compromise: it augments ray-based models with select wave properties, preserving ease of use and enabling broader applicability in graphics and vision tasks that require phase-aware modeling.

# Goals and Deliverables

Our primary objective is to extend a classical ray-based light transport system to incorporate wave-based light phenomena such as diffraction. By integrating elements of the Wigner Distribution Function into a ray-based framework, we aim to implement an Augmented Light Field (ALF) that maintains the simplicity and compatibility of traditional light fields while enabling physically-plausible wave effects.

1. What we plan to deliver (Baseline Goals)

We plan to modify the HW3 pathtracer to support wave-like behaviors in light transport. Specifically, we will:

- Implement the **Augmented Light Field (ALF)** model, drawing from the techniques proposed in [paper](https://www.cse.iitb.ac.in/~kashyap08/diffraction/index.html), to simulate Fresnel and Fraunhofer diffraction using ray-based methods.
- Render scenes that highlight **diffraction effects**, including:
  - A recreation of the **double slit experiment**, visualizing the characteristic interference patterns.
  - Simulations of **Fresnel (near-field)** and **Fraunhofer (far-field)** diffraction using appropriate geometric setups (e.g. point light source near aperture for Fresnel; distant light source with circular aperture for Fraunhofer).
- Compare ALF-rendered outputs to traditional LF renders, visually demonstrating the added physical realism due to wave-based interference patterns that LFs fail to capture.
- Provide side-by-side visual results and include diagnostic plots or overlays to illustrate where wave effects diverge from classical ray predictions.

This should enable images such as the following:

<div class="container l-body">
    <div class="row align-items-center text-center">
        <div class="col">
            {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs184/final/young_frame_color_512.png" alt="Double Slit Experiment" %}
        <div class="caption">Double Slit Experiment</div>
        </div>
        <div class="col">
            {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs184/final/box.png" alt="Diffraction on arbitrary surface" %}
        <div class="caption">Diffraction on arbitrary surface</div>
        </div>
    </div>
</div>

These deliverables form the minimum functionality required for a successful project and will demonstrate our ability to capture key physical light phenomena using our hybrid model.

1. What we hope to deliver (Aspirational Goals)

If we are able to implement and validate our core functionality ahead of schedule, we aim to go further and explore additional wave-based optical effects, including:

- **Thin-film interference** effects (e.g. iridescence on soap bubbles or oil films), simulated through phase shifts introduced by varying optical path lengths and material properties.
- **Polarization modeling** using an Angle-Impact Wigner Distribution Function, expanding ALF’s expressivity to handle directionally and polarization-sensitive phenomena.
- Simulating **non-linear light behaviors**, such as **singularities** and caustic wavefronts, going beyond the scope of the original paper.
- Rendering more complex scenes that combine multiple wave effects (e.g. a reflective bubble with diffraction and interference at once), demonstrating ALF’s versatility and potential for more expressive graphics.

We also aim to provide a clean visualization interface (potentially using OpenGL or a web-based viewer) to interactively explore rendered results and their differences under LF vs ALF simulation.

To assess the quality and success of our system, we will:

- Generate side-by-side comparisons of LF and ALF renders to qualitatively assess added realism.
- Validate our results against analytical solutions or prior work (such as figures in the referenced paper).
- If time allows, measure performance overhead of ALF relative to LF, to characterize the trade-off between physical realism and computational complexity.

# Resources

We will following the idea proposed in [Rendering Wave Effects with Augmented Light Field](https://doi.org/10.1111/j.1467-8659.2009.01620.x) by Se Baek Oh et al. 2010. The paper proposes a method to augment a ray tracing engine with a modified light field representation that can simulate wave-based diffraction effects. We will additionally be taking a look at the paper [Augmenting Light Field to model Wave Optics effects](https://arxiv.org/abs/0907.1545) also by Se Baek Oh et al. 2009. These two papers will be our initial guide in providing us with the requisite knowledge to the Wagner Distribution Function (WDF) and the Augmented Light Field (ALF) framework. We will additionally be basing our results on the results shown [here](https://www.cse.iitb.ac.in/~kashyap08/diffraction/index.html).
