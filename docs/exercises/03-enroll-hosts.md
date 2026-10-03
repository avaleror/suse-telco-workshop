# Exercise 3: Enroll site servers

**Time:** 25 min  
**Next:** [Exercise 4: First downstream cluster](04-first-downstream.md)

---

`rodeo up` registers two `BareMetalHost` objects and waits until they are `available`. You do not enroll them by hand. You read the objects and see what a real server would change.

```bash
ssh root@MGMT_IP "kubectl get baremetalhosts -A"
ssh root@MGMT_IP "kubectl get baremetalhosts -A -o yaml"
```

Check three fields on each host:

1. State is `available`.
2. The BMC address is a `redfish-virtualmedia` URL. That method is the only supported BMC method in this stack.
3. One host maps to `site-co`, the other to `site-ran`. Both sit on bridge `telco`, subnet `192.168.125.0/24`. The BMC is sushy-tools on the KVM host.

A real server uses this same API. The BMC URL changes from sushy-tools to that server's Redfish virtual-media address. The student YAML stays the telco-examples shape. That hardware track is not this lab.

Two hosts are enough. There is no worker pool in v1, so you do not need a third machine.

---

**Next:** [Exercise 4: First downstream cluster](04-first-downstream.md)
