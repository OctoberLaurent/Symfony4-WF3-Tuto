# Installation de Webpack encore

```
composer require sumfony/flex
composer require encore
yarn install
```
Installation de Bootstrap et Jquery 
```bash
yarn add bootstrap --dev
yarn add jquery popper.js --dev
yarn add jquery
```
```js
// assets>js>app.js
/*
 * Welcome to your app's main JavaScript file!
 *
 * We recommend including the built version of this JavaScript file
 * (and its CSS file) in your base layout (base.html.twig).
 */

// any CSS you require will output into a single css file (app.css in this case)
require('../css/app.css');
require('bootstrap');
require('bootstrap/dist/css/bootstrap.css');

// Need jQuery? Install it with "yarn add jquery", then uncomment to require it.
// const $ = require('jquery');

console.log('Hello Webpack Encore! Edit me in assets/js/app.js');

```


```php
{# templates/base.html.twig #}
<!DOCTYPE html>
<html>
    <head>
        <!-- ... -->

        {% block stylesheets %}
            {# 'app' must match the first argument to addEntry() in webpack.config.js #}
            {{ encore_entry_link_tags('app') }}

            <!-- Renders a link tag (if your module requires any CSS)
                 <link rel="stylesheet" href="/build/app.css"> -->
        {% endblock %}
    </head>
    <body>
        <!-- ... -->

        {% block javascripts %}
            {{ encore_entry_script_tags('app') }}

            <!-- Renders app.js & a webpack runtime.js file
                <script src="/build/runtime.js"></script>
                <script src="/build/app.js"></script> -->
        {% endblock %}
    </body>
</html>
```

- "yarn watch".
