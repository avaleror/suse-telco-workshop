# SUSE Telco Cloud 3.7: Lab guide

**Version:** 3.7 | **Date:** 3 October 2026 | **Duration:** about 3 hours 15 minutes, as a writing target  
**Author:** Andres Valero, Principal Technology Advocate, SUSE

This is the print rollup of labs 01 through 07. The short pages under Exercises are the same labs with a next link.

## Before you start

Northline is a regional mobile network. One management cluster covers the region. Your job is to see two downstream clusters in Rancher Prime, then one pod and one KubeVirt guest on `site-co-01`.

This guide uses the product names (`edition: product`). With `edition: opensource` read Rancher for Rancher Prime and openSUSE Leap Micro 6.2 for SUSE Linux Micro 6.2; everything else is the same.

The KVM host is already up when class starts. libvirt network `default`, bridge `virbr0`, subnet `192.168.122.0/24`. VMs: `mgmt` (.10), `site-co` (.21), `site-co-2` (.23), `site-co-3` (.24), `site-ran` (.32). The admin password is in `~/.rodeo/secrets.yaml`.

`manifests/` is the student YAML, and rodeo does not apply it. Lab 04 applies `site-co-01`, lab 05 applies ClusterClass `example-clusterclass` and `site-ran-01`, and lab 07 applies VirtualMachine `site-co-guest` on `site-co-01`.

Words: say downstream cluster or runtime stack. Say Edge Virtualization for the feature and KubeVirt for the project. When a sentence says CNF or VNF, it is operator language. The full list is [Product language](reference/product-language.md).

## Topology

| VM | Role | Start size |
| --- | --- | --- |
| mgmt | Management cluster, image cache, Ironic, Gitea | 8 vCPU, 16 GB |
| site-co, site-co-2, site-co-3 | Hosts for `site-co-01`, nested virt | 4 vCPU, 8 GB each |
| site-ran | Host for `site-ran-01` | 4 vCPU, 8 GB |

AWS callout: EC2 is the KVM host rodeo rented. Metal3 did not create it. Bare metal callout: this machine is the KVM host, and the BMCs are still sushy-tools.

## Lab 1: Tour the region (20 min)

```bash
virsh list --all
virsh domifaddr mgmt
virsh domifaddr site-co
virsh domifaddr site-co-2
virsh domifaddr site-co-3
virsh domifaddr site-ran
```

Open a shell on `mgmt` as root (`ssh root@192.168.122.10` from the KVM host, as root) and fetch the workshop files there. Every `kubectl` and `helm` command from here on runs in that shell, from that directory:

```bash
curl -fsSL https://github.com/avaleror/suse-telco-workshop/archive/refs/heads/main.tar.gz | tar xz
cd suse-telco-workshop-main
kubectl get nodes -o wide
kubectl version
```

Find Rancher, the five guests, sushy, and the image cache. Read the versions on the landing page.

## Lab 2: Management stack (20 min)

No install. `rodeo up` already installed Rancher Prime, Turtles, Cluster API, the RKE2 providers, Metal3, and Ironic.

```bash
kubectl get pods -A
kubectl api-resources | grep -Ei 'cluster|metal3|baremetal|turtles|rke2'
```

## Lab 3: Enroll site servers (25 min)

```bash
kubectl get baremetalhosts -A -o yaml
```

All four hosts are `available`. The BMC URL uses `redfish-virtualmedia`. A real server keeps the API and changes the address.

## Lab 4: First downstream cluster (40 min)

Apply `manifests/site-co.yaml` for `site-co-01`. The file sets `cloudProviderEnabled: false`, RKE2 `v1.36.3+rke2r1`, Cilium, and `cluster-api.cattle.io/rancher-auto-import: "true"`. Three control-plane replicas, one per `site-co` host. No workers. No `networkData` on `Metal3DataTemplate`. Endpoint `192.168.122.20`, a MetalLB address. Host labels `cluster-role: control-plane` and `site: site-co`. Its `files` block ships MetalLB, endpoint-copier and SUSE Storage (three replicas) with the cluster.

```bash
kubectl apply -f manifests/site-co.yaml
kubectl get clusters -A
kubectl get machines -n default
```

Watch Turtles import `site-co-01`. To start over, delete only the Cluster: `kubectl delete cluster -n default site-co-01`. Deleting the whole file hangs the RKE2 control plane deletion.

## Lab 5: Clusters at scale (30 min)

Apply ClusterClass `example-clusterclass` in namespace `northline`, then `site-ran-01`. One host, endpoint `192.168.122.22` (a MetalLB address, the host is .32), host labels `deploy-region: northline` and `cluster-type: site-ran`. Both clusters show in one Rancher. The lab stops at two sites. The Rancher Manager 2.15 large row is the published ceiling to quote: 500 clusters and 5,000 nodes, at 16 vCPU and 64 GB per management node, under that page's assumptions.

```bash
kubectl apply -f manifests/clusterclass.yaml
kubectl apply -f manifests/site-ran.yaml
kubectl get clusters -A
```

Production RAN sites run the SUSE Linux Micro real-time image on tuned bare metal. This lab uses the standard image: the sites are nested VMs.

## Lab 6: Site console (20 min)

Label both sites `demo=true` and `site-type=downstream`, then apply `manifests/site-console-gitrepo.yaml`. NodePort `30080`. The pod is an operations console. Operator language: CNF-side workload.

```bash
kubectl get clusters.fleet.cattle.io -n fleet-default -L demo,site-type
kubectl get gitrepo -n fleet-default
kubectl get bundle -A
```

Check Bundle status. `CLUSTER_NAME` matches `site-co-01` or `site-ran-01`.

## Lab 7: Edge Virtualization (40 min)

On `site-co-01` only. KubeVirt 1.8.3 and CDI 1.65.0 (both chart `307.0.3+up0.8.0`), dashboard extension 1.4.2 (chart `307.0.5+up1.4.2`). SUSE Storage 1.12.1 is already the default class, three replicas, installed with the cluster in lab 4. Then apply VirtualMachine `site-co-guest`. CDI imports the disk. Guide language: Edge Virtualization. Operator language: the VM is a VNF.

```bash
kubectl get secret -n default site-co-01-kubeconfig -o jsonpath='{.data.value}' | base64 -d > ~/site-co-01.kubeconfig
export KUBECONFIG=~/site-co-01.kubeconfig
kubectl apply -f manifests/kubevirt-vm.yaml
kubectl get vm,vmi
```

Open the console and the VM. Both are running on `site-co-01`.

## After the labs

[Bonus, not numbered](reference/bonus.md) points at real Redfish, `dhcp-less` nmstate, SR-IOV, CPU Manager, and the air-gap ISO. Those are not required to finish the class.
