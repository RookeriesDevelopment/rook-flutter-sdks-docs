# Changelog

## 0.3.2

* Fixed const error

## 0.3.1

* Fixed auth error
* Changed data types:
  * BodySummaryItem
    * temperatureDeltaCelsius → double?
  * SleepSummaryItem
    * temperatureMinimumCelsius → double?
    * temperatureAvgCelsius → double?
    * temperatureMaxCelsius → double?
    * temperatureDeltaCelsius → double?
    * saturationAvgPercentage → double?
    * saturationMinPercentage → double?
    * saturationMaxPercentage → double?

## 0.3.0

* Changed String date and dateTime parameters to DateTime.

## 0.2.2

* Fixed index constraint on granular data

## 0.2.1

* Fixed missing URI

## 0.2.0

* Added granular data to:
  * SleepSummaryItem
  * PhysicalSummaryItem
  * PhysicalEventItem
  * BodySummaryItem

## 0.1.0

* Removed following properties from `physical_event_item`
  * velocityVectorAvgSpeedAndDirection
  * velocityVectorMaxSpeedAndDirection
  * positionStartLatLngDeg
  * positionCentroidLatLngDeg
  * positionEndLatLngDeg
  * positionPolylineMapDataSummary

## 0.0.2

* Added physical summaries
* Added physical events
* Added body summaries
* All datetime string are required to be on ISO-8601 format and UTC.

## 0.0.1

* Initial release.
