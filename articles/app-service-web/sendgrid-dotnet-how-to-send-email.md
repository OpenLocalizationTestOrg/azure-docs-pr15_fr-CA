<properties 
    pageTitle="Comment faire pour utiliser le service de messagerie (.NET) SendGrid | Microsoft Azure" 
    description="Découvrez comment envoyer des e-mails avec le service de messagerie SendGrid sur Azure. Écrit en C# des exemples de code et utilisez l’API .NET." 
    services="app-service\web" 
    documentationCenter=".net" 
    authors="thinkingserious" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="01/14/2016" 
    ms.author="team-pi@sendgrid.com"/>





# <a name="how-to-send-email-using-sendgrid-with-azure"></a>Comment faire pour envoyer du courrier électronique à l’aide de SendGrid avec Azure


## <a name="overview"></a>Vue d’ensemble

Ce guide montre comment effectuer des tâches de programmation courantes avec le service de messagerie SendGrid sur Azure. Les exemples sont écrits en C\#
et utilisation de l’API .NET. Les scénarios présentés incluent la **construction d’e-mail**, **envoi de courrier électronique**, **Ajout de pièces jointes**et **l’utilisation de filtres**. Pour plus d’informations sur SendGrid et envoi de courrier électronique, consultez la section [étapes suivantes][] .

## <a name="what-is-the-sendgrid-email-service"></a>Quel est le service de messagerie SendGrid ?

SendGrid est un [service de messagerie basés sur le cloud] qui fournit fiable [des e-mails transactionnelle], d’évolutivité et analytique en temps réel avec une API flexible qui facilitent l’intégration personnalisée. Scénarios d’utilisation communs SendGrid sont les suivantes :

-   Envoi d’accusés de réception automatiquement aux clients.
-   Administration de distribution répertorie pour l’envoi de clients e-prospectus mensuels et des offres spéciales.
-   Collecte des métriques en temps réel pour des éléments tels que le courrier électronique bloqué et de réactivité pour les clients.
-   Génération de rapports pour aider à identifier les tendances.
-   Transfert des demandes des clients.
-   Traitement des messages entrants.

