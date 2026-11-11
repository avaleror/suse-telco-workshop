# Exercise 3: Enroll site servers

**Time:** 25 min  
**Next:** [Exercise 4: First downstream cluster](04-first-downstream.md)

---

`rodeo up` registers four `BareMetalHost` objects and waits until they are `available`. You do not enroll them by hand. You read the objects and see what a real server would change.

```bash
ssh root@MGMT_IP "kubectl get baremetalhosts -A"
ssh root@MGMT_IP "kubectl get baremetalhosts -A -o yaml"
```

Check three fields on each host:

1. State is `available`.
2. The BMC address is a `redfish-virtualmedia` URL. That method is the only supported BMC method in this stack.
3. Three hosts carry the labels `cluster-role: control-plane` and `site: site-co`: `site-co`, `site-co-2` and `site-co-3`. The fourth, `site-ran`, lives in namespace `northline`. All four sit on libvirt network `default`, subnet `192.168.122.0/24`. The BMC is sushy-tools on the KVM host.

A real server uses this same API. The BMC URL changes from sushy-tools to that server's Redfish virtual-media address. The student YAML stays the telco-examples shape. That hardware track is not this lab.

Three hosts for the central office and one for the far edge. Every node is a control-plane node. There is no worker pool in v1.

---

**Next:** [Exercise 4: First downstream cluster](04-first-downstream.md)
