# Menu Configuration

SleepingOwl Admin menu configuration is located by default at `app/Admin/navigation.php`. If file
returns array, this array will be used to build menu.

Navigation class `SleepingOwl\Admin\Navigation` is initialized by Service Container 
and can be accessed using following methods:

```php
$navigation = app('sleeping_owl.navigation');

// or using facade

$navigation = AdminNavigation::getRootFacade();
```
____

New menu section can be added using following methods:

**Using service container or facade**
```php
app('sleeping_owl.navigation')
    ->addPage()
    ->setTitle('Blog')
    ->setUrl('/blog');
    
// or

// Create menu element for model
AdminNavigation::addPage(\App\Blog::class)
    ->setPriority(100)
    ->setIcon('fa fa-newspaper-o');
```

**Using `AdminSection` class**

```php
AdminSection::addMenuPage(\App\User::class);
```

Method `addPage` can accept as argument
 - `[string] Model class name`
 - `[SleepingOwl\Admin\Navigation\Page] Page class`
 - `[array] ['title' => 'News', 'priority' => 100, 'icon' => 'fa fa-newspaper-o']`
 
**Example**
```php
// Use registered model class to add section
AdminNavigation::addPage(\App\Blog::class);

AdminNavigation::addPage(new Page()->setTitle('News'));

AdminNavigation::addPage(['title' => 'News', 'priority' => 100, 'icon' => 'fa fa-newspaper-o']);
```

Menu can have unlimited pages nesting:

```php
use SleepingOwl\Admin\Navigation\Page;

AdminNavigation::addPage(\App\Blog::class)->setPages(fuction(Page $section) {
   
    $section
        ->addPage()
        ->setTitle('Tags')
        ->setUrl('blog/tags')
        ->setIcon('fa fa-tags');
    
    $section->addPage()->setTitle('settings')->setPages(function(Page $section) {
    
        $section
            ->addPage()
            ->setTitle('blog settings')
            ->setUrl(route('blog.settings'));
        
    });
});
```

Also you can add array of menu sections:

```php

use SleepingOwl\Admin\Navigation\Page;

$array = [
     [
         'title' => 'News', 
         'priority' => 100, 
         'icon' => 'fa fa-newspaper-o'
     ],
     [
         'title' => 'About', 
         'priority' => 200, 
         'pages' => [
             // If string is passed as page, a search by registered models will be performed
             \App\Blog::class,
             
             (new Page())->setTitle('Us')->setUrl('about/us'),
             
             [
                 'title' => 'Contacts', 
                 'priority' => 300, 
                 'icon' => 'fa fa-credit-card'
             ]
         ]
     ]
];

AdminNavigation::setFromArray($array);

// Nested menu section

AdminNavigation::addPage(\App\Blog::class)->setPages(fuction(Page $section) {
       $section->addPage()->setTitle('Sub menu')->setFromArray($array);
});
```

## Access

You can set visibility rules for each menu section. 
Access check is following: each menu object can have its own local rule to check access

```php
AdminNavigation::addPage(\App\Blog::class)->setAccessLogic(function() {
    return auth()->user()->isSuperAdmin();
})
```

**If there is no rule for page:**
 - If menu item is a link to a section, and section is not accessible to view, menu item will become invisible
 - If page has parent, check if parent page has access rule, and if it has, run that check
 - If parent has no rule, go next level up, up to global rule.

**There are several access management scenarios:**
 - Set global rule
 
```php
AdminNavigation::setAccessLogic(function(Page $page) {
   return auth()->user()->isSuperAdmin();
});
```
 - Set rule for each page
 - Set rule for a section, it will be applied to each child page, that doesn't have its own rule

______
A simple example of menu configuration:

```
return [
    [
        'title' => 'Permissions',
        'icon' => 'fa fa-group',
        'pages' => [
            (new Page(\App\User::class))
                ->setIcon('fa fa-user')
                ->setPriority(0),
            (new Page(\App\Role::class))
                ->setIcon('fa fa-group')
                ->setPriority(100)
        ]
    ]
];
```
