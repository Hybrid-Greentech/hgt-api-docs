# Key data requirements on market, not on device type

Telemetry and response requirements were previously keyed on asset class, and the previous documentation argued this was correct because publish rates are set in edge configuration rather than per trade. That reasoning held for the mechanism but produced the wrong outcome: every BESS published active power at 100 ms regardless of what it sold, roughly 21 messages per second per asset, because the strictest imaginable product set the rate for all hardware of that class. We now key data frequency and response bounds on the **market**, and key only the *set* of required metrics on **device type**.

## Considered options

- **Key on device type** (previous). Simple, single lookup, but forces every asset to the strictest rate its hardware class could theoretically support. An mFRR-only fleet paid FFR-grade bandwidth forever.
- **Key on market, resolved per active task.** Rates would follow what the asset is selling right now. Rejected: the edge would reconfigure at every task handover, and a rolling compliance window spanning a rate change is not assessable.
- **Key on market, resolved at prequalification, strictest wins** (chosen). One static edge configuration per asset, derived from the strictest market it is prequalified for.

## Consequences

- An asset prequalified only for slow markets drops from a 100 ms to a 1 minute data frequency on its market-rated metrics, taking a battery from roughly 21 messages per second to roughly 3. The reduction is bounded well short of the 600-fold change in data frequency because available energy and state of charge are market-independent at 1 s, and they become the floor.
- Market availability follows the **synchronous area**, not the price zone, so entering a new Nordic zone adds a row to a zone map rather than editing every requirements row.
- Response bounds apply only under platform control, since a locally controlled asset receives no activation to measure from. The bound is therefore conditional on topology in a way the previous device-type table never had to express.
- The requirements now span every integration surface rather than the Delegated Operators API alone, so the direct Modbus path needs response expectations it previously did not state.

## Deferred

- **Change thresholds.** Every metric is change-driven as well as periodic, but the movement that constitutes a change is unspecified per metric. The periodic bounds stand alone until these are set.
- **Heat pump temperature.** Named as a requirement during design but absent from the heat pump spec, and the spec is authoritative. Excluded from the standard rather than added to the API.
- **Activation attribution.** No field links a measurement to the activation it answers, so response bounds are verified by inference from timing and direction rather than by reference. Adequate at second-scale bounds, weak at the sub-second FFR bound.
