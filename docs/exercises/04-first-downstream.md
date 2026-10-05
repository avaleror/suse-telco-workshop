# Exercise 4: First downstream cluster

**Time:** 40 min  
**Next:** [Exercise 5: Clusters at scale](05-clusters-at-scale.md)

---

This lab creates `site-co-01`, the central office. It is a downstream cluster, also called the runtime stack. One control-plane replica is enough. Leave workers out.

`manifests/downstream-single.yaml` is the 3.7 Metal3 quickstart set from telco-examples `release-3.7` (`e4ec0b0`), DHCP tree, with SR-IOV charts removed. The file already sets `cloudProviderEnabled: false`, RKE2 `v1.36.3+rke2r1`, Cilium, and this label:

```yaml
cluster-api.cattle.io/rancher-auto-import: "true"
```

The `Metal3DataTemplate` carries `metaData` only. There is no `networkData` and no upstream IPAM. The control plane endpoint is `192.168.125.21`, and the machine template selects the host labeled `cluster-role: control-plane` and `site: site-co`.

Apply it from this repo and watch Turtles import the cluster:

```bash
kubectl apply -f manifests/downstream-single.yaml
kubectl get clusters -A
```

In Rancher, `site-co-01` should show up because of the auto-import label. This workshop teaches that path only: provisioned by Cluster API plus Metal3, then imported by Rancher Turtles.

The host is `site-co`. Lab 07 needs nested virt on that guest. You do not start the VM in this lab.

---

**Next:** [Exercise 5: Clusters at scale](05-clusters-at-scale.md)
