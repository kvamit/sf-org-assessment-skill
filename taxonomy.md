# Assessment Domains & Sub-Domains

Use this fixed taxonomy for all reports. Only populate domains the user requests; scaffold the rest as "Coming Soon" placeholders.

| Domain | Code | Sub-Domains |
|--------|------|-------------|
| **Data Management** | DM | DM-01: Object Fields · DM-02: Record Types, Page Layouts, Validation Rules · DM-03: Reports, Dashboards & Listviews Performance · DM-04: Object Storage |
| **Security & Visibility** | SV | SV-01: Security Health Check · SV-02: Release Updates · SV-03: Sharing Settings – OWD · SV-04: Profiles and Permission Sets · SV-05: Role Hierarchies |
| **Integration Architecture** | IA | IA-01: Use of Middleware and Connectors · IA-02: Use of Integration Patterns · IA-03: Integration Security · IA-04: System Administrators & Integration Users |
| **Performance & Scalability** | PS | PS-01: Ownership Skew · PS-02: Governor Limits · PS-03: Record Locking · PS-04: List Views Performance · PS-05: Apex Class Performance · PS-06: Async Apex Performance (Batches) · PS-07: Future Performance (Async Processing) · PS-08: Apex Trigger Performance · PS-09: SOQL Query Performance · PS-10: API Performance · PS-11: Reports and Dashboards Performance · PS-12: Visualforce Performance · PS-13: Automations Performance |
| **Configuration & Development** | CD | CD-01: Distribution of Metadata per API Version · CD-02: Workflows, Process Builder, Flow · CD-03: Static Resource & Custom Labels · CD-04: Coding Violations · CD-05: Coding Vulnerability · CD-06: Code Smell · CD-07: Code Performance Issues · CD-08: Apex Best Practices: Classes & Triggers · CD-09: Apex Best Practices: Tests |
| **Strategy & Governance** | SG | SG-01: SF Technical Governance · SG-02: DevOps Strategy · SG-03: Design & Development Standards · SG-04: Backup, Recovery and Archive |

## Domain Key Map (for JS navigation)

| Domain | `domainKey` | Tab emoji |
|--------|------------|-----------|
| Data Management | `'dm'` | 📦 |
| Security & Visibility | `'sv'` | 🔒 |
| Integration Architecture | `'ia'` | 🔗 |
| Performance & Scalability | `'ps'` | ⚡ |
| Configuration & Development | `'cd'` | ⚙️ |
| Strategy & Governance | `'sg'` | 🏛 |

Sub-domain `subKey` is always the lowercase hyphenated code: `'dm-01'`, `'ps-13'`, `'cd-04'`, etc.
