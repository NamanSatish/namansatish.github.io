---
layout: distill
title: Bezier Curves and Triangle Meshes
description: "Part of CS184 : Foundations of Computer Graphics"
img: assets/img/cs184/hw2/plank.png
importance: 8
category: school
images:
  compare: true
  slider: true
pretty_table: true
bibliography: empty.bib
toc:
  - name: "Overview"
  - name: "Bézier Curves with 1D de Casteljau Subdivision"
  - name: "Bézier Surfaces with Separable 1D de Casteljau"
  - name: "Area-Weighted Vertex Normals"
  - name: "Edge Flip"
  - name: "Edge Split"
  - name: "Loop Subdivision for Mesh Upsampling"
---

<!--

Overview (3 pts)
Give a high-level overview of what you have implemented in this assignment. Think about what you have built as a whole. Share your thoughts on what interesting things you have learned from completing this assignment.

Part 1 (13 pts)
Briefly explain de Casteljau’s algorithm and how you implemented it in order to evaluate Bezier curves.
Take a look at the provided .bzc files and create your own Bezier curve with 6 control points of your choosing. Use this Bezier curve for your screenshots below.
Show screenshots of each step / level of the evaluation from the original control points down to the final evaluated point. Press E to step through. Toggle C to show the completed Bezier curve as well.
Show a screenshot of a slightly different Bezier curve by moving the original control points around and modifying the parameter
t
t via mouse scrolling.
Part 2 (15 pts)
Briefly explain how de Casteljau algorithm extends to Bezier surfaces and how you implemented it in order to evaluate Bezier surfaces.
Show a screenshot of bez/teapot.bez (not .dae) evaluated by your implementation.
Part 3 (12 pts)
Briefly explain how you implemented the area-weighted vertex normals.
Show screenshots of dae/teapot.dae (not .bez) comparing teapot shading with and without vertex normals. Use Q to toggle default flat shading and Phong shading.
Part 4 (12 pts)
Briefly explain how you implemented the edge flip operation and describe any interesting implementation / debugging tricks you have used.
Show screenshots of the teapot before and after some edge flips.
Write about your eventful debugging journey, if you have experienced one.
Part 5 (20 pts)
Briefly explain how you implemented the edge split operation and describe any interesting implementation / debugging tricks you have used.
Show screenshots of a mesh before and after some edge splits.
Show screenshots of a mesh before and after a combination of both edge splits and edge flips.
Write about your eventful debugging journey, if you have experienced one.
If you have implemented support for boundary edges, show screenshots of your implementation properly handling split operations on boundary edges.
Part 6 (25 pts)
Briefly explain how you implemented the loop subdivision and describe any interesting implementation / debugging tricks you have used.
Take some notes, as well as some screenshots, of your observations on how meshes behave after loop subdivision. What happens to sharp corners and edges? Can you reduce this effect by pre-splitting some edges?
Load dae/cube.dae. Perform several iterations of loop subdivision on the cube. Notice that the cube becomes slightly asymmetric after repeated subdivisions. Can you pre-process the cube with edge flips and splits so that the cube subdivides symmetrically? Document these effects and explain why they occur. Also explain how your pre-processing helps alleviate the effects.
If you have implemented any extra credit extensions, explain what you did and document how they work with screenshots.
Potential Extra Credit - Art Competition: Model something Creative!
You do not need to have your art ready at the time of submission. The Art Competition will open the following day after the Assignment 2 deadline.
If submitting at the time of the homework submission, save your best polygon mesh as artcompetition.dae in your docs folder
Include a series of screenshots showing your original mesh and your mesh after one and two rounds of subdivision. If you have used custom shaders, include screenshots of your mesh with those shaders applied as well.
Describe what you have done to enhance your mesh beyond the simple humanoid mesh described in the tutorial.

-->

https://satish.dev/projects/homework_2

# Overview

In this project, I implemented various algorithms for working with Bézier curves and triangle meshes. I started by implementing the de Casteljau algorithm for evaluating Bézier curves, which recursively interpolates between control points to compute the curve point at a given parameter. I then extended this algorithm to Bézier surfaces, which are defined by a grid of control points. I also implemented area-weighted vertex normals for shading, edge flip and split operations for mesh processing, and loop subdivision for mesh upsampling.

I began by understanding the underlying mathematics of Bézier curves which let me build up to Bézier surfaces. However, the challenge in understanding on how to effectively modify the mesh led to the need for the triangle mesh. This subsequently led to the implementation of area-weighted vertex normals to create smoother shading. In an attempt to regain the smooth parametric nature of the Bézier curves, I implemented the edge flip and split operations. However, by themselves, these operations could not be manually used to smooth out the entire mesh at once. This led to the implementation of loop subdivision, which allowed me to upsample the mesh and create a smoother mesh.

