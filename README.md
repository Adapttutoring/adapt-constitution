# Adapt Educational Constitution

A foundational governing specification for the Adapt intelligent tutoring platform, establishing educational values, judgment principles, and constitutional requirements from which all future specifications derive authority.

## Project Structure

```
adapt-constitution/
├── governance/              # Constitutional governance documents
│   ├── Evidence_Manifest.md              # Provenance record
│   ├── Batch_1_Original_Evidence.md      # Immutable evidence (LOCKED)
│   ├── Batch_1_Repair_Principles.md      # Repair constraints
│   ├── Batch_1_Repair_Log.md             # Audit trail
│   └── Batch_1_Repair_Input_v2.0.md      # Working copy
├── constitution/            # Constitutional specifications
│   └── CANONICAL_TEMPLATE_v2.0.md        # Frozen template for all requirements
├── repairs/                 # Batch repair outputs
├── specs/                   # Implementation specifications
└── docs/                    # Supporting documentation
```

## Status

- **Template v2.0:** FROZEN ✓
- **Governance Framework:** LOCKED ✓
- **Source Recovery (Phase A2):** COMPLETE ✓
- **Batch 1 Repairs (Phase A2A):** IN PROGRESS
- **Foundation Freeze:** PENDING
- **Constitution v1.0:** PENDING

## Quick Start

1. **Review Governance:** Start with `governance/Evidence_Manifest.md`
2. **Understand Original Requirements:** See `governance/Batch_1_Original_Evidence.md`
3. **Review Repair Principles:** Read `governance/Batch_1_Repair_Principles.md`
4. **Track Repairs:** Monitor `governance/Batch_1_Repair_Log.md`
5. **Review Template:** Reference `constitution/CANONICAL_TEMPLATE_v2.0.md`

## Repair Workflow

For each requirement:
1. Repair using Template v2.0
2. Run six gates
3. Run Constitutional Regression Review
4. Commit: `REPAIR(EDU-REQ-XXXX): Convert to Template v2.0`
5. Submit for review
6. Apply feedback
7. Commit: `REVIEW(EDU-REQ-XXXX): Address review feedback`
8. Freeze

## Authority

This Constitution is the single source of truth for educational and architectural decisions in Adapt.

All future specifications, implementations, and product decisions derive authority from this Constitution.

## Version History

- **v2.0-frozen:** 2026-07-08 — Template frozen, source recovery complete
- **v2.0-draft:** 2026-07-08 — Template improvements and enhancements
- **v1.0-draft:** Earlier — Initial requirements (being repaired)

---

**Last Updated:** 2026-07-08  
**Maintained By:** Educational Architect  
**Authority:** Constitutional Amendment Process
