# Création d'un service d'envoi de mail:

Avant toute chose pour les besoins nous allons installer la dépendance swiftmailer

```bash
composer require symfony/swiftmailer-bundle
```

**Céation d'un dossier 'Service' dans le dossier src.**

project>src>Service

dans ce dossier créons un fichier SendEmail.php

```php
<?php 

namespace App\Service;

use Twig\Environment;

class SendEmail{
/**
 * @var \Swift_Mailer
 */
private $mailer;

/**
 * @var Environment
 */
private $renderer;

public function __construct(\Swift_Mailer $mailer, Environment $renderer)
{
    $this->mailer = $mailer;
    $this->renderer = $renderer;
}
public function notify($title,$from, $to )
{

    $content = (new \Swift_Message($title))
        ->setFrom($from)
        ->setTo($to)
        ->setBody($this->renderer->render('emails/mail.html.twig', [
            'url' =>  $_SERVER['HTTP_HOST'],
        ]), 'text/html');
    
    $this->mailer->send($content);
}
}
```


