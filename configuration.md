# Configuration

[Installation command](command_install) publishes SleepingOwl Admin config automatically. 
If you want to publish config manually, execute this command:

```bash
$ php artisan vendor:publish --provider="SleepingOwl\Admin\Providers\SleepingOwlServiceProvider" --tag="config"
```

## Configuration parameters

#### `title`

String to display in browser title.

#### `logo`

Logo in top bar panel.

#### `logo_mini`

Logo in top bar panel, when left bar is minimized. 

#### `url_prefix`

Administrative module URL prefix.

Default: `admin`

#### `middleware`

Middleware, restricting unauthorized access to administrative module.

Default: `['web', 'auth']`

#### `auth_provider`

Users authorization provider. See more: https://laravel.com/docs/5.3/authentication#adding-custom-user-providers

Default: `users`

#### `bootstrapDirectory`

SleepingOwl Admin bootstrap directory. 
Put here your module configuration, menu configuration, custom columns and forms. 
Each `.php` file from this directory will be loaded.

Default: `app_path('Admin')`

#### `imagesUploadDirectory`

Path to uploaded images directory. Relative to your `public` directory.

Default: `'images/uploads'`

#### `filesUploadDirectory`

Path to uploaded files directory. Relative to your `public` directory.

Default: `'files/uploads'`

#### `template`

Active template class (should be inherited from `SleepingOwl\Admin\Contracts\TemplateInterface`)

Default: `SleepingOwl\Admin\Templates\TemplateDefault::class`

#### `datetimeFormat`, `dateFormat`, `timeFormat`

Date and time format, used by default in columns and form elements.

Default: `'d.m.Y H:i', 'd.m.Y', 'H:i'`

#### `wysiwyg`

Default rich text editor settings.

#### `datatables`

datatables default settings

#### `breadcrumbs`

On / off breadcrumbs

#### `aliases`

Aliases, initialized by package. 
