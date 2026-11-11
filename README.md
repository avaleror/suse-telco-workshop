# SUSE Telco Cloud 3.7 workshop

Hands-on workshop for Northline, a regional mobile network. One management cluster runs Rancher Prime, Cluster API, and Metal3. You add two downstream clusters, then a site console and one KubeVirt guest.

This repo is private. There is no public Pages URL yet. Preview locally with `mkdocs serve`. The Pages workflow is in the tree, and it does not change who can see the repo.

## Repo structure

```
docs/
  index.md                  # Landing page
  exercises/                # Labs 01 through 07
  reference/                # Overview, language, quick reference, bonus
  instructor/               # Host setup and pre-lab checklist
  lab-guide.md              # Print rollup of labs 01 through 07
manifests/                  # Student YAML. rodeo does not apply this directory
rodeo-plan.yaml             # Deployment plan for rodeo-cli
mkdocs.yml                  # MkDocs Material config
.github/workflows/
  deploy.yml                # Pages workflow, push trigger off while private
```

## Run the workshop

`edition` in `rodeo-plan.yaml` picks the build: `product` (SUSE Linux Micro 6.2, Rancher Prime, the default) or `opensource` (openSUSE Leap Micro 6.2, community Rancher). Neither needs an SCC registration code. See [host setup](docs/instructor/host-setup.md).

Build it with these commands:

```bash
curl -fsSL https://raw.githubusercontent.com/avaleror/rodeo-cli/main/install.sh | bash -s -- --ref v0.27.0
git clone https://github.com/avaleror/suse-telco-workshop.git
cd suse-telco-workshop
rodeo doctor
rodeo up                  # bare metal host
# AWS: rodeo up --target aws
```

`install.sh` with no `--ref` tracks `main`. This README names rodeo-cli `v0.27.0` (`0da19e3`, 11 November 2026), the first release with `type: suse-telco`. Its engine code is the commit that built this lab on AWS and passed labs 04 through 07 unattended, with both editions.

`rodeo up` skips `rodeo init`, writes `~/.rodeo/secrets.yaml`, and stops when the site hosts are `available`. You apply `manifests/` yourself. rodeo does not apply that directory. If a later rodeo version starts applying it, the same clusters would be created twice.

AWS uses this same file: `rodeo up --target aws`. The flag wins over `deployment_target: baremetal`, and rodeo writes `aws` back into your local `rodeo-plan.yaml`. Do not commit that change. On AWS, rodeo copies this directory's plan files to the EC2 host, so the host builds from your `rodeo-plan.yaml`.

## Local docs preview

```bash
pip install -r requirements.txt
mkdocs serve
```

Open http://127.0.0.1:8000

## Outside this repo

`manifests/` is the student YAML for `site-co-01`, ClusterClass `example-clusterclass`, `site-ran-01`, and VirtualMachine `site-co-guest`. The console repo `avaleror/telco-site-console` is named in the plan and is not created here. The `suse-telco` engine belongs in rodeo-cli, not in this repo.
