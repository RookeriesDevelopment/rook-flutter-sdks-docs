# Changelog

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

**New permissions**

From this version onwards you need to include 4 new READ permissions (Check README to see the full list of permissions
that you have to add to your manifest):

* READ_RESPIRATORY_RATE
* READ_NUTRITION
* READ_MENSTRUATION
* READ_POWER

**New permissions system**

If you are using the old health connect permissions declaration (meta-data tag), you MUST migrate otherwise
the `permissions` functions won't work.

**New required dependencies**

From this version onwards the rook-health-connect will require the `equatable` package, see
README `Required depenpendencies` section for more details.

**New timezone requirements**

The DateTime object provided when requesting health data must be in UTC, see README `Timezones` section for more
details.

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
