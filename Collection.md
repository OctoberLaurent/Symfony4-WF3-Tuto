# Collection

Dans un premier temps, configurer le fichier .env et créer la base de données.

```
php bin/console doctrine:database:create
```
Créer l'entité Picture
```
php bin/console make:entity Picture
```

- name _string 255_
- url _string 255_

Créer l'entité Product
```
php bin/console make:entity Product
```

- name _string 255_
- description _string 255_
- relation _OneToMany_ Picture

Pour notre exemple, et la démonstration, nous allons utiliser un make:crud sur l'entité Product

```
php bin/console make:crud Product
```

Pour nous permetre de d'ajouter des champs imbriqués nous allons créer un formulaire pour notre picture.

```
php bin/console make:form PictureType
```
Migrons les bases de données.
```
php bin/console make:migration
php bin/console doctrine:migrations:migrate
```