## Bézier Curves with 1D de Casteljau Subdivision

Bézier curves are fundamental in computer graphics, animation, and geometric modeling. The de Casteljau algorithm provides a recursive method to evaluate and subdivide Bézier curves, making it useful for applications like rendering and interpolation.

### 1D de Casteljau Subdivision Algorithm

The de Casteljau algorithm constructs a Bézier curve by recursively interpolating between control points. Given a set of control points $$( P_0, P_1, \dots, P_n )$$ and a parameter $$( t \in [0,1] )$$, the algorithm proceeds as follows:

1. Compute new interpolated points:
   $$
   [
   P_0^{(1)} = (1 - t) P_0 + t P_1, \quad P_1^{(1)} = (1 - t) P_1 + t P_2, \quad \dots, \quad P_{n-1}^{(1)} = (1 - t) P_{n-1} + t P_n
   ]
   $$
2. Repeat this process for successive levels:
   $$
   [
   P_0^{(2)} = (1 - t) P_0^{(1)} + t P_1^{(1)}, \quad P_1^{(2)} = (1 - t) P_1^{(1)} + t P_2^{(1)}, \quad \dots, \quad P_{n-2}^{(2)} = (1 - t) P_{n-2}^{(1)} + t P_{n-1}^{(1)}
   ]
   $$
   Continue until a single point remains, which is the curve point at $$( t)$$.

### Implementation

In my implementation, I itererated through a vector of control points, using the de Casteljau algorithm to compute the next level of points. I was able to make some efficient optimizations by reserving the necessary space for the new points and using emplace_back instead of push_back.

<div class="row">
  <div class="col-sm mt-3 mt-md-0">
    Time (original) w/ length 4: 4.541 µs <br>
    Time (original) w/ length 3: 0.542 µs <br>
    Time (original) w/ length 2: 0.25 µs
  </div>
  <div class="col-sm mt-3 mt-md-0">
    Time (optimized) w/ length 4: 0.958 µs <br>
    Time (optimized) w/ length 3: 0.334 µs <br>
    Time (optimized) w/ length 2: 0.208 µs
  </div>
</div>

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs184/hw2/6pointbcz.png" alt="6 Control Point Bezier Curve" %}
        <div class="caption">6 Control Point Bezier Curve</div>
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs184/hw2/interpolated_t_2.png" alt="Interpolated Curve (t=2)" %}
        <div class="caption">Interpolated Curve, high t</div>
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs184/hw2/interpolated_t.png" alt="Interpolated Curve" %}
        <div class="caption">Interpolated Curve, low t</div>
    </div>
</div>

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" zoomable=true path="assets/img/cs184/hw2/level_1.png" alt="Subdivision Level 1" %}
        <div class="caption">Subdivision Level 1</div>
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" zoomable=true path="assets/img/cs184/hw2/level_2.png" alt="Subdivision Level 2" %}
        <div class="caption">Subdivision Level 2</div>
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" zoomable=true path="assets/img/cs184/hw2/level_3.png" alt="Subdivision Level 3" %}
        <div class="caption">Subdivision Level 3</div>
    </div>
</div>

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs184/hw2/level_4.png" alt="Subdivision Level 4" %}
        <div class="caption">Subdivision Level 4</div>
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs184/hw2/level_5.png" alt="Subdivision Level 5" %}
        <div class="caption">Subdivision Level 5</div>
    </div>
        <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs184/hw2/modified_curve.png" alt="Modified Curve" %}
        <div class="caption">Modified Curve</div>
    </div>
</div>

## Bézier Surfaces with Separable 1D de Casteljau

Bézier surfaces are a natural extension of Bézier curves, allowing for the representation of complex shapes in 3D space. The de Casteljau algorithm extends naturally to Bézier surfaces by applying the 1D algorithm twice—once along one parameter direction and then along the other. This method leverages the fact that a Bezier surface can be seen as a set of Bezier curves defined in one direction, which can then interpolated in the perpendicular direction.

### Extending de Casteljau's Algorithm to Bezier Surfaces

1. **Row-wise Reduction (First Pass)**:
   - Each row of control points is treated as a Bezier curve in the `u` parameter.
   - Using de Casteljau’s algorithm for 1D curves, we compute an intermediate set of points—one per row—evaluated at `u`.
2. **Column-wise Reduction (Second Pass)**:
   - The intermediate points from the first pass now define a new Bezier curve along the `v` parameter.
   - Applying de Casteljau’s algorithm again, we compute the final interpolated point on the Bezier surface.

### Implementation

