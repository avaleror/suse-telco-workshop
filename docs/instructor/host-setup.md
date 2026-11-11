# Host setup

Northline is a regional mobile network, a telecom operator (CSP in the industry phrase). One management cluster covers the region. Students use the same labs 01 through 07 on AWS and on bare metal. You only change where the KVM host comes from.

SUSE Telco Cloud 3.7.0 support window, for your planning: full support ends 22 March 2027, maintenance ends 22 March 2028, end of life 23 March 2028.

## Shared facts

| Item | Value |
| --- | --- |
| Plan | `type: suse-telco`, `deployment_target: baremetal` |
| AWS command | `rodeo up --target aws` from this same repo |
| Network | libvirt `default`, bridge `virbr0`, `192.168.122.0/24` |
| VMs | `mgmt`, `site-co`, `site-ran` |
| Hardware | x86_64 with VT-x or AMD-V and nested virt on, 16 threads, 64 GB RAM, 250 GB SSD or NVMe. Details: [Architecture and hardware](../reference/architecture.md#hardware-requirements) |
| Rancher TLS | `source: rancher` (self-signed), NodePort, no public 80/443 |
| Secrets | `~/.rodeo/secrets.yaml` only |

`deployment_target: aws` on the instance raises disk floors and can use NVMe for images. Fleet workshop files, if any, keep `lab.target: baremetal`. Do not set `lab.target: aws`.

`rodeo up --target aws` overrides a committed `deployment_target: baremetal` and writes `aws` back into the local plan. One plan file is enough. The EC2 host is seeded from the copy bundled in rodeo-cli, which is kept identical to this repo's plan.

## AWS

`rodeo up --target aws` rents an EC2 instance and uses it as the KVM host. Libvirt guests on that instance are the lab machines. sushy-tools on the host is their BMC. Metal3 enrolls those guests. The EC2 API is not on that path.

Say this to the room: the EC2 box was rented by rodeo, and every server Metal3 touched was a libvirt guest with a fake Redfish BMC. There is no Cluster API Provider AWS in the Telco Cloud stack.

Tested size: `m8id.4xlarge` in `eu-north-1` ran labs 01 through 07 with both editions, `opensource` on 2026-11-10 and `product` on 2026-11-11. Nested KubeVirt worked on that virtual instance: the lab 07 guest ran with KVM acceleration. No metal size is needed.

Credentials stay in `~/.aws/credentials` or the environment. Do not put them in `rodeo-plan.yaml`.

Tear-down is `rodeo destroy --cloud` or `rodeo fleet deprovision`.

## Bare metal

`deployment_target: baremetal` means the machine under rodeo is the KVM host. No cloud `provider:` block.

This first slice uses the same libvirt guests and the same sushy-tools BMC as AWS. Tell the room that. An operator will picture a rack, and this slice is still a KVM host with emulated BMCs.

Real Redfish hardware is a later track. The student YAML stays the telco-examples shape. The BMC URL changes from sushy-tools to the server's Redfish virtual-media address.

Tear-down is `sudo rodeo clean --yes`.

## Choose the edition

`edition` in `rodeo-plan.yaml` decides what the lab is built from. The Telco Cloud 3.7 pins, the charts, the labs and the student YAML are the same in both.

| | `product` (default) | `opensource` |
| --- | --- | --- |
| Host OS for `mgmt` and the sites | SUSE Linux Micro 6.2 | openSUSE Leap Micro 6.2 |
| Rancher | Rancher Prime 2.15.1 | Rancher 2.15.1, community chart |
| Base image | you stage it once (SUSE login) | rodeo downloads it, checksum pinned |
| SCC registration code | optional | not used |

Neither edition needs an SCC code. The packages added to the downstream image are `jq` and `open-iscsi` (SUSE Storage needs it in lab 07), and Edge Image Builder installs them from a public repository: SLE BCI 16.0 for `product`, openSUSE Leap 16.0 for `opensource`. In `product`, an `scc_registration_code` in `~/.rodeo/secrets.yaml` (and uncommented in the plan) switches that to SUSE's own repositories.

## Stage the SL Micro base image (product only)

The `mgmt` VM and the downstream image both start from `SL-Micro.x86_64-6.2-Base-GM.raw.xz`. It is only on the SUSE Linux Micro download page, behind a SUSE login, so rodeo cannot fetch it for you. Download it once and copy it to the KVM host before `rodeo up`:

```bash
sudo cp SL-Micro.x86_64-6.2-Base-GM.raw.xz /var/lib/libvirt/images/
```

Or set `telco.base_image.url` in `rodeo-plan.yaml` to a location the host can fetch, and `telco.base_image.sha256` to have rodeo check it. The `opensource` edition skips this step.

On `--target aws` the copy does not work: rodeo creates the instance in the same run, and it mounts the instance NVMe over `/var/lib/libvirt/images`. Use `telco.base_image.url` there, with a URL the instance can fetch, for example a presigned link to a private S3 object in the same region. Do not publish the image: it is SUSE content behind a login.

## Install rodeo-cli

`install.sh` on `main` tracks main. Pin the tag this README names:

```bash
curl -fsSL https://raw.githubusercontent.com/avaleror/rodeo-cli/main/install.sh | bash -s -- --ref v0.27.0
```

`v0.27.0` is commit `0da19e3` (11 November 2026), the first release with `type: suse-telco`. Replace that ref only after a full lab run names the commit you actually tested.

```bash
git clone https://github.com/avaleror/suse-telco-workshop.git
cd suse-telco-workshop
rodeo doctor
rodeo up                  # bare metal
# AWS: rodeo up --target aws
```

Because this directory already has `rodeo-plan.yaml`, `rodeo up` is meant to skip `rodeo init`, write `~/.rodeo/secrets.yaml`, and wrap the deploy so a dropped SSH session does not kill it.

`rodeo up` is done when both `BareMetalHost` objects are `available`. Students apply `manifests/` after that. rodeo does not apply that directory.

## After the first boot

Raise the `mgmt` VM if Ironic or Rancher starve. The 8 vCPU and 16 GB figures are a start, not a measured pin. SUSE Storage is not installed on `mgmt`: on a single node, Ironic's volume is an `emptyDir`, the Metal3 chart default. NeuVector stays out.
