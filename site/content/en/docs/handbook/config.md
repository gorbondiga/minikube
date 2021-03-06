---
title: "Configuration"
weight: 4
description: >
  Configuring your cluster 
---

## Basic Configuration

Most minikube configuration is done via the flags interface. To see which flags are possible for the start command, run:

```shell
minikube start --help
```

## Persistent Configuration

minikube allows users to persistently store new default values to be used across all profiles, using the `minikube config` command. This is done providing a property name, and a property value.

### Listing config properties

```shell
minikube config
```

For example, to persistently configure minikube to use hyperkit:

```shell
minikube config set driver hyperkit
```

## Kubernetes configuration

minikube allows users to configure the Kubernetes components with arbitrary values. To use this feature, you can use the `--extra-config` flag on the `minikube start` command.

This flag is repeated, so you can pass it several times with several different values to set multiple options.

### Selecting a Kubernetes version

By default, minikube installs the latest stable version of Kubernetes that was available at the time of the minikube release. You may select a different Kubernetes release by using the `--kubernetes-version` flag, for example:

`minikube start --kubernetes-version=v1.11.10`
  
If you omit this flag, minikube will upgrade your cluster to the default version. If you would like to pin to a specific Kubernetes version across clusters, restarts, and upgrades to minikube, use:

`minikube config set kubernetes-version v1.11.0`

minikube follows the [Kubernetes Version and Version Skew Support Policy](https://kubernetes.io/docs/setup/version-skew-policy/), so we guarantee support for the latest build for the last 3 minor Kubernetes releases. When practical, minikube aims for the last 6 minor releases so that users can emulate legacy environments.

As of September 2019, this means that minikube supports and actively tests against the latest builds of:

* v1.16 (default)
* v1.15
* v1.14
* v1.13
* v1.12
* v1.11 (best effort)

For more up to date information, see `OldestKubernetesVersion` and `NewestKubernetesVersion` in [constants.go](https://github.com/kubernetes/minikube/blob/master/pkg/minikube/constants/constants.go)

### Enabling feature gates

Kubernetes alpha/experimental features can be enabled or disabled by the `--feature-gates` flag on the `minikube start` command. It takes a string of the form `key=value` where key is the `component` name and value is the `status` of it.

```shell
minikube start --feature-gates=EphemeralContainers=true
```

### Modifying Kubernetes defaults

The kubeadm bootstrapper can be configured by the `--extra-config` flag on the `minikube start` command.  It takes a string of the form `component.key=value` where `component` is one of the strings

* kubeadm
* kubelet
* apiserver
* controller-manager
* scheduler

and `key=value` is a flag=value pair for the component being configured.  For example,

```shell
minikube start --extra-config=apiserver.v=10 --extra-config=kubelet.max-pods=100
```

For instance, to allow Kubernetes to launch on an unsupported Docker release:

```shell
minikube start --extra-config=kubeadm.ignore-preflight-errors=SystemVerification
```

## Runtime configuration

The default container runtime in minikube is Docker. You can select it explicitly by using:

```shell
minikube start --container-runtime=docker
```

You can also select:

* *[containerd](https://github.com/containerd/containerd):
* `cri-o`: [CRI-O](https://github.com/kubernetes-sigs/cri-o):

## Environment variables

minikube supports passing environment variables instead of flags for every value listed in `minikube config`.  This is done by passing an environment variable with the prefix `MINIKUBE_`.

For example the `minikube start --iso-url="$ISO_URL"` flag can also be set by setting the `MINIKUBE_ISO_URL="$ISO_URL"` environment variable.

### Exclusive environment tunings

Some features can only be accessed by minikube specific environment variables, here is a list of these features:

* **MINIKUBE_HOME** - (string) sets the path for the .minikube directory that minikube uses for state/configuration. *Please note: this is used only by minikube and does not affect anything related to Kubernetes tools such as kubectl.*

* **MINIKUBE_IN_STYLE** - (bool) manually sets whether or not emoji and colors should appear in minikube. Set to false or 0 to disable this feature, true or 1 to force it to be turned on.

* **MINIKUBE_WANTUPDATENOTIFICATION** - (bool) sets whether the user wants an update notification for new minikube versions

* **MINIKUBE_REMINDERWAITPERIODINHOURS** - (int) sets the number of hours to check for an update notification

* **CHANGE_MINIKUBE_NONE_USER** - (bool) automatically change ownership of ~/.minikube to the value of $SUDO_USER

* **MINIKUBE_ENABLE_PROFILING** - (int, `1` enables it) enables trace profiling to be generated for minikube

### Example: Disabling emoji

```shell
export MINIKUBE_IN_STYLE=false
minikube start
```

### Making environment values persistent

To make the exported variables persistent across reboots:

* Linux and macOS: Add these declarations to `~/.bashrc` or wherever your shells environment variables are stored.
* Windows: Add these declarations via [system settings](https://support.microsoft.com/en-au/help/310519/how-to-manage-environment-variables-in-windows-xp) or using [setx](https://stackoverflow.com/questions/5898131/set-a-persistent-environment-variable-from-cmd-exe)
