---
layout: distill
title: (Auto)Stitching and Photo Mosaics
description: "Part of CS180 : Intro to Computer Vision and Computational Photography"
img: assets/img/cs180/proj4/output/landscape/tokyo_tower/mosaic_combined.png
importance: 4
category: school
bibliography: empty.bib
toc:
  - name: "Photos"
  - name: "Recovering Homography"
  - name: "Warping Images"
  - name: "Blending"
  - name: "Rectification"
  - name: "Detecting Corner features"
  - name: "ANMS"
  - name: "Corner Features"
  - name: "Matches"
  - name: "RANSAC"
---

# Photos

In the process of attaining photos that will be used for photo mosaics, I took a variety of photos of landscapes and landmarks. These photos were taken in different locations, but followed the same principle to keep the lens as fixed as possible and take multiple photos whilst rotating the camera.

<div class="row l-page mx-auto">
{% details **Mission San Francisco** %}
<div class="row l-page">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj4/input/landscape/mission_sanfrancisco/left.jpg" alt="Left view of Mission San Francisco" %}
        <div class="caption">
        Left view of Mission San Francisco
        </div>
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj4/input/landscape/mission_sanfrancisco/middle.jpg" alt="Middle view of Mission San Francisco" %}
        <div class="caption">
        Middle view of Mission San Francisco
        </div>
    </div>
</div>
<div class="row l-page">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj4/input/landscape/mission_sanfrancisco/right.jpg" alt="Right view of Mission San Francisco" %}
        <div class="caption">
        Right view of Mission San Francisco
        </div>
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj4/input/landscape/mission_sanfrancisco/topright.jpg" alt="Top Right view of Mission San Francisco" %}
        <div class="caption">
        Top Right view of Mission San Francisco
        </div>
    </div>
</div>
{% enddetails %}
</div>

<div class="row l-page mx-auto">
{% details **Pong** %}
<div class="row l-page">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj4/input/landscape/pong/left.jpg" alt="Left view of Pong landscape" %}
        <div class="caption">
        Left view of Pong landscape
        </div>
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj4/input/landscape/pong/center.jpg" alt="Center view of Pong landscape" %}
        <div class="caption">
        Center view of Pong landscape
        </div>
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj4/input/landscape/pong/right.jpg" alt="Right view of Pong landscape" %}
        <div class="caption">
        Right view of Pong landscape
        </div>
    </div>
</div>
{% enddetails %}
</div>

<div class="row l-page mx-auto">
{% details **Bay Bridge** %}
<div class="row l-page">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj4/input/landscape/bay_bridge/2.jpg" alt="View 2 of Bay Bridge" %}
        <div class="caption">
        View 2 of Bay Bridge
        </div>
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj4/input/landscape/bay_bridge/1.jpg" alt="View 1 of Bay Bridge" %}
        <div class="caption">
        View 1 of Bay Bridge
        </div>
    </div>
</div>
<div class="row l-page">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj4/input/landscape/bay_bridge/3.jpg" alt="View 3 of Bay Bridge" %}
        <div class="caption">
        View 3 of Bay Bridge
        </div>
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj4/input/landscape/bay_bridge/4.jpg" alt="View 4 of Bay Bridge" %}
        <div class="caption">
        View 4 of Bay Bridge
        </div>
    </div>
</div>
{% enddetails %}
</div>

<div class="row l-page mx-auto">
{% details **Tokyo Tower** %}
<div class="row l-page">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj4/input/landscape/tokyo_tower/tower.jpg" alt="Tower view of Tokyo Tower" %}
        <div class="caption">
        Tower view of Tokyo Tower
        </div>
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj4/input/landscape/tokyo_tower/bridge.jpg" alt="Bridge view of Tokyo Tower" %}
        <div class="caption">
        Bridge view of Tokyo Tower
        </div>
    </div>
</div>
{% enddetails %}
</div>

# Recovering Homography

To stitch the photos, we first need to determine the relationship between them. This is done by calculating the homography, a 3x3 matrix that describes the transformation between two images. We compute the homography by using correspondence points between both images, manually (for now) marked locations that are the same between both images. By constructing a system of equations, we can solve for the homography matrix.

<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
        $$
        \begin{bmatrix}
        a & b & c \\
        d & e & f \\
        g & h & i
        \end{bmatrix}
        \begin{bmatrix} x \\ y \\ 1 \end{bmatrix}
        =
        \begin{bmatrix} wx' \\ wy' \\ w \end{bmatrix}
        $$
    </div>
</div>

