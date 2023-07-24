# Rook Apple Health package

This package enables apps to extract data from Apple Health. **rook_apple_health** is
part
of Rook Extraction, a series of packages dedicated to extracting Health Data from a variety of
[Data Sources](https://docs.tryrook.io/docs/Definitions#data-sources).

## Features

* Check for Apple Health availability.
* Request permissions.
* Retrieve health data:
    * [Sleep Summary](https://docs.tryrook.io/docs/DataStructure/SleepHealth#summaries)
    * [Physical Summary](https://docs.tryrook.io/docs/DataStructure/PhysicalHealth#summaries)
    * Physical Event
    * [Body Summary](https://docs.tryrook.io/docs/DataStructure/BodyHealth#summaries)
    * Heart Rate Event
    * Oxygenation Event

## Installation

![Pub Version](https://img.shields.io/pub/v/rook_apple_health?style=for-the-badge&logo=flutter&color=7200F7)

```text
flutter pub add rook_apple_health
```

### Environment

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

## Usage

Import rook_apple_health:

```dart
import 'package:rook_apple_health/rook_apple_health.dart';
```

Create an instance of `RookAppleHealthManager` providing:

* A [clientUUID](https://docs.tryrook.io/docs/Definitions#client_uuid)

```dart

final RookAppleHealthManager manager = RookAppleHealthManager(clientUUID);
```

Call `init` and wait for the result:

```dart
void initialize() async {
  final manager = RookAppleHealthManager(Secrets.clientUUID);

  try {
    final success = await manager.init();

    if (success) {
      // Use RookAppleHealthManager functions
    } else {
      // Could not initialize
    }
  } catch (error) {
    // Manage error
  }
}
```

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

### Retrieving health data

To retrieve any type of summary, you need to provide a date. This date cannot be older than the current
day. See the examples below:

| Current date | Provided date | Is valid?                       |
|--------------|---------------|---------------------------------|
| 2023-01-08   | 2023-01-09    | No, the date is from tomorrow   |
| 2023-01-08   | 2023-01-08    | Yes, the date is from today     |
| 2023-01-08   | 2023-01-07    | Yes, the date is from yesterday |
| 2023-01-08   | 2023-01-01    | Yes, the date is 7 days old     |

To get health data, call `get_data_type` and provide a `DateTime` instance of the day you want to retrieve the data
from.

For example, if you want to get yesterday's sleep summary, call `getSleepSummary`. It will return
an `SleepSummary` instance or throw an exception if an error happens
or if there is no sleep data on that day.

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

### Keeping track of the last time a summary was retrieved

We store in preferences the last date data was retrieved

Call `getLastExtractionDate(AHRookDataType rookDataType)` providing a `AHRookDataType`, e.g. if you want to retrieve
the last date a `SleepSummary` was retrieved use `AHRookDataType.SLEEP_SUMMARY`.

It will return a `DateTime` instance.

#### Example

Let's suppose that one of your users opens the app on `2023-01-10`, the app then retrieves a sleep
summary from yesterday (`2023-01-09`) with `getSleepSummary`.

Then the user forgets to open the app until `2023-01-15`, then you'll
call `getLastExtractionDate(AHRookDataType rookDataType)`it will return `2023-01-09` or `2023-01-10` (depending on the
user's timezone) in a DateTime instance. Now, in a loop, you can recover data from the days the user did not open the
app (`2023-01-10` to `2023-01-14`).

An example using sleep summaries is detailed below:

```dart
void recoverLostDays() async {
  const oneDay = Duration(days: 1);

  final now = DateTime.now().subtract(const Duration(days: 1));
  final today = DateTime(now.year, now.month, now.day).toUtc();

  DateTime date = await manager.getLastExtractionDate(AHRookDataType.sleepSummary);

  // date = date.add(oneDay); Not necessary the returned date belongs to the last registry found

  while (date.isBefore(today)) {
    try {
      final sleepSummary = await manager.getSleepSummary(date);

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