Pour plus d’informations, consultez notre [bibliothèque C#][csharp-sendgrid] ou [https://sendgrid.com](https://sendgrid.com)

## <a name="create-a-sendgrid-account"></a>Créer un compte SendGrid

[AZURE.INCLUDE [sendgrid-sign-up](../../includes/sendgrid-sign-up.md)]

## <a name="reference-the-sendgrid-net-class-library"></a>Référence de la bibliothèque de classes .NET de SendGrid

Le [package SendGrid NuGet](https://www.nuget.org/packages/Sendgrid) est le moyen le plus simple pour obtenir l’API SendGrid et pour configurer votre application avec toutes les dépendances. NuGet est une extension de Visual Studio fournie avec Microsoft Visual Studio 2015 qui le rend facile à installer et mettre à jour des bibliothèques et des outils. 

> [AZURE.NOTE] Pour installer NuGet si vous exécutez une version de Visual Studio antérieures à Visual Studio 2015, visitez le site [http://www.nuget.org](http://www.nuget.org)et cliquez sur le bouton **Installer de NuGet** .

Pour installer le package NuGet de SendGrid dans votre application, effectuez les opérations suivantes :

1.  Permet de créer un nouveau projet.

    ![Créez un nouveau projet][create-new-project]

2.  Sélectionnez un modèle.

    ![Sélectionnez un modèle][select-a-template]

3.  Dans l' **Explorateur de solutions**, cliquez sur **références**, puis cliquez sur **Gérer les Packages NuGet**.

4.  Recherchez **SendGrid** et sélectionnez **SendGrid** dans la liste des résultats.

    ![Package NuGet de SendGrid][SendGrid-NuGet-package]

5.  Cliquez sur **installer** pour terminer l’installation et fermez cette boîte de dialogue.

Bibliothèque de classes .NET du SendGrid est appelée **SendGridMail**. Il contient les espaces de noms suivants :

-   **SendGridMail** pour la création et l’utilisation des éléments de courrier électronique.
-   **SendGridMail.Transport** pour l’envoi de courrier électronique utilisant le protocole **SMTP** , ou le protocole HTTP 1.1 avec **Web/reste**.

Ajoutez les déclarations d’espace de noms de code suivantes en haut de n’importe quel C\# fichier dans lequel vous souhaitez accéder par programme au service de messagerie SendGrid.
**System.Net** et **System.Net.Mail** sont des espaces de noms.NET Framework qui sont incluses car elles incluent les types que vous utiliserez couramment avec les APIs SendGrid.

    using System;
    using System.Net;
    using System.Net.Mail;
    using SendGrid;

## <a name="how-to-create-an-email"></a>Comment : créer un e-mail

Utilisez l’objet **SendGridMessage** pour créer un message électronique. Une fois que l’objet du message est créé, vous pouvez définir les propriétés et méthodes, y compris l’expéditeur de l’e-mail, le destinataire de l’e-mail et l’objet et le corps du message.

L’exemple suivant montre comment créer un objet de messagerie entièrement remplie :

    // Create the email object first, then add the properties.
    var myMessage = new SendGridMessage();

    // Add the message properties.
    myMessage.From = new MailAddress("john@example.com");

    // Add multiple addresses to the To field.
    List<String> recipients = new List<String>
    {
        @"Jeff Smith <jeff@example.com>",
        @"Anna Lidman <anna@example.com>",
        @"Peter Saddow <peter@example.com>"
    };

    myMessage.AddTo(recipients);

    myMessage.Subject = "Testing the SendGrid Library";

    //Add the HTML and Text bodies
    myMessage.Html = "<p>Hello World!</p>";
    myMessage.Text = "Hello World plain text!";

Pour plus d’informations sur toutes les propriétés et méthodes prises en charge par le type **SendGrid** , consultez [csharp-sendgrid][] sur GitHub.

## <a name="how-to-send-an-email"></a>Comment : envoyer un courrier électronique

Après la création d’un message électronique, vous pouvez l’envoyer à l’aide de l’API Web fourni par SendGrid. Vous pouvez également utiliser des [. NET intégrée dans la bibliothèque](https://sendgrid.com/docs/Code_Examples/csharp.html).

Envoi de courrier électronique nécessite que vous fournissez vos informations d’identification du compte SendGrid (nom d’utilisateur et mot de passe) ou la clé de l’API SendGrid. Clé de l’API est la méthode recommandée. Si vous avez besoin de plus d’informations sur la façon de configurer les clés de l’API, consultez notre [documentation](https://sendgrid.com/docs/Classroom/Send/api_keys.html)

Vous pouvez stocker ces informations d’identification via votre portail Azure en cliquant sur Configurer et en ajoutant les paires clé/valeur sous « paramètres de l’application ».

 ![Paramètres de l’application Azure][azure_app_settings]

 Puis, vous pouvez y accéder comme suit : 
    
    var username = System.Environment.GetEnvironmentVariable("SENDGRID_USERNAME"); 
    var pswd = System.Environment.GetEnvironmentVariable("SENDGRID_PASSWORD");
    var apiKey = System.Environment.GetEnvironmentVariable("SENDGRID_APIKEY");

À l’aide des informations d’identification :
    
    // Create network credentials to access your SendGrid account
    var username = "your_sendgrid_username";
    var pswd = "your_sendgrid_password";

    var credentials = new NetworkCredential(username, pswd);
    // Create an Web transport for sending email.
    var transportWeb = new Web(credentials);

À l’aide de la clé de l’API :

    var apiKey = "your_sendgrid_api_key";  
    // create a Web transport, using API Key
    var transportWeb = new Web(apiKey);


Les exemples suivants montrent comment envoyer un message à l’aide de l’API Web.

    // Create the email object first, then add the properties.
    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    // Create credentials, specifying your user name and password.
    var credentials = new NetworkCredential("username", "password");

    // Create an Web transport for sending email.
    var transportWeb = new Web(credentials);

    // Send the email, which returns an awaitable task.
    transportWeb.DeliverAsync(myMessage);

    // If developing a Console Application, use the following
    // transportWeb.DeliverAsync(mail).Wait();

## <a name="how-to-add-an-attachment"></a>Comment : ajouter une pièce jointe

Les pièces jointes peuvent être ajoutés à un message en appelant la méthode **AddAttachment** et en spécifiant le nom et le chemin d’accès du fichier à joindre.
Vous pouvez inclure plusieurs pièces jointes en appelant cette méthode une fois pour chaque fichier que vous souhaitez relier. L’exemple suivant illustre l’ajout d’une pièce jointe à un message :

    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    myMessage.AddAttachment(@"C:\file1.txt");
    
Vous pouvez également ajouter des pièces jointes à partir de données **flux**. Elle peut être effectuée en appelant la même méthode que ci-dessus, **AddAttachment**, mais en passant dans le flux de données, et le nom du fichier que vous souhaitez qu’il s’affiche dans le message. Dans ce cas, vous devrez ajouter la bibliothèque de System.IO.

    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    using (var attachmentFileStream = new FileStream(@"C:\file.txt", FileMode.Open))
    {
        myMessage.AddAttachment(attachmentFileStream, "My Cool File.txt");
    }


## <a name="how-to-use-apps-to-enable-footers-tracking-and-analytics"></a>Comment : applications permet d’activer les pieds de page, de suivi et analytique

SendGrid fournit des fonctionnalités de messagerie supplémentaires à l’aide d’applications. Il s’agit des paramètres qui peuvent être ajoutées à un message électronique pour activer des fonctionnalités spécifiques telles que le suivi des clics, Google analytique, abonnement de suivi, et ainsi de suite. Pour obtenir une liste complète des applications, reportez-vous à la section [Paramètres de l’application][].

Applications peuvent être appliquées à des messages électroniques de **SendGrid** à l’aide de méthodes implémentées en tant que partie de la classe **SendGrid** .

Les exemples suivants illustrent le pied de page et cliquez sur suivi des filtres :

### <a name="footer"></a>Pied de page

    // Create the email object first, then add the properties.
    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    // Add a footer to the message.
    myMessage.EnableFooter("PLAIN TEXT FOOTER", "<p><em>HTML FOOTER</em></p>");

### <a name="click-tracking"></a>Cliquez sur suivi

    // Create the email object first, then add the properties.
    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Html = "<p><a href=\"http://www.example.com\">Hello World Link!</a></p>";
    myMessage.Text = "Hello World!";
    
    // true indicates that links in plain text portions of the email 
    // should also be overwritten for link tracking purposes. 
    myMessage.EnableClickTracking(true);

## <a name="how-to-use-additional-sendgrid-services"></a>Comment : utiliser des services de SendGrid supplémentaires

SendGrid propose des API basées sur le web et les webhooks qui vous permet de tirer parti des fonctionnalités supplémentaires SendGrid à partir de votre application Azure. Pour plus d’informations, consultez la [documentation de l’API de SendGrid][].

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris les notions de base du service E-mail de SendGrid, cliquez sur ces liens pour en savoir plus.

*   SendGrid C\# mis en pension de bibliothèque : [csharp-sendgrid][]
*   Documentation sur les API de SendGrid : <https://sendgrid.com/docs>
*   Offre spéciale de SendGrid pour les clients Azure : [https://sendgrid.com](https://sendgrid.com)

  [Étapes suivantes]: #next-steps
  [What is the SendGrid Email Service?]: #whatis
  [Create a SendGrid Account]: #createaccount
  [Reference the SendGrid .NET Class Library]: #reference
  [How to: Create an Email]: #createemail
  [How to: Send an Email]: #sendemail
  [How to: Add an Attachment]: #addattachment
  [How to: Use Filters to Enable Footers, Tracking, and Analytics]: #usefilters
  [How to: Use Additional SendGrid Services]: #useservices
  
  [special offer]: https://www.sendgrid.com/windowsazure.html
  
  [create-new-project]: ./media/sendgrid-dotnet-how-to-send-email/create_new_project.png
  [select-a-template]: ./media/sendgrid-dotnet-how-to-send-email/select_a_template.png
  [SendGrid-NuGet-package]: ./media/sendgrid-dotnet-how-to-send-email/sendgrid_nuget.png
  [azure_app_settings]: ./media/sendgrid-dotnet-how-to-send-email/app_settings.png
  [sendgrid-csharp]: https://github.com/sendgrid/sendgrid-csharp
  [SMTP vs. Web API]: https://sendgrid.com/docs/Integrate/index.html
  [Paramètres de l’application]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
  [Documentation sur les API de SendGrid]: https://sendgrid.com/docs
  
  [service de messagerie basés sur le cloud]: https://sendgrid.com/email-solutions
  [livraison de messagerie transactionnelle]: https://sendgrid.com/transactional-email
 
