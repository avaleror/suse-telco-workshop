# Exercise 1: Tour the region

**Time:** 20 min  
**Next:** [Exercise 2: Management stack](02-management-stack.md)

---

!!! note "AWS"
    The EC2 instance is the KVM host. rodeo rented it. Metal3 did not create that instance. Every server Metal3 enrolls is a libvirt guest with a sushy-tools Redfish BMC. There is no Cluster API Provider AWS in this stack.

!!! note "Bare metal"
    This machine is the KVM host. The BMCs are still sushy-tools. A later track points the same YAML at a server's Redfish virtual-media address.

Get your bearings before you apply anything. The lab network is libvirt network `default`, bridge `virbr0`, subnet `192.168.122.0/24`. The VM names are `mgmt`, `site-co`, `site-co-2`, `site-co-3`, and `site-ran`.

```bash
virsh list --all
virsh domifaddr mgmt
virsh domifaddr site-co
virsh domifaddr site-co-2
virsh domifaddr site-co-3
virsh domifaddr site-ran
```

`rodeo up` should already show `mgmt` running, with the four site hosts present as the machines Metal3 will enroll: three for `site-co-01` and one for `site-ran-01`. Read the Rancher URL from the success screen. The admin password is in `~/.rodeo/secrets.yaml` on the KVM host. Rancher uses its own self-signed certificate (`rancher_tls.source: rancher`), on a NodePort. You do not need a public listener on port 80 or 443.

## Your shell on mgmt

Every `kubectl` and `helm` command in labs 01 through 07 runs on the management VM, `mgmt`, as root. rodeo installed both there and gave root a kubeconfig for the management cluster, so there is nothing to configure.

From the KVM host, as root (run `sudo -i` first if you logged in as another user):

```bash
ssh root@192.168.122.10
```

Then fetch the workshop files on `mgmt` and work from that directory for the rest of the class:

```bash
curl -fsSL https://github.com/avaleror/suse-telco-workshop/archive/refs/heads/main.tar.gz | tar xz
cd suse-telco-workshop-main
kubectl get nodes -o wide
kubectl version
```

`virsh` commands, like the ones above, run on the KVM host, not on `mgmt`. Keep a second terminal on the host for them.

Read the versions from `kubectl version` against the [landing page table](../index.md).

Then find the image cache on `mgmt`. It is nginx in namespace `imagecache`, serving `/opt/media` at `http://imagecache.local:8080`. It already holds `eibimage-downstream-cluster.raw` and its `.sha256`, built before class with Edge Image Builder `edge-image-builder:1.3.4`, so you are not waiting on a build.

```bash
kubectl -n imagecache get pods
ls -lh /opt/media
```

Open Rancher and confirm you are looking at one management cluster for the region. The downstream clusters are not there yet. You create those in labs 04 and 05.

---

**Next:** [Exercise 2: Management stack](02-management-stack.md)
