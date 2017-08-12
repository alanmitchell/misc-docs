.. _solar-contour-plot:

Solar Contour Plot
==================

This document provides information useful for developing a web application
that displays Solar Contour Plots.  This document was primarily created to
assist with the Launch Alaska Hackathon event.
Solar Contour Plots are plots that show the expected
output of a grid-connected solar photovoltaic system oriented at various
directions and tilt angles.  Here is a sample plot for Anchorage, Alaska:

.. figure:: /_static/solar-contour-ex.png

There are currently three topics addressed in this document

- A web service that provides the data to make this type of plot for a
  number of different locations primarily in Alaska but also locations
  in other states.
- Sample Python code for producing the plot using the open source
  `Ploty <https://plot.ly/.`_ charting library. (Ultimately, the Javascript
  version of the Plotly library should be used in a web application).
- Sample code using ggplot2 in the R language for creating this type of
  plot.

Web Service for Providing the necessary Plot Data
-------------------------------------------------

Method to List Available Sites
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


This method allows you to obtain readings from one sensor in the BMON database.
You can limit the time range of readings returned and you can also have BMON
first perform time-averaging of the readings (e.g. daily
average readings).

Request URL
~~~~~~~~~~~

::

    GET /api/v1/readings/<sensor ID>/?<parameters>

where ``<sensor ID>`` is the BMON Sensor ID for the desired sensor.
``<parameters>`` are optional query parameters that can cause filtering
and transformation of the returned readings; these are described in the
next section.

The BMON Sensor ID for a sensor can be obtained in at least two ways:

- If you use BMON to display a Current Values report that contains the sensor,
  you can hover your mouse over the Sensor Name to reveal pop-up sensor notes.
  At the bottom of the sensor notes, the Sensor ID will be shown.
- The ``/api/v1/sensors/`` API method, described next, returns a list of all
  the sensors in the database.  For each sensor returned, the Sensor ID is
  shown.

Here is a sample method call requesting daily average sensor readings from a
sensor with an ID of ``kake_temp`` from a BMON site accessed at
``https://bmon.analysisnorth.com`` (see discussion of averaging parameter
in next section)::

    https://bmon.analysisnorth.com/api/v1/readings/kake_temp/?averaging=1D

Request Parameters
~~~~~~~~~~~~~~~~~~

Below are optional request query parameters that can be used to filter or
transform the returned sensor readings.

``timezone``, optional, a timezone name string
    If specified, the timestamps of the sensor readings returned are
    expressed in this timezone.  Also, time filter query parameters,
