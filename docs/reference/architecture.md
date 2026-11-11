---
hide:
  - toc
---

# Architecture and hardware

The whole lab runs on nested virtualization on one KVM host. rodeo builds five VMs on that host and gives them a fake Redfish BMC, so Metal3 treats them as bare-metal servers. The lab 07 guest runs one level deeper, inside one of the three `site-co` hosts.

<div class="arch">
<svg viewBox="0 0 1200 830" xmlns="http://www.w3.org/2000/svg" role="img" aria-label="The KVM host runs libvirt, Edge Image Builder, a Redfish BMC and five VMs: mgmt, three site-co hosts and site-ran. A KubeVirt guest runs inside the site-co-01 cluster.">
<defs>
<marker id="arch-af" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="7" markerHeight="7" orient="auto-start-reverse"><path d="M0,0 L10,5 L0,10 z" class="a-mk-flow"/></marker>
<marker id="arch-ac" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="7" markerHeight="7" orient="auto-start-reverse"><path d="M0,0 L10,5 L0,10 z" class="a-mk-ctl"/></marker>
</defs>
<rect class="a-l0" x="10" y="10" width="1180" height="810" rx="16"/>
<rect class="a-chip" x="26" y="20" width="28" height="20" rx="5"/><text class="a-chip-t" x="40" y="34" text-anchor="middle">L0</text>
<text class="a-tl" x="64" y="35">KVM host · your x86_64 server · SLES 16 or openSUSE Leap 16</text>
<circle class="a-bd" cx="1166" cy="30" r="11"/><text class="a-bd-t" x="1166" y="34.5" text-anchor="middle">1</text>
<rect class="a-item" x="30" y="52" width="230" height="64" rx="8"/>
<text class="a-tx" x="44" y="77">KVM + libvirt</text><text class="a-ts" x="44" y="97">VM disks on local SSD or NVMe</text>
<rect class="a-item" x="275" y="52" width="190" height="64" rx="8"/>
<text class="a-tx" x="289" y="77">Edge Image Builder</text><text class="a-ts" x="289" y="97">builds the site OS image,</text><text class="a-ts" x="289" y="111">copies it to mgmt :8080</text>
<circle class="a-bd" cx="453" cy="64" r="11"/><text class="a-bd-t" x="453" y="68.5" text-anchor="middle">2</text>
<rect class="a-bmc" x="640" y="52" width="260" height="64" rx="8"/>
<text class="a-tx" x="654" y="77">sushy-tools Redfish BMC</text><text class="a-tm" x="654" y="97">192.168.122.1:8000 · podman</text>
<rect class="a-item" x="915" y="52" width="255" height="64" rx="8"/>
<text class="a-tx" x="929" y="77">rodeo</text><text class="a-ts" x="929" y="97">runs the build and the tear-down</text>
<line class="a-net" x1="30" y1="146" x2="1170" y2="146"/>
<text class="a-tm" x="50" y="138">virbr0 · 192.168.122.0/24 · NAT out via .1</text>
<line class="a-stub" x1="240" y1="146" x2="240" y2="190"/><circle class="a-dot" cx="240" cy="146" r="4"/>
<line class="a-stub" x1="620" y1="146" x2="620" y2="174"/><circle class="a-dot" cx="620" cy="146" r="4"/>
<line class="a-stub" x1="1040" y1="146" x2="1040" y2="190"/><circle class="a-dot" cx="1040" cy="146" r="4"/>
<path class="a-ctl" d="M700,116 L700,172" marker-end="url(#arch-ac)"/>
<path class="a-ctl" d="M890,116 L890,188" marker-end="url(#arch-ac)"/>
<text class="a-tw" x="708" y="164">virtual media + power</text>
<rect class="a-vm" x="50" y="190" width="380" height="610" rx="11"/>
<rect class="a-chip" x="392" y="200" width="28" height="20" rx="5"/><text class="a-chip-t" x="406" y="214" text-anchor="middle">L1</text>
<text class="a-th" x="66" y="218">mgmt · 192.168.122.10</text>
<text class="a-ts" x="66" y="238">VM · 8 vCPU · 16 GB · SL Micro 6.2 · single-node RKE2</text>
<rect class="a-item" x="66" y="256" width="348" height="46" rx="7"/>
<text class="a-tx" x="78" y="275">Metal3</text><text class="a-ts" x="78" y="292">baremetal-operator + Ironic</text>
<rect class="a-item" x="66" y="312" width="348" height="46" rx="7"/>
<text class="a-tx" x="78" y="331">Image cache :8080</text><text class="a-ts" x="78" y="348">imagecache.local · serves the EIB image</text>
<rect class="a-item" x="66" y="368" width="348" height="46" rx="7"/>
<text class="a-tx" x="78" y="387">Rancher Prime 2.15.1</text><text class="a-ts" x="78" y="404">UI on NodePort 30002</text>
<rect class="a-item" x="66" y="424" width="348" height="46" rx="7"/>
<text class="a-tx" x="78" y="443">Rancher Turtles + Cluster API</text><text class="a-ts" x="78" y="460">RKE2 providers · imports new clusters into Rancher</text>
<rect class="a-item" x="66" y="480" width="348" height="46" rx="7"/>
<text class="a-tx" x="78" y="499">Fleet</text><text class="a-ts" x="78" y="516">GitRepo telco-site-console · labelled sites</text>
<rect class="a-item" x="66" y="536" width="348" height="46" rx="7"/>
<text class="a-tx" x="78" y="555">Gitea :3000</text><text class="a-ts" x="78" y="572">copy of github.com/avaleror/telco-site-console</text>
<rect class="a-item" x="66" y="592" width="348" height="46" rx="7"/>
<text class="a-tx" x="78" y="611">MetalLB · cert-manager</text><text class="a-ts" x="78" y="628">supporting charts</text>
<path class="a-flow" d="M240,536 L240,528" marker-end="url(#arch-af)"/>
<rect class="a-bmh" x="66" y="660" width="348" height="58" rx="7"/>
<text class="a-tx" x="78" y="682">BareMetalHosts for the four site hosts</text>
<text class="a-ts" x="78" y="700">registered by rodeo · available before lab 04</text>
<text class="a-ts" x="66" y="750">No SUSE Storage on mgmt. Ironic keeps its data</text>
<text class="a-ts" x="66" y="766">on an emptyDir, the single-node default.</text>
<path class="a-ctl" d="M414,279 L475,279 L475,84 L638,84" marker-end="url(#arch-ac)"/>
<text class="a-tw" x="490" y="76">Redfish API</text>
<circle class="a-bd" cx="475" cy="212" r="11"/><text class="a-bd-t" x="475" y="216.5" text-anchor="middle">3</text>
<path class="a-flow" d="M414,335 L518,335" marker-end="url(#arch-af)"/>
<text class="a-tg" x="482" y="327" text-anchor="middle">OS image</text>
<circle class="a-bd" cx="447" cy="335" r="11"/><text class="a-bd-t" x="447" y="339.5" text-anchor="middle">4</text>
<path class="a-flow" d="M536,447 L416,447" marker-end="url(#arch-af)"/>
<text class="a-tg" x="482" y="439" text-anchor="middle">import</text>
<path class="a-flow" d="M414,503 L534,503" marker-end="url(#arch-af)"/>
<text class="a-tg" x="482" y="495" text-anchor="middle">bundle</text>
<circle class="a-bd" cx="447" cy="503" r="11"/><text class="a-bd-t" x="447" y="507.5" text-anchor="middle">5</text>
<rect class="a-vm" x="536" y="174" width="340" height="610" rx="11"/>
<rect class="a-vm" x="528" y="182" width="340" height="610" rx="11"/>
<rect class="a-vm" x="520" y="190" width="340" height="610" rx="11"/>
<rect class="a-chip" x="822" y="200" width="28" height="20" rx="5"/><text class="a-chip-t" x="836" y="214" text-anchor="middle">L1</text>
<text class="a-th" x="536" y="218">site-co, site-co-2, site-co-3</text>
<text class="a-ts" x="536" y="238">3 VMs · .21 .23 .24 · 4 vCPU · 8 GB each · nested virt</text>
<rect class="a-k8s" x="536" y="254" width="308" height="400" rx="10"/>
<text class="a-tl" x="552" y="278">site-co-01 · 3-node RKE2 cluster</text>
<text class="a-ts" x="552" y="296">lab 04 · API on VIP .20 (MetalLB)</text>
<rect class="a-item" x="552" y="312" width="276" height="46" rx="7"/>
<text class="a-tx" x="564" y="331">Site console pod</text><text class="a-ts" x="564" y="348">NodePort 30080 · lab 06</text>
<rect class="a-item" x="552" y="368" width="276" height="46" rx="7"/>
<text class="a-tx" x="564" y="387">SUSE Storage 1.12.1</text><text class="a-ts" x="564" y="404">3 replicas · comes with the cluster</text>
<rect class="a-item" x="552" y="424" width="276" height="46" rx="7"/>
<text class="a-tx" x="564" y="443">KubeVirt + CDI</text><text class="a-ts" x="564" y="460">lab 07</text>
<rect class="a-l2" x="552" y="486" width="276" height="152" rx="9"/>
<rect class="a-chip" x="790" y="496" width="28" height="20" rx="5"/><text class="a-chip-t" x="804" y="510" text-anchor="middle">L2</text>
<circle class="a-bd" cx="770" cy="506" r="11"/><text class="a-bd-t" x="770" y="510.5" text-anchor="middle">6</text>
<text class="a-tl" x="566" y="512">site-co-guest</text>
<text class="a-ts" x="566" y="534">KubeVirt VM with KVM acceleration</text>
<text class="a-ts" x="566" y="552">disk on SUSE Storage, 3 replicas</text>
<text class="a-tx" x="566" y="584">A VM inside a VM</text>
<text class="a-tx" x="566" y="602">on your KVM host</text>
<text class="a-ts" x="566" y="624">listed in the site-co-01 console</text>
<text class="a-ts" x="536" y="684">For lab 07, every site-co host needs /dev/kvm:</text>
<text class="a-ts" x="536" y="702">nested virt on the host and CPU mode</text>
<text class="a-ts" x="536" y="720">host-passthrough on these VMs.</text>
<rect class="a-vm" x="880" y="190" width="270" height="610" rx="11"/>
<rect class="a-chip" x="1112" y="200" width="28" height="20" rx="5"/><text class="a-chip-t" x="1126" y="214" text-anchor="middle">L1</text>
<text class="a-th" x="896" y="218">site-ran · .32</text>
<text class="a-ts" x="896" y="238">VM · 4 vCPU · 8 GB</text>
<rect class="a-k8s" x="896" y="254" width="238" height="400" rx="10"/>
<text class="a-tl" x="910" y="278">site-ran-01 · RKE2</text>
<text class="a-ts" x="910" y="296">lab 05 · from the ClusterClass</text>
<rect class="a-item" x="910" y="312" width="210" height="46" rx="7"/>
<text class="a-tx" x="922" y="331">Site console pod</text><text class="a-ts" x="922" y="348">NodePort 30080 · lab 06</text>
<rect class="a-item" x="910" y="368" width="210" height="46" rx="7"/>
<text class="a-tx" x="922" y="387">API endpoint .22</text><text class="a-ts" x="922" y="404">MetalLB address, not the host</text>
<rect class="a-item" x="910" y="424" width="210" height="46" rx="7"/>
<text class="a-tx" x="922" y="443">No KubeVirt</text><text class="a-ts" x="922" y="460">far-edge RAN site</text>
<text class="a-ts" x="896" y="684">Built the same way as site-co:</text>
<text class="a-ts" x="896" y="702">steps 3 to 5 repeat for this VM,</text>
<text class="a-ts" x="896" y="720">with a single node.</text>
</svg>
</div>

