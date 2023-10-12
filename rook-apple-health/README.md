# Rook Apple Health package

This package enables apps to extract data from Apple Health. **rook_apple_health** is
part
of Rook Extraction, a series of packages dedicated to extracting Health Data from a variety of
[Data Sources](https://docs.tryrook.io/docs/Definitions#data-sources).

## Features

* Check for Apple Health availability.
* Request permissions.
* Retrieve health data:
    * [Sleep Summary](https://docs.tryrook.io/docs/ROOKConnect/DataStructure/#sleep-health)
    * [Physical Summary](https://docs.tryrook.io/docs/ROOKConnect/DataStructure/#physical-health)
    * [Physical Event](https://docs.tryrook.io/docs/ROOKConnect/DataStructure/#physical-health)
    * [Body Summary](https://docs.tryrook.io/docs/ROOKConnect/DataStructure/#body-health)
    * Heart Rate Event
    * Oxygenation Event

## Installation

![Pub Version](https://img.shields.io/pub/v/rook_apple_health?style=for-the-badge&logo=flutter&label=pubdev&label=pubdev&color=7200F7)

```text
flutter pub add rook_apple_health
```

### Development environment

This package was developed with the following sdk constraints:

* dart: ">=3.0.0 <4.0.0"
* flutter: ">=3.0.0"

### Required dependencies

To use this package you'll need to add the following dependencies to your project:

* [equatable](https://pub.dev/packages/equatable): ">=2.0.0 <3.0.0"

### IOS configuration

On root folder run:

```text
flutter pub get
```

On ios/Podfile add the following

```text
platform :ios, '13.0'
```

On ios folder run:

```text
pod install
```

Open the ios folder with Xcode

Add HealthKit capability to your Xcode project:

1. Open your project in Xcode.
2. Click on your project file (most cases is Runner) in the Project Navigator.
3. Click on the "Signing and capabilities" tab.
4. Click on the "+ Capability" button and select "HealthKit" from the list.

Add HealthKit framework to your Xcode project:

1. Open your project in Xcode.
2. Click on your project file (most cases is Runner) in the Project Navigator.
3. Click on the "Build Phases" tab.
4. Click on the "+" button under the "Link Binary With Libraries" section and select "HealthKit.framework" from the
   list.

Declare the privacy permissions used by this SDK. You will need to include the NSHealthShareUsageDescription and
NSHealthUpdateUsageDescription keys in your app's Info.plist file.

These keys provide a description of why your app needs to access HealthKit data and will be displayed to the user in the
permission request dialog.

1. Open ios folder
2. Open Runner folder
3. Add the following to **Info.plist**

```plist
<key>NSHealthShareUsageDescription</key>
<string>This app requires access to your health and fitness data in order to track your workouts and activity levels.</string>
<key>NSHealthUpdateUsageDescription</key>
<string>This app requires permission to write workout data to HealthKit.</string>
```

### Environment

The `RookAppleHealthEnvironment` enum allows to quickly configure the behaviour of **rook-users**, e.g. the
api used to communicate with ROOK servers.

Available environments:

* sandbox ➞ Use this during your app development process.
* production ➞ Use this ONLY when your app is published to the PlayStore.

You can use the `kDebugMode` property of you app to configure the environment:

```dart

const environment = kDebugMode ? RookAppleHealthEnvironment.sandbox : RookAppleHealthEnvironment.production;
```

## Usage

Import rook_apple_health:

```dart
import 'package:rook_apple_health/rook_apple_health.dart';
```

### Initialization

To initialize this SDK call `RookUsersConfiguration.initRookUsers` and provide:

* Context
* [clientUUID](https://docs.tryrook.io/docs/Definitions#client_uuid)
* [Environment](#environment)

```dart
void initialize() {
  const environment = kDebugMode ? RookAppleHealthEnvironment.sandbox : RookAppleHealthEnvironment.production;

  RookAppleHealthConfiguration.initRookAppleHealth(
    Secrets.clientUUID,
    environment,
  ).then((value) {
    // Initialized
  }).catchError((exception) {
    final error = 'RookAppleHealthConfiguration: $exception';

    // Error initializing
  });
}
```

#### Recommendations

When you call `initRookUsers` an HTTP request is made, so you should only call it once. We recommend to code this
process in your application's initialization.

### RookAppleHealthManager

Create an instance of `RookAppleHealthManager`:

```dart

final RookAppleHealthManager manager = RookAppleHealthManager();
```

**WARNING:**

* Before calling any method on `manager`, check [availability](#availability).

### Availability

Before using any of the features of *rook_apple_health*, you need to ensure the user's device has Apple Health
installed.

Call `checkAvailability`, this will return a boolean.

| Status | Description                   | What to do                               |
|--------|-------------------------------|------------------------------------------|
| true   | Apple Health is installed     | Proceed to check permissions             |
| false  | Apple Health is not installed | Prompt the user to install Apple Health. |

### Permissions

#### Request permissions

There are dedicated functions for each [Health Pillar](https://docs.tryrook.io/docs/Definitions#health-data-pillars)
(Sleep, Physical and Body) to request permissions. These functions follow the
convention: `request_data_type_Permissions`

You can also call `requestAllPermissions` to request all health pillar permissions.

```dart
void requestPermissions() async {
  final result = await manager.requestAllPermissions();

  if (result) {
    // A request to Apple Health to open the permissions screen was send.
    // Apple Health can receive the request but refuse to open the permissions screen, in those cases 'result' will also be true
  } else {
    // The request was not send.
  }
}
```

### Retrieving user timezone

To retrieve time zone information call `getUserTimeZone` it will return a `AHUserTimeZone` instance.

```dart
class AHUserTimeZone {
  final String timezone; //  A tz database string describing the area and location.
  final int offset; // An integer describing the UTC offset hours
}
```

### Retrieving health data

There are 2 types of health data **Summaries** and **Events**.

| Health Data | Timezone (Input / Output) | Oldest date of retrieval | Soonest date of retrieval |
|-------------|---------------------------|--------------------------|---------------------------|
| Summary     | UTC                       | N/A                      | Yesterday                 |
| Event       | UTC                       | N/A                      | Today                     |

#### Retrieving health data with a DateTime

All health data types require a DateTime instance of the datetime you want to retrieve data from. When
using `get_health_data_type(DateTime date)` the provided `date` is used to create a range
between `date` (start) and the end of the day (end) or the current time if `date` is from today.

Follow the following examples:

##### Retrieve health data for the whole day of yesterday

Today is **2023-05-26 15:12:20**

To retrieve health data for the whole day of yesterday (**2023-05-25**) call `get_health_data_type(DateTime date)`
where date is obtained with:

```dart

final date = DateTime.now() // Today 2023-05-26T15:12:20
    .subtract(const Duration(days: 1)); // Yesterday 2023-05-25T15:12:20
```

As you can see we started obtaining **today's** then subtracted one day to get **yesterday's**.

Internally the SDK will perform the following operations:

1. Remove the 15:12:20 of `date`
2. Use 2023-05-25T00:00:00 as `start`
3. Create the range between `start` and the end of the day `start` belongs to.

So the time range will end up like:

`start`: 2023-05-25T00:00:00 < --- >`end`: 2023-05-26T00:00:00

##### Retrieve health data of today

Today is **2023-05-26 15:12:20**

To retrieve health data of today (**2023-05-26**) call `get_health_data_type(DateTime date)` where date is obtained
with:

```dart

final date = DateTime.now(); // Today 2023-05-26T15:12:20
```

Internally the SDK will perform the following operations:

1. Remove the 15:12:20 of `date`
2. Use 2023-05-26T00:00:00 as `start`
3. Create the range between `start` and the current time (**15:12:20**)

So the time range will end up like:

`start`: 2023-05-26T00:00:00 < --- >`end`: 2023-05-26T15:12:20

#### Retrieve summaries

To retrieve any type of summary, you need to provide a date. This date cannot be the current
day. See the examples below:

| Current date | Provided date | Is valid?                       |
|--------------|---------------|---------------------------------|
| 2023-01-08   | 2023-01-08    | No, the date is from today      |
| 2023-01-08   | 2023-01-07    | Yes, the date is from yesterday |
| 2023-01-08   | 2023-01-01    | Yes, the date is 7 days old     |

To get health data, call `get_data_type` and provide a DateTime instance of the day you want to retrieve the data from.

For example, if you want to get yesterday's sleep summary, call `getSleepSummary`. It will return
an `SleepSummary` instance or throw an exception if an error happens.

```dart
void getSleepSummary() async {
  try {
    final now = DateTime.now().subtract(const Duration(days: 1));
    final date = DateTime(now.year, now.month, now.day).toUtc();

    final result = await manager.getSleepSummary(date);

    // Success
  } catch (error) {
    // Manage error
  }
}
```

##### Keeping track of the last date a summary was retrieved

Health Connect does not allow retrieving data on background, so every time your users open your app
you should retrieve the data manually to help you retrieve the data of the days the user did not
open your app. We store in preferences the last date data was retrieved.

Call `getLastExtractionDate(HCRookDataType rookDataType)` providing a `HCRookDataType`, e.g. if you want to retrieve
the last date a `SleepSummary` was retrieved use `HCRookDataType.sleepSummary`.

It will return a DateTime instance.

Rules:

* The returned DateTime will be in UTC.
* The returned DateTime will be the same you provided when retrieving health data.

##### Last extraction date of a sleep summary example

Let's suppose that one of your users opens the app on `2023-01-10`, the app then retrieves a sleep
summary from yesterday (`2023-01-09`) with `getSleepSummary`.

Then the user forgets to open the app until `2023-01-15`, then you'll
call `getLastExtractionDate(HCRookDataType rookDataType)`it will return `2023-01-09` in a DateTime instance. Now,
in a loop, you can recover data from the days the user did not open the app (`2023-01-10` to `2023-01-14`).

An example using sleep summaries is detailed below:

```dart
void recoverLostDays() async {
  const oneDay = Duration(days: 1);

  final now = DateTime.now();
  final today = DateTime(now.year, now.month, now.day).toUtc();

  DateTime date = await manager.getLastExtractionDate(HCRookDataType.sleepSummary);

  date = date.add(oneDay);

  while (date.isBefore(today)) {
    try {
      final result = await manager.getSleepSummary(date);

      // Success
    } catch (error) {
      // Manage error
    }

    date = date.add(oneDay);
  }
}
```

#### Retrieve events

To retrieve any type of event, you need to provide a date. See the examples below:

| Current date | Provided date | Is valid?                       |
|--------------|---------------|---------------------------------|
| 2023-01-08   | 2023-01-08    | Yes, the date is from today     |
| 2023-01-08   | 2023-01-07    | Yes, the date is from yesterday |
| 2023-01-08   | 2023-01-01    | Yes, the date is 7 days old     |

To get health data, call `get_data_type` and provide a DateTime instance of the day you want to retrieve the data
from.

For example, if you want to get today's physical events, call `getPhysicalEvents`. It will return
an `List<HCPhysicalEvent>` or throw an exception if an error happens or `RecordsNotFoundException` if there is no
physical data on that day.

```dart
void getPhysicalEvents() async {
  try {
    final now = DateTime.now();
    final date = DateTime(now.year, now.month, now.day).toUtc();

    final result = await manager.getPhysicalEvents(date);

    // Success
  } catch (error) {
    // Manage error
  }
}
```

##### Keeping track of the last date an event was retrieved

Health Connect does not allow retrieving data on background, so every time your users open your app
you should retrieve the data manually to help you retrieve the data of the days the user did not
open your app. We store in preferences the last date data was retrieved.

Call `getLastExtractionDate(HCRookDataType rookDataType)` providing a `HCRookDataType`, e.g. if you want to retrieve
the last date a `PhysicalEvent` was retrieved use `HCRookDataType.physicalEvent`.

It will return a DateTime instance.

Rules:

* The returned DateTime will be in UTC.
* The returned DateTime will be equal to the date of the last event found.

##### Last extraction date of a physical event example

Let's suppose that one of your users opens the app on `2023-01-10`, the app then retrieves physical events from
today (`2023-01-10`) with `getPhysicalEvents`.

Then the user forgets to open the app until `2023-01-15`, then you'll
call `getLastExtractionDate(HCRookDataType rookDataType)`it will return `2023-01-10` in a DateTime instance. Now,
in a loop, you can recover data from the days the user did not open the app (`2023-01-10` to `2023-01-14`).

An example using physical events is detailed below:

```dart
void recoverLostDays() async {
  const oneDay = Duration(days: 1);

  final now = DateTime.now();
  final today = DateTime(now.year, now.month, now.day).toUtc();

  DateTime date = await manager.getLastExtractionDate(HHCRookDataType.physicalEvent);

  // date = date.plusDays(oneDay) Not necessary the returned date belongs to the last event found

  while (date.isBefore(today)) {
    try {
      final result = await manager.getPhysicalEvents(date);

      // Success
    } catch (error) {
      // Manage error
    }

    date = date.add(oneDay);
  }
}
```

## Other resources

* See a complete list of `RookAppleHealthManager` methods in
  the [API Reference](https://pub.dev/documentation/rook_apple_health/latest/rook_apple_health_manager/RookAppleHealthManager-class.html)
