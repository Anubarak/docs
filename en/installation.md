Installation Instructions
=========================

- [0. Introduction](#0-introduction)
- [1. Install Composer](#1-install-composer)
- [2. Create a New Craft Project](#2-create-a-new-craft-project)
- [3. Set up the Database](#3-set-up-the-database)
- [4. Set up the Web Server](#4-set-up-the-web-server)

## 0. Introduction

Craft 3 is available as a [Composer] package, and for the duration of the Beta, Composer is the only way to install Craft 3. (We’ll introduce an alternate, non-Composer installation method once it’s out of Beta.) If you’re unfamiliar with Composer, it’s a package manager for PHP, meaning it’s a tool that attempts to make installing and updating PHP libraries (like Craft) a simple terminal command away.

Craft’s Composer support is made up of three parts:

1. **[`craftcms/cms`]** – Composer *package* that contains all of Craft’s source code and bootstrap scripts.
2. **[`craftcms/plugin-installer`]** – Custom Composer *installer* that makes it possible to install Craft plugins with Composer.
2. **[`craftcms/craft`]** – Composer *project* that can be installed as a starting point for new Craft projects, with the `cms` and `plugin-installer` dependencies already in place.

## 1. Install Composer

You should be running at Composer 1.3.0 or later. You can find out which version of Composer you have installed (if any) by opening your terminal running the following command:

    composer -V

If that outputs an error saying `composer` is “not found” or “not recognized”, then Composer isn’t installed. Follow Composer’s instructions to install it:

  - [macOS/Linux/Unix instructions] *(install it globally)*
  - [Windows instructions]

If it outputs a version number, but it’s less than `1.3.0`, run the following command to update it:

    composer self-update

## 2. Create a New Craft Project

To create a new Craft project, simply run this command (substituting `PATH` with the path the project should be created at):

    composer create-project craftcms/craft PATH -s beta

> {tip} If Composer complains that your system doesn’t have PHP 7 installed, but you know it’s not an issue because Craft will run with a different PHP install (e.g. through MAMP or Vagrant), use the `--ignore-platform-reqs` flag.

Composer will take a few minutes to install everything, so this would be a great time to make yourself some coffee or a cocktail.

Once it’s finished, your project directory should have a file structure like this:

```
config/...
storage/
templates/
vendor/...
web/...
.env
.env.example
composer.json
craft
craft.bat
LICENSE.md
README.md
```

See [Directory Structure](directory-structure.md) for a rundown of what each of those directories and files are for.

## 3. Set up the Database

Next up, you’ll need to create a database for your Craft project. Craft 3 supports both MySQL 5.5+ and PostgreSQL 9.5+.

If you’re given a choice, we recommend the following database settings in most cases:

- **MySQL**
  - Default Character Set: `utf8`
  - Default Collation: `utf8_unicode_ci`

- **PostgreSQL**
  - Character Set: `UTF8`

Once the database is created, you’ll need to configure your `.env` file with with its connection settings. You can either edit the file manually, or run the `./craft setup` command from the root project directory in your terminal.

> {tip} That `.env` file will be processed via [PHP dotenv], which the `craftcms/craft` project comes with preinstalled. The advantage of using PHP dotenv is that it offers a place to store sensitive information (like database connection settings) in a file that doesn’t get committed to your Git repository.

## 4. Set up the Web Server

Create a new web server to host your Craft project. Its document root should point to the `web/` folder.

If you’re not using MAMP, you will probably need to update your `hosts` file, so your computer knows to route requests to your chosen host name to the local computer.

- **macOS/Linux/Unix:** `/etc/hosts`
- **Windows:** `\Windows\System32\drivers\etc\hosts`

You can test whether everything is set up correctly by pointing your web browser to `http://HOSTNAME/index.php?p=admin` (substituting `HOSTNAME` with your new web server’s host name). You should get the Craft installation wizard, which will take you through a couple setup screens, and then perform the actual installation.


[Composer]: https://getcomposer.org/
[`craftcms/cms`]: https://github.com/craftcms/cms
[`craftcms/plugin-installer`]: https://github.com/craftcms/plugin-installer
[`craftcms/craft`]: https://github.com/craftcms/craft
[Composer installer]: https://getcomposer.org/doc/articles/custom-installers.md
[project]: https://github.com/craftcms/craft
[macOS/Linux/Unix instructions]: https://getcomposer.org/doc/00-intro.md#installation-linux-unix-osx
[Windows instructions]: https://getcomposer.org/doc/00-intro.md#installation-windows
[PHP dotenv]: https://github.com/vlucas/phpdotenv
