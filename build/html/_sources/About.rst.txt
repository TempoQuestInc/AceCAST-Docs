.. _aboutlink:

About
=====

(Under Construction)


What is AceCAST?
----------------

TempoQuest's (TQI's) software product, **AceCAST, accelerates** the world's most widely used regional weather forecasting model, 
known as the Weather Research and Forecasting (`WRF <https://www.mmm.ucar.edu/weather-research-and-forecasting-model>`_) model, 
which is supported by the U.S. National Center for Atmospheric Research (NCAR). **WRF** is widely utilized by more than 36,000 
registered users located in 162 countries, and is a next-generation mesoscale **Numerical Weather Prediction** (NWP) system designed
to serve both operational forecasting and atmospheric research needs. WRF features multiple dynamical cores and a software 
architecture allowing for computational parallelism and system extensibility. WRF is suitable for a broad spectrum of applications 
across scales ranging from meters to thousands of kilometers.

AceCAST is a powerful cutting-edge software **powered by Graphics Processing Units (GPUs)** that enable acceleration of the WRF model. 
AceCAST is the product of half-a-decade of punctilious research and development that empowers WRF users to secure striking 
performance optimizations using the superior massive parallelism of GPU hardware versus traditional CPU computation. AceCAST 
encompasses an ample set of refactored common WRF physics and dynamics modules, and namelist options with NVIDIA CUDA or 
OpenACC GPU programming techniques, allowing a wide swath of users to adopt AceCAST painlessly as a **drop-in replacement 
for existing WRF configurations**. 

GPUs enable exceptional acceleration by using multi-threaded, many-core processors with tremendous computational speed 
and very high memory bandwidth. The combined features of general-purpose supercomputing, high parallelism, high memory 
bandwidth, **low cost**, and compact size are what make a GPU-based system an appealing alternative to a massively parallel 
system made up of commodity CPU clusters. TempoQuest's software is a result of specialized programming and optimization 
techniques coupled with a deep understanding of high-performance computing, NWP, atmospheric science, WRF configurations, 
GPUs, and CUDA code. The TQI developed CUDA-based GPU WRF is currently the world's fastest and highest resolution weather 
forecasting model.


How will AceCAST Benefit You?
------------------------------

Weather forecasts have a critical impact on the economy and extreme weather events are one of the greatest risks in the world.
High quality weather forecasts require a tremendous amount of computational power, which TempoQuest has addressed by accelerating
the WRF model by utilizing GPUs. Compared to standard computing methods, AceCAST is currently the world's fastest and highest 
resolution weather forecasting model. Running WRF via AceCAST enables users to run forecast and research simulations 
with accelerated time-to-solution processes at higher resolutions, lower cost, and deeper insight. The capabilities of AceCAST 
provides meteorologists and end-users the products derived by WRF that deliver greater awareness of localized weather phenomena 
that global weather models are unable to discern, or which are missed at lower resolutions. The compute performance improvement 
makes high resolution deterministic and probabilistic forecasts practical at lower cost than running WRF on Central Processing 
Units (CPUs), and the forecast simulation acceleration **has consistently improved forecast processing speed by a factor of five**.
