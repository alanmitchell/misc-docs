.. _solar-contour-plot:

Data and Sample code for Creating a Solar Contour Plot
======================================================

This document provides information useful for developing a web application
that displays Solar Contour Plots and was primarily created to
assist with the 2017 Launch Alaska Hackathon event.
Solar Contour Plots are plots that show the expected
output of a grid-connected solar photovoltaic system oriented at various
directions and tilt angles.  Here is a sample plot for Anchorage, Alaska:

.. figure:: /_static/solar-contour-ex.png

These topics are addressed in this document:

- Documentation of a web service that provides the data to make this
  type of plot for a number of different locations primarily in Alaska
  but also locations in other states.
- Sample Python code for producing the plot using the open source
  `Ploty <https://plot.ly>`_ charting library. (Ultimately, the Javascript
  version of the Plotly library should be used in a web application).

Web Service for Providing the necessary Plot Data
-------------------------------------------------

There is a web service available that provides the necessary data to draw
Solar Contour plots for a number of different sites.  There are two
methods available from that service that return JSON data conforming to
the `JSend <https://labs.omniti.com/labs/jsend>`_ specification.

API Method to List Available Sites
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

This method allows you to obtain a list of all of the available sites with
some additional info about each site.

Request URL
^^^^^^^^^^^

::

    GET https://ukwpiuisi9.execute-api.us-west-2.amazonaws.com/dev/sites/?<parameters>

where ``<parameters>`` are optional query parameters; this method
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
    1, on.  See the `distutils.util.strtobool <https://docs.python.org/2/distutils/apiref.html>`_
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

API Method to Retrieve Solar Plot data for One Site
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

This method allows you to obtain the detailed plot data for one of the
sites retrieved in the prior method call.

Request URL
^^^^^^^^^^^

::

    GET https://ukwpiuisi9.execute-api.us-west-2.amazonaws.com/dev/sites/<file_id>/?<parameters>

where ``file_id`` is the unique identifier for the site, as retrieved
from the ``sites`` method call described in the prior section.
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

Here is a sample request::

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

Many of the fields are the same as those from the "list of sites"
method call, but there are a few new ones.  The ``file_id_desc`` is
a description of the weather station used to produce the solar climate
data.  All of the climate data sites used are National Renewable
Energy Laboratory (NREL) TMY3 sites.  ``elevation`` is the elevation in feet
of the weather station.  The solar output data provided by this API
was calculated using the `NREL PVWatts program <http://pvwatts.nrel.gov/>`_;
the ``inputs`` field gives a summary of the inputs used in that PVWatts
calculation.

Finally, the ``tilt``, ``azimuth`` and ``energy`` fields provide the data
necessary to draw the contour plot.  An evenly spaced grid of solar output
values (the ``energy`` field) are provided for each combination of ``tilt``
and ``azimuth`` (azimuth is compass direction, e.g. 90 = East, 180 = South, 270 = West).
``tilt`` and ``azimuth`` are one-dimensional arrays, and ``energy`` is a
two-dimensional array because it has a value for every tilt/azimuth
combination.  In the example above, only the first three rows of the
two-dimensional ``energy`` array are shown; the first row gives the solar
output for a tilt of 0 (horizontal) for every possible azimuth.  These output
values are all the same because azimuth does not matter when a panel is horizontal.
The next row of ``energy`` values is for a tilt of 3 degrees up from the horizontal.
The first value of the row is the solar output for an azimuth of 90 degrees (East),
the next value is for azimuth = 96 degrees, etc.

The Sample Code section at the end of this documents shows how to use these values
to create the contour plot using the Plotly charting library.

API Method Errors
~~~~~~~~~~~~~~~~~

If bad or invalid parameters are passed to an API method call, the ``status``
field in the response will indicate failure and an error message(s) will be
returned in the ``data`` field.  The response Status Code will be 400.
Here is an example:

.. code-block:: json

    {
        "status": "fail",
        "data": {
            "short": "'xy' is an invalid value for the 'short' parameter"
        }
    }

If an internal error occurs while processing a request, a Status Code of
500 will be returned and an error will be indicated in the response payload,
as in this example:

.. code-block:: json

    {
        "status": "error",
        "message": "integer division or modulo by zero"
    }


Sample Code for drawing Contour Plot
------------------------------------

Below is some sample Python code for drawing the contour plot using the
`Ploty <https://plot.ly>`_ charting library.  For an actual web application,
the open source `Plotly Javascript library <https://plot.ly/javascript/>`_
should be used, as no Plotly account and sign-in is required for its use.
Much of the Python code below will translate straight-forwardly into Javascript code.

.. code-block:: python

    import requests
    import plotly.plotly as py
    import plotly.graph_objs as go

    result = requests.get('https://ukwpiuisi9.execute-api.us-west-2.amazonaws.com/dev/sites/1-911975').json()
    site = result['data']['site']
    z = site['energy']
    x = site['azimuth']
    y = site['tilt']

    # A future release of Plotly will implement the "showlabels" parameter
    # commented out below, which will label the contour lines.

    #Pre-defined color scales - 'pairs' | 'Greys' | 'Greens' | 'Bluered' | 'Hot'
    # | 'Picnic' | 'Portland' | 'Jet' | 'RdBu' | 'Blackbody'
    # | 'Earth' | 'Electric' | 'YIOrRd' | 'YIGnBu'

    trace1 = go.Contour(
        z=z,
        x=x,
        y=y,
        colorscale='Hot',
    #    contours=dict(
    #        showlabels=True
    #    ),
    )

    trace2 = go.Scatter(
        x=[90, 135, 180, 225, 270],
        y=[3.5] * len(x),
        mode='text',
        text=['East', 'Southeast', 'South', 'Southwest', 'West'],
        textposition='bottom',
        textfont=dict(
            family='Arial',
            size=20,
            color='Black'
        )
    )

    layout = go.Layout(
        title='Annual kWh Produced per DC kW for various Tilts / Azimuths',
        titlefont=dict(
            family='Arial',
            size=24,
        ),
        autosize=False,
        width=950,
        height=800,
        xaxis=dict(
            title='Direction Faced by Panels, degrees',
            titlefont=dict(
                family='Arial',
                size=22,
            ),
            autotick=False,
            ticks='outside',
            tick0=90,
            dtick=15,
            tickfont=dict(
                size=18,
            ),
        ),
        yaxis=dict(
            title='Tilt of Panels from Horizontal, degrees',
            titlefont=dict(
                family='Arial',
                size=22,
            ),
            autotick=False,
            tick0=0,
            dtick=15,
            tickfont=dict(
                size=18,
            ),
        )
    )

    data = [trace1, trace2]
    fig = go.Figure(data=data, layout=layout)
    py.iplot(fig)

