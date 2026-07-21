# Provisioning Compute Resources


CD into incus-incant directory:

```bash
cd kubernetes-the-hard-way/incus-incant
```

The `incant.yaml` is configured to assume you have at least an 8 core CPU
which most modern core i5, i7 and i9 do, and at least 16GB RAM. You can
tune these values especially if you have *less* than this by editing the
`incant.yaml` before the next step below and adjusting the values for
`RAM_SIZE` and `CPU_CORES` accordingly. It is not recommended to change
these unless you know what you are doing as it may result in crashes
and will make the lab harder to support.

This will not work if you have less than 8GB of RAM.

Run incant up:

```bash
incant up
```


This does the below:

- Deploys 5 VMs - 2 controlplane, 2 worker and 1 loadbalancer

- Set's IP addresses in the range `192.168.56.x`

    | VM            | Purpose       | IP            | RAM  |
    | ------------  |:-------------:| -------------:|-----:|
    | controlplane01      | Master        | 192.168.56.11 | 2048 |
    | controlplane02      | Master        | 192.168.56.12 | 1024 |
    | node01      | Worker        | 192.168.56.21 | 512  |
    | node02      | Worker        | 192.168.56.22 | 1024 |
    | loadbalancer  | LoadBalancer  | 192.168.56.30 | 1024 |

    > These are the default settings.
    > These can be changed in the `incant.yaml` file

- Sets required kernel settings for kubernetes networking to function correctly.
Through the `setup-kernel.sh` script.

## Access to the nodes

There are two ways to get shell access to the nodes:

### 1. Shell by incus

Run `incus shell \<vm\>` for example `incus shell controlplane01`.
Use this for updating `/root/.ssh/authorized_keys` in the node.

### 2. SSH Using SSH Client Tools

Use your favourite SSH terminal tool (`ssh` from OpenSSH).

The incant provisioning step `ssh: true` installs SSH server
and puts `~/.ssh/id_*.pub` of executing user, being you,
in `/root/.ssh/authorized_keys` at the nodes.

So you can do
```text
ssh root@<node>
```

## Verify Environment

- Ensure all VMs are up.
- Ensure VMs are assigned the above IP addresses.
- Ensure you can SSH into these VMs using the IP and private keys, or `incus shell`.
- Ensure the VMs can ping each other.

## Troubleshooting Tips

### Failed Provisioning

If any of the VMs failed to provision, or is not configured correct, delete the VM using the command:

```bash
incant destroy \<vm\>
```

Then re-provision.

```bash
incant up \<vm\>
```

Next: [Client tools](../../docs/03-client-tools.md)<br>
Prev: [Prerequisites](01-prerequisites.md)
