# Data display types


## Table
Tables are used to display list of section documents.

At present moment following list display types are supported:
 - `AdminDisplay::table()` - ordinary table (`SleepingOwl\Admin\Display\DisplayTable`)
 - `AdminDisplay::datatables()` - table data output, using plugin https://datatables.net/ (`SleepingOwl\Admin\Display\DisplayDatatables`)
 - `AdminDisplay::datatablesAsync()` (`SleepingOwl\Admin\Display\DisplayDatatablesAsync`)
 - `AdminDisplay::tree()` - output data as tree (`SleepingOwl\Admin\Display\DisplayTree`)

**Usage example:**
```php
$model->onDisplay(function () {
    $display = AdminDisplay::table();
    
    ...
    
    return $display;
});
```

### Configure columns (Extension)
`SleepingOwl\Admin\Display\Extension\Columns`

You can read [here](columns.md) about column types and their descriptions.

```php
$display->setColumns([
  ...
  AdminColumn::link('title')->setLabel('Title'),
  AdminColumn::datetime('created_at')->setLabel('Created')->setFormat('d.m.Y'),
  ...
]);

// or 

$display->getColumns()->push(
  AdminColumn::text('title')
);
```

### Column filters (Extension)
You can read [here](columnfilters.md) about column filter types.
`SleepingOwl\Admin\Display\Extension\ColumnFilters`

```php
$display->setColumnFilters([
  ...
  AdminColumnFilter::text()->setPlaceholder('Full Name'),
  ...
]);

// or 

$display->getColumnFilters()->push(
  AdminColumnFilter::text()->setPlaceholder('Full Name')
);
```

