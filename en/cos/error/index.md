---
title: Troubleshooting {{ coi }} issues in {{ cos-full-name }}
description: This guide describes how you can fix issues with a {{ coi }}.
---

# Troubleshooting

To view Docker image startup logs, use the command:

```bash
sudo journalctl -eu yc-container-daemon
```

See below for a list of common issues and their fixes:

## Service account has no permission to download the specified Docker image {#permission-denied}

**Example**:

```text
Mar 25 12:07:39 instance-name yc-container-daemon[516]:
{"level":"DEBUG","ts":"2021-03-25T12:07:39.785Z","caller":"container/image.go:75","msg":"trying to pull image (0/3)"}
Mar 25 12:07:39 instance-name yc-container-daemon[516]:
{"level":"DEBUG","ts":"2021-03-25T12:07:39.786Z","caller":"container/image.go:47","msg":"pulling image: '{{ registry }}/crpgrueprn********/nginx:1.16.0'"}
Mar 25 12:07:41 instance-name yc-container-daemon[516]:
{"level":"ERROR","ts":"2021-03-25T12:07:41.005Z","caller":"container/image.go:78","msg":"error pulling image: Error response from daemon: pull access denied for {{ registry }}/crpgruern********/ngin>
```

**How to fix it**: [Assign to the service account](../../iam/operations/sa/set-access-bindings.md) the `viewer` or `container-registry.images.puller` role for a repository, registry, or folder. For more information about the roles available in the service, see our [documentation](../../container-registry/security/index.md).

## No network access to {{ container-registry-name }} {#connection-to-cr}

**Example**:

```text
Sep 28 08:00:18 instance-name yc-container-daemon[952]:
{"level":"DEBUG","ts":"2019-09-28T08:00:18.842Z ","caller":"container/container.go:121","msg":"trying to pull image (0/3)"}
Sep 28 08:00:18 instance-name yc-container-daemon[952]:
{"level":"DEBUG","ts":"2019-09-28T08:00:18.842Z","caller":"container/container.go:162","msg":"pulling image: '{{ registry }}/crpgrueprnhc********/nginx:1.16.0'"}
Sep 28 08:00:33 instance-name yc-container-daemon[952]:
{"level":"ERROR","ts":"2019-09-28T08:00:33.843Z","caller":"container/container.go:124","msg":"error pulling image: Error response from daemon: Get https://{{ registry }}/v2/: net/http: request canceled while waiting for connection (Client.Timeout exceeded while awaiting headers)"}
```

**How to fix it**: Check if there is access to {{ container-registry-name }} by running this command: `nc -vz {{ registry }} 443`. If there is no access, [configure a NAT instance](../../tutorials/routing/nat-instance/index.md) or assign a public IP address to the VMs with {{ coi }}. You can also [set up a NAT gateway](../../vpc/operations/create-nat-gateway.md) for the subnet you are creating the VMs in.

## No service account is linked to the VM to enable access to {{ container-registry-name }} {#sa-for-registry}

**Example**:

```text
Mar 25 12:13:23 instance-name yc-container-daemon[518]:
{"level":"WARN","ts":"2021-03-25T12:13:23.466Z","caller":"container/container.go:240","msg":"Attempting to pull Container Registry image with empty credentials. It will only work if public registry>
Mar 25 12:13:23 instance-name yc-container-daemon[518]:
{"level":"DEBUG","ts":"2021-03-25T12:13:23.466Z","caller":"container/image.go:75","msg":"trying to pull image (0/3)"}
Mar 25 12:13:23 instance-name yc-container-daemon[518]:
{"level":"DEBUG","ts":"2021-03-25T12:13:23.467Z","caller":"container/image.go:47","msg":"pulling image: '{{ registry }}/crpgruehrnhc********/nginx:1.16.0'"}
Mar 25 12:13:24 instance-name yc-container-daemon[518]:
{"level":"ERROR","ts":"2021-03-25T12:13:24.706Z","caller":"container/image.go:78","msg":"error pulling image: Error response from daemon: unauthorized: Authentication problem ; requestId = b2f6f07>
```

