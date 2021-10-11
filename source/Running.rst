.. _Running AceCAST:

Running AceCAST
###############


A Statement from the Developers
===============================

AceCAST is simply a modified implementation of the standard CPU-based WRF model distributed by NCAR (checkout the 
`wrf users page here <https://www2.mmm.ucar.edu/wrf/users/>`_) that runs on high-performance GPUs. Our goal is to 
make AceCAST a simple and convenient alternative to running WRF on CPUs and as such have preserved as much of the
standard WRF functionality as possible. It is assumed that users are at least somewhat familiar with running the
standard WRF model prior to running AceCAST. This guide is intended to help existing WRF users become familiar 
with the mechanics of running AceCAST as well as help them understand how CPU-WRF and GPU-AceCAST differ.


Input Data
==========

Once the installation script has been run successfully, the next step is to download some input data for testing 
AceCAST. AceCAST intentionally uses the same exact `namelist.input, wrfbdy*, wrfinput*, etc.` files that are used by 
the standard CPU-WRF model. The only restrictions are that they must be compatible with `WRFV3.8.1` and the namelist
options must be supported by AceCAST (see :ref:`Namelist`). Although you can use your own test data, to keep
things simple it is highly recommended to start with one of our :ref:`benchmark test cases <Benchmarks>`. In this
example, we will use the :ref:`Easter500 benchmark <Easter500>`, which is a good test case for a small number of GPUs
and for convenience we download and unpack the benchmark data in the `AceCASTv1.3/benchmarks` directory:

::

    [ec2-user@gpu-node test]$ cd AceCASTv1.3/benchmarks/
    [ec2-user@gpu-node benchmarks]$ wget https://tqi-public.s3.us-east-2.amazonaws.com/datasets/easter500.tar.gz
    ...
    [ec2-user@gpu-node benchmarks]$ tar -xf easter500.tar.gz 
    [ec2-user@gpu-node benchmarks]$ ls
    easter500  easter500.tar.gz
    [ec2-user@gpu-node benchmarks]$ tree easter500
    easter500
    ├── namelist.input
    ├── namelist.wps
    ├── wrfbdy_d01
    └── wrfinput_d01