### Eager Loading
Optimized DB queries, in case you use related models. [See more](https://laravel.com/docs/5.3/eloquent-relationships#eager-loading)

```php
$display->with('country', 'companies');
```

### Query modification (Extension)
`SleepingOwl\Admin\Display\Extension\Apply`

You can modify query, if you need

```php
$display->setApply(function ($query) {
    $query->orderBy('title', 'asc');
});

// or 

$display->setApply([
  ...
  function ($query) {
      $query->orderBy('title', 'asc');
  },
  ...
]);

// or

$display->getApply()->push(function ($query) {
    $query->orderBy('title', 'asc');
});
```

### Applying scopes (Extension)
`SleepingOwl\Admin\Display\Extension\Scopes`

You can apply [Eloquent scopes](https://laravel.com/docs/5.3/eloquent#query-scopes) to data output:

```php
$display->setScopes('last');

//or

$display->setScopes(['last', 'trashed']);

// or

$display->getScopes()->push('last');
```

### Document Actions (Extension)
`SleepingOwl\Admin\Display\Extension\Actions`

Use [Action](columns.md#action) column type

```php
$table = AdminDisplay::table()
    ->setActions([
        AdminColumn::action('export', 'Export')->setIcon('fa fa-share')->setAction(route('news.export')),
    ])
    ->setColumns([
        AdminColumn::checkbox(),
        ...
    ]);

// Change buttons placement on page
$table->getActions()
    ->setPlacement('panel.buttons')
    ->setHtmlAttribute('class', 'pull-right');
```

# Api

Table classes use traits:
 - [HtmlAttributes](html_attributes.md), to configure HTML attributes.
 - [Assets](assets_trait.md), to include assets.

## Methods available in all types

#### extend
Add new extension to view

    SleepingOwl\Admin\Display\Display::extend(string $name, \SleepingOwl\Admin\Contracts\Display\DisplayExtensionInterface $extension): return $extension

#### getExtensions
Get list of all extensions

    SleepingOwl\Admin\Display\Display::getExtensions() return \Illuminate\Support\Collection|array<mixed,\SleepingOwl\Admin\Contracts\Display\DisplayExtensionInterface>
    
#### with
[Eager loading](https://laravel.com/docs/5.3/eloquent-relationships#eager-loading)

    SleepingOwl\Admin\Display\Display::with(array|array<mixed,string> $relations): return self
    
#### setTitle
Set title for table

    SleepingOwl\Admin\Display\Display::setTitle(string $title): return self
    
#### setView
Change data output template

    SleepingOwl\Admin\Display\Display::setView(string|\Illuminate\View\View $view): return self
    
```php
$table->setView(view('display.custom.table.view'));

// Will search in path sleeping_owl::default.display.custom.table.view
$table->setView('display.custom.table.view');
```

#### getActions
Get extension object

    SleepingOwl\Admin\Display\Display::getActions(): return SleepingOwl\Admin\Display\Extension\Actions

```php
$actions = $table->getActions();

$actions->setHtmlAttribite('class', 'custm-class');

$actions->push(AdminColumn::action());

// Set columns placement
$actions->setPlacement(...);
```

#### setActions

    SleepingOwl\Admin\Display\Display::setActions(array|...SleepingOwl\Admin\Contracts\ActionInterface): return self


#### getApply
Get extension object

    SleepingOwl\Admin\Display\Display::getApply(): return SleepingOwl\Admin\Display\Extension\Apply
    
#### setApply

    SleepingOwl\Admin\Display\Display::setApply(array|...\Closure): return self
    

#### getScopes
Get extension object

    SleepingOwl\Admin\Display\Display::getScopes(): return SleepingOwl\Admin\Display\Extension\Scopes
    
#### setScopes

    SleepingOwl\Admin\Display\Display::setScopes(array|...(string|array)): return self

#### getFilters
Get extension object

    SleepingOwl\Admin\Display\Display::getFilters(): return SleepingOwl\Admin\Display\Extension\Filters
    
#### setFilters

    SleepingOwl\Admin\Display\Display::setFilters(array|...SleepingOwl\Admin\Contracts\FilterInterface): return self
    
#### setRepositoryClass
Chane repository class. (Class must implement interface `SleepingOwl\Admin\Contracts\RepositoryInterface`)

    SleepingOwl\Admin\Display\Display::setRepositoryClass(string $repository): return self
    
    
## table()
`SleepingOwl\Admin\Display\DisplayTable`

#### setParameters
Pass additional parameters to URL, invoked by new record button creation.

    SleepingOwl\Admin\Display\DisplayTable::setParameters(array $parameters): return self
    
#### setParameter
Pass additional parameter to URL, invoked by new record button creation.

    SleepingOwl\Admin\Display\DisplayTable::setParameter(string $key, mixed $value): return self
    
#### paginate
Output list with pagination, set pagination parameters

    SleepingOwl\Admin\Display\DisplayTable::paginate(integer $perPage, string $pageName): return self
    
```php
$table->paginate(20, 'custom_page');
```
    
#### disablePagination
Disable paginated output

    SleepingOwl\Admin\Display\DisplayTable::disablePagination(): return self

#### usePagination
Check, if pagination is used.

    SleepingOwl\Admin\Display\DisplayTable::usePagination(): return boolean
    
#### getColumns
Get extension object

    SleepingOwl\Admin\Display\DisplayTable::getColumns(): return SleepingOwl\Admin\Display\Extension\Columns

```php
$columns = $table->getColumns();

$columns->push(AdminColumn::text());

$columns->setControlColumn(new \App\Display\Columns\CustomControlColumn());

$columns->setView(...);
```
    
#### setColumns
Set columns for table output. Argument can be array or `SleepingOwl\Admin\Contracts\ColumnInterface`

    SleepingOwl\Admin\Display\DisplayTable::setColumns(array|...SleepingOwl\Admin\Contracts\ColumnInterface): return self
    
```php
$table->setColumns(AdminColumn::text(...), AdminColumn::datetime(..));


$table->setColumns([
    AdminColumn::text(), 
    AdminColumn::datetime(..)
]);
```
    
#### getColumnFilters
Get extension object

    SleepingOwl\Admin\Display\DisplayTable::getColumnFilters(): return SleepingOwl\Admin\Display\Extension\ColumnFilters
    
```php
$filters = $table->geColumnFilters();

$filters->push(AdminColumnFilter::text());

$filters->setPlacement(...);
```

#### setColumnFilters

    SleepingOwl\Admin\Display\DisplayTable::setColumnFilters(array|...SleepingOwl\Admin\Contracts\ColumnFilterInterface): return self
    
## datatables()
`SleepingOwl\Admin\Display\DisplayDatatables`

#### setDatatableAttributes
Set parameters for datatable

    SleepingOwl\Admin\Display\DisplayDatatables::setDatatableAttributes(array $datatableAttributes): return self
    
#### setOrder
Set dataset sort order. https://datatables.net/examples/basic_init/table_sorting.html

    SleepingOwl\Admin\Display\DisplayDatatables::setOrder(array $order): return self
    
```php
$display->setOrder([[1, 'asc']]);
```

## datatablesAsync()
`SleepingOwl\Admin\Display\DisplayDatatablesAsync`


#### setName

    SleepingOwl\Admin\Display\DisplayDatatablesAsync::setName(string $name): return self
    
#### setDistinct

    SleepingOwl\Admin\Display\DisplayDatatablesAsync::setDistinct(boolean $distinct): return self

## tree()
`SleepingOwl\Admin\Display\DisplayTree`

Used to output data as tree with sort support (drag & drop). 
This view supports с https://github.com/etrepat/baum, https://github.com/lazychaser/laravel-nestedset, 
And the simplest variant, when tree is built using `parent_id` (Table also should have `order` field for sorting).

This class registers route `admin.display.tree.reorder`, which is responsible for data sorting. 
You can redefine this route in `app/Admin/routes.php`.

```php
Route::post('{adminModel}/reorder', ['as' => 'admin.display.tree.reorder', function (ModelConfigurationInterface $model) {
    if ($model->getClass() == 'App\Post') {
        // ...
    } else {
        $model->fireDisplay()->getRepository()->reorder(
              Request::input('data')
        );
   }
}]);
```

**If tree is nested inside tab, sorting can work incorrectly**

#### setValue
Set documents title

    SleepingOwl\Admin\Display\DisplayTree::setValue(string $value): return self
    
#### setParentField
Set parent field

    SleepingOwl\Admin\Display\DisplayTree::setParentField(string $parentField): return self
    
#### setOrderField
Set order field

    SleepingOwl\Admin\Display\DisplayTree::setOrderField(string $orderField): return self

#### setRootParentId

    SleepingOwl\Admin\Display\DisplayTree::setRootParentId(null|string $rootParentId): return self
    
#### setParameters
Pass additional parameters to URL, invoked by new record creation button.

    SleepingOwl\Admin\Display\DisplayTree::setParameters(array $parameters): return self
    
#### setParameter
Pass additional parameter to URL, invoked by new record creation button.

    SleepingOwl\Admin\Display\DisplayTree::setParameter(string $key, mixed $value): return self
    
#### setReorderable

    SleepingOwl\Admin\Display\DisplayTree::setReorderable(boolean $reorderable): return self
    
#### getTree
Get extension object (not used now)

    SleepingOwl\Admin\Display\DisplayTree::getTree(): return SleepingOwl\Admin\Display\Extension\Tree
    
#### setRepositoryClass
Change repository class. (Class must implement interface `SleepingOwl\Admin\Contracts\TreeRepositoryInterface`)

    SleepingOwl\Admin\Display\DisplayTree::setRepositoryClass(string $repository): return self
    
# Extending tables
Class `SleepingOwl\Admin\Display\Display` inherited by all classes, 
implementing data output, allows extend its behaviour with extensions. 
Extensions can affect data retrieving, by modifying query builder before executing DB query,
or modify HTML data output.

Now table data output class entirely relies upon extensions, in particular, when you call `setColumns` 
or `getColumns`, `setColumnFilters` or `getColumnFilters` you call extension classes.

#### Usage:
You can use extensions with new `Display` type, as well as with existing types.

**If you want extensions to work in your display, you must inherit `SleepingOwl\Admin\Display\Display`. 
Extension class must be inherited from `SleepingOwl\Admin\Contracts\Display\DisplayExtensionInterface`**

**New class example:**

```php
// New display class
namespace App\Display;

class CustomDisplay extends \SleepingOwl\Admin\Display\Display 
{
    /**
     * Display constructor.
     */
    public function __construct()
    {
        parent::__construct();

        $this->extend('custom_extension', new \App\Display\Extension\CustomExtension());
    }
}
```

**Example of using extension with existing type:**

```php
$display = AdminDisplay::table();
$display->extend('custom_extension', new \App\Display\Extension\CustomExtension());
```

After initialization you can call it this way:

```php
// Get extension object
$table->getCustomExtension()->...

// If class implements method set(), you can pass data to it
$table->setCustomExtension(...)
```

Extensions can be of two types:
 - Modify query 
 - Modify HTML code template in Display
    - If you implement interface `Illuminate\Contracts\Support\Renderable` output will be rendered in common extensions stack
    - If you implement interface `SleepingOwl\Admin\Contracts\Display\Placable` output will be rendered in place, defined by `getPlacement` method

### Modify query
By default any extension can modify query, executed by Display class before data output.
Before query executing it calls each extension method `modifyQuery` and passes `\Illuminate\Database\Eloquent\Builder` object to it.

**Example**
```php
public function modifyQuery(\Illuminate\Database\Eloquent\Builder $query)
{
    $query->orderBy('column', 'desc');
}
```

### HTML Output

If extension implements interface `Illuminate\Contracts\Support\Renderable`, extension rendering will be called in common stack, 
i.e. `render` will be called for each extension sequentially. https://github.com/LaravelRUS/SleepingOwlAdmin/blob/development/resources/views/default/display/table.blade.php#L28
Before rendering extensions are sorted by value of `getOrder`

If extension implements interface `SleepingOwl\Admin\Contracts\Display\Placable`, 
then extension HTML code will be rendered at certain place, defined by `getPlacement`

Places, where you can place code, are implemented with `@yield`
https://github.com/LaravelRUS/SleepingOwlAdmin/blob/development/resources/views/default/display/table.blade.php

# Using table in form
If needed, table can be used in form to output related records.

Let's say we have a gallery (section `Gallery`) and photos in it (`Photo`). Each photo has `category_id` - category identifier.
After category created, edit form needs to add photos to this category.

```php
// Create And Edit
$model->onCreateAndEdit(function($id = null) {

    $form = AdminForm::panel()->addHeader(
        AdminFormElement::text('title', 'Gallery name')->required(),
    );
    
    if (!is_null($id)) { // If gallery is created and has an ID
        $photos = AdminDisplay::table()
            ->setModelClass(Photo::class) // You must provide a calss, implementing photos storage
            ->setApply(function($query) use($id) {
                $query->where('category_id', $id); // Filter photos by gallery ID
            })
            ->setParameter('category_id', $id) // When you press "Add" button, set gallery ID
            ->setColumns(
                AdminColumn::link('name', 'Photo name'),
                AdminColumn::image('thumb', 'Preview')
                    ->setHtmlAttribute('class', 'text-center')
                    ->setWidth('100px')
            )
        
        $form->addBody($photos);
    }
});
```
