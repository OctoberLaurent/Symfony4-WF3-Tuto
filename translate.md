# Translate :

[Documentation officielle](https://symfony.com/doc/current/translation.html)

Installation des dépendances
```
composer require symfony/translation
```
ajout de la configuration dans le fichier config.

```yaml
# config/packages/translation.yaml
framework:
    default_locale: 'en'
    translator:
        default_path: '%kernel.project_dir%/translations'
```

[Lien vers la documentation sur la création d'un fichier dictionnaire ](https://symfony.com/doc/current/translation/message_format.html)

```yaml
# messages.en.yaml
Hello: 'Bojour'
```

Exemple dans twig :
```twig
<h1>{% trans %}Symfony is great!{% endtrans %}</h1>
```

Exemple dans un controleur :

```php
$translated = $translator->trans('Hello '.$name);
```
