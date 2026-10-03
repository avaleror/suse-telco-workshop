# Pre-lab checklist

Run this after `rodeo up` finishes and before students open lab 01. The `suse-telco` engine is not in the rodeo-cli tag named in the README, so this list will not pass on `v0.19.0`.

## Secrets

```bash
test -f ~/.rodeo/secrets.yaml && echo secrets file present
# Passwords resolve from ?? keys. Nothing secret belongs in rodeo-plan.yaml.
grep -E 'rancher_admin_password|scc_registration_code' ~/.rodeo/secrets.yaml >/dev/null
```

Confirm the SCC registration code is set. SUSE Linux Micro 6.2 builds ask for it. AWS credentials, if you used `--target aws`, stay in `~/.aws/credentials` or the environment.

## Image cache

On `mgmt`, the 3.7 image build output is already cached. Students must not be waiting on Edge Image Builder or Kiwi. The tags are in `rodeo-plan.yaml`: `edge-image-builder:1.3.4` and `kiwi-builder:10.2.29.1`.

## Controller rollout

On the management cluster, these are ready: Cluster API, CAPM3, IPAM CRDs, RKE2 bootstrap, RKE2 control plane, cert-manager, baremetal-operator, Ironic, and Turtles.

```bash
virsh domifaddr mgmt
ssh root@MGMT_IP "kubectl get pods -A"
ssh root@MGMT_IP "kubectl get nodes"
```

Rancher Prime should be 2.15.1. The node OS is SUSE Linux Micro 6.2, RKE2 `v1.36.3+rke2r1`.

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

Students start at lab 01. Manifests in this repo are still stubs, so labs 04, 05, and 07 stop at the warning until the tested YAML replaces the comments. Say that at the start of class if you are teaching from this commit.
