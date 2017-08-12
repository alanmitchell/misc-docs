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

These topics are addressed in this document:

- Documentation of a web service that provides the data to make this
  type of plot for a number of different locations primarily in Alaska
  but also locations in other states.
- Sample Python code for producing the plot using the open source
  `Ploty <https://plot.ly/.`_ charting library. (Ultimately, the Javascript
  version of the Plotly library should be used in a web application).

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

One query parameter is available, described below:

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

Method to Retrieve Solar Plot data for One Site
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

This method allows you to obtain the detailed plot data for one of the
sites retrieved in the prior method call.

Request URL
^^^^^^^^^^^

::

    GET https://ukwpiuisi9.execute-api.us-west-2.amazonaws.com/dev/sites/<file_id>/?<parameters>

where ``file_id`` is the unique identifier for the site, as retrieved
from the ``sites`` method call desicribed in the prior section.
``<parameters>`` are optional query parameters, although this method
is generally called with no parameters

Here is a sample method call requesting a plot data for the site with
a ``file_id`` of ``1-911975``::

    https://ukwpiuisi9.execute-api.us-west-2.amazonaws.com/dev/sites/1-911975

Request Parameters
^^^^^^^^^^^^^^^^^^

The ``short`` query parameter, described in the prior method call, is also
available for this method.  However, this method generally uses the default
value of False because the intent is to return the full set of data associated
with a site.

Response Fields
^^^^^^^^^^^^^^^

A successful request results in a returned response Status Code of 200, and
a JSON response with the following JSON key/value fields.

``status``, a string
    For a successful request, this field will have the value ``success``.

``data``, one key/value pair with the key ``site``
    The ``site`` key contains a collection of information about the site,
    including all of the data necessary to make the contour plot.  The
    fields returned under the ``site`` key are discussed in the next
    section giving example usage.

Example Usage
^^^^^^^^^^^^^

Here is a sample successful request::

    https://ukwpiuisi9.execute-api.us-west-2.amazonaws.com/dev/sites/1-911975

Here is the JSON response:

.. code-block:: json

    {
        "status": "success"
        "data": {
            "site": {
                "file_id": "1-911975",
                "file_id_desc": "KONA INTL AT KEAHOL",
                "city": "Kona",
                "state": "Hawaii",
                "country": "USA",
                "latitude": 19.7329998016,
                "longitude": -156.050003052,
                "elevation": 49.215,
                "inputs": "{'losses': 14, 'module_type': 0, 'file_id': u'1-911975', 'array_type': 0, 'system_capacity': 1.0}",
                "tilt": [0,3,6,9,12,15,18,21,24,27,30,33,36,39,42,45,48,51,54,57,60,63,66,69,72,75,78,81,84,87,90],
                "azimuth": [90,96,102,108,114,120,126,132,138,144,150,156,162,168,174,180,186,192,198,204,210,216,222,228,234,240,246,252,258,264,270],
                "energy": [
                    [1454,1454,1454,1454,1454,1454,1454,1454,1454,1454,1454,1454,1454,1454,1454,1454,1454,1454,1454,1454,1454,1454,1454,1454,1454,1454,1454,1454,1454,1454,1454],
                    [1462,1464,1466,1468,1470,1471,1472,1473,1474,1475,1475,1475,1475,1475,1475,1474,1473,1472,1471,1469,1468,1466,1464,1462,1460,1458,1456,1454,1452,1449,1447],
                    [1467,1471,1475,1478,1482,1484,1487,1489,1491,1492,1493,1493,1493,1492,1492,1490,1488,1486,1484,1481,1478,1474,1471,1467,1463,1459,1454,1450,1446,1442,1438],
                    ["Additional arrays deleted for brevity.  There is an array for each tilt."]
                ]
            }
        }
    }
