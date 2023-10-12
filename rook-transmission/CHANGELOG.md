# Changelog

## 0.6.0

* Authorization it's now managed by this SDK (dependency on **rook_auth** is no longer required),
  see [Initialization](README.md#initialization) for more information.
* Added `RookTransmissionEnvironment` to configure internal behaviour, see [Environment](README.md#environment) for
  more information.
* Changed `RookTransmissionManager` constructor parameters,
  see [RookTransmissionManager](README.md#rooktransmissionmanager) for more information.

## 0.5.1

* Added `uploadUserTimeZone` to update users timezone, see [Updating user timezone](README.md#updating-user-timezone) for more information.

## 0.5.0

* Updated rook_auth to 0.3.0

## 0.4.0

**Migrated to data matrix 2.0**

* Added events transmission
    * Blood Glucose Event
    * Blood Pressure Event
    * Body Metrics Event
    * Heart Rate Event
    * Hydration Event
    * Mood Event
    * Nutrition Event
    * Oxygenation Event
    * Stress Event
    * Temperature Event

**New required dependencies**

From this version onwards the rook-transmission will require the `equatable` package, see
README `Required depenpendencies` section for more details.

**Other**

* Updated flutter and dart versions to ">=3.0.0 <4.0.0"
* Updated http to ">=1.0.0 <2.0.0"
* Updated rook_auth to 0.2.0

## 0.3.3

* Updated schemas to isar `3.1.0+1`. Please verify you have this version before updating.

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
