# Changelog

## 0.6.0

* Authorization it's now managed by this SDK (dependency on **rook_auth** is no longer required),
  see [Initialization](README.md#initialization) for more information.
* Added `RookHealthConnectEnvironment` to configure internal behaviour, see [Environment](README.md#environment) for
  more information.
* Changed `RookHealthConnectManager` constructor parameters,
  see [RookHealthConnectManager](README.md#rookhealthconnectmanager) for more information.
* Removed `getPhysicalStressEvents` and `getBodyMoodEvents` due to always being empty. Will be added in the future if
  Health Connect adds support for the data they require (The classes `HCMoodEvent` and `HCStressEvent` won't be
  removed).

## 0.5.1

* Added Time Zone extraction, see [Retrieving user timezone](README.md#retrieving-user-timezone) for more information.

## 0.5.0

* Updated rook-auth (native)

## 0.4.0

* When retrieving events if the Event has no data `RecordsNotFoundException` will be thrown.
* Body pillar events will be divided in intervals of 1 hour.
* `lastExtractionDate` will follow a different set of rules for Summaries and Events,
  see [Keeping track of the last date a summary was retrieved](README.md#keeping-track-of-the-last-date-a-summary-was-retrieved)
  and [Keeping track of the last date an event was retrieved](README.md#keeping-track-of-the-last-date-an-event-was-retrieved)
  for more details.
* The DateTime provided to health data extraction has new rules,
  see [Retrieving health data](README.md#retrieving-health-data)
  and [Retrieving health data in UTC](README.md#retrieving-health-data-in-utc) for more details.

## 0.3.0

**All classes and enums now have HC at the beginning, e.g.:**

* BodySummary → **HC**BodySummary
* BloodGlucoseGranularDataMgPerDL → **HC**BloodGlucoseGranularDataMgPerDL

**Migrated to data matrix 2.0**

* Added events extraction
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

**New permissions system**

If you are using the old health connect permissions declaration (meta-data tag), you MUST migrate otherwise
the `permissions` functions won't work, see [Android configuration](README.md#android-configuration) for more details.

**New permissions**

From this version onwards you need to include 4 new READ permissions go
to [Android configuration](README.md#android-configuration) to see the full list of permissions
that you have to add to your manifest:

* READ_RESPIRATORY_RATE
* READ_NUTRITION
* READ_MENSTRUATION
* READ_POWER

**New required dependencies**

From this version onwards the rook-health-connect will require the `equatable` package, see
[Required dependencies](README.md#required-dependencies) for more details.

**New timezone requirements**

The DateTime object provided when requesting health data must be in UTC,
see [Retrieving health data in UTC](README.md#retrieving-health-data-in-utc) for more
details.

**Last extraction date**

The following functions have been removed:

* getSleepSummaryLastDate
* getPhysicalSummaryLastDate
* getPhysicalEventsLastDate
* getBodySummaryLastDate

Instead, you should use `getLastExtractionDate` and provide a `HCRookDataType`

**Other**

* Updated flutter and dart versions to ">=3.0.0 <4.0.0"

## 0.2.1

* Updated rook-health-connect (Kotlin)
  to [0.3.3](https://github.com/RookeriesDevelopment/rook-android-sdks-docs/blob/main/rook-health-connect/CHANGELOG.md#033)
* `bloodPressureDayAvgSystolicDiastolic` on `BodySummary` changed to a nullable object.

## 0.2.0

* Updated rook-health-connect (Kotlin)
  to [0.3.1](https://github.com/RookeriesDevelopment/rook-android-sdks-docs/blob/main/rook-health-connect/CHANGELOG.md#031)
* Added sourceOfData to:
    * SleepSummary
    * PhysicalSummary
    * PhysicalEvent
    * BodySummary
* Changed name of the following classes and properties:
    * `FloorsClimbedGranularDataFloors` to `FloorsClimbedGranularData`
    * `BloodPressureDayAvgSystolicDiastolicBp` to `BloodPressureSystolicDiastolic`
    * `BloodPressureSystolicDiastolicBp` to `BloodPressureSystolicDiastolic`
    * `BloodPressureGranularDataSystolicDiastolicBp` to `BloodPressureGranularDataSystolicDiastolic`
    * `BloodPressureGranularDataSystolicDiastolicBp.bloodPressureSystolicDiastolicBp`
      to `BloodPressureGranularDataSystolicDiastolic.bloodPressureSystolicDiastolic`
    * `BodySummary.bloodPressureDayAvgSystolicDiastolicBp`
      to `BodySummary.bloodPressureDayAvgSystolicDiastolic`
    * `BodySummary.bloodPressureGranularDataSystolicDiastolicBp`
      to `BodySummary.bloodPressureGranularDataSystolicDiastolic`
    * `PhysicalEvent.floorsClimbedGranularDataFloors` to `PhysicalEvent.floorsClimbedGranularData`
    * `PhysicalSummary.floorsClimbedGranularDataFloors`
      to `PhysicalSummary.floorsClimbedGranularData`

## 0.1.0

* Updated rook-health-connect (Kotlin)
  to [0.1.0](https://github.com/RookeriesDevelopment/rook-android-sdks-docs/blob/main/rook-health-connect/CHANGELOG.md#010)
    * You may have to update your gradle plugin to **7.4.1**
    * You may have to update your kotlin plugin to **1.8.10**

## 0.0.4

* Added new records to physical events
    * Calories
    * Distance
    * Floors climbed
    * Elevation
    * Heart rate
    * Heart rate variability (RMSSD)
    * Saturation
    * Vo2Max

## 0.0.3

* Fixed missing proto files

## 0.0.2

* Added physical summaries
* Added physical events
* Added body summaries

## 0.0.1

* Initial release.
