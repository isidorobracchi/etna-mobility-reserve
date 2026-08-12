# Etna Mobility Reserve

## Modelling ground transport demand during disruptions at Catania Fontanarossa Airport

This project analyses how operational disruptions at **Catania Fontanarossa Airport (CTA)**, particularly those associated with Etna volcanic activity, can generate large-scale demand for terrestrial passenger transport across Sicily and nearby alternative airports.

The central idea is simple:

> **An airport diversion does not eliminate disruption. It moves part of the problem from the aviation network into the territorial transport network.**

Rather than asking whether individual flights *should* have been cancelled or diverted, the project asks:

> **When flights scheduled for Catania are diverted elsewhere, how much terrestrial transport capacity may be required to move passengers between Catania and alternative airports, and how much can coordinated fleet reuse reduce the number of physical vehicles required?**

The project combines observed flight-disruption data published by SAC with explicit passenger-demand scenarios, a bidirectional ground-mobility model and vehicle-scheduling optimization.

---

## Interactive dashboard

### Live project

**Open the interactive dashboard:**

**https://isidorobracchi.github.io/etna-catania-10aug/**

The dashboard is available in **Italian and English**.

It includes:

- aviation disruption indicators;
- diversion distribution;
- estimated passenger-transfer demand;
- animated bus movements;
- passenger-carrying services;
- empty repositioning movements;
- vehicle handling periods;
- buses available at each airport;
- passengers onboard through time;
- cumulative passenger transfers;
- fleet activity through time;
- individual vehicle itineraries;
- methodology, assumptions and limitations.

No software installation or download is required.

---

## Case study

The current case study focuses on:

**12 August 2026**

during the Etna-related disruption affecting operations at Catania Fontanarossa Airport.

The SAC dataset used for the analysis contains:

| Indicator | Value |
|---|---:|
| Flight movements | **262** |
| Cancelled movements | **173** |
| Raw diverted records | **74** |
| Cleaned diverted arrivals | **73** |

One identified placeholder/duplicate diversion record was removed through a conservative cleaning rule before the ground-mobility model was built.

Cancelled flights are **not** assumed to have been potentially divertible.

The terrestrial model is based only on the observed cleaned diversion events.

---

## Diversion geography

The 73 cleaned diverted arrivals were distributed across five alternative airports:

| Airport | Code | Diverted arrivals |
|---|---|---:|
| Palermo Falcone Borsellino | PMO | **49** |
| Trapani Birgi | TPS | **12** |
| Comiso Pio La Torre | CIY | **9** |
| Lamezia Terme | SUF | **2** |
| Reggio Calabria Tito Minniti | REG | **1** |
| **Total** |  | **73** |

These airports define the geographic structure of the terrestrial transport problem represented in the model.

---

## Research questions

The project addresses six main questions.

1. **How large was the aviation disruption?**

   How many flight movements were cancelled, diverted or otherwise affected?

2. **Where were diverted arrivals redistributed?**

   Which alternative airports absorbed flights originally scheduled for CTA?

3. **How much terrestrial passenger demand could those diversions generate?**

   Since actual passenger counts are unavailable, what range of demand emerges under explicit passenger assumptions?

4. **How many passenger-carrying bus movements would be required?**

   How does estimated passenger demand translate into vehicle loads?

5. **How many physical buses are required if vehicles can be reused?**

   The number of service missions is not the same as the number of physical buses.

6. **What does coordination change?**

   Can vehicles be reused across successive transfers, including through empty repositioning between airports?

---

## Analytical framework

The project deliberately separates four different levels of information.

### 1. Observed

Derived directly from SAC flight information:

- scheduled flight movements;
- cancellations;
- diverted arrivals;
- flight numbers;
- origins and destinations;
- scheduled times;
- reported diversion airports.

These are the empirical aviation events on which the analysis is based.

### 2. Estimated

Actual passenger counts are not available in the SAC data.

Passenger volumes are therefore estimated using explicit scenarios.

### 3. Simulated

The model generates terrestrial transfer demand from the observed diversions.

