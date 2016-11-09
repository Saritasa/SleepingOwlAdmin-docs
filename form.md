# Forms

Used to build HTML forms.

At present time there are 3 form types implemented:
 - `AdminForm::form()`
 - `AdminForm::panel()` - based on [Bootstrap component `panel`](http://getbootstrap.com/components/#panels)
 - `AdminForm::tabbed()` - groups forms and elements into tabs

## form
 * Class `\SleepingOwl\Admin\Form\FormDefault`
 * View `resources\views\default\form\default.blade.php`

This form type renders elements without styles. You can adjust form look, using HTML attributes

```php
$form = AdminForm::form()->setElements([
    AdminFormElement::text('title', 'Title'),
]);

$form->addElement(
    AdminFormElement::date('created_at', 'Created at')
);

$form->setHtmlAttribute('class', 'panel panel-default');
$form->getButtons()->setHtmlAttribute('class', 'panel-footer');
```

## panel
 * Class `\SleepingOwl\Admin\Form\FormPanel`
 * View `resources\views\default\form\panel.blade.php`

This form type automatically adds to HTML attribute `class="panel panel-default"` to form `class="panel-footer"` to buttons
and allows to place form elements into blocks `header`, `body`, `footer`.

```php
$form = AdminForm::panel()
    ->addHeader([
        AdminFormElement::text('title', 'Title'),
    ])
    ->addBody(
        AdminFormElement::wysiwyg('text', 'Text', 'ckeditor')->required()
    )
    ->addFooter(
        AdminFormElement::select('type', 'Type'), ...)
    );

$form->addItem(AdminFormElement::date('created_at', 'Created at'));

$form->addElement(AdminFormElement::date('created_at', 'Created at')); // Place element without block

$form->setElements([
    AdminFormElement::date('created_at', 'Created at')
]); // Place list of elements without block
```

Form blocks are classes, implementing interface `SleepingOwl\Admin\Contracts\Form\PanelInterface`. You can implement 
your own block class and put it into form:

```php
$form->addElement(new \App\Form\Panel\CustomBlockClass([
    AdminFormElement::text('title', 'Title')
]));
```

### API

#### addItem
Add element to form. If form already contains blocks (`header`, `body`, `footer`), then element will be added to last
added block. If there are not blocks, `body` block will be created, and element will be placed inside it.

    SleepingOwl\Admin\Form\FormDefault::addItem(mixed $item): return self

#### addHeader
Add elements to block `panel-heading`

    SleepingOwl\Admin\Form\FormPanel::addHeader(array|\SleepingOwl\Admin\Contracts\FormElementInterface $items): return self
    
#### addBody
Add elements to block `panel-body`. If previous block is `body`, a divider `<hr />` will be placed between them

    SleepingOwl\Admin\Form\FormPanel::addBody(array|\SleepingOwl\Admin\Contracts\FormElementInterface $items): return self
    
#### addFooter
Add elements to block `panel-footer`

    SleepingOwl\Admin\Form\FormPanel::addFooter(array|\SleepingOwl\Admin\Contracts\FormElementInterface $items): return self

## tabbed
A variation of forms, in which you can distribute elements across tabs

```php
AdminForm::tabbed()->setElements([
    'tab1' => [
        ....
    ],
    'tab2' => [
        ....
    ]
]);
```

# API (methods available in all classes)

Forms class uses traits:
  - [HtmlAttributes](html_attributes.md), to configure HTML attributes.
  - [Assets](assets_trait.md), to include assets.

#### setButtons
Set class which renders buttons. Default: `SleepingOwl\Admin\Form\FormButtons`

    SleepingOwl\Admin\Form\FormDefault::setButtons(\SleepingOwl\Admin\Contracts\FormButtonsInterface $buttons): return self

#### setView
Set view, used to render form

    SleepingOwl\Admin\Form\FormDefault::setView(\Illuminate\View\View|string $view): return self
    
#### setAction
Set URL, to which form will send data

    SleepingOwl\Admin\Contracts\FormInterface::setAction(string $action): return self
    
#### setElements
Add elements array to form

    SleepingOwl\Admin\Contracts\Form\ElementsInterface::setElements(array $elements): return self
    

#### addElement
Add single element to form

    SleepingOwl\Admin\Form\FormElements::addElement(mixed $element): return self
    
# Form elements (Fields)
В качестве элемента формы может выступать любой класс, которые реализует интерфейс `Illuminate\Contracts\Support\Renderable`.

## AdminColumn
You can use table columns, if needed

```php
AdminForm::form()->setElements([
    AdminFormElement::upload('image', 'Image'), // Input element to upload image
    AdminColumn::image('image', 'Image') // Output uploaded image
])
```

## Tabs
You can place form elements to tabs. 
**Provide unique tab names, when you place several tab sections, otherwise tabs can switch incorrectly.**

```php
$tabs = AdminDisplay::tabbed();
$tabs->setTabs(function ($id) {
    $tabs = [];

    $tabs[] = AdminDisplay::tab(AdminForm::elements([
        AdminFormElement::text('title', 'Title')->required(),
    ]))->setLabel('SEO');

    $tabs[] = AdminDisplay::tab(new \SleepingOwl\Admin\Form\FormElements([
        AdminFormElement::date('created_at', 'Created at')->required()
    ]))->setLabel('Dates');

    return $tabs;
});

$tabs->appendTab([
    AdminFormElement::text('title', 'Title')->required(),
], 'Tab 1');


$tabs1 = AdminDisplay::tabbed();
    
$tabs1 = ....;


AdminForm::form()
    ->addElement($tabs)
    ->setElements([
        AdminFormElement::upload('image', 'Image'), // Input element to upload image
        AdminColumn::image('image', 'Image') // Output image element
    ])
    ->addElement($tabs1);
    
// or

$form = AdminForm::panel()
    ->addHeader([
        $tabs
    ]);
    
// or

$form = AdminForm::panel()
    ->setElements([
        AdminFormElement::upload('image', 'Image'),
        $tabs
    ]);
```

## Columns
Split form into several columns. Columns can be used inside tabs, and vice versa..

```php
$columns = AdminFormElement::columns([
    [
        AdminFormElement::text('title', 'Title')->required()
    ],
    // or 
    function() {
        return [
            AdminFormElement::text('title', 'Title')->required()
        ];
    },
    // or
    new \SleepingOwl\Admin\Form\Columns\Column([
        AdminFormElement::date('created_at', 'Created At')->required()
    ])
]);

$columns->addColumn([
    AdminFormElement::date('created_at', 'Created At')->required()
]);

// or 

$columns->addColumn(function() {
    return [
        AdminFormElement::date('created_at', 'Created At')->required()
    ];
});

// or

$columns->addColumn(new \SleepingOwl\Admin\Form\Columns\Column([
    AdminFormElement::date('created_at', 'Created At')->required()
]));



$columns->addColumn($subColumns = AdminFormElement::columns([
    [
        AdminFormElement::text('description', 'Description')
    ]
]);

$subColumns->addColumn(...)

$form = AdminForm::panel()->addBody($columns);
```

#### Example of usage columns and tabs together
```php

$tabs = AdminDisplay::tabbed([
    'Tab 1' => new FormElements([
        AdminFormElement::text('title', 'Title')->required()
    ]),
    'Tab 2' => new FormElements([
        AdminFormElement::select('type', 'Type'),
        AdminFormElement::date('event_at', 'Event at')->setFormat('Y-m-d H:i:00')
    ])
]);

$columns = AdminFormElement::columns();

$columns->addColumn([$tabs]);

$columns->addColumn([
    AdminFormElement::date('created_at', 'Created at')
]);

// Or

$tabs->appendTab(new FormElements([$columns]));
```

## Upload
Element `AdminFormElement::upload('image', 'Image')` is used to upload files using `<input type="upload" />`.

When you add this element, form should automatically get attribute `enctype="multipart/form-data"` assigned.. 
If it does not happen,:

```php
return AdminForm::panel()
    ....
    ->setHtmlAttribute('enctype', 'multipart/form-data');
```

To work with this element, you can use trait `KodiComponents\Support\Upload`, which helps to upload file attachment
and save link to it into DB. (You can use this trait standalone, with composer package kodicomponents/support) 
 который поможет с загрузкой прикрепленного файла и сохранением ссылки на него в БД. (При желании данный трейт можно использовать отдельно, используя composer пакет `kodicomponents/support`)

Usage example:

```php
class User extends Model
{
    use \KodiComponents\Support\Upload;
    
    /**
     * The attributes that should be cast to native types.
     *
     * @var array
     */
    protected $casts = [
        'image' => 'image', // or file | upload
    ];
}
```

After you add trait to model, you should add to `$casts` array fields, which should work with uploaded files. 
Files should have type `file` or `upload`, images should have type `image`.
**Object `Illuminate\Http\UploadedFile` is expected as file**

If you set field type to `image`, then you can provide rules for image processing 
(Work with images implemented via package http://image.intervention.io/)

```php
/**
 * @return array
 */
public function getUploadSettings()
{
    return [
        'image' => [ // Key field
            // Name of function to apply to image http://image.intervention.io/api/fit and parameters, passed to it
            'fit' => [300, 300, function ($constraint) {
                $constraint->upsize();
                $constraint->aspectRatio();
            }],
            'crop' => [300, 300], // http://image.intervention.io/api/crop
            ...
        ],
        'image_small' => [
            ...
        ]
    ];
}
```

Default path to store files: `public\storage\{table_name}\{field_name}\{file_name_hash:2chars}\{uniqid}.{ext}`.
You can provide your own file name format

```php
/**
 * @param UploadedFile $file
 *
 * @return string
 */
protected function getUploadFilename(\Illuminate\Http\UploadedFile $file)
{
    return md5($this->id).'.'.$file->getClientOriginalExtension();
}
```
At present moment you cannot change path to store files.

If you have multiple image files (ex. thumbnail and large image), and you can load them via single field, you can do this:

```php
/**
 * The attributes that should be cast to native types.
 *
 * @var array
 */
protected $casts = [
    'image' => 'image',
    'thumb' => 'image',
];

/**
 * @return array
 */
public function getUploadSettings()
{
    return [
        'image' => [
            'orientate' => [],
            'resize' => [1280, null, function ($constraint) {
                $constraint->upsize();
                $constraint->aspectRatio();
            }]
        ],
        'thumb' => [
            'orientate' => [],
            'fit' => [200, 300, function ($constraint) {
                $constraint->upsize();
                $constraint->aspectRatio();
            }]
        ]
    ];
}

/**
 * @param \Illuminate\Http\UploadedFile $file
 */
public function setUploadImageAttribute(\Illuminate\Http\UploadedFile $file = null)
{
    if (is_null($file)) {
        return;
    }

    foreach ($this->getUploadFields() as $field) {
        $this->{$field.'_file'} = $file;
    }
}
```

In form you write `AdminFormElement::upload('upload_image', 'Image')`, i.e. uploaded file will be passed into model, 
using `upload_image` field mutator `setUploadImageAttribute`, where file will be stored to appropriate fields, processed
and saved according to their rules.

Also uploaded file has full local path and URL. Ex. for field named `image`:

 - `$user->image` `public\storage\users\image\23n4b23hj4b.jpg`
 - `$user->image_path` `\var\www\site.com\public\storage\users\image\23n4b23hj4b.jpg`
 - `$user->image_url` `http:\\site.com\storage\users\image\23n4b23hj4b.jpg`
 - 
 
### Validation
This field type supports uploaded files validation  
 - https://laravel.com/docs/5.2/validation#rule-image
 - https://laravel.com/docs/5.2/validation#rule-mimetypes
 - https://laravel.com/docs/5.2/validation#rule-mimes

```php
AdminFormElement::upload('image', 'Image')->addValidationRule('image')

// or for file

AdminFormElement::upload('pdf', 'PDF')->addValidationRule('mime:pdf'),
```

### Trait usage limitations
**!!! Trait will override methods `getAttribute`, `mutateAttribute`, `setAttribute`. In case you override them 
in your model too, this can cause troubles !!!**
