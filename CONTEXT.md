# HGT API Documentation

The language used to describe how third parties integrate assets with the Hybrid Greentech platform for real-time dispatch. This file is a glossary only — no rates, thresholds, or implementation detail.

## Language

**Device type**:
The class of equipment being dispatched, which determines the set of metrics it must publish. Provisional canonical term spanning both the delegated-operator classes and the DER types.
_Avoid_: asset class, resource type, equipment type

**Market**:
The ancillary-service or energy product an asset is delivering, which determines how fast its data must flow and how fast it must answer a dispatch.
_Avoid_: product, service, task type

**Frequency-responsive market**:
A market in which the asset acts on grid frequency it measures itself, rather than on a dispatch issued by the platform.
_Avoid_: autonomous market, local control market

**Price zone**:
A bidding zone the platform operates in, and the first segment of every MQTT topic.
_Avoid_: bidding zone, region, area, market area

**Synchronous area**:
The interconnected system whose harmonised products and activation profiles a price zone inherits. Determines which markets exist for an asset.
_Avoid_: grid, system, TSO area

**Metric**:
A single named quantity an asset publishes to the platform, with one type and one unit.
_Avoid_: measurement type, signal, telemetry field

**Power-domain metric**:
A metric tracking the asset's instantaneous power behaviour or its instantaneous power capability, and therefore carrying the market's timing obligation.
_Avoid_: fast metric

**State metric**:
A metric describing a slow-moving physical condition of the asset, whose periodic bound is set by the physics of the quantity rather than by the market.
_Avoid_: slow metric, static metric

**Up**:
The upward-regulation direction: the asset injects more power to the grid, by discharging or by reducing consumption.
_Avoid_: charge, positive, increase

**Down**:
The downward-regulation direction: the asset absorbs more power from the grid, by charging or by increasing consumption.
_Avoid_: discharge, negative, decrease

**Data frequency**:
The periodic bound on a metric, expressed as a maximum gap between consecutive published samples. It coexists with the change threshold; neither replaces the other.
_Avoid_: publish rate, sample rate, reporting interval

**Change threshold**:
The smallest movement in a metric's value that obliges the asset to publish immediately, without waiting for the periodic bound.
_Avoid_: deadband, tolerance, delta

**Roundtrip asset response**:
The bound on the time from a dispatch until the platform observes, in the asset's published data, that the asset has begun moving toward the commanded value. Completion of the ramp is not required.
_Avoid_: response time, latency, setpoint response time

**Full activation**:
The TSO's obligation for how much of the committed response the asset must actually have delivered, and by when. Owned by the TSO, not by the platform.
_Avoid_: activation time, ramp time, delivery time

**Local control**:
An arrangement where the control loop is closed outside the platform: the platform issues a task but no activations, and the counterparty computes the response itself. The counterparty answers to the TSO's obligation directly.
_Avoid_: delegated control, autonomous control, edge control

**Platform control**:
An arrangement where the platform computes the response and issues activations, and the asset executes them.
_Avoid_: central control, remote control, cloud control

### Integration surfaces

These three names are internal shorthand for how equipment reaches the platform. The published documentation uses the navigation labels instead — Individual Sites, Distributed Energy Resources, and OEM Integration.

**Delegated portfolio**:
An integration where a third party offers a fleet of small devices — EV chargers or heat pumps — for dispatch. Its devices are **resources**.
_Avoid_: aggregation, DER portfolio, fleet

**Delegated control**:
An integration where a third party dispatches larger equipment on the platform's behalf through the Delegated Operators API. Its equipment are **assets**.
_Avoid_: aggregator, operator integration

**Direct control**:
An integration where the platform drives equipment itself over an industrial protocol, through the OEM's controller. Its equipment are **assets**.
_Avoid_: PPC integration, hardware integration

**Resource**:
A single dispatchable device within a delegated portfolio, identified by `resourceId`.
_Avoid_: asset, device, unit

**Asset**:
A single dispatchable installation under delegated or direct control, identified by `assetId`.
_Avoid_: resource, site, plant
