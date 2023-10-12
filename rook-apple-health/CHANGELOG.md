# Changelog

## 0.4.0

* Authorization it's now managed by this SDK (dependency on **rook_auth** is no longer required),
  see [Initialization](README.md#initialization) for more information.
* Added `RookAppleHealthEnvironment` to configure internal behaviour, see [Environment](README.md#environment) for
  more information.
* Changed `RookAppleHealthManager` constructor parameters, see [RookAppleHealthManager](README.md#rookapplehealthmanager) for more
  information.

## 0.3.2

* Added Time Zone extraction, see [Retrieving user timezone](README.md#retrieving-user-timezone) for more information.

**Before running your app you MUST run `pod install` or `pod update` in your **/ios** directory**

## 0.3.1

* Fixed typo in oxygenation events

**Before running your app you MUST run `pod install` or `pod update` in your **/ios** directory**

## 0.3.0

* Body pillar events will be divided in intervals of 1 hour.
* `lastExtractionDate` will follow a different set of rules for Summaries and Events,
  see [Keeping track of the last date a summary was retrieved](README.md#keeping-track-of-the-last-date-a-summary-was-retrieved)
  and [Keeping track of the last date an event was retrieved](README.md#keeping-track-of-the-last-date-an-event-was-retrieved)
  for more details.
* The DateTime provided to health data extraction has new rules,
  see [Retrieving health data](README.md#retrieving-health-data) for more details.

## 0.2.0

**Migrated to data matrix 2.0**

* Added events extraction
    * Physical Event
    * Heart Rate Event
    * Oxygenation Event

**New permissions**

This version added new permissions, please ask your users for permissions again to avoid unexpected behaviour

**New required dependencies**

From this version onwards the rook-health-connect will require the `equatable` package, see
[Required dependencies](README.md#required-dependencies) for more details.

**Last extraction date**

The following functions have been removed:

* getSleepSummaryLastDate
* getPhysicalSummaryLastDate
* getPhysicalEventsLastDate
* getBodySummaryLastDate

Instead, you should use `getLastExtractionDate` and provide a `AHRookDataType`

**Other**

* Updated flutter and dart versions to ">=3.0.0 <4.0.0"
* Before running your app you MUST run `pod install` or `pod update` in your **/ios** directory

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