In addition to passengers arriving at alternative airports and requiring transport toward Catania, a symmetric outbound component is simulated for passengers who would need to reach the airport from which their diverted aircraft is assumed to depart.

### 4. Modelled

Vehicle scheduling is then optimized to determine how the same physical buses may be reused through time.

This stage includes:

- passenger-carrying missions;
- vehicle availability;
- loading and unloading;
- temporal flexibility;
- empty repositioning;
- reuse of vehicles across successive missions.

---

## Passenger-demand scenarios

Actual passenger counts per diverted flight are not available.

The model therefore uses three explicit scenarios:

| Scenario | Estimated passengers per diverted flight | Bus capacity |
|---|---:|---:|
| Low | 140 | 50 |
| Central | 160 | 50 |
| High | 180 | 50 |

These values are **model assumptions**, not observed passenger counts.

For the main dashboard, the **Central scenario** is used.

---

## Bidirectional ground demand

For every cleaned diverted arrival, the model represents two terrestrial directions.

### Inbound component

**Alternative airport → Catania**

This represents passengers from an observed diverted arrival who need onward terrestrial transport toward CTA / the Catania area.

The direction is based on an observed diversion event.

Passenger volume is estimated.

### Outbound component

**Catania → alternative airport**

This represents passengers assumed to require terrestrial transport from Catania toward the alternative airport from which the aircraft would operate.

This direction is simulated.

It should therefore not be interpreted as an observed passenger transfer.

---

## Central scenario results

With:

- **73 cleaned diversions**
- **160 passengers per diverted flight**
- **50-seat buses**
- bidirectional demand

the model produces:

### Passenger-transfer demand

**23,360 passenger-transfer movements**

consisting of:

- **11,680 estimated inbound movements**
- **11,680 simulated outbound movements**

The term **passenger-transfer movements** is important.

These figures do **not** represent 23,360 distinct observed individuals.

They represent modelled passenger transfers in the two directions.

---

## Bus missions

With a 50-seat vehicle:

**ceil(160 / 50) = 4 bus loads per direction**

Each diversion therefore generates:

- 4 inbound service missions
- 4 outbound service missions
- **8 service missions in total**

Across 73 diversion events:

**73 × 8 = 584 passenger-carrying bus missions**

Therefore:

**584 service missions does not mean that 584 separate buses are required.**

A central objective of the model is precisely to estimate how many of those missions can be performed by the **same physical vehicles at different times**.

---

## Coordinated fleet optimization

The current model treats the bus system as a shared fleet.

A physical bus can:

1. perform a passenger mission;
2. complete unloading;
3. become available;
4. perform another compatible mission;
5. reposition empty to another airport when necessary.

The scheduling problem therefore becomes a vehicle-reuse problem across both space and time.

The optimization was implemented using a vehicle-chain formulation solved with **Google OR-Tools**.

---

## Central fleet result

For 12 August 2026, under the Central scenario:

| Indicator | Result |
|---|---:|
| Passenger-transfer movements | **23,360** |
| Passenger-carrying missions | **584** |
| Best feasible fleet found | **118 buses** |
| Mathematical lower bound | **108 buses** |
| Absolute optimality gap | **10 buses** |
| Relative gap | **8.5%** |
| Empty repositioning legs | **13** |
| Best deadhead time found | **1,900 min** |
| Average service load factor | **80%** |

The optimization did **not** prove that 118 buses are the mathematical minimum.

The correct interpretation is:

> **Under the current assumptions, a feasible coordinated schedule was found using 118 physical buses, while the mathematical lower bound is 108.**

The optimum therefore lies somewhere between:

**108 and 118 buses**

for this model instance.

This is an optimization bound, **not a statistical confidence interval**.

---

## Deadhead optimization

Once a feasible fleet structure is identified, the model attempts to reduce empty vehicle repositioning.

The selected Central solution contains:

**13 deadhead legs**

with:

**1,900 minutes of empty repositioning**

in the best solution found during the optimization run used for the dashboard.

This should not be interpreted as the proven minimum deadhead distance or time.

The optimization did not close the second-stage optimality gap.

---

## Temporal assumptions

The model uses explicit operational parameters.

Current baseline assumptions include:

