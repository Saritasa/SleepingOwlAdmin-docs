# HTML attributes

This trait is used to organize HTML manipulations in classes. This can be useful, for example, if you create a class
which will be output to HTML and you must provide user ability to set css classes, identifier and other element attributes.

Lets say we have a class `TableColumn`, which interpreted into `<td>{{ $value }}</td>` and you want to give user ability
to set attributes to get output `<td class="bg-primary" id="row-3" data-value="test"></test>`

You should use this trait

```php
<?php

namespace SleepingOwl\Admin\Display;

use KodiComponents\Support\HtmlAttributes;
use Illuminate\Contracts\Support\Arrayable;
use Illuminate\Contracts\Support\Renderable;

class TableColumn implements Arrayable, Renderable
{
    use HtmlAttributes;
    
    public function toArray()
    {
        return [
            'value' => 'test',
            'attributes' => $this->htmlAttributesToString(),
        ];
    }

    public function render()
    {
        return view(
            'table_column',
            $this->toArray()
        );
    }
}
```

```html
<td {{ $attributes }}>{{ $value }}</td>
```

Now you can set attributes to this class

```php
$column = new TableColumn();

$column->setHtmlAttribute('class', 'bg-primary');
$column->setHtmlAttribute('class', 'text-right');
$column->setHtmlAttribute('id', 'row-3');
$column->setHtmlAttribute('data-value', 'test');

// Or

$column->setHtmlAttributes([
   'class' => ['bg-primary', 'text-right'],
   'id' => 'row-3',
   'data-value' => 'test'
]);

// return <td class="bg-primary text-right" id="row-3" data-value="test">test</td>

// Redefine CSS class
$column->replaceHtmlAttribute('class', 'new-class');

// return <td class="new-class" id="row-3" data-value="test">test</td>

// Check, if CSS class exists 
$column->hasClassProperty('new-class'); // return true

// Check, if attribute exists
$column->hasHtmlAttribute('data-value'); // return true

// Remove attribute
$column->removeHtmlAttribute('data-value');

// Remove all attributes
$column->clearHtmlAttributes();


// Convert attributes to string
$column->htmlAttributesToString();
// return "class="new-class" id="row-3" data-value="test""
```
