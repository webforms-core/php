![ ](https://github.com/user-attachments/assets/6a40f714-67aa-4b47-bc8e-97f485b9eb63)

# WebForms Core

## WFC for PHP

**WFC** is the PHP implementation of the **WebForms Core Commander**. It provides the `WebForms` class for generating WebForms Core commands from PHP server-side applications.

## WebForms Core Architecture

WebForms Core consists of two main parts:

- **Commander** — WebForms classes implemented for different programming languages. Commanders generate WebForms Core commands that describe changes to the HTML document.
- **Executor** — **WebFormsJS**, the client-side JavaScript library that receives and executes those commands in the browser.

The PHP `WFC` implementation provides the **Commander** for PHP, while WebFormsJS provides the common browser-side Executor.

The WebFormsJS Executor is loaded into the HTML document:

```xml
<script type="module" src="/script/web-forms.js"></script>
```

WebForms Core is a technology developed by [Elanat](https://elanat.net).

## Installation

### Installation via Composer

The package requires **PHP 8.0 or later**.

The PHP package is available on Packagist:

https://packagist.org/packages/webforms-core/php

Install using Composer:

```bash
composer require webforms-core/php
```

or

Download / Copy the File:

https://github.com/webforms-core/WebFormsClasses/tree/elanat_framework/php

# How to Work with WebForms Core in PHP

The following example demonstrates how to use WFC in a PHP server-side application.

```php
<?php
include 'WebForms.php';

use WebFormsCore\WebForms;
use WebFormsCore\InputPlace;

if (!empty($_POST['btn_SetBodyValue']))
{
    $Name = $_POST['txt_Name'];
    $BackgroundColor = $_POST['txt_BackgroundColor'];
    $FontSize = (int) $_POST['txt_FontSize'];

    $form = new WebForms();

    $form->setFontSize(InputPlace::tag('form'), $FontSize . 'px');
    $form->setBackgroundColor(
        InputPlace::tag('form'),
        $BackgroundColor
    );

    $form->setDisabled(
        InputPlace::name('btn_SetBodyValue'),
        true
    );

    $form->addTag(
        InputPlace::tag('form'),
        'h3'
    );

    $form->setText(
        InputPlace::tag('h3'),
        "Welcome " . $Name . "!"
    );

    echo $form->response();
    exit();
}
?>
<!DOCTYPE html>
<html>
<head>
    <title>Using WebForms Core</title>
    <script type="module" src="/script/web-forms.js"></script>
</head>
<body>
    <form method="post" action="/">

        <label for="txt_Name">Your Name</label>
        <input name="txt_Name" id="txt_Name" type="text">
        <br>

        <label for="txt_FontSize">Set Font Size</label>
        <input
            name="txt_FontSize"
            id="txt_FontSize"
            type="number"
            value="16"
            min="10"
            max="36"
        >
        <br>

        <label for="txt_BackgroundColor">
            Set Background Color
        </label>

        <input
            name="txt_BackgroundColor"
            id="txt_BackgroundColor"
            type="text"
        >
        <br>

        <input
            name="btn_SetBodyValue"
            type="submit"
            value="Click to send data"
        >
    </form>
</body>
</html>
```

## How the PHP Example Works

When the page is requested for the first time, PHP returns the complete HTML page. The page includes the WebFormsJS Executor:

```xml
<script type="module" src="/script/web-forms.js"></script>
```

When the form is submitted, PHP receives the submitted form data through `$_POST`.

If the submit button is included in the request, a `WebForms` instance is created:

```php
$form = new WebForms();
```

The application then generates WebForms Core commands:

```php
$form->setFontSize(
    InputPlace::tag('form'),
    $FontSize . 'px'
);

$form->setBackgroundColor(
    InputPlace::tag('form'),
    $BackgroundColor
);

$form->setDisabled(
    InputPlace::name('btn_SetBodyValue'),
    true
);
```

Additional commands modify the HTML document:

```php
$form->addTag(
    InputPlace::tag('form'),
    'h3'
);

$form->setText(
    InputPlace::tag('h3'),
    "Welcome " . $Name . "!"
);
```

Finally, the generated WebForms Core response is returned:

```php
echo $form->response();
exit();
```

WebFormsJS receives the response and executes the generated commands on the HTML DOM.

If the submit button is not included in the request, the application returns the initial HTML page normally.

This allows a PHP server-side application to control the client-side HTML document through WebForms Core commands without requiring JavaScript code for each individual UI operation.

## Sending Commands with the Initial HTML

When WebForms Core commands need to be sent together with the initial HTML document, you can use `exportToHtmlComment()` instead of `response()`.

`exportToHtmlComment()` embeds the generated WebForms Core commands in an HTML comment. WebFormsJS detects these commands when the HTML document is loaded and executes them in the browser.

For example:

```php
<?php
include 'WebForms.php';

use WebFormsCore\WebForms;
use WebFormsCore\InputPlace;

$form = new WebForms();

$form->setBackgroundColor(
    InputPlace::tag('body'),
    'lightblue'
);

?>
<!DOCTYPE html>
<html>
<head>
    <title>WebForms Core</title>
    <script type="module" src="/script/web-forms.js"></script>
</head>
<body>
    <h1>Hello World!</h1>
</body>
</html>
<?php echo $form->exportToHtmlComment(); ?>
```

In this case, the generated commands are embedded in the HTML response itself. When WebFormsJS loads, it detects the commands in the HTML comment and executes them on the DOM.

Use `response()` when returning WebForms Core commands as a response to a request. Use `exportToHtmlComment()` when the commands need to be included directly in the HTML document, such as during the initial page load.

## Commander and Executor

The `WebForms` class is the **Commander**. It generates WebForms Core commands that describe operations to be performed on the HTML document.

For example:

```php
$form = new WebForms();

$form->addTag(InputPlace::tag('form'), 'h3');
$form->setText(InputPlace::tag('h3'), 'Hello World!');
$form->setBackgroundColor(
    InputPlace::tag('form'),
    'lightblue'
);

echo $form->response();
```

The Commander does not directly manipulate the browser DOM. It generates the WebForms Core response that is sent to the client.

**WebFormsJS** is the **Executor**. It receives WebForms Core commands from the server and executes them in the browser.

```text
                    WebForms Core
                         │
              ┌──────────┴──────────┐
              │                     │
          Commander              Executor
              │                     │
          PHP WFC              WebFormsJS
              │                web-forms.js
              │                     │
     Generates commands ──► Executes commands
                                    │
                                    ▼
                               Browser DOM
```

The **Commander describes what should happen**.

The **Executor performs those commands in the browser**.

WebForms Core uses a **command-driven flow** and a **stateless server architecture**. The same WebForms Core programming model can be used across different programming languages and server-side environments.

WebForms Core is available for web development in multiple programming languages.

## WebFormsJS

WebFormsJS is the client-side JavaScript **Executor** of WebForms Core.

The latest WebFormsJS Executor is available here:

- WebFormsJS on Elanat: https://elanat.net/webforms-js
- WebFormsJS on GitHub: https://github.com/webforms-core/WebFormsJS

The npm package is also available:

https://www.npmjs.com/package/webformsjs

Install using npm:
```bash
npm install webformsjs
```