In my implementation, I followed the steps outlined above, iterating through the rows and columns of control points to compute the final surface point. I used a similar optimization strategy as with the 1D de Casteljau algorithm.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs184/hw2/teapot.png" alt="Teapot Bezier Surface" %}
        <div class="caption">Teapot Bezier Surface</div>
    </div>
</div>

## Area-Weighted Vertex Normals

We will now be switching to triangle meshes, as though Bézier surfaces are powerful, they are not as efficient as triangle meshes for rendering. One important aspect of rendering is shading, so we will begin by implementing Phong shading using area-weighted vertex normals.

### Algorithm

1. **Compute Weighted Face Normals**:
   - For each face incident on a vertex, compute the face normal.
   - Weight each face normal by the respective face area.
2. **Compute Vertex Normals**:
   - For each vertex, sum the incident weighted face normals.
   - Normalize the resulting vector to obtain the vertex's normal.

### Implementation

In my implementation, I traversed the incident faces by first going through the vertex's halfedge, then calling twin(). With a halfedge pointing back towards our original vertex, I can traverse to the next halfedge and call twin() again. This allows me to travel through all the faces incident on a vertex, accumulating the weighted face normals, until I reach the starting halfedge. I disregard the computation on any faces that are boundaries, and calculate the area by crossing two vectors that make up the triangle and dividing the norm by two. I then weight the face normal by the area and add it to the vertex's normal, normalizing the accumulated normals at the end of the traversal.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs184/hw2/flat_shading.png" alt="Teapot Flat Shading" %}
        <div class="caption">Teapot Flat Shading</div>
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs184/hw2/phong_shading.png" alt="Teapot Phong Shading" %}
        <div class="caption">Teapot Phong Shading</div>
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs184/hw2/flat_top.png" alt="Teapot Top Flat Shading" %}
        <div class="caption">Teapot Top Flat Shading</div>
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs184/hw2/phong_top.png" alt="Teapot Top Phong Shading" %}
        <div class="caption">Teapot Top Phong Shading</div>
    </div>
</div>

## Edge Flip

The edge flip operation is a fundamental operation in mesh processing that swaps the diagonal of a quadrilateral face.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs184/hw2/edge_flip.png" alt="Edge Flip Diagram" %}
        <div class="caption">Diagram of Edge Flip Operation</div>
    </div>
</div>

### Implementation

In my implementation of edge flipping, I first checked if the edge was a boundary edge, and if so we left it as is. Otherwise, I extracted all relevant halfedges, vertices, and faces, and then reconnected them to form the changed faces.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs184/hw2/before_edge_flip.png" alt="Teapot Before Edge Flip" %}
        <div class="caption">Teapot Before Edge Flip</div>
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs184/hw2/after_edge_flip.png" alt="Teapot After Edge Flip" %}
        <div class="caption">Teapot After Edge Flip</div>
    </div>
        <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs184/hw2/after_edge_flip_2.png" alt="Teapot After Edge Flip w/ new angle" %}
        <div class="caption">Teapot After Edge Flip w/ new angle</div>
    </div>
</div>

### Debugging Journey

I began by drawing an example of the triangle before and after the flip, and then I drew the halfedges and vertices to understand the connections. I then drew the new faces and halfedges to ensure that the connections were correct. Unfortunately, in my first drawing I had mistakenly drawn my halfedges clockwise, leading to an incorrect implementation. Additionally, I spent a large time debugging skinny degenerate triangles that were created by the edge flip operation. I eventually realized that this was expected behavior, as the edge flip operation can create skinny triangles.

## Edge Split

The edge split operation is another fundamental operation in mesh processing that splits an edge by adding a new vertex in the middle edge of a quad face which is connected to the side vertices.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs184/hw2/edge_split.png" alt="Edge Split Diagram" %}
        <div class="caption">Diagram of Edge Split Operation</div>
    </div>
</div>

### Implementation

My implementation of edge splitting was similar to edge flipping, where I first checked if the edge was a boundary edge. I then extracted all relevant halfedges, vertices, and faces, created 1 new vertex, 2 new faces, 4 new edges, and 6 new halfedges. I reused the existing middle halfedges and two faces, and reconnected all relevant elements to form the new faces.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs184/hw2/prior_flip_split.png" alt="Teapot Before Edge Split" %}
        <div class="caption">Teapot Before Modification</div>
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs184/hw2/split.png" alt="Teapot After Edge Split" %}
        <div class="caption">Teapot After Edge Split</div>
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs184/hw2/flip_and_split.png" alt="Teapot After Edge Flip and Split" %}
        <div class="caption">Teapot After Edge Flip and Split</div>
    </div>
</div>

### Debugging Journey

