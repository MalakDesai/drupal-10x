# Drupal 10 Development
A minimal project build for Drupal 10.

## Contents

* [Getting started](#getting-started)

## Getting started

### Requirements
This project requires [DDEV](https://ddev.readthedocs.io/en/latest/users/install/) and [Composer 2](https://getcomposer.org/doc/00-intro.md#installation-linux-unix-macos) be installed before you begin.

### Setup

Checkout the project and run the following commands from project root:

* `ddev start`
* `composer install`
* `ddev auth ssh`

Now you are ready to install Drupal and test modules:

* `ddev install`

This command will install Drupal 10 using the `standard` profile and configures the development environment with:
- **Admin account**: `admin` / `admin`
- **Admin theme**: Claro

You can login with `admin` / `admin` at https://drupal-10x.ddev.site/user.

### Drush

`drush 13` is installed by default and can be run with `ddev drush COMMAND`. You can use `ddev drush site:install` if you want to customize the install.

### Composer

The use of `composer require` is assumed to be used for maintenance of the core framework, not adding modules for testing.

You can use `composer require` to add modules that you need -- and to check dependencies. Do not commit those additions to the project.

### Install locations

Modules are installed to `web/modules/contrib`, which is leveraged by our [common ddev tasks](#common-tasks).