Dashed orange lines: Metal3 drives the machines through the Redfish BMC. Solid green lines: images, imports and workloads. The numbers match the steps below.

## What happens, in order

1. **rodeo up, host.** rodeo installs KVM and libvirt, creates the five VMs from `definition.yaml` on `virbr0`, and starts sushy-tools as the BMC for the four site hosts.
2. **rodeo up, mgmt.** `mgmt` boots SL Micro and gets RKE2, Rancher Prime, Turtles, Cluster API, Metal3, Gitea and the image cache. Edge Image Builder builds the site OS image on the host, and rodeo copies it to `imagecache.local:8080`.
3. **rodeo up, enroll.** rodeo registers four BareMetalHosts at `redfish-virtualmedia://192.168.122.1:8000`, one per site host, labelled by rodeo's built-in site map (`telco.sites` in `rodeo-plan.yaml` overrides it). Ironic inspects each VM through the BMC and marks it `available`. rodeo stops here, and the students take over.
4. **Labs 04 and 05.** `kubectl apply` creates the Cluster API objects. Metal3 picks hosts by their labels, three for `site-co-01` and one for `site-ran-01`, and Ironic boots its agent on each VM through BMC virtual media. The agent writes the OS image from `:8080` to the VM's disk and RKE2 starts. On the first boot RKE2 also installs what the manifests ship with each cluster: MetalLB for the API address (.20 and .22) and, on `site-co-01`, SUSE Storage. Turtles imports each cluster into Rancher.
5. **Lab 06.** Students label both clusters. Fleet clones the console repo from Gitea on `mgmt` and deploys it to each labelled site on NodePort 30080.
6. **Lab 07.** On `site-co-01` only, students install KubeVirt and CDI on top of the SUSE Storage the cluster came with. `site-co-guest` then starts on one of the three hosts, one nesting level below it, with its disk replicated across all three.

