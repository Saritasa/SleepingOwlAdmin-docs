# Installation

Use this command for initial SleepingOwl Admin configuration. It will create all necessary files and directories.

## Use

```bash
$ php artisan sleepingowl:install
```

## What it does

 - Publish SleepingOwl Admin config.
 - Publish assets from SleepingOwl Admin to `public/packages/sleepingowl/default`.
   ```bash
   $ php artisan vendor:publish --tag=assets --force
   ```
   
 - Create bootstrap directory (by default `app/Admin`).
 - Create default menu configuration file. (by default `app/Admin/navigation.php`)
 - Create default bootstrap file. (by default `app/Admin/bootstrap.php`)
 - Create default routes file. (Поby default `app/Admin/routes.php`)
 - Create public directory structure (*создает директорию `images/uploads`*)
 - Create [Service Provider](model_configuration_section.md) `app\Providers\AdminSectionsServiceProvider` 

---

# Обновление

Package update should be performed by composer. 

**Sometimes updates contain assets, which requires to delete** 
`public/packages/sleepingowl/default`.
**It's not recommended to store custom scripts in this directory.** 
