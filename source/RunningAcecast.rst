.. meta::
   :description: Running AceCast, click for more
   :keywords: Running, Usage, MPI, input, environment, AceCast, Documentation, TempoQuest

.. _OpenMPI mpirun documentation:
   https://www.open-mpi.org/doc/v3.1/man1/mpirun.1.php

.. _NVHPC OpenACC Environment Variables:
   https://docs.nvidia.com/hpc-sdk/archive/21.9/compilers/openacc-gs/index.html#env-vars

.. _Running AceCAST:

Running AceCAST
###############

This guide will demonstrate how to run AceCAST by walking you through an example with the 
:ref:`Easter500 benchmark <Benchmarks>` test case.

Before attempting to run, make sure AceCAST and its dependencies have been installed correctly and 
that you have a valid AceCAST license to use the software (see :ref:`installationguide`). Also make
sure that you have a valid license file (see :ref:`acquirealicense`) and have placed it in your
`acecast-v3.2.2/acecast/run/` directory.

For this example we will assume your AceCAST installation is in your home directory (i.e. at 
`~/acecast-v3.2.2`). If it is somewhere else you will need to modify the code examples accordingly.

Input Data
==========

The first step in any AceCAST/WRF workflow is to generate input data for the model. AceCAST uses 
the same `namelist.input, wrfbdy*, wrfinput*, etc.` files that are used by the standard CPU-WRF 
model. The only restriction is that the specified namelist options must be supported by AceCAST 
(see :ref:`namelistconfiguration`). In this guide we will use we will use the 
:ref:`Easter500 benchmark <Benchmarks>` from our standard :ref:`Benchmarks`, which is typically a 
good test case for a small number of GPUs.

**Download Easter500 Test Case Data:**

.. code-block:: shell

    cd ~/acecast-v3.2.2/acecast
    mkdir benchmarks
    cd benchmarks
    wget https://tqi-public.s3.us-east-2.amazonaws.com/datasets/v2/easter500.tar.gz
    tar -xf easter500.tar.gz

At this point your acecast directory should look like this:

.. code-block:: output

    ~/acecast-v3.2.2/acecast
    ├── benchmarks
    │   ├── easter500
    │   │   ├── met_em.d01.2020-04-12_00:00:00.nc
    │   │   ├── << more met_em.* files >>
    │   │   ├── met_em.d01.2020-04-13_12:00:00.nc
    │   │   └── namelist.input
    │   └── easter500.tar.gz
    └── run
        ├── acecast-advisor.sh
        ├── acecast-trial.lic
        ├── acecast.exe
        ├── gpu-launch.sh
        ├── real.exe
        └── << various static data files >>


Setting Up the Simulation Run Directory
=======================================

Next we would like to create a new directory to run the simulation in containing all of the 
necessary runtime and input files. This will typically include the following:

    - AceCAST Run Files (always found in `~/acecast-v3.2.2/acecast/run/`):
        - executables (`real.exe, acecast.exe, etc.`)
        - acecast advisor script (`acecast-advisor.sh`)
        - license file (`acecast.lic` or `acecast-trial.lic`)
        - MPI wrapper script (`gpu_launch.sh`)
        - static data files (`CCN_ACTIVATE.BIN, GENPARM.TBL, LANDUSE.TBL, etc.`)
    - Simulation Specific Input Data and Configuration Files (found in `~/acecast-v3.2.2/acecast/benchmarks/easter500` for this example):
        - namelist file (`namelist.input`)
        - `real.exe` or `acecast.exe` input data (`met_em*` or `wrfbdy*, wrfinput*, etc.`)

.. tip::
    We consider it best practice to create a new directory for each simulation you run. This can 
    help you avoid common mistakes when running large numbers of simulations and also allows you 
    to run multiple simulations simultaneously if you have the compute resources to do so.

For our example we will be using 4 GPUs and will set up this simulation run directory at 
`~/acecast-v3.2.2/acecast/easter500-4GPU`:

