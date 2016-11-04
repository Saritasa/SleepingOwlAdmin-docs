# Assets

This trait is used to plug in ассетов.

Let's say, we have class `Form\Element\Select`

```php
class Select extends ... implements \SleepingOwl\Admin\Contracts\Initializable 
{

  use \SleepingOwl\Admin\Traits\Assets;
  
  public function __construct()
  {
    // Инициализация пакета для хранения ассетов
    $this->initializePackage();
    
    ...
  }
  
  public function initialize()
  {
    // подключение ассетов в шаблон
    $this->includePackage();
    
    ...
  }
  
  ...
}
```

When we use trait, it initialises new package via `PackageManager` using current class name, i.e. for above class
it will be `PackageManager::add('Form\Element\Select')` and when we call trait methods `withPackage`, `addScript` and `addStyle`
we add new assets to this package.

As we know, the `initialize` method in class `Form\Element\Select` will be called at embedding element into a form, 
and assets will be added at that moment.

## API

#### addStyle
Add css file to package

```php
static::addStyle(string $handle, string $style, array $attributes): return self
```

#### addScript
Add js file to package

```php
static::addScript(string $handle, string $script, array $dependency): return self
```

#### withPackage
Plug in existing package

```php
static::withPackage(string|array $packages): return self
```
