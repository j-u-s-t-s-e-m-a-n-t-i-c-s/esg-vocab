# esg-vocab

**A framework-neutral semantic vocabulary for ESG data.**

Persistent URI namespace: `https://w3id.org/esg/`  
Publisher: [JustSemantics](https://github.com/j-u-s-t-s-e-m-a-n-t-i-c-s)  
License: [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/)

---

## Problem

Every major ESG reporting framework (CSRD, ESRS, GRI, TCFD, CDP, SFDR, EU Taxonomy)
defines its own disclosure schema. None share machine-readable identifiers. A "Scope 2
market-based emission" in ESRS E1-6 and a "GRI 305-2 market-based" figure are the same
concept — but no machine knows that. Every compliance tool re-implements the mapping from
scratch, incorrectly, in a spreadsheet.

This vocabulary is the missing semantic layer.

## What this is

- **Core ontology** (`ontology/esg-core.ttl`) — OWL classes for ESG domains, emission
  scopes (GHG Protocol Scope 1/2/3 + all 15 Scope 3 categories), ESG indicators,
  reporting frameworks, and unit bindings
- **QUDT unit bindings** — tCO2e, kWh, MWh, GJ, m³, t — resolving the free-text unit
  problem at the vocabulary layer
- **Wikidata alignment** — `owl:sameAs` links to Wikidata entities throughout
- **Crosswalk modules** (`crosswalks/`) — SSSOM-serialised mappings between frameworks,
  with confidence scores and boundary notes per mapping

## Crosswalks (v0.1)

| File | Coverage |
|------|----------|
| `crosswalks/esrs-e1-gri-305.ttl` | ESRS E1-6 ↔ GRI 305 (Scope 1, 2 LB, 2 MB, 3) |

Planned: ESRS ↔ TCFD, ESRS ↔ CDP, GRI ↔ EU Taxonomy, SFDR PAIs ↔ ESRS datapoints.

## Design principles

1. **Framework-neutral core** — `esg-c:` concepts exist independently of any reporting
   standard. Frameworks are modelled as `esg-c:ReportingFramework` instances.
2. **Crosswalk-first** — the mapping logic between frameworks is a first-class artefact,
   not a footnote. Each crosswalk has its own resolvable URI and version.
3. **QUDT over free text** — every indicator binds to a `qudt:Unit`. No custom unit
   strings. No "Ein Liter Heizöl".
4. **SSSOM for mappings** — machine-actionable mapping provenance with confidence scores
   and justification codes per mapping triple.
5. **Wikidata-aligned** — `owl:sameAs` to Wikidata entities where they exist, connecting
   into the broader linked data graph.

## Namespace structure

```
https://w3id.org/esg/          → core vocabulary
https://w3id.org/esg/core/     → core classes and properties
https://w3id.org/esg/esrs/     → ESRS-specific disclosure concepts
https://w3id.org/esg/gri/      → GRI-specific disclosure concepts
https://w3id.org/esg/crosswalk/→ crosswalk mapping sets
```

## Usage

```sparql
PREFIX esg-c: <https://w3id.org/esg/core/>
PREFIX esrs:  <https://w3id.org/esg/esrs/>
PREFIX gri:   <https://w3id.org/esg/gri/>
PREFIX skos:  <http://www.w3.org/2004/02/skos/core#>

# Find all GRI disclosures that map to an ESRS E1 indicator
SELECT ?esrsIndicator ?griIndicator ?confidence WHERE {
  ?mapping sssom:subject_id ?esrsIndicator ;
           sssom:object_id  ?griIndicator ;
           sssom:confidence ?confidence .
  ?esrsIndicator esg-c:reportedUnder esg-c:ESRS .
  ?griIndicator  esg-c:reportedUnder esg-c:GRI .
}
ORDER BY DESC(?confidence)
```

## Status

`v0.1.0` — initial scope taxonomy, unit bindings, framework stubs, first crosswalk.  
Not yet stable. Breaking changes possible before `v1.0.0`.

## Contributing

Issues and PRs welcome. Crosswalk contributions especially — if you have domain expertise
in TCFD, CDP or SFDR disclosure requirements, open an issue.
