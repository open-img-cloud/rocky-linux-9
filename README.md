# Rocky Linux 9 cloud images

Cloud-init-ready, signed Rocky Linux 9 qcow2 images for OpenStack and Proxmox,
part of the [openimages.cloud](https://openimages.cloud) catalog.

- Catalog page: <https://openimages.cloud/images/rocky-linux-9/>
- Registry: <https://images.openimages.cloud/rocky-linux-9/latest/>

## About

Rocky Linux 9 is a community enterprise Linux, binary-compatible with RHEL 9, published by the Rocky Enterprise Software Foundation
([https://rockylinux.org/](https://rockylinux.org/)). This repo takes the official upstream
GenericCloud image ([listing](https://dl.rockylinux.org/pub/rocky/9/images/x86_64/)), verifies it against the
upstream checksum list, applies the openimages.cloud cloud-init policy,
boot-tests it, signs it with cosign (keyless) and publishes it.

The build pipeline is shared with the rest of
[`open-img-cloud`](https://github.com/open-img-cloud): this repo only ships
`VERSION`, `build/customize.sh`, `build/detect-upstream.sh` and two thin
caller workflows that delegate to the reusable workflows in
[`open-img-cloud/.github`](https://github.com/open-img-cloud/.github).

## Image

- File: `rocky-linux-9-<version>-x86_64.qcow2`, where `<version>` is the upstream
  image version (e.g. `9.8-20260525.0`).
- Default user: `rocky` (SSH key from cloud-init, passwordless sudo).
- Virtual disk: 10 GB; any flavor with a 10 GB+ root disk works.

## Use it

```sh
V=9.8-20260525.0
curl -fLO https://images.openimages.cloud/rocky-linux-9/$V/rocky-linux-9-$V-x86_64.qcow2

openstack image create \
    --disk-format qcow2 --container-format bare \
    --property hw_qemu_guest_agent=yes \
    --file rocky-linux-9-$V-x86_64.qcow2 \
    'Rocky Linux 9 '$V
```

Verification (cosign), Proxmox commands and provenance details are on the
[catalog page](https://openimages.cloud/images/rocky-linux-9/) and in the
[verification guide](https://openimages.cloud/verify/).

## Release flow

1. `watch.yml` runs daily (06:05 UTC) and opens an
   `auto/upstream-bump` pull request when upstream publishes a new image.
2. Merge it, then push the tag `v<VERSION>`: `release.yml` builds, boot-tests,
   signs and publishes the image.

## License

The build tooling in this repository is [GPL-2.0](LICENSE). The image keeps
the licensing terms of Rocky Linux 9.
