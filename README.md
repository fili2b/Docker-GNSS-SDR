<!-- prettier-ignore-start -->
[comment]: # (
SPDX-License-Identifier: MIT
)

[comment]: # (
SPDX-FileCopyrightText: 2020 Carles Fernandez-Prades <carles.fernandez@cttc.es>
)
<!-- prettier-ignore-end -->

# Docker GNSS-SDR 

This repository contains a Dockerfile that creates a
[Docker](https://www.docker.com/) image with [GNSS-SDR](https://gnss-sdr.org)
and its dependencies installed via `.deb` packages. This includes
[GNU Radio](https://gnuradio.org/) and drivers for a wide range of RF front-ends
through [UHD](https://github.com/EttusResearch/uhd),
[gr-osmosdr](http://osmocom.org/projects/sdr/wiki/GrOsmoSDR) and
[gr-iio](https://github.com/analogdevicesinc/gr-iio).

This image uses [baseimage-docker](https://github.com/phusion/baseimage-docker),
a special Docker image that is configured for correct use within Docker
containers. It is Ubuntu, plus:

- Modifications for Docker-friendliness.
- Administration tools that are especially useful in the context of Docker.
- Mechanisms for easily running multiple processes, without violating the Docker
  philosophy.
- It only consumes 9 MB of RAM.

The startGNSS.sh script allows us to automate the start of GNSS SDR inside the container by :
- Creating the gps.conf file required for the use of an HackRF One
- Filling it with the parameters required
- Executing GNSS SDR with this configuration file
Thus, when we start the container, GNSS SDR is started immediately afterwards.

## Create a cross-plateform image

We use this repository to create a cross-plateform image of GNSS-SDR for ARM architecture. This is the procedure followed on a x86 architecture : 

      $ git clone https://github.com/ISEN-Cyber/FakeMyAs.git
      $ cd docker-gnsssdr
      $ docker buildx create --name newbuilder
      $ docker buildx use newbuilder
      $ docker buildx build --platform Linux/arm/v7 -t therese2b/gnss:latest --push .
      
The created image is for the `Linux/arm/v7` plateform. We use it for our project and it is available on the therese2b docker hub. You can put your own docker hub name to push the image on it.

## Pull docker image on Raspberry Pi

Run:

    $ docker pull therese2b/gnss:latest
    
If you change the name of the docker hub, then replace `docker pull therese2b/gnss:latest` by `docker pull dockerHubName/repositoryName:version`

### Run with access to a folder in the host machine

Within the scope of our project, we need to use a USB port for the HackRF One and to share, in a volume located on the Raspberry Pi, the output files generated during the GNSS SDR execution. We did that by running the container as:

    $ docker run -it --device=/dev/bus/usb -v data-gpx:/home 7bf344c4f234

`7bf344c4f234` is the id of the image previously created. The `-v` option will mount the `/data-gpx` folder in the host machine on the `/home`
folder inside the container, with read and write permissions.
