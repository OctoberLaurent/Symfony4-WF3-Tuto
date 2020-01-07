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

Créer dans un premier temps un formulaire pour l'entité Ads.
```
php bin/console make:form AdsType
```

```php
<?php
namespace App\Form;
use App\Entity\User;
use App\Entity\Boxes;
use App\Entity\Rooms;
use App\Entity\Content;
use Symfony\Component\Form\AbstractType;
use Symfony\Component\Form\FormBuilderInterface;
use Symfony\Bridge\Doctrine\Form\Type\EntityType;
use Symfony\Component\OptionsResolver\OptionsResolver;
use Symfony\Component\Form\Extension\Core\Type\TextType;
use Symfony\Component\Form\Extension\Core\Type\NumberType;
use Symfony\Component\Form\Extension\Core\Type\CollectionType;
class BoxesType extends AbstractType
{
    public function buildForm(FormBuilderInterface $builder, array $options)
    {
        $builder
            //....
            ->add('content', CollectionType::class, [
            'entry_type' => PictureType::class,
            'allow_delete' => true,
            'allow_add' => true,
            'prototype' => true,
            'attr' => [
                'class' => 'picture',
            ]    
        ]);
        ;
    }
    public function configureOptions(OptionsResolver $resolver)
    {
        $resolver->setDefaults([
            'data_class' => Boxes::class,
        ]);
    }
}
```




