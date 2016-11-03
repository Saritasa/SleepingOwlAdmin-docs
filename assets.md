# Assets Management

SleepingOwlAdmin implements a flexible assets management, using package
[kodicms/laravel-assets](https://github.com/KodiCMS/laravel-assets) - 
it allows developer to manage static resources in web-application, like CSS or JavaScript files.
(this package can be used standalone, without admin panel)

To manage assets within form elements, views, etc. you can use trait [assets](assets_trait.md), 
implemented via class `Meta`.

Package is represented with 3 facades: 
 - [Meta](#meta)
 - [Assets](#assets)
 - [PackageManager](#package-manager)
 - [Package](#package)


<a id="meta"></a>
## Meta
`KodiCMS\Assets\Meta`

Class Meta is used to generate bock 'meta' in template. This class is a service container,
initialized during all system components initialization. It's a `singleton` 
and allows to add styles and scripts at any moment before template rendering.

**Usage example**
```php
 Meta::setTitle('Test title')
 ->setMetaDescription(...)
 ->addJs('admin-default', asset('js/app.js'), ['admin-scripts'])
 ->addJs('admin-scripts', route('admin.scripts'))
 ->addCss('admin-default', asset('css/app.css'));
```

```html
<!DOCTYPE html>
<html lang="en">
<head>
	{!!
		Meta::addMeta(['charset' => 'utf-8'], 'meta::charset')
			->addMeta(['content' => csrf_token(), 'name' => 'csrf-token'])
			->addMeta(['content' => 'width=device-width, initial-scale=1', 'name' => 'viewport'])
			->addMeta(['content' => 'IE=edge', 'http-equiv' => 'X-UA-Compatible'])
			->render()
	!!}
</head>
<body>
 ...
 <!-- Render footer scritps -->
	{!! Meta::renderScripts(true) !!}
</body>
</html>
```

### API

#### `loadPackage`

Loads previously defined packages.

```php
static::loadPackage(string|array $packages): return $this
```

```php
// app\Providers\AppServiceProvider

PackageManager::add('jquery')
  ->js('jquery.js', 'https://code.jquery.com/jquery-3.1.0.min.js');
  
PackageManager::add('ckeditor')
  ->css('ckeditor.css', asset('css/ckeditor.css'))
  ->js('ckeditor.js', asset('js/ckeditor.js'));
  
// Template
Meta::loadPackage(['jquery', 'ckeditor'])
```

#### `addJs`

Adds *javascript* file.

```php
static::addJs(string $handle, string $src, array|string $dependency = null, bool $footer = false): return $this
```

##### Arguments
* `$handle` **string** - Asset key (if you provide existing key, corresponding asset will be replaced)
* `$src` **string** - Path to file (URL)
* `$dependency` **array|string** - Dependencies (identified by `$handle`. I.e. если you have asset `jquery` and 
you need to use your script only after it is loaded, you can put `jquery` in dependencies. The same about packages)
* `$footer` **bool** - will be marked to print include in footer (rather than in header)

#### `addJsElixir`

Add versioned javascript file

```php
static::addJsElixir(string $filename = 'js/app.js', string|array $dependency = null, bool $footer = false): return $this
```

#### `removeJs`

Remove javascript file. *If parameter `$handle` not provided, all javascript assets will be removed*

```php
static::removeJs(string $handle = null): return $this
```

#### `addCss`
Add *css* file.

```php
static::addCss(string $handle, string $src, array|string $dependency = null, array $attributes = []): return $this
```

##### Arguments
* `$handle` **string** - Asset key (if you provide existing key, existing asset will be replaced)
* `$src` **string** - path to file (URL)
* `$dependency` **array|string** - Dependencies (Identified by `$handle`. If you have asset `jquery` and 
you need to use your script only after it is loaded, you can put `jquery` in dependencies. The same about packages)
* `$attributes` **array** - Additional attributes (`['rel' => 'stylesheet', 'media' => 'all']`)

#### `addCssElixir`

Add versioned css file

```php
static::addCssElixir(string $filename = 'css/all.css', string|array $dependency = null, array $attributes = []): return $this
```

#### `removeCss`

Remove css file. *If parameter`$handle` not provided, all css assets will be removed*

```php
static::removeCss(string $handle = null): return $this
```

#### `putVars`
Print variables into javascript template.

```php
static::putVars(string|array $key, mixed $value = null): return $this
```

```php
Meta::putVars(['key' => 'value', 'key1' => ['data]])
// or

Meta::putVars('key', ['data'])
```

```html
<script>
window.key = 'value';
</script>
```

#### `removeVars`

Remove all added variables from stack.

```php
static::removeVars(): return $this
```


#### `setTitle`
Set title `<title>...</title>`

```php
static::setTitle(string $title): return $this
```
   
```php
Meta::setTitle('SleepingOwl Admin')
```

#### `setMetaDescription`

Add tag `<meta name="description" content="...">`

```php
static::setMetaDescription(string $description): return $this
```

#### `setMetaKeywords`

Set keywords in tag `<meta name="keywords" content="...">`

```php
static::setMetaKeywords(array|string $keywords): return $this
```

#### `setMetaRobots`

`<meta name=“robots” content=“...”>`

```php
static::setMetaRobots(string $robots): return $this
```

#### `setMetaData`

Set meta data via class, implementing `KodiCMS\Assets\Contracts\MetaDataInterface`
  - title
  - description
  - keywords
  - robots
  
```php
static::setMetaData(\KodiCMS\Assets\Contracts\MetaDataInterface $data): return $this
```
  
#### `addSocialTags`

Add social nets tags via class, implementing `KodiCMS\Assets\Contracts\SocialMediaTagsInterface`

```php
static::addSocialTags(\KodiCMS\Assets\Contracts\SocialMediaTagsInterface $socialTags): return $this
```

#### `setFavicon`

Set favicon for a page `<link rel=".." href=".." type="image/x-icon" />`

```php
static::setFavicon(string $url, string $rel = 'shortcut icon'): return $this
```

#### `addMeta`

Add `meta` tag

```php
static::addMeta(array $attributes, string $group = null): return $this
```
##### Arguments
* `$group` **string** - Key of element in group


```php
Meta::addMeta(['name' => 'description', 'content' => 'hello world']) // <meta name="description" content="hello world">
```

#### `addTagToGroup`

Add HTML tag into group. *By default, all tags (`favicon`, `description`, `keywords`) created via class `Meta` 
after html generation will be added to group with key `meta`*

```php
static::addTagToGroup(string $handle, string $content, array $params = [], string|array $dependency = null): return $this
```

##### Arguments
* `$handle` **string** - Element key in group
* `$content` **string** - HTML code `<meta name=":name" content=":description" />`
* `$params` **array** - Replaceable parameters. (`[':name' => $name, ':description' => 'My super description']`)
* `$dependency` **array|string** - Dependencies (identified by `$handle`. I.e. если you have asset `jquery` and 
you need to use your script only after it is loaded, you can put `jquery` in dependencies. The same about packages)

```php
Meta::addTagToGroup('favicon', '<link rel=":rel" href=":url" type=":type" />', [
 ':url' => $url,
 ':rel' => $rel,
 ':type' => $type
])
```

#### `removeFromGroup`

Delete HTML tag from group.

```php
static::removeFromGroup(string $handle): return $this
```

#### `assets`

Get object `KodiCMS\Assets\Assets`

```php
static::removeFromGroup(string $handle): return $this
```

<a id="assets"></a>
## Assets
`KodiCMS\Assets\Assets`

Class Assets is a storage for lists `css`, `javascript`, `vars` и `groups`. 

**Class Meta uses this class as storage, when adds assets.**

## API

#### `packageManager`

Gets object `KodiCMS\Assets\PackageManager`

```php
static::removeFromGroup(string $handle): return $this
```

<a id="package-manager"></a>
## PackageManager
`KodiCMS\Assets\PackageManager extends Collection`

Package manager. Package is a set of assets (javascript and css), which are joined into group, available by name. 

**Initialization example**
```php
// app\Providers\AppServiceProvider.php

...
public function boot() 
{
  PackageManager::add('custom')
   ->css('extend', asset('css/custom.css'))
   ->js('extend', asset('js/custom.js'));
}
```

You can get all accessible packages with console command

```bash
$ php artisan assets:packages
```

## API

#### `add`

Add new package

```php
static::add(KodiCMS\Assets\Contracts\PackageInterface|string $package): return KodiCMS\Assets\Contracts\PackageInterface
```

#### `load`

Load package object 

```php
static::load(string $name): return KodiCMS\Assets\Contracts\PackageInterface|null
```

<a id="package"></a>
## Package
`KodiCMS\Assets\Package extends Collection`

Package (container) for assets storage

## API

#### `with`

Add other packages dependency (these packages will be loaded automatically, when you add a package to template)

```php
static::with(array|...$packages): return $this
```

#### `js`

Add javascript file.

```php
static::js(string $handle, string $src, array|string $dependency = null, bool $footer = false): return $this
```

##### Аргументы
* `$handle` **string** - Asset key (if you provide existing key, corresponding asset will be replaced)
* `$src` **string** - Path to file (URL)
* `$dependency` **array|string** - Dependencies (identified by `$handle`. I.e. если you have asset `jquery` and 
you need to use your script only after it is loaded, you can put `jquery` in dependencies. The same about packages)
* `$footer` **bool** - will be marked to print include in footer (rather than in header)

#### `css`
Add css file.

```php
static::css(string $handle, string $src, array|string $dependency = null, array $attributes = []): return $this
```

##### Arguments
* `$handle` **string** - Asset key (if you provide existing key, corresponding asset will be replaced)
* `$src` **string** - Path to file (URL)
* `$dependency` **array|string** - Dependencies (identified by `$handle`. I.e. если you have asset `jquery` and 
you need to use your script only after it is loaded, you can put `jquery` in dependencies. The same about packages)
* `$footer` **bool** - will be marked to print include in footer (rather than in header)
* `$attributes` **array** - Additional attributes (`['rel' => 'stylesheet', 'media' => 'all']`)
