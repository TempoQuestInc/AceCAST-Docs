.. meta::
   :description: AceCast Container Usage, click for more
   :keywords: docker, nvidia-docker, container, singularity, license, running, acecast, documentation, tempoquest

.. _Containers:


Containers
##########

Containers are a useful solution that bundle applications along with any runtime dependencies that they require. 
Containers have a number of advantages for application portability and deployment. 

.. important::
   This guide is intended to be a supplement addressing the specific topic of running AceCAST containers and as 
   such does not explain other important topics already covered elsewhere. Container users should still take a 
   look at the core topics prior to reading this guide. Of particular importance are the :ref:`Before You Begin`, 
   :ref:`Running AceCAST` and :ref:`namelistconfiguration` pages although users intending to use containers 
   exclusively can notably skip over the :ref:`installationguide`.

Docker
======

Docker is a widely used container system and is a good option if you intend on running AceCAST on any number of 
GPUs on a single node. More information regarding docker can be found at `Docker Docs <https://docs.docker.com/>`_.

Prerequisites
*************

Prior to running the AceCAST containers on a GPU-enabled machine you will need to install the docker engine as well 
as the necessary nvidia drivers for your system. 

Getting the AceCAST Docker Image
***********************************

Our Docker-based AceCAST container images can be found on the `AceCAST DockerHub repository <https://hub.docker.com/repository/docker/tempoquestinc/acecast:3.2.2>`_. 
Once you have chosen the specific image you would like to use you can obtain the image with the 
`docker pull <https://docs.docker.com/engine/reference/commandline/pull/>`_ command:

::

    [acecast-user@gpu-node]$ docker pull tempoquestinc/acecast:3.2.2
    3.2.2: Pulling from tempoquestinc/acecast:3.2.2
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
    Status: Downloaded newer image for tempoquestinc/acecast:3.2.2
    docker.io/tempoquestinc/acecast:3.2.2

Check that the image exists with the `docker images <https://docs.docker.com/engine/reference/commandline/images/>`_ 
command, which lists some basic information about your local images:

::

    [acecast-user@gpu-node]$ docker images
    REPOSITORY                 TAG              IMAGE ID       CREATED        SIZE
    tempoquestinc/acecast:3.2.2      3.2.2        6d6882fef187       5 hours ago    17.6GB

