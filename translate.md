# Translate :

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

[lien vers la documentation sur la création d'un fichier dictionnaire ](https://symfony.com/doc/current/translation/message_format.html)
