# Exercise 6: Site console

**Time:** 20 min  
**Next:** [Exercise 7: Edge Virtualization](07-edge-virtualization.md)

---

The container on the site is an operations console. Operator language: this is the CNF-side workload. The pod is still an operations console. It has no user plane, no SR-IOV, and no Multus.

Fleet delivers it. The GitRepo targets clusters labelled `demo=true` and `site-type=downstream`. Selectors inside the console `fleet.yaml` do not apply when the GitRepo sets its own targets. Do not go looking for a `bank=true` label.

## Label the sites

The imported clusters carry no labels yet. Fleet reads them from the Rancher cluster objects, and those have generated IDs (`c-xxxxx`), so look each one up by its display name. You can also add the labels in the Rancher UI, under Cluster Management, Edit Config.

```bash
ssh root@MGMT_IP 'for c in site-co-01 site-ran-01; do
  id=$(kubectl get clusters.management.cattle.io \
    -o jsonpath="{.items[?(@.spec.displayName==\"$c\")].metadata.name}")
  kubectl label clusters.management.cattle.io "$id" demo=true site-type=downstream --overwrite
done'
ssh root@MGMT_IP "kubectl get clusters.fleet.cattle.io -n fleet-default -L demo,site-type"
```

Both sites should show `true` and `downstream`. Rancher copies the labels to the Fleet clusters within a few seconds.

## Deliver the console

```bash
scp manifests/site-console-gitrepo.yaml root@MGMT_IP:
ssh root@MGMT_IP "kubectl apply -f site-console-gitrepo.yaml"
ssh root@MGMT_IP "kubectl get gitrepo -n fleet-default"
ssh root@MGMT_IP "kubectl get bundle -n fleet-default"
```

The GitRepo points at Gitea on `mgmt`: `http://192.168.122.10:3000/gitea/telco-site-console.git`. rodeo copied `https://github.com/avaleror/telco-site-console.git`, the `fleet_repo` value in `rodeo-plan.yaml`, into that Gitea when it built the lab. Fleet never goes to GitHub. You can browse the repo at `http://192.168.122.10:3000/gitea/telco-site-console` from the KVM host.

The console shows its own site name in the header. Rancher labels each Fleet cluster with its display name, and the repo's `fleet.yaml` passes that label to the chart.

When the bundle is ready, open the console on node port `30080` of each site host: `192.168.122.21` for `site-co-01`, `192.168.122.32` for `site-ran-01`. `CLUSTER_NAME` in the console must match the cluster you are on. Check Bundle status, not only the NodePort. A console can answer on `30080` while the bundle is `NotReady`, for example when one image in the repo fails to pull. A LoadBalancer Service with no provider left a previous workshop Bundle `NotReady`. That Service stays out of the bundle.

Live panels are cluster facts. Simulated rows stay labeled simulated.

---

**Next:** [Exercise 7: Edge Virtualization](07-edge-virtualization.md)
