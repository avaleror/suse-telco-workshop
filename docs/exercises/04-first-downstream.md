# Exercise 4: First downstream cluster

**Time:** 40 min  
**Next:** [Exercise 5: Clusters at scale](05-clusters-at-scale.md)

---

!!! warning "Stub manifest"
    `manifests/downstream-single.yaml` is still a comment stub. Do not apply it until the tested 3.7 objects replace those comments. The steps below are the lab procedure.

This lab creates `site-co-01`, the central office. It is a downstream cluster, also called the runtime stack. One control-plane replica is enough. Leave workers out.

The objects follow the SUSE Telco Cloud 3.7 Metal3 quickstart, taken from telco-examples `release-3.7` (`e4ec0b0`), DHCP tree, with SR-IOV charts removed. Keep `cloudProviderEnabled: false`, RKE2 `v1.36.3+rke2r1`, Cilium, and this label:

```yaml
cluster-api.cattle.io/rancher-auto-import: "true"
```

Copy the `Metal3DataTemplate` from that line and leave `networkData` off it. Do not add upstream IPAM resources.

When the file is real, apply it from this repo and watch Turtles import the cluster:

```bash
kubectl apply -f manifests/downstream-single.yaml
kubectl get clusters -A
```

In Rancher, `site-co-01` should show up because of the auto-import label. This workshop teaches that path only: provisioned by Cluster API plus Metal3, then imported by Rancher Turtles.

The host is `site-co`. Lab 07 needs nested virt on that guest. You do not start the VM in this lab.

---

**Next:** [Exercise 5: Clusters at scale](05-clusters-at-scale.md)
