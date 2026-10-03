# Exercise 1: Tour the region

**Time:** 20 min  
**Next:** [Exercise 2: Management stack](02-management-stack.md)

---

!!! note "AWS"
    The EC2 instance is the KVM host. rodeo rented it. Metal3 did not create that instance. Every server Metal3 enrolls is a libvirt guest with a sushy-tools Redfish BMC. There is no Cluster API Provider AWS in this stack.

!!! note "Bare metal"
    This machine is the KVM host. The BMCs are still sushy-tools. A later track points the same YAML at a server's Redfish virtual-media address.

Get your bearings before you apply anything. The lab network is bridge `telco`, subnet `192.168.125.0/24`. The VM names are `mgmt`, `site-co`, and `site-ran`.

```bash
virsh list --all
virsh domifaddr mgmt
virsh domifaddr site-co
virsh domifaddr site-ran
```

`rodeo up` should already show `mgmt` running, with `site-co` and `site-ran` present as the hosts Metal3 will enroll. Read the Rancher URL from the success screen. The admin password is in `~/.rodeo/secrets.yaml` on the KVM host. TLS is the secret from the plan, on a NodePort. You do not need a public listener on port 80 or 443.

On the management cluster, read the versions against the [landing page table](../index.md):

```bash
# Use the mgmt address from virsh domifaddr. Bridge telco, 192.168.125.0/24.
ssh root@MGMT_IP "kubectl get nodes -o wide && kubectl version"
```

Then find the image cache on `mgmt`. The cache is filled before class so you are not waiting on an Edge Image Builder run. You want the 3.7 tags from the pin table: EIB `edge-image-builder:1.3.4` and Kiwi `kiwi-builder:10.2.29.1`.

Open Rancher and confirm you are looking at one management cluster for the region. The downstream clusters are not there yet. You create those in labs 04 and 05.

---

**Next:** [Exercise 2: Management stack](02-management-stack.md)
