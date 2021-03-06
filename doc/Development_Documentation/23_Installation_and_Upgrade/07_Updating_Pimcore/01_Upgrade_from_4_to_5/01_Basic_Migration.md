# Basic Migration

The following steps are needed for every migration to Pimcore 5 and should be done before continuing migration either
to the Pimcore 4 compatibility bridge or the Symfony Stack.

- **Backup your system!**

- Replace your `composer.json` with [this one](https://github.com/pimcore/skeleton/blob/master/composer.json) and re-add your custom dependencies. 
  
- Run `COMPOSER_MEMORY_LIMIT=3G composer update`
If you encounter errors, please fix them until the command works properly.
You can use `--no-scripts` to install dependencies and then iterate through errors in subsequent calls to save some time.

- The [Pimcore CLI](https://github.com/pimcore/pimcore-cli) provides a set of commands to ease the migration. It is able
  to do the following:

  - extract Pimcore 5 build
  - create several necessary directories
  - move config files to new locations
  - move class files to new location
  - move versions to new location
  - move logs to new location
  - move email logs to new location
  - move assets to new location
  - move website folder to /legacy/website
  - move plugins folder to /legacy/plugins
  - update `system.php` to be ready for Pimcore 5
  
  Just follow [these instructions](https://github.com/pimcore/pimcore-cli/blob/master/doc/pimcore_5_migration.md).
  
- Optionally (if you can't use pimcore-cli from above) a simpler [migration.sh](./migration.sh) script can handle basic file moving for you and can be adapted to your needs
- Refactor `constants.php` and move it to `app/constants.php`
- Refactor `startup.php` and move content either to `AppKernel::boot()` or `AppBundle::boot()`

- Update system configs in `/var/config/system.php` (this will be done automatically by Pimcore CLI)
    - `email` > `method` => if `''`, change to `null`
    - `email` > `smtp` > `ssl` => if `''` change to `null`
    - `email` > `smtp` > `auth` > `method` => if `''` change to `null`
    - `email` > `smtp` > `auth` > `password` => add if not there with value `''`
    - `newsletter` > `method` => if `''` change to `null`
    - `newsletter` > `smtp` > `ssl` => if `''` change to `null`
    - `newsletter` > `smtp` > `auth` > `method` => if `''` change to `null`
    - `newsletter` > `smtp` > `auth` > `password` => add if not there with value `''`

- Change document root of your webserver to `/web` directory - document root must not be project root anymore

- At this point, the basic application should work again. Please try to run `bin/console` to see if the console works

- The [pimcore-migrations-40-to-54.php](https://gist.github.com/brusch/c3e572947a7a7e8523e18e9787cf88c3) script contains
  all migrations which were introduced from Pimcore 5.0 to 5.4 and are needed when migrating from Pimcore 4. 
  To execute the script, use the following command (making a backup at this stage is strongly recommended):
  
  ```bash
  $ wget https://gist.githubusercontent.com/brusch/c3e572947a7a7e8523e18e9787cf88c3/raw/da97304ab59a7220ef41e1c09346a5370dda898c/pimcore-migrations-40-to-54.php -O  pimcore-migrations-40-to-54.php 
  $ php pimcore-migrations-40-to-54.php
  ```
  
- Execute all core migrations from 5.4.x to the latest version, by running the following command: 

  ```bash
  $ ./bin/console pimcore:migrations:migrate -s pimcore_core 
  ```
  
- Run `composer update` once again to update the autoloader and class maps
- The admin interface of your system should now work again and you can proceed to [migrate your application code](./README.md). 
- Update your [.gitignore](https://github.com/pimcore/skeleton/blob/master/.gitignore)
