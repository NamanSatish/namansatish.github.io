---
layout: distill
title: Handshake Bot
description: "Final Project in C106A : Introduction to Robotics"
img: assets/img/ee106a/working_handshake.gif
importance: 6
date: 2024-12-18
category: school
bibliography: empty.bib
toc:
  - name: "Introduction"
  - name: "Design"
  - name: "Implementation"
  - name: "Results"
  - name: "Conclusion"
authors:
  - name: Alexander Lui
    affiliations:
      name: UC Berkeley
  - name: Evan Chang
    affiliations:
      name: UC Berkeley
  - name: Naman Satish
    affiliations:
      name: UC Berkeley
  - name: Saurav Suresh
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

# Introduction

## Goal

Our goal was to create a robot that could perform a handshake with a human.

<div class="row mx-auto" style="text-align: center;">
  <div class="col-sm mt-3 mt-md-0" id="inspirational_quote">
    “humanity lies in the details – a truly human-like robot needs to do the very human task of giving a handshake” - Team 38
  </div>
</div>

Robots may appear inherently inhuman, with mechanical components designed to move through space with precision and speed, therefore they lack the organic movements normal to humans. Our project aimed to bridge this gap by creating a robot that could perform a handshake with a human, turning robotic motion into something organic and human-like. This task is deceptively complex, as it requires a robot to move through it's joint space in a way that is both precise and fluid, while also being able to sense the presence of a human hand and adjust its motion accordingly.

## Challenges

1. **Computer Vision Perception**: The robot must be able to continously sense the presence of a human hand in its workspace, and track the movement of the hand and extract its 3D position in the robot's coordinate frame. This requires a robust computer vision system that can handle occlusions, lighting changes, and other environmental factors, and must be quick enough to provide real-time tracking of the hand.
2. **Path Planning and Motion**: The robot must be able to move its end effector to a desired position in space, whilst also matching the movement of the hand it is tracking. This cannot consist of rotations that would be unnatural in a handshake. Therefore, the robot cannot use normal IK solutions as that would result in unnatural movements, and would not result in a human-like handshake.

# Design

## Design Criteria and Functional Requirements

1. We wanted to utilize a camera and CV system to visually track the human hand and determine the location that the robot should move to based on a specific handshake trajectory.
2. The robot should perform movement within a designated area and should not harm the human, surrounding objects, or itself.
3. The robot should be able to match or mirror the movement of the human hand in order to perform a handshake.
4. The robot should execute paths in a smooth manner such that the movement looks like a natural handshake (i.e. no unnecessary twists and rotations of joints that look unnatural)

## System Architecture

We decided to go with a 4 Module System Architecture:

1. **Hand Tracking**: This module contained our hand tracking algorithm which relied on the MediaPipe's ML model to track the hand in 2D space, and then used the depth information from the RealSense camera to extract the 3D position of the hand in the camera's coordinate frame.
2. **Hand Transformation**: This module transformed the 3D position of the hand from the camera's coordinate frame to the robot's coordinate frame. This was done using predefined transformation parameters that were calculated using QR codes.
3. **Handshake Procedure / Mirroring**: This module was responsible for calculating the desired position of the robot's end effector based on the 3D position of the hand. By reading a predefined trajectory, the published position of the robot's end effector could be updated throughout the handshake procedure.
4. **Path Execution**: This module was responsible for executing the path calculated by the Handshake Procedure module. It construct trajectories based on a collection of waypoints and then executed the trajectory using the MoveIt! library.

## Design Choices and Trade-offs

### Camera Setup

We opted for a single RealSense camera with depth information mounted on a tripod to have a clear view of the scene. This setup allowed us to use a single camera system while still being able to extract 3D coordinates from the camera data. However, we had to position the tripod at a sufficiently large distance to ensure there was enough room for the robot arm to move without obscuring the human's hand. We also attached an AR tag to the camera lens to determine the camera's position from the robot's frame. This setup had some issues, such as inconsistent positioning and uncertainty about the orientation of the camera's axes, which affected the accuracy of the position.

### Hand Tracking

We chose to track the feature point of the wrist, as it provided a consistent point for tracking the human's motion. If we had more time, we could have used additional points to determine the orientation of the hand as well, however because some of the points on the hand are obscured, we could not have simply used the depth map to compute the 3D points, and would have had to use percieved depth information from Mediapipe in combination with the real depth at a certain point to compute 3D coordinates of 3 points on the hand. We streamed the data of the points at the frame rate of the camera, as we believed having more data would be generally helpful for the trajectory and we could figure out how to use it later.

### Handshake Procedure

For simplicity, we mirrored the hand's position across the x, y, and z planes, which allowed us to achieve almost all the functionality we desired for executing a handshake procedure. We did not publish orientation information due to time constraints and focused more on achieving smooth motion.

### Path Execution

