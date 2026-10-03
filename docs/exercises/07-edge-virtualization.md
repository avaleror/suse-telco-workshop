# Exercise 7: Edge Virtualization

**Time:** 40 min  
**Next:** [Lab overview](../reference/lab-overview.md)

---

!!! warning "Stub manifest"
    `manifests/kubevirt-vm.yaml` is a comment stub. Do not apply it until it contains one real `VirtualMachine`.

Do this on `site-co-01` only. `site-ran-01` stays without a guest.

Guide language: Edge Virtualization. The project is KubeVirt. Operator language: the VM is a VNF. It is one guest. Harvester is out of scope.

Install the KubeVirt Helm charts from the SUSE registry, plus CDI, at the pins in `rodeo-plan.yaml`:

- KubeVirt 1.8.3
- CDI 1.65.0
- dashboard extension 1.4.2
- SUSE Storage 1.12.1 if the node has no storage class

Chart names and the registry URL are in the [Edge Virtualization](https://documentation.suse.com/suse-telco/3.7/html/telco/components-kubevirt.html) chapter. Use those. CDI imports the disk. Longhorn V2 backing images are not supported for this import.

`site-co` needs nested virt or the guest will not start. On AWS, a virtual EC2 size can fail this lab even when labs 01 through 06 passed. The metal size is the one to use when this lab has to pass. That size is not a tested pin yet.

When the manifest is real:

```bash
kubectl apply -f manifests/kubevirt-vm.yaml
kubectl get vm,vmi
kubectl get pods -A | grep -E 'virt|site-console|console'
```

Open the console pod and the VM. Both should be running on `site-co-01`.

---

**Next:** [Bonus, not numbered](../reference/bonus.md)
