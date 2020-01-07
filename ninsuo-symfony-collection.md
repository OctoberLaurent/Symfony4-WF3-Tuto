# Ninsuo symfony-collection

Installation avec npm :
```
npm install ninsuo/symfony-collection
```
déplacer les éléments suivants:

node_modules/symfony-collection/jquery.collection.js dans **App/assets/**

node_modules/symfony-collection/jquery.collection.html.twig wherever you want in your views in **app/templates/views**

Pour l'exemple nous avons une entité Ads, celle-ci peut contenir plusieurs images dont les liens sont dans une entité Pictures.

Créer dans un premier temps un formulaire pour l'entité Picture.
```
php bin/console make:form PicturesType
```

Créer dans un premier temps un formulaire pour l'entité Ads.
```
php bin/console make:form AdsType
```

```php
<?php
namespace App\Form;
use App\Entity\Pictures;
use Symfony\Component\Form\AbstractType;
use Symfony\Component\Form\FormBuilderInterface;
use Symfony\Component\OptionsResolver\OptionsResolver;
class ContentType extends AbstractType
{
    public function buildForm(FormBuilderInterface $builder, array $options)
    {
        $builder
            ->add('url')
        ;
    }
    public function configureOptions(OptionsResolver $resolver)
    {
        $resolver->setDefaults([
            'data_class' => Content::class,
        ]);
    }
}
```


