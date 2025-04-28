.. meta::
   :description: System requirements for AceCast, click for more
   :keywords: Requirements, system, information, CUDA, GPU, AceCast, Documentation, TempoQuest

.. _NVIDIA HPC SDK: 
   https://developer.nvidia.com/hpc-sdk

.. _NVHPC Installation Guide: 
   https://docs.nvidia.com/hpc-sdk/archive/25.3/hpc-sdk-install-guide/index.html

.. _NVHPC 25.3 Downloads: 
   https://developer.nvidia.com/nvidia-hpc-sdk-253-downloads

.. _NVHPC platform requirements: 
   https://docs.nvidia.com/hpc-sdk/archive/25.3/hpc-sdk-release-notes/index.html#platform-requirements

.. _CUDA Installation Guide: 
   https://docs.nvidia.com/cuda/archive/12.3.0/cuda-installation-guide-linux/index.html

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

AceCAST can only be run on CUDA-capable GPUs with a compute capability of 5.0 or above. To check 
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

Prior to installing the `NVIDIA HPC SDK`_ you will need to make sure the proper CUDA driver (version 
12.0 or higher) is installed. To check if the CUDA driver is already installed on your system you 
can try running the following:

**NVIDIA SMI Command:**

.. tabs::

    .. tab:: command

        .. code-block:: shell
            
            nvidia-smi

    .. tab:: example output
        
        .. code-block:: output

            +---------------------------------------------------------------------------------------+
            | NVIDIA-SMI 535.183.01             Driver Version: 535.183.01   CUDA Version: 12.2     |
            |-----------------------------------------+----------------------+----------------------+
            | GPU  Name                 Persistence-M | Bus-Id        Disp.A | Volatile Uncorr. ECC |
            | Fan  Temp   Perf          Pwr:Usage/Cap |         Memory-Usage | GPU-Util  Compute M. |
            |                                         |                      |               MIG M. |
            |=========================================+======================+======================|
            |   0  NVIDIA A10G                    On  | 00000000:00:1E.0 Off |                    0 |
            |  0%   31C    P8              23W / 300W |      0MiB / 23028MiB |      0%      Default |
            |                                         |                      |                  N/A |
            +-----------------------------------------+----------------------+----------------------+
                                                                                                     
            +---------------------------------------------------------------------------------------+
            | Processes:                                                                            |
            |  GPU   GI   CI        PID   Type   Process name                            GPU Memory |
            |        ID   ID                                                             Usage      |
            |=======================================================================================|
            |  No running processes found                                                           |
            +---------------------------------------------------------------------------------------+

If the command runs without any issues the CUDA drivers are already installed. Make sure the cuda 
version is at least 12.0 or higher in the *nvidia-smi* output. If this is not the case then follow
the `CUDA Installation Guide`_ before moving on. Note that this step requires root access to 
install.

.. _nvhpc_install:

Installing the NVIDIA HPC SDK
=============================

AceCAST requires installation of the NVIDIA HPC SDK version 25.3. You can either follow the 
`NVHPC Installation Guide`_ (make sure to use the archived downloads page at 
`NVHPC 25.3 Downloads`_) or you can try our quick method below:

.. important::
   AceCAST v5.0.* uses the NVHPC SDK version 25.3. Previous versions of AceCAST required older versions of the NVHPC SDK. Users will need to install this newer version of the NVIDIA HPC SDK with the new version of AceCAST.

**NVHPC v25.3 Quick Install:**

