# Steroids v0.7.0

[![Latest Stable Version](https://poser.pugx.org/pragmarx/steroids/v/stable.png)](https://packagist.org/packages/pragmarx/steroids) [![License](https://poser.pugx.org/pragmarx/steroids/license.png)](https://packagist.org/packages/pragmarx/steroids)

## Laravel 4 Blade on Steroids

This package provides some aditional features to Laravel Blade:

### Automatic command generation

Create a file named `<command>.blade.php` in the templates directory and it automatically becomes a blade command.

Take the file

    default\css.blade.php

Whaving the contents:

    <link rel="stylesheet" type="text/css" media="screen" href="@_1">

Hackers can now use the command

    @css('/css/bootstrap.css')

In their blade templates to generate:

    <link rel="stylesheet" type="text/css" media="screen" href="/css/bootstrap.css">

### Subtemplating

Every subdirectory in your templates dir creates a new level of templating in your commands, so this tree:

```
├── default
│   ├── input.blade.php
│   ├── js.blade.php
│   └── php.blade.php
│   └── text.blade.php
├── bs
│   └── v2
│   │   ├── input.blade.php
│   │   └── form.blade.php
│   │   └── model.blade.php
│   ├── input.blade.php
│   └── form.blade.php
```

Would give you the following commands:

    @input()
    @js()
    @php()
    @text()

    @bs.input()
    @bs.form()

    @bs.v2.input()
    @bs.v2.form()
    @bs.v2.model()

### Block commands

Let's take the (silly, I know! :) `@php` (file `php.blade.php`) command as an example of block:

    @php
        $title = 'subscribe';
    @@

Note that a block ends with `@@` and you can have as many nested blocks as you want. This is the `@php` command's source code:

    <?php 
        @_BODY;
    ?>

Is that simple, to create a block command you just have to add the `@_BODY` identifier in any part of your command.

### Extending commands

You can create an `@input` command:

    <input type="@_1" @_ATTRIBUTES />

And use it to create a 

`@text`:
    
    @input(text,@_PARAMETERS)

`@email`:

    @input(email,@_PARAMETERS)

and `@password` commands:

    @input(password,@_PARAMETERS)

### HTML Attributes, Local Variables and Positional Parameters

You can dinamically create and send any number of paramenters to your commands:

#### HTML Attributes

Take @input as an example:

    @input(type=email,class=form-control,id=example,placeholder=Enter email)

Having this template

    <input @_ATTRIBUTES />

It will generate this tag:

    <input type="email" class="form-control" id="example" placeholder="Enter email">

#### Local Variables

Use a hash to define a local variable:

    @input(#type=email,class=form-control,id=example,placeholder=Enter email)

And you access it by using the variable identifier `@_`:

    <input type="@_type" @_ATTRIBUTES />

#### Positional Parameters

You also can access any of yours parameter by the number, let's set the type of input as the first one:

    @input(email,class=form-control,id=example,placeholder=Enter email)

Then you just have to use the variable identifier followed by the parameter number:

    <input type="@_1" @_ATTRIBUTES />

Another example is the Form::model(), provided by `@model`, this is the template

    {{ Form::model(@_1, $options) }}
        @_BODY
    {{ Form::close() }}

And in your view you just have to:

    @model($user,url=/profile)
        ... your controls ...
    @@

### Special functions

#### ->has

If you need to know if a variable was set you can use the `->has` function:

    @if (@_label->has) 
        <label class="label">@_label</label>
    @endif
    <input type="@_1" @_ATTRIBUTES />

The `->has` function will return `true` or `false`, and then your view (in PHP) would probably look like this:

    <?php if (false): ?>
        <label class="label"></label>
    <?php endif; ?>
    <input type="email" ... />

Steroids comes with some examples:

#### ->bare

If you need to access one of your HTML attributes you can use the `->bare` function:

    <input type="@_1" class="@_class->bare" />

### Demimiters and Quotation marks

As delimiters of yours parameters you can use `,` or `;`:

    @input(email,class=form-control,id=example,placeholder=Enter email)

    @input(email;class=form-control;id=example;placeholder=Enter email)

You don't need to use quotation marks (single `'` or double `"`), unless you need to use any of those delimiters in your strings:

    @input(email,placeholder="Hello, World!")

### Examples

Steroids comes with some examples, but you can get crazy and create as many as you wish:

```
├── default
│   ├── css.blade.php
│   ├── form.blade.php
│   ├── h.blade.php
│   ├── input.blade.php
│   ├── js.blade.php
│   ├── model.blade.php
│   ├── p.blade.php
│   ├── php.blade.php
│   ├── row.blade.php
│   └── text.blade.php
├── bs
│   ├── md.blade.php
│   └── xs.blade.php
```

### Easy creation of partials

Sometimes creating s simple box can be as complicated as:

    <div class="row">
        <article class="col-sm-12 col-md-12 col-lg-12">
                <div>
                    <div class="jarviswidget-editbox">
                        @editbox('your name goes here')
                    </div>

                    <div class="widget-body no-padding">
                        @_BODY
                    </div>
                </div>
            </div>
        </article>
    </div>

But after Steroids, you just need to do this in your code:

    @box
        And do whatever you need inside it!
    @@

### Artisan Commands

Steroids has two artisan commands:

`steroids:templates` - to copy the examples to your `app/config/package` folder

    php artisan steroids:templates

`view:clear` - to clear you views cache

    php artisan view:clear

### Using the Facade directly

To compile a view using Steroids, you just have to:

     return Steroids::inject('@input(type=email,name=email,class=form-control)')

### Installation

#### Requirements

- Laravel 4.1+
- Composer >= 2014-01-07 - This is a PSR-4 package

#### Installing

Require the Steroids package:

    composer require pragmarx/steroids dev-master

Add the service provider to your app/config/app.php:

    'PragmaRX\Steroids\Vendor\Laravel\ServiceProvider',

To publish the configuration file you'll have to:

    php artisan config:publish pragmarx/steroids

Copy the templates examples to your app folder: 

    php artisan steroids:templates


### TODO

- Invalidate main templates when a Steroids command changes

- Add more tests

### Author

[Antonio Carlos Ribeiro](http://twitter.com/iantonioribeiro) 

### License

Steroids is licensed under the MIT License - see the `LICENSE` file for details

### Contributing

Pull requests and issues are more than welcome.