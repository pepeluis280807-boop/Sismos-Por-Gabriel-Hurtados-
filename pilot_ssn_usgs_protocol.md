# SSN–USGS offline candidate-association pilot: protocol 1

Frozen before examining SSN–USGS candidate pairs on **2026-11-15 at
19:00:19 UTC**, after inspecting source coverage, fields and cohort sizes only.
Gabriel authorized this local pilot; no publication or change to catalogue bytes
is implied. The immutable execution copy and SHA-256 receipt are retained under
`outputs/ssn_usgs_pilot_2026-11-15/`.

## Question and limits

Can a reproducible bipartite association procedure expose conflicts and ambiguity
in a common SSN–USGS selection, while retaining original rows and source evidence?
These are **candidate associations**, not verified physical-event identities.
There is no independent reference linking the selected SSN rows to USGS IDs.
Therefore precision, recall, F1, accuracy and unique physical-event counts will
not be estimated. Agreement with a comparator is method concordance only.

## Inputs and cohort, selected without matching

Use the hash-pinned current-preferred packages in the local catalogue registry:
`usgs-mx-historical` and `ssn-mx-historical`. Verify both package and manifest
SHA-256 against the registry, then verify all contributing native CSV bytes
against those manifests. Retain native source-row references and all original
values; do not modify any source or web package.

In each source independently select:

- Origin UTC in `[2016-01-01T00:00:00Z, 2026-01-01T00:00:00Z)`.
- Reported numerical magnitude at least 3.5, with no scale conversion.
- Latitude in `[14,33]`, longitude in `[-118,-86]`, including boundaries.
- Valid finite coordinates and explicitly UTC origin timestamps.

No country/land filter or status filter. No selection by whether an event finds a
partner. The source-only profile gave 3,945 USGS and 170,880 SSN rows. This cohort
does not establish completeness or equal observation capabilities. USGS selects
earthquake rows; SSN supplies no event type. SSN magnitude type, official event ID,
uncertainties and update timestamp remain unknown; do not impute them.

## Primary candidate graph and assignment

Create bipartite nodes for individual source rows. A candidate edge requires both
`abs(origin_time_ssn - origin_time_usgs) <= 30 seconds` and epicentral great-circle
distance `<= 50 km`, inclusively, before display rounding. Use haversine with mean
Earth radius 6,371.0088 km and exact integer-nanosecond timestamp differences.
A fixed absolute distance tolerance of 1e-9 km is solely a numerical-boundary
guard, not location uncertainty. Do not constrain magnitude difference or depth:
types differ or are unknown, and SSN errors are absent.

Resolve each bipartite connected component with maximum cardinality, then minimum
sum of normalized cost `abs(delta_time)/time_window + distance/distance_window`.
Quantize this dimensionless cost to 1e-9 for reproducible optimization. Traverse
equal-cost choices by stable source keys. Missing input fields cannot increase
confidence. An assignment remains a candidate; any component containing more than
one candidate edge is explicitly ambiguous. Do not fuse catalogue rows, take
coordinate/magnitude averages, infer independence, decluster or discard evidence.

## Comparators and prespecified sensitivity

1. The candidate window alone, retaining every qualifying pair.
2. Deterministic nearest-cost greedy one-to-one assignment on exactly those edges.
3. Real GEM oq-mbtk at the separately retained source commit, SSN as primary/host
   and USGS as secondary, ID comparison disabled, 30 s / 50 km. Record GEM's own
   geodesia, date precision, inclusivity and order semantics. Differences in Earth
   models or threshold guards must be disclosed; do not silently call them equal.

Run all nine combinations `{10,30,60} seconds × {10,50,100} km`. Do not choose a
winning threshold after seeing results. Optional reversed GEM order is a labelled
order-sensitivity check, not a replacement of the primary comparator.

Use identical original USGS IDs shared by the Mexico and North America packages
as a separate exact-copy control. A successful control establishes integrity of
those copies, not SSN–USGS association accuracy or magnitude homogeneity.

## Outputs and metrics

Retain cohort CSVs; all candidate edges with time/distance/cost and component;
graph and greedy selected edges; source rows with zero candidates; component sizes
and degrees; input/protocol/script hashes; and timings. Metrics are row coverage,
edge counts, one-to-many conflicts, ambiguous component counts, graph/greedy/GEM
edge-set concordance and assignment cost. Report each source denominator separately.
Do not call an unmatched row a missed physical earthquake or an edge a confirmed
duplicate. A graph representation alone is not a demonstrated scientific advance.

## Blinded adjudication package, with labels initially empty

Prespecify 25 distinct pairs per stratum, selected by ascending SHA-256 of protocol
seed `ssn-usgs-pilot-v1` and pair keys:

- Isolated one-edge components (graph-unambiguous, **not confirmed**).
- Edges in ambiguous components.
- Symmetric graph/GEM assignment differences, after GEM output is supplied.
- Near-outside pairs from the 60 s / 100 km window, outside the primary window.

Use strata in this order and exclude already selected pairs. If fewer than 25 are
available, keep the shortage explicit; do not backfill or change criteria.
Separate a blinded measurement table with empty human judgement/evidence/reviewer
fields from a private discovery key containing methods, scores and strata. No
automatic labels or reference metrics. Parameter-only manual inspection can still
be inconclusive; independent documentary or phase evidence would be needed to
establish physical identity. No adjudication is claimed until a human records it.

Any departure, extra cohort or new threshold requires a labelled protocol revision
and disclosure of which results were already known. Current-pilot output stays
private and does not alter the article, live site or Git history.
