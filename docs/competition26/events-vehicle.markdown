---
title: "Taxi Fleet and Vehicle Events"
permalink: /docs/competition26/events-vehicle
date: 2026-04-06T12:30:00+02:00
author: Bernhard Berger
author_profile: false
toc: true
toc_sticky: true
sidebar:
  nav: "competition"
---
The following events handle the management of taxis and vehicle movements within the simulation. The event system uses a two-layer architecture: the **taxi fleet layer** (`taxi-fleet` category) handles taxi-specific business logic such as adding taxis, planning routes with passenger operations, and tracking pick-ups, and drop-offs, while the **vehicle layer** (`vehicle` category) handles the underlying vehicle mechanics such as movement, intersection passing, and route completion.

When a taxi is added or a route is planned, the taxi fleet layer translates the request into corresponding vehicle layer events. This separation allows the vehicle layer to remain generic and reusable for different vehicle types in the future.

The documentation is split into two sections:
1. **Optimizer-relevant events** — Events that the optimizer sends or receives and must handle.
2. **Internal and visualization events** — Events primarily for simulation-internal processing or visualization purposes.

---

## Optimizer-Relevant Events

These events are directly relevant for the optimizer. The optimizer either sends these events to control the taxi fleet or receives them to track the state of vehicles and taxi operations. Below you see a sequence diagram for adding a taxi and executing a route

```
OPTIMIZER/VISUALIZATION                     SIMULATION                        ALL CLIENTS
        |                                       |                                  |
        |-- taxi-fleet:add-taxi (from vis) ---> |                                  |
        |                                       |-- vehicle:added ---------------> |
        |                                       |-- taxi-fleet:added-taxi -------> |
        |                                       |                                  |
        |-- taxi-fleet:plan-route (from opt)--> |                                  |
        |                                       |-- vehicle:move ----------------> |
        |                                       |-- vehicle:route-planned -------> |
        |                                       |                                  |
        |                                       |   (vehicle traverses road)       |
        |                                       |-- vehicle:passed-intersection -> |
        |                                       |                                  |
        |                                       |   (pcked up passenger)           |
        |                                       |-- taxi-fleet:picked-up --------> |
        |                                       |-- vehicle:route-event ---------> |
        |                                       |                                  |
        |                                       |   (vehicle traverses road)       |
        |                                       |-- vehicle:passed-intersection -> |
        |                                       |                                  |
        |                                       |   (dropped off passenger)        |
        |                                       |-- taxi-fleet:dropped-off ------> |
        |                                       |-- vehicle:route-event ---------> |
        |                                       |                                  |
        |                                       |-- vehicle:finished-move -------> |
        |                                       |                                  |
```

---

### `taxi-fleet:added-taxi`

Confirms that a new taxi has been successfully added to the fleet. This event is sent after the corresponding `vehicle:added` event and includes the full taxi properties (the content of the events is exactly identical).

#### Senders
* `SIMULATION`

#### Receivers
* `OPTIMIZER`
* `SIMULATION`
* `VISUALIZATION`

#### Data Fields

| Field             | Type      | Required | Description                                                  |
|-------------------|-----------|----------|--------------------------------------------------------------|
| `id`              | `string`  | Yes      | The unique identifier of the added taxi.                     |
| `intersection-id` | `integer` | Yes      | The identifier of the intersection where the taxi is placed. |
| `properties`      | `object`  | Yes      | The full properties of the taxi (see `taxi-fleet:add-taxi`). |

#### Example

```json
{
  "category": "taxi-fleet",
  "name": "added-taxi",
  "data": {
    "id": "taxi-1",
    "intersection-id": 243863178,
    "properties": {
      "maximum-capacity": 1,
      "maximum-speed": 100,
      "energy-efficiency-constant": 0.87,
      "resistance-constant": 1.7e-4,
      "friction-constant": 0.0981,
      "co2-factor": 310.0,
      "mass": 1760,
      "cost-per-meter": 0.07,
      "distance-cost-factor": 1.0,
      "label": "Taxi 1",
      "type": "taxi"
    }
  }
}
```

---

