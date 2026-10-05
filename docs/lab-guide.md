# SUSE Telco Cloud 3.7: Lab guide

**Version:** 3.7 | **Date:** 3 October 2026 | **Duration:** about 3 hours 15 minutes, as a writing target  
**Author:** Andres Valero, Principal Technology Advocate, SUSE

This is the print rollup of labs 01 through 07. The short pages under Exercises are the same labs with a next link.

## Before you start

Northline is a regional mobile network. One management cluster covers the region. Your job is to see two downstream clusters in Rancher Prime, then one pod and one KubeVirt guest on `site-co-01`.

The KVM host is already up when class starts. Bridge `telco`, subnet `192.168.125.0/24`. VMs: `mgmt`, `site-co`, `site-ran`. The admin password is in `~/.rodeo/secrets.yaml`.

`manifests/` is the student YAML, and rodeo does not apply it. Lab 04 applies `site-co-01`, lab 05 applies ClusterClass `example-clusterclass` and `site-ran-01`, and lab 07 applies VirtualMachine `site-co-guest` on `site-co-01`.

Words: say downstream cluster or runtime stack. Say Edge Virtualization for the feature and KubeVirt for the project. When a sentence says CNF or VNF, it is operator language. The full list is [Product language](reference/product-language.md).

## Topology

| VM | Role | Start size |
| --- | --- | --- |
| mgmt | Management cluster, image cache, Ironic, Gitea | 8 vCPU, 16 GB |
| site-co | Host for `site-co-01`, nested virt | 4 vCPU, 8 GB |
| site-ran | Host for `site-ran-01` | 4 vCPU, 8 GB |

AWS callout: EC2 is the KVM host rodeo rented. Metal3 did not create it. Bare metal callout: this machine is the KVM host, and the BMCs are still sushy-tools.

## Lab 1: Tour the region (20 min)

```bash
virsh list --all
virsh domifaddr mgmt
virsh domifaddr site-co
virsh domifaddr site-ran
ssh root@MGMT_IP "kubectl get nodes -o wide && kubectl version"
```

Find Rancher, the three guests, sushy, and the image cache. Read the versions on the landing page.

## Lab 2: Management stack (20 min)

No install. `rodeo up` already installed Rancher Prime, Turtles, Cluster API, the RKE2 providers, Metal3, and Ironic.

```bash
ssh root@MGMT_IP "kubectl get pods -A"
ssh root@MGMT_IP "kubectl api-resources | grep -Ei 'cluster|metal3|baremetal|turtles|rke2'"
```

## Lab 3: Enroll site servers (25 min)

```bash
ssh root@MGMT_IP "kubectl get baremetalhosts -A -o yaml"
```

Both hosts are `available`. The BMC URL uses `redfish-virtualmedia`. A real server keeps the API and changes the address.

## Lab 4: First downstream cluster (40 min)

Apply `manifests/downstream-single.yaml` for `site-co-01`. The file sets `cloudProviderEnabled: false`, RKE2 `v1.36.3+rke2r1`, Cilium, and `cluster-api.cattle.io/rancher-auto-import: "true"`. One control-plane replica. No workers. No `networkData` on `Metal3DataTemplate`. Endpoint `192.168.125.21`. Host labels `cluster-role: control-plane` and `site: site-co`.

```bash
kubectl apply -f manifests/downstream-single.yaml
kubectl get clusters -A
```

Watch Turtles import `site-co-01`.

## Lab 5: Clusters at scale (30 min)

Apply ClusterClass `example-clusterclass` in namespace `northline`, then `site-ran-01`. One host, endpoint `192.168.125.22`, host labels `deploy-region: northline` and `cluster-type: site-ran`. Both clusters show in one Rancher. The lab stops at two sites. The Rancher Manager 2.15 large row is the published ceiling to quote: 500 clusters and 5,000 nodes, at 16 vCPU and 64 GB per management node, under that page's assumptions.

```bash
kubectl apply -f manifests/clusterclass.yaml
kubectl apply -f manifests/site-ran.yaml
kubectl get clusters -A
```

## Lab 6: Site console (20 min)

Fleet GitRepo labels are `demo=true` and `site-type=downstream`. NodePort `30080`. The pod is an operations console. Operator language: CNF-side workload.

```bash
ssh root@MGMT_IP "kubectl get gitrepo -n fleet-default"
ssh root@MGMT_IP "kubectl get bundle -A"
```

Check Bundle status. `CLUSTER_NAME` matches `site-co-01` or `site-ran-01`.

## Lab 7: Edge Virtualization (40 min)

On `site-co-01` only. KubeVirt 1.8.3, CDI 1.65.0, dashboard extension 1.4.2. Add SUSE Storage 1.12.1 if there is no storage class. Then apply VirtualMachine `site-co-guest`. CDI imports the disk. Guide language: Edge Virtualization. Operator language: the VM is a VNF.

```bash
kubectl apply -f manifests/kubevirt-vm.yaml
kubectl get vm,vmi
```

Open the console and the VM. Both are running on `site-co-01`.

## After the labs

[Bonus, not numbered](reference/bonus.md) points at real Redfish, `dhcp-less` nmstate, SR-IOV, CPU Manager, and the air-gap ISO. Those are not required to finish the class.
