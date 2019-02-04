.. _installation-label:

Installation
==================

Before you can use the Mobius On-Premise SDK, you have to follow a few steps as explained here.
There are two ways of installation: wheel package and docker image. The docker image is very easy to install, but is around 6GB (for the GPU version).
The wheel package requires a few more steps to be installed, but is only around 400 MB. By default we provide a docker container. Therefore, our instructions focus on this set-up.


Requirements for the Mobius On-Premise SDK
-------------------------------------------

In order to run the Mobius Vision On-Premise SDK, the following software requirements have to be met:

*   CUDA 9+ (requires a GPU)
*   Python 2.7
*   ffmpeg 2.8.15 (only for |mobvis_video|)


Installation from docker image
-------------------------------

1. First, you need to install nvidia-docker2. If nvidia-docker2 are installed already then you can skip this step and jump to step 2.

  1.1 Install Docker CE:
  https://docs.docker.com/install/linux/docker-ce/ubuntu/


  1.2. Add nvidia-docker repo to apt, as explained here:
    https://nvidia.github.io/nvidia-docker/

  1.3. Install the nvidia-docker2 package and reload the Docker daemon configuration
  ::

    sudo apt-get install nvidia-docker2
    sudo pkill -SIGHUP dockerd

  1.4. Add your user to the docker group.
  ::

    sudo usermod -aG docker $USER

  1.5. Log out and log back in so that your group membership is re-evaluated.


2. Load Mobius Vision docker image
::

  docker load --input mobius_vision.tar


3. To check that image was loaded sucessfully run following command
::

  docker images

You should see something like this
::

  REPOSITORY TAG IMAGE ID CREATED SIZE
  mobius_labs/mobius_sdk 0.1 ef8d42276b3f 18 minutes ago 6GB


Now you are ready to run the SDK.
