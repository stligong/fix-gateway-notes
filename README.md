# Multi-broker FIX gateway — architecture notesNotes on the design of a trading gateway that connects to several brokers over FIX, taken from a production system I built and still maintain. No source code and no client specifics here — the diagram is the point.## The problem
Every broker implements order entry slightly differently: field meanings, cancel semantics, execution-report ordering, what happens on a sequence gap. A strategy that has to know about those differences cannot be tested. So the differences have to be absorbed in exactly one place.
## Layers
1. **Session layer** — one connection per broker. Owns logon/logout, heartbeats, sequence numbers, reconnect and replay. Nothing above this layer knows a session was dropped.
2. **Normalisation layer** — maps each broker's message set onto one internal order model (order / cancel / replace), with a single state machine and one canonical set of execution reports.
