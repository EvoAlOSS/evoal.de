---
title: "Explainable ride-sharing optimisation for sustainable traffic organisation"
permalink: /pages/competition26
date: 2026-01-01T13:00:00+02:00
author: Bernhard Berger
author_profile: false
toc: true
toc_sticky: true
---

[![Publications and Research Topics](/assets/images/banner-competition.jpg){: .full}](/pages/competition26)

## Motivation
Optimisation is central to real-world logistical challenges such as resource allocation and scheduling. While techniques for static optimization have been studied for several decades, today’s fast-paced world demands online and dynamic algorithms that can adapt to changing conditions while still delivering near-optimal solutions. In contrast to offline algorithms, which have full knowledge of the input in advance, online algorithms receive input sequentially over time. Decisions must be made incrementally, with no or incomplete knowledge of the future.

On-demand ride-hailing and ride-sharing services are a prime example of online, real-time optimization challenges that underlie dynamic changes in the environment. Platforms such as Uber and Lyft have developed applications to connect customers with drivers via a mobile application, removing the need for curbside hailing and complementing public transport options to offer customers a comfortable, affordable, and convenient means of travel. With the option of ride-sharing, these benefits are retained while also allowing a more sustainable means of transport.

Ride-hailing and ride-sharing services employ a fleet of drivers to serve dynamically arising customer requests. These requests are not known in advance by the fleet operator, but users expect quick service. At the same time, businesses must strike a balance between customer satisfaction, minimizing operational costs to maximize profits, and prioritizing sustainability. This problem is NP-hard and inherently very complex, especially in real-world scenarios, where hundreds of requests must be coordinated quickly.

In our challenge, we supply the participants with a ‘plug-and-play’ setup environment for the ride-hailing and ride-sharing problems. Participants are requested to develop their own optimisation algorithms for solving the challenge. Algorithms can be easily implemented within the setup environment by extending existing interfaces, which abstract away from the fine details of the simulation and define core functionalities an algorithm must provide. This allows participants to focus on the optimisation logic without getting bogged down by the details of the simulation itself.

## Tracks
There will be four competition tracks, each corresponding to a specific problem and environment. The ride-hailing problem is a specific case of ride-sharing in which each vehicle in the fleet can pick up at most one customer. In ride-sharing, on the other hand, vehicles may pick up multiple customers at once. In the dynamic environment, changing traffic conditions are simulated, such as traffic jams and spontaneous road closures, which affect driving times during optimisation. In the static environment, distances and times remain the same. Participants are encouraged to choose one or more tracks based on their interests, previous experience, and abilities:

* Track 1: Ride-hailing in a static environment.
* Track 2: Ride-hailing in a dynamic environment.
* Track 3: Ride-sharing in a static environment.
* Track 4: Ride-sharing in a dynamic environment.

Participants will have the opportunity to test their algorithms on benchmark request sequences and compare their performance to other submissions. At competition close, submissions will be evaluated on a private set of test instances, and the top submission in each track will be recognized with a cash reward.

## Evaluation Environment
We provide participants with a simulation framework that visualises their algorithm's behaviour. Below, you can see a video demonstrating the simulation environment. It additionally allows participants to 'play around' a bit, before testing on the provided benchmark request sequences. 

<video width="1000" height="800" controls>
  <source src="../assets/videos/frontend_demo.mp4" type="video/mp4">
</video>

## Submission Process
Deadline for submission is June 14th, 2026, 23:59 (AOE). Details on where to upload, what format, and other requirements will follow. 

## Evaluation Criteria

We will judge entries on four different criteria:

* Total driving distance of all vehicles in the fleet
* Total driving time of all vehicles in the fleet
* Waiting time of customers
* Explainability of algorithm decisions for customer as well as central fleet control

## Prizes
We will award 500€ of prize money to be divided among the top contestants.

## How to get started
We will provide text and video tutorials to help participants get started and ease troubleshooting. These will be released on this page pending competition acceptance. Additionally, we'll hold an online Q&A session at the start of the competition. Participants will be able to contact us via mailing list if they encounter any problems.



