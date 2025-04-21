---
layout: distill
title: "Milestone Update, Apollo: Augmenting Light Functions to Simulate Wave-Based Diffraction"
description: "Final Project in CS184 : Foundations of Computer Graphics"
img: assets/img/cs184/final/apollo.png
importance: 12
date: 2025-04-20
category: school
bibliography: empty.bib
toc:
  - name: "Current Progress"
  - name: "Plans Going Forward"
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

Team Number 54

Link : https://satish.dev/projects/184_milestone/

Proposal Link : https://satish.dev/projects/184_proposal/

Slides : https://docs.google.com/presentation/d/1Xtp83b7pyEKYRISCwhU9ZiQJkb9-J2A-jG7GtQ50qKQ/edit?usp=sharing

Video Link: https://youtu.be/FEWRREKrkVo

# Current Progress

At our current state, we have completed the creation of our first demo scene in Blender to demonstrate the wave effects we intend to implement.
We have additionally implemented a switch in the UI that allows us to toggle later any Augmented Light Function specific implementations. This allows us to test the original pathtracer and our wave-based renderer in the same scene.

After many group reads of the paper, we are beginning to understand the math needed for our implementation, and the underlying principles of the modifications we need to make to the pathtracer.

The scene was created in Blender 2.72 due to issues in modern versions of Blender exporting incompatible dae files with the path tracer. There were issues in the xml formatting and the ability to maintain surface shading parameters, this took a while to discover and resolve, requiring us to remake the scene in a buggy and frequently crashing Blender. We chose to scale our objects to real world experiments to hopefully see the interference with our first attempt.

<div class="container l-page">
    <div class="row align-items-center text-center">
        <div class="col">
            {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs184/final/slit_blender.png" alt="Blender Scene" %}
            <div class="caption">Blender Scene</div>
        </div>
        <div class="col">
            {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs184/final/slit_pathtracer.png" alt="Naive Rendering" %}
            <div class="caption">Naive Rendering</div>
        </div>
    </div>
</div>

Going forward, our plan is to continue work on the wave-based renderer itself. Rather than overhauling the original pathtracer, we will consider rendering wave effects separately and layering it on top of the original scene in a hybrid approach. Given the remaining timeframe and our current progress relative to our original plan, we will likely opt to avoid implementing additional effects such as thin film interference and focus on the core diffraction simulation.

# Plans Going Forward

- Continue development of the wave-based renderer.

- Consider a hybrid approach: render wave effects separately and layer them over the original pathtraced scene.

- Avoid overhauling the original pathtracer to save time.

- Prioritize core diffraction simulation over additional effects.

- Defer complex features like thin film interference due to time constraints.
