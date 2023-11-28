# Changelog

## 0.5.0

* When registering a user the Health Connect data source status will be changed to active.
* Replaced `removeUserFromPreferences` with `deleteUserFromRook`, this function will delete from both server and
  preferences, see [Removing registered users](README.md#removing-registered-users) for more information.
* Changed all `clientPassword` instances to `secretKey`.

## 0.4.0

* Authorization it's now managed by this SDK (dependency on **rook_auth** is no longer required),
  see [Initialization](README.md#initialization) for more information.
* Added `RookUsersEnvironment` to configure internal behaviour, see [Environment](README.md#environment) for
  more information.
* Changed `RookUsersManager` constructor parameters, see [RookUsersManager](README.md#rookusersmanager) for more
  information.

## 0.3.0

* Updated rook_auth to 0.3.0

## 0.2.0

* Updated flutter and dart versions to ">=3.0.0 <4.0.0"
* Updated http to ">=1.0.0 <2.0.0"
* Updated rook_auth to 0.2.0

## 0.1.2

* Register Apple Health users

## 0.1.1

* Fixed auth error

## 0.1.0

* Updated to new API requirements

## 0.0.1

* Initial release.
