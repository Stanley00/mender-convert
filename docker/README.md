Docker environment for mender-convert
=====================================

In order to correctly set up partitions and bootloaders, mender-convert has many dependencies,
and their version and name vary between Linux distributions.

To make using mender-convert easier, a reference setup using a Ubuntu 18.04 Docker container
is provided.

You need to [install Docker Engine](https://docs.docker.com/install) to use this environment.


## Build the mender-convert container image

To build a container based on Ubuntu 18.04 with all required dependencies for mender-convert,
copy this directory to your workstation and change the current directory to it.

Then run

```bash
./docker-build
```

This will create a container image you can use to run mender-convert.


## Use the mender-convert container image

Create a directory `input` under the directory where you copied these files (`docker-build`, `docker-mender-convert`, etc.):

```bash
mkdir input
```

Then put your raw disk image into `input`, e.g.

```bash
mv ~/Downloads/2018-11-13-raspbian-stretch.img input/2018-11-13-raspbian-stretch.img
```

You can run mender-convert from inside the container with your desired options, e.g.


```bash
DEVICE_TYPE="raspberrypi3"
RAW_DISK_IMAGE="input/2018-11-13-raspbian-stretch.img"

ARTIFACT_NAME="2018-11-13-raspbian-stretch"
MENDER_DISK_IMAGE="2018-11-13-raspbian-stretch.sdimg"
TENANT_TOKEN="<INSERT-TOKEN-FROM Hosted Mender>"

./docker-mender-convert from-raw-disk-image                 \
            --raw-disk-image $RAW_DISK_IMAGE                \
            --mender-disk-image $MENDER_DISK_IMAGE          \
            --device-type $DEVICE_TYPE                      \
            --mender-client /mender                         \
            --artifact-name $ARTIFACT_NAME                  \
            --bootloader-toolchain arm-linux-gnueabihf      \
            --server-url "https://hosted.mender.io"         \
            --tenant-token $TENANT_TOKEN
```

Note that the default Mender client is the latest stable and cross-compiled for generic ARM boards,
which should work well in most cases. If you would like to use a different Mender client,
place it in `inputs` and adjust the `--mender-client` argument.

Conversion will take 10-15 minutes, depending on your storage and resources available.
You can watch `output/build.log` for progress and diagnostics information.

After it finishes, you can find your images in the `output` directory on your host machine!


## Known issues
* BeagleBone images might not convert properly using this docker envirnoment due to permission issues: `mount: /mender-convert/output/embedded/rootfs: WARNING: device write-protected, mounted read-only.`