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
The ratio of successfully served customers to the total number of customers within the simulation period.

**Calculation:**
$$ \text{Relative Throughput} = \frac{\text{Number of Successfully Served Customers}}{\text{Total Number of Customers}} $$

**Adjustment for different settings:**
A customers is served successfully if the customer was dropped off at the designated destinations.

**Range:**
$$0.0$$ to $$1.0$$ (or $$0\%$$ to $$100\%$$).

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
The total distance traveled by vehicles.

**Calculation:**
$$ \text{Driven Distance} = \sum_{i=1}^{n} \text{Distance Traveled by Vehicle } i $$
*(Where $n$ is the number of vehicles)*

**Adjustment for different settings:**
None

**Range:**
$\geq 0$.

**Unit:**
Kilometers.

***

### Used CO<sub>2</sub> Emissions

**Category:** Cost-based

**Definition:**
The total carbon dioxide emissions produced by all vehicles.

**Calculation:**
$$ CO_2\text{ }Emissions = \sum_{v \in vehicles} CO_2\text{ }Emissions(v) $$

$ CO_2\text{ }Emission(v)$ is the sum of the $CO_2\text{ }Emission$ of all roads the vehicle has driven dependent on the speed.

The $ CO_2\text{ }Emissions $ for a distance $length$ travelled in $metres$ at a speed $v$ in $km/h$ are calculated as follows:

$$ \text{VSP} = v \cdot(\text{friction-constant} + \text{resistance-constant} \cdot v^2)$$

$$ energy = VSP \cdot mass \cdot (length / v) / 3600000$$

$$ CO_2\text{ }emissions = (energy / \text{energy-efficiency-factor}) * \cdot \text{CO2-factor}$$



**Adjustment for different settings:**
None

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
$$ \text{Total Operational Cost} = (\alpha \times \text{CO\_2 Emissions}) + (\sum_{v\in vehicles}\alpha_v \times \text{Driven Distance}) +$$
*(Where $\alpha_v$ represents the cost factor per meter of the vehicle and $\beta$ represents the cost factor per unit of CO<sub>2</sub>)*

**Adjustment for different settings:**
The weight $\beta$ are predefined constant that model current fuel prices and CO<sub>2</sub> certificate costs. 

**Range:**
$\geq 0$.

**Unit:**
EUR.