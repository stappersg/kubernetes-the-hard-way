# Kubernetes The Hard Way on incus

Begin here if your machine is Windows or Intel Mac. For these machines,
we use incus as the hypervisor, and incant to provision the
Virtual Machines.

This should also work with Linux (as the host operating system, not
running in a VM), but it not so far tested.

## Prerequisites


### Hardware Requirements

This lab provisions 5 VMs on your workstation. That's a lot of compute resource!

- 16GB RAM. It may work with less, but will be slow and may crash unexpectedly.
- 8 core or better CPU e.g. Intel Core-i7/Core-i9, AMD Ryzen-7/Ryzen-9. May
  work with fewer, but will be slow and may crash unexpectedly.
- 50 GB disk space

### incus

Download and install [incus](https://linuxcontainers.org/incus/)

```bash
sudo apt install --yes incus
```

### incant

Once `incus` is installed you may chose to deploy virtual machines
manually on it.
`incant` provides an easier way to deploy multiple virtual machines on
incus more consistently.

Download and install [incant](https://github.com/lnussbaum/incant)

```bash
sudo apt install --yes incant
```

This tutorial assumes that you have also installed incant.


### Lab Defaults

The labs have been configured with the following networking defaults. It
is not recommended to change these. If you change any of these after
you have deployed any of the lab, you'll need to completely reset it
and start again from the beginning:

```bash
incant destroy
incant up
```

If you do change any of these, **please consider that a personal preference and don't submit a PR for it**.

#### Virtual Machine Network

The network used by the incus virtual machines is `192.168.56.0/24`.

To change this, edit the [incant.yaml](../../incus-incant/incant.yaml) in your
cloned copy (do not edit directly in github), and set the new value for
the network prefix. This should not overlap any of the other
network settings.

For changing an incus network address block,
you start with `incus network list`.
Lets say in the output is a network with name `incusbr-1000`.
So we can zoom in on that network
with `incus network list-allocations | grep incusbr-1000`.
There should be `192.168.56.1/24` shown. If not, edit by
```shell
sudo incus network edit incusbr-1000
```
Check the result by
another `incus network list-allocations | grep incusbr-1000`
The goal we aiming for looks like:
```
$ incus network list-allocations
+-----------------------------+------------------+----------+-----+-------------+
|            USED BY          |      ADDRESS     |   TYPE   | NAT | MAC ADDRESS |
+-----------------------------+------------------+----------+-----+-------------+
~~~
| /1.0/networks/incusbr-1000  | 192.168.56.1/24  | network  | YES |             |
+-----------------------------+------------------+----------+-----+-------------+
~~~
$
```

Note that you do not need to edit any of the other scripts to make the
above change. It is all managed by shell variable computations based
on the assigned VM  IP  addresses and the values in the hosts file
(also computed).

It is *recommended* that you leave the pod and service networks with the
following defaults. If you change them then you will also need to edit
one or both of the CoreDNS and Weave networking manifests to accommodate
your change.

#### Pod Network

The network used to assign IP addresses to pods is `10.244.0.0/16`.

To change this, open all the `.md` files in the [docs](../../docs/)
directory in your favourite IDE and do a global replace on<br>
`POD_CIDR=10.244.0.0/16`<br>
with the new CDIR range.  This should not overlap any of the other
network settings.

#### Service Network

The network used to assign IP addresses to Cluster IP services is
`10.96.0.0/16`.

To change this, open all the `.md` files in the [docs](../../docs/)
directory in your favourite IDE and do a global replace on<br>
`SERVICE_CIDR=10.96.0.0/16`<br>
with the new CDIR range.  This should not overlap any of the other
network settings.

Additionally edit line 164 of
[coredns.yaml](../../deployments/coredns.yaml) to set the new DNS service
address (should still end with `.10`)

## Running Commands in Parallel with tmux

[tmux](https://github.com/tmux/tmux/wiki) can be used to run commands
on multiple compute instances at the same time. Labs in this tutorial
may require running the same commands across multiple compute instances,
in those cases consider using tmux and splitting a window into multiple
panes with synchronize-panes enabled to speed up the provisioning process.

> The use of tmux is optional and not required to complete this tutorial.

![tmux screenshot](../../images/tmux-screenshot.png)

> Enable synchronize-panes by pressing `CTRL+B` followed by `"` to
> split the window into two panes. In each pane (selectable with mouse),
> ssh to the host(s) you will be working with.</br>Next type `CTRL+X`
> at the prompt to begin sync. In sync mode, the dividing line between
> panes will be red. Everything you type or paste in one pane will
> be echoed in the other.<br>To disable synchronization type `CTRL+X`
> again.</br></br>Note that the `CTRL-X` key binding is provided by a
> `.tmux.conf` loaded onto the VM by the incant provisioner.

Next: [Compute Resources](02-compute-resources.md)
