---
layout: page
title: Master Thesis Research
description: Human behavior prediction for human-robot collaboration, using Bayesian inference and low-level human motion generation
img: assets/img/proj1/use_case_screenshot.png
importance: 1
category: # blank in not categorized
redirect: # external link
related_publications: true
published: true
giscus_comments: false

---

During my master’s thesis at [MERLIN Lab](https://merlin.deib.polimi.it/), I carried out robotics research under the supervision of Prof. Paolo Rocco. My research focused on human behavior prediction for collaborative robotics applications in industrial assembly. This research addresses a critical need in the field of collaborative robotics, where improving human-robot interaction is essential for advancing efficiency, safety, and adaptability in shared workspaces. Traditional collaboration approaches often struggle to integrate predicted human intentions in dynamic environments, limiting their advantages in real-world industrial settings. By bridging this gap, my work aimed to prove the enhancements obtained by applying short-term human intention prediction in terms of collaboration fluency and robot intuitiveness.

I developed a “Robust Bayesian Goal Reaching Inference Engine for Intent Prediction in Human-Robot Collaboration”, which was validated using a collaborative dual-arm robot (ABB’s YuMi) and external participants. The model accurately predicts the objects grasped by a human inside a collaborative workspace, combining upper body tracking, limb motion generation, and Bayesian target prediction. The experimental campaign demonstrated significant collaboration improvements, including a 30% reduction in cycle time for shared assembly tasks, more natural and intelligent human-robot interactions, and increased robustness compared to prior works in the literature {% cite paperICRA2023 casalino2018operator Zanchettin2017 %}.

<div class="row align-items-center">
    <div class="col-md-6">
        {% include figure.liquid loading="eager" path="assets/img/proj1/setup.gif" title="Application setup" class="img-fluid rounded z-depth-1" height="70%" width="70%" centered="true" %}
        <div class="caption">
            Working setup of the prediction model.
        </div>
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/proj1/use_case_screenshot.png" title="example image" class="img-fluid rounded z-depth-1" %}
        <div class="caption">
            Shared human-robot assembly experiment.
        </div>
    </div>
</div>

The proposed model is built within a Bayesian inference framework, aiming for a generalized approach that does not rely on any specific training and is computationally efficient. First, a hand motion generation problem is solved, whose output then serves as input to the Bayesian prediction layer. The model aims to predict the most likely reaching target for a user by inferring from the last available 3D arm data (positions and tangents). This allows for automatic replanning of robot actions during shared human-robot tasks, accounting for the inherent uncertainty of the human and increasing the overall safety of operation.

The captured human arm's movement is spatially discretized, with updates occurring only during significant motion, helping to focus on reaching intentions and ignore stationary phases. The path generation model employs a minimum curvature approach, optimizing hand motion by minimizing the overall path curvature, subject to boundary conditions like current position and tangent. Solving a convex quadratic optimization results in smooth polynomial paths for each goal object, which are then used as predictive information in the inference step.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/proj1/traj_scheme.png" title="example image" class="img-fluid rounded z-depth-1" %}
        <div class="caption">
            Path generation example.
        </div>
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/proj1/tangents_scheme.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
        <div class="caption">
            Human arm feature extraction.
        </div>
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/proj1/u_goal.png" title="example image" class="img-fluid rounded z-depth-1" %}
        <div class="caption">
            Robustness to prediction uncertainty.
        </div>
    </div>
</div>

The underlying probabilistic model uses Bayesian inference to update the posterior probability distribution over a set of suitable goals, based on measured and predicted human features. The problem is framed as a classification task, aiming to identify the most likely goal by updating the probability distribution recursively using a Bayes classifier. The probability update is divided into prediction and measurement steps. Markov’s assumption simplifies the model's structure, ensuring that the distribution depends only on the latest measurements, implicitly favoring more reactive predictions.

The prior distribution is computed using a transition model for intentions, which defines the probability of the operator switching goals between samples. In the measurement step, the posterior distribution is updated using new captured data and predicted paths. The final predicted goal is the one with the highest posterior probability, provided it exceeds a certain threshold. This pipeline allows for efficient short-term motion prediction with reduced computational complexity. Given the captured features $$\mathbf{\hat{X}}$$, the prior and posterior distributions are computed over the set of known goals $$\mathbf{G}$$ with a procedure as the following:

$$
P^{prior}_{k+1}(\mathbf{g}) = \mathbb{P}\left(\mathbf{g}^{k+1} \mid \mathbf{\hat{X}}_{k}\right)=(\cdots)= \sum_{\mathbf{g}^k \in \mathbf{G}} P^{post}_{k}(\mathbf{g})\mathbb{P}\left(\mathbf{g}^{k+1} \mid \mathbf{g}^k\right),
$$

$$
\label{eq:cauchy-schwarz}
P^{post}_{k+1}(\mathbf{g}) = \mathbb{P}\left(\mathbf{g}^{k+1} \mid \mathbf{\hat{X}}_{k}, \mathbf{\hat{X}}_{k+1}\right) = (\cdots) = \eta_{k+1}L_{k+1}(\mathbf{g})P^{prior}_{k+1}(\mathbf{g})
$$

The likelihood function evaluates how well each potential goal aligns with current and predicted human movements. Key measures include the angle between predicted and measured hand tangents , the distance between the hand and each goal, and the alignment of the shoulder’s motion with the goal direction for certain movements. These measures are combined into a likelihood function modeled as a mixture of normal distributions. Weights are dynamically adjusted to favor angular measures for distant goals and distance measures for nearby ones, increasing robustness in case of aligned goals. Moreover, only goals within the hand’s general motion direction are considered, while entropy is used to handle uncertainty, increasing the probability of an unknown goals when none of the current goals seem likely. This approach ensures smoother and more accurate predictions while addressing variability and ambiguous cases.

<div class="row align-items-center">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/proj1/post_dist.jpg" title="example image" class="img-fluid rounded z-depth-1" height="95%" width="95%" centered="true" %}
        <div class="caption">
            Example of prediction model's output.
        </div>
    </div>
        <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/proj1/cycle_time.png" title="example image" class="img-fluid rounded z-depth-1" height="70%" width="70%" centered="true" %}
        <div class="caption">
            Assembly cycle time improvements.
        </div>
    </div>
</div>

The model’s prediction performance was tested on a comprehensive dataset of recorded human paths, achieving high accuracy (95%) and early goal prediction, with confirmation happening at only around 1/4 of the full path. Incorporating shoulder information improved prediction speed (+9%) and reduced variability (−13%), enabling more robust predictions. The model also successfully identified unknown goals in 89% of tests, demonstrating its adaptability.

In a collaborative robotics use case, the predictive model significantly reduced assembly cycle times (−30%) compared to non-predictive setups. Participants in experimental trials also noted improved task fluency and robot intuitiveness when the model was used. These results validate the model’s robustness and effectiveness for real-world human-robot collaboration, enhancing efficiency and interaction quality. This work was a great opportunity to apply HRI research and appreciate the potential of novel interactions between humans and robots, paving the way for future works that develop adaptive deep learning algorithms to enable seamless HRI in more complex and unstructured environments.

<div class="row align-items-center">
    <div class="col-sm mt-3 mt-md-0">
        {% include video.liquid path="assets/video/coll_assembly_slim.mp4" class="img-fluid rounded z-depth-1" controls=true autoplay=false height="60%" width="60%" centered="true"%}
        <div class="caption">
            Full collaborative assembly example using the prediction model.
        </div>
    </div>
</div>
