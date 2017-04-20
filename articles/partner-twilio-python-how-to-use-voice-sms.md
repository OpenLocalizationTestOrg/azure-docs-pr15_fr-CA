<properties
    pageTitle="L’utilisation de Twilio pour la voix et les SMS (PHP) | Microsoft Azure"
    description="Apprenez à effectuer un appel téléphonique et d’envoyer un message SMS avec le service Twilio API sur Azure. Exemples de code écrits en PHP."
    services=""
    documentationCenter="python"
    authors="devinrader"
    manager="twilio"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="python" 
    ms.topic="article"
    ms.date="02/19/2015"
    ms.author="MicrosoftHelp@twilio.com"/>





# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-php"></a>L’utilisation de Twilio pour la voix et les fonctionnalités SMS dans PHP
Ce guide montre comment effectuer des tâches de programmation courantes avec le service Twilio API sur Azure. Les scénarios présentés incluent un appel téléphonique et l’envoi d’un message de Service SMS (Short Message). Pour plus d’informations sur Twilio et de l’utilisation de la voix et SMS dans vos applications, consultez la section [Étapes suivantes](#NextSteps) .

## <a id="WhatIs"></a>Quel est Twilio ?
Twilio est l’avenir de communications d’entreprise, permettant aux développeurs d’incorporer des voix, la VoIP et dans des applications de messagerie. La virtualisation toute infrastructure nécessaire dans un environnement en nuage, global, exposant via la plate-forme de communications API Twilio. Les applications sont simples à créer et évolutive. Profitez de flexibilité avec paie-sous-vous allez tarification et bénéficier de la fiabilité du nuage.

**Twilio vocale** permet à vos applications d’effectuer et de recevoir des appels téléphoniques. **Twilio SMS** permet à votre application d’envoyer et de recevoir des messages texte. **Twilio Client** vous permet d’effectuer des appels VoIP à partir de n’importe quel téléphone, tablette ou navigateur et prend en charge les WebRTC.

## <a id="Pricing"></a>Twilio tarifs et offres spéciales

Azure clientèle [offre spéciale] 10 $ de crédit de Twilio lorsque vous mettez à niveau votre compte Twilio. Ce crédit de Twilio peut être appliqué à toute utilisation Twilio (équivalent à envoyer des SMS jusqu'à 1 000 ou de la réception jusqu'à 1000 minutes vocaux entrants, en fonction de l’emplacement de la destination d’appel ou un message et numéro de téléphone de crédit de 10 $). Convertir ce crédit Twilio et commencer à : [ahoy.twilio.com/azure].

Twilio est un service de paiement. Il n’y a aucun frais d’installation et vous pouvez fermer votre compte à tout moment. Vous trouverez plus de détails au [Prix de Twilio] [twilio_pricing].

## <a id="Concepts"></a>Concepts
L’API Twilio est une API RESTful qui fournit la voix et les fonctionnalités SMS pour les applications. Les bibliothèques clientes sont disponibles dans plusieurs langues ; pour une liste, consultez [Bibliothèques d’API Twilio] [twilio_libraries].

Verbes de Twilio et de langage de balisage Twilio (TwiML) sont des aspects clés de l’API Twilio.

### <a id="Verbs"></a>Verbes de Twilio
L’API utilise des Twilio des verbes ; par exemple, le ** &lt;dire&gt; ** verbe indique à Twilio de façon audible remettre un message lors d’un appel.

Voici une liste de verbes de Twilio. En savoir plus sur les autres verbes et capacités via [documentation de langage de balisage Twilio] [http://www.twilio.com/docs/api/twiml].

* ** &lt;à distance&gt;**: connecte l’appelant vers un autre téléphone.
* ** &lt;Collecter&gt;**: collecte les chiffres entrés sur le clavier du téléphone.
* ** &lt;Raccrochage&gt;**: met fin à un appel.
* ** &lt;Lire&gt;**: joue un fichier audio.
* ** &lt;Pause&gt;**: attend silencieusement un nombre de secondes spécifié.
* ** &lt;Enregistrement&gt;**: enregistre la voix de l’appelant et retourne une URL d’un fichier qui contient l’enregistrement.
* ** &lt;Redirection&gt;**: transfère le contrôle d’un appel ou un SMS à le TwiML à une autre URL.
* ** &lt;Rejeter&gt;**: rejette un appel entrant vers votre numéro Twilio sans facturation
* ** &lt;Dire&gt;**: convertit la synthèse vocale qui est effectuée lors d’un appel.
* ** &lt;Sms&gt;**: envoie un message SMS.

### <a id="TwiML"></a>TwiML
TwiML est un ensemble d’instructions basé sur XML basé sur les verbes Twilio qui informent Twilio comment traiter un appel ou SMS.

Par exemple, le TwiML suivant permet de convertir le le texte **Hello World** à reconnaissance vocale.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Lorsque votre application appelle l’API Twilio, l’un des paramètres API est l’URL qui renvoie la réponse TwiML. À des fins de développement, vous pouvez utiliser l’URL fourni par Twilio pour fournir les réponses TwiML utilisés par vos applications. Vous permet d’héberger votre propre URL pour produire les réponses TwiML, et une autre option consiste à utiliser l’objet **TwiMLResponse** .

Pour plus d’informations sur les Twilio des verbes, leurs attributs et TwiML, consultez [TwiML] [twiml]. Pour plus d’informations sur l’API Twilio, voir [API de Twilio] [twilio_api].

## <a id="CreateAccount"></a>Créer un compte Twilio
Lorsque vous êtes prêt obtenir un compte Twilio, inscrivez-vous à [Essayer de Twilio] [try_twilio]. Vous pouvez démarrer avec un compte gratuit et mise à niveau de votre compte ultérieurement.

Lorsque vous vous inscrivez pour un compte Twilio, vous recevez un ID de compte et un jeton d’authentification. Les deux seront nécessaires pour effectuer des appels d’API de Twilio. Pour empêcher tout accès non autorisé à votre compte, sécuriser votre jeton d’authentification. Votre ID de compte et l’authentification de jeton sont visibles dans la [page compte de Twilio] [twilio_account], dans les champs intitulé **SID de compte** et d' **Authentification de jeton**, respectivement.

## <a id="create_app"></a>Création d’une Application PHP
Une application PHP qui utilise le service Twilio et s’exécute dans Azure n’est pas différente de celle de toute autre application PHP qui utilise le service Twilio. Twilio services sont basés sur les autres et peuvent être appelées de plusieurs façons à partir de PHP, cet article se concentrera sur l’utilisation des services de Twilio avec la [bibliothèque Twilio pour PHP à partir de GitHub][twilio_php]. Pour plus d’informations sur l’utilisation de la bibliothèque Twilio pour PHP, voir [http://readthedocs.org/docs/twilio-php/en/latest/index.html][twilio_lib_docs].

Pour obtenir des instructions détaillées pour la création et le déploiement d’une application Twilio/PHP vers Azure sont disponibles à [la création d’un Twilio à l’aide d’un appel dans une Application PHP sur Azure][howto_phonecall_php].

## <a id="configure_app"></a>Configurez votre Application pour utiliser les bibliothèques Twilio
Vous pouvez configurer votre application pour utiliser la bibliothèque Twilio pour PHP de deux manières :

1. Télécharger la bibliothèque Twilio pour PHP à partir de GitHub ([https://github.com/twilio/twilio-php][twilio_php]) et ajoutez le répertoire des **Services** à votre application.

    -OU-

2. Installez la bibliothèque Twilio pour PHP sous la forme d’un package de POIRIERS. Il peut être installé avec les options suivantes :

        $ pear channel-discover twilio.github.com/pear
        $ pear install twilio/Services_Twilio

Une fois que vous avez installé la bibliothèque Twilio pour PHP, vous pouvez ajouter une instruction **require_once** en haut de vos fichiers PHP pour faire référence à la bibliothèque :

        require_once 'Services/Twilio.php';

Pour plus d’informations, consultez [https://github.com/twilio/twilio-php/blob/master/README.md][twilio_github_readme].

## <a id="howto_make_call"></a>Comment : effectuer un appel sortant
Voici comment effectuer un appel sortant à l’aide de la classe **Services_Twilio** . Ce code utilise également un site fourni par Twilio pour renvoyer la réponse de langage de balisage Twilio (TwiML). Remplacez par les numéros de téléphone **à partir** d’et **vers** vos valeurs et assurez-vous que vous vérifiez le numéro de téléphone **à partir** de votre compte de Twilio avant d’exécuter le code.

    // Include the Twilio PHP library.
    require_once 'Services/Twilio.php';

    // Library version.
    $version = "2010-04-01";

    // Set your account ID and authentication token.
    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";

    // The number of the phone initiating the the call.
    // (Must be previously validated with Twilio.)
    $from_number = "NNNNNNNNNNN";

    // The number of the phone receiving call.
    $to_number = "NNNNNNNNNNN";

    // Use the Twilio-provided site for the TwiML response.
    $url = "http://twimlets.com/message";

    // The phone message text.
    $message = "Hello world.";

    // Create the call client.
    $client = new Services_Twilio($sid, $token, $version);

    //Make the call.
    try
    {
        $call = $client->account->calls->create(
            $from_number,
            $to_number,
            $url.'?Message='.urlencode($message)
        );
    }
    catch (Exception $e)
    {
        echo 'Error: ' . $e->getMessage();
    }

Comme mentionné, ce code utilise un site fourni par Twilio pour retourner la réponse TwiML. Vous pouvez utiliser à la place de votre propre site pour fournir la réponse TwiML ; Pour plus d’informations, consultez [comment fournir des réponses TwiML à partir de votre propre Site Web](#howto_provide_twiml_responses).


- **Remarque**: pour résoudre les erreurs de validation de certificat SSL, voir [http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html][ssl_validation]


## <a id="howto_send_sms"></a>Comment : envoyer un message SMS
Voici comment envoyer un message SMS à l’aide de la classe **Services_Twilio** . Le numéro **** d’ordre est fourni par Twilio des comptes d’essai pour envoyer des messages SMS. **Le numéro** doit être vérifié pour votre compte de Twilio avant d’exécuter le code.

    // Include the Twilio PHP library.
    require_once 'Services/Twilio.php';

    // Library version.
    $version = "2010-04-01";

    // Set your account ID and authentication token.
    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";


    $from_number = "NNNNNNNNNNN"; // With trial account, texts can only be sent from your Twilio number.
    $to_number = "NNNNNNNNNNN";
    $message = "Hello world.";

    // Create the call client.
    $client = new Services_Twilio($sid, $token, $version);

    // Send the SMS message.
    try
    {
        $client->account->sms_messages->create($from_number, $to_number, $message);
    }
    catch (Exception $e)
    {
        echo 'Error: ' . $e->getMessage();
    }

## <a id="howto_provide_twiml_responses"></a>Comment : fournir des réponses à partir de votre propre site Web TwiML
Lorsque votre application lance un appel à l’API Twilio, Twilio enverra votre demande à une URL qui est censée renvoyer une réponse TwiML. L’exemple ci-dessus utilise l' URL fourni par Twilio [http://twimlets.com/message][twimlet_message_url]. (Alors que TwiML est conçu pour une utilisation par Twilio, vous pouvez afficher l’informatique dans votre navigateur. Par exemple, cliquez sur [http://twimlets.com/message] [ twimlet_message_url] pour voir vide `<Response>` élément ; comme autre exemple, cliquez sur [http://twimlets.com/message?Message%5B0%5D=Hello%20World] [ twimlet_message_url_hello_world] de voir un `<Response>` élément contenant un `<Say>` élément.)

Plutôt que d’utiliser l’URL fourni par Twilio, vous pouvez créer votre propre site qui renvoie des réponses HTTP. Vous pouvez créer le site dans n’importe quel langage qui renvoie les réponses XML ; Cette rubrique suppose que vous utilisez PHP pour créer la TwiML.

La page PHP suivante génère une réponse TwiML indiquant **Hello World** lors de l’appel.

    <?php
        header("content-type: text/xml");
        echo "<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n";
    ?>
    <Response>
        <Say>Hello world.</Say>
    </Response>

Comme vous pouvez le voir dans l’exemple ci-dessus, la réponse à TwiML est simplement un document XML. La bibliothèque Twilio pour PHP contient des classes qui génèrent des TwiML pour vous. L’exemple ci-dessous génère la réponse équivalente, comme indiqué ci-dessus, mais utilise le **Services\_Twilio\_Twiml** classe dans la bibliothèque de Twilio pour PHP :

    require_once('Services/Twilio.php');

    $response = new Services_Twilio_Twiml();
    $response->say("Hello world.");
    print $response;

Pour plus d’informations sur TwiML, reportez-vous à la section [https://www.twilio.com/docs/api/twiml][twiml_reference].

Une fois que vous avez votre page PHP configuré pour fournir des réponses TwiML, utilisez l’URL de la page PHP que l’URL est passée dans le `Services_Twilio->account->calls->create` méthode. Par exemple, si vous disposez d’une application Web nommée **MyTwiML** est déployé sur un Azure hébergé le service et le nom de la page PHP est **mytwiml.php**, l’URL peut être passé à **Services_Twilio -> compte -> appels -> créez** comme indiqué dans l’exemple suivant :

    require_once 'Services/Twilio.php';

    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";
    $from_number = "NNNNNNNNNNN";
    $to_number = "NNNNNNNNNNN";
    $url = "http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.php";

    // The phone message text.
    $message = "Hello world.";

    $client = new Services_Twilio($sid, $token, "2010-04-01");

    try
    {
        $call = $client->account->calls->create(
            $from_number,
            $to_number,
            $url.'?Message='.urlencode($message)
        );
    }
    catch (Exception $e)
    {
        echo 'Error: ' . $e->getMessage();
    }

Pour plus d’informations sur l’utilisation de Twilio dans Azure avec PHP, voir [comment effectuer un Twilio à l’aide d’un appel dans une Application PHP sur Azure][howto_phonecall_php].

## <a id="AdditionalServices"></a>Comment : utiliser des Services supplémentaires Twilio
Outre les exemples donnés ci-dessus, Twilio propose des API basée sur le web qui vous permet de tirer parti des fonctionnalités supplémentaires Twilio à partir de votre application Azure. Pour plus d’informations, consultez la [documentation de l’API de Twilio] [twilio_api_documentation].

## <a id="NextSteps"></a>Étapes suivantes
Maintenant que vous avez appris les notions de base du service Twilio, cliquez sur ces liens pour en savoir plus :

* [Consignes de sécurité Twilio] [twilio_security_guidelines]
* [Guides de faire de la Twilio et l’exemple de Code] [twilio_howtos]
* [Didacticiels de démarrage rapide de Twilio][twilio_quickstarts]
* [Twilio sur GitHub] [twilio_on_github]
* [Communiquer avec prise en charge de Twilio] [twilio_support]

[twilio_php]: https://github.com/twilio/twilio-php
[twilio_lib_docs]: http://readthedocs.org/docs/twilio-php/en/latest/index.html
[twilio_github_readme]: https://github.com/twilio/twilio-php/blob/master/README.md
[ssl_validation]: http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html

[howto_phonecall_php]: http://windowsazure.com/documentation/articles/partner-twilio-php-make-phone-call
[twimlet_message_url]: http://twimlets.com/message
[twimlet_message_url_hello_world]: http://twimlets.com/message?Message%5B0%5D=Hello%20World
[twiml_reference]: https://www.twilio.com/docs/api/twiml
[twilio_pricing]: http://www.twilio.com/pricing

[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api]: http://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]:  https://www.twilio.com/user/account
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_api_documentation]: http://www.twilio.com/api
[twilio_security_guidelines]: http://www.twilio.com/docs/security
[twilio_howtos]: http://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: http://www.twilio.com/help/contact
[twilio_quickstarts]: http://www.twilio.com/docs/quickstart
