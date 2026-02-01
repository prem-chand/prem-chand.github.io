---
title: "Publications"
description: "Research publications in robotics, control systems, and reinforcement learning"
---

## Peer-Reviewed Publications

### Interactive Dynamic Walking: Learning Gait Switching Policies with Generalization Guarantees
**P. Chand**, S. Veer, I. Poulakakis
*IEEE Robotics and Automation Letters, vol. 7, no. 2, Jan 2022, 4149-4156.*
In this letter, we consider the problem of adapting a dynamically walking bipedal robot to follow a leading co-worker based on physical interaction. Our approach relies on switching among a family of Dynamic Movement Primitives (DMPs) as governed by a supervisor. We train the supervisor to orchestrate the switching among the DMPs in order to adapt to the leader’s intentions, which are only implicitly available in the form of interaction forces. The primary contribution of our approach is that it furnishes certificates of generalization to novel leader intentions for the trained supervisor. This is achieved by leveraging the Probably Approximately Correct (PAC)-Bayes bounds from generalization theory. We demonstrate the efficacy of our approach by training a neural-network supervisor to adapt the gait of a dynamically walking biped to a leading collaborator whose intended trajectory is not known explicitly.

[Download PDF](/publications/Chand_Veer_Poulakakis___2022___Interactive_Dynamic_Walking_Learning_Gait_Switching_Policies_with_Generalization_Guarante.pdf)

---

### An Adaptive Supervisory Control Approach to Dynamic Locomotion under Parametric Uncertainty
**P. Chand**, S. Veer, I. Poulakakis
*IEEE International Conference on Robotics and Automation (ICRA) 2020.*

This paper presents an adaptive control scheme
for robotic systems that operate in the face of—potentially
large—structured uncertainty. The proposed adaptive con-
troller employs an on-line supervisor that utilizes logic-based
switching among a finite set of controllers to identify uncertain
parameters, and adapt the behavior of the system based on a
current estimate of their value. To achieve this, the adaptive
control approach in this paper combines on-line parameter
estimation and feedback control while avoiding some of the
inherent difficulties of classical adaptive control strategies.
Furthermore, the proposed supervisory control architecture
is modular as it relies on established “off-the-shelf” feedback
control law and estimator design approaches, instead of customizing the overall design to the specific requirements of an
adaptive control algorithm. We demonstrate the efficacy of the
method on the problem of a dynamically-walking bipedal robot
delivering a payload of unknown mass, and show that, by
switching to the controller that is the “best” according to a
current estimate of the uncertainty, the system maintains a low
energy cost during its operation.

[Download PDF](/publications/Chand_Veer_Poulakakis___2020___An_Adaptive_Supervisory_Control_Approach_to_Dynamic_Locomotion_under_Parametric_Uncertain.pdf)
