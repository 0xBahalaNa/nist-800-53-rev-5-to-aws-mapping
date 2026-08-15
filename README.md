![License](https://img.shields.io/badge/License-MIT-green?style=flat)
![AWS](https://img.shields.io/badge/AWS-Service%20Mapping-FF9900?style=flat&logo=amazonwebservices)
![OSCAL](https://img.shields.io/badge/OSCAL-Component%20Definition-1c5b94?style=flat)
![NIST 800-53](https://img.shields.io/badge/NIST-800--53%20Rev%205-004990?style=flat)
![FedRAMP](https://img.shields.io/badge/FedRAMP-High%20Baseline-0071bc?style=flat)
![CJIS](https://img.shields.io/badge/CJIS-Security%20Policy%20v6.1-cc0000?style=flat)

# NIST 800-53 Rev 5 to AWS Service Mapping

I map NIST 800-53 Rev 5 security controls to the AWS services that support their implementation, stored as an OSCAL Component Definition JSON file. A Python generator renders that mapping as markdown, with optional FedRAMP High baseline filtering and a CJIS v6.1 delta section that calls out where CJIS exceeds FedRAMP requirements. Thirty-one controls in the component definition; five of them carry a `cjis-delta` prop.

## Architecture Overview

```mermaid
graph LR
    A["data/component-definition.json<br/>OSCAL Component Definition"] --> B["scripts/generate_mapping.py"]
    B --> C["optional --fedramp-only<br/>filter fedramp-high props"]
    B --> D["output/mapping.md<br/>NIST 800-53 → AWS table"]
    C --> D
    D --> E["CJIS v6.1 Delta Requirements<br/>where CJIS exceeds FedRAMP High"]
```

Editable Mermaid source (kept in sync with the fence above): [`docs/architecture.mmd`](docs/architecture.mmd).

The OSCAL Component Definition at `data/component-definition.json` is the machine-readable mapping source (control implementations with AWS service props, including `fedramp-high` and `cjis-delta`). `scripts/generate_mapping.py` reads that JSON and writes `output/mapping.md`. Pass `--fedramp-only` to emit only FedRAMP High controls; the markdown adds a CJIS v6.1 delta section listing controls with a `cjis-delta` prop (under `--fedramp-only`, the delta section is likewise restricted to FedRAMP High controls).

## Compliance Controls Addressed

| NIST 800-53 Rev 5 | FedRAMP High | CJIS v6.1 | Validation Method |
|--------------------|:------------:|:---------:|-------------------|
| AC-2 Account Management | Yes | Need-to-know granularity + 1-day deprovisioning | OSCAL prop mapping |
| AC-3 Access Enforcement | Yes | n/a | OSCAL prop mapping |
| AC-6 Least Privilege | Yes | n/a | OSCAL prop mapping |
| AC-17 Remote Access | Yes | n/a | OSCAL prop mapping |
| AU-2 Event Logging | Yes | n/a | OSCAL prop mapping |
| AU-3 Content of Audit Records | Yes | n/a | OSCAL prop mapping |
| AU-6 Audit Record Review | Yes | 1-year retention, weekly review | OSCAL prop mapping |
| AU-6(1) Automated Integration | Yes | n/a | OSCAL prop mapping |
| AU-9 Protection of Audit Info | Yes | n/a | OSCAL prop mapping |
| AU-12 Audit Record Generation | Yes | n/a | OSCAL prop mapping |
| CM-2 Baseline Configuration | Yes | n/a | OSCAL prop mapping |
| CM-3 Configuration Change Control | Yes | n/a | OSCAL prop mapping |
| CM-6 Configuration Settings | Yes | n/a | OSCAL prop mapping |
| CM-7 Least Functionality | Yes | n/a | OSCAL prop mapping |
| CM-8 System Component Inventory | Yes | n/a | OSCAL prop mapping |
| IA-2 Identification & Authentication | Yes | AAL2 phishing-resistant MFA | OSCAL prop mapping |
| IA-5 Authenticator Management | Yes | n/a | OSCAL prop mapping |
| IA-8 Non-Organizational Users | Yes | n/a | OSCAL prop mapping |
| IR-4 Incident Handling | Yes | n/a | OSCAL prop mapping |
| IR-5 Incident Monitoring | Yes | n/a | OSCAL prop mapping |
| IR-6 Incident Reporting | Yes | CSO/FBI CJIS Division reporting | OSCAL prop mapping |
| SC-7 Boundary Protection | Yes | n/a | OSCAL prop mapping |
| SC-8 Transmission Confidentiality | Yes | n/a | OSCAL prop mapping |
| SC-12 Cryptographic Key Management | Yes | n/a | OSCAL prop mapping |
| SC-13 Cryptographic Protection | Yes | n/a | OSCAL prop mapping |
| SC-28 Protection of Info at Rest | Yes | Agency-managed CMK required | OSCAL prop mapping |
| SC-28(1) Cryptographic Protection | Yes | n/a | OSCAL prop mapping |
| SI-2 Flaw Remediation | Yes | n/a | OSCAL prop mapping |
| SI-3 Malicious Code Protection | Yes | n/a | OSCAL prop mapping |
| SI-4 System Monitoring | Yes | n/a | OSCAL prop mapping |
| SI-7 Software/Firmware Integrity | Yes | n/a | OSCAL prop mapping |

## How to Run the Generator

```bash
# Generate the full mapping (all controls)
python3 scripts/generate_mapping.py --input data/component-definition.json --output output/mapping.md

# Generate FedRAMP High baseline only
python3 scripts/generate_mapping.py --input data/component-definition.json --output output/mapping.md --fedramp-only
```

## How an Assessor Reads This Output

The generated mapping document shows which AWS services satisfy each NIST 800-53 control and how. An assessor reviewing a FedRAMP High authorization package can use the `--fedramp-only` output to verify that every in-scope control has a documented AWS service implementation. The CJIS v6.1 Delta Requirements section at the bottom identifies the specific controls where a law enforcement agency's deployment must exceed the FedRAMP High baseline.

## FedRAMP 20x Alignment

The OSCAL Component Definition format aligns with FedRAMP 20x compliance-as-code requirements. The structured JSON data source can feed directly into automated validation pipelines (e.g., compliance-trestle, OSCAL-based tooling) and continuous monitoring workflows. Same machine-readable format FedRAMP 20x will require for compliance submissions.

## CJIS v6.1 Relevance

CJIS Security Policy v6.1 (released June 25, 2026) is the current policy, aligned with NIST 800-53 Rev 5. v6.x has been the default audit baseline since April 1, 2026 (v5.9.5 sunset March 31, 2026); modernized Priority 2-4 controls are fully enforceable Oct 1, 2027 (timing varies by state CSA). This mapping identifies 5 controls where CJIS exceeds FedRAMP High: need-to-know granularity and prompt (within-one-day, AC-2(h)) deprovisioning on role change or separation (AC-2), AAL2 MFA (IA-2), 1-year audit log retention with weekly review (AU-6), agency-managed encryption keys (SC-28), and incident reporting to the CSO/FBI CJIS Division (IR-6). AC-2's review cadence is annual (AC-2(j) is unambiguous) — FedRAMP High's own ac-02_odp.10 is an organization-defined parameter typically set to annual as well, so the CJIS delta is deprovisioning speed and need-to-know granularity, not review frequency.

## Sample Evidence Output

See the generated mapping: [`output/mapping.md`](output/mapping.md)

## Project Structure

```
├── data/
│   └── component-definition.json   # OSCAL Component Definition source
├── scripts/
│   └── generate_mapping.py         # Python generator (OSCAL → markdown)
├── output/
│   └── mapping.md                  # Generated mapping document
├── docs/
│   └── architecture.mmd            # Mermaid source (sync with README fence)
├── CLAUDE.md
├── README.md
└── LICENSE.txt
```

## License

MIT