## Nesting depth

| Level | What runs there |
| --- | --- |
| L0 | The KVM host: your server |
| L1 | `mgmt`, `site-co`, `site-co-2`, `site-co-3`, `site-ran`: libvirt VMs |
| L2 | `site-co-guest`: a KubeVirt VM inside `site-co` |

Metal3 only sees a Redfish BMC, so the site VMs look like bare-metal servers to it. Swap sushy-tools for real BMCs and the student YAML stays the same. Only the BMC address changes.

If your KVM host is itself a VM, every level moves down by one. `site-co-guest` then runs three levels deep, and the hypervisor under your host must pass nested virtualization through to it.

## Hardware requirements

One machine runs the whole lab. rodeo checks the first four rows before it creates any VM and stops if one fails.

| Item | Required | Recommended | Why |
| --- | --- | --- | --- |
| CPU | x86_64 with Intel VT-x or AMD-V, turned on in the firmware | 24 or more threads | The VMs get 24 vCPU in total: 8 for `mgmt` and 4 for each of the four site hosts. The labs are tested on 16 threads, so the VMs share CPUs: all labs passed, and lab 04 took about 10 minutes for three nodes. With 24 or more threads they do not share. Fewer than 16 is not tested. |
| Nested virtualization | On in the KVM module: `kvm_intel nested=Y` or `kvm_amd nested=1` | Same | `site-co` runs the lab 07 KubeVirt guest. rodeo checks this setting but does not turn it on. |
| Memory | 48 GiB free when `rodeo up` starts | 64 GB in the machine | The VMs take 48 GiB: 16 for `mgmt` and 8 for each of the four site hosts. The host also needs memory for itself and for the Edge Image Builder run. The labs are tested on 64 GB. |
| Disk | 260 GB free in `/var/lib/libvirt/images` | 350 GB or more, SSD or NVMe | VM disks are 80 GB for `mgmt` and 40 GB for each of the four site hosts. rodeo adds 20 GB of headroom. The SL Micro base image, the built site image and the lab 07 guest disk come on top of that. The labs are tested on NVMe. Spinning disks are not tested. |
| Host OS | SLES 16 or openSUSE Leap 16 | Same | rodeo installs KVM, libvirt and podman with zypper. |
| Network | Outbound HTTPS, and `192.168.122.0/24` free on the host | Unfiltered outbound HTTPS | The lab pulls images and charts during the build and the labs. libvirt's `default` network takes `192.168.122.0/24`, so no other interface on the host can use it. |
| Access | root or sudo on the host, plus SSH from the students' laptops | Same | rodeo runs as root. Students reach the lab through the host. |

