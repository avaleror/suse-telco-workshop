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

These are the commands once `type: suse-telco` exists in rodeo-cli:

```bash
curl -fsSL https://raw.githubusercontent.com/avaleror/rodeo-cli/main/install.sh | bash -s -- --ref v0.19.0
git clone https://github.com/avaleror/suse-telco-workshop.git
cd suse-telco-workshop
rodeo doctor
rodeo up                  # bare metal host
# AWS: rodeo up --target aws
```

`install.sh` with no `--ref` tracks `main`. This README names rodeo-cli `v0.19.0` (`cc12c84`, 2 October 2026). The labs have not been run on that tag. `type: suse-telco` is not in that release, so `rodeo up` cannot build this lab today.

When the engine exists, `rodeo up` skips `rodeo init`, writes `~/.rodeo/secrets.yaml`, and stops when the site hosts are `available`. You apply `manifests/` yourself. rodeo does not apply that directory. If a later engine starts applying it, the same clusters would be created twice.

AWS uses this same file: `rodeo up --target aws`. The flag wins over `deployment_target: baremetal`, and rodeo writes `aws` back into your local `rodeo-plan.yaml`. Do not commit that change. On AWS, rodeo seeds the EC2 host from the copy of this plan bundled in rodeo-cli, so the two files are kept identical.

## Local docs preview

```bash
pip install -r requirements.txt
mkdocs serve
```

Open http://127.0.0.1:8000

## Outside this repo

`manifests/` is the student YAML for `site-co-01`, ClusterClass `example-clusterclass`, `site-ran-01`, and VirtualMachine `site-co-guest`. The console repo `avaleror/telco-site-console` is named in the plan and is not created here. The `suse-telco` engine belongs in rodeo-cli, not in this repo.
