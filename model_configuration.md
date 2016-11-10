# Model Configuration

SleepingOwl Admin model configurations must be placed in a directory, configured in `sleeping_owl.bootstrapDirectory`
 (*default: `app/Admin`*).

You can store model configurations in one file or distribute them across multiple files, as you wish.

Here is a sample of model configuration:

```php
AdminSection::registerModel(Company::class, function (ModelConfiguration $model) {
    $model->setTitle('Companies');
    // Display
    $model->onDisplay(function () {
        $display = AdminDisplay::table()->setColumns([
            AdminColumn::link('title')->setLabel('Title')->setWidth('400px'),
            AdminColumn::text('address')->setLabel('Address')->setAttribute('class', 'text-muted'),
        ]);
        $display->paginate(15);
        return $display;
    });
    // Create And Edit
    $model->onCreateAndEdit(function() {
        return $form = AdminForm::panel()->addBody(
            AdminFormElement::text('title', 'Title')->required()->unique(),
            AdminFormElement::textarea('address', 'Address')->setRows(2),
            AdminFormElement::text('phone', 'Phone')
        );
        return $form;
    });
})
    ->addMenuPage(Company::class, 0)
    ->setIcon('fa fa-bank');
```

By default section will be available at `admin/companies` URL, i.e. class name will be used in multiple form.
To set your own path you may use method `setAlias`

```php
...
$model->setAlias('subdir/companies');
...
```

## Action popup messages

You can change text of messages, displayed on record add, edit, delete.

```php
AdminSection::registerModel(Company::class, function (ModelConfiguration $model) { 

    // Create record
    $model->setMessageOnCreate('Company created');
   
    // Edit record
    $model->setMessageOnUpdate('Company updated');
    
    // Delete record
    $model->setMessageOnDelete('Company deleted');
   
    // Restore record
    $model->setMessageOnRestore('Company restored');
});
```

## Restrict actions.

In some cases you need to restrict editing or deleting records.
There are special methods for this:

```php
AdminSection::registerModel(Company::class, function (ModelConfiguration $model) { 
    // Disable view
    $model->disableDisplay();
    
    // Disable creating
    $model->disableCreating();
    
    // Disable editing
    $model->disableEditing();
    
    // Disable deleting
    $model->disableDeleting();
    
    // Disable restoring
    $model->disableRestoring();
})
```

## Access restriction

By default, all users have access to admin panel sections. 
If you want to customize some sections access, first you should enable access check

```php
AdminSection::registerModel(User::class, function (ModelConfiguration $model) {
    ...
    $model->enableAccessCheck();
    ...
});
```

After that each action access will be checked by `Gate` https://laravel.com/docs/5.3/authorization#via-the-gate-facade

How to use it? Laravel has an excellent mean for that - `Policies` https://laravel.com/docs/5.3/authorization#policies

You should create a new class, for example `App\Policies\UserPolicy`

```php
<?php

namespace App\Policies;

use App\User;
use Illuminate\Auth\Access\HandlesAuthorization;

class UserPolicy
{

    use HandlesAuthorization;

    /**
     * @param User   $user
     * @param string $ability
     *
     * @return bool
     */
    public function before(User $user, $ability, User $item)
    {
        if ($user->isSuperAdmin()) {
            return true;
        }
    }

    /**
     * @param User $user
     * @param User $item
     *
     * @return bool
     */
    public function display(User $user, User $item)
    {
        return true;
    }

    /**
     * @param User $user
     * @param User $item
     *
     * @return bool
     */
    public function create(User $user, User $item)
    {
        return true;
    }

    /**
     * @param User $user
     * @param User $item
     *
     * @return bool
     */
    public function edit(User $user, User $item)
    {
        return true;
    }

    /**
     * @param User $user
     * @param User $item
     *
     * @return bool
     */
    public function delete(User $user, User $item)
    {
        return true;
    }

    /**
     * @param User $user
     * @param User $item
     *
     * @return bool
     */
    public function restore(User $user, User $item)
    {
        return true;
    }
}

```

