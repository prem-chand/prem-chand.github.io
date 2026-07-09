---
title: "About"
description: "Prem Chand leads the controls team at Strider Robotics, working on reinforcement learning for legged robots and the sim-to-real gap"
---

<img src="/images/profile.jpg" alt="Prem Chand" class="profile-photo profile-photo-about">

I lead the controls team at [Strider Robotics](https://www.strider-robotics.in/) in Bengaluru, India, where a handful of us get reinforcement learning to work on real legged robots — not just in simulation. A policy that walks beautifully in Isaac Lab usually falls over the first time it meets real friction, backlash, and motor heat, so most of my day-to-day is spent closing that gap: characterizing actuators, randomizing what we can't model perfectly, and building the test rigs and pipelines that tell us whether a policy is actually ready for hardware.

## Background

I earned my Master's in Mechanical Engineering from the University of Delaware, working in the [Dynamic Robot Autonomy Interaction and Locomotion (DRAIL) Lab](https://sites.udel.edu/poulakas/) under Prof. Ioannis Poulakakis. My thesis work was on getting bipedal robots to adapt their gait on the fly — switching strategies mid-stride in response to unknown payloads and disturbances, which turned into papers at ICRA and RA-L. Before grad school, I did a B.Tech in Mechanical Engineering at IIT Bombay, then spent a year as an actuarial analyst in Mumbai before deciding robots were more interesting than loss reserves.

## Interests

A few threads run through most of what I work on:

- **Making RL survive contact with reality** — the actual hard part isn't training a policy, it's everything between a good sim result and a robot you'd trust on stairs.
- **Legged locomotion**, bipedal and quadrupedal — gait design, balance recovery, and what changes when a robot has to react to terrain it's never seen.
- **Instrumentation and test infrastructure** — dynamometers, replay pipelines, and the unglamorous plumbing that turns a fleet of robots into a dataset.

## This Site

I built this site to share technical notes and ideas. Most posts focus on optimization, control theory, and robotics. The site is built with [Hugo](https://gohugo.io) and hosted on GitHub Pages.
