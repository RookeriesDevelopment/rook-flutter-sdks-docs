# Changelog

## 0.2.0

**Migrated to data matrix 2.0**

* Added events extraction
    * Physical Event
    * Heart Rate Event
    * Oxygenation Event

**New permissions**

This version added new permissions, please ask your users for permissions again to avoid unexpected behaviour

**New required dependencies**

From this version onwards the rook-apple-health will require the `equatable` package, see
README `Required depenpendencies` section for more details.

**Last extraction date**

The following functions have been removed:

* getSleepSummaryLastDate
* getPhysicalSummaryLastDate
* getPhysicalEventsLastDate
* getBodySummaryLastDate

Instead, you should use `getLastExtractionDate` and provide a `AHRookDataType`

When calling `getLastExtractionDate` the returned `DateTime` will be equal to the `datetime` of the last registry found
see [Keeping track of the last time a summary was retrieved > example](README.md#example) for more details.

**Other**

* Updated flutter and dart versions to ">=3.0.0 <4.0.0"

## 0.1.1

* Fixed physical summary crash
    * To apply update:
        * Run `flutter pub get` on the root directory
        * Run `pod update` or `pod install` on the ios directory

## 0.1.0

All classes and enums now have AH at the beginning, e.g.:

* BodySummary → **AH**BodySummary
* BloodGlucoseGranularDataMgPerDL → **AH**BloodGlucoseGranularDataMgPerDL

## 0.0.1

* Initial release.
