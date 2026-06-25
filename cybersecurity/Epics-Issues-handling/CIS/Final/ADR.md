# CIS Benchmark Selection for Greenfield

* Status: Proposed
* Deciders: Security & Compliance Team, Volvo Client Stakeholders
* Date: 2026-06-25
* Terminology : CMO - Current Mode of Operation, FMO - Future Mode of Operation, POC - Proof of Concept, CIS - Center for Internet Security

Technical Story: Decision on which CIS benchmark version to follow for Azure & AWS regions for Volvo client foundational baseline

## Context and Problem Statement

Volvo needs to establish a foundational security baseline using CIS benchmarks for both Azure and AWS cloud environments. Multiple CIS benchmark versions exist with varying levels of maturity and tool support. The primary constraint is that monitoring capabilities must be available through Prisma CNAPP (CMO) & Defender for Cloud (FMO under POC). The decision must balance foundational security coverage with practical implementation speed and tool availability, while avoiding unnecessary future migration efforts

## Decision Drivers

* **Tool Availability**: Monitoring capabilities must be supported by Prisma CNAPP (CMO) & Defender for Cloud (FMO under POC) - CNAPP/CSPM platform as the compliance monitoring tool
* **CMO Requirements**: CMO audit/enforcement baseline is set at CIS Controls v1.4, establishing a minimum version threshold
* **Benchmark Scope Alignment**: CIS explicitly recommends Foundations benchmarks as the first step, with Service Category benchmarks as a second step for mature implementations
* **Implementation Speed vs. Freshness**: Priority given to operationalizing controls quickly rather than adopting the latest benchmark versions immediately
* **Migration Cost**: Avoiding unnecessary version jumps that would create future re-platforming work
* **Operational Risk**: Minimizing deployment complexity during initial control adoption phase

## Considered Options

### Option 1: CIS Benchmark Versions Below v1.4
Excluded because CMO audit/enforcement baseline is currently set at v1.4, making older versions non-compliant with governance requirements.

### Option 2: CIS Latest Versions (v6.0.0+)
* **Azure**: CIS_Microsoft_Azure_Foundations_Benchmark_v6.0.0
* **AWS**: CIS_Amazon_Web_Services_Foundations_Benchmark_v7.0.0

Not currently viable because these versions are not yet listed under CMO/FMO CNAPP/CSPM tool support matrix.

### Option 3: CIS Benchmark Versions v1.x - v2.x
Represent older benchmark generations and do not provide meaningful implementation advantages. Would require migration to v3.0+ in near-term, creating unnecessary extra migration steps.

### Option 4: CIS Foundational Benchmark v3.0.0 Level 1 for Azure and CIS v5.0.0 Level 1 for AWS (Recommended)
* **Azure**: CIS_Microsoft_Azure_Foundations_Benchmark_v3.0.0
* **AWS**: CIS_AWS_Foundations_Benchmark_v5.0.0

### Option 5: CIS Benchmark Versions which is higher than Option 4
* Combining first-time control deployment with aggressive benchmark targeting increases delivery risk during initial phase
* For both 4.0 & 5.0 Database services are not directly covered under foundational benchmark for Azure. It has been relocated from CIS Foundational official benchmark, which is not available in Prisma

## Recommendation Rationale

Option 4 is recommended based on the following rationale:

1. **Meets CMO Compliance Threshold**: v3.0.0 is above the minimum v1.4 requirement set by current CMO audit/enforcement baseline
2. **Tool Support Confirmed**: Both Azure v3.0.0 (Available as Preview currently in Defender for Cloud) and AWS v5.0.0 are listed in the CMO/FMO CNAPP/CSPM tool support matrix
3. **Foundational First Approach**: Aligns with CIS's own guidance that Foundations benchmarks should be the first step; Level 2 or Service Category benchmarks follow as a mature second step
4. **Optimal Maturity-to-Implementation Ratio**: Balances reasonably current baseline coverage with manageable implementation complexity
5. **Minimizes Future Migration Work**: Is established enough to represent a stable foundational target without being so new that it risks unsupported tooling gaps
6. **Prioritizes Operationalization**: Allows focus on establishing governance and remediation discipline before uplifting to newer versions

### Expected Positive Consequences (if approved)

* Establishes a current, defensible foundational security baseline aligned with CIS best practices
* Ensures all controls are monitorable through existing FMO/CMO tooling
* Reduces implementation risk by targeting a stable, mature benchmark version
* Creates a clear upgrade path for future Phase 2+ enhancements
* Enables rapid deployment and operational maturity before benchmark transitions
* Meets compliance requirements without unnecessary over-engineering
* Provides sufficient coverage of major Azure/AWS foundational security domains needed for Phase 1

### Known Trade-offs (to be accepted if approved)

* Does not capture the most recent security guidance
* Will require a future benchmark uplift initiative to achieve maximum freshness
* Latest threat landscape findings in newer versions will not be addressed until Phase 2+
* May miss emerging control categories introduced in later versions

## Pros and Cons of the Options

### Option 4: CIS Foundational Benchmark v3.0.0 Level 1 for Azure and CIS v5.0.0 Level 1 for AWS (Recommended)

* **Good** becuase both 4.0 & 5.0 Database services are not directly covered under foundational benchmark for Azure
* **Good** because it is the lowest version that still provides current foundational coverage while supported by Defender for Cloud
* **Good** because it meets the CMO v1.4 minimum requirement with a significant maturity margin
* **Good** because it allows focus on operationalization discipline before benchmark targeting
* **Good** because it establishes a stable, well-documented baseline with broad ecosystem understanding
* **Good** because it avoids the implementation risk of combining first-time deployment with aggressive version targeting
* **Bad** because it is not the latest available version
* **Bad** because future uplift to newer versions will be required as the organization matures

### Option 5: CIS Benchmark Versions which is higher than Option 4

* **Good** because it represents the most current security guidance from CIS
* **Good** because it captures emerging control categories and latest threat landscape
* **Good** because it potentially requires fewer future migrations
* **Bad** because it combines first-time control deployment with aggressive benchmark transition, increasing delivery risk
* **Bad** because tool support maturity in FMO/CMO may lag behind latest versions
* **Bad** because it prioritizes benchmark freshness over operationalization discipline
* **Bad** because implementation complexity is higher during initial Phase 1 deployment

### Option 3: CIS Benchmark v1.x - v2.x (Older Versions)

* **Good** because they require simpler implementations
* **Bad** because they represent outdated security guidance
* **Bad** because they create unnecessary future migration work (will need to uplift to 3.0+)
* **Bad** because they do not provide meaningful implementation advantages

## Links

* **Reference**: [CIS Benchmarks - Foundations First Approach](https://www.cisecurity.org/benchmarks)
* Feature reference: [Define the Cloud Security Standard and reference baseline](https://github.com/volvo-cars/azure-core-infrastructure/issues/370)
* **Related Documents**:
    - [CIS Azure Prisma Policy Control Matrix](Link 1)
    - [CIS AWS Prisma Policy Control Matrix](Link 2)
    - [CIS Azure - Azurepolicy Gap Analysis](link 3)
    - [CIS AWS - SCP Gap Analysis](Link 4)

