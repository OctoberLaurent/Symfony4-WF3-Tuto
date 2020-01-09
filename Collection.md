# Collection

```
php bin/console make:entity Picture
```

- name _string 255_
- url _string 255_

```
php bin/console make:entity Product
```

- name _string 255_
- description _string 255_
- relation _OneToMany_ Picture

Pour notre exemple, nous allons utiliser un make Crud sur l'entité Product

```
php bin/console make:crud Product
```

Pour nous permetre de d'ajouter des champs imbiqués nous allons créer un formulaire pour notre picture.

```
php bin/console make:form PictureType
```


