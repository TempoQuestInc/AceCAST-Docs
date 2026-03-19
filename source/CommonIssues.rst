.. meta::
   :description: Common AceCAST runtime issues and fixes
   :keywords: AceCast, troubleshooting, segfault, stack size, documentation

.. _commonissues:

Common Issues
#############

Segmentation Faults in AceCAST Executables
==========================================

If you encounter a segmentation fault while running any AceCAST executable, first verify your
shell stack-size limit.

This applies to all executables shipped in the AceCAST package, including `real.exe`,
`acecast.exe`, `geogrid.exe`, `ungrib.exe`, and `metgrid.exe`.

.. tabs::

   .. tab:: Check current stack size

      .. code-block:: shell

         ulimit -s

   .. tab:: Set stack size to unlimited (recommended)

      .. code-block:: shell

         ulimit -s unlimited

.. important::
   Set `ulimit -s unlimited` in the same shell/session before launching AceCAST executables.
   If you run through a scheduler or wrapper script, set it inside that script before `mpirun`.

Why this helps
--------------

AceCAST uses stack-based allocation in performance-critical code paths. With default shell stack
limits, some model configurations can exceed available stack and terminate with a segmentation
fault.
