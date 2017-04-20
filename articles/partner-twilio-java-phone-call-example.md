<properties 
    pageTitle="Comment faire pour effectuer un appel téléphonique à partir de Twilio (Java) | Microsoft Azure" 
    description="Apprenez à effectuer un appel téléphonique à partir d’une page web à l’aide de Twilio dans une application Java sur Azure." 
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

# <a name="how-to-make-a-phone-call-using-twilio-in-a-java-application-on-azure"></a>Comment faire pour effectuer un appel téléphonique à l’aide de Twilio dans une Application Java sur Azure 

L’exemple suivant vous montre comment vous pouvez utiliser Twilio pour effectuer un appel à partir d’une page web hébergée dans Azure. L’application résultante invite l’utilisateur pour les valeurs de l’appel téléphonique, comme illustré dans la capture d’écran suivante.

![Formulaire appel Azure à l’aide de Twilio et Java][twilio_java]

Vous devez effectuer les opérations suivantes pour utiliser le code dans cette rubrique :

1. Acquérir une authentification et un Twilio compte jeton. Pour vous familiariser avec Twilio, évaluer la tarification au [http://www.twilio.com/pricing][twilio_pricing]. Vous pouvez vous inscrire à [https://www.twilio.com/try-twilio][try_twilio]. Pour plus d’informations sur l’API fournie par Twilio, voir [http://www.twilio.com/api][twilio_api].
2. Obtenir le Twilio JAR. Au [https://github.com/twilio/twilio-java][twilio_java_github], vous pouvez télécharger les sources les GitHub et créer votre propre fichier JAR ou télécharger un JAR prédéfini (avec ou sans dépendances).
Le code de cette rubrique a été écrite à l’aide du fichier JAR TwilioJava 3.3.8 avec dépendances prédéfini.
3. Ajoutez le fichier JAR à votre chemin d’accès de la version Java.
4. Si vous utilisez Eclipse pour créer cette application Java, incluent le JAR Twilio dans votre fichier de déploiement de l’application (WAR) à l’aide de la fonction d’assemblage du Éclipse déploiement. Si vous n’utilisez pas Eclipse pour créer cette application Java, vérifiez le JAR Twilio est inclus dans le rôle Azure même que votre application Java et ajouté au chemin des classes de votre application.
5. Assurez-vous que votre magasin de clés cacerts contient le certificat de l’autorité de certification Equifax Secure avec 67:CB:9 d’empreinte digitale MD5 D : C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 (le numéro de série est le 35:DE:F4:CF et l’empreinte SHA1 est D2:32:09:AD:23:D3:14:23:21:74:E4:0 D : 7F:9 D : 62:13:97:86:63:3A). Il s’agit de l’autorité de certification pour le [https://api.twilio.com] [ twilio_api_service] service, qui est appelé lorsque vous utilisez Twilio APIs. Pour plus d’informations sur l’ajout de ce certificat d’autorité de certification au magasin de cacert de votre JDK, consultez [Ajout d’un certificat dans le magasin de certificats d’autorité de certification Java][add_ca_cert].

En outre, vous familiariser avec les informations relatives à la [Création d’un Hello World Application à l’aide de la Shared Computer Toolkit Azure pour Éclipse][azure_java_eclipse_hello_world], ou à d’autres techniques pour l’hébergement des applications Java dans Azure si vous n’utilisez pas Eclipse, est recommandé.

## <a name="create-a-web-form-for-making-a-call"></a>Créer un formulaire web pour effectuer un appel

Le code suivant montre comment créer un formulaire web pour récupérer des données d’utilisateur d’un appel. Pour les besoins de cet exemple, un projet web dynamic, nommé **TwilioCloud**, a été créé et **callform.jsp** a été ajouté sous la forme d’un fichier JSP.

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
        pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Automated call form</title>
    </head>
    <body>
     <p>Fill in all fields and click <b>Make this call</b>.</p>
     <br/>
      <form action="makecall.jsp" method="post">
       <table>
         <tr>
           <td>To:</td>
           <td><input type="text" size=50 name="callTo" value="" />
           </td>
         </tr>
         <tr>
           <td>From:</td>
           <td><input type="text" size=50 name="callFrom" value="" />
           </td>
         </tr>
         <tr>
           <td>Call message:</td>
           <td><input type="text" size=400 name="callText" value="Hello. This is the call text. Good bye." />
           </td>
         </tr>
         <tr>
           <td colspan=2><input type="submit" value="Make this call" />
           </td>
         </tr>
       </table>
     </form>
     <br/>
    </body>
    </html>

## <a name="create-the-code-to-make-the-call"></a>Créer le code pour effectuer l’appel
Le code suivant, qui est appelé lorsque l’utilisateur termine le formulaire affiché par callform.jsp, crée le message d’appel et génère l’appel. Pour les besoins de cet exemple, le fichier JSP est nommé **makecall.jsp** et a été ajouté au projet **TwilioCloud** . (Utilisez votre compte de Twilio et de l’authentification des jetons au lieu des valeurs d’espace réservé affectées à **accountSID** et **jeton d’authentification** dans le code ci-dessous).

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
    import="java.util.*"
    import="com.twilio.*"
    import="com.twilio.sdk.*"
    import="com.twilio.sdk.resource.factory.*"
    import="com.twilio.sdk.resource.instance.*"
    pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Call processing happens here</title>
    </head>
    <body>
        <b>This is my make call page.</b><p/>
     <%
    try 
    {
         // Use your account SID and authentication token instead
         // of the placeholders shown here.
         String accountSID = "your_twilio_account";
         String authToken = "your_twilio_authentication_token";
     
         // Instantiate an instance of the Twilio client.     
         TwilioRestClient client;
         client = new TwilioRestClient(accountSID, authToken);

         // Retrieve the account, used later to retrieve the CallFactory.
         Account account = client.getAccount();

         // Display the client endpoint. 
         out.println("<p>Using Twilio endpoint " + client.getEndpoint() + ".</p>");
     
         // Display the API version.
         String APIVERSION = TwilioRestClient.DEFAULT_VERSION;
         out.println("<p>Twilio client API version is " + APIVERSION + ".</p>");
    
         // Retrieve the values entered by the user.
         String callTo = request.getParameter("callTo");  
         // The Outgoing Caller ID, used for the From parameter,
         // must have previously been verified with Twilio.
         String callFrom = request.getParameter("callFrom");
         String userText = request.getParameter("callText");
     
         // Replace spaces in the user's text with '%20', 
         // to make the text suitable for a URL.
         userText = userText.replace(" ", "%20");
     
         // Create a URL using the Twilio message and the user-entered text.
         String Url="http://twimlets.com/message";
         Url = Url + "?Message%5B0%5D=" + userText;
     
         // Display the message URL.
         out.println("<p>");
         out.println("The URL is " + Url);
         out.println("</p>");
    
         // Place the call From, To and URL values into a hash map. 
         HashMap<String, String> params = new HashMap<String, String>();
         params.put("From", callFrom);
         params.put("To", callTo);
         params.put("Url", Url);
     
         CallFactory callFactory = account.getCallFactory();
         Call call = callFactory.create(params);
         out.println("<p>Call status: " + call.getStatus()  + "</p>"); 
    } 
    catch (TwilioRestException e) 
    {
        out.println("<p>TwilioRestException encountered: " + e.getMessage() + "</p>");
        out.println("<p>StackTrace: " + e.getStackTrace().toString() + "</p>");
    }
    catch (Exception e) 
    {
        out.println("<p>Exception encountered: " + e.getMessage() + "");
        out.println("<p>StackTrace: " + e.getStackTrace().toString() + "</p>");
    }
    %>
    </body>
    </html>

En plus de rendre l’appel, makecall.jsp affiche le point de terminaison Twilio, version de l’API et le statut de l’appel. Par exemple, la capture d’écran suivante :

![Réponse appel d’Azure à l’aide de Twilio et Java][twilio_java_response]

## <a name="run-the-application"></a>Exécution de l’application
Voici les principales étapes à suivre pour exécuter votre application ; les détails de ces étapes, consultez [Création d’un Hello World Application à l’aide de la Shared Computer Toolkit Azure pour Éclipse][azure_java_eclipse_hello_world].

1. Exporter votre guerre TwilioCloud vers le dossier Azure **approot** . 
2. Modifiez **startup.cmd** pour décompresser votre guerre TwilioCloud.
3. Compilez votre application pour l’émulateur de calcul.
4. Démarrer le déploiement dans l’émulateur de calcul.
5. Ouvrez un navigateur et exécuter **http://localhost:8080/TwilioCloud/callform.jsp**.
6. Entrez des valeurs dans le formulaire et cliquez sur **appeler**, puis consultez les résultats dans makecall.jsp.

Lorsque vous êtes prêt à déployer sur Azure, recompile pour le déploiement vers le nuage, déployer sur Azure et exécuter http://*your_hosted_name*.cloudapp.net/TwilioCloud/callform.jsp dans le navigateur (Substituez votre valeur pour *your_hosted_name*).

## <a name="next-steps"></a>Étapes suivantes
Ce code a été fourni pour vous montrer les fonctionnalités de base à l’aide de Twilio dans Java sur Azure. Avant de déployer sur Azure dans la production, vous souhaiterez sans doute ajouter plus de gestion des erreurs ou d’autres fonctionnalités. Par exemple :

* Au lieu d’utiliser un formulaire web, vous pouvez utiliser les BLOB Azure de stockage ou base de données de SQL pour stocker les numéros de téléphone et appelez le texte. Pour plus d’informations sur l’utilisation des objets BLOB de stockage Azure dans Java, voir [comment utiliser le Service de stockage de Blob à partir de Java][howto_blob_storage_java]. Pour plus d’informations sur l’utilisation de la base de données SQL en Java, reportez-vous [à l’aide de la base de données SQL en Java][howto_sql_azure_java].
* Vous pouvez utiliser **RoleEnvironment.getConfigurationSettings** pour récupérer l’ID du compte Twilio et le jeton d’authentification à partir des paramètres de configuration de votre déploiement, au lieu de coder en dur les valeurs de makecall.jsp. Pour plus d’informations sur la classe **RoleEnvironment** , reportez-vous [à l’aide de la bibliothèque d’exécution de Service Azure dans JSP] [ azure_runtime_jsp] et de la documentation du package du Runtime de Service Azure à [http://dl.windowsazure.com/javadoc][azure_javadoc].
* Le code de makecall.jsp assigne une URL fournie par Twilio, [http://twimlets.com/message][twimlet_message_url], à la variable **d’Url** . Cette URL donne une réponse de langage de balisage Twilio (TwiML) qui indique comment procéder avec l’appel à Twilio. Par exemple, le TwiML est retournée peut contenir un ** &lt;dire&gt; ** verbe qui aboutit à une prononciation pour le destinataire de l’appel de texte. Au lieu d’utiliser l’URL fourni par Twilio, vous pouvez créer votre propre service pour répondre à la demande de de Twilio ; Pour plus d’informations, consultez [comment Twilio utilisation de la voix et les fonctionnalités de SMS dans Java][howto_twilio_voice_sms_java]. Vous trouverez plus d’informations sur le TwiML à [http://www.twilio.com/docs/api/twiml][twiml]et plus d’informations sur ** &lt;dire&gt; ** vous trouverez les autres verbes de Twilio à [http://www.twilio.com/docs/api/twiml/say]et[twilio_say].
* Lisez les consignes de sécurité Twilio à [https://www.twilio.com/docs/security][twilio_docs_security].

Pour plus d’informations sur Twilio, reportez-vous à [https://www.twilio.com/docs][twilio_docs].

## <a name="see-also"></a>Voir aussi
* [L’utilisation de Twilio pour la voix et les fonctionnalités SMS dans Java][howto_twilio_voice_sms_java]
* [Ajout d’un certificat au magasin de certificats d’autorité de certification Java][add_ca_cert]

[twilio_pricing]: http://www.twilio.com/pricing
[try_twilio]: http://www.twilio.com/try-twilio
[twilio_api]: http://www.twilio.com/api
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_java_github]: http://github.com/twilio/twilio-java
[twimlet_message_url]: http://twimlets.com/message
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api_service]: http://api.twilio.com
[add_ca_cert]: java-add-certificate-ca-store.md
[azure_java_eclipse_hello_world]: http://msdn.microsoft.com/library/windowsazure/hh690944.aspx
[howto_twilio_voice_sms_java]: partner-twilio-java-how-to-use-voice-sms.md
[howto_blob_storage_java]: http://www.windowsazure.com/develop/java/how-to-guides/blob-storage/
[howto_sql_azure_java]: http://msdn.microsoft.com/library/windowsazure/hh749029.aspx
[azure_runtime_jsp]: http://msdn.microsoft.com/library/windowsazure/hh690948.aspx
[azure_javadoc]: http://dl.windowsazure.com/javadoc
[twilio_docs_security]: http://www.twilio.com/docs/security
[twilio_docs]: http://www.twilio.com/docs
[twilio_say]: http://www.twilio.com/docs/api/twiml/say
[twilio_java]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaCallForm.jpg
[twilio_java_response]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaMakeCall.jpg
