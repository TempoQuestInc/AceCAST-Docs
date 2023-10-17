.. meta::
   :description: System requirements for AceCast, click for more
   :keywords: Requirements, system, information, CUDA, GPU, AceCast, Documentation, TempoQuest

.. _NVIDIA HPC SDK: 
   https://developer.nvidia.com/hpc-sdk

.. _NVHPC Installation Guide: 
   https://docs.nvidia.com/hpc-sdk/archive/21.9/hpc-sdk-install-guide/index.html

.. _NVHPC 21.9 Downloads: 
   https://developer.nvidia.com/nvidia-hpc-sdk-219-downloads

.. _NVHPC platform requirements: 
   https://docs.nvidia.com/hpc-sdk/archive/21.9/hpc-sdk-release-notes/index.html#platform-requirements

.. _CUDA Installation Guide: 
   https://docs.nvidia.com/cuda/archive/11.4.1/cuda-installation-guide-linux/index.html

.. _AceCAST Registration Page:
    https://tempoquest.com/acecast-registration
   
.. _AMD ROCM: 
   https://docs.amd.com/

.. _installationguide:


Installation Guide
##################

This is a step-by-step guide to installing AceCAST and its software dependencies on your system 
locally.

.. _requirementslink:

Platform Requirements
=====================

Check for compatible OS and CPU architecture
--------------------------------------------

AceCAST is highly integrated with the `NVIDIA HPC SDK`_ and is supported on any platforms supported 
by the `NVIDIA HPC SDK`_ (for more details see `NVHPC platform requirements`_). Currently we only 
provide AceCAST distribution packages (see :ref:`releaseslink`) for Linux x86_64 systems but if you 
are interested in AceCAST for Linux OpenPOWER or Linux ARM, please contact us at 
support@tempoquest.com. AceCAST is not supported on Windows machines but note that AceCAST can be 
run within Linux VMs running on Windows or as a container (see :ref:`Containers`).

**CPU Architecture:**

.. tabs::

    .. tab:: command

        .. code-block:: shell
            
            uname -m

    .. tab:: example output
        
        .. code-block:: output

            x86_64


**Operating System Info:**