### `taxi-fleet:plan-route`

Plans a route for a taxi. The route consists of an ordered list of route steps, which can be road segments to follow, passenger pick-up operations, or passenger drop-off operations. This event is sent by the optimizer to instruct a taxi to execute a specific route. The simulation validates the route and, if valid, begins executing it.

#### Senders
* `OPTIMIZER`

#### Receivers
* `OPTIMIZER`
* `SIMULATION`
* `VISUALIZATION`

#### Data Fields

| Field        | Type     | Required | Description                                           |
|--------------|----------|----------|-------------------------------------------------------|
| `vehicle-id` | `string` | Yes      | The identifier of the taxi to plan the route for.    |
| `move-id`   | `string` | Yes      | A unique identifier for this move/route execution.    |
| `route`     | `array`  | Yes      | An ordered list of route step objects.                |

Each route step object has a `type` field that determines its structure. The following types are supported:

##### Route Step: `follow-road`

| Field     | Type      | Required | Description                              |
|-----------|-----------|----------|------------------------------------------|
| `type`    | `string`  | Yes      | Must be `"follow-road"`.                 |
| `road-id` | `integer` | Yes      | The identifier of the road to traverse.  |

##### Route Step: `pick-up-passengers`

| Field             | Type      | Required | Description                                                    |
|-------------------|-----------|----------|----------------------------------------------------------------|
| `type`            | `string`  | Yes      | Must be `"pick-up-passengers"`.                                |
| `intersection-id` | `integer` | Yes      | The intersection where passengers are picked up.               |
| `count`           | `integer` | Yes      | The number of passengers to pick up.                           |
| `request-id`      | `string`  | Yes      | The identifier of the transportation request being fulfilled.  |

##### Route Step: `drop-off-passengers`

| Field             | Type      | Required | Description                                                    |
|-------------------|-----------|----------|----------------------------------------------------------------|
| `type`            | `string`  | Yes      | Must be `"drop-off-passengers"`.                               |
| `intersection-id` | `integer` | Yes      | The intersection where passengers are dropped off.             |
| `count`           | `integer` | Yes      | The number of passengers to drop off.                          |
| `request-id`      | `string`  | Yes      | The identifier of the transportation request being fulfilled.  |

#### Example

```json
{
  "category": "taxi-fleet",
  "name": "plan-route",
  "data": {
    "vehicle-id": "taxi-2",
    "move-id": "move-0",
    "route": [
      { "type": "follow-road", "road-id": 33739 },
      { "type": "pick-up-passengers", "intersection-id": 243974386, "count": 1, "request-id": "request-1" },
      { "type": "follow-road", "road-id": 33744 },
      { "type": "drop-off-passengers", "intersection-id": 243974397, "count": 1, "request-id": "request-1" }
    ]
  }
}
```

#### Flow after Triggering
1. The optimizer sends `taxi-fleet:plan-route`.
2. The simulation validates the route and sends `vehicle:move` to begin execution.
3. The simulation sends `vehicle:route-planned` to confirm the route.
4. As the vehicle traverses the route, `vehicle:passed-intersection` events are emitted at each intersection.
5. When the vehicle reaches a pick-up point, `taxi-fleet:picked-up-passengers` is emitted.
6. When the vehicle reaches a drop-off point, `taxi-fleet:dropped-off-passengers` is emitted.
7. When the entire route is completed, `vehicle:finished-move` is emitted.

---

### `taxi-fleet:picked-up-passengers`

Informs all participants that a taxi has picked up passengers at an intersection as part of a planned route. This event is emitted when the vehicle reaches a `pick-up-passengers` route step.

#### Senders
* `SIMULATION`

#### Receivers
* `OPTIMIZER`
* `SIMULATION`
* `VISUALIZATION`

#### Data Fields