Initially, we tested simple linear path following, but it was too restrictive for the points we were feeding in, causing the robot to not move. We then tested simple approaches using the MoveIt controller to reach the published points, but this resulted in strange paths and jittery movement as the robot would stop after each path was executed. To address this, we set simple thresholds on the distance between points and adjusted the sampling rate by taking the median of a window of 5 points to remove outliers and reduce unnecessary movements. We also set joint constraints on the Sawyer arm to prevent unnecessary joint rotations and created bounding box objects to prevent collisions with obstacles. Finally, we used Cartesian path planning to take in a queue of points, which led to some latency in determining the path but resulted in smoother and more consistent movement.

### Impact on Real Engineering Applications

These design choices impacted the project's robustness, durability, and efficiency. The single camera setup provided a cost-effective solution but introduced challenges in positioning and orientation accuracy. The wrist tracking method ensured consistent motion tracking but limited the ability to capture hand orientation. The simplified mirroring approach allowed for functional handshakes but lacked orientation data, which could be critical in more complex applications. The path execution improvements, such as joint constraints and Cartesian path planning, enhanced the smoothness and consistency of the robot's movements, making the system more reliable and efficient in real-world scenarios. However without online path planning, the robot's movements were slower and less responsive, meaning it could not keep up with the human's movements.

# Implementation

## Hardware

We used the following hardware components for our project:

