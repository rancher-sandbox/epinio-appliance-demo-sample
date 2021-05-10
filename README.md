# EpinioOS demo appliance

This is a sample repository to demonstrate how to create a [Epinio](https://github.com/epinio/epinio) appliance with [cos-toolkit](https://github.com/rancher-sandbox/cOS-toolkit).

<!-- TOC -->

- [EpinioOS demo appliance](#epinioos-demo-appliance)
    - [Ready to fork](#ready-to-fork)
        - [Credentials](#credentials)
        - [Images](#images)
    - [Main difference with cos-toolkit-sample-repo](#main-difference-with-cos-toolkit-sample-repo)
    - [Build locally](#build-locally)
    - [First boot](#first-boot)
        - [Upgrades](#upgrades)

<!-- /TOC -->

## Ready to fork

This project is ready to fork! By forking it, you have already all the pieces to build and distribute a custom Epinio appliance.

The Epinio appliance is built entirely on Github Actions. 

You can test locally the appliance by grabbing an ISO from the [github page](https://github.com/rancher-sandbox/epinio-appliance-demo-sample/actions/workflows/build.yaml?query=event%3Aschedule+is%3Asuccess). Final images are pushed over [Dockerhub](https://hub.docker.com/repository/docker/raccos/epinio-appliance-demo-test) and the cache images can be found [here](https://hub.docker.com/repository/docker/raccos/epinio-appliance-cache-demo-test).


### Credentials

To start your own derivative, you need to set the `DOCKER_USERNAME` and `DOCKER_PASSWORD` secrets as [described here](https://docs.github.com/en/actions/reference/encrypted-secrets#creating-encrypted-secrets-for-a-repository).


### Images

To tweak where all the generated images should go, set `REPO_CACHE` and `FINAL_REPO` according to your needs in the [Makefile](https://github.com/rancher-sandbox/epinio-appliance-demo-sample/blob/master/Makefile).

- `REPO_CACHE` is the image reference used to push all the cache images used to guarantee a reproducible build of your tree given a git checkout (which successfully went through CI).
- `FINAL_REPO` is the destination of the final images that are consumed by the derivatives during upgrades

To allow your derivative to point to the images you have built during upgrades, edit [packages/epinioOS/02_upgrades.yaml](https://github.com/rancher-sandbox/epinio-appliance-demo-sample/blob/master/packages/epinioOS/02_upgrades.yaml) accordingly:

- [Set the FINAL_IMAGE](https://github.com/rancher-sandbox/epinio-appliance-demo-sample/blob/23fd08bda6e26cc9c6018e24b3089a7aa5d44ad5/packages/epinioOS/02_upgrades.yaml#L37)
- [Set the package name](https://github.com/rancher-sandbox/epinio-appliance-demo-sample/blob/23fd08bda6e26cc9c6018e24b3089a7aa5d44ad5/packages/epinioOS/02_upgrades.yaml#L49) accordingly to the package that you want to upgrade when running `cos-upgrade` ( in our case, [system/epinioOS](https://github.com/rancher-sandbox/epinio-appliance-demo-sample/blob/master/packages/epinioOS/definition.yaml) )


## Main difference with [cos-toolkit-sample-repo](https://github.com/rancher-sandbox/cos-toolkit-sample-repo)

In this example, we are not using a [.luet.yaml file](https://github.com/rancher-sandbox/cos-toolkit-sample-repo/blob/master/.luet.yaml) in the top level directory, we are instead using a git submodule to point to specific cos-toolkit checkouts.

_Reproducibility_: The `.luet.yaml` file in the top level is used to fetch the `latest` published version of cos-toolkit and use it to build packages from, this means you should then annotate somewhere what is the version of `cos-toolkit` used to build against if you want to be able to build against the same checkout.

Using a git submodule  allows more fine-grained control on which version of the `cos-toolkit` tree we want to rely on - Also guarantees that at a given point in time at an `EpinioOS` checkout corresponds a given `cos-toolkit` version by looking at the repository history.

```bash
git submodule add https://github.com/rancher-sandbox/cOS-toolkit packages/cos-toolkit
```

## Build locally


Requires `Docker` installed locally and at least 10G of disk space free on `/var/lib/docker`. First build can take up long time depending on your internet connectivity.

```bash
$ git clone --recurse-submodules https://github.com/rancher-sandbox/epinio-appliance-demo-sample
$ cd epinio-appliance-demo-sample
$ source .envrc
$ cos-build
```

## First boot

After installing the appliance with `cos-install`, you already have a [k3s](https://github.com/k3s-io/k3s) cluster and you can see [Longhorn](https://github.com/longhorn/longhorn) and [Epinio](https://github.com/epinio/epinio) coming up:

```KUBECONFIG=/etc/rancher/k3s/k3s.yaml watch kubectl get pods -A```


### Upgrades

Run `cos-upgrade` to upgrade the appliance