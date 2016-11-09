# Generic description

System starts work when you bootstrap `SleepingOwl\Admin\Providers\SleepingOwlServiceProvider` in your Laravel application

```php
// config/app.php
...
'providers' => [
    ...

    /*
    * SleepingOwl Service Provider
    */
    SleepingOwl\Admin\Providers\SleepingOwlServiceProvider::class,

    /*
    * Application Service Providers...
    */
    App\Providers\AppServiceProvider::class,
...
],
```

After bootstrap `SleepingOwl` registers its components:

- Load config sleeping_owl.php
- Register view templates with namespace `sleeping_owl::`
- Register language packs with namespace `sleeping_owl::`
- Register media assets from file https://github.com/LaravelRUS/SleepingOwlAdmin/blob/master/resources/assets.php
- Publish media libraries in `public/packages/sleepingowl`
- Register service providers from `sleeping_owl` config (this allows you not to register lots of them in main application,
  and you always can change that set)
- Register facades from `sleeping_owl` config
- Register console commands

If you want to replace system template, then you should place it in `resources\views\vendor\sleeping_owl\default\`, i.e.
If system has template in `sleepingowl\src\resources\default\display\extensions\columns.blade.php`, 
then replacing template should be in `resources\views\vendor\sleeping_owl\default\display\extensions\columns.blade.php`

### Registered service containers:

- AdminSection - `sleeping_owl` - `SleepingOwl\Admin\Admin`
- AdminTemplate - `sleeping_owl.template` - `SleepingOwl\Admin\Templates\TemplateDefault`
- AdminNavigation - `sleeping_owl.navigation` - `SleepingOwl\Admin\Navigation`
- WysiwygManager - `sleeping_owl.wysiwyg` - `SleepingOwl\Admin\Wysiwyg\Manager`

Also to work with data more conveniently, field, tables, forms etc. classes are grouped 
(https://github.com/LaravelRUS/SleepingOwlAdmin/blob/master/src/Providers/AliasesServiceProvider.php),
each group is accessible via service container too 

- AdminColumnFilter - `sleeping_owl.column_filter`
- AdminDisplay - `sleeping_owl.display`
- AdminColumn - `sleeping_owl.table.column`
- AdminColumnEditable - `sleeping_owl.table.column.editable`
- AdminDisplayFilter - `sleeping_owl.display.filter`
- AdminForm - `sleeping_owl.form`
- AdminFormElement - `sleeping_owl.form.element`

**If you want to add new field type, for example, you should do following**

```php
\AdminColumn::add('field_type', \App\Fields\CustomField::class);
// or
app('sleeping_owl.table.column')->add('field_type', \App\Fields\CustomField::class);

// after that field will be available like this

\AdminColumn::field_type()->...
// or
app('sleeping_owl.table.column')->field_type()->...
```

### Files loading

After all components registration system looks for files in `app/Admin` and loads them.
- First loads `bootstrap.php`
- Then loads modules configuration

**After method `boot` is executed in all service providers, system will execute**
*(Execution happens upon `app()->booted` event in order to allow you register models not only in `app/Admin`, 
folder, but with your service providers too)*:
- Load `app/Admin/routes.php` *(if file exists)*
- Register system routes
- Load `app/Admin/navigation.php` *(if file exists)*

!!! **After registration of system routes, creating new sections may cause errors, if aliases are used** !!!
