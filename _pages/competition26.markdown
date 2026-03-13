---
title: "Explainable ride-sharing optimisation for sustainable traffic organisation"
permalink: /pages/competition26
date: 2026-01-06T13:00:00+02:00
author: Bernhard Berger
author_profile: false
toc: true
toc_sticky: true
---

[![Publications and Research Topics](/assets/images/banner-competition.jpg){: .full}](/pages/competition26)


## Updates
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

This competition evaluates optimization algorithms for ride-hailing and ride-sharing systems. The goal is to fulfill passenger requests efficiently by balancing service quality, time efficiency, and operational costs. The performance of an algorithm is assessed based on a multi-objective problem where one metric from each category—Success-based, Time-based, and Cost-based—is selected for final judgment. The remaining metrics are provided for comprehensive evaluation and to assist contestants in the analysis and modification of their algorithms.

The simulation considers a fixed operational period of **24 hours**. Any request that is not fully fulfilled by the end of this 24-hour period is automatically considered unsuccessful, modeling the operational constraints of a typical taxi company.

### Key Definitions

To ensure consistent interpretation of the metrics, the following terms are defined as used within this competition:

*   **Requested Pickup Time:** The specific future time desired by the passenger for the pickup to occur. This serves as the reference point for calculating wait times and determining the validity of a request.
*   **First Pickup Time:** The timestamp at which the vehicle arrives at the pickup location and the passenger(s) board the vehicle. For metric calculation, arrival and boarding are considered simultaneous.
*   **Final Drop-off Time:** The timestamp at which the vehicle arrives at the destination and the passenger(s) exit the vehicle. For metric calculation, arrival and exiting are considered simultaneous.
*   **Non-Driving Time:** The cumulative duration a passenger spends waiting at an intermediate location. This specifically refers to situations where a passenger is dropped off at a location that is not their final destination and must wait to be picked up by a subsequent vehicle. It does not include time spent inside a vehicle during detours or stops.

***

### Metrics

#### Relative Throughput (used for Evaluation)

**Category:** Success-based

**Definition:**
The ratio of successfully fulfilled requests to the total number of requests received within the simulation period.

**Calculation:**
$ \text{Relative Throughput} = \frac{\text{Number of Successfully Fulfilled Requests}}{\text{Total Number of Requests}} $

**Adjustment for different settings:**
A request is considered successfully fulfilled if and only if all passengers associated with that specific request have been dropped off at their designated destinations.

**Range:**
$0.0$ to $1.0$ (or $0\%$ to $100\%$).

**Unit:**
Dimensionless (ratio).

***

#### (Average) Passenger Pickup Wait Time

**Category:** Time-based

**Definition:**
The duration of time a passenger waits from the moment they request a ride until the actual pickup occurs.

**Calculation:**
  $$ \text{Passenger Pickup Wait Time} = \text{Actual Pickup Time} - \text{Requested Pickup Time} $$ 

**Adjustment for different settings:**
In scenarios involving multiple passengers within a single request (e.g., ride-sharing), the wait time is determined based on the first passenger to be picked up.

**Range:**
$\geq 0$.

**Unit:**
Minutes (or seconds, depending on the simulation configuration).

***

#### (Average) Passenger Journey Time (used for Evaluation)

**Category:** Time-based

**Definition:**
The total duration of time elapsed from the moment a ride is requested until the passenger is dropped off at their destination.

**Calculation:**
$$ \text{Passenger Journey Time} = \text{Actual Drop-off Time} - \text{Requested Pickup Time} $$

**Adjustment for different settings:**
In scenarios involving multiple passengers within a single request, the metric is calculated using the time of the first passenger to be picked up and the time of the last passenger to be dropped off.

**Range:**
$\geq 0$.

**Unit:**
Minutes (or seconds, depending on the simulation configuration).

***

#### (Average) Passenger Non-Driving Time

**Category:** Time-based

**Definition:**
The cumulative amount of time during a passenger's journey where the vehicle is not moving towards that passenger's final destination. This includes stops for picking up or dropping off other passengers.

**Calculation:**
$$ \text{Passenger Non-Driving Time} = \sum (\text{End of Stop Time} - \text{Start of Stop Time}) $$
*(Summed over all stops occurring while the passenger is in the vehicle)*

**Adjustment for different settings:**
In scenarios involving multiple passengers within a single request, the non-driving times for each individual passenger are summed to provide a total value for the request.

**Range:**
$\geq 0$.

**Unit:**
Minutes (or seconds, depending on the simulation configuration).

***

#### Driven Distance

**Category:** Cost-based

**Definition:**
The total distance traveled by vehicles to fulfill a specific request.

**Calculation:**
$$ \text{Driven Distance} = \sum_{i=1}^{n} \text{Distance Traveled by Vehicle } i $$
*(Where $n$ is the number of distinct vehicles used for the request)*

**Adjustment for different settings:**
If all passengers of a request are transported in the same vehicle, the metric corresponds to the total distance traveled by that single vehicle. If passengers are split across multiple vehicles, the distances traveled by each vehicle are summed.

**Range:**
$\geq 0$.

**Unit:**
Kilometers.

***

#### Used CO2 Emissions

**Category:** Cost-based

**Definition:**
The total carbon dioxide emissions produced by vehicles to fulfill a specific request.

**Calculation:**
$$ \text{Used CO2 Emissions} = \sum_{i=1}^{n} \text{CO2 Emissions by Vehicle } i $$
*(Where $n$ is the number of distinct vehicles used for the request)*
$$ \text{CO2 Emission per distance} = VSP \cdot m \cdot \Delta t \cdot \text{energy-efficiency-factor}$$
$$ \text{VSP} = v \cdot(v^{2}\cdot\text{resistance-constant} + \text{fraction-constant})$$

**Adjustment for different settings:**
If all passengers of a request are transported in the same vehicle, the metric corresponds to the total emissions produced by that single vehicle. If passengers are split across multiple vehicles, the emissions produced by each vehicle are summed.

**Range:**
$\geq 0$.

**Unit:**
Kilograms (kg) or grams (g).

***

#### Total Operational Cost (used for Evaluation)

**Category:** Cost-based

**Definition:**
A composite metric representing the total financial cost associated with fulfilling a specific request, accounting for both fuel consumption and carbon dioxide emissions.

**Calculation:**
$$ \text{Total Operational Cost} = (\alpha \times \text{Driven Distance}) + (\beta \times \text{Used CO2 Emissions}) $$
*(Where $\alpha$ represents the cost factor per kilometer and $\beta$ represents the cost factor per unit of CO2)*

**Adjustment for different settings:**
The weights $\alpha$ and $\beta$ are predefined constants that model current fuel prices and CO2 certificate costs. If passengers are split across multiple vehicles, the driven distance and CO2 emissions are summed for all vehicles involved before applying the weights.

**Range:**
$\geq 0$.

**Unit:**
EUR.

## How to get started
We will provide text and video tutorials to help participants get started and ease troubleshooting. These will be released on this page pending competition acceptance. Additionally, we'll hold an online Q&A session at the start of the competition. Participants will be able to contact us via mailing list if they encounter any problems.



## Acknowledgements
The challenge is supported by the DFG-funded [Research Training Group CAUSE](https://rtg-cause.github.io).
