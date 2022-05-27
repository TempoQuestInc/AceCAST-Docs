.. meta::
   :description: AceCast Container Usage, click for more
   :keywords: docker, nvidia-docker, container, singularity, license, running, acecast, documentation, tempoquest

.. _Containers:


Containers
##########

Containers are a useful solution that bundle applications along with any runtime dependencies that they require. 
Containers have a number of advantages for application portability and deployment. 

Docker
======

Docker is a widely used container system and is a good option if you intend on running AceCAST on any number of 
GPUs on a single node. More information regarding docker can be found at `Docker Docs <https://docs.docker.com/>`_.

Prerequisites
*************

Prior to running the AceCAST containers on a GPU-enabled machine you will need to install the docker engine as well 
as nvidia-docker and the necessary nvidia drivers for your system. More information on installing these components 
can be found `here <https://github.com/NVIDIA/nvidia-docker/blob/master/README.md>`_.

Getting the AceCAST Docker Image
***********************************

Our Docker-based AceCAST container images can be found on the `AceCAST DockerHub repository <https://hub.docker.com/repository/docker/sammelliott/acecast>`_. 
Once you have chosen the specific image you would like to use you can obtain the image with the 
`docker pull <https://docs.docker.com/engine/reference/commandline/pull/>`_ command:

::

    [acecast-user@gpu-node]$ docker pull sammelliott/acecast:2.0.0-beta.0
    2.0.0-beta.0: Pulling from sammelliott/acecast
    f70d60810c69: Already exists 
    545277d80005: Already exists 
    1e7f98e28850: Already exists 
    86f4173eb75d: Already exists 
    5c52a07ee59c: Already exists 
    3b22fa154f31: Already exists 
    2cd3bbcb6d62: Already exists 
    7751aa45d446: Pull complete 
    646ba5f2329e: Pull complete 
    83c0d9a9685f: Pull complete 
    619ac9d050cc: Pull complete 
    13f006ddd7e1: Pull complete 
    Digest: sha256:a6b5311c66200fe0b8c3f1b11d8856847f144565c9d0666b366f11ae8c733722
    Status: Downloaded newer image for sammelliott/acecast:2.0.0-beta.0
    docker.io/sammelliott/acecast:2.0.0-beta.0

Check that the image exists with the `docker images <https://docs.docker.com/engine/reference/commandline/images/>`_ 
command, which lists some basic information about your local images:

::

    REPOSITORY               TAG                 IMAGE ID       CREATED        SIZE
    sammelliott/acecast      2.0.0-beta.0        6d6882fef187   5 hours ago    17.6GB

The AceCAST image contains all of the software necessary for running AceCAST inside the container including the 
*NVIDIA HPC SDK* and *AceCAST* itself. The AceCAST executables, static runtime data files, scripts, etc. are 
installed in the */opt/acecast/run/* directory inside the container.

::

    [acecast-user@gpu-node]$ nvidia-docker run --rm sammelliott/acecast:2.0.0-beta.0 ls /opt/acecast/run
    acecast.exe
    aerosol.formatted
    aerosol_lat.formatted
    ...
    gpu-launch.sh
    ...
    real.exe
    ...


Running AceCAST in a Container
******************************

Users are free to use containers to run AceCAST in whatever way they see fit. Note that you can use the host OS
to manage your inputs/outputs and use a container simply to run acecast itself. In this example we have already
downloaded the :ref:`Easter500` test case data as well as our acecast license file into the *inputs/* subdirectory.

::
    
    [acecast-user@gpu-node]$ ls
    inputs  run.sh
    [acecast-user@gpu-node]$ ls inputs/
    acecast.lic met_em.d01.2020-04-11_12:00:00.nc  met_em.d01.2020-04-11_13:00:00.nc  namelist.input


We also have a script in the current directory that is intended to run in the container:

::

    [acecast-user@gpu-node]$ cat run.sh 
    #!/bin/bash

    mkdir -p run outputs
    cd run
    ln -sf /opt/acecast/run/* .
    ln -sf ../inputs/* .
    mpirun -np 4 --allow-run-as-root ./real.exe
    mpirun -np 4 --allow-run-as-root ./gpu-launch.sh ./acecast.exe
    mv wrfout* ../outputs/

To run this script inside the container we use the *nvidia-docker run* command:

::

    [acecast-user@gpu-node]$ nvidia-docker run --gpus all -v `pwd`:`pwd` -w `pwd` --rm sammelliott/acecast:2.0.0-beta.0 ./run.sh 
     starting wrf task             1  of             4
     starting wrf task             2  of             4
     starting wrf task             3  of             4
     starting wrf task             0  of             4
     starting wrf task             1  of             4
     starting wrf task             2  of             4
     starting wrf task             3  of             4
     starting wrf task             0  of             4


For a description of the options used above check out the `docker run command documentation <https://docs.docker.com/engine/reference/commandline/run/>`_.

After the container finishes executing the script we should see the wrf output files in the *outputs/* subdirectory on the host OS:

::

    [acecast-user@gpu-node]$ ls outputs/
    wrfout_d01_2020-04-11_12:00:00



