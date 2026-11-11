# Exercise 7: Edge Virtualization

**Time:** 40 min  
**Next:** [Lab overview](../reference/lab-overview.md)

---

Do this on `site-co-01` only. `site-ran-01` stays without a guest. The guest in `manifests/kubevirt-vm.yaml` is VirtualMachine `site-co-guest`.

Guide language: Edge Virtualization. The project is KubeVirt. Operator language: the VM is a VNF. It is one guest. Harvester is out of scope.

Every command in this lab goes to `site-co-01`, not to the management cluster. Cluster API keeps the cluster's kubeconfig in a Secret on `mgmt`. In your shell on `mgmt`, from the `suse-telco-workshop-main` directory, write it to a file and point `kubectl` and `helm` at it:

```bash
kubectl get secret -n default site-co-01-kubeconfig -o jsonpath='{.data.value}' | base64 -d > ~/site-co-01.kubeconfig
export KUBECONFIG=~/site-co-01.kubeconfig
kubectl get nodes
```

Three nodes, all `Ready`. The API address in that file is `https://192.168.122.20:6443`, the MetalLB address from lab 04. To go back to the management cluster, run `unset KUBECONFIG`.

## Storage is already there

The guest disk is a PersistentVolumeClaim, so `site-co-01` needs a storage class before CDI can import anything. SUSE Storage 1.12.1 (Longhorn) came with the cluster in lab 04: the `files` block in `manifests/site-co.yaml` installed it on first boot. Check it:

```bash
kubectl get storageclass
kubectl -n longhorn-system get nodes.longhorn.io
```

`longhorn` is the default class, and all three nodes show in SUSE Storage. The class keeps three replicas of every volume, one per node, so a volume survives the loss of one host. SUSE Storage needs `open-iscsi` and a running `iscsid` on every node. The site image rodeo builds already has both. If the Longhorn pods never get Ready, check `iscsid` on the `site-co` hosts first.

## KubeVirt and CDI

Install KubeVirt and CDI from the SUSE registry at the pins in `rodeo-plan.yaml`:

- KubeVirt 1.8.3, chart `307.0.3+up0.8.0`
- CDI 1.65.0, chart `307.0.3+up0.8.0`
- dashboard extension 1.4.2, chart `307.0.5+up1.4.2`

```bash
helm install kubevirt oci://registry.suse.com/edge/charts/kubevirt \
  --version 307.0.3+up0.8.0 -n kubevirt-system --create-namespace
helm install cdi oci://registry.suse.com/edge/charts/cdi \
  --version 307.0.3+up0.8.0 -n cdi-system --create-namespace
kubectl -n kubevirt-system wait kubevirt --all --for=condition=Available --timeout=600s
kubectl -n cdi-system wait cdi --all --for=condition=Available --timeout=300s
```

The [Edge Virtualization](https://documentation.suse.com/suse-telco/3.7/html/telco/components-kubevirt.html) chapter has the dashboard extension steps. CDI imports the disk. Longhorn V2 backing images are not supported for this import.

## The guest

`site-co` needs nested virt or the guest will not start. On AWS, `m8id.4xlarge` passed this lab: the guest ran with KVM acceleration, not emulation. No metal size is needed.

That file is the guest only. It does not install KubeVirt or Harvester. CDI imports the disk from the registry source in the manifest onto the default storage class.

```bash
kubectl apply -f manifests/kubevirt-vm.yaml
kubectl get dv,pvc
kubectl get vm,vmi
kubectl get pods -A | grep -E 'virt|site-console|console'
```

The DataVolume reaches `Succeeded` first, then the VMI goes `Running`. Open the console pod and `site-co-guest`. Both should be running on `site-co-01`.

---

**Next:** [Bonus, not numbered](../reference/bonus.md)
