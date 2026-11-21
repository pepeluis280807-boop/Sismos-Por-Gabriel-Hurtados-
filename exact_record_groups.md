# Exact source-copy groups

This optional browser view groups repeated source records without editing the
catalogue packages or claiming physical earthquake identities. Existing URLs
and article views retain the original row-counting default. Enable **Group exact
copies** in Explore, or share a view containing `copies=1`.

## Evidence and identity

`scripts/exact_catalog_groups.py` reads all 60 original CSV files referenced by
the four source manifests and verifies their bytes and the package hashes.
It produces `system/dist/data/exact-record-groups.json`, schema
`seismic-exact-source-evidence/1`. Each multiple-member group includes its full
original column names and literal source cells, a SHA-256 signature, and every
member's catalogue, original or derived row key, file hash and row number.

- USGS: same provider namespace, same official ComCat ID and all **22** original
  CSV cells equal. Same IDs with different revision content remain separate.
- NRCan: all **nine** original CSV cells equal, including the unnamed trailing
  columns. No official event ID or depth unit is invented.
- SSN: no exact repeated source cells were found in these inputs. Unknown SSN
  event IDs and magnitude types stay unknown.
- Different provider namespaces never establish an exact-copy group.

Equality is not inferred from the browser packages, which retain fewer source
columns. The browser pins the evidence's SHA-256 and checks its bindings to the
loaded packages, source-file manifests, source identities, available values and
geographic overlays. Missing or conflicting evidence disables grouped queries;
the ordinary source-row view remains available. The Python audit and browser
grouping must reproduce the same memberships for the same inputs.

## Counting and provenance

The fixed four-package input has **287,936** source rows and **285,768** groups:
**2,163** repeated USGS copies and **five** NRCan copies are grouped. All **4,336**
members of the 2,168 multiple-member groups remain available. These counts do not
represent independently verified unique earthquakes. The native SSN–USGS pilot
is a separate candidate-association experiment and does not change this view.

Choose a representative by fixed catalogue priority: USGS North America, then
USGS Mexico, then the stable catalogue-qualified row key. Never prefer a larger
magnitude or later update. Per-catalogue statistics describe the representative
catalogue; the memberships preserve all contributing catalogues. A group is
filtered and drawn once. Search also checks every member key. Loading the
administrative layer rebuilds the groups before subdivision filtering.

In browser SQLite:

```sql
SELECT COUNT(*) AS selected_groups,
       SUM(source_record_count) AS represented_source_rows
FROM records;

SELECT group_key, representative_key, member_key, catalogue_id, source_event_id
FROM exact_membership
ORDER BY group_key, member_key;
```

`exact_membership` contains all members of **multiple-member** selected groups;
singletons have `source_record_count=1`. Raw mode has one source row per record,
`source_record_count=1`, and an empty membership table. All query and analysis
JSON identify the counting unit and bind the evidence hash. Grouped CSV adds
source-key and membership columns; the selection ZIP also includes
`exact-groups.json` with full original-cell proofs and
`exact-copy-members.csv`. No source bytes are overwritten or deleted.

## Reproduction

From the repository root, using Python 3.10+ and Node:

```text
python scripts/exact_catalog_groups.py --verify-only
python -m unittest discover -s tests -p test_exact_catalog_groups.py -v
node tests/test_catalog_composition.cjs
node tests/test_view_state.cjs
node tests/test_sql_explorer.cjs
node tests/test_sql_panel.cjs
node tests/test_catalog_frontend.cjs
```

The independent Python and JavaScript checks compare all memberships and fixed
representatives against the same original-cell proof, including single-source
and combined views. This local implementation is not a deployment. Commit, push
and publication each require Gabriel's explicit prior authorization.