Because we are using projective geometry, we will set $$i = 1$$, which enforces consistent scaling, and solve for the remaining 8 unknowns. We can now solve for these 8 unknowns, let us start by expanding the above equation:

<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
        $$
        \begin{align*}
        ax + by + c = wx' \\
        dx + ey + f = wy' \\
        gx + hy + 1 = w \\
        \end{align*}
        $$
    </div>
</div>

We can now solve for the unknowns by constructing a system of equations. We can do this because our 3rd equation contains just w on the right side, meaning we can substitute the other two equations.

<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
        $$
        \begin{align*}
        ax + by + c = (gx + hy + 1) x' \\
        dx + ey + f = (gx + hy + 1) y' \\
        \\
        \text{And further simplifying, we get:} \\
        \\
        ax + by + c - gxx' - hyx' = x' \\
        dx + ey + f - gxy' - hyy' = y' \\
        \end{align*}
        $$
    </div>
</div>

Now we can construct a matrix equation to solve for the unknowns:

<div class="row l-page mx-auto">
    <div class="col-sm mt-3 mt-md-0">
        $$
        \begin{align*}
        &\begin{bmatrix}
        x & y & 1 & 0 & 0 & 0 & -xx' & -yx' \\
        0 & 0 & 0 & x & y & 1 & -xy' & -yy' \\
        \end{bmatrix}
        \begin{bmatrix} a \\ b \\ c \\ d \\ e \\ f \\ g \\ h \end{bmatrix}
        =
        \begin{bmatrix} x' \\ y' \end{bmatrix}
        \end{align*}
        $$
    </div>
</div>

With 8 unknowns, and 2 equations per correspondence point, we need at least 4 correspondence points to solve for the homography matrix. Using least squares, we can solve for the homography matrix with additional points to reduce errors from minor misalignments.

Here is an example of what the point correspondences used to calculate the homography matrix look like on the images:

<div class="row mx-auto">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj4/output/figures/visualization_of_correspondence_points.jpg" alt="Bay Bridge 1 and 2 Correspondence Points" %}
        <div class="caption">
        Bay Bridge 1 and 2 Correspondence Points
        </div>
    </div>
</div>

# Warping Images

Homography matrices are useful to warp all the points from one image to another. Going forward, we will refer to images in their own coordinate system, with the origin at the top left corner, as well as the mosaic coordinate system, which is a grid large enough to contain all images in their warped form. The mosaic coordinate system relies on the presence of a root image, which all other images will be warped to. In my implementation, I constructed a graph of where images were connected by their provided correspondence points, and used the closeness centrality metric to determine which image had the smallest distance to all other images. This image was then chosen as the root image.

For each image that would be warped, warp_im, I computed the shortest path to the root image, root_im. Then by traversing from warp_im to root_im, I could construct a homography matrix that would warp from warp_im to the intermediary nodes, and finally to root_im.

Once the homography matrices were computed, I could apply inverse warping from the pixels within the polygon that bounded the warp_im in the mosaic coordinate system, and interpolate the pixel values to fill in the mosaic image.

## Results

<div class="row l-page mx-auto">
{% details **Mission San Francisco** %}
<div class="row l-page">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj4/output/landscape/mission_sanfrancisco/left.png" alt="Left view of Mission San Francisco in mosaic coordinates" %}
        <div class="caption">
        Left view of Mission San Francisco in mosaic coordinates
        </div>
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj4/output/landscape/mission_sanfrancisco/middle.png" alt="Middle view of Mission San Francisco in mosaic coordinates" %}
        <div class="caption">
        Middle view of Mission San Francisco in mosaic coordinates
        </div>
    </div>
</div>
<div class="row l-page">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj4/output/landscape/mission_sanfrancisco/right.png" alt="Right view of Mission San Francisco in mosaic coordinates" %}
        <div class="caption">
        Right view of Mission San Francisco in mosaic coordinates
        </div>
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj4/output/landscape/mission_sanfrancisco/topright.png" alt="Top Right view of Mission San Francisco in mosaic coordinates" %}
        <div class="caption">
        Top Right view of Mission San Francisco in mosaic coordinates
        </div>
    </div>
</div>
{% enddetails %}
</div>

<div class="row l-page mx-auto">
{% details **Pong** %}
<div class="row l-page">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj4/output/landscape/pong/left.png" alt="Left view of Pong landscape in mosaic coordinates" %}
        <div class="caption">
        Left view of Pong landscape in mosaic coordinates
        </div>
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj4/output/landscape/pong/center.png" alt="Center view of Pong landscape in mosaic coordinates" %}
        <div class="caption">
        Center view of Pong landscape in mosaic coordinates
        </div>
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj4/output/landscape/pong/right.png" alt="Right view of Pong landscape in mosaic coordinates" %}
        <div class="caption">
        Right view of Pong landscape in mosaic coordinates
        </div>
    </div>
