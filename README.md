# NIST 800-53 Rev 5 to AWS Service Mapping

Machine-readable NIST 800-53 Rev 5 control-to-AWS-service mapping using OSCAL Component Definition JSON, with a Python generator that renders markdown output.

## Overview

This project maps NIST 800-53 Rev 5 security controls to AWS services that support their implementation. The mapping data is stored as an OSCAL Component Definition JSON file — a structured, machine-readable format that enables reuse in compliance automation pipelines.

A Python generator script parses the OSCAL data and produces a human-readable markdown mapping document with FedRAMP High baseline filtering and CJIS v6.0 delta annotations.

## Control Family Coverage

| NIST 800-53 Family | FedRAMP High | CJIS v6.0 Delta | Status |
|--------------------|-------------|------------------|--------|
| AC (Access Control) | Included | Included | Planned |
| IA (Identification & Authentication) | Included | Included | Planned |
| AU (Audit & Accountability) | Included | Included | Planned |
| SC (System & Communications Protection) | Included | Included | Planned |
| CM (Configuration Management) | Included | Included | Planned |
| SI (System & Information Integrity) | Included | — | Planned |
| IR (Incident Response) | Included | — | Planned |

## Project Structure

```
├── data/                  # OSCAL Component Definition JSON source
├── output/                # Generated markdown mapping documents
├── scripts/               # Python generator script
├── README.md
└── .gitignore
```

## Audit Relevance

- **Evidence artifact:** Maps specific AWS services to each control, showing how cloud infrastructure supports compliance requirements
- **Assessment support:** FedRAMP High baseline filtering identifies which controls apply to High-impact systems
- **CJIS differentiation:** Delta annotations highlight where CJIS v6.0 requirements exceed the FedRAMP High baseline (e.g., FIPS 140-2/3 for CJI encryption, agency-managed keys, AAL2 MFA)

## FedRAMP 20x Alignment

The OSCAL Component Definition format aligns with FedRAMP 20x compliance-as-code requirements. The structured JSON data source can feed directly into automated validation pipelines and continuous monitoring workflows.

## Data Format

The mapping data uses the OSCAL Component Definition model:

```
component → control-implementations[] → implemented-requirements[] → props[]
```

Custom properties:
- `fedramp-high`: Whether the control is in the FedRAMP High baseline
- `cjis-delta`: Where CJIS v6.0 exceeds the base control requirement

## Usage

*Coming soon — see Issue #3 for generator script development.*

## Sample Output

*Coming soon — see Issue #5 for the generated mapping document.*

## License

MIT