.. tabs::

    .. tab:: command

        .. code-block:: shell
            
            cat /etc/*release

    .. tab:: example output
        
        .. code-block:: output

            NAME="Red Hat Enterprise Linux"
            VERSION="8.3 (Ootpa)"
            ID="rhel"
            ID_LIKE="fedora"
            VERSION_ID="8.3"
            PLATFORM_ID="platform:el8"
            PRETTY_NAME="Red Hat Enterprise Linux 8.3 (Ootpa)"
            ANSI_COLOR="0;31"
            CPE_NAME="cpe:/o:redhat:enterprise_linux:8.3:GA"
            HOME_URL="https://www.redhat.com/"
            BUG_REPORT_URL="https://bugzilla.redhat.com/"

            REDHAT_BUGZILLA_PRODUCT="Red Hat Enterprise Linux 8"
            REDHAT_BUGZILLA_PRODUCT_VERSION=8.3
            REDHAT_SUPPORT_PRODUCT="Red Hat Enterprise Linux"
            REDHAT_SUPPORT_PRODUCT_VERSION="8.3"
            Red Hat Enterprise Linux release 8.3 (Ootpa)
            Red Hat Enterprise Linux release 8.3 (Ootpa)

Make sure your OS/CPU architecture combination is supported by the `NVIDIA HPC SDK`_ (see 
`NVHPC platform requirements`_). 

Check for CUDA-capable GPUS
---------------------------

AceCAST can only be run on CUDA-capable GPUs with a compute capability of 3.5 or above. To check 
for CUDA-capable GPUs run the following:

**Checking for GPUs:**

.. tabs::

    .. tab:: command

        .. code-block:: shell
            
            lspci | grep -i nvidia

    .. tab:: example output
        
        .. code-block:: output

            01:00.0 3D controller: NVIDIA Corporation GA100 [A100 SXM4 40GB] (rev a1)
            41:00.0 3D controller: NVIDIA Corporation GA100 [A100 SXM4 40GB] (rev a1)
            81:00.0 3D controller: NVIDIA Corporation GA100 [A100 SXM4 40GB] (rev a1)
            c1:00.0 3D controller: NVIDIA Corporation GA100 [A100 SXM4 40GB] (rev a1)

Once you have determined what type of GPUs you have you can verify they have a valid compute 
capability `here <https://developer.nvidia.com/cuda-gpus>`_.

Installing the NVIDIA CUDA Driver
=================================

Prior to istalling the `NVIDIA HPC SDK`_ you will need to make sure the proper CUDA driver (version 
11.0 or higher) is installed. To check if the CUDA driver is already installed on your system you 
can try running the following:

**NVIDIA SMI Command:**

.. tabs::

    .. tab:: command

        .. code-block:: shell
            
            nvidia-smi

    .. tab:: example output
        
        .. code-block:: output

            +-----------------------------------------------------------------------------+
            | NVIDIA-SMI 470.57.02    Driver Version: 470.57.02    CUDA Version: 11.4     |
            |-------------------------------+----------------------+----------------------+
            | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
            | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
            |                               |                      |               MIG M. |
            |===============================+======================+======================|
            |   0  NVIDIA A100-SXM...  On   | 00000000:01:00.0 Off |                    0 |
            | N/A   28C    P0    50W / 400W |      0MiB / 40536MiB |      0%      Default |
            |                               |                      |             Disabled |
            +-------------------------------+----------------------+----------------------+
            |   1  NVIDIA A100-SXM...  On   | 00000000:41:00.0 Off |                    0 |
            | N/A   26C    P0    48W / 400W |      0MiB / 40536MiB |      0%      Default |
            |                               |                      |             Disabled |
            +-------------------------------+----------------------+----------------------+
            |   2  NVIDIA A100-SXM...  On   | 00000000:81:00.0 Off |                    0 |
            | N/A   28C    P0    50W / 400W |      0MiB / 40536MiB |      0%      Default |
            |                               |                      |             Disabled |
            +-------------------------------+----------------------+----------------------+
            |   3  NVIDIA A100-SXM...  On   | 00000000:C1:00.0 Off |                    0 |
            | N/A   26C    P0    48W / 400W |      0MiB / 40536MiB |      0%      Default |
            |                               |                      |             Disabled |
            +-------------------------------+----------------------+----------------------+
                                                                                           
            +-----------------------------------------------------------------------------+
            | Processes:                                                                  |
            |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
            |        ID   ID                                                   Usage      |
            |=============================================================================|
            |  No running processes found                                                 |
            +-----------------------------------------------------------------------------+

If the command runs without any issues the CUDA drivers are already installed. Make sure the cuda 
version is at least 11.0 or higher in the *nvidia-smi* output. If this is not the case then follow
the `CUDA Installation Guide`_ before moving on. Note that this step requires root access to 
install.

.. _nvhpc_install:

Installing the NVIDIA HPC SDK
=============================

AceCAST requires installation of the NVIDIA HPC SDK version 21.9. You can either follow the 
`NVHPC Installation Guide`_ (make sure to use the archived downloads page at 
`NVHPC 21.9 Downloads`_) or you can try our quick method below:

**NVHPC v21.9 Quick Install:**

.. tabs::

    .. tab:: Quick Installation

        .. code-block:: shell
            
            export NVHPC_INSTALL_DIR=$HOME/nvhpc     # feel free to change this path
            export NVHPC_INSTALL_TYPE=single 
            export NVHPC_SILENT=true 
            wget https://developer.download.nvidia.com/hpc-sdk/21.9/nvhpc_2021_219_Linux_x86_64_cuda_multi.tar.gz
            tar xpzf nvhpc_2021_219_Linux_x86_64_cuda_multi.tar.gz
            nvhpc_2021_219_Linux_x86_64_cuda_multi/install
                
            echo '#!/bin/bash'"
            export NVARCH=\`uname -s\`_\`uname -m\`
            export NVCOMPILERS=$NVHPC_INSTALL_DIR
            export MANPATH=\$MANPATH:\$NVCOMPILERS/\$NVARCH/21.9/compilers/man
            export PATH=\$NVCOMPILERS/\$NVARCH/21.9/compilers/bin:\$PATH
            export LD_LIBRARY_PATH=\$NVCOMPILERS/\$NVARCH/21.9/compilers/lib:\$LD_LIBRARY_PATH
            export LD_LIBRARY_PATH=\$NVCOMPILERS/\$NVARCH/21.9/cuda/11.0/lib64:\$LD_LIBRARY_PATH
            export LD_LIBRARY_PATH=\$NVCOMPILERS/\$NVARCH/21.9/math_libs/11.0/lib64:\$LD_LIBRARY_PATH

            export PATH=\$NVCOMPILERS/\$NVARCH/21.9/comm_libs/mpi/bin:\$PATH
            export LD_LIBRARY_PATH=\$NVCOMPILERS/\$NVARCH/21.9/comm_libs/mpi/lib:\$LD_LIBRARY_PATH
            export MANPATH=\$MANPATH:\$NVCOMPILERS/\$NVARCH/21.9/comm_libs/mpi/man
            " > $NVHPC_INSTALL_DIR/acecast_env.sh

        .. note::
            This step can take a while depending on your internet speeds. The installation itself typically 
            takes 10 minuts or so.

    .. tab:: Updating Environment Script
        
        .. note::
            AceCAST v3.1.0 introduced changes that require updated paths in the environment. To ensure AceCAST
            v3.1.0 and later link properly at runtime, users who set up the *acecast_env.sh* script prior to 
            v3.1.0 with the Quick Installation commands should use this to update their acecast environment script.

        .. code-block:: shell
            
            export NVHPC_INSTALL_DIR=$HOME/nvhpc     # make sure this is set to what it was when you ran the quick install
                
            echo '#!/bin/bash'"
            export NVARCH=\`uname -s\`_\`uname -m\`
            export NVCOMPILERS=$NVHPC_INSTALL_DIR
            export MANPATH=\$MANPATH:\$NVCOMPILERS/\$NVARCH/21.9/compilers/man
            export PATH=\$NVCOMPILERS/\$NVARCH/21.9/compilers/bin:\$PATH
            export LD_LIBRARY_PATH=\$NVCOMPILERS/\$NVARCH/21.9/compilers/lib:\$LD_LIBRARY_PATH
            export LD_LIBRARY_PATH=\$NVCOMPILERS/\$NVARCH/21.9/cuda/11.0/lib64:\$LD_LIBRARY_PATH
            export LD_LIBRARY_PATH=\$NVCOMPILERS/\$NVARCH/21.9/math_libs/11.0/lib64:\$LD_LIBRARY_PATH

            export PATH=\$NVCOMPILERS/\$NVARCH/21.9/comm_libs/mpi/bin:\$PATH
            export LD_LIBRARY_PATH=\$NVCOMPILERS/\$NVARCH/21.9/comm_libs/mpi/lib:\$LD_LIBRARY_PATH
            export MANPATH=\$MANPATH:\$NVCOMPILERS/\$NVARCH/21.9/comm_libs/mpi/man
            " > $NVHPC_INSTALL_DIR/acecast_env.sh


.. _environmentsetup:

Environment Setup
-----------------

Notice that a new script is created at *$NVHPC_INSTALL_DIR/acecast_env.sh*. You will need to source
this script to setup your environment prior to running AceCAST. Example:

.. code-block:: shell

    source $HOME/nvhpc/acecast_env.sh


Installing AceCAST
==================

Download AceCAST Distribution Package
-------------------------------------

To install AceCAST itself, navigate to the :ref:`latestlink` and copy the download url for 
AceCAST. You can then download and unpack the distribution using the *wget* and *tar* commands as 
follows:

.. code-block:: shell

    wget https://tqi-public.s3.us-east-2.amazonaws.com/distros/acecast-v3.1.0%2Blinux.x86_64.haswell.tar.gz
    tar -xf acecast-v3.1.0+linux.x86_64.haswell.tar.gz

If successful you should see a new directory *acecast-v3.1.0*. The directory structure should look 
like the following:

.. code-block:: output

    acecast-v3.1.0
    ├── acecast
    │   └── run
    │       ├── acecast.exe
    │       ├── ideal.exe
    │       ├── ndown.exe
    │       ├── real.exe
    │       └── tc.exe
    ├── upp
    │   └─── exec
    │       └── unipost.exe
    └── wps
        ├── geogrid.exe
        ├── metgrid.exe
        └─── ungrib.exe

.. note::
   You should see more files/directories than what is shown here. We are only showing a subset here 
   to give users a sense of the package contents.

Notice that we have added UPP and WPS packages for your convenience since they are frequently used 
within AceCAST/WRF workflows.

Verify Runtime Environment
--------------------------

One quick way to verify that you have installed and set up your environment correctly in the 
previous steps is to print the shared libraries used by the *acecast.exe* executable with the
*ldd* command.

.. tabs::

    .. tab:: command

        .. code-block:: shell
            
            ldd acecast-v3.1.0/acecast/run/acecast.exe

    .. tab:: successful output example
        
        .. code-block:: output
            :emphasize-lines: 9

            linux-vdso.so.1 (0x0000155555551000)
            libstdc++.so.6 => /lib64/libstdc++.so.6 (0x0000155554f96000)
            libutil.so.1 => /lib64/libutil.so.1 (0x0000155554d92000)
            libz.so.1 => /lib64/libz.so.1 (0x0000155554b7b000)
            libm.so.6 => /lib64/libm.so.6 (0x00001555547f9000)
            libmpi_usempif08.so.40 => /home/samm.tempoquest/nvhpc/Linux_x86_64/21.9/comm_libs/mpi/lib/libmpi_usempif08.so.40 (0x00001555545d0000)
            libmpi_usempi_ignore_tkr.so.40 => /home/samm.tempoquest/nvhpc/Linux_x86_64/21.9/comm_libs/mpi/lib/libmpi_usempi_ignore_tkr.so.40 (0x00001555543cb000)
            libmpi_mpifh.so.40 => /home/samm.tempoquest/nvhpc/Linux_x86_64/21.9/comm_libs/mpi/lib/libmpi_mpifh.so.40 (0x000015555417e000)
            libmpi.so.40 => /home/samm.tempoquest/nvhpc/Linux_x86_64/21.9/comm_libs/mpi/lib/libmpi.so.40 (0x0000155553d3f000)
            libdl.so.2 => /lib64/libdl.so.2 (0x0000155553b3b000)
            libpthread.so.0 => /lib64/libpthread.so.0 (0x000015555391b000)
            librt.so.1 => /lib64/librt.so.1 (0x0000155553713000)
            libc.so.6 => /lib64/libc.so.6 (0x0000155553350000)
            /lib64/ld-linux-x86-64.so.2 (0x000015555532b000)
            libgcc_s.so.1 => /lib64/libgcc_s.so.1 (0x0000155553138000)
            libopen-rte.so.40 => /home/samm.tempoquest/nvhpc/Linux_x86_64/21.9/comm_libs/mpi/lib/libopen-rte.so.40 (0x0000155552df4000)
            libopen-pal.so.40 => /home/samm.tempoquest/nvhpc/Linux_x86_64/21.9/comm_libs/mpi/lib/libopen-pal.so.40 (0x000015555292b000)
            librdmacm.so.1 => /usr/lib64/librdmacm.so.1 (0x0000155552710000)
            libibverbs.so.1 => /usr/lib64/libibverbs.so.1 (0x00001555524f1000)
            libnuma.so.1 => /usr/lib64/libnuma.so.1 (0x00001555522e5000)
            libnvf.so => /home/samm.tempoquest/nvhpc/Linux_x86_64/21.9/comm_libs/mpi/lib/../../../../compilers/lib/libnvf.so (0x0000155551cb0000)
            libnvhpcatm.so => /home/samm.tempoquest/nvhpc/Linux_x86_64/21.9/comm_libs/mpi/lib/../../../../compilers/lib/libnvhpcatm.so (0x0000155551aa5000)
            libatomic.so.1 => /usr/lib64/libatomic.so.1 (0x000015555189d000)
            libnvcpumath.so => /home/samm.tempoquest/nvhpc/Linux_x86_64/21.9/comm_libs/mpi/lib/../../../../compilers/lib/libnvcpumath.so (0x0000155551468000)
            libnvc.so => /home/samm.tempoquest/nvhpc/Linux_x86_64/21.9/comm_libs/mpi/lib/../../../../compilers/lib/libnvc.so (0x0000155551210000)
            libnl-3.so.200 => /usr/lib64/libnl-3.so.200 (0x0000155550fed000)
            libnl-route-3.so.200 => /usr/lib64/libnl-route-3.so.200 (0x0000155550d67000)


        As you can see above all of the required libraries were found and are in the expected 
        locations (for example notice the the mpi library libmpi.so.40 was found within the 
        OpenMPI installation of the NVIDIA HPC SDK).

    .. tab:: problematic output example
        
        .. code-block:: output
            :emphasize-lines: 6,7,8,9

            linux-vdso.so.1 (0x0000155555551000)
            libstdc++.so.6 => /lib64/libstdc++.so.6 (0x0000155554f96000)
            libutil.so.1 => /lib64/libutil.so.1 (0x0000155554d92000)
            libz.so.1 => /lib64/libz.so.1 (0x0000155554b7b000)
            libm.so.6 => /lib64/libm.so.6 (0x00001555547f9000)
            libmpi_usempif08.so.40 => not found
            libmpi_usempi_ignore_tkr.so.40 => not found
            libmpi_mpifh.so.40 => not found
            libmpi.so.40 => not found
            libdl.so.2 => /lib64/libdl.so.2 (0x00001555545f5000)
            libpthread.so.0 => /lib64/libpthread.so.0 (0x00001555543d5000)
            librt.so.1 => /lib64/librt.so.1 (0x00001555541cd000)
            libc.so.6 => /lib64/libc.so.6 (0x0000155553e0a000)
            /lib64/ld-linux-x86-64.so.2 (0x000015555532b000)
            libgcc_s.so.1 => /lib64/libgcc_s.so.1 (0x0000155553bf2000)

        Notice here that the various required MPI libraries (*libmpi.so.40, libmpi_mpifh.so.40, 
        etc.*) were not found. In this case either the NVIDIA HPC SDK was not installed correctly, 
        or the environment was not set up correctly (i.e. the *acecast_env.sh* script was not 
        created or not sourced).

.. note::
   The *ldd* command doesn't guarantee that AceCAST will run correctly but it can be extremely
   helpful in identifying a number of common issues that users run into regularly.


.. _acquirealicense:

Acquire A License
=================

AceCAST is a licensed software package and as such requires a valid license to run. A 30-day trial 
license can be acquired by registering at the `AceCAST Registration Page`_. After registering 
you should recieve an email containing your trial license (*acecast-trial.lic*). We suggest placing 
this file in the *acecast-v3.1.0/acecast/run* directory. If your 30-day trial has ended please 
contact support@tempoquest.com to request an extension and/or a quote.
























