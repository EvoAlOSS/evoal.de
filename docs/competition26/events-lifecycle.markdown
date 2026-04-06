---
title: "Simulation Lifecycle Events"
permalink: /docs/competition26/events-lifecycle
date: 2026-04-06T11:36:00+02:00
author: Bernhard Berger
author_profile: false
toc: true
toc_sticky: true
sidebar:
  nav: "competition"
---

The following events control the lifecycle of the simulation. They govern the starting, initializing, pausing, resuming, and stopping of the simulation as well as the associated state transitions.

## State Model

The simulation passes through the following states:

```
[START] → INITIALIZING → RUNNING ⇄ PAUSED → STOPPED
```

State transitions are triggered by command events and confirmed by `simulation:state` notification events.

---

## Event Overview and Lifecycle

| Event              | Category     | Type         | Description                                         |
|--------------------|--------------|--------------|-----------------------------------------------------|
| `start`            | `simulation` | Command      | Starts the simulation                               |
| `reset`            | `simulation` | Command      | Resets the simulation to its initial state          |
| `initialize-world` | `simulation` | Command (internal) | Initializes the simulation world              |
| `initialize`       | `simulation` | Command      | Requests all clients to initialize                  |
| `initialized`      | `client`     | Response     | Confirms that a client has completed initialization |
| `state`            | `simulation` | Notification | Informs about state changes                         |
| `pause`            | `simulation` | Command      | Pauses the simulation                               |
| `continue`         | `simulation` | Command      | Resumes a paused simulation                         |
| `stop`             | `simulation` | Command      | Stops the simulation                                |


```
OPTIMIZER/VISUALIZATION          SIMULATION                   ALL CLIENTS
        |                            |                             |
        |--- simulation:start -----> |                             |
        |                            |-- simulation:state -------> |  (INITIALIZING)
        |                            |-- simulation:reset -------> |
        |                            |-- simulation:init-world (internal)
        |                            |-- simulation:initialize --> |
        |                            |                             |
        |                            |<-- client:initialized ----- |  (per client)
        |                            |<-- client:initialized ----- |
        |                            |                             |
        |                            |-- simulation:state -------> |  (RUNNING)
        |                            |                             |
        |--- simulation:pause -----> |                             |
        |                            |-- simulation:state -------> |  (PAUSED)
        |                            |                             |
        |--- simulation:continue --> |                             |
        |                            |-- simulation:state -------> |  (RUNNING)
        |                            |                             |
        |--- simulation:stop ------> |                             |
        |                            |-- simulation:state -------> |  (STOPPED)
```


## Events in Detail

### `simulation:start`

Starts the simulation. This event triggers the initialization process of the simulation.

#### Senders
* `OPTIMIZER`,
* `VISUALIZATION`

#### Receivers
* `SIMULATION`

#### Data Fields
* *This event contains no data fields.*

#### Example

```json
{
  "category": "simulation",
  "name": "start",
  "data": {}
}
```

#### Flow after Triggering
1. The simulation receives `simulation:start`.
2. The simulation sends `simulation:state` with `state: "INITIALIZING"`.
3. The simulation sends `simulation:reset`.
4. The simulation sends `simulation:initialize-world` internally.
5. The simulation sends `simulation:initialize`.
6. All clients respond with `client:initialized`.
7. Once all clients have confirmed, the simulation sends `simulation:state` with `state: "RUNNING"`.

---

### `simulation:state`

Informs all participants about a state change of the simulation. This event is sent exclusively by the simulation and serves as confirmation of a completed state transition.

#### Senders
* `SIMULATION`

#### Receivers
* `OPTIMIZER`,
* `SIMULATION`,
* `VISUALIZATION`

#### Data Fields

| Field   | Type     | Required | Description |
|---------|----------|----------|-------------|
| `state` | `string` | Yes      | The new state of the simulation. Possible values: `INITIALIZING`, `RUNNING`, `PAUSED`, `STOPPED` |

#### Example

```json
{
  "category": "simulation",
  "name": "state",
  "data": {
    "state": "INITIALIZING"
  }
}
```

#### Valid State Transitions