The AceCAST image contains all of the software necessary for running AceCAST inside the container including the 
*NVIDIA HPC SDK* and *AceCAST* itself. The AceCAST executables, static runtime data files, scripts, etc. are 
installed in the */opt/acecast/run/* directory inside the container.

::

    [acecast-user@gpu-node]$ docker run --rm tempoquestinc/acecast:3.2.2 ls /opt/acecast/run
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

General docker run command usage for AceCAST:

::

    docker run [OPTIONS] tempoquestinc/acecast:3.2.2 COMMAND

.. list-table:: Common Docker Run Command Options
   :widths: 25 100
   :header-rows: 0

   * - Option
     - Description
   * - --gpus gpu-request
     - GPU devices to add to the container ('all' to pass all GPUs)
   * - --rm
     - Automatically remove the container when it exits
   * - -w string
     - Working directory inside the container
   * - -v
     - Mount volumes from the specified container(s)

For a full description of available options check out the `docker run command documentation <https://docs.docker.com/engine/reference/commandline/run/>`_ 
or run *docker run --help* from the command line.

Users are free to use containers to run AceCAST in whatever way they see fit. Note that you can use the host OS
to manage your inputs/outputs and use a container simply to run acecast itself. In this example we have already
downloaded the :ref:`Easter500 <Benchmarks>` test case data as well as our acecast license file into the *inputs/* 
subdirectory. This current directory will be mounted inside the container using the *-v* option to the *docker run*
command so all of the files you see here will be available inside the container when it is running.

::
    
    [acecast-user@gpu-node]$ ls
    inputs  run.sh
    [acecast-user@gpu-node]$ ls inputs/
    acecast.lic met_em.d01.2020-04-11_12:00:00.nc  met_em.d01.2020-04-11_13:00:00.nc  namelist.input


We also have a script in the current directory that will be run inside the container in the next step:

::

    [acecast-user@gpu-node]$ cat run.sh 
    #!/bin/bash

    # Create directory for running acecast in and cd into it
    mkdir -p run
    cd run

    # Link AceCAST executables and runtime data files into the run directory
    # Note that these files only exist in the container so they won't be available from the 
    # host system that is running the container after the script finishes executing
    ln -sf /opt/acecast/run/* . 

    # Link the input files that we made available to the docker container using the -v option
    # that we passed to the docker run command
    ln -sf ../inputs/* .        

    # Run real.exe to generate the wrf input files
    # Note that the --allow-run-as-root option for the mpirun command is necessary since the 
    # user will be root inside the container
    mpirun -np 4 --allow-run-as-root ./real.exe

    # Run acecast.exe
    mpirun -np 4 --allow-run-as-root ./gpu-launch.sh ./acecast.exe

To run this script inside the container we use the *docker run* command:

::

    [acecast-user@gpu-node]$ docker run --gpus all -v `pwd`:`pwd` -w `pwd` --rm tempoquestinc/acecast:3.2.2 ./run.sh 
     starting wrf task             1  of             4
     starting wrf task             2  of             4
     starting wrf task             3  of             4
     starting wrf task             0  of             4
     starting wrf task             1  of             4
     starting wrf task             2  of             4
     starting wrf task             3  of             4
     starting wrf task             0  of             4


After the container finishes executing the script we should see the wrf output files in the *run/* 
subdirectory on the host system:

::

    [acecast-user@gpu-node]$ ls run/wrfout*
    wrfout_d01_2020-04-11_12:00:00


Other Useful Examples
*********************

Running the AceCAST advisor script:

::

    [acecast-user@gpu-node]$ ls
    namelist.input
    [acecast-user@gpu-node]$ docker run -v `pwd`:`pwd` -w `pwd` --rm tempoquestinc/acecast:3.2.2 /opt/acecast/run/acecast-advisor.sh --tool support-check

    ***********************************************************************************
    *      ___           _____           _      ___      _       _                    *
    *     / _ \         /  __ \         | |    / _ \    | |     (_)                   *
    *    / /_\ \ ___ ___| /  \/ __ _ ___| |_  / /_\ \ __| |_   ___ ___  ___  ____     *
    *    |  _  |/ __/ _ \ |    / _` / __| __| |  _  |/ _` \ \ / / / __|/ _ \|  __|    *
    *    | | | | (_|  __/ \__/\ (_| \__ \ |_  | | | | (_| |\ V /| \__ \ (_) | |       *
    *    \_| |_/\___\___|\____/\__,_|___/\__| \_| |_/\__,_| \_/ |_|___/\___/|_|       *
    *                                                                                 *
    ***********************************************************************************


    WARNING: Namelist file not specified by user. Using default namelist file path: /home/samm/test_acecast/namelist.input

    Support Check Configuration:
        Namelist                    : /home/samm/test_acecast/namelist.input
        AceCAST Version             : 3.2.2 (build: linux.x86_64.haswell)
        WRF Compatibility Version   : 4.4.2


    NOTE: Namelist options may be determined implicitly if not specified in the given namelist.

    SUPPORT CHECK FAILURE:
        Unsupported option selected for namelist variable ra_lw_physics in &physics: ra_lw_physics=1,1,1
        Supported options for namelist variable ra_lw_physics: 0,4

    SUPPORT CHECK FAILURE:
        Unsupported option selected for namelist variable ra_sw_physics in &physics: ra_sw_physics=1,1,1
        Supported options for namelist variable ra_sw_physics: 0,4

    Support Check Tool Failure: One or more options found that are not supported by AceCAST. Please modify your namelist selections based on the previous "SUPPORT CHECK FAILURE" messages and run this check again.


Verify that GPUs are available on the container:

::

    [acecast-user@gpu-node]$ docker run --gpus all --rm tempoquestinc/acecast:3.2.2 nvidia-smi
    Wed Mar 15 18:14:34 2023       
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 470.161.03   Driver Version: 470.161.03   CUDA Version: 11.4     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  NVIDIA A100-SXM...  Off  | 00000000:87:00.0 Off |                    0 |
    | N/A   31C    P0    54W / 400W |      0MiB / 40536MiB |      0%      Default |
    |                               |                      |             Disabled |
    +-------------------------------+----------------------+----------------------+
    |   1  NVIDIA A100-SXM...  Off  | 00000000:90:00.0 Off |                    0 |
    | N/A   31C    P0    52W / 400W |      0MiB / 40536MiB |      0%      Default |
    |                               |                      |             Disabled |
    +-------------------------------+----------------------+----------------------+
    |   2  NVIDIA A100-SXM...  Off  | 00000000:B7:00.0 Off |                    0 |
    | N/A   29C    P0    53W / 400W |      0MiB / 40536MiB |      0%      Default |
    |                               |                      |             Disabled |
    +-------------------------------+----------------------+----------------------+
    |   3  NVIDIA A100-SXM...  Off  | 00000000:BD:00.0 Off |                    0 |
    | N/A   29C    P0    55W / 400W |      0MiB / 40536MiB |      0%      Default |
    |                               |                      |             Disabled |
    +-------------------------------+----------------------+----------------------+
                                                                                   
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |  No running processes found                                                 |
    +-----------------------------------------------------------------------------+

