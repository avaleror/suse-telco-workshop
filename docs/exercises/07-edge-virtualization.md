# Exercise 7: Edge Virtualization

**Time:** 40 min  
**Next:** [Lab overview](../reference/lab-overview.md)

---

Do this on `site-co-01` only. `site-ran-01` stays without a guest. The guest in `manifests/kubevirt-vm.yaml` is VirtualMachine `site-co-guest`.

Guide language: Edge Virtualization. The project is KubeVirt. Operator language: the VM is a VNF. It is one guest. Harvester is out of scope.

Point `kubectl` and `helm` at `site-co-01` for every command in this lab, not at the management cluster.

## Storage first

The guest disk is a PersistentVolumeClaim, so `site-co-01` needs a storage class before CDI can import anything. A fresh site has none:

```bash
kubectl get storageclass
```

Install SUSE Storage 1.12.1 (Longhorn). `site-co-01` has one node, so set one replica, or every volume stays degraded:

```bash
helm repo add longhorn https://charts.longhorn.io
helm install longhorn longhorn/longhorn --version 1.12.1 \
  -n longhorn-system --create-namespace \
  --set persistence.defaultClassReplicaCount=1 \
  --set defaultSettings.defaultReplicaCount=1 \
  --wait --timeout 15m
kubectl get storageclass
```

`longhorn` should be the default class. SUSE Storage needs `open-iscsi` and a running `iscsid` on the node. The site image rodeo builds already has both. If the Longhorn pods never get Ready, check `iscsid` on the `site-co` host first.

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
