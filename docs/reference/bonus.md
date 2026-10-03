# Bonus, not numbered

No student steps here. These are pointers for after labs 01 through 07.

The numbered labs stay on Cluster API plus Metal3, one management cluster, emulated BMCs, two single-node downstream clusters, the site console, and one KubeVirt guest.

[telco-examples](https://github.com/suse-edge/telco-cloud-examples/tree/release-3.7/telco-examples) on `release-3.7` (`e4ec0b0`) is the place to read:

- A real Redfish virtual-media URL, in place of sushy-tools
- The `dhcp-less` nmstate tree
- SR-IOV
- CPU Manager
- The air-gap management ISO

Two other 3.7 onboarding paths exist besides the one this workshop teaches. One is an Edge Image Builder standalone cluster, then an import into Rancher. The numbered labs do not build that path. They use Cluster API plus Metal3, then the Turtles auto-import label.

Later host targets, after AWS and bare metal both finish a full lab run, are `instruqt`, `gcp`, `vultr`, and `hetzner`. They are names only.
