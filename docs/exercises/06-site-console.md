# Exercise 6: Site console

**Time:** 20 min  
**Next:** [Exercise 7: Edge Virtualization](07-edge-virtualization.md)

---

The container on the site is an operations console. Operator language: this is the CNF-side workload. The pod is still an operations console. It has no user plane, no SR-IOV, and no Multus.

Fleet delivers it. The GitRepo targets are `demo=true` and `site-type=downstream`. The Service is NodePort `30080`. Selectors inside the console `fleet.yaml` do not apply when the GitRepo sets its own targets. Do not go looking for a `bank=true` label.

```bash
ssh root@MGMT_IP "kubectl get gitrepo -n fleet-default"
ssh root@MGMT_IP "kubectl get bundle -A"
```

The GitRepo URL that Rancher stores is Gitea on `mgmt`. rodeo mirrors `fleet_repo` into that Gitea. The plan value is `https://github.com/avaleror/telco-site-console.git`. That repo is not created in this pass, and a private clone from the lab host is still an open question.

When the bundle is ready, open the console on node port 30080. `CLUSTER_NAME` must match the cluster you are on: `site-co-01` or `site-ran-01`. Check Bundle status, not only the NodePort. A LoadBalancer Service with no provider left a previous workshop Bundle `NotReady`. That Service stays out of the bundle.

Live panels are cluster facts. Simulated rows stay labeled simulated.

---

**Next:** [Exercise 7: Edge Virtualization](07-edge-virtualization.md)
