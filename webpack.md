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