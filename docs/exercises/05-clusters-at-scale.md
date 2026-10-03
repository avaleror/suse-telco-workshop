# Exercise 5: Clusters at scale

**Time:** 30 min  
**Next:** [Exercise 6: Site console](06-site-console.md)

---

!!! warning "Stub manifests"
    `manifests/clusterclass.yaml` and `manifests/site-ran.yaml` are comment stubs. Do not apply them until the tested objects replace those comments.

A second site is the same shape again. `ClusterClass` is how an operator repeats it. The lab names are `site-co-01` and `site-ran-01`, one host each.

```bash
kubectl apply -f manifests/clusterclass.yaml
kubectl apply -f manifests/site-ran.yaml
kubectl get clusters -A
```

Both clusters should show in the same Rancher. `site-ran-01` is the far-edge site. It does not get KubeVirt.

Scale, as this lab teaches it, is a replica count plus one `BareMetalHost` per machine. The lab stops at two sites.

The talk track uses the Rancher Manager 2.15 large row as the published ceiling: 500 clusters and 5,000 nodes, at 16 vCPU and 64 GB per management node, under the assumptions on that page. The Telco Cloud guide does not publish a downstream count of its own. More than one management cluster is how you cover regions and limit upgrade blast radius. Northline uses one.

---

**Next:** [Exercise 6: Site console](06-site-console.md)
