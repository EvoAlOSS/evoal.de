---
title: "Metrics"
permalink: /docs/competition26/metrics
date: 2026-03-13T13:00:00+02:00
author: Bernhard Berger
author_profile: false
toc: true
toc_sticky: true
sidebar:
  nav: "competition"
---


## Key Definitions

To ensure consistent interpretation of the metrics, the following terms are defined as used within this competition:

*   **Requested Pickup Time:** The specific future time desired by the passenger for the pickup to occur. This serves as the reference point for calculating wait times and determining the validity of a request.
*   **First Pickup Time:** The timestamp at which the vehicle arrives at the pickup location and the passenger(s) board the vehicle. For metric calculation, arrival and boarding are considered simultaneous.
*   **Final Drop-off Time:** The timestamp at which the vehicle arrives at the destination and the passenger(s) exit the vehicle. For metric calculation, arrival and exiting are considered simultaneous.
*   **Non-Driving Time:** The cumulative duration a passenger spends waiting at an intermediate location. This specifically refers to situations where a passenger is dropped off at a location that is not their final destination and must wait to be picked up by a subsequent vehicle. It does not include time spent inside a vehicle during detours or stops.

## Metrics
### Relative Throughput (used for Evaluation)

**Category:** Success-based

**Definition:**
The ratio of successfully fulfilled requests to the total number of requests received within the simulation period.

**Calculation:**
$$ \text{Relative Throughput} = \frac{\text{Number of Successfully Fulfilled Requests}}{\text{Total Number of Requests}} $$

**Adjustment for different settings:**
A request is considered successfully fulfilled if and only if all passengers associated with that specific request have been dropped off at their designated destinations.

**Range:**
$$$0.0$$ to $$1.0$$ (or $$0\%$$ to $$100\%$$).

**Unit:**
Dimensionless (ratio).

***

### (Average) Passenger Pickup Wait Time

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

### (Average) Passenger Journey Time (used for Evaluation)

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

### (Average) Passenger Non-Driving Time

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

### Driven Distance

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

### Used CO2 Emissions

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

### Total Operational Cost (used for Evaluation)

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