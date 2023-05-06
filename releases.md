# Release Notes
- [Versioning Scheme](#versioning-scheme)
- [Support Policy](#support-policy)
- [Laravel 11](#laravel-11)

## [Versioning Scheme](#versioning-scheme)
Laravel and its other first-party packages follow [Semantic Versioning](https://semver.org). Major framework releases are released every year (~Q1), while minor and patch releases may be released as often as every week. Minor and patch releases should **never** contain breaking changes.

When referencing the Laravel framework or its components from your application or package, you should always use a version constraint such as `^11.0`, since major releases of Laravel do include breaking changes. However, we strive to always ensure you may update to a new major release in one day or less.

#### [Named Arguments](#named-arguments)
[Named arguments](https://www.php.net/manual/en/functions.arguments.php#functions.named-arguments) are not covered by Laravel's backwards compatibility guidelines. We may choose to rename function arguments when necessary in order to improve the Laravel codebase. Therefore, using named arguments when calling Laravel methods should be done cautiously and with the understanding that the parameter names may change in the future.

## [Support Policy](#support-policy)
For all Laravel releases, bug fixes are provided for 18 months and security fixes are provided for 2 years. For all additional libraries, including Lumen, only the latest major release receives bug fixes. In addition, please review the database versions [supported by Laravel](/docs/master/database#introduction).

  
| Version | PHP (*) | Release | Bug Fixes Until | Security Fixes Until |   
| ---- | ---- | ---- | ---- | ---- |   
| 9 | 8.0 - 8.2 | February 8th, 2022 | August 8th, 2023 | February 6th, 2024 |   
| 10 | 8.1 - 8.2 | Q1 2023 | August 6th, 2024 | February 4th, 2025 |   
| 11 | 8.2 | Q1 2024 | August 5th, 2025 | February 3rd, 2026 | End of life Security fixes only (*) Supported PHP versions

## [Laravel 11](#laravel-11)
To be determined...

### [PHP 8.2](#php-8)
Laravel 11.x requires a minimum PHP version of 8.2.

