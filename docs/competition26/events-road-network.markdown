---
title: "Road Network Events"
permalink: /docs/competition26/events-road-network
date: 2026-04-06T11:45:00+02:00
author: Bernhard Berger
author_profile: false
toc: true
toc_sticky: true
sidebar:
  nav: "competition"
---
## Road Network Events

The following events handle the creation and modification of the road network within the simulation. The road network consists of intersections (nodes) and roads (edges) that form the topology on which vehicles travel. These events are emitted during runtime when properties of roads or intersections change.

During initialization, the complete road network can also be downloaded via the simulation's REST endpoint at `/simulation/road-network/intersections` and `/simulation/road-network/roads`. This is particularly useful for the visualization to render the full road network at startup. For details on the structure, refer to the [tutorial on the road network](https://evoal.de/docs/competition26/tutorial#the-road-network).

---

### `road-network:intersections-added`

Informs all participants that new intersections have been added to the road network. Each intersection represents a node in the road network and is defined by a unique identifier and a geographic position.

#### Important
This event is not used in the competition setting.

#### Senders
* `SIMULATION`

#### Receivers
* `OPTIMIZER`
* `SIMULATION`
* `VISUALIZATION`

#### Data Fields

| Field           | Type    | Required | Description                                      |
|-----------------|---------|----------|--------------------------------------------------|
| `intersections` | `array` | Yes      | A list of intersection objects that were added.   |

Each intersection object contains:

| Field       | Type      | Required | Description                                          |
|-------------|-----------|----------|------------------------------------------------------|
| `id`        | `integer` | Yes      | The unique identifier of the intersection.           |
| `latitude`  | `number`  | Yes      | The geographic latitude of the intersection.         |
| `longitude` | `number`  | Yes      | The geographic longitude of the intersection.        |

#### Example

```json
{
  "category": "road-network",
  "name": "intersections-added",
  "data": {
    "intersections": [
      { "id": 0, "latitude": 49.87318, "longitude": 8.65148 },
      { "id": 1, "latitude": 49.87291, "longitude": 8.65026 },
      { "id": 2, "latitude": 49.87244, "longitude": 8.6484 }
    ]
  }
}
```

#### Flow after Triggering
All receivers register the new intersections in their internal representation of the road network.

---

### `road-network:roads-added`

Informs all participants that new roads have been added to the road network. Each road represents a directed edge connecting two intersections, along with properties such as length, and maximum speed.

#### Important
This event is not used in the competition setting.

#### Senders
* `SIMULATION`

#### Receivers
* `OPTIMIZER`
* `SIMULATION`
* `VISUALIZATION`

#### Data Fields

| Field   | Type    | Required | Description                            |
|---------|---------|----------|----------------------------------------|
| `roads` | `array` | Yes      | A list of road objects that were added. |

Each road object contains:

| Field           | Type      | Required | Description                                                      |
|-----------------|-----------|----------|------------------------------------------------------------------|
| `id`            | `integer` | Yes      | The unique identifier of the road.                               |
| `from`          | `integer` | Yes      | The identifier of the intersection where the road starts.        |
| `to`            | `integer` | Yes      | The identifier of the intersection where the road ends.          |
| `length`        | `number`  | Yes      | The length of the road in meters.                                |
| `maximum-speed` | `number`  | Yes      | The maximum allowed speed on the road in km/h.                   |

#### Example

```json
{
  "category": "road-network",
  "name": "roads-added",
  "data": {
    "roads": [
      { "id": 0, "from": 0, "to": 1, "length": 100.0, "maximum-speed": 50 },
      { "id": 1, "from": 1, "to": 2, "length": 150.0, "maximum-speed": 30 }
    ]
  }
}
```

#### Flow after Triggering
All receivers register the new roads in their internal representation of the road network.

---

### `road-network:changed-road-property`

Informs all participants that a property of an existing road has been changed. This event can be emitted during runtime, for example when the simulation adjusts road parameters such as the maximum speed to simulate traffic jams.

#### Senders
* `SIMULATION`

#### Receivers
* `OPTIMIZER`
* `SIMULATION`
* `VISUALIZATION`

#### Data Fields

| Field        | Type      | Required | Description                                                  |
|--------------|-----------|----------|--------------------------------------------------------------|
| `road-id`    | `integer` | Yes      | The unique identifier of the road whose property changed.    |
| `properties` | `object`  | Yes      | An object containing the changed properties and their new values. |

Known properties:

| Property        | Type     | Description                                  |
|-----------------|----------|----------------------------------------------|
| `maximum-speed` | `number` | The new maximum allowed speed in km/h.       |

#### Example

```json
{
  "category": "road-network",
  "name": "changed-road-property",
  "data": {
    "road-id": 1,
    "properties": {
      "maximum-speed": 30
    }
  }
}
```

#### Flow after Triggering
All receivers update the specified property of the given road in their internal representation.

---

### `road-network:changed-intersection-property`

Informs all participants that a property of an existing intersection has been changed. This event can be emitted during runtime when intersection parameters are modified.

#### Important
This event is not used in the competition setting.

#### Senders
* `SIMULATION`

#### Receivers
* `OPTIMIZER`,
* `SIMULATION`,
* `VISUALIZATION`

#### Data Fields

| Field             | Type      | Required | Description                                                          |
|-------------------|-----------|----------|----------------------------------------------------------------------|
| `intersection-id` | `integer` | Yes      | The unique identifier of the intersection whose property changed.    |
| `properties`      | `object`  | Yes      | An object containing the changed properties and their new values.    |

#### Example

```json
{
  "category": "road-network",
  "name": "changed-intersection-property",
  "data": {
    "intersection-id": 0,
    "properties": {}
  }
}
```

#### Flow after Triggering
1. All receivers update the specified property of the given intersection in their internal representation.
2. The visualization may update the visual representation of the affected intersection.

---

### `road-network:error`

Informs all participants about an error related to the road network. This event is sent by the simulation when an invalid operation is attempted on the road network, such as referencing a non-existent road or intersection.

#### Senders
* `SIMULATION`

#### Receivers
* `OPTIMIZER`
* `SIMULATION`
* `VISUALIZATION`

#### Data Fields

| Field     | Type     | Required | Description                                      |
|-----------|----------|----------|--------------------------------------------------|
| `reason`  | `string` | Yes      | The error reason.                                |
| `request` | `object` | Yes      | The original request.                            |

#### Example

```json
{
  "category": "road-network",
  "name": "error",
  "reason" : "id-exists-not"
  "request": event
}
```

#### Flow after Triggering
1. All receivers may log or display the error message.
2. The operation that caused the error is not applied.
