.. meta::
   :description: License Information for AceCast, click for more
   :keywords: License, AceCast, Documentation, TempoQuest

.. _Licenselink:

License Information
===================

AceCAST Trial License
*********************

If you would like to request a trial license, please fill out the `AceCAST Trial Request Form <https://tempoquest.com/acecast-registration/>`_ justifying your request. Once we have received your request we will contact you within 24 hours (weekdays) with a 30-day trial license if your request is approved.

AceCAST License Requests
************************

Please contact support@tempoquest.com for any other licensing requests.

AceCAST License Usage
*********************

Once you have received a license file, this file will be checked by the acecast.exe executable at runtime to ensure the user has a valid license.

To ensure that AceCAST can evaluate your license at runtime you must point the RLM_LICENSE environment variable to the location of the license file::

    $ export RLM_LICENSE=$HOME/AceCAST/run/acecast-trial.lic

* When running acecast, a successful license checkout should generate output similar to this in the rsl.out.0000 and rsl.error.0000 files::

		Checking out AceCAST Licenses.
		RLM license checkout:
		------------------------
		Product: acecast
		Version: 4
		Count:   8
		------------------------
		Successfully checked out licenses.
