# Table columns
[Data displays](displays) extending 

- [Introduction](#introduction)
- [Supported display types](#supported-types)
- [Column heading](#column-heading)
- [Methods, supported in all elements](#all-methods)
- Types
    - [Text](#text)
    - [DateTime](#datetime)
    - [Link](#link)
    - [RelatedLink](#related-link)
    - [Url](#url)
    - [Lists](#lists)
    - [Count](#count)
    - [Email](#email)
    - [Image](#image)
    - [Order](#order)
    - [Custom](#custom)
    - [Action](#action)
    - [Checkbox](#checkbox)
    - [Control](#control)

<a name="introduction"></a>
## Introduction

These classes are intended for displaying data as table columns,
and output model values in edit form.

**Usage example**

```php
AdminDisplay::table()
    ->setColumns([
        AdminColumn::link('title')->setLabel('Title'),
        AdminColumn::datetime('date')->setLabel('Date')->setFormat('d.m.Y')->setWidth('150px')
    ])
```

```php
AdminForm::panel()
    ->addBody([
        AdminColumn::datetime('date')->setLabel('Date')->setFormat('d.m.Y')
    ])
```

Class `SleepingOwl\Admin\Display\TableColumn`, is base class for all columns
implements interfaces `Illuminate\Contracts\Support\Arrayable`,
`Illuminate\Contracts\Support\Renderable` and `SleepingOwl\Admin\Contracts\Initializable`.

<a name="supported-types"></a>
## Supported display types

 - `AdminColumn::text($name, $label = null)`
 - `AdminColumn::datetime($name, $label = null)`
 - `AdminColumn::link($name, $label = null)`
 - `AdminColumn::relatedLink($name, $label = null)`
 - `AdminColumn::count($name, $label = null)`
 - `AdminColumn::custom($label = null, \Closure $callback = null)`
 - `AdminColumn::image($name, $label = null)`
 - `AdminColumn::email($name, $label = null)`
 - `AdminColumn::lists($name, $label = null)`
 - `AdminColumn::url($name, $label = null)`
 - `AdminColumn::action($name)`
 - `AdminColumn::checkbox($label = null)`
 - `AdminColumn::control($label = null)`
 - `AdminColumn::filter($name, $label = null)`
 - `AdminColumn::order()`
 - `AdminColumn::treeControl()`

<a name="column-heading"></a>
## Column heading

Each table column has a header, represented by an individual object
`SleepingOwl\Admin\Contracts\Display\TableHeaderColumnInterface`.

**Example of header usage**

```php
...
    ->setColumns([
        ...
        AdminColumn::link('title')->setLabel('Title')->setOrderable(false),
        ...
    ]);
```

**Or direct header object usage**
```php
...
    ->setColumns([
        ...
        $link = AdminColumn::link('title')
        ...
    ]);

    $link->getHeader()
        ->setTitle('Title')
        ->setOrderable(false)
        ->setHtmlAttribute('class', 'bg-success text-center')
        ->setHtmlAttribute('data-tooltip', 'Test tooltip');
```

# API

<a name="all-methods"></a>
## Methods, supported in all elements
Columns class use traits 
 - [HtmlAttributes](html_attributes.md), to configure HTML attributes.
 - [Assets](assets_trait.md), to include assets.

#### setLabel
Seth column header text

    SleepingOwl\Admin\Display\TableColumn::setLabel(string $title): return self

#### setName
Set column key

    SleepingOwl\Admin\Contracts\NamedColumnInterface::setName(string $name): return self

#### setWidth
Set column width

    SleepingOwl\Admin\Display\TableColumn::setWidth(string $width): return self

#### setView
Set column view

    SleepingOwl\Admin\Display\TableColumn::setView(string|\Illuminate\View\View $view): return self

#### append

    SleepingOwl\Admin\Display\TableColumn::append(\SleepingOwl\Admin\Contracts\ColumnInterface $append): return self

#### setOrderable
If order by this column can be applied

    SleepingOwl\Admin\Display\TableColumn::setOrderable(boolean $orderable): return self

<a name="action"></a>
## Action 
`SleepingOwl\Admin\Display\Column\Action`

This element is used to add Button, executing some action on table data.
**Needs a `checkbox` element in table, for this element to work**

```php
$table = AdminDisplay::table()
    ->setActions([
        AdminColumn::action('export', 'Export')->setIcon('fa fa-share')->setAction(route('news.export')),
    ])
    ->setColumns([
        AdminColumn::checkbox(),
        ...
    ]);

// Buttons placement on page
$table->getActions()
    ->setPlacement('panel.buttons')
    ->setHtmlAttribute('class', 'pull-right');
```

#### setTitle
Set button title

    SleepingOwl\Admin\Display\Column\Action::setTitle(string $title): return self

#### setAction
Set url, to which will be sent request, containing selected elements

    SleepingOwl\Admin\Display\Column\Action::setAction(string $action): return self

#### setMethod
Set request type to send [POST, GET, ...]

    SleepingOwl\Admin\Display\Column\Action::setMethod(string $method): return self

#### useGet
Use GET request method

    SleepingOwl\Admin\Display\Column\Action::useGet(): return self

#### usePost
Use POST request method

    SleepingOwl\Admin\Display\Column\Action::usePost(): return self


#### usePut
Use PUT request method

    SleepingOwl\Admin\Display\Column\Action::usePut(): return self

#### useDelete
Use DELETE request method

    SleepingOwl\Admin\Display\Column\Action::useDelete(): return self

#### setIcon
Set icon for the button

    SleepingOwl\Admin\Display\Column\Action::setIcon(string $icon): return self

<a name="checkbox"></a>    
## Checkbox
`SleepingOwl\Admin\Display\Column\Checkbox`

This element is used to output checkbox column in table to select rows [Action](#action)

```php
AdminColumn::checkbox(),
```

<a name="custom"></a>
## Custom
`SleepingOwl\Admin\Display\Column\Custom`

This element is used to add custom code as table column


```php
AdminColumn::custom(function(\Illuminate\Database\Eloquent\Model $model) {
    return $model->id;
})->setWidth('150px'),
```

#### setCallback
Set closure, which will be called for each table element, and get `Illuminate\Database\Eloquent\Model`
instance as argument.

    SleepingOwl\Admin\Display\Column\Custom::setCallback(\Closure $callback): return self

<a name="datetime"></a>
## DateTime
`SleepingOwl\Admin\Display\Column\DateTime`

This element is used to output date in provided format

```php
AdminColumn::datetime('date', 'Date')->setFormat('d.m.Y')->setWidth('150px'),
```

#### setFormat
Set date format

    SleepingOwl\Admin\Display\Column\DateTime::setFormat(string $format): return self

<a name="link"></a>    
## Link
`SleepingOwl\Admin\Display\Column\Link`

This element is used to output reference URL to current model.

```php
AdminColumn::link('title', 'Title')->setLinkAttributes(['target' => '_blank']),
```

#### setLinkAttributes
Set link element attributes

    SleepingOwl\Admin\Display\Column\Link::setLinkAttributes(array $linkAttributes): return self

<a name="related-link"></a>    
## RelatedLink
`SleepingOwl\Admin\Display\Column\RelatedLink`

This element is used to output reference URL to a related model.

```php
AdminColumn::relatedLink('author.name', 'Author')
```

#### setLinkAttributes
Set link element attributes

    SleepingOwl\Admin\Display\Column\Link::setLinkAttributes(array $linkAttributes): return self


<a name="url"></a>    
## URL
`SleepingOwl\Admin\Display\Column\Url`

This element is used to output a field value as URL link

```php
AdminColumn::url('title', 'Title'),
```

#### setLinkAttributes
Set link element attributes

    SleepingOwl\Admin\Display\Column\Url::setLinkAttributes(array $linkAttributes): return self
    
<a name="text"></a>    
## Text
`SleepingOwl\Admin\Display\Column\Text`

This element is used to output field value as plain text.

```php
AdminColumn::text('title', 'Title'),
```

<a name="count"></a>    
## Count
`SleepingOwl\Admin\Display\Column\Count`

This element is used to count and output elements value. 
Count executed by calling `count` function, i.e. provided field must be either an array field or contain an element with `*Many` reference.

```php
AdminColumn::count('list', 'Total'),
```

<a name="email"></a>    
## Email
`SleepingOwl\Admin\Display\Column\Email`

This element is used to output field, containing email address, as `<a href="mailto:"></a>` link.

```php
AdminColumn::email('email', 'Email'),
```

<a name="image"></a>    
## Image
`SleepingOwl\Admin\Display\Column\Image`

This element is used to output an image

```php
AdminColumn::image('avatar', 'Avatar'),
```

#### setImageWidth
Set image width

    SleepingOwl\Admin\Display\Image::setImageWidth(string $width): return self


<a name="lists"></a>    
## Lists
`SleepingOwl\Admin\Display\Column\Lists`

This element is used to output a field, containing array of values or a `*Many` relation

```php
AdminColumn::lists('roles.title', 'Roles') // Вывод списка ролей из связанной таблицы

// or

AdminColumn::lists('tags', 'Tags') // Вывод списка тегов из поля содержащего массив тегов
```


<a name="order"></a>    
## Order
`SleepingOwl\Admin\Display\Column\Order`

This element is used to sort table elements. 
**Applicable model should contain trait `SleepingOwl\Admin\Traits\OrderableModel`**. 
By default, field against which sorting is performed - `order`. 
To set alternative key, you should implement in your model method:

```php
class Users extend Model {
    use \SleepingOwl\Admin\Traits\OrderableModel;
    
    ...

    /**
     * Get order field name.
     * @return string
     */
    public function getOrderField()
    {
        return 'custom_order_field_name';
    }
}
```

Don't forget to sort records by this field to display table correctly on output `->orderBy('order', 'asc')`

<a name="control"></a>
## Control
`SleepingOwl\Admin\Display\Column\Control`

This element is used in table view to display action buttons, related to table element. 
It is added to all table elements automatically and provides following actions:
 - Edit element
 - Delete
 - Restore
 
**Access current element**
```php
$display = AdminDisplay::table()->...;
 
$display->getColumns()->getControlColumn(); // return SleepingOwl\Admin\Display\Column\Control
```
If needed, you can add more actions performed to element:
```php
$control = $display->getColumns()->getControlColumn();

$link = new \SleepingOwl\Admin\Display\ControlLink(function (\Illuminate\Database\Eloquent\Model $model) {
   return 'http://localhost/'.$model->getKey(); // Generate link
}, 'Button text', 50);

$control->addButton($link);

$button = new \SleepingOwl\Admin\Display\ControlButton(function (\Illuminate\Database\Eloquent\Model $model) {
   return 'http://localhost/delete/'.$model->getKey(); // Generate link
}, 'Button text', 50);

// Change method of form submit button
$button->setMethod('delete');

// Hide text from button
$button->hideText();

// Set icon
$button->setIcon('fa fa-trash');

// Additional HTML attributes for button
$button->setHtmlAttribute('class', 'btn-danger btn-delete');

// Button visibility condition (optional)
$button->setCondition(function(\Illuminate\Database\Eloquent\Model $model) {
   return auth()->user()->can('delete', $model);
});

$control->addButton($button);
```
**At present moment there are two button classes** 
 - `SleepingOwl\Admin\Display\ControlButton` - submit button inside form
 - `SleepingOwl\Admin\Display\ControlLink` - link button
 
 To add your own button classes, by implementing interface `SleepingOwl\Admin\Contracts\Display\ControlButtonInterface`
