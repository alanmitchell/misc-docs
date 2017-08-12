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

There are currently three topics addressed in this document:

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

There is a web service available that provides the necessary data to draw
Solar Contour plots for a number of different sites.  There are two
methods available from that service that return JSON data conforming to
the `JSend <https://labs.omniti.com/labs/jsend>`_ specification.

Method to List Available Sites
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

This method allows you to obtain a list of all of the available sites with
some additional info about each site.

Request URL
^^^^^^^^^^^

::

    GET https://ukwpiuisi9.execute-api.us-west-2.amazonaws.com/dev/sites/?<parameters>

where ``<parameters>`` are optional query parameters, although this method
is generally called with the parameter ``short=T``.  Parameters are discussed
in the next section.

Here is a sample method call requesting a list of all sites with basic
information for each site::

    https://ukwpiuisi9.execute-api.us-west-2.amazonaws.com/dev/sites/?short=T

Request Parameters
^^^^^^^^^^^^^^^^^^

Below are optional request query parameters that can be used to filter or
transform the returned sensor readings.

``short``, optional, a string indicating True or False, defaults to False
    If True, only basic information is returned about each site.  If False,
    all of the data necessary for producing a plot is returned for each site,
    making for a large response payload.  The values of True and False can
    be indicated by a number of different strings, such as: True, true, T, Y,
    1, on.  See `distutils.util.strtobool <https://docs.python.org/2/distutils/apiref.html>`_
    documentation.

Response Fields
^^^^^^^^^^^^^^^

A successful request results in a returned response Status Code of 200, and
a JSON response with the following JSON key/value fields.

``status``, a string
    For a successful request, this field will have the value ``success``.

``data``, one key/value pair with the key ``sites``
    The ``sites`` key contains an array of sites, with each site having a
    collection of key/value pairs describing the site.  The key/value pairs
    are shown in the following example and mostly self-explanatory.

Example Usage
^^^^^^^^^^^^^

Here is a sample successful request::

    https://ukwpiuisi9.execute-api.us-west-2.amazonaws.com/dev/sites/?short=T

Here is the JSON response, with only two of the sites shown in the
response.  There are over 40 sites in the current database.

.. code-block:: json

    {
        "status": "success"
        "data": {
            "sites": [
                {
                    "file_id": "1-911975",
                    "city": "Kona",
                    "state": "Hawaii",
                    "country": "USA",
                    "latitude": 19.7329998016,
                    "longitude": -156.050003052
                },
                {
                    "file_id": "1-703810",
                    "city": "Juneau",
                    "state": "Alaska",
                    "country": "USA",
                    "latitude": 58.3499984741,
                    "longitude": -134.582992554
                }
            ]
        }
    }

The fields are self-explanatory except the ``file_id`` field.  This field
is a unique ID code identifying the site.  It is needed for the next method
call that returns detailed information about one site.