1. **Sawyer Robot by Rethink Robotics**: A versatile and collaborative robot arm. [Sawyer Robot by Rethink Robotics](https://robotsguide.com/robots/sawyer)

2. **Intel RealSense Depth Camera D435**: A depth camera used for tracking the human hand in 3D space. [Intel RealSense Depth Camera D435](https://www.intelrealsense.com/depth-camera-d435/)

<div class="row mx-auto">
  {% include figure.liquid path="assets/img/ee106a/robot_and_camera.png" class="img-fluid rounded z-depth-1" zoomable=true%}
</div>

## Parts Used

- **Sawyer Robot**: For executing the handshake motion.
- **Intel RealSense Depth Camera D435**: For capturing the 3D position of the human hand.
- **Tripod**: To mount the RealSense camera.
- **AR Tag**: For calibrating the camera's position relative to the robot.

## Software

We began our implementation from Lab 5 and Lab 7 codebases which provided us with functionality for inverse kinematics and QR code detection.

The software components we developed include:

1. **cam_transform.py**:

   - Creates a TF Buffer to look up the transform between the robot base frame and the AR marker.
   - Saves this transform to a JSON file.
   - Provides a function for loading a transform from a JSON file.

2. **realsense_tracking.py**:

   - Creates a `hand_pose_tracking` node and `/hand_pose` publisher that publishes a `PointStamped` object (queue size of 1).
   - Starts up the RealSense pipeline for streaming images from the RealSense camera.
   - Creates a `HandLandmarker` MediaPipe object and processes images to find the coordinates of hand landmarks, specifically the wrist point.
   - Converts the wrist point into a 3D coordinate and publishes it to the `/hand_pose` topic.

3. **realtime_move.py**:

   - Creates an `arm_controller` node that subscribes to `/hand_pose`.
   - Loads the transform calculated by `cam_transform.py`.
   - Broadcasts the frame of the camera location for visualization purposes.
   - Performs the transformation from the camera frame to the robot frame of the `PointStamped` read from `/hand_pose` and publishes it to `/hand_pose_base`.

4. **handshake_procedure.py**:

   - Creates a `handshake_procedure` node.
   - Creates a subscriber to `hand_pose_base`.
   - Takes in a command line argument of interactive or the JSON file with procedure configurations (interactive asks for user input of which axes to mirror about and duration of the procedure, creates `ProcedureStep` object and saves to a JSON file).
   - `ProcedureStep`: boolean `axis_x`, boolean `axis_y`, boolean `axis_z`, float `duration`.
   - `HandshakeProcedure`: loops for the duration amount of time and continuously calculates and publishes the desired robot point as a `PointStamped` to the `/robot_point` publisher.

5. **move_hand.py**:
   - Creates an `arm_controller` node.
   - Creates a `MoveGroupCommander` using the RRT planner.
   - Creates `CollisionObject`: creates the collision box objects representing the walls and ceiling of the arm environment.
   - Specifies `JointConstraint` objects and adds them as constraints to the planner.
   - Creates a subscriber to `/robot_point` and runs `pose_callback()`.
   - `pose_callback`: Reads in the desired point and adds the point to the waypoints queue if it meets the specified threshold for L2 distance away.
   - Attempts to create a plan using `compute_cartesian_path` with the queue of waypoints (avoid_collision=True), and executes this plan.

<div class="row mx-auto">
  {% include figure.liquid path="assets/img/ee106a/rqt_graph.png" class="img-fluid rounded z-depth-1" zoomable=true %}
</div>

## System Workflow

1. **Start Up**: Start the AR tracking packages and joint action server.
2. **Calibrate Camera**: Move the RealSense camera with the attached AR tag into the frame of the right arm camera. Run `cam_transform.py` to determine the transform from the camera frame to the robot frame and save the transform as a JSON file.
3. **Hand Tracking**: Remove the AR tag and run `realsense_tracking.py` to start an interactive window displaying the hand features being tracked and published to the `/hand_pose` topic. Additionally run `realtime_move.py` to transform the hand pose from the camera frame to the robot frame and publish it to the `/hand_pose_base` topic.
4. **Handshake Procedure**: Run `handshake_procedure.py` and specify the desired handshake configuration (i.e., which axes to mirror about). This procedure is saved to a JSON file, which can be specified as a command line argument to skip this step in the future. Center the hand in the window and establish the point to mirror about.
5. **Path Execution**: Run `move_hand.py` to start the path planner and movement node. The Sawyer arm will start following/mirroring hand movements.

# Results

Our project was able to successfully track a human hand in 3D space and mirror its movements with the Sawyer robot. The robot was able to perform a handshake with a human, moving its end effector in a smooth and natural manner. It was both capable of tracking human hand movements and mirroring them in order to create a handshake motion. The robot was able to execute the handshake procedure within a designated area and did not harm the human, surrounding objects, or itself.

<div class="row l-page mx-auto">
  <div class="col-sm mt-3 mt-md-0">
  {% include figure.liquid path="assets/img/ee106a/working_handshake.gif" class="img-fluid rounded z-depth-1" zoomable=true %}
  </div>
  <div class="col-sm mt-3 mt-md-0">
  {% include figure.liquid path="assets/img/ee106a/robot_mirroring.gif" class="img-fluid rounded z-depth-1" zoomable=true %}
  </div>
</div>

# Conclusion

### Results

Our results largely matched our design criteria, except for some minor inaccuracies and latency. As shown in the video, we were able to make the Sawyer robot mirror and track in such a way that it could perform certain kinds of handshakes with a human partner. The movement was somewhat slow due to the time taken to perform real-time path planning. The tracking could also be slightly inaccurate due to human error during camera calibration or if the camera was moved.

### Difficulties

We encountered difficulties calibrating the transform from the camera frame to the robot base frame. This was caused by the poor quality of the robot wrist camera, bad lighting, glare from the camera, and interference from other AR markers. We overcame these problems by moving the wrist camera closer, turning off the RealSense camera, and recalibrating until the transform stabilized. We also had issues with the path planning settling on paths that were unnecessarily long or dangerous. We solved this by adding collision boxes to bound the arm’s workspace and by adding joint constraints. Additionally, we used a Cartesian path planner, which planned a path based on a set of waypoints, producing more sensible paths. We also encountered a delay in the robot’s movement due to the time spent on path planning.

### Flaws and Hacks

Our method for transforming hand points in the camera frame to the robot base frame is somewhat improvised. Ideally, we would streamline a process where the Sawyer robot automatically computes this transform without the need for an AR marker and manual calibration. To reduce latency caused by path planning, we filtered out points that were close together to lessen the load on the path planner. While effective to some extent, a more robust solution would involve optimizing the path planning process itself to enhance speed. Even with filtering, delays persist, requiring the human partner to move slowly for successful tracking and mirroring. With additional time, we would focus on speeding up path planning and improving the responsiveness of the robot.

# Team

<div class="row mx-auto">
  <div class="col-sm mt-3 mt-md-0">
  {% include figure.liquid path="assets/img/ee106a/team_photo.jpg" class="img-fluid rounded z-depth-1" zoomable=true %}
  </div>
</div>

## Members

1. Saurav Suresh: Senior majoing in Computer Science and Math.
2. Naman Satish: Senior majoring in Electrical Engineering and Computer Science, with a focus in Computer Vision.
3. Alexander Lui: Senior majoring in Computer Science.
4. Evan Chang: Senior majoring in Electrical Engineering and Computer Science.

## Contributions

1. Saurav Suresh: Realsense Tracking
2. Naman Satish: Handshake Procedure
3. Alexander Lui : Move Hand
4. Evan Chang: Cam Transform

# Additional Materials

# Code

[Handshake Bot Github](https://github.com/NamanSatish/HandshakeBot)

<!--

https://drive.google.com/file/d/1KpU2-hxeUGlkkliAK7MLH2-zhfnthcEd/view?usp=drive_link

https://drive.google.com/file/d/1uUCDI7Fie_2VFIInZSgS9vOE47zhxm4-/view?usp=drive_link

-->

[Software Demo Video](https://drive.google.com/file/d/1uUCDI7Fie_2VFIInZSgS9vOE47zhxm4-/view?usp=drive_link)

[Handshake Video](https://drive.google.com/file/d/1KpU2-hxeUGlkkliAK7MLH2-zhfnthcEd/view?usp=drive_link)

[Project Presentation](https://docs.google.com/presentation/d/1GoCEK2VPXtKTkxlpo6xZE2zSntcXuxX6zAFLYc7gf7s/edit?usp=sharing)
