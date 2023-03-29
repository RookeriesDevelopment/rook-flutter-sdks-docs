# Changelog

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