No inbound port other than SSH is needed. Rancher (`192.168.122.10:30002`), Gitea (`:3000`) and the site consoles (`:30080`) sit on the private lab network. Open them from the host, or through an SSH tunnel:

```bash
ssh -L 30002:192.168.122.10:30002 user@kvm-host   # then open https://localhost:30002
```

The labs are tested with unfiltered outbound HTTPS. If your network filters it, these are the hosts the build and the labs are known to use. The list has not been tested on a filtered network, so expect to add to it: `registry.suse.com`, `registry.rancher.com`, `charts.rancher.com`, `releases.rancher.com`, `get.rke2.io`, `github.com` and its download hosts under `githubusercontent.com`, `charts.jetstack.io`, `charts.longhorn.io`, Docker Hub (`docker.io`, `registry-1.docker.io`), `updates.suse.com` and `download.opensuse.org`.

### Check a host before `rodeo up`

```bash
grep -c -E 'vmx|svm' /proc/cpuinfo                 # more than 0: VT-x or AMD-V is on
nproc                                             # 24 or more threads recommended
cat /sys/module/kvm_intel/parameters/nested 2>/dev/null \
  || cat /sys/module/kvm_amd/parameters/nested     # Y or 1
free -g                                           # 48 GiB or more available
sudo mkdir -p /var/lib/libvirt/images && df -h /var/lib/libvirt/images   # 260 GB or more free
ip -4 addr | grep 192.168.122.                    # no output, or only virbr0
grep PRETTY_NAME /etc/os-release                  # SLES 16 or openSUSE Leap 16
rodeo doctor                                      # rodeo's own checks
```

### Turn on nested virtualization

Do this with no VMs running. On Intel:

```bash
echo "options kvm_intel nested=1" | sudo tee /etc/modprobe.d/kvm-nested.conf
sudo modprobe -r kvm_intel && sudo modprobe kvm_intel
cat /sys/module/kvm_intel/parameters/nested        # Y
```

On AMD, use `kvm_amd` in all three lines. The last command prints `1`. If `modprobe -r` fails because the module is in use, reboot. The file in `/etc/modprobe.d/` keeps the setting.

If `grep -c -E 'vmx|svm' /proc/cpuinfo` prints `0`, turn on Intel VT-x or AMD-V (sometimes called SVM) in the server's firmware setup first.