.. tabs::

    .. tab:: Quick Installation

        .. code-block:: shell
            
            export NVHPC_INSTALL_DIR=$HOME/nvhpc     # feel free to change this path
            export NVHPC_INSTALL_TYPE=single
            export NVHPC_SILENT=true
            wget https://developer.download.nvidia.com/hpc-sdk/25.3/nvhpc_2025_253_Linux_x86_64_cuda_12.8.tar.gz
            tar xpzf nvhpc_2025_253_Linux_x86_64_cuda_12.8.tar.gz
            nvhpc_2025_253_Linux_x86_64_cuda_12.8/install

            echo '#!/bin/bash'"
            export NVARCH=\`uname -s\`_\`uname -m\`
            export NVCOMPILERS=$NVHPC_INSTALL_DIR
            export MANPATH=\$MANPATH:\$NVCOMPILERS/\$NVARCH/25.3/compilers/man
            export PATH=\$NVCOMPILERS/\$NVARCH/25.3/compilers/bin:\$PATH
            export LD_LIBRARY_PATH=\$NVCOMPILERS/\$NVARCH/25.3/compilers/lib:\$LD_LIBRARY_PATH
            export LD_LIBRARY_PATH=\$NVCOMPILERS/\$NVARCH/25.3/cuda/lib64:\$LD_LIBRARY_PATH
            export LD_LIBRARY_PATH=\$NVCOMPILERS/\$NVARCH/25.3/math_libs/lib64:\$LD_LIBRARY_PATH

            export PATH=\$NVCOMPILERS/\$NVARCH/25.3/comm_libs/hpcx/bin:\$PATH
            export LD_LIBRARY_PATH=\$NVCOMPILERS/\$NVARCH/25.3/comm_libs/hpcx/lib:\$LD_LIBRARY_PATH
            export MANPATH=\$MANPATH:\$NVCOMPILERS/\$NVARCH/25.3/comm_libs/hpcx/man
            " > $NVHPC_INSTALL_DIR/acecast_env.sh

        .. note::
            This step can take a while depending on your internet speeds. The installation itself typically 
            takes 10 minutes.

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

    wget https://tqi-public.s3.us-east-2.amazonaws.com/distros/acecast-v4.0.2%2Blinux.x86_64.haswell.nvhpc25.3.tar.gz
    tar -xf acecast-v4.0.2+linux.x86_64.haswell.nvhpc25.3.tar.gz

If successful you should see a new directory *acecast-v4.0.2*. The directory structure should look 
like the following:

.. code-block:: output

    acecast-v4.0.2
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
            
            ldd acecast-v4.0.2/acecast/run/acecast.exe

    .. tab:: successful output example
        
        .. code-block:: output
            :emphasize-lines: 9

            linux-vdso.so.1 (0x00007ffe0a7fa000)
            libstdc++.so.6 => /lib64/libstdc++.so.6 (0x00007f049c9d0000)
            libutil.so.1 => /lib64/libutil.so.1 (0x00007f049c7cd000)
            libz.so.1 => /lib64/libz.so.1 (0x00007f049c5b8000)
            libatomic.so.1 => /lib64/libatomic.so.1 (0x00007f049c3b0000)
            libm.so.6 => /lib64/libm.so.6 (0x00007f049c070000)
            libmpi_usempif08.so.40 => /home/samm.tempoquest/nvhpc/Linux_x86_64/24.3/comm_libs/mpi/lib/libmpi_usempif08.so.40 (0x00001555545d0000)
            libmpi_usempi_ignore_tkr.so.40 => /home/samm.tempoquest/nvhpc/Linux_x86_64/24.3/comm_libs/mpi/lib/libmpi_usempi_ignore_tkr.so.40 (0x00001555543cb000)
            libmpi_mpifh.so.40 => /home/samm.tempoquest/nvhpc/Linux_x86_64/24.3/comm_libs/mpi/lib/libmpi_mpifh.so.40 (0x000015555417e000)
            libmpi.so.40 => /home/samm.tempoquest/nvhpc/Linux_x86_64/24.3/comm_libs/mpi/lib/libmpi.so.40 (0x0000155553d3f000)
            libnvhpcwrapcufft.so => /home/samm.tempoquest/nvhpc/Linux_x86_64/24.3/compilers/lib/libnvhpcwrapcufft.so (0x00007f049be6e000)
            libcufft.so.11 => /usr/local/cuda-12.2/targets/x86_64-linux/lib/libcufft.so.11 (0x00007f049113e000)
            libcudart.so.12 => /home/samm.tempoquest/nvhpc/Linux_x86_64/24.3/cuda/12.3/lib64/libcudart.so.12 (0x00007f0490e90000)
            libcudafor_120.so => /home/samm.tempoquest/nvhpc/Linux_x86_64/24.3/compilers/lib/libcudafor_120.so (0x00007f048af67000)
            libcudafor.so => /home/samm.tempoquest/nvhpc/Linux_x86_64/24.3/compilers/lib/libcudafor.so (0x00007f048ad52000)
            libacchost.so => /home/samm.tempoquest/nvhpc/Linux_x86_64/24.3/compilers/lib/libacchost.so (0x00007f048aaed000)
            libaccdevaux.so => /home/samm.tempoquest/nvhpc/Linux_x86_64/24.3/compilers/lib/libaccdevaux.so (0x00007f048a8d1000)
            libacccuda.so => /home/samm.tempoquest/nvhpc/Linux_x86_64/24.3/compilers/lib/libacccuda.so (0x00007f048a5a0000)
            libdl.so.2 => /lib64/libdl.so.2 (0x00007f048a39c000)
            libcudadevice.so => /home/samm.tempoquest/nvhpc/Linux_x86_64/24.3/compilers/lib/libcudadevice.so (0x00007f048a185000)
            libcudafor2.so => /home/samm.tempoquest/nvhpc/Linux_x86_64/24.3/compilers/lib/libcudafor2.so (0x00007f0489f82000)
            libnvf.so => /home/samm.tempoquest/nvhpc/Linux_x86_64/24.3/compilers/lib/libnvf.so (0x00007f048985f000)
            libnvomp.so => /home/samm.tempoquest/nvhpc/Linux_x86_64/24.3/compilers/lib/libnvomp.so (0x00007f048885e000)
            libnvhpcatm.so => /home/samm.tempoquest/nvhpc/Linux_x86_64/24.3/compilers/lib/libnvhpcatm.so (0x00007f0488653000)
            libpthread.so.0 => /lib64/libpthread.so.0 (0x00007f0488435000)
            libnvcpumath-avx2.so => /home/samm.tempoquest/nvhpc/Linux_x86_64/24.3/compilers/lib/libnvcpumath-avx2.so (0x00007f0487ff1000)
            libnvc.so => /home/samm.tempoquest/nvhpc/Linux_x86_64/24.3/compilers/lib/libnvc.so (0x00007f0487d8d000)
            librt.so.1 => /lib64/librt.so.1 (0x00007f0487b85000)
            libc.so.6 => /lib64/libc.so.6 (0x00007f04877d8000)
            libgcc_s.so.1 => /lib64/libgcc_s.so.1 (0x00007f04875c2000)
            /lib64/ld-linux-x86-64.so.2 (0x00007f049cd52000)
            libnvcpumath.so => /home/samm.tempoquest/nvhpc/Linux_x86_64/24.3/compilers/lib/libnvcpumath.so (0x00007f048717e000)


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

AceCAST Trial License
---------------------

AceCAST is a licensed software package and as such requires a valid license to run. If you would like to request a trial license, please fill out the `AceCAST Trial Request Form <https://tempoquest.com/acecast-registration/>`_ justifying your request. Once we have received your request we will contact you within 24 hours (weekdays) with a 30-day trial license if your request is approved.

AceCAST License Requests
------------------------

Please contact support@tempoquest.com for any other licensing requests.

Licence File
------------

Once you have recieved your license file, we suggest placing it in the *acecast-v4.0.2/acecast/run* directory.





