| Parameter | Value |
|---|---:|
| Dispatch delay | 15 min |
| Passenger loading | 10 min |
| Passenger unloading | 10 min |
| Maximum outbound earliness | 60 min |
| Maximum inbound waiting flexibility | 60 min |
| Aircraft turnaround assumption | 60 min |

For diverted arrivals, the scheduled CTA arrival time is used as a temporal proxy for when terrestrial passenger demand becomes available at the alternative airport.

This is a simplification.

Actual:

- diversion landing time;
- taxi time;
- passenger disembarkation;
- baggage delivery;
- ground handling duration

are not available in the dataset used for the model.

---

## Travel-time assumptions

Ground travel times are model parameters rather than observed journey times for individual missions.

The current matrix includes:

| Route | Baseline travel time |
|---|---:|
| CTA ↔ PMO | 165 min |
| CTA ↔ TPS | 220 min |
| CTA ↔ CIY | 90 min |
| CTA ↔ SUF | 170 min |
| CTA ↔ REG | 135 min |
| PMO ↔ TPS | 65 min |
| PMO ↔ CIY | 215 min |
| PMO ↔ SUF | 275 min |
| PMO ↔ REG | 250 min |
| TPS ↔ CIY | 275 min |
| TPS ↔ SUF | 340 min |
| TPS ↔ REG | 315 min |
| CIY ↔ SUF | 245 min |
| CIY ↔ REG | 215 min |
| SUF ↔ REG | 80 min |

These values are modelling parameters and should not be interpreted as exact realized journey times.

Cross-Strait and longer-distance connections are especially sensitive to real operating conditions.

---

## Interactive fleet simulation

The dashboard converts the optimized schedule into an animated network.

Each moving marker represents **one physical bus**.

### Blue vehicles

Passenger-carrying service missions.

### Orange vehicles

Empty repositioning movements.

### Handling

After reaching its destination, a bus remains unavailable during the unloading / handling period.

### Available vehicles

Stationary available buses are aggregated at airport level rather than drawn individually.

The dashboard therefore shows, through time:

- buses in service;
- buses repositioning;
- buses handling passengers;
- available buses;
- passengers currently onboard;
- passengers already transferred;
- completed missions;
- active routes.

---

## Physical fleet reuse

One of the most important distinctions in the project is:

**service missions ≠ physical vehicles**

A single bus can perform several transfers during the operating window.

For example, one physical vehicle may perform the following sequence:

- CTA → PMO
- PMO → CTA
- CTA → PMO
- PMO → CTA
- CTA → TPS
- TPS → CTA

provided that the timing of each mission is operationally compatible.

The interactive **bus explorer** in the dashboard makes this vehicle reuse directly visible.

---

## Data collection

Flight information originates from the public flight-information service of **SAC — Società Aeroporto Catania**.

The underlying data include fields such as:

- flight number;
- airline;
- scheduled time;
- estimated or rescheduled time where available;
- origin or destination;
- operational flight status;
- diversion airport.

The project preserves repeated snapshots because the SAC public interface exposes current operational information rather than a complete historical archive.

For the analytical dataset, the latest available snapshot for the operational date is used.

---

## Data cleaning

Raw operational data are not assumed to be analytically clean.

Diversion records are therefore audited before modelling.

For example, a flight appearing twice with:

- the same operational date;
- same flight number;
- same origin;
- same diversion airport;
- one record at exactly `00:00`;
- another record at a non-midnight scheduled time

is treated conservatively as a potential placeholder duplication.

Only the midnight record is excluded in that specific situation.

A genuine standalone midnight flight is not removed automatically.

For the 12 August case study:

**74 raw DVT records → 73 cleaned diversion events**

---

## Flight-status classification

The analytical baseline distinguishes:

- `CANCELLED`
- `DIVERTED`
- `OTHER`

Raw SAC status codes are retained separately.

Statuses that have not been reliably mapped are not forced into an operational category prematurely.

This avoids assigning unsupported meanings to source-system codes.

---

## Why cancellations are not converted into diversions

The dataset contains a large number of cancelled flights.

