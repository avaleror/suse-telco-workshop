# Product language

Use these words on the student pages so the lab matches the SUSE Telco Cloud 3.7 guide.

| Say this | When |
| --- | --- |
| Downstream cluster, or runtime stack | The site cluster. The guide does not say "workload cluster". |
| Management cluster, or management stack | The Rancher Prime cluster that owns the sites. |
| Rancher Prime | The product. The bill of materials name is SUSE Rancher Prime 2.15.1. The open source edition runs community Rancher 2.15.1. |
| SUSE Linux Micro 6.2 | The host OS. ISO names use the `SL-Micro` prefix. The guide does not tell you to install a product named SLE Micro. |
| Edge Virtualization | The feature. |
| KubeVirt | The project, at 1.8.3 in this lab. |
| `redfish-virtualmedia` | The only BMC method this stack supports. |

Containers are the primary workload in the guide, described as CNFs across Packet Core, RAN, OSS, and BSS. When a page says CNF or VNF, label that sentence as operator language. The pod in lab 06 is an operations console. The VM in lab 07 is one guest.

CSP belongs in the instructor intro, next to the guide's words "telecom operators". Northline is a telecom operator.

Leave these out of the numbered labs:

- Harvester
- Cluster API Provider AWS
- A second management cluster
- Elemental phone-home as a numbered path
- K3s as something to install or teach

Elemental phone-home, image-based standalone clusters, and a second management cluster are real 3.7 paths. They are named on the [bonus page](bonus.md). They are not labs 01 through 07.
