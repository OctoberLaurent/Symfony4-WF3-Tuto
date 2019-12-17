#Forcer le nom d'une table.

Dans le cas d'un ManyToMany par exemple ou on veut renomer une table d'une façon plus parlante.
**@ORM\JoinTable(name="favorites")**

```php
    /**
     * @ORM\ManyToMany(targetEntity="App\Entity\Ads", inversedBy="users")
     * @ORM\JoinTable(name="favorites")
     */
    private $favorites;
```
