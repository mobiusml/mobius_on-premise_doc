.. _installation-label:

Installation
==================

Before you can use the Mobius On-Premise SDK, you have to follow a few steps as explained here.
We provide our solution as a combination of a python package (wheel) and dockerfile. Using the Dockerfile allows to build a docker image with everything you need to run the Mobius Vision SDK.


Requirements for the Mobius On-Premise SDK
-------------------------------------------

In order to run the Mobius Vision On-Premise SDK, the following software requirements have to be met:

*   Nvidia Drivers >= 410.48 (for GPU version)
*   docker >= 1.12
*   nvidia-docker2 (for GPU version)

We can support the following hardware generations for GPUs: Kepler, Maxwell, Pascal, Volta, Turing

We also offer a CPU version. Please let us know if you are interested in the specific requirements for that version. 

Docker Installation
-------------------

To use GPU versions of Mobius Vision SDK you need to have nvidia-docker2. You can install it by instructions from https://github.com/NVIDIA/nvidia-docker or our instructions.

If nvidia-docker2 is installed already then you can skip this step.

1. Install Docker CE:
https://docs.docker.com/install/linux/docker-ce/ubuntu/


2. Add nvidia-docker repo to apt, as explained here:
  https://nvidia.github.io/nvidia-docker/

3. Install the nvidia-docker2 package and reload the Docker daemon configuration
::

  sudo apt-get install nvidia-docker2
  sudo pkill -SIGHUP dockerd

4. Add your user to the docker group.
::

  sudo usermod -aG docker $USER

5. Log out and log back in so that your group membership is re-evaluated.


Mobius Vision Installation
--------------------------

1. Build Mobius Vision docker image
::

  unzip mobius_sdk.zip
  cd mobius_sdk
  nvidia-docker build -t mobius_labs/mobius_sdk:latest -f Dockerfile .

3. To check that image was built sucessfully run following command
::

  docker images

You should see something like this
::

  REPOSITORY               TAG           IMAGE ID            CREATED             SIZE
  mobius_labs/mobius_sdk   latest        d4a9c41f3496        32 minutes ago      6.69GB

Now you are ready to run the SDK.
