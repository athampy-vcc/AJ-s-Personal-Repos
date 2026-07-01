> * Level 2 must be applied for all new workloads, instead of level 1 –– And a process to mitigate/fix noncompliant resources.
 - As per CIS recommendations, the Foundations Benchmark should be considered the first step in establishing a secure baseline, while Level 2 controls and Service Category Benchmarks represent a more advanced stage of maturity. Our recommendation is to first achieve comprehensive Level 1/Foundations coverage across the current environment, ensuring a consistent baseline across all workloads. Once this baseline is established and operationalized, we can progress towards Level 2 implementation as a platform-wide initiative, following a structured and risk-based approach. Could you please review and let us know if we can include this point as part of the ADR, or if implementing Level 2 is considered mandatory?


> * It must cover entire platform –– Not limiting our focus to GF environment. Offcourse, how we apply the benchmark will differ BF Vs GF.
 - As discussed, and as confirmed by Gusten during the planning meeting, BF and Legacy are out of scope for this epic and have therefore been removed from our plans. Our understanding was that the workloads in Brownfield and Legacy would be migrated to Greenfield and addressed as part of a separate migration epic.

> * A process to be up to date with new CIS releases
 - The following item is planned to be updated as part of this feature:
[Azure Core Infrastructure Issue #378](https://github.com/orgs/volvo-cars/projects/923/views/22?filterQuery=&pane=issue&itemId=193537654&issue=volvo-cars%7Cazure-core-infrastructure%7C378&sortedBy%5Bdirection%5D=&sortedBy%5BcolumnId%5D=)

> * How will avoid policy duplication comes from CIS Vs MCSB?
 - Please note that, as the current plan was focused solely on CIS, we have not yet performed a detailed assessment of the MCSB benchmark. If MCSB is also to be considered within this epic, we have the following options in mind:
   - : Create a mapping matrix between CIS and MCSB controls. This would help determine the appropriate action for each control (for example: avoid duplication, add only where required, or track as an exception/manual control).

Could you please review and advise whether this should be considered as part of this PR, or whether we can add a separate feature for comparing with MCSB?

> * Integration with VCC cybersecurity tools.
 - When referring to VCC cybersecurity tools, are you specifically referring to the Cybersecurity Portal, or to a different tool? Additionally, would it be acceptable to consider integrations as part of future features, given that the primary focus of the current feature is to establish and define the security baseline?
