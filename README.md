<div align="center">
  <a href="https://elementary.io" align="center">
    <center align="center">
      <img src="https://raw.githubusercontent.com/elementary/brand/master/logomark-black.png" alt="elementary" align="center">
    </center>
  </a>
  <br>
  <h1 align="center"><center>elementary OS</center></h1>
  <h3 align="center"><center>Build scripts for image creation</center></h3>
  <br>
  <br>
</div>

<p align="center">
  <img src="https://github.com/elementary/os/workflows/stable/badge.svg" alt="Stable">
  <img src="https://github.com/elementary/os/workflows/daily-5.1/badge.svg" alt="Daily 5.1">
  <img src="https://github.com/elementary/os/workflows/daily-6.0/badge.svg" alt="Daily 6.0">
</p>

---

## Building Locally

As elementary OS is built with the Debian version of `live-build`, not the Ubuntu patched version, it's easiest to build an elementary .iso in a Debian VM or container. This prevents messing up your host system too.

The following examples assume you have Docker correctly installed and set up, and that your current working directory is this repo. When done, your image will be in the `builds` folder.

### 64-bit AMD/Intel

Configure the channel in the `etc/terraform.conf` (stable, daily), then run:

```sh
docker run --privileged -i -v /proc:/proc \
    -v ${PWD}:/working_dir \
    -w /working_dir \
    debian:latest \
    /bin/bash -s etc/terraform.conf < build.sh
```

### 64-bit ARM

Configure the channel in the `etc/terraform-next-7.0-arm64.conf` (daily), then run:
```sh
docker run --privileged -i -v /proc:/proc \
    -v ${PWD}:/working_dir \
    -w /working_dir \
    debian:latest \
    /bin/bash -s etc/terraform-next-7.0-arm64.conf < build.sh
```

#### **NOTE**: If building on macOS, run the following
```sh
docker run --rm -v $(pwd):/source -v eos:/working_dir \
     debian:latest cp -av /source/. /working_dir
docker run --rm --privileged --network host -i -v /proc:/proc \
    -v eos:/working_dir \
    -w /working_dir \
    debian:latest \
    /bin/bash -s etc/terraform-next-7.0-arm64.conf < build.sh
docker run --rm -v $(pwd):/source -v eos:/working_dir \
     debian:latest cp -av /working_dir/builds/arm64/elementaryos-7.0-daily.yyyymmdd-arm64.iso /source/.
docker volume rm eos
```
___[ADD TO WIKI]___

Docker can't use a macOS directory as a working directory because APFS isn't case-sensitive (by default) whereas Linux is.
To work around this, the working directories content are copied into a Docker volume and mounted by the container that then builds the image.
After the build, the disk image is then be copied out of the Docker volume and the volume is erased. Another oddity is sometimes downloads from the elementary PPAs will timeout when using Docker's default networking (bridged). Setting it to host prevents this.

It's also recommended to have Docker configured to use the new Virtualization framework and VirtioFS, as they greatly increase build and transfer speeds.

___[END ADD]___

### Raspberry Pi 4

```sh
docker run --privileged -i -v /proc:/proc \
    -v ${PWD}:/working_dir \
    -w /working_dir \
    debian:unstable \
    ./build-rpi.sh
```

### Pinebook Pro

```sh
docker run --privileged -i -v /proc:/proc \
    -v ${PWD}:/working_dir \
    -w /working_dir \
    debian:unstable \
    ./build-pinebookpro.sh
```

## Further Information

More information about the concepts behind `live-build` and the technical decisions made to arrive at this set of tools to build an .iso can be found [on the wiki](https://github.com/elementary/os/wiki/Building-iso-Images).