This class contains methods, called when access is checked.
First Gate calls method `before`, which checks global access permissions. If it returns true, others checks are ignored.
If it returns nothing, Gate calls method, responsible for certain action, and passes two parameters:
  - Current user object
  - Object to validate access rights
 
For example, we want user to be able to create new posts and edit only his own posts:
```php
public function create(User $user, Post $post)
{
    return true;
}

public function edit(User $user, Post $post)
{
    return $post->isAutoredBy($user);
}
```

Now we should register policy in `App\Providers\AuthServiceProvider`

```php
protected $policies = [
    ...
    \App\User::class => \App\Policies\UserPolicy::class
    ...
];
```

После чего раздел будет использовать этот policy для проверки прав доступа.

Данный механизм позволяет очень гибко настраивать права доступа к каждому разделу.

## События

У вас есть возможность подписаться на события создания, редактирования, удаления и восстановления записей в разделе.

```php
AdminSection::registerModel(Company::class, function (ModelConfiguration $model) { 

    // Создание записи
    $model->creating(function(ModelConfiguration $model, Company $company) {
        // Если вернуть false, то создание будет прервано
    });
    
    $model->created(function(ModelConfiguration $model, Company $company) {
    
    });
    
    // Обновление записи
    $model->updating(function(ModelConfiguration $model, Company $company) {
        // Если вернуть false, то обновление будет прервано
    });
    
    $model->updated(function(ModelConfiguration $model, Company $company) {
    
    });
    
    // Удаление записи
    $model->deleting(function(ModelConfiguration $model, Company $company) {
        // Если вернуть false, то удаление будет прервано
    });
    
    $model->deleted(function(ModelConfiguration $model, Company $company) {
    
    });
    
    // Восстановление записи
    $model->restoring(function(ModelConfiguration $model, Company $company) {
        // Если вернуть false, то восстановление будет прервано
    });
    
    $model->restored(function(ModelConfiguration $model, Company $company) {
    
    });
});
```

Также чисто теоретически можно подписаться на событие следующим образом

```php
Even::listen('sleeping_owl.section.updating:'.Company::class, function(ModelConfiguration $model, Company $company) {

});
```

## Переопределение контроллера

В случае если вы хотите использовать собственный контроллер используйте метод `setControllerClass`

```php
AdminSection::registerModel(User::class, function (ModelConfiguration $model) {
    ...
    $model->setControllerClass(UserController::class);
    ...
});
```

```php
namespace App\Http\Controllers;

class UserController extends \SleepingOwl\Admin\Http\Controllers\AdminController
{
    ...
}
```

# API (Доступные методы)

### setAlias
Установка алиаса ссылки для раздела *(По умолчанию название класса модели во множественном числе)*

    \SleepingOwl\Admin\Model\ModelConfiguration SleepingOwl\Admin\Model\ModelConfiguration::setAlias(string $alias)

```php
$model->setAlias('manage/user');
```

### setTitle
Установка заголовка для раздела *(По умолчанию название класса модели во множественном числе)*

    \SleepingOwl\Admin\Model\ModelConfiguration SleepingOwl\Admin\Model\ModelConfiguration::setTitle(string $title)


### setCreateTitle
Установка заголовка для страницы создания

    \SleepingOwl\Admin\Model\ModelConfiguration SleepingOwl\Admin\Model\ModelConfiguration::setCreateTitle(string $title)

### setUpdateTitle
Установка заголовка для страницы редактирования

    \SleepingOwl\Admin\Model\ModelConfiguration SleepingOwl\Admin\Model\ModelConfiguration::setUpdateTitle(string $title)

### onDisplay
Описание данных для вывода списка записей на страницы 

    \SleepingOwl\Admin\Model\ModelConfiguration SleepingOwl\Admin\Model\ModelConfiguration::onDisplay(\Closure $callback)

### onCreate
Описание формы для страницы создания записи (Форму редактирования необходимо будет описывать отдельно)

    \SleepingOwl\Admin\Model\ModelConfiguration SleepingOwl\Admin\Model\ModelConfiguration::onCreate(\Closure|null $callback)

