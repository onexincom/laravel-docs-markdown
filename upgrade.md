# Upgrade Guide
- [Upgrading To 11.0 From 10.x](#upgrade-11.0)

## [High Impact Changes](#high-impact-changes)
- [Updating Dependencies](#updating-dependencies)
- [Updating Minimum Stability](#updating-minimum-stability)

## [Upgrading To 11.0 From 10.x](#upgrade-11.0)
#### [Estimated Upgrade Time: ?? Minutes](#estimated-upgrade-time-??-minutes)
> **Note**  
>  We attempt to document every possible breaking change. Since some of these breaking changes are in obscure parts of the framework only a portion of these changes may actually affect your application. Want to save time? You can use [Laravel Shift](https://laravelshift.com/) to help automate your application upgrades.

### [Updating Dependencies](#updating-dependencies)
**Likelihood Of Impact: High**

#### PHP 8.2.0 Required
Laravel now requires PHP 8.2.0 or greater.

#### Composer Dependencies
You should update the following dependencies in your application's `composer.json` file:

- `laravel/framework` to `^11.0`

