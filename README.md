# EpinioOS demo appliance

This is a sample repository to demonstrate how to create a [Epinio](https://github.com/epinio/epinio) appliance with [cos-toolkit](https://github.com/rancher-sandbox/cOS-toolkit).

## Ready to fork

This project is ready to fork! By forking it, you have already all the pieces to build and distribute a custom Epinio appliance.

### Github Actions

The Epinio appliance is built entirely on Github Actions. 

You can test locally the appliance by grabbing an ISO from the [github page](https://github.com/rancher-sandbox/epinio-appliance-demo-sample/actions/workflows/build.yaml?query=event%3Aschedule+is%3Asuccess). Final images are pushed over [Dockerhub](https://hub.docker.com/repository/docker/raccos/epinio-appliance-demo-test) and the cache images can be found [here](https://hub.docker.com/repository/docker/raccos/epinio-appliance-cache-demo-test).

#### Setup

##### Credentials

To start your own derivative, you need to set the `DOCKER_USERNAME` and `DOCKER_PASSWORD` secrets as [described here](https://docs.github.com/en/actions/reference/encrypted-secrets#creating-encrypted-secrets-for-a-repository).


##### Images

To tweak where all the generated images should go, set `REPO_CACHE` and `FINAL_REPO` according to your needs in the [Makefile](https://github.com/rancher-sandbox/epinio-appliance-demo-sample/blob/master/Makefile).

- `REPO_CACHE` is the image reference used to push all the cache images used to guarantee a reproducible build of your tree given a git checkout (which successfully went through CI).
- `FINAL_REPO` is the destination of the final images that are consumed by the derivatives during upgrades

To allow your derivative to point to the images you have built during upgrades, edit [packages/epinioOS/02_upgrades.yaml](https://github.com/rancher-sandbox/epinio-appliance-demo-sample/blob/master/packages/epinioOS/02_upgrades.yaml) accordingly:

- [Set the FINAL_IMAGE](https://github.com/rancher-sandbox/epinio-appliance-demo-sample/blob/23fd08bda6e26cc9c6018e24b3089a7aa5d44ad5/packages/epinioOS/02_upgrades.yaml#L37)
- [Set the package name](https://github.com/rancher-sandbox/epinio-appliance-demo-sample/blob/23fd08bda6e26cc9c6018e24b3089a7aa5d44ad5/packages/epinioOS/02_upgrades.yaml#L49) accordingly to the package that you want to upgrade when running `cos-upgrade` ( in our case, [system/epinioOS](https://github.com/rancher-sandbox/epinio-appliance-demo-sample/blob/master/packages/epinioOS/definition.yaml) )