### onEdit
Описание формы для страницы редактирования записи (Форму создания необходимо будет описывать отдельно)

    \SleepingOwl\Admin\Model\ModelConfiguration SleepingOwl\Admin\Model\ModelConfiguration::onEdit(\Closure|null $callback)

##### Arguments
* $callback **Closure|null** В функцию будет передан идентификатор записи


### onCreateAndEdit
Описание формы для страницы создания и редактирования записи

    \SleepingOwl\Admin\Model\ModelConfiguration SleepingOwl\Admin\Model\ModelConfiguration::onCreateAndEdit(\Closure|null $callback)

##### Arguments
* $callback **Closure|null** В функцию будет передан идентификатор записи


### onDelete
Код, который будет выполнен в момент удаления записи

    \SleepingOwl\Admin\Model\ModelConfiguration SleepingOwl\Admin\Model\ModelConfiguration::onDelete(\Closure|null $callback)

##### Arguments
* $callback **Closure|null** В функцию будет передан идентификатор записи


### onDestroy
Код, который будет выполнен в момент окончательного удаления записи (в случае если запись помечена как удаленная)

    \SleepingOwl\Admin\Model\ModelConfiguration SleepingOwl\Admin\Model\ModelConfiguration::onDestroy(\Closure|null $callback)

##### Arguments
* $callback **Closure|null** В функцию будет передан идентификатор записи

### onRestore
Код, который будет выполнен в момент восстановления записи

    \SleepingOwl\Admin\Model\ModelConfiguration SleepingOwl\Admin\Model\ModelConfiguration::onRestore(\Closure|null $callback)

##### Arguments
* $callback **Closure|null** В функцию будет передан идентификатор записи


### disableDisplay
Запрет страницы просмотра списка записей раздела

    \SleepingOwl\Admin\Model\ModelConfiguration SleepingOwl\Admin\Model\ModelConfiguration::disableDisplay()


### disableCreating
Запрет создания записей

    \SleepingOwl\Admin\Model\ModelConfiguration SleepingOwl\Admin\Model\ModelConfiguration::disableCreating()

### disableEditing
Запрет редактирования записей

    \SleepingOwl\Admin\Model\ModelConfiguration SleepingOwl\Admin\Model\ModelConfiguration::disableEditing()

### disableDeleting
Запрет удаления записей

    \SleepingOwl\Admin\Model\ModelConfiguration SleepingOwl\Admin\Model\ModelConfiguration::disableDeleting()


### disableDestroying
Запрет окончательного удаления записей (в случае если запись помечена как удаленная)

    \SleepingOwl\Admin\Model\ModelConfiguration SleepingOwl\Admin\Model\ModelConfiguration::disableDestroying()

### disableRestoring
Запрет восстановления записей

    \SleepingOwl\Admin\Model\ModelConfiguration SleepingOwl\Admin\Model\ModelConfiguration::disableRestoring()


### enableAccessCheck
Включение проверки прав доступа для раздела (Для проверки прав доступа используется `Gate`. По умолчанию доступ запрещен для всех)

    \SleepingOwl\Admin\Model\ModelConfiguration SleepingOwl\Admin\Model\ModelConfiguration::enableAccessCheck()

### disableAccessCheck
Выключение проверки прав доступа

    \SleepingOwl\Admin\Model\ModelConfiguration SleepingOwl\Admin\Model\ModelConfiguration::disableAccessCheck()

### setMessageOnCreate
Установка текста сообщения отображаемого после создания записи

    mixed SleepingOwl\Admin\Model\ModelConfiguration::setMessageOnCreate(string $messageOnCreate)

### setMessageOnUpdate
Установка текста сообщения отображаемого после обновления записи

    \SleepingOwl\Admin\Model\ModelConfiguration SleepingOwl\Admin\Model\ModelConfiguration::setMessageOnUpdate(string $messageOnUpdate)

### setMessageOnDelete
Установка текста сообщения отображаемого после удаления записи

    \SleepingOwl\Admin\Model\ModelConfiguration SleepingOwl\Admin\Model\ModelConfiguration::setMessageOnDelete(string $messageOnDelete)