Luckily the extension from flipping to splitting was relatively straightforward, as I was able to reuse the existing halfedges and faces. However, the sheer amount of variables required me to be specific in my naming in order to ensure I was not misplacing any elements. I also had to be careful to ensure that I was connecting the correct vertices and halfedges to the new elements.

## Loop Subdivision for Mesh Upsampling

Loop subdivision is a powerful technique for mesh upsampling, creating a smoother and more detailed mesh from an existing one. It works by refining the mesh through a series of steps that involve splitting edges, updating vertex positions, and creating new faces.

### Implementation

I implemented it via computing the new vertex positions and positions for new midpoints. Then I split all my original edges and flipped any edges that connected an old vertex to a new one. Finally, I updated all my points with their stored new positions.

This function caused me to rewrite my edge flip and split functions twice. Turns out the issue was in the upsampling all along, as I was not updating the newly created edges after splitting correctly.

Here are some of the errors I encountered:

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs184/hw2/error_1.png" alt="Error 1" %}
        <div class="caption">Error 1</div>
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs184/hw2/error_2.png" alt="Error 2" %}
        <div class="caption">Error 2</div>
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs184/hw2/error_3.png" alt="Error 3" %}
        <div class="caption">Error 3</div>
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs184/hw2/error_4.png" alt="Error 4" %}
        <div class="caption">Error 4</div>
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs184/hw2/error_5.png" alt="Error 5" %}
        <div class="caption">Error 5</div>
    </div>
</div>

### Observations

The subdivision algorithm was able to create a smoother mesh for my teapot mesh, as they round out the curves instead of creating sharp edges.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs184/hw2/teapot_0.png" alt="Teapot Before Subdivision" %}
        <div class="caption">Teapot Before Subdivision</div>
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs184/hw2/teapot_1.png" alt="Teapot After 1 Subdivision" %}
        <div class="caption">Teapot After 1 Subdivision</div>
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs184/hw2/teapot_2.png" alt="Teapot After 2 Subdivisions" %}
        <div class="caption">Teapot After 2 Subdivisions</div>
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs184/hw2/teapot_3.png" alt="Teapot After 3 Subdivisions" %}
        <div class="caption">Teapot After 3 Subdivisions</div>
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs184/hw2/teapot_3_wf.png" alt="Teapot After 3 Subdivisions Wireframe" %}
        <div class="caption">Teapot After 3 Subdivisions Wireframe</div>
    </div>
</div>

I noticed that presplitting around the handles of the teapot helped to reduce the sharpness of the edges, as the subdivision algorithm was able to round out the edges more effectively. However, there were some areas where the subdivision algorithm had some issues, such as the cube mesh.

In the cube mesh, I noticed that the cube became slightly asymmetric after repeated subdivisions.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs184/hw2/cube_0.png" alt="Cube Before Subdivision" %}
        <div class="caption">Cube Before Subdivision</div>
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs184/hw2/cube_1.png" alt="Cube After 1 Subdivision" %}
        <div class="caption">Cube After 1 Subdivision</div>
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs184/hw2/cube_2.png" alt="Cube After 2 Subdivisions" %}
        <div class="caption">Cube After 2 Subdivisions</div>
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs184/hw2/cube_3.png" alt="Cube After 3 Subdivisions" %}
        <div class="caption">Cube After 3 Subdivisions</div>
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs184/hw2/cube_4.png" alt="Cube After 4 Subdivisions" %}
        <div class="caption">Cube After 4 Subdivisions</div>
    </div>
</div>

I tried applying some pre splits along a face and noticed that this had some useful effects in reducing the asymmetry.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs184/hw2/cube_presplit_0.png" alt="Cube Before Subdivision" %}
        <div class="caption">Cube Before Subdivision</div>
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs184/hw2/cube_presplit_1.png" alt="Cube After 1 Subdivision" %}
        <div class="caption">Cube After 1 Subdivision</div>
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs184/hw2/cube_presplit_2.png" alt="Cube After 2 Subdivisions" %}
        <div class="caption">Cube After 2 Subdivisions</div>
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs184/hw2/cube_presplit_3.png" alt="Cube After 3 Subdivisions" %}
        <div class="caption">Cube After 3 Subdivisions</div>
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs184/hw2/cube_presplit_4.png" alt="Cube After 4 Subdivisions" %}
        <div class="caption">Cube After 4 Subdivisions</div>
    </div>
</div>

I believe that the pre-splitting helped to alleviate the asymmetry by providing the subdivision algorithm with more information about the mesh, allowing it to create a more symmetric subdivision. This is because although we can represent a cube with a single face, the subdivision may try to smooth out this large face with the edges, leading to the asymmetry.
