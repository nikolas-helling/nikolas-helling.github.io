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

To implement the compliant controller, the first step was to develop an inverse differential kinematics algorithm capable of transforming external wrenches (forces and torques) measured in operational space, as well as desired end-effector motions, into corresponding joint level references for the low-level motion controllers. A robust joint control scheme was then designed to achieve precise actuation. On top of this base, a high-level admittance controller was developed and fine-tuned using the available model of the humanoid. The resulting control system enables accurate task-space motion planning and can be tuned to achieve complete compliance (manual guidance), safe collisions during task execution, or a hybrid approach according to a custom switching logic based on the application.

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

The first task was solved developing an inverse differential kinematics algorithm, which was based on a closed loop kinematic control architecture that is used to overcome singular representations and can be tuned for zero-steady state error with a desired convergence rate. The inverse kinematics algorithm achieved errors on the order of $$10^{-5}$$ m in position and $$10^{-4}$$ radians in orientation during simulation. These results validate the mathematical accuracy of the algorithm under ideal conditions and provide a strong foundation for further development and real-world tolerances. The inverse kinmatic algorithm can be summarized by the following:

$$
\mathbf{q} = \mathbf{J}^*_{\mathbf{A(q)}} \left( \dot{\mathbf{x}}_d + \mathbf{K} (\mathbf{x}_d - \mathbf{x(q)}) \right),
$$

where $$\mathbf{q}$$ are the joint variables, $$\mathbf{J}^*_{\mathbf{A(q)}}$$ is the pseudo-inverse of the Jacobian, $$\dot{\mathbf{x}}_d$$ are the desired end-effector velocities, $$\mathbf{K}$$ is the tunable gain matrix, and $$\mathbf{x(q)}$$ are the current task-space quantities computed through direct kinematics.

The low-level joint control scheme is designed to be an order of magnitude faster than the high-level force control. Thus, a centralized PID scheme is developed accounting for robust disturbance rejection of joint coupling during motion. The admittance controller is then developed in task-space, allowing for direct handling of wrenches applied on the end-effector's force sensor. Based on the tuning of the admittance controller this allows to achieve a spring-mass-damper response of the robotic arm, which can be automatically tuned for complete compliance (stiffness set to 0), or smooth collision (stiffness different from 0) during task execution.

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

An example use case for this system could involve the humanoid robot autonomously grasping an object and, in the event of human contact, dynamically adjusting its controller to facilitate manual guidance by the human during a failed task or ensuring safe collision handling for unexpected human-robot interactions. Moreover, an advanced hybrid approach could also enable collaborative manipulation of objects, allowing the humanoid and human to work together in physically demanding tasks, such as shared object carrying, achieving true physical collaboration.

The following media showcases some example interactions with the humanoid's arm when using the developed force control scheme, including a preview of the Simulink implementation.

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
