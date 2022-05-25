.. meta::
   :description: Installation of AceCast, click for more
   :keywords: Installation, prerequisites, download, package, license, running, script, dependencies, AceCast, Documentation, TempoQuest

.. _Installation:


Installation
############

Prerequisites 
=============

Before attempting to install AceCAST users should ensure they have access to a compatible system with NVIDIA GPUs 
(see :ref:`requirementslink`).

Contact and User Support
========================

Please do not hesitate to contact support@tempoquest.com for any issues or questions that you may have during the
installation process.

Download AceCAST
================

To download AceCAST, navigate to the :ref:`releaseslink` page and follow the download instructions for the newest
release of AceCAST. Once the AceCAST tarball has been downloaded, unpack it using the **tar** command as follows.

::

    [acecast-user@localhost]$ tar -xf https://tqi-public.s3.us-east-amazonaws.com/distros/acecast-v2.0.0-beta.0%2Blinux.x86_64.haswell.tar.gz

Package Contents
================

Once unpacked you should see a new `AceCAST` directory with a file structure similar to this:

::

    acecast-v2.0.0-beta.0
    ├── acecast
    │   └── run
    │       - The directory where AceCAST can be launched in, the gpu-launch.sh script can also be 
    │       - found here
    ├── upp
    │   ├── clean
    │   ├── compile 
    │   ├── configure
    │   ├── configure.upp
    │   ├── docs 
    │   ├── exec 
    │   ├── Externals.cfg  
    │   ├── include 
    │   ├── jobs 
    │   ├── lib 
    │   ├── manage_externals
    │   ├── modulefiles
    │   ├── parm 
    │   ├── run_JGLOBAL_NCEPPOST
    │   ├── scripts 
    │   ├── sorc
    │   ├── submit_run_JGLOBAL_NCEPPOST.sh 
    │   └── ush 
    │       - The directory where AceCAST post-processing can occur in
    └── wps 
        ├── geogrid 
        ├── geogrid.exe 
        ├── link_grib.csh
        ├── metgrid
        ├── metgrid.exe
        ├── ungrib
        ├── ungrib.exe
        └── util 
            - The regular WPS CPU directory where AceCAST pre-processing can occur in
 

Many of these files should already look familiar to seasoned WRF users. We have added short annotations above describing
files and directories that we believe are worth noting.

WPS (preprocessing system) and UPP (post-processing system) are included in the download so as to provide 
a convenient way for a user to conduct an end-to-end workflow. However, if a user chooses so, they do not 
have to use the WPS and UPP that comes with the AceCAST download and may chose to conduct their own 
preprocessing and postprocessing elsewhere.

Acquire a License
=================

AceCAST is a licensed software package and as such requires a valid license to run. A 60-day trial license can be acquired
by registering at the `TempoQuest Registration Page <https://tempoquest.com/acecast-registration/>`_. 
After registering you should recieve an email containing your trial license. We suggest placing this file in the 
`AceCAST/run` directory. If your 60-day trial has ended please contact support@tempoquest.com to request an extension 
and/or a quote.


Getting dependencies
====================

To ensure compatibility with the precompiled executables in the AceCAST distribution please navigate to the NVIDIA 
HPC SDK page to `download the NVIDIA SDK 21.9 library <https://developer.nvidia.com/nvidia-hpc-sdk-219-downloads>`_. 
For help installing this library please refer to `this page <https://docs.nvidia.com/hpc-sdk/archive/21.9/index.html>`_.
