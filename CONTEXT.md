# HGT API Documentation

The language used to describe how third parties integrate assets with the Hybrid Greentech platform for real-time dispatch. This file is a glossary only — no rates, thresholds, or implementation detail.

## Language

**Device type**:
The class of equipment being dispatched, which determines the set of metrics it must publish. Scoped to assets; the resource-side equivalent is **resource type**.
_Avoid_: asset class, equipment type

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

**Active power**:
The instantaneous real power an asset is exchanging with the grid. The resource-side equivalent is **measured power**.
_Avoid_: measured power, real power, AC power

**Measured power**:
The instantaneous power a resource is drawing or delivering, signed so that export is positive. The position every setpoint and every headroom value is read against, and the reason it is published even by equipment whose response could be inferred elsewhere. The asset-side equivalent is **active power**.
_Avoid_: raw power, power reading, consumption

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

**Available power up / Available power down**:
The additional power a resource can still deliver in each direction, measured from its current operating point rather than from zero. Headroom, not a rating. It falls to zero when a constraint outside the platform's control has already claimed the capability, and it never exceeds what the equipment can physically reach.
_Avoid_: capacity, availability, flexibility, rated power, nameplate

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

**Activation**:
A real-time dispatch command instructing equipment to move to a single power setpoint now. On the asset surface it is always issued inside a task; on the resource surface it stands alone, and carries its own expiry instead.
_Avoid_: dispatch, command, signal, event

**Setpoint**:
The signed power value an activation commands, in the generator convention where export is positive and import is negative.
_Avoid_: power value, target, output

**Deactivation**:
The end of an activation on the asset surface, expressed as a setpoint of zero rather than as a separate message or a change of state.
_Avoid_: stop, cancellation, inactive

**Release**:
The end of an activation on the resource surface, expressed as its own command that returns the resource to its own control logic. Deliberately not a setpoint of zero, because zero power is itself a valid instruction to a resource that can only consume.
_Avoid_: deactivation, clear, stop, idle

**Curtailment**:
A downward activation on a generating resource: a setpoint below the power it could produce under present conditions. Its magnitude is bounded by the resource's available power down. Raising the setpoint again is an upward activation, and a **release** is the separate command that ends platform control altogether.
_Avoid_: clipping, derating, limiting, throttling

**Control granularity**:
The set of setpoints a resource can actually reach — continuous, fixed steps, or on and off only. Declared once at registration, and the reason a commanded setpoint and the power that follows it may legitimately differ.
_Avoid_: resolution, precision, step size

**Task**:
The planned window inside which activations may occur, naming the market to deliver and the magnitude committed.
_Avoid_: job, schedule, booking, session

**Heartbeat**:
A periodic liveness signal the platform publishes for each asset, carrying no dispatch meaning.
_Avoid_: keepalive, ping, health check

**Site**:
A physical location owning one or more resources or assets. Distinct from the Individual Sites integration surface, which names a control arrangement rather than a place.
_Avoid_: plant, installation, location, facility

**Site import / Site export**:
The power flowing into and out of a site at its metered connection point. A shared physical fact about the site rather than about any one resource, and reported for monitoring and settlement rather than as a dispatch limit.
_Avoid_: grid power, net power, meter power

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
A single distributed energy resource the platform dispatches individually, identified by `resourceId`. One of many like-typed devices a third party offers to the platform. Deliberately not an **asset**.
_Avoid_: asset, device, unit

**Asset**:
A single dispatchable installation under delegated or direct control, identified by `assetId`. Deliberately not a **resource**, and the two are never interchangeable in dispatch or settlement.
_Avoid_: resource, site, plant

**Resource type**:
The class of a resource, which determines the metrics it must publish and the configuration it declares at registration. Currently EV charger, heat pump, BESS, CHP, P2X, PV, and `misc` — the catch-all for dispatchable equipment with no class of its own yet — and the list grows as new classes are onboarded. CHP, P2X, and `misc` carry the heat pump metric set and declare an empty configuration. PV carries the same metric set and declares where the array sits and how it is oriented, which is what production forecasting reads. The asset-side equivalent is **device type**.
_Avoid_: device type, asset class, DER type

**Module group**:
A set of photovoltaic modules sharing one orientation and one tilt, declared at registration. A roof with two faces declares two, because production forecasting reads orientation per group rather than per resource.
_Avoid_: string, sub-array, panel group

**Resource portfolio**:
A group of resources of a single resource type, offered by one third party. Resources at one site may sit in different portfolios, and a portfolio spans many sites. Distinct from **delegated portfolio**, which names an integration arrangement rather than a grouping.
_Avoid_: fleet, aggregation, cluster

**Resource state**:
The dispatch-relevant condition of a resource, drawn from a set small enough to mean the same thing for every resource type. Deliberately not the equipment's own operating vocabulary, which describes a session rather than a readiness to be dispatched.
_Avoid_: status, availability, OCPP status

**Charging session state**:
The state of a charging session as the charge point's own protocol reports it. Carried alongside **resource state** rather than inside it, because it describes what a vehicle and a charger are doing together, not whether the resource can be dispatched.
_Avoid_: status, resource state, OCPP status

**Acknowledgement**:
A resource's answer to a single activation, carrying whether it was accepted and, when it was not, why. It exists on the resource surface because a refusal and a lost link look identical in a measurement stream. Assets have no equivalent; their response is inferred from measurements alone.
_Avoid_: ack, response, confirmation, receipt
