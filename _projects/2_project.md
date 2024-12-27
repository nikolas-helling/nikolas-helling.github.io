---
layout: page
title: Interaction Control for Humanoid Robot
description: Force control for Oversonic Robotics' humanoid robot RoBee (image courtesy of Oversonic Robotics)
img: assets/img/proj2/robee-robot-certificato-768x1152.png
importance: 2
category: # blank in not categorized
redirect: # external link
related_publications: false
published: true
giscus_comments: false

---

During my master’s program, I collaborated with a group of selected students on a semester project with [Oversonic Robotics](https://oversonicrobotics.com/?lang=en), an Italian tech company developing an advanced humanoid robot for industrial and harsh environments. The goal of the project was to design a custom compliance controller for their humanoid RoBee, allowing for intuitive manual guidance and safer, compliant physical interactions with humans. The task involved implementing an inverse kinematics-based admittance control system for the humanoid's arms, starting from a detailed upper-torso model and leveraging MATLAB & Simulink for simulation and control.

To implement the compliant controller, the first step was to develop an inverse differential kinematics algorithm. This algorithm transforms external wrenches (forces and torques) measured in operational space and desired end-effector motions, into corresponding joint level references for the low-level motion controllers. A robust joint control scheme was then designed to achieve precise actuation. On top of this, a high-level admittance controller was developed and fine-tuned using the available model of the humanoid. The resulting control system enables accurate task-space motion planning for the humanoid's arm and can be configured to ensure compliance during physical interaction. Depending on the application, it can be tuned to achieve full compliance (e.g., manual guidance), safe collisions during task execution, or a hybrid approach for adaptive interactions according to a custom switching logic.

<div class="row align-items-center">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/proj2/robee-robot-certificato-768x1152.png" title="Application setup" class="img-fluid rounded z-depth-1" height="55%" width="55%" centered="true" %}
        <div class="caption">
            Humanoid RoBee (image courtesy of <a href='https://oversonicrobotics.com/?lang=en'>Oversonic Robotics</a>).
        </div>
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/proj2/dh_robee.png" title="example image" class="img-fluid rounded z-depth-1" height="60%" width="60%" centered="true"%}
        <div class="caption">
            RoBee's torso DH representation.
        </div>
    </div>
</div>

An inverse differential kinematics algorithm was developed using a closed loop kinematic control architecture with a Jacobian-based structure. This architecture effectively overcomes singular representations and can be tuned for zero steady-state error with a desired convergence rate. The inverse kinematics algorithm demonstrated errors on the order of $$10^{-5}$$ m in position and $$10^{-4}$$ radians in orientation during simulation. These results validate the mathematical accuracy of the algorithm under ideal conditions, providing a strong foundation for further development and real-world tolerances. The inverse kinematic algorithm can be summarized as follows:

$$
\mathbf{q} = \mathbf{J}^*_{\mathbf{A(q)}} \left( \dot{\mathbf{x}}_d + \mathbf{K} (\mathbf{x}_d - \mathbf{x(q)}) \right),
$$

where $$\mathbf{q}$$ are the joint variables, $$\mathbf{J}^*_{\mathbf{A(q)}}$$ is the pseudo-inverse of the analytical Jacobian, $$\dot{\mathbf{x}}_d$$ are the desired end-effector velocities, $$\mathbf{K}$$ is the tunable gain matrix, and $$\mathbf{x(q)}$$ are the current task-space quantities computed through direct kinematics.

The low-level joint control scheme was designed to operate an order of magnitude faster than the high-level force control. To achieve this, a centralized PID scheme is developed ensuring robust disturbance rejection, particularly of joint coupling terms during motion. This low-level control ensures precise tracking of joint positions and velocities while compensating for dynamic disturbances.

At the highest level, an admittance controller is developed in task-space, directly handling the wrenches applied to the end-effector's force sensor. The admittance controller is designed to provide a spring-mass-damper behavior for the robotic arm, where the system’s stiffness, damping, and mass can be dynamically adjusted to suit the application. With the assumption of a fast low-level control, this implicit force controller does not rely on the full dynamic model of the torso and only acts on the end-effector reference trajectories. Given the inverse differential kinematics scheme, the multi-level controller can be implemented and tested directly in task-space coordinates, allowing for easier integration of motion planning while enabling adaptive physical interaction.

<div class="row align-items-center">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/proj2/ee_error_idk.png" title="Application setup" class="img-fluid rounded z-depth-1" height="90%" width="90%" centered="true" %}
        <div class="caption">
            Simulated error of the inverse differential kinematics algorithm.
        </div>
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/proj2/error_adm.png" title="example image" class="img-fluid rounded z-depth-1" height="90%" width="90%" centered="true"%}
        <div class="caption">
            Simulated error of the overall admittance control (manual guidance).
        </div>
    </div>
</div>

The admittance controller allows the robotic arm to achieve a spring-mass-damper response, which can be automatically adjusted for complete compliance (with stiffness set to 0) or smooth collision handling (with stiffness set to a value greater than 0) during task execution.

This flexibility enables the robot to adapt to various interaction scenarios, ensuring safe and efficient coexistence with humans. An example use case for this system could involve the humanoid robot operating autonomously and, in the event of human contact, dynamically adjusting its controller to facilitate manual guidance by the human during a failed task or ensuring safe collision handling for unexpected human-robot interactions. Moreover, an advanced hybrid approach could also enable collaborative manipulation of objects, allowing the humanoid and human to work together in physically demanding tasks, such as shared object carrying, achieving true physical collaboration.

The following media showcases some example interactions with the humanoid's arm when using the developed force control scheme, including a preview of the overall Simulink implementation.

<div class="row align-items-center">
    <div class="col-sm mt-3 mt-md-0">
        {% include video.liquid path="assets/video/f1.mp4" class="img-fluid rounded z-depth-1" controls=true autoplay=true loop=true height="75%" width="75%" centered="true"%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include video.liquid path="assets/video/f2.mp4" class="img-fluid rounded z-depth-1" controls=true autoplay=true loop=true height="75%" width="75%" centered="true"%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/proj2/force.png" title="example image" class="img-fluid rounded z-depth-1" centered="true"%}
    </div>
</div>
<div class="caption">
    Application of linear forces showcasing the arm's compliance achieved with the controller when tuned for manual guidance.
</div>

<div class="row align-items-center">
    <div class="col-sm mt-3 mt-md-0">
        {% include video.liquid path="assets/video/t1.mp4" class="img-fluid rounded z-depth-1" controls=true autoplay=true loop=true height="75%" width="75%" centered="true"%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include video.liquid path="assets/video/t2.mp4" class="img-fluid rounded z-depth-1" controls=true autoplay=true loop=true height="75%" width="75%" centered="true"%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/proj2/torque.png" title="example image" class="img-fluid rounded z-depth-1" centered="true"%}
    </div>
</div>
<div class="caption">
    Application of torques showcasing the arm's compliance achieved with the controller when tuned for manual guidance.
</div>

<div class="row align-items-center">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/proj2/simulink_model.png" title="Application setup" class="img-fluid rounded z-depth-1" height="80%" width="80%" centered="true" %}
        <div class="caption">
            Overall Simulink scheme involving humanoid model, high and low level control system, and simulated interactions.
        </div>
    </div>
</div>
