# SUSE Telco Cloud 3.7 workshop

Hands-on workshop for Northline, a regional mobile network. One management cluster runs Rancher Prime, Cluster API, and Metal3. You add two downstream clusters, a three-node central office with SUSE Storage and a one-node far-edge site, then a site console and one KubeVirt guest.

The lab guide is published at https://avaleror.github.io/suse-telco-workshop/. Every push to `main` republishes it. Preview locally with `mkdocs serve`.

## Repo structure

```
docs/
  index.md                  # Landing page
  exercises/                # Labs 01 through 07
  reference/                # Architecture and hardware, overview, language, quick reference, bonus
  instructor/               # Host setup and pre-lab checklist
  lab-guide.md              # Print rollup of labs 01 through 07
manifests/                  # Student YAML. rodeo does not apply this directory
rodeo-plan.yaml             # Deployment plan for rodeo-cli
definition.yaml             # The five lab VMs. rodeo reads it instead of its built-in topology
mkdocs.yml                  # MkDocs Material config
.github/workflows/
  deploy.yml                # Publishes the docs to GitHub Pages on every push to main
```

## Run the workshop

`edition` in `rodeo-plan.yaml` picks the build: `product` (SUSE Linux Micro 6.2, Rancher Prime, the default) or `opensource` (openSUSE Leap Micro 6.2, community Rancher). Neither needs an SCC registration code. See [host setup](docs/instructor/host-setup.md).

Build it with these commands:

```bash
curl -fsSL https://raw.githubusercontent.com/avaleror/rodeo-cli/main/install.sh | bash -s -- --ref v0.28.1
git clone https://github.com/avaleror/suse-telco-workshop.git
cd suse-telco-workshop
rodeo doctor
rodeo up                  # bare metal host
# AWS: rodeo up --target aws
```

`install.sh` with no `--ref` tracks `main`. This README names rodeo-cli `v0.28.1` (`9f616b6`, 11 November 2026). Its engine code is the same as `v0.27.0` (`0da19e3`), the first release with `type: suse-telco`, which built this lab on AWS and passed labs 04 through 07 unattended, with both editions. `v0.28.0` made the five-VM layout, `site-co-01` on three hosts, the built-in default. That layout passed on 2026-11-11. `v0.28.1` fixes a crash when a plan has an empty `telco:` block.

`rodeo up` skips `rodeo init`, writes `~/.rodeo/secrets.yaml`, and stops when the site hosts are `available`. You apply `manifests/` yourself. rodeo does not apply that directory. If a later rodeo version starts applying it, the same clusters would be created twice.

AWS uses this same file: `rodeo up --target aws`. The flag wins over `deployment_target: baremetal`, and rodeo writes `aws` back into your local `rodeo-plan.yaml`. Do not commit that change. On AWS, rodeo copies this directory's plan files to the EC2 host, so the host builds from your `rodeo-plan.yaml`.

## Local docs preview

```bash
pip install -r requirements.txt
mkdocs serve
```

Open http://127.0.0.1:8000

## Outside this repo

`manifests/` is the student YAML for `site-co-01`, ClusterClass `example-clusterclass`, `site-ran-01`, the site console GitRepo, and VirtualMachine `site-co-guest`. The console itself lives in its own repo, `avaleror/telco-site-console`, named in the plan. rodeo copies it into Gitea on `mgmt`, and Fleet deploys it from there. The `suse-telco` engine belongs in rodeo-cli, not in this repo.
