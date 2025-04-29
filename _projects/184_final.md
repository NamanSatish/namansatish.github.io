---
layout: distill
title: "Final Report, Apollo: Augmenting Light Functions to Simulate Wave-Based Diffraction"
description: "Final Project in CS184 : Foundations of Computer Graphics"
img: assets/img/cs184/final/apollo.png
importance: 13
date: 2025-04-26
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

Final Report : https://satish.dev/projects/184_final/

Milestone : https://satish.dev/projects/184_milestone/
Slides : https://docs.google.com/presentation/d/1Xtp83b7pyEKYRISCwhU9ZiQJkb9-J2A-jG7GtQ50qKQ/edit?usp=sharing
Video Link: https://youtu.be/FEWRREKrkVo

Proposal Link : https://satish.dev/projects/184_proposal/

# Analysis

We began with an analysis of the pathtracer from the end of Homework 3, testing out some of the features that we would need to implement and seeing how they rendered prior to implemention. This exposed some unique intricacies of the pathtracer that we did not initially expect!

While our first thought on how to approach the project was to physically mockup Young's double slit experiment, we found a more intuitive and unexplored approach to simulating diffraction through the use of negative radiance. Before we set out to implement this, we wanted to test out the pathtracer's ability to render negative radiance.

To set up the basis for our experimentation, we constructed a simple scene in blender with a vertical plane a bit behind the axis, a plane with two vertical slits offset from the center, and 3 point lights between the plane and the camera. The lights were arranged in a horizontal line and from left to right, were applied with light strengths of 1, -0.3, and 0.1.

<div class="container l-page">
    <div class="row align-items-center text-center">
        <div class="col">
            {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs184/final/blender_setup.png" alt="Blender Scene" %}
            <div class="caption">Blender Scene</div>
        </div>
        <div class="col">
            {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs184/final/negative_0p3_1.png" alt="Naive Rendering" %}
            <div class="caption">Naive Rendering</div>
        </div>
    </div>
    <div class="row align-items-center text-center">
        <div class="col">
            {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs184/final/negative_0p3_2.png" alt="Naive Rendering" %}
            <div class="caption">Naive Rendering</div>
        </div>
        <div class="col">
            {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs184/final/negative_0p3_3.png" alt="Naive Rendering" %}
            <div class="caption">Naive Rendering</div>
        </div>
    </div>
</div>

The closer darker green rectangle is the plane with slits, and the background with the colors is our vertical plane. The variety of colors that was reflected on the wall was very interesting and not expected at all. It did demonstrate that the pathtracer was capable of rendering negative radiance, which was a good sign, and that we were on a good path to implementing our diffraction simulation.

To better understand this scene, take a look at the following diagram:

{% details **Code** %}

```python
import matplotlib.pyplot as plt

# Scene parameters
light_positions = [(-5, 3), (-5, 0), (-5, -3)]
intensities = [1, -0.3, 0.1]
slit_centers = [0.5, -0.5]
slit_half = 0.2
back_x = 5

fig, ax = plt.subplots(figsize=(8, 6))

# Draw slit plane at x=0 with two gaps
ymin, ymax = -10, 10
segments = [
    (ymin, slit_centers[1] - slit_half),
    (slit_centers[1] + slit_half, slit_centers[0] - slit_half),
    (slit_centers[0] + slit_half, ymax)
]
for y0, y1 in segments:
    ax.plot([0, 0], [y0, y1], linewidth=6)

# Draw back plane at x=5
ax.plot([back_x, back_x], [ymin, ymax], linewidth=6)

# Label slits
for idx, center in enumerate(slit_centers, start=1):
    ax.text(0.1, center, f"Slit {idx}", va='center')
    ax.plot([0, 0], [center - slit_half, center + slit_half], linewidth=2)

# Draw sources and rays
for (lx, ly), inten in zip(light_positions, intensities):
    ax.plot(lx, ly, 'o')
    t_back = (back_x - lx) / (-lx)
    for center in slit_centers:
        # Draw two rays per slit (to each slit edge)
        for edge in [center - slit_half, center + slit_half]:
            y_back = ly + t_back * (edge - ly)
            ax.plot([lx, back_x], [ly, y_back], linestyle='--')
        # Label intensity at midpoint of slit projection
        y_mid = ly + t_back * (center - ly)
        #ax.text(back_x + 0.1, y_mid, str(inten), va='center')

# Annotate planes
ax.text(-0.3, 4.5, "Slit plane", rotation=90, va='center')
ax.text(back_x + 0.3, 4.5, "Back plane", rotation=90, va='center')

# Final formatting
ax.set_xlim(-6, 6)
ax.set_ylim(-6, 6)
ax.set_aspect('equal')
ax.axis('off')

plt.show()
```

{% enddetails %}

<div class="container l-page">
    <div class="row align-items-center text-center">
        <div class="col">
            {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs184/final/slit_colors.png" alt="Slit Colors" %}
            <div class="caption">Initial Slit Coloring</div>
        </div>
    </div>
</div>

# Modifications to the Pathtracer

## Negative Lights and Nanometer

In order to simulate diffraction, we needed to implement a way to assign negative light sources to the pathtracer. Thankfully, negative radiance is smoothly handled already, but we have specific negative light interactions that we want to perform, thus, we added a new boolean member to the `SceneLight` class, which would be set to true if the light's radiance was negative. We also added a new integer member to store the wavelength of the light in nanometers, which would be used for diffraction calculations.

### 1. scene.h

- **New members in `SceneLight`**

  - `bool negative_`
  - `int nanometer_`

- **Default constructor**  
  Initialized both members to safe defaults:

  ```cpp
  SceneLight() : negative_(false), nanometer_(0) { }
  ```

- **Getters added**

  ```cpp
  bool is_negative() const;
  int  nanometer()    const;
  ```

- **Helper function**
  ```cpp
  void init_radiance_features(const Vector3D &rad);
  ```
  - Sets `negative_` if any RGB component of `rad` is < 0.
  - Chooses `nanometer_` = 650 nm (red), 525 nm (green), 450 nm (blue) based on the largest absolute channel.
  - Defaults to Red if all channels are 0.

### 2. light.cpp

- **Invoked `init_radiance_features`** in our PointLight constructor:
  ```cpp
  PointLight::PointLight(const Vector3D rad, const Vector3D pos)
    : radiance(rad), position(pos) {
      init_radiance_features(rad);
      …
  }
  ```
  Similar calls were not needed for `DirectionalLight`, `InfiniteHemisphereLight`, `SpotLight`, `AreaLight`, `SphereLight`, and `MeshLight` since those are not used in our model of diffraction.

### 3. Usage

After these changes, we could now access these fields in our pathtracer.

```cpp
if (light->is_negative()) {
  // handle negative‐intensity sources
}
int λ = light->nanometer();  // 650, 525, or 450
```
