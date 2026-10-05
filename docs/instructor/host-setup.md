# Host setup

Northline is a regional mobile network, a telecom operator (CSP in the industry phrase). One management cluster covers the region. Students use the same labs 01 through 07 on AWS and on bare metal. You only change where the KVM host comes from.

The `suse-telco` engine is not in rodeo-cli `v0.19.0`. Do not expect `rodeo up` to build this lab on that tag. The steps below are the setup once that engine is the one you are running.

SUSE Telco Cloud 3.7.0 support window, for your planning: full support ends 22 March 2027, maintenance ends 22 March 2028, end of life 23 March 2028.

## Shared facts

| Item | Value |
| --- | --- |
| Plan | `type: suse-telco`, `deployment_target: baremetal` |
| AWS command | `rodeo up --target aws` from this same repo |
| Network | libvirt `default`, bridge `virbr0`, `192.168.122.0/24` |
| VMs | `mgmt`, `site-co`, `site-ran` |
| Host bar to start from | 64 GB RAM, 16 vCPU, 250 GB free under the image directory |
| Rancher TLS | `source: rancher` (self-signed), NodePort, no public 80/443 |
| Secrets | `~/.rodeo/secrets.yaml` only |

Correct the host bar after the first real run. The 32 GB metal3-demo host is a smaller stack. The edge workshop's 40 GB host is a different engine.

`deployment_target: aws` on the instance raises disk floors and can use NVMe for images. Fleet workshop files, if any, keep `lab.target: baremetal`. Do not set `lab.target: aws`.

`rodeo up --target aws` overrides a committed `deployment_target: baremetal` and writes `aws` back into the local plan. One plan file is enough. The EC2 host is seeded from the copy bundled in rodeo-cli, which is kept identical to this repo's plan.

## AWS

`rodeo up --target aws` rents an EC2 instance and uses it as the KVM host. Libvirt guests on that instance are the lab machines. sushy-tools on the host is their BMC. Metal3 enrolls those guests. The EC2 API is not on that path.

Say this to the room: the EC2 box was rented by rodeo, and every server Metal3 touched was a libvirt guest with a fake Redfish BMC. There is no Cluster API Provider AWS in the Telco Cloud stack.

Suggested sizes to try, not tested pins: `m8id.4xlarge` for labs 01 through 06. When lab 07 has to pass, use an EC2 metal size already in the rodeo catalog. `m7i.metal-24xl` is the pattern. Nested KubeVirt on a virtual EC2 instance is the risk. Do not publish either size as tested before that run.

Credentials stay in `~/.aws/credentials` or the environment. Do not put them in `rodeo-plan.yaml`.

Tear-down is `rodeo destroy --cloud` or `rodeo fleet deprovision`.

## Bare metal

`deployment_target: baremetal` means the machine under rodeo is the KVM host. No cloud `provider:` block.

This first slice uses the same libvirt guests and the same sushy-tools BMC as AWS. Tell the room that. An operator will picture a rack, and this slice is still a KVM host with emulated BMCs.

Real Redfish hardware is a later track. The student YAML stays the telco-examples shape. The BMC URL changes from sushy-tools to the server's Redfish virtual-media address.

Tear-down is `sudo rodeo clean --yes`.

## Install rodeo-cli

`install.sh` on `main` tracks main. Pin the tag this README names:

```bash
curl -fsSL https://raw.githubusercontent.com/avaleror/rodeo-cli/main/install.sh | bash -s -- --ref v0.19.0
```

`v0.19.0` is commit `cc12c84` (2 October 2026). Replace that ref only after a full lab run names the commit you actually tested.

```bash
git clone https://github.com/avaleror/suse-telco-workshop.git
cd suse-telco-workshop
rodeo doctor
rodeo up                  # bare metal
# AWS: rodeo up --target aws
```

Because this directory already has `rodeo-plan.yaml`, `rodeo up` is meant to skip `rodeo init`, write `~/.rodeo/secrets.yaml`, and wrap the deploy so a dropped SSH session does not kill it.

The SCC registration code comes from `??scc_registration_code`. SUSE Linux Micro builds need it. Do not switch the student image to Leap Micro to avoid the code.

`rodeo up` is done when both `BareMetalHost` objects are `available`. Students apply `manifests/` after that. rodeo does not apply that directory.

## After the first boot

Raise the `mgmt` VM if Ironic or Rancher starve. The 8 vCPU and 16 GB figures are a start, not a measured pin. SUSE Storage stays if Ironic needs the volume. NeuVector stays out.
