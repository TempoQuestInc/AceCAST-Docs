.. meta::
   :description: Verification for AceCAST, Validation for AceCAST, click for more
   :keywords: Validation, Verification, Testing, Accuracy, Results, AceCast, Documentation, TempoQuest

.. _Verification:

Verification and Validation
###########################

TempoQuest Verification Process
===============================

TempoQuest goes through an extensive review process to ensure their AceCAST product meets high quality standards. For every 
release of AceCAST TempoQuest runs AceCAST through 1,000s of small simulations to test for mainly different dynamics options 
and physics options. By comparing against 2 CPU baselines and checking the root mean squares and root mean squared errors 
for each default output variable, TempoQuest is able to verify that the results from AceCAST are the same as when running 
WRF on CPU’s. Additionally, TempoQuest also checks that the results are the same (or better) across different versions of 
AceCAST. Through a series of regression tests, TempoQuest also checks AceCAST through 3 (main) different builds, different 
domain sizes and regions, different runtimes, different GPU’s (NVIDIA V100 and NVIDIA A100), support tests, host-compile/exec.
preservation tests, bit-for-bit tests, and false-positive support tests. TempoQuest is proud to stand by their products’ 
exceptional performance. Should you have any questions or concerns regarding the verification process and/or procedure, 
please feel free to reach out to us!

TempoQuest Validation Process
=============================

AceCAST by TempoQuest is built upon the Weather Research and Forecasting model (WRF). This means that results should be the 
same between the WRF and AceCAST. For any new features added to AceCAST TempoQuest checks the results of numerous variables 
(such as temperature, wind, radar, reflectivity, etc…) across 2 different CPU simulations and 1 GPU simulation. If the 
difference in values is within a specific range, the results are considered the same and thus depicts that AceCAST works 
as intended.

.. admonition:: Disclaimer

    TempoQuest is not responsible for bugs that appear in the base WRF model (CPU). This means that any problems with a 
    simulation that appear in WRF simulation will likely also appear in AceCAST. However, TempoQuest submits any issues 
    directly to the WRF developers to ensure that the problem is found, addressed, and fixed in a future release of the 
    WRF model, and in extension, AceCAST.
