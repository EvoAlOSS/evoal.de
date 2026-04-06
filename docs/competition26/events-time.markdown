---
title: "Time Events"
permalink: /docs/competition26/events-time
date: 2026-04-06T11:36:00+02:00
author: Bernhard Berger
author_profile: false
toc: true
toc_sticky: true
sidebar:
  nav: "competition"
---

The following events handle the time progression of the simulation. They control the internal simulation clock, notify participants about the current simulation time, and allow adjusting the time scale. The simulation distinguishes between internal time ticks and externally communicated time updates. The `time:time` event is sent based on the current time scale, which determines how many internal ticks are simulated within a second before an external time update is broadcast.

## Sequence Diagram: Time Progression with Scale Change

```
VISUALIZATION                    SIMULATION                                  ALL CLIENTS
    |                                |                                            |
    |                                |-- time:tick (t=1, d=1) --> (internal)
    |                                |-- time:time (t=1, d=1) ------------------> |
    |                                |                                            |
    |                                |-- time:tick (t=2, d=1) --> (internal)
    |                                |-- time:time (t=2, d=1) ------------------> |
    |                                |                                            |
    |--- time:set-time-scale(5) --->|                                             |
    |                                |                                            |
    |                                |-- time:tick (t=3, d=1) --> (internal)
    |                                |-- time:tick (t=4, d=1) --> (internal)
    |                                |-- time:tick (t=5, d=1) --> (internal)
    |                                |-- time:tick (t=6, d=1) --> (internal)
    |                                |-- time:tick (t=7, d=1) --> (internal)
    |                                |-- time:time (t=7, d=5) ------------------> |
    |                                |                                            |
```
---

## Events in Detail

### `time:tick`
Advances the internal simulation clock by one tick (second). This is a simulation-internal event that drives the core simulation loop. Each tick represents the smallest discrete time step of the simulation. The `time:tick` event is emitted on every internal time step, regardless of the current time scale.

#### Senders
* `SIMULATION`

#### Receivers
* `SIMULATION`

#### Data Fields

| Field   | Type      | Required | Description                                              |
|---------|-----------|----------|----------------------------------------------------------|
| `delta` | `integer` | Yes      | The time increment of this tick.                         |
| `time`  | `integer` | Yes      | The current simulation time after applying the delta.    |

#### Example

```json
{
  "category": "time",
  "name": "tick",
  "data": {
    "delta": 1,
    "time": 1
  }
}
```

#### Flow after Triggering
1. The simulation processes the tick internally and updates the simulation state.
2. If the number of ticks since the last `time:time` event equals the current time scale, the simulation sends a `time:time` event to all participants.

---

### `time:time`

Notifies all participants about the current simulation time. This event is sent periodically based on the current time scale. When the time scale is `1`, a `time:time` event is sent for every internal tick. When the time scale is greater than `1`, multiple ticks are aggregated and a single `time:time` event is sent, with the `delta` field reflecting the total elapsed time since the last `time:time` event.

#### Senders
* `SIMULATION`

#### Receivers
* `OPTIMIZER`
* `SIMULATION`
* `VISUALIZATION`

#### Data Fields

| Field   | Type      | Required | Description                                                                 |
|---------|-----------|----------|-----------------------------------------------------------------------------|
| `delta` | `integer` | Yes      | The total time elapsed since the last `time:time` event was sent.           |
| `time`  | `integer` | Yes      | The current simulation time.                                                |

#### Example

With a time scale of `1`:

```json
{
  "category": "time",
  "name": "time",
  "data": {
    "delta": 1,
    "time": 1
  }
}
```

With a time scale of `5`:

```json
{
  "category": "time",
  "name": "time",
  "data": {
    "delta": 5,
    "time": 7
  }
}
```

#### Flow after Triggering
1. All receivers update their internal representation of the simulation time.
2. The visualization may update displayed time information accordingly.

---

### `time:set-time-scale`

Sets the time scale of the simulation. The time scale determines how many internal `time:tick` events are aggregated before a `time:time` event is broadcast to all participants. A time scale of `1` means every tick is communicated externally. A time scale of `5` means that five internal ticks occur before a single `time:time` event is sent.

#### Senders
* `VISUALIZATION`

#### Receivers
* `SIMULATION`

#### Data Fields

| Field   | Type      | Required | Description                                                                                  |
|---------|-----------|----------|----------------------------------------------------------------------------------------------|
| `scale` | `integer` | Yes      | The new time scale. Must be a positive integer. Determines the number of ticks per `time:time` event. |

#### Example

```json
{
  "category": "time",
  "name": "set-time-scale",
  "data": {
    "scale": 5
  }
}
```

#### Flow after Triggering
1. The visualization sends `time:set-time-scale` with the desired scale.
2. The simulation updates its internal time scale.
3. Subsequent `time:time` events are sent according to the new time scale.