| Previous State | New State      | Triggering Event                  |
|----------------|----------------|-----------------------------------|
| `STOPPED`      | `INITIALIZING` | `simulation:start`                |
| `INITIALIZING` | `RUNNING`      | All `client:initialized` received |
| `RUNNING`      | `PAUSED`       | `simulation:pause`                |
| `PAUSED`       | `RUNNING`      | `simulation:continue`             |
| `RUNNING`      | `STOPPED`      | `simulation:stop`                 |
| `PAUSED`       | `STOPPED`      | `simulation:stop`                 |

---

### `simulation:reset`

Resets the simulation to its initial state. This event is sent by the simulation during the initialization process and signals all participants to reset their internal state.

#### Senders
* `SIMULATION`

#### Receivers
* `OPTIMIZER`
* `SIMULATION`
* `VISUALIZATION`

#### Data Fields
* *This event contains no data fields.*

#### Example

```json
{
  "category": "simulation",
  "name": "reset",
  "data": {}
}
```

---

### `simulation:initialize-world`

Initializes the simulation world. This is a simulation-internal event and is processed exclusively within the simulation. It is used to build the world structure (e.g., terrain, objects, entities) before the clients are initialized.

#### Senders
* `SIMULATION`

#### Receivers
* `SIMULATION`

#### Data Fields
* *This event contains no data fields.*

#### Example
```json
{
  "category": "simulation",
  "name": "initialize-world",
  "data": {}
}
```

---
### `simulation:initialize`

Requests all clients to initialize themselves. This event is sent after the simulation world has been built. Each receiving client must respond with a `client:initialized` event upon completion of its initialization.

#### Senders
* `SIMULATION`

#### Receivers
* `OPTIMIZER`
* `SIMULATION`
* `VISUALIZATION`

#### Data Fields
* *This event contains no data fields.*

#### Example

```json
{
  "category": "simulation",
  "name": "initialize",
  "data": {}
}
```

#### Expected Receiver Behaviour

Each receiver must send a `client:initialized` event to the simulation after successful initialization. The simulation waits for confirmation from **all** clients before transitioning to the `RUNNING` state.

---

### `client:initialized`

Confirms that a client has completed its initialization. This event is sent in response to `simulation:initialize`. The simulation waits for a `client:initialized` event from each registered client before transitioning to the `RUNNING` state.

#### Senders
* `OPTIMIZER`
* `SIMULATION`
* `VISUALIZATION`

#### Receivers
* `SIMULATION`

#### Data Fields
* *This event contains no data fields.*

#### Example
```json
{
  "category": "client",
  "name": "initialized",
  "data": {}
}
```

---

### `simulation:pause`

Pauses the running simulation. The simulation transitions to the `PAUSED` state and confirms this by sending a `simulation:state` event.

#### Senders
* `VISUALIZATION`

#### Receivers
* `OPTIMIZER`
* `SIMULATION`
* `VISUALIZATION`

#### Data Fields
* *This event contains no data fields.*

#### Example

```json
{
  "category": "simulation",
  "name": "pause",
  "data": {}
}
```

#### Flow after Triggering

1. The visualization sends `simulation:pause`.
2. The simulation receives the event and transitions to the `PAUSED` state.
3. The simulation sends `simulation:state` with `state: "PAUSED"`.

---

### `simulation:continue`

Resumes a paused simulation. The simulation transitions back to the `RUNNING` state and confirms this by sending a `simulation:state` event.

#### Senders
* `VISUALIZATION`

#### Receivers
* `OPTIMIZER`
* `SIMULATION`
* `VISUALIZATION`

#### Data Fields
* *This event contains no data fields.*

#### Example

```json
{
  "category": "simulation",
  "name": "continue",
  "data": {}
}
```

#### Flow after Triggering

1. The visualization sends `simulation:continue`.
2. The simulation receives the event and transitions back to the `RUNNING` state.
3. The simulation sends `simulation:state` with `state: "RUNNING"`.

---

### `simulation:stop`

Stops the simulation. The simulation is terminated and transitions to the `STOPPED` state.

#### Senders
* `OPTIMIZER`
* `VISUALIZATION`

####Receivers
* `OPTIMIZER`
* `SIMULATION`
* `VISUALIZATION`

#### Data Fields
* *This event contains no data fields.*

#### Example

```json
{
  "category": "simulation",
  "name": "stop",
  "data": {}
}
```

#### Flow after Triggering

1. The optimizer or the visualization sends `simulation:stop`.
2. The simulation receives the event and transitions to the `STOPPED` state.
3. The simulation sends `simulation:state` with `state: "STOPPED"`.