</div>
{% enddetails %}
</div>

<div class="row l-page mx-auto">
{% details **Bay Bridge** %}
<div class="row l-page">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj4/output/landscape/bay_bridge/2.png" alt="View 2 of Bay Bridge in mosaic coordinates" %}
        <div class="caption">
        View 2 of Bay Bridge in mosaic coordinates
        </div>
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj4/output/landscape/bay_bridge/1.png" alt="View 1 of Bay Bridge in mosaic coordinates" %}
        <div class="caption">
        View 1 of Bay Bridge in mosaic coordinates
        </div>
    </div>
</div>
<div class="row l-page">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj4/output/landscape/bay_bridge/3.png" alt="View 3 of Bay Bridge in mosaic coordinates" %}
        <div class="caption">
        View 3 of Bay Bridge in mosaic coordinates
        </div>
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj4/output/landscape/bay_bridge/4.png" alt="View 4 of Bay Bridge in mosaic coordinates" %}
        <div class="caption">
        View 4 of Bay Bridge in mosaic coordinates
        </div>
    </div>
</div>
{% enddetails %}
</div>

<div class="row l-page mx-auto">
{% details **Tokyo Tower** %}
<div class="row l-page">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj4/output/landscape/tokyo_tower/tower.png" alt="Tower view of Tokyo Tower in mosaic coordinates" %}
        <div class="caption">
        Tower view of Tokyo Tower in mosaic coordinates
        </div>
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj4/output/landscape/tokyo_tower/bridge.png" alt="Bridge view of Tokyo Tower in mosaic coordinates" %}
        <div class="caption">
        Bridge view of Tokyo Tower in mosaic coordinates
        </div>
    </div>
</div>
{% enddetails %}
</div>

# Blending

It is a good check to ensure that the images are correctly positioned atop each other at this point. If we simply change the alpha values and combine, we can see that our warping does work.

<div class="row mx-auto">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj4/output/figures/mosaic.jpg" alt="Bay Bridge 1 and 2 Overlap" %}
        <div class="caption">
        Bay Bridge 1 and 2 Overlap
        </div>
    </div>
</div>
However, simply overlaying the images will not suffice. We need to blend the images together to create a seamless mosaic. To do this, we can use a simple linear blending technique. We can calculate the alpha value for each pixel in the mosaic image by taking the distance from the center of the image and dividing by the maximum distance from the center. This will create a gradient that we can use to blend the images together.

<div class="row l-page mx-auto">
{% details **Mission San Francisco** %}
<div class="row l-page">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj4/output/landscape/mission_sanfrancisco/left_distance_transform.jpg" alt="Left view of Mission San Francisco's euclidean distance mask" %}
        <div class="caption">
        Left view of Mission San Francisco's euclidean distance mask
        </div>
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj4/output/landscape/mission_sanfrancisco/middle_distance_transform.jpg" alt="Middle view of Mission San Francisco's euclidean distance mask" %}
        <div class="caption">
        Middle view of Mission San Francisco's euclidean distance mask
        </div>
    </div>
</div>
<div class="row l-page">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj4/output/landscape/mission_sanfrancisco/right_distance_transform.jpg" alt="Right view of Mission San Francisco's euclidean distance mask" %}
        <div class="caption">
        Right view of Mission San Francisco's euclidean distance mask
        </div>
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj4/output/landscape/mission_sanfrancisco/topright_distance_transform.jpg" alt="Top Right view of Mission San Francisco's euclidean distance mask" %}
        <div class="caption">
        Top Right view of Mission San Francisco's euclidean distance mask
        </div>
    </div>
</div>
{% enddetails %}
</div>

<div class="row l-page mx-auto">
{% details **Bay Bridge** %}
<div class="row l-page">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj4/output/landscape/bay_bridge/2_distance_transform.jpg" alt="View 2 of Bay Bridge's euclidean distance mask" %}
        <div class="caption">
        View 2 of Bay Bridge's euclidean distance mask
        </div>
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj4/output/landscape/bay_bridge/1_distance_transform.jpg" alt="View 1 of Bay Bridge's euclidean distance mask" %}
        <div class="caption">
        View 1 of Bay Bridge's euclidean distance mask
        </div>
    </div>
