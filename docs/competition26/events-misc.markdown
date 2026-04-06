---
title: "Explanations and Metrics Event"
permalink: /docs/competition26/events-misc
date: 2026-04-06T13:15:00+02:00
author: Bernhard Berger
author_profile: false
toc: true
toc_sticky: true
sidebar:
  nav: "competition"
---

## Explanations

Throughout the simulation, certain events — particularly those related to route planning — may include an `explanations` attribute. Explanations provide human-readable text that describes the reasoning behind a decision made by the optimizer. They are targeted at three distinct roles, each representing a different perspective on the decision:

| Role            | Description                                                                                         |
|-----------------|-----------------------------------------------------------------------------------------------------|
| `customer`      | An explanation directed at the customer, describing why this decision is beneficial or relevant to them. |
| `taxi-manager`  | An explanation directed at the taxi fleet manager, providing strategic or operational reasoning.      |
| `taxi-driver`   | An explanation directed at the taxi driver, providing practical or route-specific reasoning.          |

The `explanations` attribute is an object with up to three keys corresponding to these roles. Each value is a human-readable string. The attribute is optional and may be `null` if no explanations are provided.

### JSON Structure

```json
{
  "explanations": {
    "customer": "I think this is a good idea.",
    "taxi-manager": "I had no idea of how to solve it.",
    "taxi-driver": "Well, just be quick."
  }
}
```

### Data Fields

| Field          | Type     | Required | Description                                                      |
|----------------|----------|----------|------------------------------------------------------------------|
| `customer`     | `string` | No       | Explanation text directed at the customer.                       |
| `taxi-manager` | `string` | No       | Explanation text directed at the taxi fleet manager.             |
| `taxi-driver`  | `string` | No       | Explanation text directed at the taxi driver.                    |

### Usage

The `explanations` attribute appears in events such as `vehicle:route-planned`, where it is attached to the optimizer's routing decision. For example:

```json
{
  "category": "vehicle",
  "name": "route-planned",
  "data": {
    "vehicle-id": "taxi-2",
    "move-id": "move-0",
    "request-id": "request-1",
    "explanations": {
      "customer": "Your taxi is the closest available vehicle and will arrive shortly.",
      "taxi-manager": "Assigned the nearest idle taxi to minimize wait time.",
      "taxi-driver": "Head straight to the pick-up point via the fastest route."
    },
    "route": [
      { "type": "follow-road", "road-id": 33739 },
      { "type": "pick-up-passengers", "intersection-id": 243974386, "count": 1, "request-id": "request-1" },
      { "type": "follow-road", "road-id": 33744 },
      { "type": "drop-off-passengers", "intersection-id": 243974397, "count": 1, "request-id": "request-1" }
    ]
  }
}
```

When no explanations are provided, the field is set to `null`:

```json
{
  "explanations": null
}
```

---

## Metrics Events

The following event provides periodic performance metrics for the simulation. Metrics are computed by the simulation and broadcast at regular intervals (approximately every 10 seconds of simulation time) to allow the optimizer and visualization to monitor the overall performance of the taxi fleet. For a detailed description of each metric and how it is calculated, refer to the [metrics documentation](https://evoal.de/docs/competition26/metrics).

---

### `metrics:metrics`

Provides a snapshot of the current simulation performance metrics. This event is sent periodically by the simulation and contains aggregated values for all tracked metrics at the given simulation time.

#### Senders
* `SIMULATION`

#### Receivers
* `VISUALIZATION`
* `OPTIMIZER`

#### Data Fields

| Field     | Type      | Required | Description                                                  |
|-----------|-----------|----------|--------------------------------------------------------------|
| `time`    | `number`  | Yes      | The simulation time at which the metrics were computed.      |
| `metrics` | `object`  | Yes      | An object containing the current values of all tracked metrics. |

The `metrics` object contains:

| Metric                                 | Type     | Description                                                                                      |
|----------------------------------------|----------|--------------------------------------------------------------------------------------------------|
| `average-waiting-time`                 | `number` | The average time customers waited before being picked up.                                        |
| `minimum-passenger-pickup-wait-time`   | `number` | The minimum time any customer waited before being picked up.                                     |
| `driven-distance`                      | `number` | The total distance driven by all taxis in meters.                                                |
| `maximum-passenger-journey-time`       | `number` | The maximum total journey time experienced by any passenger (from pick-up to drop-off).          |
| `total-cost`                           | `number` | The total operational cost incurred by all taxis.                                                |
| `relative-throughput`                   | `number` | The ratio of served requests to total requests (0.0 to 1.0).                                    |
| `maximum-passenger-non-driving-time`   | `number` | The maximum non-driving time experienced by any passenger (waiting time plus any idle time).     |
| `co2-emission`                         | `number` | The total CO₂ emissions produced by all taxis in grams.                                         |

#### Example

Initial metrics (no activity yet):

```json
{
  "category": "metrics",
  "name": "metrics",
  "data": {
    "time": 2,
    "metrics": {
      "average-waiting-time": 0.0,
      "minimum-passenger-pickup-wait-time": 0.0,
      "driven-distance": 0.0,
      "maximum-passenger-journey-time": 0.0,
      "total-cost": 0.0,
      "relative-throughput": 0.0,
      "maximum-passenger-non-driving-time": 0.0,
      "co2-emission": 0.0
    }
  }
}
```

Metrics after some simulation activity:

```json
{
  "category": "metrics",
  "name": "metrics",
  "data": {
    "time": 12,
    "metrics": {
      "average-waiting-time": 1.0,
      "minimum-passenger-pickup-wait-time": 1.0,
      "driven-distance": 85.77038704862369,
      "maximum-passenger-journey-time": 7.0,
      "total-cost": 10.766068549118945,
      "relative-throughput": 1.0,
      "maximum-passenger-non-driving-time": 1.0,
      "co2-emission": 7.893701721401691
    }
  }
}
```

---