# Exercise 5: Clusters at scale

**Time:** 30 min  
**Next:** [Exercise 6: Site console](06-site-console.md)

---

A second site is the same shape again. ClusterClass `example-clusterclass` in namespace `northline` is how an operator repeats it. `site-co-01` is already up from lab 04. This lab adds `site-ran-01`, one host.

```bash
kubectl apply -f manifests/clusterclass.yaml
kubectl apply -f manifests/site-ran.yaml
kubectl get clusters -A
```

`manifests/clusterclass.yaml` creates the namespace and `example-clusterclass`. `manifests/site-ran.yaml` creates `site-ran-01` from that class, one control-plane replica, RKE2 `v1.36.3+rke2r1`, the auto-import label, and endpoint `192.168.122.22`. That endpoint is a MetalLB address on the new cluster, not the address of the `site-ran` host, which is `192.168.122.32`. The class owns the control-plane machine template. Cluster API clones it for each cluster and fills in the site type, so `site-ran-01` selects `cluster-role: control-plane`, `deploy-region: northline`, and `cluster-type: site-ran` from the variable `clusterType: site-ran`.

Both clusters should show in the same Rancher. `site-ran-01` is the far-edge site. It does not get KubeVirt.

Scale, as this lab teaches it, is a replica count plus one `BareMetalHost` per machine. The lab stops at two sites.

The talk track uses the Rancher Manager 2.15 large row as the published ceiling: 500 clusters and 5,000 nodes, at 16 vCPU and 64 GB per management node, under the assumptions on that page. The Telco Cloud guide does not publish a downstream count of its own. More than one management cluster is how you cover regions and limit upgrade blast radius. Northline uses one.

---

**Next:** [Exercise 6: Site console](06-site-console.md)
