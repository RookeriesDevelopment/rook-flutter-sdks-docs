# Rook Apple Health package

This package enables apps to extract data from Apple Health. **rook_apple_health** is
part
of Rook Extraction, a series of packages dedicated to extracting Health Data from a variety of
[Data Sources](https://docs.tryrook.io/docs/Definitions#data-sources).

## Features

* Check for Apple Health Connect availability.
* Request permissions.
* Retrieve a [Sleep Summary](https://docs.tryrook.io/docs/DataStructure/SleepHealth#summaries) from
  a specific day.
* Retrieve a [Physical Summary](https://docs.tryrook.io/docs/DataStructure/PhysicalHealth#summaries)
  from a specific day.
* Retrieve a [Body Summary](https://docs.tryrook.io/docs/DataStructure/BodyHealth#summaries) from a
  specific day.

## Installation

![Pub Version](https://img.shields.io/pub/v/rook_apple_health?color=%23F44336)

```text
flutter pub add rook_apple_health
```

This package requires flutter `3.3.0` or higher.

### IOS configuration

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

#### Request

There are dedicated functions for
each [Health Pillar](https://docs.tryrook.io/docs/Definitions#health-data-pillars)
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

### Retrieving data

To retrieve any type of summary, you need to provide a date. This date cannot be older than the current
day. See the examples below:

| Current date | Provided date | Is valid?                       |
|--------------|---------------|---------------------------------|
| 2023-01-08   | 2023-01-09    | No, the date is from tomorrow   |
| 2023-01-08   | 2023-01-08    | Yes, the date is from today     |
| 2023-01-08   | 2023-01-07    | Yes, the date is from yesterday |
| 2023-01-08   | 2023-01-01    | Yes, the date is 7 days old     |

To get health data, call `get_data_type` and provide a `DateTime` instance of the day you want to
retrieve the data from.

For example, if you want to get yesterday's sleep summary, call `getSleepSummary`. It will return
an `SleepSummary` instance or throw an exception if an error happens
or if there is no sleep data on that day.

```dart
void getSleepSummary() async {
  try {
    final date = DateTime.now();
    final result = await manager.getSleepSummary(date);

    // Success
  } catch (error) {
    // Manage error
  }
}
```

### Keeping track of the last time a summary was retrieved

We store in preferences the last date data was retrieved from (even if that attempt
resulted in no data being found).

Call `getLastExtractionDate` and provide a `RookDataType`, it will return a `DateTime` instance.

#### Example

Let's suppose that one of your users opens the app on `2023-01-10`, the app then retrieves a sleep
summary from yesterday (`2023-01-09`) with `getSleepSummary`.

Then the user forgets to open the app until `2023-01-15`, then you'll call `getLastExtractionDate`
providing `RookDataType.sleepSummary` it will return `2023-01-09` in a DateTime instance. Now, in a loop, you can
recover data from the days the user did not open the app (`2023-01-10` to `2023-01-14`).

An example using sleep summaries is detailed below:

```dart
DateTime dateOnly(DateTime date) {
  return DateTime(date.year, date.month, date.day);
}

void recoverLostDays() async {
  const oneDay = Duration(days: 1);
  final today = dateOnly(DateTime.now());

  DateTime date = await manager.getLastExtractionDate(
    RookDataType.sleepSummary,
  );

  date = date.add(oneDay);

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