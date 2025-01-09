---
layout: page
title: Rotational Motion Control for Quadcopter
description: Roll-pitch-yaw model-based control for a custom quadcopter, using PID, LQR, and MPC controllers
img: assets/img/proj3/drone_no_bg.png
importance: 3
category: # blank in not categorized
redirect: # external link
related_publications: false
published: true
giscus_comments: false

---

For a semester project at the Automation and Control Laboratory at Politecnico di Milano, I was assigned with a team to a project aimed at developing rotational motion control for a custom quadcopter. The project involved implementing model-based controllers and comparing their performance in terms of the autonomous orientation of the drone, using MATLAB and Simulink for control and simulation, and custom Arduino plugins for deploying code to the on-board ArduCopter. The goal was to build a robust control system from the ground up, starting from non-linear modeling and system identification, to control and performance testing on the real quadcopter. 

After estimating an accurate model of the quadcopter's dynamics, a set of model-based controllers were designed and tested in simulation. The developed controllers were then deployed on the real system and fine-tuned through hands-on experiments, achieving robust control performance and satisfactory sim-to-real transfer. While the project is not fully reproducible due to our custom drone configuration, most of the base simulation code is available on [GitHub](https://github.com/nikolas-helling/drone_attitude_control.git). The repository includes base templates for different quadcopter models, system identification scripts and model-based controllers, including PID, LQR and MPC. All functions can be easily adapted to similar quadcopter architectures and used as foundation for developing a model-based attitude control system for a custom setup.

<div class="row align-items-center">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/proj3/drone.jpg" title="example image" class="img-fluid rounded z-depth-1" height="70%" width="70%" centered="true" %}
        <div class="caption">
            Custom constrained quadcopter setup.
        </div>
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/proj3/drone_springs.jpg" title="example image" class="img-fluid rounded z-depth-1" height="70%" width="70%" centered="true"%}
        <div class="caption">
            Spring setup of experiments.
        </div>
    </div>
</div>

Our setup was composed of a custom lightweight quadcopter with a cross-like symmetrical structure, attached to a 3 d.o.f. rotational hinge constraining all its linear motions and isolating roll-pitch-yaw maneuvers. The control system was deployed to the on-board pc, equipped with a basic 50Hz IMU and 4 ESC's controlling 4 PMDC motors through suitable PWM signals. Sensor readings processed by the controllers were transformed into PWM control inputs (duty cycles of PWM signals) fed to the ESC's to obtain the desired reference trajectory following. Having no direct measurement of motor speeds added additional complexity in the control system design.

Given the simple sensor setup and the few available technical characteristics of the drone, an important part of the project was estimating missing parameters such as the inertia tensor, aerodynamic properties and geometrical values. Additionally, the control transformation between torques and thrusts applied on the system and the relationship with motor dynamics were also unknown. This part was tackled using both custom system identification approaches and standard black-box and grey-box estimation algorithms.

<div class="row align-items-center">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/proj3/drone_osc.png" title="example image" class="img-fluid rounded z-depth-1" height="70%" width="70%" centered="true" %}
        <div class="caption">
            Damped oscillations for inertia tensor identification.
        </div>
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/proj3/n4sid.png" title="example image" class="img-fluid rounded z-depth-1" height="100%" width="100%" centered="true"%}
        <div class="caption">
            Example output of black-box linear model identification (n4sid).
        </div>
    </div>
</div>

In particular, we relied on a physical spring setup combined with a set of damped oscillations experiments to estimate roughly the roll and pitch inertia in the body frame. Similarly, the setup was used to estimate the relationship between the control signal (the manipulated variable) and the corresponding aerodynamic thrust for each motor. Using geometrical considerations and assuming the drone in a hover condition, we also estimated the relationship between thrusts, motor torques and torques generated around the body frame. While this procedure was empirical, it produced reasonable estimates to be used in the model-based estimation part.

