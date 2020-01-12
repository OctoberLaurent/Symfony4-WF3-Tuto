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
    // ---- > ProductController.php
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

Créer un fichier *add-collection-widget.js* pour gérer dynamiquement l'ajout et la suppression des champs.

```js
// add-collection-widget.js
$(document).ready(function () {
    
    $('.add-another-collection-widget').click(function (e) {
        var list = $($(this).attr('data-list-selector'));
        
        var counter = list.data('widget-counter') || list.children().length;
  
        var newWidget = list.attr('data-prototype');        

        newWidget = newWidget.replace(/__name__/g, counter);
        
        newWidget += '<button type="button" class="delete btn btn-danger">Delete</button>';
        
        counter++;

        list.data('widget-counter', counter);

        var newElem = $(list.attr('data-widget-tags')).html(newWidget);
        newElem.appendTo(list);

        handleDelete()
    });

        collectionHolder = $('ul#picture-fields-list');
        collectionHolder.find('li').each(function() {
        
        addPictureFormDeleteLink($(this));
    });

    function handleDelete(){

        var elements = document.getElementsByClassName('delete');
        elements.forEach(element => {
             element.addEventListener('click', function (event) {
                    this.parentNode.remove();
                    });
                                    });
    }
        
    function addPictureFormDeleteLink($pictureFormLi) {
        var $removeFormButton = $('<button type="button" class="delete btn btn-danger">Delete</button>');
        $pictureFormLi.append($removeFormButton);

        $removeFormButton.on('click', function(e) {
            // remove the li for the Picture form
            $pictureFormLi.remove();
        });
    }

});

```
Voici le fichier _form.html.twig modifié.
```twig
<div class="container">
    {{ form_start(form) }}
    <div class="row">
        <div class="col-6">
            {{ form_label(form.name) }}
        </div>
        <div class="col-6">
            {{ form_widget(form.name) }}
            {{ form_errors(form.name) }}
        </div>
        <div class="col-6">
            {{ form_label(form.description) }}
        </div>
        <div class="col-6">
            {{ form_widget(form.description) }}
            {{ form_errors(form.description) }}
        </div>
            <div class="col-6">
            {{ form_label(form.price) }}
        </div>
        <div class="col-6">
            {{ form_widget(form.price) }}
            {{ form_errors(form.price) }}
        </div>
    </div>

        {{ form_row(form.name) }}
        {{ form_errors(form.name) }}
        
        <ul id="picture-fields-list"
            data-prototype="{{ form_widget(form.picture.vars.prototype)|e }}"
            data-widget-tags="{{ '<li></li>'|e }}"
            data-widget-counter="{{ form.picture|length }}">
        {% for pictureField in form.picture %}
            <li>
                    {{ form_row(pictureField.name) }}
                    {{ form_row(pictureField.url) }}
            </li>
        {% endfor %}
        </ul>

        <button type="button"
            class="add-another-collection-widget btn btn-primary"
            data-list-selector="#picture-fields-list">Add another picture</button>

        <button class="btn btn-success">{{ button_label|default('Save') }}</button>
</div>
{{ form_end(form) }}
```
Dans l'entité product.php ajouter en annotation à l'attribut picture :  cascade={"remove"} .

```php
    /**
     * @ORM\OneToMany(targetEntity="App\Entity\Pictures", mappedBy="product", cascade={"remove"} )
     */
    private $picture;
```

Rendez-vous sur le [le dépôt](https://gitlab.com/OctoberLaurent/collection) 
