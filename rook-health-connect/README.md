# Rook Health Connect package

This package enables apps to extract data from Google Health Connect. **rook_health_connect** is
part
of Rook Extraction, a series of packages dedicated to extracting Health Data from a variety of
[Data Sources](https://docs.tryrook.io/docs/Definitions#data-sources).

* This package was developed with the Health
  Connect [native SDK](https://developer.android.com/guide/health-and-fitness/health-connect), so
  all its limitations and requirements apply to this package as well.

## Features

* Check for Health Connect APK availability.
* Check and request permissions.
* Retrieve health data:
    * [Sleep Summary](https://docs.tryrook.io/docs/DataStructure/SleepHealth#summaries)
    * [Physical Summary](https://docs.tryrook.io/docs/DataStructure/PhysicalHealth#summaries)
    * Physical Event
    * [Body Summary](https://docs.tryrook.io/docs/DataStructure/BodyHealth#summaries)
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

## Installation

![Pub Version](https://img.shields.io/pub/v/rook_health_connect?style=for-the-badge&logo=flutter&color=7200F7)

```text
flutter pub add rook_health_connect
```

### Environment

This package was developed with the following sdk constraints:

* dart: ">=3.0.0 <4.0.0"
* flutter: ">=3.0.0"

### Required dependencies

To use this package you'll need to add the following dependencies to your project:

* [equatable](https://pub.dev/packages/equatable): ">=2.0.0 <3.0.0"

## Getting started

To get authorization to use this package, you'll need to install and configure
the [rook-auth](https://pub.dev/packages/rook_auth) package.

### Android configuration

In your **AndroidManifest.xml** add a query for Health Connect

```xml

<manifest>
    <queries>
        <package android:name="com.google.android.apps.healthdata"/>
    </queries>
</manifest>
```

Then declare the health permissions used by this package:

```text
<uses-permission android:name="android.permission.health.READ_SLEEP" />
<uses-permission android:name="android.permission.health.READ_STEPS" />
<uses-permission android:name="android.permission.health.READ_DISTANCE" />
<uses-permission android:name="android.permission.health.READ_FLOORS_CLIMBED" />
<uses-permission android:name="android.permission.health.READ_ELEVATION_GAINED" />
<uses-permission android:name="android.permission.health.READ_OXYGEN_SATURATION" />
<uses-permission android:name="android.permission.health.READ_VO2_MAX" />
<uses-permission android:name="android.permission.health.READ_TOTAL_CALORIES_BURNED" />
<uses-permission android:name="android.permission.health.READ_ACTIVE_CALORIES_BURNED" />
<uses-permission android:name="android.permission.health.READ_HEART_RATE" />
<uses-permission android:name="android.permission.health.READ_RESTING_HEART_RATE" />
<uses-permission android:name="android.permission.health.READ_HEART_RATE_VARIABILITY" />
<uses-permission android:name="android.permission.health.READ_EXERCISE" />
<uses-permission android:name="android.permission.health.READ_SPEED" />
<uses-permission android:name="android.permission.health.READ_WEIGHT" />
<uses-permission android:name="android.permission.health.READ_HEIGHT" />
<uses-permission android:name="android.permission.health.READ_BLOOD_GLUCOSE" />
<uses-permission android:name="android.permission.health.READ_BLOOD_PRESSURE" />
<uses-permission android:name="android.permission.health.READ_HYDRATION" />
<uses-permission android:name="android.permission.health.READ_BODY_TEMPERATURE" />
<uses-permission android:name="android.permission.health.READ_RESPIRATORY_RATE" />
<uses-permission android:name="android.permission.health.READ_NUTRITION" />
<uses-permission android:name="android.permission.health.READ_MENSTRUATION" />
<uses-permission android:name="android.permission.health.READ_POWER" />
```

Finally, inside your MainActivity declaration add an intent filter for the Health Connect
permissions action:

```xml

<activity android:name=".MainActivity">
    <intent-filter>
        <action android:name="androidx.health.ACTION_SHOW_PERMISSIONS_RATIONALE"/>
    </intent-filter>
</activity>
```

In your build.gradle (app) set your min and target sdk version like below:

```groovy
minSdk 26
targetSdk 33
```

* This package will only work with devices of a minSdk 28 or later. The `minSdk 26` is to keep
  compatibility with other Rook packages that can be used with older package.

In the app folder create a **proguard-rules.pro** file and add the following:

```text
-keep class * extends com.google.protobuf.GeneratedMessageLite { *; }
```

Finally, in your build.gradle (app) add the `proguard-rules.pro` from previous step:

```groovy
buildTypes {
    release {
        proguardFiles getDefaultProguardFile('proguard-android-optimize.txt'), 'proguard-rules.pro'
    }
}
```

## Usage

Import rook_health_connect:

```dart
import 'package:rook_health_connect/rook_health_connect.dart';
```

Create an instance of `RookHealthConnectManager`:

```dart

final RookHealthConnectManager manager = RookHealthConnectManager();
```

**WARNING:**

* Before calling any method on `manager`, check [availability](#availability).

### Privacy policy

Health Connect requires a privacy policy where you inform your users how you will handle and use their data.

In the [android configuration](#android-configuration) section an intent filter was added to listen
when your app is launched from said intent you can code
your [own](https://docs.flutter.dev/get-started/flutter-for/android-devs#how-do-i-handle-incoming-intents-from-external-applications-in-flutter)
implementation from scratch or use a package
like [receive_intent](https://pub.dev/packages/receive_intent). You can find an example of the
second approach in our demo app.

### Availability

Before using any of the features of *rook-health-connect*, you need to ensure the user's device is compatible with
Health
Connect and check if the [APK](https://play.google.com/store/apps/details?id=com.google.android.apps.healthdata) is
installed.

Call `checkAvailability` on `RookHealthConnectAvailability`, this will return an `AvailabilityStatus`.

| Status       | Description                                 | What to do                                      |
|--------------|---------------------------------------------|-------------------------------------------------|
| installed    | APK is installed                            | Proceed to check permissions                    |
| notInstalled | APK is not installed                        | Prompt the user to install Health Connect.      |
| notSupported | This device does not support Health Connect | Take the user out of the Health Connect section |

### Permissions

#### Check permissions

There are dedicated functions for each [Health Pillar](https://docs.tryrook.io/docs/Definitions#health-data-pillars)
(Sleep, Physical, and Body) to check permissions. These functions follow the convention: `has_data_type_Permissions`

You can also call `hasAllPermissions` to check if your app has all the permissions required to
extract data from all health pillars.

```dart
void checkPermissions() async {
  final result = await manager.hasAllPermissions();

  if (result) {
    // The app has permissions.
  } else {
    // The app does not have permissions.
  }
}
```

#### Request permissions

There are dedicated functions for each [Health Pillar](https://docs.tryrook.io/docs/Definitions#health-data-pillars)
(Sleep, Physical and Body) to request permissions. These functions follow the
convention: `request_data_type_Permissions`

You can also call `requestAllPermissions` to request all health pillar permissions.

```dart
void requestPermissions() async {
  final result = await manager.requestAllPermissions();

  if (result) {
    // A request to Health Connect to open the permissions screen was send.
    // Health Connect can receive the request but refuse to open the permissions screen, in those cases 'result' will also be true
  } else {
    // The request was not send.
  }
}
```

**Permissions denied**

If the user clicks cancel or navigates away from the permissions screen, Health Connect will take it as if the user
denied the permissions.

If the user 'denies' the permissions 2 times, your app will be blocked by Health Connect. This is
permanent and cannot be undone even if the user uninstalls your app.

When your app is blocked, any permissions request will be ignored.

To solve this problem, we recommend you also include an `Open Health Connect` button in your
permissions UI. This button will call `openHealthConnectSettings`, there your users can manually
grant permissions to your app.

```dart
void openHealthConnectSettings() async {
  final result = await manager.openHealthConnectSettings();

  if (result) {
    // A request to open Health Connect was send.
  } else {
    // The request was not send.
  }
}
```

### Retrieving health data

### Retrieving health data

There are 2 types of health data **Summaries** and **Events**.

| Health Data | Timezone (Input / Output) | Oldest date of retrieval | Soonest date of retrieval |
|-------------|---------------------------|--------------------------|---------------------------|
| Summary     | UTC                       | 29 days                  | Yesterday                 |
| Event       | UTC                       | 29 days                  | Today                     |

#### Retrieving health data in UTC

All health data types require a DateTime instance if the date (summary) or datetime (event) you want to retrieve
data from. When using `get_health_data_type(DateTime date)` the provided `date` is used to create a range
between `date` (start) and the end of the day (end). This can become tricky specially with
all the different timezones your users may be in.

For that reason we **only allow UTC when retrieving data**, follow the following examples:

##### Retrieve health data for the whole day of yesterday

Today is **2023-05-26** in Mexico City (**UTC-6**)

To retrieve health data for the whole day of yesterday (**2023-05-25**) call `get_health_data_type(DateTime date)`
where date is obtained with:

```dart

final now = DateTime.now() // Today 2023-05-26T15:12:20
    .subtract(const Duration(days: 1)); // Yesterday 2023-05-25T15:12:20

final date = DateTime(now.year, now.month, now.day) // Yesterday 2023-05-25T00:00:00
    .toUtc(); // Yesterday 2023-05-25T06:00:00Z
```

As you can see we started obtaining **today's** then subtracted one day to get **yesterday's**, and because we want
to cover the whole day, all fields after `day` were truncated to zero.

Finally, the date was converted to UTC resulting in **2023-05-25T06:00:00Z** it has **06:00:00** because
**2023-05-25T00:00:00** in Mexico City is **2023-05-25T06:00:00Z** in UTC.

Internally the SDK will perform the following operations:

1. Use 2023-05-25T06:00:00Z as `start`
2. Create the range between `start` and the end of the day `start` belongs to.

So the time range will end up like:

`start`: 2023-05-25T06:00:00Z < --- >`end`: 2023-05-26T06:00:00Z

Which in **Mexico City (UTC-6)** is equivalent to:

`start`: 2023-05-25T00:00:00 < --- >`end`: 2023-05-26T00:00:00

##### Retrieve health data for the whole day of today

Today is **2023-05-26** in Mexico City (**UTC-6**)

To retrieve health data for the whole day of today (**2023-05-26**) call `get_health_data_type(DateTime date)`
where date is obtained with:

```dart

final now = DateTime.now(); // Today 2023-05-26T15:12:20
final date = DateTime(now.year, now.month, now.day) // Today 2023-05-26T00:00:00
    .toUtc(); // Today 2023-05-26T06:00:00Z
```

As you can see we started obtaining **today's**, and because we want health data from the entire day, all fields
after `day` were truncated to zero.

Finally, the date was converted to UTC resulting in **2023-05-26T00:00:00Z** it has **06:00:00** because
**2023-05-26T00:00:00** in Mexico City is **2023-05-26T06:00:00Z** in UTC.

Internally the SDK will perform the following operations:

1. Use 2023-05-26T06:00:00Z as `start`
2. Create the range between `start` and the end of the day `start` belongs to.

So the time range will end up like:

`start`: 2023-05-26T06:00:00Z < --- >`end`: 2023-05-27T06:00:00Z

Which in **Mexico City (UTC-6)** is equivalent to:

`start`: 2023-05-26T00:00:00 < --- >`end`: 2023-05-27T00:00:00

##### Retrieve health data for the rest of the day of today

Today is **2023-05-26** in Mexico City (**UTC-6**)

To retrieve health data for the rest of the day of today (**2023-05-26**)
call `get_health_data_type(DateTime date)` where date is obtained with:

```dart

final now = DateTime.now() // Today 2023-05-26T12:00:00
    .toUtc(); // Today 2023-05-26T06:00:00Z
```

In this case the date was only converted to UTC resulting in **2023-05-26T18:00:00Z** it has **18:00:00** because
**2023-05-26T12:00:00** in Mexico City is **2023-05-26T18:00:00Z** in UTC.

Internally the SDK will perform the following operations:

1. Use 2023-05-26T18:00:00Z as `start`
2. Create the range between `start` and the end of the day `start` belongs to

So the time range will end up like:

`start`: 2023-05-26T18:00:00Z < --- >`end`: 2023-05-27T06:00:00Z

Which in **Mexico City (UTC-6)** is equivalent to:

`start`: 2023-05-26T12:00:00 < --- >`end`: 2023-05-27T00:00:00

#### Retrieve summaries

To retrieve any type of summary, you need to provide a date. This date cannot be the current
day and cannot be older than 29 days. See the examples below:

| Current date | Provided date | Is valid?                          |
|--------------|---------------|------------------------------------|
| 2023-01-08   | 2023-01-08    | No, the date is from today         |
| 2023-01-08   | 2023-01-07    | Yes, the date is from yesterday    |
| 2023-01-08   | 2022-11-01    | No, the date is older than 29 days |
| 2023-01-08   | 2023-01-01    | Yes, the date is 7 days old        |

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
* If the stored date is older than 29 days it will be ignored it and a DateNotFoundException will be thrown.
* If your users are more likely to change locations (timezones), you may have to instead convert the result
  of `getLastExtractionDate(HCRookDataType rookDataType)` to your user's new timezone, add one day, truncate (if
  necessary) and convert again to UTC.

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

To retrieve any type of event, you need to provide a date. This date cannot be older than 29 days. See the examples
below:

| Current date | Provided date | Is valid?                          |
|--------------|---------------|------------------------------------|
| 2023-01-08   | 2023-01-08    | Yes, the date is from today        |
| 2023-01-08   | 2023-01-07    | Yes, the date is from yesterday    |
| 2023-01-08   | 2022-11-01    | No, the date is older than 29 days |
| 2023-01-08   | 2023-01-01    | Yes, the date is 7 days old        |

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
* If the stored date is older than 29 days it will be ignored it and a DateNotFoundException will be thrown.
* If your users are more likely to change locations (timezones), you may have to instead convert the result
  of `getLastExtractionDate(HCRookDataType rookDataType)` to your user's new timezone, add one day, truncate (if
  necessary) and convert again to UTC.

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

* See a complete list of `RookHealthConnectManager` methods in
  the [API Reference](https://pub.dev/documentation/rook_health_connect/latest/rook_health_connect/RookHealthConnectManager-class.html)
