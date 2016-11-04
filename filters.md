# Data filters
Extension of [Data displays](displays.md) 

Used to filter data by query parameters. It's a variation of data filters without form.

I.e., let's say we have a news table with different categories (`category_id`) 
and you want to display data from a particular category:

```php
$display = AdminDisplay::table()
    ->setFilters(
        AdminDisplayFilter::field('category_id')->setTitle('Category ID [:value]')
    )
    ->setColumns(
        AdminColumn::link('title', 'Title'),
        AdminColumn::link('category', 'Category'),
        AdminColumn::datetime('created_at', 'Publish date')->setWidth('150px')
    )->paginate(20);
```

This example shows basic filter usage. Now, if you put in browser's URL `?category_id=1`, 
a query condition will be added to query `where category_id = 1` and table header will display string `Category ID [1]`.

### Filters types:
 - Field filter
 - Scope filter *[eloquent scopes](https://laravel.com/docs/5.3/eloquent#query-scopes))
 - Custom filter

## API (Methods, implemented in all filters)

#### setName
Set key name, which will be used as field name to modify data query.

    SleepingOwl\Admin\Display\Filter\FilterBase::setName(string $name): return self
    
#### setAlias
Set alias name, which will be used to retrieve value from query.

    SleepingOwl\Admin\Display\Filter\FilterBase::setAlias(string $alias): return self
    
```php
AdminDisplayFilter::field('category_id')->setAlias('category'); // ?category=1
```

#### setTitle
Title, if this filter was applied. I.e. as soon, as filter was applied on top of 
results list there will be a filter title. 
If multiple filters were applied, they will be separated by ` | `

    SleepingOwl\Admin\Display\Filter\FilterBase::setTitle(\Closure|string $title): return self
    
```php
AdminDisplayFilter::field('category_id')->setTitle('Category ID [:value]'); 

// or

AdminDisplayFilter::field('category_id')->setTitle(function($value) {
    return "Category ID [{$value}]";
}); 
```

#### setValue
Force set value, applied to filter. **If you force value, filter will ignore value in query parameters**

    SleepingOwl\Admin\Display\Filter\FilterBase::setValue(mixed $value): return self
    

```php
AdminDisplayFilter::field('category_id')->setValue(1);
```

## Filter by field
This filter is bound to model field.

```php
AdminDisplayFilter::field('category_id');
```

### API

#### setOperator
Set comparison operator. Instead of regular equality you can set, how filter should compare value. 

    SleepingOwl\Admin\Display\Filter\FilterBase::setOperator(string $operator): return self
    
 - `equal` - `column = value`
 - `not_equal` - `column != value`
 - `less` - `column < value`
 - `less_or_equal` - `column <= value`
 - `greater` - `column > value`
 - `greater_or_equal` - `column >= value`
 - `begins_with` - `column like value%`
 - `not_begins_with` - `column not like value%`
 - `contains` - `column like %value%`
 - `not_contains` - `column not like %value%`
 - `ends_with` - `column like %value`
 - `not_ends_with` - `column not like %value`
 - `is_empty` - `column = ''`
 - `is_not_empty` - `column != ''`
 - `is_null` - `column is null`
 - `is_not_null` - `column is not null`
 - `between` - `column between value`
 - `not_between` - `column not between value`
 - `in` - `column in value`
 - `not_in` - `column not in value`

```php
AdminDisplayFilter::field('category_id')->setOperator('in'); // ?category_id[]=1&category_id[]=2&category_id[]=5
```

## Filter by [eloquent scopes](https://laravel.com/docs/5.3/eloquent#query-scopes)
This filter will apply `scope` to your query. 
Let's say, you have a news list, and you want to filter your records by `scope`, 
defined in model `App\Post`, which you use to query list.

```php
<?php

namespace App;
class Post extends Model
{
    ...
    
    /**
     * @param     $query
     *
     * @return \Illuminate\Database\Eloquent\Builder
     */
    public function scopeLatest($query)
    {
        return $query->orderBy('created_at', 'desc');
    }
    
    /**
     * @param     $query
     *
     * @return \Illuminate\Database\Eloquent\Builder
     */
    public function scopeType($query, $type)
    {
        return $query->where('type', $type);
    }
}
```

```php
$display = AdminDisplay::table()
    ->setFilters(
        AdminDisplayFilter::scope('latest'); // ?latest
        AdminDisplayFilter::scope('type'); // ?type=news | ?latest&type=news
    );
```

## Custom filter
Can be used if you want to implement a filter by custom query

```php
AdminDisplayFilter::custom('custom_filter')->setCallback(function($query, $value) {
    $query->where('myField', $value);
}); // ?custom_filter=test
```

