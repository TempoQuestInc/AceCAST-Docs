
.. meta::
   :description: AceCast (GPU-Accelerated WRF) homepage, click for more
   :keywords: AceCast, Documentation, TempoQuest, WRF, GPU, Accelerated, WSV3

.. |br| raw:: html

   <br />

AceCast Online Documentation Homepage
#####################################

Quick links: :ref:`Download <latestlink>` | `TempoQuest <https://tempoquest.com/>`_
###################################################################################


.. figure:: /Images/NWP_Logo.png
   :alt: What is NWP?
   :align: left
   :width: 250

   Numerical weather predictions |br|   by splitting the globe into "cubes". `Credit <https://business.esa.int/projects/decisionx>`_

What is AceCAST?
----------------
TempoQuest’s software product, AceCAST, accelerates the world’s most widely used regional weather forecasting system—the Weather Research and Forecasting (`WRF <https://www.mmm.ucar.edu/weather-research-and-forecasting-model>`_) model—developed and supported by the U.S. National Center for Atmospheric Research (NCAR). WRF is a next-generation mesoscale Numerical Weather Prediction (NWP) system designed for both operational forecasting and atmospheric research. It incorporates multiple dynamical cores and a modular architecture that supports extensive parallelization and configurability. With over 36,000 registered users across 162 countries, WRF is employed for applications spanning scales from meters to thousands of kilometers.

While the standard NCAR WRF distribution is designed for traditional CPU-based computing and does not natively support GPU acceleration, AceCAST extends WRF with full GPU capability. Developed through years of targeted research and optimization, AceCAST enables users to achieve performance gains of up to an order of magnitude faster than CPU-only implementations by exploiting the massive parallelism and high memory bandwidth of GPUs.

GPUs deliver exceptional computational throughput through thousands of concurrent cores and superior memory subsystems. Their combination of high parallelism, scalability, and energy efficiency makes GPU-based computing an ideal platform for next-generation NWP. AceCAST incorporates refactored WRF physics and dynamics modules—implemented with CUDA and OpenACC—and is designed as a drop-in replacement for existing WRF configurations, allowing seamless adoption without major workflow changes.

The result is a GPU-accelerated WRF that delivers significantly faster runtimes and higher spatial resolution than CPU-only implementations, enabling operational forecasting at resolutions and timescales previously impractical on conventional computing systems.

Index
=====

.. toctree::
   :maxdepth: 2
   :caption: AceCast

   BeforeYouBegin.rst
   InstallationGuide.rst
   License.rst
   RunningAcecast.rst
   NamelistConfiguration.rst
   GeneratingInputData.rst
   Releases.rst
   Benchmarks.rst
   Containers.rst
   AdvancedTopics.rst

.. toctree::
   :maxdepth: 1
   :caption: Tools

   NmlSupportTbl.rst
   NmlAdvisorApp.rst
   

.. toctree::
   :maxdepth: 2
   :caption: WSV3

   WSV3.rst


.. toctree::
   :maxdepth: 2
   :caption: Other


   Gallery.rst
   Extras.rst
   External_Links.rst
   FAQS.rst
   Verification.rst
   Support.rst