| Field             | Type      | Required | Description                                                         |
|-------------------|-----------|----------|---------------------------------------------------------------------|
| `vehicle-id`      | `string`  | Yes      | The identifier of the taxi that picked up passengers.               |
| `move-id`         | `string`  | Yes      | The identifier of the current move/route.                           |
| `request-id`      | `string`  | Yes      | The identifier of the transportation request being fulfilled.       |
| `intersection-id` | `integer` | Yes      | The intersection where the pick-up occurred.                        |
| `road-id`         | `integer` | Yes      | The identifier of the road the vehicle was on when the pick-up occurred. |
| `time`            | `number`  | Yes      | The simulation time at which the pick-up occurred.                  |
| `picked-up`       | `array`   | Yes      | A list of person identifiers that were picked up.                   |

#### Example

```json
{
  "category": "taxi-fleet",
  "name": "picked-up-passengers",
  "data": {
    "vehicle-id": "taxi-2",
    "move-id": "move-0",
    "request-id": "request-1",
    "intersection-id": 243974386,
    "road-id": 33739,
    "time": 2,
    "picked-up": ["person-request-1-0"]
  }
}
```

---

### `taxi-fleet:dropped-off-passengers`

Informs all participants that a taxi has dropped off passengers at an intersection as part of a planned route. This event is emitted when the vehicle reaches a `drop-off-passengers` route step.

#### Senders
* `SIMULATION`

#### Receivers
* `OPTIMIZER`
* `SIMULATION`
* `VISUALIZATION`

#### Data Fields

| Field                  | Type      | Required | Description                                                           |
|------------------------|-----------|----------|-----------------------------------------------------------------------|
| `vehicle-id`           | `string`  | Yes      | The identifier of the taxi that dropped off passengers.               |
| `move-id`              | `string`  | Yes      | The identifier of the current move/route.                             |
| `request-id`           | `string`  | Yes      | The identifier of the transportation request being fulfilled.         |
| `intersection-id`      | `integer` | Yes      | The intersection where the drop-off occurred.                         |
| `road-id`              | `integer` | Yes      | The identifier of the road the vehicle was on when the drop-off occurred. |
| `time`                 | `number`  | Yes      | The simulation time at which the drop-off occurred.                   |
| `dropped-off-passengers` | `array` | Yes      | A list of person identifiers that were dropped off.                   |

#### Example

```json
{
  "category": "taxi-fleet",
  "name": "dropped-off-passengers",
  "data": {
    "vehicle-id": "taxi-2",
    "move-id": "move-0",
    "request-id": "request-1",
    "intersection-id": 243974397,
    "road-id": 33744,
    "time": 8,
    "dropped-off-passengers": ["person-request-1-0"]
  }
}
```

---

### `taxi-fleet:remove-taxi`

Requests the removal of a taxi from the fleet. The taxi is taken out of service and its corresponding vehicle is removed from the simulation.

#### Senders
* `SIMULATION`,
* `VISUALIZATION`

#### Receivers
* `OPTIMIZER`
* `SIMULATION`
* `VISUALIZATION`

#### Data Fields

| Field | Type     | Required | Description                                    |
|-------|----------|----------|------------------------------------------------|
| `id`  | `string` | Yes      | The unique identifier of the taxi to remove.   |

#### Example

```json
{
  "category": "taxi-fleet",
  "name": "remove-taxi",
  "data": {
    "id": "taxi-1"
  }
}
```

#### Flow after Triggering
1. The simulation receives `taxi-fleet:remove-taxi`.
2. The simulation removes the vehicle and sends `vehicle:removed`.


---

### `vehicle:passed-intersection`

Informs all participants that a vehicle has passed through an intersection while following its planned route. This event is emitted each time a vehicle reaches the end of a road segment and arrives at the next intersection.

#### Senders
* `SIMULATION`

#### Receivers
* `OPTIMIZER`
* `SIMULATION`
* `VISUALIZATION`

#### Data Fields

| Field             | Type      | Required | Description                                                  |
|-------------------|-----------|----------|--------------------------------------------------------------|
| `vehicle-id`      | `string`  | Yes      | The identifier of the vehicle.                               |
| `move-id`         | `string`  | Yes      | The identifier of the current move/route.                    |
| `road-id`         | `integer` | Yes      | The identifier of the road the vehicle just traversed.       |
| `intersection-id` | `integer` | Yes      | The identifier of the intersection the vehicle has reached.  |
| `time`            | `number`  | Yes      | The simulation time at which the intersection was passed.    |

