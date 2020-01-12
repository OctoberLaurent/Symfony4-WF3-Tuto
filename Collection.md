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
Nous avons 2 entités, une entité product et une entité picture. 
Nous allons permettre d'ajouter plusieurs "pictures" à "product", via un formulaire.
Il sera aussi possible de supprimer des "pictures" à "product" dans le formulaire d'édition ou dans le formualaire de création si vous avez ajouter l'élément par erreur.

Modification de notre formulaire Product.
```php
<?php

namespace App\Form;

use App\Entity\Product;
use App\Form\PicturesType;
use Symfony\Component\Form\AbstractType;
use Symfony\Component\Form\FormBuilderInterface;
use Symfony\Component\OptionsResolver\OptionsResolver;
use Symfony\Component\Form\Extension\Core\Type\TextType;
use Symfony\Component\Form\Extension\Core\Type\CollectionType;
use Symfony\Component\Form\Extension\Core\Type\NumberType;

class ProductType extends AbstractType
{
    public function buildForm(FormBuilderInterface $builder, array $options)
    {
        $builder
            ->add('name', TextType::class)
            ->add('description', TextType::class)
            ->add('price', NumberType::class)
            ->add('picture', CollectionType::class , [
                'entry_type' => PicturesType::class,
                'entry_options' => ['label' => false],
                'allow_add' => true,
                'allow_delete' => true,
                'attr' => [ 'class' => 'form-group']
            ]
            )
        ;
    }

    public function configureOptions(OptionsResolver $resolver)
    {
        $resolver->setDefaults([
            'data_class' => Product::class,
        ]);
    }
}

```
Modification du formulaire pour Picture

```php
<?php

namespace App\Form;

use App\Entity\Pictures;

use Symfony\Component\Form\AbstractType;
use Symfony\Component\Form\Extension\Core\Type\ButtonType;
use Symfony\Component\Form\FormBuilderInterface;
use Symfony\Component\OptionsResolver\OptionsResolver;
use Symfony\Component\Form\Extension\Core\Type\TextType;

class PicturesType extends AbstractType
{
    public function buildForm(FormBuilderInterface $builder, array $options)
    {
        $builder
            ->add('name', TextType::class, [
                'label' => 'Nom de l\'image',
                'attr' => [ 'class' => 'form-control']
            ])
            ->add('url', TextType::class, [
                'label' => 'adresse de l\'image',
                'attr' => [ 'class' => 'form-control']
            ])
        ;
    }

    public function configureOptions(OptionsResolver $resolver)
    {
        $resolver->setDefaults([
            'data_class' => Pictures::class,
        ]);
    }
}

```
Modification du controlleur pour la création d'un produit .

```php
    
    // ---- > ProductController.php
    
    /**
     * @Route("/new", name="product_new", methods={"GET","POST"})
     */
    public function new(Request $request): Response
    {
        // create a new product
        $product = new Product();
        // generate a form 
        $form = $this->createForm(ProductType::class, $product);
        // 
        $form->handleRequest($request);

        if ($form->isSubmitted() && $form->isValid()) {
            $entityManager = $this->getDoctrine()->getManager();
                //*** Add this ***
                // loop on all picture elements and persist all
                foreach($product->getPicture() as $picture) {
                $picture->setProduct($product);
                $entityManager->persist($picture);
                //*** ****** ***
            }
            // persist product
            $entityManager->persist($product);
            // Add product in database in unclude all picture
            $entityManager->flush();

            return $this->redirectToRoute('product_index');
        }

        return $this->render('product/new.html.twig', [
            'product' => $product,
            'form' => $form->createView(),
        ]);
    }
```

Modification du controlleur pour la modification d'un produit .

```php
    /**
     * @Route("/{id}/edit", name="product_edit", methods={"GET","POST"})
     */
    public function edit(Request $request, Product $product): Response
    {
        $originalPictures = new ArrayCollection();
       
        foreach ($product->getPicture() as $picture) {
        $originalPictures->add($picture);
        }

        $form = $this->createForm(ProductType::class, $product);
        $form->handleRequest($request);

        if ($form->isSubmitted() && $form->isValid()) {
            
            $entityManager = $this->getDoctrine()->getManager();

                foreach($product->getPicture() as $picture) {
                $picture->setProduct($product);
                
                $entityManager->persist($picture);
                }
                
                //*** Add this ***
                foreach ($originalPictures as $picture) {

                    if (false === $product->getPicture()->contains($picture)) {

                    $picture->setProduct(null);
                    
                    $entityManager->persist($picture);
                    
                    }
                }
                //*** ****** ***
               
                $entityManager->persist($product);
                $entityManager->flush();

                return $this->redirectToRoute('product_index');

    }
```




