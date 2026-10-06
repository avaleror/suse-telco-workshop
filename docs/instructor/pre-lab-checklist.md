# Pre-lab checklist

Run this after `rodeo up` finishes and before students open lab 01. The `suse-telco` engine is not in the rodeo-cli tag named in the README, so this list will not pass on `v0.19.0`.

## Secrets

```bash
test -f ~/.rodeo/secrets.yaml && echo secrets file present
# Passwords resolve from ?? keys. Nothing secret belongs in rodeo-plan.yaml.
grep -E 'rancher_admin_password|bmc_password|mgmt_root_password' ~/.rodeo/secrets.yaml >/dev/null
```

No SCC registration code is needed. AWS credentials, if you used `--target aws`, stay in `~/.aws/credentials` or the environment.

## Image cache

On `mgmt`, the downstream image is already in the cache. Students must not be waiting on Edge Image Builder (`edge-image-builder:1.3.4`, from `rodeo-plan.yaml`).

```bash
ssh root@MGMT_IP "ls -lh /opt/media && curl -sfI http://imagecache.local:8080/eibimage-downstream-cluster.raw"
```

Both `eibimage-downstream-cluster.raw` and `eibimage-downstream-cluster.raw.sha256` are there, and the `curl` returns `200`.

## Controller rollout

On the management cluster, these are ready: Cluster API, CAPM3, IPAM CRDs, RKE2 bootstrap, RKE2 control plane, cert-manager, baremetal-operator, Ironic, and Turtles.

```bash
virsh domifaddr mgmt
ssh root@MGMT_IP "kubectl get pods -A"
ssh root@MGMT_IP "kubectl get nodes"
```

Rancher Prime (product) or Rancher (opensource) should be 2.15.1. The node OS is SUSE Linux Micro 6.2 (product) or openSUSE Leap Micro 6.2 (opensource), RKE2 `v1.36.3+rke2r1`.

## BareMetalHost available

```bash
ssh root@MGMT_IP "kubectl get baremetalhosts -A"
```

Two hosts, both `available`, BMC method `redfish-virtualmedia`, sushy-tools addresses. One is `site-co`, one is `site-ran`.

## Nested virt on site-co

Lab 07 starts a KubeVirt guest inside `site-co`. That guest needs nested virt. `site-ran` does not.

```bash
virsh dumpxml site-co | grep -i nested
```

## Hand-off

Students start at lab 01. Labs 04, 05, and 07 apply the YAML in `manifests/`: `site-co-01`, ClusterClass `example-clusterclass`, `site-ran-01`, and VirtualMachine `site-co-guest`.
