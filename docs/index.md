---
hide:
  - toc
---

<div class="hero">
  <div class="subtitle">SUSE Telco Cloud 3.7</div>
  <h1>Northline</h1>
  <div class="tagline">Rancher Prime &bull; Cluster API &bull; Metal3 &bull; Edge Virtualization</div>

  <a href="exercises/01-environment-tour/" class="md-button md-button--primary">Start the lab &rarr;</a>
  &nbsp;
  <a href="reference/quick-reference/" class="md-button">Quick reference</a>
</div>

---

## The operator

<div class="scenario">
Northline is a regional mobile network. One management cluster covers the region. Each site is a downstream cluster: a central office, <code>site-co-01</code>, then a far-edge site, <code>site-ran-01</code>.
</div>

You prove four things, in order. The management cluster is already running Rancher Prime, Turtles, Cluster API, and Metal3. A downstream cluster is a set of Cluster API objects, bound by Metal3 to hosts that expose Redfish virtual media, then imported by Rancher. A second site is the same shape again. On `site-co-01` only, a pod and a KubeVirt VM run side by side.

The EC2 box, when you use AWS, is the KVM host rodeo rented. Metal3 did not create that instance. Every server Metal3 touches is a libvirt guest with a sushy-tools Redfish BMC. There is no Cluster API Provider AWS in this stack.

## Lab network

<div class="topology">
<pre>
KVM host
libvirt network default (virbr0), 192.168.122.0/24
│
├── mgmt       192.168.122.10  management cluster, image cache, Ironic
├── site-co    192.168.122.21  ┐
├── site-co-2  192.168.122.23  ├ hosts for site-co-01 (cluster endpoint .20), nested virt for lab 07
├── site-co-3  192.168.122.24  ┘
└── site-ran   192.168.122.32  host for site-ran-01 (cluster endpoint .22)
</pre>
</div>

| VM | Role | Start size, not a measured pin |
| --- | --- | --- |
| mgmt | Management cluster, image cache, Ironic | 8 vCPU, 16 GB |
| site-co, site-co-2, site-co-3 | The three hosts of `site-co-01`, KubeVirt in lab 07 | 4 vCPU, 8 GB each, nested virt |
| site-ran | The host of `site-ran-01` | 4 vCPU, 8 GB |

`rodeo up` stops when those hosts are `available`. You apply the downstream `Cluster` yourself. rodeo does not apply `manifests/`.

Each VM gets a fixed DHCP reservation on `192.168.122.0/24`, libvirt network `default`, bridge `virbr0`. Read each address with `virsh domifaddr`.

## Two ways to start

AWS first. Bare metal second. The labs are the same pages. The split is where the KVM host comes from.

```bash
curl -fsSL https://raw.githubusercontent.com/avaleror/rodeo-cli/main/install.sh | bash -s -- --ref v0.28.1
git clone https://github.com/avaleror/suse-telco-workshop.git
cd suse-telco-workshop
rodeo doctor
rodeo up                  # bare metal host
# AWS: rodeo up --target aws
```

