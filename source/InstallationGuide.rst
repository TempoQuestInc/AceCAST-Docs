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


Installing the NVIDIA HPC SDK
=============================

AceCAST requires installation of the NVIDIA HPC SDK version 21.9. You can either follow the 
`NVHPC Installation Guide`_ (make sure to use the archived downloads page at 
`NVHPC 21.9 Downloads`_) or you can try our quick method below:

**NVHPC v21.9 Quick Install:**

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

    export PATH=\$NVCOMPILERS/\$NVARCH/21.9/comm_libs/mpi/bin:\$PATH
    export MANPATH=\$MANPATH:\$NVCOMPILERS/\$NVARCH/21.9/comm_libs/mpi/man" > $NVHPC_INSTALL_DIR/acecast_env.sh

.. note::
    This step can take a while depending on your internet speeds. The installation itself typically 
    takes 10 minuts or so.

Notice that a new script is created at *$NVHPC_INSTALL_DIR/acecast_env.sh*. You will need to source
this script to setup your environment prior to running AceCAST. Example:

.. code-block:: shell

    source $HOME/nvhpc/acecast_env.sh


Installing AceCAST
==================

To-Do
