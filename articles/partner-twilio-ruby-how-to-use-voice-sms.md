<properties 
    pageTitle="L’utilisation de Twilio pour la voix et les SMS (Ruby) | Microsoft Azure" 
    description="Apprenez à effectuer un appel téléphonique et d’envoyer un message SMS avec le service Twilio API sur Azure. Exemples de code écrites en Ruby." 
    services="" 
    documentationCenter="ruby" 
    authors="devinrader" 
    manager="twilio" 
    editor=""/>

<tags 
    ms.service="multiple" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="ruby" 
    ms.topic="article" 
    ms.date="11/25/2014" 
    ms.author="MicrosoftHelp@twilio.com"/>





# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-ruby"></a>L’utilisation de Twilio pour la voix et les fonctionnalités SMS en Ruby
Ce guide montre comment effectuer des tâches de programmation courantes avec le service Twilio API sur Azure. Les scénarios présentés incluent un appel téléphonique et l’envoi d’un message de Service SMS (Short Message). Pour plus d’informations sur Twilio et de l’utilisation de la voix et SMS dans vos applications, consultez la section [Étapes suivantes](#NextSteps) .

## <a id="WhatIs"></a>Quel est Twilio ?
Twilio est une API de service web de téléphonie qui vous permet d’utiliser vos compétences et langages de web existants pour créer des applications de SMS et de la voix. Twilio est un service de tiers (pas une fonction d’Azure et non un produit Microsoft).

**Twilio vocale** permet à vos applications d’effectuer et de recevoir des appels téléphoniques. **Twilio SMS** permet à vos applications d’effectuer et de recevoir des messages SMS. **Twilio Client** permet à vos applications permettre la communication vocale à l’aide de connexions Internet existantes, y compris les connexions mobiles.

## <a id="Pricing"></a>Twilio tarifs et offres spéciales
Informations sur la tarification de Twilio sont disponibles au [Prix de Twilio] [twilio_pricing]. Les clients Azure recevoir une [offre spéciale][special_offer]: un crédit gratuit de textes de 1000 ou 1000 minutes entrantes. Pour vous inscrire à cette offre ou obtenir plus d’informations, visitez le site [http://ahoy.twilio.com/azure][special_offer].  

## <a id="Concepts"></a>Concepts
L’API Twilio est une API RESTful qui fournit la voix et les fonctionnalités SMS pour les applications. Les bibliothèques clientes sont disponibles dans plusieurs langues ; pour une liste, consultez [Bibliothèques d’API Twilio] [twilio_libraries].

### <a id="TwiML"></a>TwiML
TwiML est un ensemble d’instructions basé sur XML qui informent Twilio comment traiter un appel ou SMS.

Par exemple, le TwiML suivant permet de convertir le le texte **Hello World** à reconnaissance vocale.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Tous les documents de TwiML ont `<Response>` que leur élément racine. À partir de là, vous utilisez les verbes Twilio pour définir le comportement de votre application.

### <a id="Verbs"></a>Verbes de TwiML
Les verbes de Twilio sont des balises XML qui indiquent Twilio quoi **faire**. Par exemple, le ** &lt;dire&gt; ** verbe indique à Twilio de façon audible remettre un message lors d’un appel. 

Voici une liste de verbes de Twilio.

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

Pour plus d’informations sur les Twilio des verbes, leurs attributs et TwiML, consultez [TwiML] [twiml]. Pour plus d’informations sur l’API Twilio, voir [API de Twilio] [twilio_api].

## <a id="CreateAccount"></a>Créer un compte Twilio
Lorsque vous êtes prêt à créer un compte Twilio, inscrivez-vous à [Essayer de Twilio] [try_twilio]. Vous pouvez démarrer avec un compte gratuit et mise à niveau de votre compte ultérieurement.

Lorsque vous vous inscrivez pour un compte Twilio, vous obtiendrez un numéro de téléphone gratuit pour votre application. Vous recevrez également un SID de compte et un jeton d’authentification. Les deux seront nécessaires pour effectuer des appels d’API de Twilio. Pour empêcher tout accès non autorisé à votre compte, sécuriser votre jeton d’authentification. Votre jeton d’authentification et les SID de compte peuvent être affichées dans la [page compte de Twilio][twilio_account], dans les champs intitulé **SID de compte** et d' **Authentification de jeton**, respectivement.

### <a id="VerifyPhoneNumbers"></a>Vérifiez les numéros de téléphone
En plus du numéro vous est proposée par Twilio, vous pouvez également vérifier les numéros de contrôle (par exemple, votre téléphone portable ou à la maison numéro de téléphone) pour une utilisation dans vos applications. 

Pour plus d’informations sur la façon de vérifier un numéro de téléphone, voir [Gérer les numéros] [verify_phone].

## <a id="create_app"></a>Créer une Application Ruby
Une application Ruby qui utilise le service Twilio et s’exécute dans Azure n’est pas différente de celle de toute autre application Ruby qui utilise le service Twilio. Alors que les services Twilio sont RESTful et peuvent être appelées de plusieurs façons de Ruby, cet article se concentrera sur l’utilisation des services de Twilio avec la [bibliothèque d’assistance de Twilio pour Ruby][twilio_ruby].

Tout d’abord, [configurer une nouvelle machine virtuelle de Linux de Azure] [ azure_vm_setup] d’agir comme un hôte pour votre nouvelle application web Ruby. Ignorer les étapes impliquant la création d’une application de Rails, la machine virtuelle de configuration juste. Assurez-vous que vous créez un point de terminaison avec un port externe de 80 et un port interne de 5000.

Dans les exemples ci-dessous, nous utiliserons [Sinatra][sinatra], un cadre web très simple pour Ruby. Mais vous pouvez certainement utiliser la bibliothèque d’assistance Twilio pour Ruby avec toute autre infrastructure web, y compris Ruby on Rails.

SSH dans votre nouvelle machine virtuelle et créez un répertoire pour votre nouvelle application. À l’intérieur de ce répertoire, créez un fichier appelé Gemfile et copiez le code suivant :

    source 'https://rubygems.org'
    gem 'sinatra'
    gem 'thin'

Sur la ligne de commande exécutée `bundle install`. Ceci installera les dépendances ci-dessus. Ensuite, créez un fichier appelé `web.rb`. Il s’agit de dans laquelle le code de votre application web réside. Coller le code suivant :

    require 'sinatra'

    get '/' do
        "Hello Monkey!"
    end

À ce stade vous devriez pouvoir l’exécution de la commande `ruby web.rb -p 5000`. Cela sera toupie à distance un serveur web de petite taille sur le port 5000. Vous permettre de rechercher à cette application dans votre navigateur après avoir visité l’URL vous configurer votre Azure VM. Une fois que vous pouvez atteindre votre application web dans le navigateur, vous êtes prêt à démarrer la construction d’une application Twilio.

## <a id="configure_app"></a>Configurez votre Application pour utiliser Twilio
Vous pouvez configurer votre application web pour utiliser la bibliothèque Twilio en mettant à jour votre `Gemfile` pour inclure cette ligne :

    gem 'twilio-ruby'

Sur la ligne de commande, exécutez `bundle install`. Ouvrez à présent `web.rb` et en incluant cette ligne dans la partie supérieure :

    require 'twilio-ruby'

Vous êtes maintenant prêt à utiliser la bibliothèque d’assistance Twilio pour Ruby dans votre application web.

## <a id="howto_make_call"></a>Comment : effectuer un appel sortant
Voici comment effectuer un appel sortant. Concepts clés incluent à l’aide de la bibliothèque d’assistance Twilio pour Ruby pour effectuer des appels de l’API REST et le rendu de TwiML. Remplacez par les numéros de téléphone **à partir** d’et **vers** vos valeurs et assurez-vous que vous vérifiez le numéro de téléphone **à partir** de votre compte de Twilio avant d’exécuter le code.

Ajoutez cette fonction à `web.md`:

    # Set your account ID and authentication token.
    sid = "your_twilio_account_sid";
    token = "your_twilio_authentication_token";

    # The number of the phone initiating the the call.
    # This should either be a Twilio number or a number that you've verified
    from = "NNNNNNNNNNN";

    # The number of the phone receiving call.
    to = "NNNNNNNNNNN";

    # Use the Twilio-provided site for the TwiML response.
    url = "http://yourdomain.cloudapp.net/voice_url";
      
    get '/make_call' do
      # Create the call client.
      client = Twilio::REST::Client.new(sid, token);
      
      # Make the call
      client.account.calls.create(to: to, from: from, url: url)
    end

    post '/voice_url' do
      "<Response>
         <Say>Hello Monkey!</Say>
       </Response>"
    end
    
Si vous ouvrir à distance `http://yourdomain.cloudapp.net/make_call` dans un navigateur, qui déclenche l’appel à l’API Twilio pour effectuer l’appel téléphonique. Les deux premiers paramètres dans `client.account.calls.create` sont relativement explicites : le numéro de l’appel est `from` et le numéro de l’appel est `to`. 

Le troisième paramètre (`url`) est l’URL que demande de Twilio pour obtenir des instructions sur ce qu’il faut effectuer une fois que l’appel est connecté. Dans ce cas nous configuration un URL (`http://yourdomain.cloudapp.net`) qui renvoie un document TwiML simple et qui utilise le `<Say>` verbe pour effectuer une conversion vocale du texte et dire « Hello singe » pour la personne qui reçoit l’appel.

## <a id="howto_recieve_sms"></a>Comment : recevoir un message SMS
Dans l’exemple précédent, nous avons lancé un appel téléphonique **sortant** . Cette fois, nous allons utiliser le numéro de téléphone Twilio nous a fourni pendant l’inscription pour traiter un message SMS **entrant** .

Tout d’abord, reconnectez-vous à votre [tableau de bord de Twilio][twilio_account]. Dans la navigation supérieure, cliquez sur « Nombres » et puis cliquez sur le numéro de Twilio que vous ont été fournis. Vous verrez deux URL que vous pouvez configurer. URL de demande une URL de requête vocale et SMS. Il s’agit de l’URL Twilio appelle chaque fois qu’un appel téléphonique ou un SMS est envoyé à votre numéro. Les URL sont également connus comme « hooks web ».

Nous souhaitons traiter les messages SMS entrants, mettons donc à jour l’URL pour `http://yourdomain.cloudapp.net/sms_url`. Continuez et cliquez sur Enregistrer les changements au bas de la page. Revenons dans `web.rb` du programme nous allons notre application pour gérer cette situation :

    post '/sms_url' do
      "<Response>
         <Message>Hey, thanks for the ping! Twilio and Azure rock!</Message>
       </Response>"
    end

Après avoir apporté la modification, assurez-vous de redémarrer votre application web. Maintenant, retirez votre téléphone et envoyer un SMS au numéro de Twilio. Vous devriez obtenir rapidement d’une réponse SMS indiquant que « Hey, Merci de la commande ping ! Rock Twilio et Azure ! ».

## <a id="additional_services"></a>Comment : utiliser des Services supplémentaires Twilio
Outre les exemples donnés ci-dessus, Twilio propose des API basée sur le web qui vous permet de tirer parti des fonctionnalités supplémentaires Twilio à partir de votre application Azure. Pour plus d’informations, consultez la [documentation de l’API de Twilio] [twilio_api_documentation].

### <a id="NextSteps"></a>Étapes suivantes
Maintenant que vous avez appris les notions de base du service Twilio, cliquez sur ces liens pour en savoir plus :

* [Consignes de sécurité Twilio] [twilio_security_guidelines]
* [Twilio HowTos et l’exemple de Code] [twilio_howtos]
* [Didacticiels de démarrage rapide de Twilio][twilio_quickstarts] 
* [Twilio sur GitHub] [twilio_on_github]
* [Communiquer avec prise en charge de Twilio] [twilio_support]

[twilio_ruby]: https://www.twilio.com/docs/ruby/install





[twilio_pricing]: http://www.twilio.com/pricing
[special_offer]: http://ahoy.twilio.com/azure
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
[sinatra]: http://www.sinatrarb.com/
[azure_vm_setup]: http://www.windowsazure.com/develop/ruby/tutorials/web-app-with-linux-vm/
