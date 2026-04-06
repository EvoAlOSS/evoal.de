---
title: "Error Events"
permalink: /docs/competition26/events-error
date: 2026-03-13T12:35:00+02:00
author: Bernhard Berger
author_profile: false
toc: true
toc_sticky: true
sidebar:
  nav: "competition"
---

If the simulation finds an error, such as used IDs that are not known to the simulation, routes that are physically not possible, or overfilled taxis the simulator will send an error event. The category is set accordingly to the origin of the error. The remainder of the structure is always the same. 

## `<category>:error`

Informs all participants about an error related to the used category.

#### Senders
* `SIMULATION`

#### Receivers
* `OPTIMIZER`
* `SIMULATION`
* `VISUALIZATION`

#### Data Fields

| Field     | Type     | Required | Description                                      |
|-----------|----------|----------|--------------------------------------------------|
| `reason`  | `string` | Yes      | The error reason (there are several error categories, such as, "invalid-id").                                |
| `request` | `object` | Yes      | The original event.                            |

#### Example

```json
{
  "category": "<category>",
  "name": "error",
  "reason" : "<error-identifier>"
  "request": event
}
```

#### Flow after Triggering
1. All receivers may log or display the error message.
2. The operation that caused the error is not applied.
