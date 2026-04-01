# State management

This document describes the canonical states for registries and records, the allowed state transitions, and the rules for updates. Use the `state-management` API routes (see the API docs) or the UI (click the ••• menu on a registry/record card) to perform state changes.

#### State transition diagram

Below is a visual diagram that summarizes the allowed transitions for registries and records. If your renderer supports SVG, the image will appear inline; otherwise the file is available at&#x20;

<figure><img src="../../.gitbook/assets/Screenshot 2025-12-16 at 6.51.00 PM.png" alt=""><figcaption></figcaption></figure>

_Diagram: registry transitions (top) and record transitions (bottom)._

#### Registry states

Available registry states:

* `live`
* `suspended`
* `revoked`
* `expired`

Rules and transitions for registries:

* From `live` a registry can be moved to any other state (`suspended`, `revoked`, `expired`).
* A `suspended` registry can be reinstated back to `live`.
* `revoked` and `expired` registries are immutable: once in these states they cannot be changed to another state.
* Registry updates (modifying registry data) are allowed only when the registry is in the `live` state.

#### Record states

Available record states:

* `draft`
* `live`
* `suspended`
* `revoked`
* `expired`

Rules and transitions for records:

* From `draft` a record can be moved only to `live`.
* From `live` a record can be moved to any state except `draft`.
* A `suspended` record can be reinstated back to `live`.
* `revoked` and `expired` records are immutable and cannot be changed to another state.
* Record updates (modifying record data) are allowed when the record is in either the `draft` or `live` state.
