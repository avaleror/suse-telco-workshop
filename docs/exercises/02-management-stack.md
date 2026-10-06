# Exercise 2: Management stack

**Time:** 20 min  
**Next:** [Exercise 3: Enroll site servers](03-enroll-hosts.md)

---

`rodeo up` already installed the management stack. This lab is identification, not an install.

In Rancher, and with kubectl on `mgmt`, find each of these:

- Rancher Prime 2.15.1
- Rancher Turtles `v0.27.1`, built into Rancher, plus the providers chart `307.0.8+up0.27.0`
- Cluster API controller `v1.13.3`
- The RKE2 bootstrap and control-plane providers, `v0.25.0`
- CAPM3 `v1.13.2`
- Metal3, chart `307.0.31+up0.16.0`, with baremetal-operator 0.13.3
- Ironic 38.0.0
- cert-manager 1.20.1

```bash
ssh root@MGMT_IP "kubectl get pods -A"
ssh root@MGMT_IP "kubectl api-resources | grep -Ei 'cluster|metal3|baremetal|turtles|rke2'"
```

!!! note "Open source edition"
    With `edition: opensource` you see Rancher 2.15.1 from the community chart instead of Rancher Prime, and the node OS is openSUSE Leap Micro 6.2. Every other component and version on this page is the same.

The management node is RKE2 `v1.36.3+rke2r1` on SUSE Linux Micro 6.2. Cilium is the CNI on that image. Ironic keeps its working data on an `emptyDir` volume, the Metal3 chart default for a single management node. SUSE Storage is not installed on `mgmt`.

You should see the controllers the platform waits for: Cluster API, CAPM3, IPAM CRDs, the RKE2 providers, cert-manager, baremetal-operator, Ironic, and Turtles. IPAM CRDs can be present. This lab does not use upstream Metal3 IPAM objects.

NeuVector is not part of this lab. If you do not see it, that is the pin.

---

**Next:** [Exercise 3: Enroll site servers](03-enroll-hosts.md)
