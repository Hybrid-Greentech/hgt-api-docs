# One DER spec, keyed on resource type

EV chargers and heat pumps each had their own AsyncAPI spec, and the two agreed on almost nothing: snake_case against camelCase, a flat envelope against a nested `payload`, `timestamp_evse` against `resourceTimestamp`, raw power against availability, a power-limit activation against a directional one, mandatory acknowledgements against none at all. Onboarding BESS and PV would have added a fourth and fifth spec to that pattern. We replace both with a single spec in which the only per-type variation is **which metrics a resource publishes** and **what it declares at registration**; every message shape, topic, and control semantic is shared. Delegated Operators and the OEM/PPC path stay out of scope, and their `asset` vocabulary stays deliberately distinct from `resource`.

## Considered options

- **Unify the naming, keep two specs.** Rejected: the divergence was structural, not lexical. Renaming fields would have left two incompatible control models — a ceiling on consumption versus a directional command with no magnitude — behind a consistent vocabulary, which is worse than visible inconsistency.
- **One spec per resource type over a shared components library.** Rejected: shared components constrain field names and nothing else. Each type's activation model would still evolve independently, and the file count grows with every type onboarded.
- **One spec, per-type message variants discriminated on `resourceType` in the payload.** Rejected: this is the same drift relocated. It puts a type discriminator on every message and licenses each type to keep its own activation dialect.
- **One spec, one message set, per-type variation confined to the metric set and the registration configuration** (chosen). A new resource type costs one enum value, one configuration variant, and a row in the metric applicability table.

## Consequences

- Available power is **headroom from the current operating point**, not a rating, so an absolute setpoint is only interpretable alongside a measured position. That is why `measuredPower` is mandatory for every resource type including heat pumps, which published none under v1. The obligation exists to make headroom and setpoints commensurable, not to collect telemetry for its own sake.
- Heat pump downward capability is **redefined, not renamed**. v1's `availableDownKw` is backup-heater capacity alone, because the directional command engaged the heater specifically. Under absolute setpoints the resource stages its own equipment, so `availablePowerDown` becomes compressor headroom plus backup heater. A heat pump that treats the change as a rename under-reports downward capability by its entire compressor headroom.
- **Release cannot be a zero setpoint.** Delegated Operators encodes deactivation as `setpoint.value: 0`, which is sound for a battery, where idle is zero, and unsound for consume-only equipment, where 0 kW is a valid instruction distinct from "resume your own control". Both v1 DER specs already carried an explicit release command, and v2 keeps one rather than importing the asset-side convention.
- **The EV sign convention inverts.** v1 `power_kw` is positive for consumption; the generator convention makes the same physical situation negative. The value stays plausible while being wrong, so this is the migration's most dangerous change and the one the changelog leads with.
- Registration declares the achievable envelope, and v1 EV registration has no rated-power field at all, so `maxImportKw` is a new required field for every existing EV integrator.
- Timing bounds stay in Data Standards rather than moving into the spec, because bounds are keyed on **market** and markets span every integration surface. Duplicating the table into the DER spec would drift at the first TSO revision.
- Two DER specs run in parallel for an unbounded period. v1 stays published and fully operational for existing customers; new integrations get v2 only.
- One physical battery may be a **resource** or an **asset** depending on who closes its control loop, and the two are never interchangeable in dispatch or settlement. This is a permanent split in vocabulary, not a transitional one.

## Deferred

- **PV.** `resourceType` is an open enum, but `pv` lands in a later additive release rather than shipping with a registration variant we know is incomplete.
- **Site import and export for EV chargers.** Scoped out because an OCPP-backend integrator typically has no site-meter access. Revisit if some do; making the metrics optional for one type would mean the platform could never rely on them.
- **Machine-readable error codes.** `severity` plus a free-text `code` for v2. A structured enum waits for evidence of which codes consumers actually branch on.
- **Change thresholds for the state metrics.** ±1 kW covers the power metrics. `stateOfCharge`, `availableEnergyUp`, and `availableEnergyDown` stay periodic-only, since a 1 s bound already outpaces how fast they move.
- **The v1 heat-pump documentation error.** `heat-pumps/quickstart.mdx:97` and `:184` describe heat-pump direction as the inverse of the platform's actual semantics. The pages stay as published by decision; the correct mapping appears only in the v2 migration guide.
