<properties 
    pageTitle="L’utilisation de Twilio pour la voix et les SMS (Java) | Microsoft Azure" 
    description="Apprenez à effectuer un appel téléphonique et d’envoyer un message SMS avec le service Twilio API sur Azure. Exemples de code écrits en Java." 
    services="" 
    documentationCenter="java" 
    authors="devinrader" 
    manager="twilio" 
    editor="mollybos"/>

<tags 
    ms.service="multiple" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="11/25/2014" 
    ms.author="microsofthelp@twilio.com"/>

# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-java"></a>L’utilisation de Twilio pour la voix et les fonctionnalités SMS dans Java

Ce guide montre comment effectuer des tâches de programmation courantes avec le service Twilio API sur Azure. Les scénarios présentés incluent un appel téléphonique et l’envoi d’un message de Service SMS (Short Message). Pour plus d’informations sur Twilio et de l’utilisation de la voix et SMS dans vos applications, consultez la section [Étapes suivantes](#NextSteps) .

## <a id="WhatIs"></a>Quel est Twilio ?
Twilio est une API de service web de téléphonie qui vous permet d’utiliser vos compétences et langages de web existants pour créer des applications de SMS et de la voix. Twilio est un service de tiers (pas une fonction d’Azure et non un produit Microsoft).

**Twilio vocale** permet à vos applications d’effectuer et de recevoir des appels téléphoniques. **Twilio SMS** permet à vos applications d’effectuer et de recevoir des messages SMS. **Twilio Client** permet à vos applications permettre la communication vocale à l’aide de connexions Internet existantes, y compris les connexions mobiles.

## <a id="Pricing"></a>Twilio tarifs et offres spéciales
Informations sur la tarification de Twilio sont disponibles au [Prix de Twilio] [twilio_pricing]. Les clients Azure recevoir une [offre spéciale][special_offer]: un crédit gratuit de textes de 1000 ou 1000 minutes entrantes. Pour vous inscrire à cette offre ou obtenir plus d’informations, visitez le site [http://ahoy.twilio.com/azure][special_offer].  

## <a id="Concepts"></a>Concepts
L’API Twilio est une API RESTful qui fournit la voix et les fonctionnalités SMS pour les applications. Les bibliothèques clientes sont disponibles dans plusieurs langues ; pour une liste, consultez [Bibliothèques d’API Twilio] [twilio_libraries].

Verbes de Twilio et de langage de balisage Twilio (TwiML) sont des aspects clés de l’API Twilio.

### <a id="Verbs"></a>Verbes de Twilio
L’API utilise des Twilio des verbes ; par exemple, le ** &lt;dire&gt; ** verbe indique à Twilio de façon audible remettre un message lors d’un appel. 

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
Lorsque vous êtes prêt à créer un compte Twilio, inscrivez-vous à [Essayer de Twilio] [try_twilio]. Vous pouvez démarrer avec un compte gratuit et mise à niveau de votre compte ultérieurement.

Lorsque vous vous inscrivez pour un compte Twilio, vous recevrez un ID de compte et un jeton d’authentification. Les deux seront nécessaires pour effectuer des appels d’API de Twilio. Pour empêcher tout accès non autorisé à votre compte, sécuriser votre jeton d’authentification. Votre ID de compte et l’authentification de jeton sont visibles dans la [page compte de Twilio] [twilio_account], dans les champs intitulé **SID de compte** et d' **Authentification de jeton**, respectivement.

## <a id="create_app"></a>Créer une Application Java
1. Obtenir le JAR Twilio et l’ajouter à votre chemin d’accès de la version Java et votre assembly de déploiement de guerre. Au [https://github.com/twilio/twilio-java][twilio_java], vous pouvez télécharger les sources les GitHub et créer votre propre fichier JAR ou télécharger un JAR prédéfini (avec ou sans dépendances).
2. Vérifiez le magasin de clés **cacerts** de votre JDK contient le certificat de l’autorité de certification Equifax Secure avec 67:CB:9 d’empreinte digitale MD5 D : C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 (le numéro de série est le 35:DE:F4:CF et l’empreinte SHA1 est D2:32:09:AD:23:D3:14:23:21:74:E4:0 D : 7F:9 D : 62:13:97:86:63:3A). Il s’agit de l’autorité de certification pour le [https://api.twilio.com] [ twilio_api_service] service, qui est appelé lorsque vous utilisez Twilio APIs. Pour plus d’informations sur la garantie de votre JDK **cacerts** keystore contient le certificat d’autorité de certification approprié, consultez [Ajout d’un certificat au magasin de certificats d’autorité de certification Java][add_ca_cert].

Pour obtenir des instructions détaillées pour l’utilisation de la bibliothèque de client Twilio pour Java sont disponibles sur [la création d’un Twilio à l’aide d’un appel dans une Application Java sur Azure][howto_phonecall_java].

## <a id="configure_app"></a>Configurez votre Application pour utiliser les bibliothèques Twilio
Dans votre code, vous pouvez ajouter des instructions **import** en haut de vos fichiers sources pour le Twilio packages ou les classes que vous souhaitez utiliser dans votre application. 

Pour les fichiers source Java :

    import com.twilio.*;
    import com.twilio.sdk.*;
    import com.twilio.sdk.resource.factory.*;
    import com.twilio.sdk.resource.instance.*;

Pour les fichiers source Java Server Page (JSP) :

    import="com.twilio.*"
    import="com.twilio.sdk.*"
    import="com.twilio.sdk.resource.factory.*"
    import="com.twilio.sdk.resource.instance.*"
Selon les packages de Twilio ou les classes que vous souhaitez utiliser, vos instructions **import** peuvent être différentes.

## <a id="howto_make_call"></a>Comment : effectuer un appel sortant
Voici comment effectuer un appel sortant à l’aide de la classe **CallFactory** . Ce code utilise également un site fourni par Twilio pour renvoyer la réponse de langage de balisage Twilio (TwiML). Remplacez par les numéros de téléphone **à partir** d’et **vers** vos valeurs et assurez-vous que vous vérifiez le numéro de téléphone **à partir** de votre compte de Twilio avant d’exécuter le code.

    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    String accountSID = "your_twilio_account";
    String authToken = "your_twilio_authentication_token";

    // Create an instance of the Twilio client.
    TwilioRestClient client;
    client = new TwilioRestClient(accountSID, authToken);

    // Retrieve the account, used later to create an instance of the CallFactory.
    Account account = client.getAccount();

    // Use the Twilio-provided site for the TwiML response.
    String Url="http://twimlets.com/message";
    Url = Url + "?Message%5B0%5D=Hello%20World";

    // Place the call From, To and URL values into a hash map. 
    HashMap<String, String> params = new HashMap<String, String>();
    params.put("From", "NNNNNNNNNN"); // Use your own value for the second parameter.
    params.put("To", "NNNNNNNNNN");   // Use your own value for the second parameter.
    params.put("Url", Url);

    // Create an instance of the CallFactory class.
    CallFactory callFactory = account.getCallFactory();

    // Make the call.
    Call call = callFactory.create(params);

Pour plus d’informations sur les paramètres passés à la méthode **CallFactory.create** , voir [http://www.twilio.com/docs/api/rest/making-calls][twilio_rest_making_calls].

Comme mentionné, ce code utilise un site fourni par Twilio pour retourner la réponse TwiML. Vous pouvez utiliser à la place de votre propre site pour fournir la réponse TwiML ; Pour plus d’informations, consultez [comment fournir des réponses TwiML dans une Application Java sur Azure](#howto_provide_twiml_responses).

## <a id="howto_send_sms"></a>Comment : envoyer un message SMS
Voici comment envoyer un message SMS à l’aide de la classe **SmsFactory** . La zone **de** numéro, **4155992671**, est fourni par Twilio des comptes d’essai pour envoyer des messages SMS. **Le numéro** doit être vérifié pour votre compte de Twilio avant d’exécuter le code.

    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    String accountSID = "your_twilio_account";
    String authToken = "your_twilio_authentication_token";

    // Create an instance of the Twilio client.
    TwilioRestClient client;
    client = new TwilioRestClient(accountSID, authToken);

    // Retrieve the account, used later to create an instance of the SmsFactory.
    Account account = client.getAccount();

    // Send an SMS message.
    MessageFactory messageFactory = account.getMessageFactory();
    
    List<NameValuePair> params = new ArrayList<NameValuePair>();
    params.add(new BasicNameValuePair("To", "+14159352345")); // Replace with a valid phone number for your account.
    params.add(new BasicNameValuePair("From", "+14158141829")); // Replace with a valid phone number for your account.
    params.add(new BasicNameValuePair("Body", "Where's Wallace?"));
    
    Message sms = messageFactory.create(params);
        
Pour plus d’informations sur les paramètres passés à la méthode **SmsFactory.create** , voir [http://www.twilio.com/docs/api/rest/sending-sms][twilio_rest_sending_sms].

## <a id="howto_provide_twiml_responses"></a>Comment : fournir des réponses à partir de votre propre site Web TwiML
Lorsque votre application lance un appel à l’API Twilio, par exemple via la méthode **CallFactory.create** , Twilio envoie votre demande à une URL qui est censée renvoyer une réponse TwiML. L’exemple ci-dessus utilise l' URL fourni par Twilio [http://twimlets.com/message][twimlet_message_url]. (Alors que TwiML est conçu pour une utilisation par les services Web, vous pouvez afficher le TwiML dans votre navigateur. Par exemple, cliquez sur [http://twimlets.com/message] [ twimlet_message_url] pour voir vide ** &lt;réponse&gt; ** élément ; comme autre exemple, cliquez sur [http://twimlets.com/message?Message%5B0%5D=Hello%20World] [ twimlet_message_url_hello_world] de voir un ** &lt;réponse&gt; ** élément contenant un ** &lt;dites&gt; ** élément.)

Plutôt que d’utiliser l’URL fourni par Twilio, vous pouvez créer votre propre site URL qui renvoie des réponses HTTP. Vous pouvez créer le site dans n’importe quel langage qui renvoie des réponses HTTP ; Cette rubrique suppose que vous allez héberger l’URL dans une page JSP.

La page JSP suivante génère une réponse TwiML indiquant **Hello World** lors de l’appel.

    <%@ page contentType="text/xml" %>
    <Response> 
        <Say>Hello World</Say>
    </Response>

La page JSP suivante génère une réponse TwiML qui indique que du texte, possède plusieurs pauses et indique les informations sur la version de l’API de Twilio et le nom de rôle Azure.


    <%@ page contentType="text/xml" %>
    <Response> 
        <Say>Hello from Azure</Say>
        <Pause></Pause>
        <Say>The Twilio API version is <%= request.getParameter("ApiVersion") %>.</Say>
        <Say>The Azure role name is <%= System.getenv("RoleName") %>.</Say>
        <Pause></Pause>
        <Say>Good bye.</Say>
    </Response>

Le paramètre **ApiVersion** est disponible dans les demandes de voix Twilio (pas les requêtes SMS). Pour afficher les paramètres de requête disponibles pour les demandes SMS et de la Twilio vocale, voir <https://www.twilio.com/docs/api/twiml/twilio_request> et <https://www.twilio.com/docs/api/twiml/sms/twilio_request>, respectivement. La variable d’environnement **RoleName** est disponible dans le cadre d’un déploiement d’Azure. (Si vous souhaitez ajouter des variables d’environnement personnalisées afin qu’ils peuvent être récupérés à partir de **System.getenv**, reportez-vous à la section de variables d’environnement à [Divers paramètres de Configuration de rôle][misc_role_config_settings].)

Une fois que vous avez votre page JSP configuré pour fournir des réponses TwiML, utilisez l’URL de la page JSP sous l’URL passée à la méthode **CallFactory.create** . Par exemple, si vous disposez d’une application Web nommée MyTwiML déployé sur un Azure hébergé le service et le nom de la page JSP est mytwiml.jsp, l’URL peut être passé à **CallFactory.create** , comme illustré ci-dessous :

    // Place the call From, To and URL values into a hash map. 
    HashMap<String, String> params = new HashMap<String, String>();
    params.put("From", "NNNNNNNNNN");
    params.put("To", "NNNNNNNNNN");
    params.put("Url", "http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.jsp");

    CallFactory callFactory = account.getCallFactory();
    Call call = callFactory.create(params);

Une autre option pour répondre avec TwiML est la classe **TwiMLResponse** , qui est disponible dans le package **com.twilio.sdk.verbs** .

Pour plus d’informations sur l’utilisation de Twilio dans Azure avec Java, reportez-vous [à un Twilio à l’aide d’un appel dans une Application Java sur Azure][howto_phonecall_java].

## <a id="AdditionalServices"></a>Comment : utiliser des Services supplémentaires Twilio
Outre les exemples donnés ci-dessus, Twilio propose des API basée sur le web qui vous permet de tirer parti des fonctionnalités supplémentaires Twilio à partir de votre application Azure. Pour plus d’informations, consultez la [documentation de l’API de Twilio] [twilio_api_documentation].

## <a id="NextSteps"></a>Étapes suivantes
Maintenant que vous avez appris les notions de base du service Twilio, cliquez sur ces liens pour en savoir plus :

* [Consignes de sécurité Twilio] [twilio_security_guidelines]
* [De Twilio HowTo et l’exemple de Code] [twilio_howtos]
* [Didacticiels de démarrage rapide de Twilio][twilio_quickstarts] 
* [Twilio sur GitHub] [twilio_on_github]
* [Communiquer avec prise en charge de Twilio] [twilio_support]

[twilio_java]: https://github.com/twilio/twilio-java
[twilio_api_service]: https://api.twilio.com
[add_ca_cert]: java-add-certificate-ca-store.md
[howto_phonecall_java]: partner-twilio-java-phone-call-example.md
[misc_role_config_settings]: http://msdn.microsoft.com/library/windowsazure/hh690945.aspx
[twimlet_message_url]: http://twimlets.com/message
[twimlet_message_url_hello_world]: http://twimlets.com/message?Message%5B0%5D=Hello%20World
[twilio_rest_making_calls]: http://www.twilio.com/docs/api/rest/making-calls
[twilio_rest_sending_sms]: http://www.twilio.com/docs/api/rest/sending-sms
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