However, the project does **not** assume that these flights could simply have been redirected to another airport.

Alternative-airport capacity depends on constraints such as:

- runway capacity;
- apron and stand availability;
- ground handling;
- crew limits;
- airline operations;
- slots;
- refuelling;
- baggage systems;
- terminal processing;
- local airport capacity.

Therefore:

> **Observed cancellations are not treated as unrealized diversions.**

Any future counterfactual scenario reallocating cancelled flights would need to be presented explicitly as a **stress test**, not as a claim about what operationally should have happened.

---

## Why the model is bidirectional

Diversion events create more than an arrival problem.

If an aircraft and its operation are relocated to another airport, passengers may also need to reach that airport from Catania.

The model therefore conceptualizes airport disruption as a transport-network problem involving:

- aviation network;
- alternative-airport capacity;
- ground-transfer network.

rather than treating each airport independently.

---

## Etna Mobility Reserve

The broader policy concept behind the project is an **Etna Mobility Reserve**.

The concept does not necessarily imply a publicly owned fleet of buses.

Instead, it asks whether recurrent Etna-related disruption justifies a pre-planned terrestrial contingency capacity that could be activated rapidly.

Possible components could include:

- predefined activation thresholds;
- framework agreements with coach operators;
- airport-specific vehicle staging;
- predefined transfer corridors;
- integration with extraordinary rail services;
- coordinated passenger information;
- operational command protocols;
- dynamic passenger aggregation;
- guaranteed reserve capacity.

The policy question is therefore not necessarily:

> *How many buses should a public authority own?*

but rather:

> **How much terrestrial capacity should be guaranteed, where should it be available, and how quickly should it be activated?**

---

## Interpretation

The key analytical idea is that a diverted flight remains part of a larger mobility system.

Moving an aircraft from CTA to PMO, TPS, CIY, SUF or REG may preserve an aviation movement while creating additional terrestrial transport demand.

A resilient contingency system therefore needs to consider:

- airport capacity;
- alternative-airport capacity;
- passenger ground mobility;
- vehicle availability;
- temporal coordination.

as interconnected parts of the same network.

The project ultimately asks:

> **When disruption is recurrent, should emergency ground mobility remain an extraordinary response, or should it become a permanently dimensioned component of the transport system?**

---

## Limitations

This project is a modelling exercise, not a reconstruction of actual ground operations.

Important limitations include:

- passenger counts are scenario assumptions;
- actual passenger loads per flight are unavailable;
- actual diversion landing times are not reconstructed;
- actual disembarkation and baggage-release times are unavailable;
- road travel times are model parameters;
- the dashboard uses schematic route geometry;
- congestion is not dynamically modelled;
- driver shifts and legal driving-time constraints are not yet represented;
- PRM requirements are not separately modelled;
- baggage capacity is not explicitly represented;
- rail substitution is not included in the current optimization;
- the initial fleet configuration is a model condition rather than an operational recommendation;
- the 118-bus solution is feasible but not proven globally optimal.

These limitations are intentionally stated because the purpose of the project is not to create false operational precision.

---

## Future extensions

Potential extensions include:

- actual road-route geometries;
- dynamic traffic conditions;
- observed diversion landing times;
- driver-shift constraints;
- baggage and accessibility constraints;
- rail-coach multimodal integration;
- alternative fleet capacities;
- uncertainty and sensitivity analysis;
- additional disruption days;
- multi-day fleet planning;
- counterfactual diversion-capacity stress tests;
- alternative initial fleet distributions;
- resilience margins above the mathematical minimum;
- real-time activation logic.

---

## Disclaimer

This is an **independent analytical project**.

It is not an operational plan issued by:

- SAC;
- ENAC;
- airlines;
- airports;
- coach operators;
- railway operators;
- public authorities.

Passenger volumes, travel times and fleet requirements are model outputs based on explicitly stated assumptions.

The project does not claim that observed cancelled flights could have been diverted or that the modelled fleet configuration should have been implemented in reality.

Its purpose is to quantify and visualize the terrestrial consequences that aviation disruption can generate.

---

## Closing idea

> **The disruption can be measured. The response has to be designed.**