#### Example

```json
{
  "category": "vehicle",
  "name": "passed-intersection",
  "data": {
    "vehicle-id": "taxi-2",
    "move-id": "move-0",
    "road-id": 33739,
    "intersection-id": 243974386,
    "time": 2
  }
}
```

---

### `vehicle:finished-move`

Informs all participants that a vehicle has completed its entire planned route. This event is emitted when the last route step has been executed.

#### Senders
* `SIMULATION`

#### Receivers
* `OPTIMIZER`
* `SIMULATION`
* `VISUALIZATION`

#### Data Fields

| Field        | Type     | Required | Description                                        |
|--------------|----------|----------|----------------------------------------------------|
| `vehicle-id` | `string` | Yes      | The identifier of the vehicle.                    |
| `move-id`   | `string` | Yes      | The identifier of the completed move/route.        |
| `time`      | `number` | Yes      | The simulation time at which the move was completed.|

#### Example

```json
{
  "category": "vehicle",
  "name": "finished-move",
  "data": {
    "vehicle-id": "taxi-2",
    "move-id": "move-0",
    "time": 8.175467867500906
  }
}
```

---

### `vehicle:route-planned`

Confirms that a route has been successfully planned and validated for a vehicle. This event is sent by the simulation in response to a `taxi-fleet:plan-route` event after the route has been accepted.

#### Senders
* `SIMULATION`

#### Receivers
* `OPTIMIZER`
* `SIMULATION`
* `VISUALIZATION`

#### Data Fields

| Field          | Type     | Required | Description                                                      |
|----------------|----------|----------|------------------------------------------------------------------|
| `vehicle-id`   | `string` | Yes      | The identifier of the vehicle.                                   |
| `move-id`      | `string` | Yes      | The identifier of the move/route.                                |
| `route`        | `array`  | Yes      | The validated route (same structure as in `taxi-fleet:plan-route`). |
| `request-id`   | `string` | No       | The associated request identifier, or `null` if not applicable.  |
| `explanations`  | `object` | No       | Optional explanations or metadata, or `null`.                    |

#### Example

```json
{
  "category": "vehicle",
  "name": "route-planned",
  "data": {
    "vehicle-id": "taxi-2",
    "move-id": "move-0",
    "request-id": null,
    "explanations": null,
    "route": [
      { "type": "follow-road", "road-id": 33739 },
      { "type": "pick-up-passengers", "intersection-id": 243974386, "count": 1, "request-id": "request-1" },
      { "type": "follow-road", "road-id": 33744 },
      { "type": "drop-off-passengers", "intersection-id": 243974397, "count": 1, "request-id": "request-1" }
    ]
  }
}
```

---

### `vehicle:added`

Confirms that a new vehicle has been successfully added to the simulation. This event is emitted by the simulation after processing a vehicle creation request (e.g., triggered by `taxi-fleet:add-taxi`).

#### Senders
* `SIMULATION`

#### Receivers
* `OPTIMIZER`
* `SIMULATION`
* `VISUALIZATION`

#### Data Fields

| Field             | Type      | Required | Description                                                  |
|-------------------|-----------|----------|--------------------------------------------------------------|
| `id`              | `string`  | Yes      | The unique identifier of the vehicle.                        |
| `intersection-id` | `integer` | Yes      | The identifier of the intersection where the vehicle is placed. |
| `properties`      | `object`  | Yes      | The full properties of the vehicle.                          |

The `properties` object contains:

