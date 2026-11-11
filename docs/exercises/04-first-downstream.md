# Exercise 4: First downstream cluster

**Time:** 40 min  
**Next:** [Exercise 5: Clusters at scale](05-clusters-at-scale.md)

---

This lab creates `site-co-01`, the central office. It is a downstream cluster, also called the runtime stack. It has three control-plane nodes, one on each of `site-co`, `site-co-2` and `site-co-3`, and no workers. Three nodes is what a central office runs: the cluster keeps working if one host fails.

`manifests/site-co.yaml` is the 3.7 Metal3 quickstart set from telco-examples `release-3.7` (`e4ec0b0`), DHCP tree, with SR-IOV charts removed. The file already sets `cloudProviderEnabled: false`, RKE2 `v1.36.3+rke2r1`, Cilium, and this label:

```yaml
cluster-api.cattle.io/rancher-auto-import: "true"
```

The `Metal3DataTemplate` carries `metaData` only. There is no `networkData` and no upstream IPAM. The machine template selects hosts labeled `cluster-role: control-plane` and `site: site-co`, and three hosts match.

A three-node cluster cannot use one host's address for its API. The control plane endpoint is `192.168.122.20`, a MetalLB address. The `files` block in the same file writes Helm charts and objects into `/var/lib/rancher/rke2/server/manifests/` on every node, and RKE2 applies them on first boot:

- MetalLB and endpoint-copier-operator, which announce `192.168.122.20` and keep the `kubernetes-vip` Service pointed at the API servers
- SUSE Storage 1.12.1, three replicas, so the cluster comes up with a default storage class

This is the layout in the SUSE Telco Cloud 3.7 guide, [Downstream cluster provisioning with Directed network provisioning (multi-node)](https://documentation.suse.com/suse-telco/3.7/html/telco/multi-node.html), with MetalLB as the load balancer. As in that chapter, `RKE2ControlPlane` sets `registrationAddress` to the VIP and carries the `load-balancer-exclusion` annotation, so MetalLB stops sending API traffic to a node while Cluster API drains it. The guide's second MetalLB pool, for Traefik ingress, is left out: the labs reach the sites on NodePorts. `site-ran-01` gets the same MetalLB pattern from its ClusterClass in lab 05.

Apply it from this repo and watch Turtles import the cluster:

```bash
kubectl apply -f manifests/site-co.yaml
kubectl get clusters -A
kubectl get machines -n default
```

Cluster API adds the nodes one at a time. The cluster turns `Available` after the first node, and the other two follow. Wait until all three machines are `Running`.

In Rancher, `site-co-01` should show up because of the auto-import label. This workshop teaches that path only: provisioned by Cluster API plus Metal3, then imported by Rancher Turtles.

The hosts are `site-co`, `site-co-2` and `site-co-3`. Lab 07 needs nested virt on them. You do not start the VM in this lab.

!!! warning "Starting over"
    Delete the Cluster object only: `kubectl delete cluster -n default site-co-01`. Cluster API removes the machines and releases the host. `kubectl delete -f manifests/site-co.yaml` also deletes the Metal3MachineTemplate while the cluster is still going away, and the RKE2 control plane deletion hangs.

---

**Next:** [Exercise 5: Clusters at scale](05-clusters-at-scale.md)
