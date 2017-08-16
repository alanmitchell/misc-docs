.. _heat-pump-calc:

Data for a Heat Pump Economics Calculator Application
=====================================================

This document describe and links to data that will be useful in creating
a Heat Pump Economics calculator application.  This page was created to
support the 2017 Launch Alaska Hackathon.

Spreadsheet Version of the Calculator
-------------------------------------

`Here is a link <http://analysisnorth.com/hackathon/heatpump/Ductless_Heat_Pump_Calculator_Alaska.xlsm>`_
to a spreadsheet version of a Heat Pump
Economics calculator.  The primary use for this spreadsheet is to understand the
calculation logic of the calculator.  There are also some weather and fuel cost data in
the spreadsheet, but more complete and accurate data is linked to in
subsequent sections of this document.

Climate Data
------------

`Here is a tab-delimited text File <http://analysisnorth.com/hackathon/heatpump/temp_bin_data.txt>`_
that contains outdoor temperature data from 75 Alaskan sites.  This data is "bin data"
and shows how many hours of a typical year fall into each possible 5 degree range of
outdoor temperature.  For example, one of the bins in the file is labeled
``(50, 55]`` and contains the value of 603 for the Adak weather station.  This means that
of the 8760 total hours in a year, the outdoor temperature is between 50 and 55 degrees
Fahrenhet for 603 of those hours.  This data format is useful for calculating the
performance of a heat pump because the amount of heating load to be supplied and the
efficiency of the heat pump both vary with the outdoor temperature.  Fuel savings
for each temperature bin can be calculated and aggregated into a total for the
whole year.

The file also contains additional data about each site, including latitude and
longitude.  These coordinates will be useful for finding the nearest climate site
to a user's home address.

Heating Fuel and Electricity Price Data
---------------------------------------

Price data for the fuel being avoided by the heat pump and the electricity
used by the heat pump are needed to calculcate the dollar savings from
use of the heat pump.  `This tab-delimited text file <http://analysisnorth.com/hackathon/heatpump/fuel_prices.txt>`_
gives fuel and electricity price data for 200+ cities in Alaska, along with
the latitude and longitude of those cites so the city nearest to a user's
home can be located.  Units for the fuel price data in the file are:

- Oil: $/gallon
- Propane: $/gallon
- Electricity: $/kWh
- Natural Gas: $/ccf