| Property                       | Type     | Required | Description                                                    |
|--------------------------------|----------|----------|----------------------------------------------------------------|
| `maximum-capacity`             | `integer`| Yes      | The maximum number of passengers the vehicle can carry.        |
| `maximum-speed`                | `number` | Yes      | The maximum speed of the vehicle in km/h.                      |
| `energy-efficiency-constant`   | `number` | Yes      | Energy efficiency constant used in fuel/energy calculations.   |
| `resistance-constant`          | `number` | Yes      | Air resistance constant of the vehicle.                        |
| `friction-constant`            | `number` | Yes      | Friction constant of the vehicle.                              |
| `co2-factor`                   | `number` | Yes      | CO₂ emission factor in g/kWh.                                 |
| `mass`                         | `number` | Yes      | The mass of the vehicle in kg.                                 |
| `cost-per-meter`               | `number` | Yes      | The operational cost per meter travelled.                       |
| `distance-cost-factor`         | `number` | Yes      | A multiplier applied to distance-based cost calculations.      |
| `label`                        | `string` | Yes      | A human-readable label for the vehicle.                        |
| `type`                         | `string` | Yes      | The vehicle type (e.g., `"taxi"`).                             |

#### Example

```json
{
  "category": "vehicle",
  "name": "added",
  "data": {
    "id": "taxi-1",
    "intersection-id": 243863178,
    "properties": {
      "maximum-capacity": 1,
      "maximum-speed": 100,
      "energy-efficiency-constant": 0.87,
      "resistance-constant": 1.7e-4,
      "friction-constant": 0.0981,
      "co2-factor": 310.0,
      "mass": 1760,
      "cost-per-meter": 0.07,
      "distance-cost-factor": 1.0,
      "label": "Taxi 1",
      "type": "taxi"
    }
  }
}
```

---

### `vehicle:removed`

Informs all participants that a vehicle has been removed from the simulation.

#### Senders
* `SIMULATION`

#### Receivers
* `OPTIMIZER`
* `SIMULATION`
* `VISUALIZATION`

#### Data Fields

| Field | Type     | Required | Description                                       |
|-------|----------|----------|---------------------------------------------------|
| `id`  | `string` | Yes      | The unique identifier of the removed vehicle.     |

#### Example

```json
{
  "category": "vehicle",
  "name": "removed",
  "data": {
    "id": "taxi-1"
  }
}
```

## Internal and Visualization Events

These events are primarily used for simulation-internal processing or by the visualization layer. The optimizer receives some of these events but they are not required for optimization logic.
---

### `taxi-fleet:add-taxi`

Requests the simulation to add a new taxi to the fleet. The taxi is placed at a specific intersection in the road network. The event includes the taxi's physical and cost properties. Upon successful processing, the simulation responds with a `vehicle:added` event followed by a `taxi-fleet:added-taxi` event.

#### Senders
* `SIMULATION`,
* `VISUALIZATION`

#### Receivers
* `SIMULATION`

#### Data Fields

| Field             | Type     | Required | Description                                                       |
|-------------------|----------|----------|-------------------------------------------------------------------|
| `id`              | `string` | Yes      | The unique identifier for the new taxi.                           |
| `intersection-id` | `integer`| Yes      | The identifier of the intersection where the taxi is placed.      |
| `properties`      | `object` | Yes      | The physical and cost properties of the taxi.                     |

The `properties` object contains:

| Property                       | Type     | Required | Description                                                    |
|--------------------------------|----------|----------|----------------------------------------------------------------|
| `maximum-capacity`             | `integer`| Yes      | The maximum number of passengers the taxi can carry.           |
| `maximum-speed`                | `number` | Yes      | The maximum speed of the taxi in km/h.                         |
| `energy-efficiency-constant`   | `number` | Yes      | Energy efficiency constant used in fuel/energy calculations.   |
| `resistance-constant`          | `number` | Yes      | Air resistance constant of the vehicle.                        |
| `friction-constant`            | `number` | Yes      | Friction constant of the vehicle.                              |
| `co2-factor`                   | `number` | Yes      | CO₂ emission factor in g/kWh.                                 |
| `mass`                         | `number` | Yes      | The mass of the vehicle in kg.                                 |
| `cost-per-meter`               | `number` | Yes      | The operational cost per meter traveled.                       |
| `distance-cost-factor`         | `number` | Yes      | A multiplier applied to distance-based cost calculations.      |
| `label`                        | `string` | No       | A human-readable label for the taxi (may be auto-generated).   |
| `type`                         | `string` | No       | The vehicle type. Defaults to `"taxi"` if not provided.        |

#### Example