</div>
<div class="row l-page">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj4/output/landscape/bay_bridge/3_distance_transform.jpg" alt="View 3 of Bay Bridge's euclidean distance mask" %}
        <div class="caption">
        View 3 of Bay Bridge's euclidean distance mask
        </div>
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj4/output/landscape/bay_bridge/4_distance_transform.jpg" alt="View 4 of Bay Bridge's euclidean distance mask" %}
        <div class="caption">
        View 4 of Bay Bridge's euclidean distance mask
        </div>
    </div>
</div>
{% enddetails %}
</div>

By stacking each image's distance transform, and normalizing the values by channel, we can now blend the images together to create a seamless mosaic using the channel-wise values as alpha values in a weighted sum.

## Results

<div class="row mx-auto">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj4/output/landscape/mission_sanfrancisco/mosaic_combined.png" alt="Mission San Francisco Mosaic" imagemagick_disabled=true %}
        <div class="caption">
        Mission San Francisco Mosaic
        </div>
    </div>
</div>

<div class="row mx-auto">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj4/output/landscape/pong/mosaic_combined.png" alt="Pong Mosaic" imagemagick_disabled=true %}
        <div class="caption">
        Pong Mosaic
        </div>
    </div>
</div>

<div class="row mx-auto">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj4/output/landscape/bay_bridge/mosaic_combined.png" alt="Bay Bridge Mosaic" imagemagick_disabled=true %}
        <div class="caption">
        Bay Bridge's Mosaic
        </div>
    </div>
</div>

<div class="row mx-auto">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj4/output/landscape/tokyo_tower/mosaic_combined.png" alt="Tokyo Tower Mosaic" imagemagick_disabled=true %}
        <div class="caption">
        Tokyo Tower Mosaic
        </div>
    </div>
</div>

# Rectification

The warping process can be used to rectify images as well. By using the homography matrix to warp the images, we can correct for perspective distortion. This is useful in cases where the camera is not perfectly aligned with the scene, and we want to correct for the distortion. The applications are truly boundless, but one scenario where it is useful is when you must take a picture of an important document, but you are unable to take it head-on. By taking a picture at an angle, the document will appear distorted, and non-legible. By using the homography matrix to warp the image, we can correct for this distortion and make the document legible.

We start by marking the corners of the document, and then the respective location in our new image where these corners should be. In rectification, this is often the corners of the new image, but it can be any location. We then calculate the homography matrix, and using this perform an inverse warp over our new image to fill in the pixels from the original image. Interpolation is extremely important here, as we are filling in pixels that may not have a direct correspondence in the original image.

## Results

Someone left their secret password on the table, but I wasn't able to get a good picture. Let us see if we can rectify the image to make it legible.

<div class="row l-page mx-auto">
{% details **Secret Password** %}
<div class="row l-page">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj4/input/rectification/secret/password.jpg" alt="Original picture of the secret password" %}
        <div class="caption">
        Original picture of the secret password
        </div>
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj4/output/rectification/password_rectified.jpg" alt="Rectified picture of the secret password" %}
        <div class="caption">
        Rectified picture of the secret password
        </div>
    </div>
</div>
<div class="row l-page">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj4/output/figures/password_rectification_comparison.jpg" alt="Comparison of the original and rectified secret password" %}
        <div class="caption">
        Comparison of the original and rectified secret password
        </div>
    </div>
</div>
{% enddetails %}
</div>
This example is quite notable because of the orientation of the document. The document was not only at a steep angle, but it was also not facing the camera. By correctly defining the corners of the document as relative to its orientation, it can be correctly projected into the new image and is now legible without having to mirror the text!

I took a picture of a fun Pikachu graphic, and I want to use it as my background! But the picture is at an angle, and I want to rectify it to make it straight.

<div class="row l-page mx-auto">
{% details **Pokemon Graphic** %}
<div class="row l-page">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj4/input/rectification/poke_graphic/wall.jpg" alt="Original picture of the Pokemon graphic" %}
        <div class="caption">
        Original picture of the Pokemon graphic
        </div>
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj4/output/rectification/wall_rectified.jpg" alt="Rectified picture of the Pokemon graphic" %}
        Rectified picture of the Pokemon graphic
        </div>
    </div>
</div>
<div class="row l-page">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj4/output/figures/wall_rectification_comparison.jpg" alt="Comparison of the original and rectified Pokemon graphic" %}
        <div class="caption">
        Comparison of the original and rectified Pokemon graphic
        </div>
    </div>
</div>
{% enddetails %}
</div>

# Detecting Corner Features

In this section, we computed a Harris matrix for our image, and chose the peaks with a limitation of a growing maximum in a region until we had a reasonable number of peaks. This was necessary to keep our values feasible for vectorization. I found around 45,000 to be the vectorization limit, and about 10,000 was a good compromise between speed and accuracy.