We developed nonlinear dynamic models using various assumptions, from considering gyroscopic effects to center of mass displacement from the hinge and motor dynamics. The models were used in MATLAB with nonlinear grey-box algorithms (e.g. nlgreyest) to estimate missing parameters, such as yaw and rotor inertia, thrust and torque coefficients, and geometric lengths. The estimated parameters were sufficient for developing model-based controllers, but the estimation proved to be very complex given the many non-idealities such as disturbances from reaction forces at the hinge, an unstable system which required closed loop excitation experiments, and a few reliable initial parameter guesses.

A set of black-box estimation approaches (e.g. n4sid, ssest) also proved to be suitable for estimating the matrices of the linearized system around the equilibrium. Controllers based of the identified state space model were satisfactory enough for simple trajectories not far from the stability condition, while more complex controllers (e.g. MPC, LQR) and models were better suited for generic coupled roll-pitch-yaw maneuvers.

<div class="column">
    <div class="row-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/proj3/step.png" title="example image" class="img-fluid rounded z-depth-1" height="80%" width="80%" centered="true" %}
    </div>
    <div class="row-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/proj3/sine.png" title="example image" class="img-fluid rounded z-depth-1" height="80%" width="80%" centered="true"%}
    </div>
</div>
<div class="caption">
    Sine and step reference trajectory following.
</div>

The control system was designed and tuned in Simulink using custom MATLAB controller implementations (see [here](https://github.com/nikolas-helling/drone_attitude_control.git)). The set of model-based controllers were tested on the real quadcopter considering both the free system (3 d.o.f.) and each orientation component independently.

The implemented controllers include: decentralized PID's, static pole placement (PP), static LQR, gain-scheduled LQR, gain-scheduled MPC. By static we mean that the controllers are designed using the estimated linearized model of the system, while gain-scheduled means the controllers are adapted in real-time depending on the operating point, thus being more accurate but without the need of a full nonlinear control architecture. This simplification allows for better performance with respect to linear-based controllers but also significantly lowers real-time computational cost, which is critical considering the simple on-board processor.

A series of control results from real experiments are shown in the following media. The developed control pipeline allows for effective stabilization and satisfactory trajectory tracking for various aggressive signals, such as step references. Moreover, the controllers are robust to external disturbances, as is shown in some of the videos below. Paired with a more reliable estimation setup and enlarged with linear motion control, this work provides a strong base for future flight control architecture that can achieve aggressive mid-flight maneuvers, especially for rotational motions. This project was a great opportunity for collaborating to achieve a complete autonomous system from start to finish, applying interdisciplinary control knowledge on a real-world system.

<div class="row align-items-center">
    <div class="col-sm mt-3 mt-md-0">
        {% include video.liquid path="assets/video/ctrl_dist1.mp4" class="img-fluid rounded z-depth-1" controls=true autoplay=true loop=true height="80%" width="80%" centered="true"%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include video.liquid path="assets/video/ctrl_dist2.mp4" class="img-fluid rounded z-depth-1" controls=true autoplay=true loop=true height="80%" width="80%" centered="true"%}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include video.liquid path="assets/video/ctrl_dist3.mp4" class="img-fluid rounded z-depth-1" controls=true autoplay=true loop=true height="80%" width="80%" centered="true"%}
    </div>
</div>
<div class="caption">
    Some examples of the quadcopter's orientation control, considering also significant external disturbances.
</div>

<div class="row align-items-center">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/proj3/comparison.png" title="Application setup" class="img-fluid rounded z-depth-1" height="80%" width="80%" centered="true" %}
        <div class="caption">
            Comparison of different model-based controllers tuned in simulation for trajectory tracking.
        </div>
    </div>
</div>

<div class="row align-items-center">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/proj3/dist.png" title="Application setup" class="img-fluid rounded z-depth-1" height="80%" width="80%" centered="true" %}
        <div class="caption">
            Example roll-pitch-yaw experiment results considering also manual disturbances after the trajectory.
        </div>
    </div>
</div>