`v0.28.1` (`9f616b6`) has the five-VM layout as its built-in default. `v0.27.0` was the first rodeo-cli release with `type: suse-telco`. Check the [hardware requirements](reference/architecture.md#hardware-requirements) first. Details are in the [README](https://github.com/avaleror/suse-telco-workshop) and [host setup](instructor/host-setup.md).

## Exercises

<div class="exercise-grid">

<a href="exercises/01-environment-tour/" class="exercise-card">
  <div class="ex-number">Exercise 01</div>
  <div class="ex-title">Tour the region</div>
  <div class="ex-time">20 min</div>
  <div class="ex-desc">Find Rancher, the libvirt hosts, sushy, and the image cache. Read the versions.</div>
</a>

<a href="exercises/02-management-stack/" class="exercise-card">
  <div class="ex-number">Exercise 02</div>
  <div class="ex-title">Management stack</div>
  <div class="ex-time">20 min</div>
  <div class="ex-desc">Identify Rancher Prime, Turtles, Cluster API, the RKE2 providers, Metal3, and Ironic. No install.</div>
</a>

<a href="exercises/03-enroll-hosts/" class="exercise-card">
  <div class="ex-number">Exercise 03</div>
  <div class="ex-title">Enroll site servers</div>
  <div class="ex-time">25 min</div>
  <div class="ex-desc">Read the BareMetalHost objects, the redfish-virtualmedia URL, and state available.</div>
</a>

<a href="exercises/04-first-downstream/" class="exercise-card">
  <div class="ex-number">Exercise 04</div>
  <div class="ex-title">First downstream cluster</div>
  <div class="ex-time">40 min</div>
  <div class="ex-desc">Apply the three-node manifest for site-co-01 and watch Turtles import it.</div>
</a>

<a href="exercises/05-clusters-at-scale/" class="exercise-card">
  <div class="ex-number">Exercise 05</div>
  <div class="ex-title">Clusters at scale</div>
  <div class="ex-time">30 min</div>
  <div class="ex-desc">Apply the ClusterClass and site-ran-01. Both clusters show in one Rancher.</div>
</a>

<a href="exercises/06-site-console/" class="exercise-card">
  <div class="ex-number">Exercise 06</div>
  <div class="ex-title">Site console</div>
  <div class="ex-time">20 min</div>
  <div class="ex-desc">Fleet delivers the operations console on NodePort 30080. Operator language calls this the CNF-side workload.</div>
</a>

<a href="exercises/07-edge-virtualization/" class="exercise-card">
  <div class="ex-number">Exercise 07</div>
  <div class="ex-title">Edge Virtualization</div>
  <div class="ex-time">40 min</div>
  <div class="ex-desc">On site-co-01 only, start one KubeVirt guest next to the console pod.</div>
</a>

</div>

Times are writing targets, not a measured run.

## Versions

| Piece | Pin |
| --- | --- |
| SUSE Telco Cloud | 3.7.0 |
| Host OS | SUSE Linux Micro 6.2 (`SL-Micro` images). Open source edition: openSUSE Leap Micro 6.2 |
| RKE2 | `v1.36.3+rke2r1` |
| Rancher Prime | 2.15.1. Open source edition: Rancher 2.15.1 |
| Rancher Turtles | `v0.27.1`, built into Rancher |
| Turtles providers chart | `307.0.8+up0.27.0` |
| Cluster API | `v1.13.3` |
| CAPM3 | `v1.13.2` |
| RKE2 bootstrap and control plane | `v0.25.0` |
| Metal3 chart | `307.0.31+up0.16.0` (Metal3 0.16.0, baremetal-operator 0.13.3, Ironic 38.0.0) |
| cert-manager | 1.20.1 |
| SUSE Storage | 1.12.1 |
| KubeVirt / CDI / dashboard | 1.8.3 / 1.65.0 / 1.4.2 |
| Edge Image Builder | `registry.suse.com/edge/3.7/edge-image-builder:1.3.4` |
| Kiwi | `registry.suse.com/edge/3.7/kiwi-builder:10.2.29.1` |
| CNI | Cilium |
| telco-examples | `release-3.7` (`e4ec0b0`) |

NeuVector is not in this lab. The same pins are in `rodeo-plan.yaml`.

## Pages

<div class="link-row">
  <a href="reference/lab-overview/">Lab overview</a>
  <a href="reference/quick-reference/">Quick reference</a>
  <a href="reference/product-language/">Product language</a>
  <a href="reference/bonus/">Bonus, not numbered</a>
  <a href="lab-guide/">Full lab guide (print)</a>
  <a href="instructor/host-setup/">Instructor: host setup</a>
  <a href="instructor/pre-lab-checklist/">Instructor: pre-lab checklist</a>
  <a href="https://github.com/avaleror/rodeo-cli">rodeo-cli</a>
</div>
