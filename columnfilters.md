# Column filters

Column filters are used to filter out lists. 

In case of using `AdminDisplay::datatables()` search is executed, using library `datatable`.

**Usage example:**

```php
$display = AdminDisplay::datatables();

$display->setColumnFilters([
  null, // Do not search by first column
  
  // Text search
  AdminColumnFilter::text()->setPlaceholder('Full Name'),
  
  // Search by value range
  AdminColumnFilter::range()->setFrom(
      AdminColumnFilter::text()->setPlaceholder('From')
  )->setTo(
      AdminColumnFilter::text()->setPlaceholder('To')
  ),
  
  // Serch by date range
  AdminColumnFilter::range()->setFrom(
      AdminColumnFilter::date()->setPlaceholder('From Date')->setFormat('d.m.Y')
  )->setTo(
      AdminColumnFilter::date()->setPlaceholder('To Date')->setFormat('d.m.Y')
  ),
  
  // Search by dropdown list values
  AdminColumnFilter::select()->setPlaceholder('Country')->setModel(new Country)->setDisplay('title')
]);
```

**When you list column filters, number of search definitions must match table columns 
(if you don't need search by one of columns, you should pass `null` as corresponding placeholder) 
and order should be the same**

# API

Column filter classes use traits:
 - [HtmlAttributes](html_attributes.md), to configure HTML attributes.
 - [Assets](assets_trait.md), to include assets.

## Methods, available in all filters

### setOperator
Operator, used in filtering operation. Default: `equal`

```php
  static::setOperator(string $operator): return self
```

#### List of applicable comparison operators:

  - `SleepingOwl\Admin\Contracts\FilterInterface::EQUAL = equal` - equality
  - `SleepingOwl\Admin\Contracts\FilterInterface::NOT_EQUAL = not_equal` - not equal
  - `SleepingOwl\Admin\Contracts\FilterInterface::LESS = less` - less
  - `SleepingOwl\Admin\Contracts\FilterInterface::LESS_OR_EQUAL = less_or_equal` - less or equal
  - `SleepingOwl\Admin\Contracts\FilterInterface::GREATER = greater` - greater
  - `SleepingOwl\Admin\Contracts\FilterInterface::GREATER_OR_EQUAL = greater_or_equal` - greater or equal
  - `SleepingOwl\Admin\Contracts\FilterInterface::BEGINS_WITH = begins_with` - string begins with
  - `SleepingOwl\Admin\Contracts\FilterInterface::NOT_BEGINS_WITH = not_begins_with`- string does not begin with
  - `SleepingOwl\Admin\Contracts\FilterInterface::CONTAINS = contains` - string contains
  - `SleepingOwl\Admin\Contracts\FilterInterface::NOT_CONTAINS = not_contains` - string does not contain
  - `SleepingOwl\Admin\Contracts\FilterInterface::ENDS_WITH = ends_with` - string ends with
  - `SleepingOwl\Admin\Contracts\FilterInterface::NOT_ENDS_WITH = not_ends_with` - string does not end with
  - `SleepingOwl\Admin\Contracts\FilterInterface::BETWEEN = between` - between (values are listed over `,`)
  - `SleepingOwl\Admin\Contracts\FilterInterface::NOT_BETWEEN = not_between` - not between (values are listed over `,`)
  - `SleepingOwl\Admin\Contracts\FilterInterface::IN = in` - one of (values are listed over `,`)
  - `SleepingOwl\Admin\Contracts\FilterInterface::NOT_IN = not_in` - not one of (values are listed over `,`)


## Text
Filter data by text string

```php
AdminColumnFilter::text()->setPlaceholder('Full Name')->setOperator(\SleepingOwl\Admin\Contracts\FilterInterface::CONTAINS)
```

### setPlaceholder
Set a placeholder for an input.

```php
  static::setPlaceholder(string $placeholder): return self
```


## Date
Filter data by date

```php
AdminColumnFilter::date()->setPlaceholder('Date')->setFormat('d.m.Y')
```

### setFormat
Set date filter, which is expected in input field

```php
  static::setFormat(string $format): return self
```

### setPickerFormat
Set date picker format, used in input, and valid for Javascript

```php
  static::setPickerFormat(string $pickerFormat): return self
```

### setSearchFormat
Set date format, used to store and search date in DB

```php
  static::setSearchFormat(string $searchFormat): return self
```

### setWidth
Input field width

```php
  static::setWidth(int $width): return self
```

## Select
Filter data by drop down list values


## Range
Filter data by values range.


### setFrom
Set field for range start

```php
  static::setFrom(ColumnFilterInterface $from): return self
```

### setTo
Set field for range end

```php
  static::setTo(ColumnFilterInterface $from): return self
```
