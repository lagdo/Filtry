Filtry
==============

A package to filter and sanitize input data in Laravel 4 or as a standalone package.
This is perfect to use with the Laravel 4 Validation class to filter data before validation.
You can easily extend and create your own custom filters.

##Installation
Install through composer. Add require `mjarestad/filtry` to your `composer.json`

    "require": {
        "mjarestad/filtry": "dev-master"
    }
    
###Laravel 4

Add the ServiceProvider to the providers array in `app/config/app.php`

    'Mjarestad\Filtry\FiltryServiceProvider',
    
Add the Facade to the aliases array in `app/config/app.php`

    'Filtry'  => 'Mjarestad\Filtry\Facades\Filtry',

##Usage

###Laravel 4

Add a the filters property to your Eloquent Model or anywhere else you prefer.

    class Post extends Eloquent {
    
        public static $filters = array(
            'author' => 'trim|ucwords',
            'slug'   => 'trim|slug'
        );
        
        public static $rules = array(
            'author' => 'required',
            'slug'   => 'required'
        );
        
    }
    
In your controller or service call `Filtry::make()` and provide the data to filter and your filters array.

    $filtry = Filtry::make(Input::all(), Post::$filters);
    
To get the filtered value use `$filtry->getFiltered()`

    $validator = Validator::make($filtry->getFiltered(), Post::$rules);
    
To get the unfiltered values, use:

    $filtry->getOld();
    
Every method can be used to filter a single value.

    Filtry::trim('some string');
    Filtry::slug('some string');
    Filtry::snakeCase('some string');
    
###Standalone

    $filters = array(
        'author' => 'trim|ucwords',
        'slug'   => 'trim|slug'
    );
    
    $data = array(
        'author' => 'John Doe',
        'slug'   => 'My post title',
    );
    
    $filtry = new Mjarestad\Filtry\Filtry;
    $filtry->make($data, $filters);
    $filteredData = $filtry->getFiltered();
    
##Create custom filters

###Laravel 4

Extend with custom filters to use in `Filtry::make()` or as dynamic methods.

    Filtry::extend('my_custom_filter', function($data){
        return str_replace('-', '_', $data);
    });
    
Call the extended filter dynamically

    Filtry::myCustomFilter('some-custom-string');

###Standalone

    $filtry = new Mjarestad\Filtry\Filtry;

    $filtry->extend('my_custom_filter', function($data){
        return str_replace('-', '_', $data);
    });

    $filtry->myCustomFilter('some-custom-string');
    
##Available filters

###Core PHP filters

* trim
* ltrim
* rtrim
* lower (strtolower)
* upper (strtoupper)
* ucfirst
* ucwords
* stripslashes

###Custom filters

* xss_clean - clean string with htmlspecialchars
* remove_whitespace - removes all white spaces
* slug - makes string url-friendly
* prep_url - adds http:// if not present

###Laravel 4 filters

* snake_case
* camel_case
* studly_case

> Theese filters can still be used in a non-Laravel application.