.. code-block:: shell

    # Create and cd to new run directory
    mkdir ~/acecast-v3.2.2/acecast/easter500-4GPU
    cd ~/acecast-v3.2.2/acecast/easter500-4GPU

    # Link static acecast run files
    ln -s ../run/* .
    
    # Link input data files
    ln -s ../benchmarks/easter500/met_em.* .

    # Copy the namelist file
    cp ../benchmarks/easter500/namelist.input .
    
.. tip::
    We typically copy the namelist.input file rather than create a symbolic link like we do with 
    all of the other files here. Since the namelist is modified regularly it is best to make 
    changes to the local copy of the file rather than the original, which can cause confusing 
    problems if the namelist is linked and edited in multiple run directories.


Verify Namelist Configuration
=============================

At this point we can use the `acecast-advisor.sh` script to verify that all of the options 
specified in the namelist are supported by AceCAST. We have an entire section of the documentation
dedicated to this topic (see :ref:`namelistconfiguration`) but we will keep things simple for this 
example. 

.. note::
   The :ref:`Easter500 benchmark <Benchmarks>` is distributed with a fully supported namelist but
   we recommend trying out the `acecast-advisor.sh` tool anyways to get a sense of how it works for
   when you start using your own namelists rather than the one that we provide for this example.

**AceCAST Advisor -- Support Check Tool**

.. tabs::

    .. tab:: command

        .. code-block:: shell

            # cd to the simulation run directory if you aren't already there
            ./acecast-advisor.sh --tool support-check

    .. tab:: output for supported namelist

        .. code-block:: output

    
            ***********************************************************************************
            *      ___           _____           _      ___      _       _                    *
            *     / _ \         /  __ \         | |    / _ \    | |     (_)                   *
            *    / /_\ \ ___ ___| /  \/ __ _ ___| |_  / /_\ \ __| |_   ___ ___  ___  ____     *
            *    |  _  |/ __/ _ \ |    / _` / __| __| |  _  |/ _` \ \ / / / __|/ _ \|  __|    *
            *    | | | | (_|  __/ \__/\ (_| \__ \ |_  | | | | (_| |\ V /| \__ \ (_) | |       *
            *    \_| |_/\___\___|\____/\__,_|___/\__| \_| |_/\__,_| \_/ |_|___/\___/|_|       *
            *                                                                                 *
            ***********************************************************************************
            
            
            WARNING: Namelist file not specified by user. Using default namelist file path: /home/samm.tempoquest/acecast-v3.2.2/acecast/easter500-4GPU/namelist.input 

            Support Check Configuration:
                Namelist                    : /home/samm.tempoquest/acecast-v3.2.2/acecast/easter500-4GPU/namelist.input
                AceCAST Version             : 3.2.2
                WRF Compatibility Version   : 4.4.2


            NOTE: Namelist options may be determined implicitly if not specified in the given namelist.

            Support Check Tool Success: No unsupported options found -- Ok to use namelist for AceCAST execution.

    .. tab:: output for unsupported namelist

        .. code-block:: output
            
            ***********************************************************************************
            *      ___           _____           _      ___      _       _                    *
            *     / _ \         /  __ \         | |    / _ \    | |     (_)                   *
            *    / /_\ \ ___ ___| /  \/ __ _ ___| |_  / /_\ \ __| |_   ___ ___  ___  ____     *
            *    |  _  |/ __/ _ \ |    / _` / __| __| |  _  |/ _` \ \ / / / __|/ _ \|  __|    *
            *    | | | | (_|  __/ \__/\ (_| \__ \ |_  | | | | (_| |\ V /| \__ \ (_) | |       *
            *    \_| |_/\___\___|\____/\__,_|___/\__| \_| |_/\__,_| \_/ |_|___/\___/|_|       *
            *                                                                                 *
            ***********************************************************************************
            
            
            WARNING: Namelist file not specified by user. Using default namelist file path: /home/samm.tempoquest/acecast-v3.2.2/acecast/easter500-4GPU/namelist.input 

            Support Check Configuration:
                Namelist                    : /home/samm.tempoquest/acecast-v3.2.2/acecast/easter500-4GPU/namelist.input
                AceCAST Version             : 3.2.2
                WRF Compatibility Version   : 4.4.2


            NOTE: Namelist options may be determined implicitly if not specified in the given namelist.

            SUPPORT CHECK FAILURE:
                Unsupported option selected for namelist variable mp_physics in &physics: mp_physics=10
                Supported options for namelist variable mp_physics: 0,1,6,8,28

            SUPPORT CHECK FAILURE:
                Unsupported option selected for namelist variable cu_physics in &physics: cu_physics=16
                Supported options for namelist variable cu_physics: 0,1,2,11

            Support Check Tool Failure: One or more options found that are not supported by AceCAST. Please modify your namelist selections based on the previous "SUPPORT CHECK FAILURE" messages and run this check again.


Setting Up Your Environment
===========================

Prior to running the executables in the following sections you will need to make sure your 
environment is set up correctly as described in the :ref:`installationguide` (see 
:ref:`environmentsetup`).

Modify OpenMPI Settings (Optional)
----------------------------------

The NVIDIA HPC SDK uses an older version of OpenMPI (version 3.1.5). This version is performant 
and works well on a variety of systems but it can produce some confusing warnings when running MPI
jobs. These warnings can be suppressed by setting the *btl_base_warn_component_unused=0* option 
using the following commands.

.. code-block:: shell
    
    mkdir -p ~/.openmpi
    echo "btl_base_warn_component_unused = 0" > ~/.openmpi/mca-params.conf

Note that this only needs to be done one time on any given system.


Running Real
============

To generate the `wrfinput*, wrfbdy*, etc.` inputs for AceCAST we need to run Real. This works 
the same way it does for WRF and this process should be familiar for WRF users.

.. tabs::

    .. tab:: simple usage

        .. code-block:: shell

            # cd to the simulation run directory if you aren't already there
            mpirun -n <number of cpu cores> ./real.exe

        Change the `<number of cpu cores>` to the number of cores you would like to use to run 
        `real.exe`.

    .. tab:: general usage

        .. code-block:: shell

            # cd to the simulation run directory if you aren't already there
            mpirun [MPIRUN_OPTIONS] ./real.exe

        For more details about the `mpirun` command check out the `OpenMPI mpirun documentation`_ 
        or try:

        .. code-block:: shell

            mpirun --help

.. note::
   The `mpirun` command options can vary depending on a number of factors including the number of
   nodes, CPU cores per node or whether you are running under resource managers (e.g., SLURM, 
   Torque, etc.) to name a few.

If `real.exe` ran successfully then you should see that it generated the input files for AceCAST
(`wrfinput*, wrfbdy*, etc.`) and you can also check for a successful completion message in the RSL
log files:

.. tabs::

    .. tab:: command

        .. code-block:: shell

            tail -n 5 rsl.error.0000

    .. tab:: example output

        .. code-block:: output

            d01 2020-04-13_12:00:00 forcing artificial silty clay loam at   11 points, out of  15625
            d01 2020-04-13_12:00:00 Timing for processing          0 s.
            d01 2020-04-13_12:00:00 Timing for output          1 s.
            d01 2020-04-13_12:00:00 Timing for loop #   37 =          5 s.
            d01 2020-04-13_12:00:00 real_em: SUCCESS COMPLETE REAL_EM INIT    
        
        

Running AceCAST
===============

General AceCAST usage can be summarized as follows:

.. code-block:: shell

    mpirun [MPIRUN_OPTIONS] gpu-launch.sh [--gpu-list GPU_LIST] acecast.exe

We always recommend that you use one MPI task per each GPU you intend to run on. This is 
accomplished through the proper choice of `MPIRUN_OPTIONS` as well as the `gpu-launch.sh` MPI 
wrapper script. The goal of the former is to launch the correct number of MPI tasks on each node. 
The `gpu-launch.sh` script sets the `ACC_DEVICE_NUM` environment variable (see 
`NVHPC OpenACC Environment Variables`_) to the specific GPU id for each MPI task prior to launching 
the `acecast.exe` executable.

.. note::
   For more information about the `gpu-launch.sh` script check out :ref:`GPU Mapping`. 

For our example we can run with 4 GPUs on a single node:

.. tabs::

    .. tab:: command

        .. code-block:: shell

            mpirun -n 4 ./gpu-launch.sh ./acecast.exe

    .. tab:: example output

        .. code-block:: output

             starting wrf task             0  of             4
             starting wrf task             1  of             4
             starting wrf task             2  of             4
             starting wrf task             3  of             4


If AceCAST ran successfully then you should see that it generated the `wrfout*` files. You should 
also check for a successful completion message in the RSL log files:

.. tabs::

    .. tab:: command

        .. code-block:: shell

            tail -n 5 rsl.error.0000

    .. tab:: example output

        .. code-block:: output

            Timing for main: time 2020-04-12_00:59:48 on domain   1:    0.09450 elapsed seconds
            Timing for main: time 2020-04-12_01:00:00 on domain   1:    0.09443 elapsed seconds
            d01 2020-04-12_01:00:00 wrf: SUCCESS COMPLETE WRF
            Checking-in/releasing AceCAST Licenses
            Successfully checked-in/released AceCAST Licenses.



Summary and Next Steps
======================

In this section we covered the basics of running AceCAST through an example where we ran the 
:ref:`Easter500 benchmark <Benchmarks>` test case with 4 GPUs on a single node. By using input 
data from one of our benchmark test cases, we were able to focus on the fundamental mechanics 
of running the AceCAST software before moving on to other critical topics such as generating 
input data and namelist configuration. These will be covered in the next sections 
:ref:`Generating Input Data` and :ref:`namelistconfiguration`.







