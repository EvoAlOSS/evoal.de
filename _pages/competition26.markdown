---
title: "Explainable ride-sharing optimisation for sustainable traffic organisation"
permalink: /pages/competition26
date: 2026-01-06T13:00:00+02:00
author: Bernhard Berger
author_profile: false
toc: true
toc_sticky: true
layout: single
sidebar:
  nav: "competition"
---

[![Publications and Research Topics](/assets/images/banner-competition.jpg){: .full}](/pages/competition26)


## Updates
* **2026-03-15** Added tutorial. 
* **2026-01-06** Put initial competition page online. 
* **2026-02-02** First description of metrics online. 
* **2026-02-26** First description of architecture online.
* **2026-03-13** Description of events online

## Important
We will upload details on the challenge, as well as, example scenarios starting on February, $2^{nd}$. We will add the documentation, Docker containers, and example scenarios to our [GECCO'26 Competition GitLab project](https://gitlab.informatik.uni-bremen.de/evoal/vehicle-routing-problem/gecco26-competition). If you are interested in updates, you can join our Discord Server &mdash; `ugETXPP2`. There, we will post information on updates.

## Motivation
Optimization is central to real-world logistical challenges such as resource allocation and scheduling. While techniques for static optimization have been studied for several decades, today’s fast-paced world demands online and dynamic algorithms that can adapt to changing conditions while still delivering near-optimal solutions. In contrast to offline algorithms, which have full knowledge of the input in advance, online algorithms receive input sequentially over time. Decisions must be made incrementally, with no or incomplete knowledge of the future.

On-demand ride-hailing and ride-sharing services are a prime example of online, real-time optimization challenges that underlie dynamic changes in the environment. Platforms such as Uber and Lyft have developed applications to connect customers with drivers via a mobile application, removing the need for curbside hailing and complementing public transport options to offer customers a comfortable, affordable, and convenient means of travel. With the option of ride-sharing, these benefits are retained while also allowing a more sustainable means of transport.

Ride-hailing and ride-sharing services employ a fleet of drivers to serve dynamically arising customer requests. These requests are not known in advance by the fleet operator, but users expect quick service. At the same time, businesses must strike a balance between customer satisfaction, minimizing operational costs to maximize profits, and prioritizing sustainability. This problem is NP-hard and inherently very complex, especially in real-world scenarios, where hundreds of requests must be coordinated quickly.

In our challenge, we supply the participants with a ‘plug-and-play’ setup environment for the ride-hailing and ride-sharing problems. Participants are requested to develop their own optimization algorithms for solving the challenge. Algorithms can be easily implemented within the setup environment by extending existing interfaces, which abstract away from the fine details of the simulation and define core functionalities an algorithm must provide. This allows participants to focus on the optimization logic without getting bogged down by the details of the simulation itself.

## Tracks
There will be four competition tracks, each corresponding to a specific problem and environment. The ride-hailing problem is a specific case of ride-sharing in which each vehicle in the fleet can pick up at most one customer. In ride-sharing, on the other hand, vehicles may pick up multiple customers at once. In the dynamic environment, changing traffic conditions are simulated, such as traffic jams and spontaneous road closures, which affect driving times during optimization. In the static environment, distances and times remain the same. Participants are encouraged to choose one or more tracks based on their interests, previous experience, and abilities:

* Track 1: Ride-hailing in a static environment.
* Track 2: Ride-hailing in a dynamic environment.
* Track 3: Ride-sharing in a static environment.
* Track 4: Ride-sharing in a dynamic environment.

Participants will have the opportunity to test their algorithms on benchmark request sequences and compare their performance to other submissions. At competition close, submissions will be evaluated on a private set of test instances, and the top submission in each track will be recognized with a cash reward.



## Submission Process
We will publish the final competition resources on March 15th, 2026, 23:59 (AOE), and the submission deadline is June 14th, 2026, 23:59 (AOE). In the meantime, we will polish and document our material and make it available step by step. We will mention updates in the update section. We encourage you to make your competition entry publicly available on [arXiv](https://arxiv.org) and [Zenodo](https://zenodo.org), but you can also submit it in a non-public form.

For submission, please prepare a Docker container that we can build and use to evaluate your submission. We will post more details on this in the future.


## Prizes
We will award 500€ of prize money to be divided among the top contestants.


## Evaluation Environment
We provide participants with a simulation framework that visualizes their algorithm's behaviour. Below, you can see a video demonstrating the simulation environment. It additionally allows participants to 'play around' a bit, before testing on the provided benchmark request sequences. 

<video width="900" height="720" controls>
  <source src="../assets/videos/frontend_demo.mp4" type="video/mp4">
</video>



## Evaluation Criteria

This competition evaluates optimization algorithms for ride-hailing and ride-sharing systems. The goal is to fulfil passenger requests efficiently by balancing service quality, time efficiency, and operational costs. The performance of an algorithm is assessed based on a multi-objective problem where one metric from each category—Success-based, Time-based, and Cost-based—is selected for final judgment. The remaining metrics are provided for comprehensive evaluation and to assist contestants in the analysis and modification of their algorithms.

The simulation considers a fixed operational period of **24 hours**. Any request that is not fully fulfilled by the end of this 24-hour period is automatically considered unsuccessful, modelling the operational constraints of a typical taxi company.

More details on the metrics used can be found on the [Metrics page](/docs/competition26/metrics). 

***

## How to get started
We will provide text and video tutorials to help participants get started and ease troubleshooting. These will be released on this page pending competition acceptance. Additionally, we'll hold an online Q&A session at the start of the competition. Participants will be able to contact us via mailing list if they encounter any problems.



## Acknowledgements
The challenge is supported by the DFG-funded [Research Training Group CAUSE](https://rtg-cause.github.io).