<div class="row l-page">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj4/output/figures/tokyo_tower_harris_values.jpg" alt="Tokyo Tower Harris Values" %}
        <div class="caption">Harris values for Tokyo Tower</div>
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj4/output/figures/tokyo_bridge_harris_values.jpg" alt="Tokyo Bridge Harris Values" %}
        <div class="caption">Harris values for Tokyo Bridge</div>
    </div>
</div>

# ANMS

We then used the ANMS algorithm to select the best corners. This was done by computing the minimum radius before suppression and keeping the top `n` with the highest radius.
There is an optional robustness variable that helps limit suppression, where a value of 1 enforces separation between points but loses some strong corners.

<div class="row l-page">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj4/output/figures/tokyo_tower_ANMS_Robust_Comparison.jpg" alt="Tokyo Tower ANMS Robustness Comparison" %}
        <div class="caption">ANMS Robustness Comparison on Tokyo Tower</div>
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj4/output/figures/tokyo_bridge_ANMS_Robust_Comparison.jpg" alt="Tokyo Bridge ANMS Robustness Comparison" %}
        <div class="caption">ANMS Robustness Comparison on Tokyo Bridge</div>
    </div>
</div>

# Corner Features

Now we can sample a 40x40 region around corners and downsample to 8x8 to get a descriptor for each corner. We assume the images are already axis-aligned.

<div class="row l-page">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj4/output/figures/tokyo_tower_top_20_8x8_Feature_Descriptors.jpg" alt="Tokyo Tower Corner Features" %}
        <div class="caption">Tokyo Tower Corner Features</div>
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj4/output/figures/tokyo_bridge_top_20_8x8_Feature_Descriptors.jpg" alt="Tokyo Bridge Corner Features" %}
        <div class="caption">Tokyo Bridge Corner Features</div>
    </div>
</div>

Here are the descriptors after normalizing and standardizing the values, making them more robust to lighting changes and other factors.

<div class="row l-page">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj4/output/figures/tokyo_bridge_top_20_8x8_Feature_Descriptors_Standardized_Normalized.jpg" alt="Tokyo Bridge Corner Features Normalized/Standardized" %}
        <div class="caption">Tokyo Bridge Corner Features (Normalized/Standardized)</div>
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj4/output/figures/tokyo_tower_top_20_8x8_Feature_Descriptors_Standardized_Normalized.jpg" alt="Tokyo Tower Corner Features Normalized/Standardized" %}
        <div class="caption">Tokyo Tower Corner Features (Normalized/Standardized)</div>
    </div>
</div>

# Matches

We compute the difference between how well a descriptor matches to its closest match and its second-closest match. This helps determine if a match is good, filtering out bad matches.

<div class="row l-page">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj4/output/figures/tokyo_tower_bridge_matches.jpg" alt="Tokyo Tower and Bridge Matches" %}
        <div class="caption">Feature Matches between Tokyo Tower and Bridge</div>
    </div>
</div>

# RANSAC

Finally, we use RANSAC to find a good homography matrix, which allows us to warp the images together and blend them into a seamless mosaic.

<div class="row l-page">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj4/output/RANSAC/tokyotower.png" alt="Tokyo Tower RANSAC" %}
        <div class="caption">RANSAC Result for Tokyo Tower</div>
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj4/output/landscape/tokyo_tower/mosaic_combined.png" alt="Tokyo Tower Mosaic" %}
        <div class="caption">Tokyo Tower Mosaic</div>
    </div>
</div>

<div class="row l-page">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj4/output/RANSAC/baybridge.png" alt="Bay Bridge RANSAC" %}
        <div class="caption">RANSAC Result for Bay Bridge</div>
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj4/output/landscape/bay_bridge_2/mosaic_combined.png" alt="Bay Bridge Mosaic" %}
        <div class="caption">Bay Bridge Mosaic</div>
    </div>
</div>

<div class="row l-page">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj4/output/RANSAC/church.png" alt="Mission San Francisco RANSAC" %}
        <div class="caption">RANSAC Result for Mission San Francisco</div>
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="lazy" zoomable=true path="assets/img/cs180/proj4/output/landscape/mission_sanfrancisco_2/mosaic_combined.png" alt="Mission San Francisco Mosaic" %}
        <div class="caption">Mission San Francisco Mosaic</div>
    </div>
</div>

The coolest thing I learned in this project was definitely the rectification process. It was fascinating to see how a skewed image of a document could be rectified to make it legible—something useful in many applications!
