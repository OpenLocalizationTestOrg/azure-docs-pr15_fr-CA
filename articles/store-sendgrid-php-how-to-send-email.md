<properties 
    pageTitle="Comment faire pour utiliser le service de messagerie électronique (PHP) SendGrid | Microsoft Azure" 
    description="Découvrez comment envoyer des e-mails avec le service de messagerie SendGrid sur Azure. Exemples de code écrits en PHP." 
    documentationCenter="php" 
    services="" 
    manager="sendgrid" 
    editor="mollybos" 
    authors="thinkingserious"/>

<tags 
    ms.service="multiple" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="PHP" 
    ms.topic="article" 
    ms.date="10/30/2014" 
    ms.author="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork; matt.bernier@sendgrid.com"/>
# <a name="how-to-use-the-sendgrid-email-service-from-php"></a>Comment faire pour utiliser le Service de messagerie de SendGrid à partir de PHP

Ce guide montre comment effectuer des tâches de programmation courantes avec le service de messagerie SendGrid sur Azure. Les exemples sont écrits en PHP.
Les scénarios présentés incluent la **construction d’e-mail**, **envoi de courrier électronique**et **l’Ajout de pièces jointes**. Pour plus d’informations sur SendGrid et envoi de courrier électronique, consultez la section [Étapes suivantes](#next-steps) .

## <a name="what-is-the-sendgrid-email-service"></a>Quel est le Service de messagerie SendGrid ?

SendGrid est un [service de messagerie basés sur le cloud] qui fournit fiable [des e-mails transactionnelle], d’évolutivité et analytique en temps réel avec une API flexible qui facilitent l’intégration personnalisée. Scénarios d’utilisation communs SendGrid sont les suivantes :

-   Envoi d’accusés de réception automatiquement aux clients
-   Administration de distribution répertorie pour l’envoi de clients e-prospectus mensuels et des offres spéciales
-   Collecte des métriques en temps réel pour des éléments tels que les e-mails bloqués et de réactivité pour les clients
-   Génération de rapports pour aider à identifier les tendances
-   Transfert des demandes des clients
- Notifications par courrier électronique à partir de votre application.

Pour plus d’informations, consultez [https://sendgrid.com][].

## <a name="create-a-sendgrid-account"></a>Créer un compte SendGrid

[AZURE.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="using-sendgrid-from-your-php-application"></a>À l’aide de SendGrid à partir de votre Application PHP

À l’aide de SendGrid dans une application PHP d’Azure ne requiert aucun spéciaux de configuration ou de codage. Car SendGrid est un service, il est accessible de la même façon à partir d’une application en nuage que possible à partir d’une application sur site.

## <a name="how-to-send-an-email"></a>Comment : envoyer un courrier électronique

Vous pouvez envoyer des e-mails à l’aide de SMTP ou l’API de Web fournie par SendGrid.

### <a name="smtp-api"></a>API DE SMTP

Pour envoyer un e-mail à l’aide de l’API de SMTP SendGrid, utilisez *Swift Mailer*, une bibliothèque basée sur un composant pour l’envoi de messages électroniques à partir d’applications PHP. Vous pouvez télécharger la bibliothèque *Swift du service de publipostage* à partir de [http://swiftmailer.org/download][] v5.3.0 (utilisez [Composer] pour installer le service de publipostage Swift). Envoi de courrier électronique avec la bibliothèque implique la création d’instances de la <span class="auto-style2">Swift\_SmtpTransport</span>, <span class="auto-style2">Swift\_Mailer</span>, et <span class="auto-style2">Swift\_Message</span> classes, en définissant les propriétés appropriées et en appelant le <span class="auto-style2">Swift\_Mailer::send</span> méthode.

    <?php
     include_once "vendor/autoload.php";
     /*
      * Create the body of the message (a plain-text and an HTML version).
      * $text is your plain-text email
      * $html is your html version of the email
      * If the reciever is able to view html emails then only the html
      * email will be displayed
      */ 
     $text = "Hi!\nHow are you?\n";
     $html = "<html>
           <head></head>
           <body>
               <p>Hi!<br>
                   How are you?<br>
               </p>
           </body>
           </html>";
     // This is your From email address
     $from = array('someone@example.com' => 'Name To Appear');
     // Email recipients
     $to = array(
           'john@contoso.com'=>'Destination 1 Name',
           'anna@contoso.com'=>'Destination 2 Name'
     );
     // Email subject
     $subject = 'Example PHP Email';

     // Login credentials
     $username = 'yoursendgridusername';
     $password = 'yourpassword';
     
     // Setup Swift mailer parameters
     $transport = Swift_SmtpTransport::newInstance('smtp.sendgrid.net', 587);
     $transport->setUsername($username);
     $transport->setPassword($password);
     $swift = Swift_Mailer::newInstance($transport);

     // Create a message (subject)
     $message = new Swift_Message($subject);

     // attach the body of the email
     $message->setFrom($from);
     $message->setBody($html, 'text/html');
     $message->setTo($to);
     $message->addPart($text, 'text/plain');
     
     // send message 
     if ($recipients = $swift->send($message, $failures))
     {
         // This will let us know how many users received this message
         echo 'Message sent out to '.$recipients.' users';
     }
     // something went wrong =(
     else
     {
         echo "Something went wrong - ";
         print_r($failures);
     }

### <a name="web-api"></a>API Web

Utilisez la [fonction de coin de PHP][] pour envoyer un e-mail à l’aide de l’API de Web SendGrid.

    <?php

     $url = 'https://api.sendgrid.com/';
     $user = 'USERNAME';
     $pass = 'PASSWORD'; 

     $params = array(
          'api_user' => $user,
          'api_key' => $pass,
          'to' => 'john@contoso.com',
          'subject' => 'testing from curl',
          'html' => 'testing body',
          'text' => 'testing body',
          'from' => 'anna@contoso.com',
       );
       
     $request = $url.'api/mail.send.json';
     
     // Generate curl request
     $session = curl_init($request);
     
     // Tell curl to use HTTP POST
     curl_setopt ($session, CURLOPT_POST, true);
     
     // Tell curl that this is the body of the POST
     curl_setopt ($session, CURLOPT_POSTFIELDS, $params);
     
     // Tell curl not to return headers, but do return the response
     curl_setopt($session, CURLOPT_HEADER, false);
     curl_setopt($session, CURLOPT_RETURNTRANSFER, true);
     
     // obtain response
     $response = curl_exec($session);
     curl_close($session);
     
     // print everything out
     print_r($response);

API de Web de SendGrid est très similaire à une API REST, s’il n’est pas réellement une API RESTful puisque, dans la plupart des appels, à la fois obtenir et les verbes POST peuvent être utilisées indifféremment.

## <a name="how-to-add-an-attachment"></a>Comment : ajouter une pièce jointe

### <a name="smtp-api"></a>API DE SMTP

Envoyer une pièce jointe à l’aide de l’API SMTP consiste à ajouter une ligne de code pour l’exemple de script pour l’envoi d’un message électronique avec le service de publipostage Swift.

    <?php
     include_once "vendor/autoload.php";
     /*
      * Create the body of the message (a plain-text and an HTML version).
      * $text is your plain-text email
      * $html is your html version of the email
      * If the reciever is able to view html emails then only the html
      * email will be displayed
      */
     $text = "Hi!\nHow are you?\n";
      $html = "<html>
          <head></head>
          <body>
             <p>Hi!<br>
                How are you?<br>
             </p>
          </body>
          </html>";

     // This is your From email address
     $from = array('someone@example.com' => 'Name To Appear');
     
     // Email recipients
     $to = array(
          'john@contoso.com'=>'Destination 1 Name',
          'anna@contoso.com'=>'Destination 2 Name'
     );
     // Email subject
     $subject = 'Example PHP Email';
     
     // Login credentials
     $username = 'yoursendgridusername';
     $password = 'yourpassword';
     
     // Setup Swift mailer parameters
     $transport = Swift_SmtpTransport::newInstance('smtp.sendgrid.net', 587);
     $transport->setUsername($username);
     $transport->setPassword($password);
     $swift = Swift_Mailer::newInstance($transport);
     
     // Create a message (subject)
     $message = new Swift_Message($subject);
     
     // attach the body of the email
     $message->setFrom($from);
     $message->setBody($html, 'text/html');
     $message->setTo($to);
     $message->addPart($text, 'text/plain');
     $message->attach(Swift_Attachment::fromPath("path\to\file")->setFileName("file_name"));
     
     // send message 
     if ($recipients = $swift->send($message, $failures))
     {
          // This will let us know how many users received this message
          echo 'Message sent out to '.$recipients.' users';
     }
     // something went wrong =(
     else
     {
          echo "Something went wrong - ";
          print_r($failures);
     }

La ligne de code supplémentaire est la suivante :

     $message->attach(Swift_Attachment::fromPath("path\to\file")->setFileName('file_name'));

Cette ligne de code appelle la méthode attach sur le <span class="auto-style2">Swift\_Message</span> objet et utilise la méthode statique <span class="auto-style2">fromPath</span> sur la <span class="auto-style2">Swift\_pièce jointe</span> classe d’obtenir et de joindre un fichier à un message.

### <a name="web-api"></a>API Web

Envoyer une pièce jointe à l’aide de l’API Web est très similaire à l’envoi d’un courrier électronique à l’aide de l’API Web. Toutefois, notez que dans l’exemple qui suit, le tableau de paramètres doit contenir cet élément :

    'files['.$fileName.']' => '@'.$filePath.'/'.$fileName

Exemple :

    <?php

     $url = 'https://api.sendgrid.com/';
     $user = 'USERNAME';
     $pass = 'PASSWORD';
     
     $fileName = 'myfile';
     $filePath = dirname(__FILE__);

     $params = array(
         'api_user' => $user,
         'api_key' => $pass,
         'to' =>'john@contoso.com',
         'subject' => 'test of file sends',
         'html' => '<p> the HTML </p>',
         'text' => 'the plain text',
         'from' => 'anna@contoso.com',
         'files['.$fileName.']' => '@'.$filePath.'/'.$fileName
     );
     
     print_r($params);
     
     $request = $url.'api/mail.send.json';
     
     // Generate curl request
     $session = curl_init($request);
     
     // Tell curl to use HTTP POST
     curl_setopt ($session, CURLOPT_POST, true);
     
     // Tell curl that this is the body of the POST
     curl_setopt ($session, CURLOPT_POSTFIELDS, $params);
     
     // Tell curl not to return headers, but do return the response
     curl_setopt($session, CURLOPT_HEADER, false);
     curl_setopt($session, CURLOPT_RETURNTRANSFER, true);
     
     // obtain response
     $response = curl_exec($session);
     curl_close($session);
     
     // print everything out
     print_r($response);

## <a name="how-to-use-filters-to-enable-footers-tracking-and-analytics"></a>Comment : utiliser des filtres pour activer les pieds de page, de suivi et Analytique

SendGrid fournit une fonctionnalité de messagerie supplémentaires à l’aide de « filtres ». Il s’agit des paramètres qui peuvent être ajoutées à un message électronique pour activer des fonctionnalités spécifiques telles que l’activation du suivi des clics, Google analytique, suivi des abonnements et ainsi de suite.

Filtres peuvent être appliqués à un message à l’aide de la propriété filters. Chaque filtre est spécifié par un hachage qui contient des paramètres spécifiques au filtre. L’exemple suivant active le filtre de pied de page et spécifie un message texte qui est ajouté au bas du message électronique.
Pour cet exemple, nous allons utiliser [bibliothèque php-sendgrid].
[Composer] permet d’installer la bibliothèque :
    
    php composer.phar require sendgrid/sendgrid 2.1.1

Exemple :    

    <?php
     /*
      * This example is used for sendgrid-php V2.1.1 (https://github.com/sendgrid/sendgrid-php/tree/v2.1.1)
      */
     include "vendor/autoload.php";

     $email = new SendGrid\Email();
     // The list of addresses this message will be sent to
     // [This list is used for sending multiple emails using just ONE request to SendGrid]
     $toList = array('john@contoso.com', 'anna@contoso.com');

     // Specify the names of the recipients
     $nameList = array('Name 1', 'Name 2');

     // Used as an example of variable substitution
     $timeList = array('4 PM', '5 PM');

     // Set all of the above variables
     $email->setTos($toList);
     $email->addSubstitution('-name-', $nameList);
     $email->addSubstitution('-time-', $timeList);

     // Specify that this is an initial contact message
     $email->addCategory("initial");

     // You can optionally setup individual filters here, in this example, we have 
     // enabled the footer filter
     $email->addFilter('footer', 'enable', 1);
     $email->addFilter('footer', "text/plain", "Thank you for your business");
     $email->addFilter('footer', "text/html", "Thank you for your business");

     // The subject of your email
     $subject = 'Example SendGrid Email';

     // Where is this message coming from. For example, this message can be from 
     // support@yourcompany.com, info@yourcompany.com
     $from = 'someone@example.com';

     // If you do not specify a sender list above, you can specifiy the user here. If 
     // a sender list IS specified above, this email address becomes irrelevant.
     $to = 'john@contoso.com';

     # Create the body of the message (a plain-text and an HTML version). 
     # text is your plain-text email 
     # html is your html version of the email
     # if the receiver is able to view html emails then only the html
     # email will be displayed

     /*
      * Note the variable substitution here =)
      */
     $text = "
     Hello -name-,
     Thank you for your interest in our products. We have set up an appointment to call you at -time- EST to discuss your needs in more detail.
     Regards,
     Fred";

     $html = "
     <html> 
     <head></head>
     <body>
     <p>Hello -name-,<br>
     Thank you for your interest in our products. We have set up an appointment
     to call you at -time- EST to discuss your needs in more detail.

     Regards,

     Fred<br>
     </p>
     </body>
     </html>";

     // set subject
     $email->setSubject($subject);

     // attach the body of the email
     $email->setFrom($from);
     $email->setHtml($html);
     $email->addTo($to);
     $email->setText($text);

     // Your SendGrid account credentials
     $username = 'sendgridusername@yourdomain.com';
     $password = 'example';

     // Create SendGrid object
     $sendgrid = new SendGrid($username, $password);

     // send message
     $response = $sendgrid->send($email);

     print_r($response);

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris les notions de base du service E-mail de SendGrid, cliquez sur ces liens pour en savoir plus.

-   Documentation de SendGrid : <https://sendgrid.com/docs>
-   Bibliothèque PHP de la SendGrid : <https://github.com/sendgrid/sendgrid-php>
-   Offre spéciale de SendGrid pour les clients Azure : <https://sendgrid.com/windowsazure.html>

Pour plus d’informations, consultez également le [Centre pour développeurs PHP](/develop/php/).


  [https://sendgrid.com]: https://sendgrid.com
  [https://sendgrid.com/transactional-email/pricing]: https://sendgrid.com/transactional-email/pricing
  [special offer]: https://www.sendgrid.com/windowsazure.html
  [Packaging and Deploying PHP Applications for Azure]: http://msdn.microsoft.com/library/windowsazure/hh674499(v=VS.103).aspx
  [http://swiftmailer.org/download]: http://swiftmailer.org/download
  [fonction de roulage]: http://php.net/curl
  [service de messagerie basés sur le cloud]: https://sendgrid.com/email-solutions
  [livraison de messagerie transactionnelle]: https://sendgrid.com/transactional-email
  [bibliothèque php-sendgrid]: https://github.com/sendgrid/sendgrid-php/tree/v2.1.1
  [Composer]: https://getcomposer.org/download/