**How to fix it**: For private registries, [link a service account](../../compute/operations/vm-connect/auth-inside-vm.md#link-sa-with-instance) to access Docker images.

## Not enough disk space {#disk-full}

**Example**:

```text
Mar 25 12:34:22 instance-name yc-container-daemon[518]:
{"level":"DEBUG","ts":"2021-03-25T12:34:22.043Z","caller":"container/image.go:75","msg":"trying to pull image (0/3)"}
Mar 25 12:34:22 instance-name yc-container-daemon[518]:
{"level":"DEBUG","ts":"2021-03-25T12:34:22.043Z","caller":"container/image.go:47","msg":"pulling image: 'openjdk:7' (normalized: 'docker.io/library/openjdk:7')"}
Mar 25 12:34:46 instance-name yc-container-daemon[518]:
{"level":"DEBUG","ts":"2021-03-25T12:34:46.276Z","caller":"container/image.go:59","msg":"received ImagePull response: ... {\"message\":\"failed to register layer: Error processing tar file(exit status 1): write /usr/bin/hostnamectl: no space left on device\"},\"error\":\"failed to register layer: Error processing tar file(exit status 1): write /usr/bin/hostnamectl: no space left on device\"}\r\n)."}
```

**How to fix it**: Stop the VM and [increase the disk size](../../compute/operations/disk-control/update.md#change-disk-size).


## Disk partition not specified {#disk-partition}

**Example**:

```text
Oct 23 19:43:36 instance-name yc-container-daemon[781]: {"level":"ERROR","ts":"2024-10-23T19:43:36.478Z","caller":"mdtracking/checker.go:135","msg":"OnChange callback failed: received multiline output from lsblk, the device likely contains subpartitions:\nNAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS\nloop0    7:0    0  63.3M  1 loop /snap/core20/1822\nloop1    7:1    0    64M  1 loop /snap/core20/2379\nloop2    7:2    0 111.9M  1 loop /snap/lxd/24322\nloop3    7:3    0    87M  1 loop /snap/lxd/29351\nloop4    7:4    0  49.8M  1 loop /snap/snapd/18357\nloop5    7:5    0  38.8M  1 loop /snap/snapd/21759\nvda    252:0    0    30G  0 disk \n├─vda1 252:1    0     1M  0 part \n└─vda2 252:2    0    30G  0 part /\nvdb    252:16   0    20G  0 disk \n├─vdb1 252:17   0     1M  0 part \n└─vdb2 252:18   0    15G  0 part \n"}
```

**How to fix it**: Determine which partition number is needed for mounting using the `lsblk` command, add the `partition: X` parameter with the partition number to the [Docker Compose specification](../concepts/coi-specifications.md#compose-spec), [update the VM](../tutorials/vm-update.md) using the edited specification.

## Disk not mounting {#disk-mount}

**Example**:

```text
Sep 29 08:10:10 instance-name yc-container-daemon[743]: {"level":"ERROR","ts":"2024-09-29T08:10:10.133Z","caller":"mdtracking/checker.go:135","msg":"OnChange callback failed: device /dev/disk/by-id/virtio-coi-data access error: stat /dev/disk/by-id/virtio-coi-data: no such file or directory"}
```

**How to fix it**: Specify the `device-name` parameter in flags and CLI commands for adding disks to the VM, equal to the `device_name` value in the [Docker Compose specification](../concepts/coi-specifications.md#compose-spec-example). In the management console, it is specified in the pop-up window when [attaching a disk to a VM](../../compute/operations/vm-control/vm-attach-disk#attach). In the log example above, the `device-name` will be `coi-data`.


## The requested image's platform does not match the host platform {#platforms-not-match}

**Example**:

```text
WARNING: The requested image's platform (linux/arm64/v8) does not match the detected host platform (linux/amd64/v4) and no specific platform was requested
```

**How to fix it**: Use a Docker image compatible with a platform that fits your VM's OS and architecture.

One Docker image can support [multiple platforms](https://docs.docker.com/build/building/multi-platform/). When you run such an image, Docker will automatically select an option that fits in with your host's OS and architecture. If the image contains no suitable option, it will fail to run with an error message.