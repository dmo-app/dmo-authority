# Controlo Create — Peso / Comparação Known Functional Bugs

**Status:** TWO OPEN FUNCTIONAL DEFECTS RECORDED IN `dmo-app-beta/docs/KNOWN_FUNCTIONAL_ISSUES.md`

**Type:** Bug fixes + regression tests

## Scope

The current implementation repository explicitly records two JavaScript-era defects that must not be reproduced in the Beta implementation.

Both are in:

```text
Controlo Create
-> Peso
-> Comparação / historical Peso selection
```

No additional Peso Criar bug is invented by this file. If another defect is found, add it explicitly with evidence.

## BUG 1 — Historical comparison incorrectly filtered by current machine

### Problem

When the same physical CM Tool is compatible with more than one machine, valid historical Peso records are incorrectly excluded if they were produced on a different machine from the current Job On.

### Correct behavior

Historical lookup is based on the physical CM identity:

```text
current CM
-> tool_id
-> historical CM contexts for that tool_id
-> Job Ons where that CM was used
-> eligible Peso records
```

The current machine must not be a hidden mandatory equality filter.

Machine remains useful context/display information and Tool compatibility still applies to where a Tool may be used.

### Required acceptance behavior

- valid history from another compatible machine remains selectable;
- machine difference alone does not make a Peso ineligible;
- the user explicitly selects the historical production/Peso;
- no hidden `machine == current_machine` condition is reintroduced in query, API or UI.

## BUG 2 — Comparison incorrectly requires equal CM counts

### Problem

The historical comparison is incorrectly blocked when the current and historical productions contain different numbers of CM measurements.

Example:

```text
current:    CM1 CM2 CM3 CM4
historical: CM1 CM2 CM3 CM4 CM5
```

The 4-vs-5 difference is valid and must not block the operation.

### Correct behavior

Only the CMs that are validly comparable between the two productions participate.

```text
CM1 <-> CM1
CM2 <-> CM2
CM3 <-> CM3
CM4 <-> CM4

CM5 -> no counterpart -> excluded
```

The comparison average is calculated from the participating/comparable CMs only.

The operation is refused only when there are no valid CMs to compare, with an explicit reason.

## Regression-test requirement

Both defects must be covered by automated tests when the corresponding Peso/Comparação path is completed.

A refactor must not silently restore the old JavaScript behavior.

## Reviewer checks

Reject an implementation that:

- scopes CM comparison history by current machine rather than canonical CM `tool_id`;
- requires equal total CM counts;
- fabricates missing CM counterparts;
- includes unmatched CM rows in the comparison average;
- removes explicit human selection of the eligible historical production/Peso.