### setMessageOnDestroy
Установка текста сообщения отображаемого после окончательного удаления записи (в случае если запись помечена как удаленная)

    \SleepingOwl\Admin\Model\ModelConfiguration SleepingOwl\Admin\Model\ModelConfiguration::setMessageOnDestroy(string $messageOnDestroy)

### setMessageOnRestore
Установка текста сообщения отображаемого после восстановления записи

    \SleepingOwl\Admin\Model\ModelConfiguration SleepingOwl\Admin\Model\ModelConfiguration::setMessageOnRestore(string $messageOnRestore)


### setControllerClass
Установка названия класса, который будет использован для работы с данными раздела

    \SleepingOwl\Admin\Model\ModelConfiguration SleepingOwl\Admin\Model\ModelConfiguration::setControllerClass(string $controllerClass)

### setIcon
Установка иконки для раздела (Будет отображаться в меню)

    \SleepingOwl\Admin\Model\ModelConfigurationManager SleepingOwl\Admin\Model\ModelConfigurationManager::setIcon(string $icon)


### addToNavigation
Добавление раздела в меню

    \SleepingOwl\Admin\Navigation\Page SleepingOwl\Admin\Model\ModelConfigurationManager::addToNavigation(integer $priority, string|\Closure|\KodiComponents\Navigation\Contracts\BadgeInterface $badge)

##### Arguments
* $priority **integer** Приоритет вывода в списке
* $badge **string|Closure|KodiComponents\Navigation\Contracts\BadgeInterface** Текст или класс бейджа, который отображается рядом с пунктом меню (Например кол-во записей)

### creating
Событие срабатываемое в процессе создания записи (В случае если метод возвращает false, запись не будет создана) 

     SleepingOwl\Admin\Model\ModelConfigurationManager::creating(Closure $callback)

##### Arguments
* $callback **Closure|null** В функцию будет передан `ModelConfigurationInterface $config` и `\Illuminate\Database\Eloquent\Model $model`

### created
Событие срабатываемое после создания записи

     SleepingOwl\Admin\Model\ModelConfigurationManager::created(Closure $callback)

##### Arguments
* $callback **Closure|null** В функцию будет передан `ModelConfigurationInterface $config` и `\Illuminate\Database\Eloquent\Model $model`

### updating
Событие срабатываемое в процессе обновления записи (В случае если метод возвращает false, запись не будет обновлена)

     SleepingOwl\Admin\Model\ModelConfigurationManager::updating(Closure $callback)

##### Arguments
* $callback **Closure|null** В функцию будет передан `ModelConfigurationInterface $config` и `\Illuminate\Database\Eloquent\Model $model`

### updated
Событие срабатываемое после обновления записи

     SleepingOwl\Admin\Model\ModelConfigurationManager::updated(Closure $callback)

### deleting
Событие срабатываемое в процессе удаления записи (В случае если метод возвращает false, запись не будет удалена)

     SleepingOwl\Admin\Model\ModelConfigurationManager::deleting(Closure $callback)

##### Arguments
* $callback **Closure|null** В функцию будет передан `ModelConfigurationInterface $config` и `\Illuminate\Database\Eloquent\Model $model`

### deleted
Событие срабатываемое после удаления записи

     SleepingOwl\Admin\Model\ModelConfigurationManager::deleted(Closure $callback)

##### Arguments
* $callback **Closure|null** В функцию будет передан `ModelConfigurationInterface $config` и `\Illuminate\Database\Eloquent\Model $model`

### restoring
Событие срабатываемое в процессе восстановления записи (В случае если метод возвращает false, запись не будет восстановлена)

     SleepingOwl\Admin\Model\ModelConfigurationManager::restoringClosure $callback)

##### Arguments
* $callback **Closure|null** В функцию будет передан `ModelConfigurationInterface $config` и `\Illuminate\Database\Eloquent\Model $model`

### restored
Событие срабатываемое после восстановления записи

     SleepingOwl\Admin\Model\ModelConfigurationManager::restored(Closure $callback)

##### Arguments
* $callback **Closure|null** В функцию будет передан `ModelConfigurationInterface $config` и `\Illuminate\Database\Eloquent\Model $model`