```json
{
  "category": "taxi-fleet",
  "name": "add-taxi",
  "data": {
    "id": "taxi-1",
    "intersection-id": 243863178,
    "properties": {
      "maximum-capacity": 1,
      "maximum-speed": 100,
      "energy-efficiency-constant": 0.87,
      "resistance-constant": 1.7e-4,
      "friction-constant": 0.0981,
      "co2-factor": 310.0,
      "mass": 1760,
      "cost-per-meter": 0.07,
      "distance-cost-factor": 1.0
    }
  }
}
```

#### Flow after Triggering
1. The simulation receives `taxi-fleet:add-taxi`.
2. The simulation creates the vehicle internally and sends `vehicle:added`.
3. The simulation sends `taxi-fleet:added-taxi` to confirm the taxi was added to the fleet.

---

### `vehicle:move`

An internal simulation event that initiates the movement of a vehicle along a planned route. This event is generated by the simulation after receiving and validating a `taxi-fleet:plan-route` command.

#### Senders
* `SIMULATION`

#### Receivers
* `OPTIMIZER`,
* `SIMULATION`,
* `VISUALIZATION`

#### Data Fields

| Field        | Type     | Required | Description                                                         |
|--------------|----------|----------|---------------------------------------------------------------------|
| `vehicle-id` | `string` | Yes      | The identifier of the vehicle to move.                             |
| `move-id`   | `string` | Yes      | The unique identifier for this move/route execution.                |
| `route`     | `array`  | Yes      | The route to follow (same structure as in `taxi-fleet:plan-route`). |

#### Example

```json
{
  "category": "vehicle",
  "name": "move",
  "data": {
    "vehicle-id": "taxi-2",
    "move-id": "move-0",
    "route": [
      { "type": "follow-road", "road-id": 33739 },
      { "type": "pick-up-passengers", "intersection-id": 243974386, "count": 1, "request-id": "request-1" },
      { "type": "follow-road", "road-id": 33744 },
      { "type": "drop-off-passengers", "intersection-id": 243974397, "count": 1, "request-id": "request-1" }
    ]
  }
}
```

---

### `vehicle:stop`

Commands a vehicle to stop its current movement. This event can be sent by the optimizer to interrupt a vehicle's route execution.

#### Senders
* `OPTIMIZER`

#### Receivers
* `SIMULATION`

#### Data Fields

| Field        | Type     | Required | Description                                |
|--------------|----------|----------|--------------------------------------------|
| `vehicle-id` | `string` | Yes      | The identifier of the vehicle to stop.    |

#### Example

```json
{
  "category": "vehicle",
  "name": "stop",
  "data": {
    "vehicle-id": "taxi-2"
  }
}
```

---

### `vehicle:route-event`

Informs participants about a specific route event that occurred during vehicle movement, such as a passenger pick-up or drop-off being executed. This event provides detailed timing information about when the route step was actually performed.

#### Senders
* `SIMULATION`

#### Receivers
* `OPTIMIZER`,
* `SIMULATION`,
* `VISUALIZATION`

#### Data Fields

| Field             | Type      | Required | Description                                                       |
|-------------------|-----------|----------|-------------------------------------------------------------------|
| `vehicle-id`      | `string`  | Yes      | The identifier of the vehicle.                                    |
| `move-id`         | `string`  | Yes      | The identifier of the current move/route.                         |
| `type`            | `string`  | Yes      | The type of route event (e.g., `"pick-up-passengers"`, `"drop-off-passengers"`). |
| `intersection-id` | `integer` | Yes      | The intersection where the event occurred.                        |
| `count`           | `integer` | Yes      | The number of passengers involved.                                |
| `request-id`      | `string`  | Yes      | The identifier of the associated transportation request.          |
| `time`            | `number`  | Yes      | The precise simulation time at which the event occurred.          |

#### Example

```json
{
  "category": "vehicle",
  "name": "route-event",
  "data": {
    "vehicle-id": "taxi-2",
    "move-id": "move-0",
    "type": "pick-up-passengers",
    "intersection-id": 243974386,
    "count": 1,
    "request-id": "request-1",
    "time": 2.811119365709157
  }
}
```

---
