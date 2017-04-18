<properties 
    pageTitle="Ajouter une application Java pour Azure Application Service Web Apps" 
    description="Ce didacticiel vous montre comment ajouter une page ou une application à votre instance d’Azure App Apps Service Web qui est déjà configuré pour utiliser Java." 
    services="app-service\web" 
    documentationCenter="java" 
    authors="rmcmurray" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

# <a name="add-a-java-application-to-azure-app-service-web-apps"></a>Ajouter une application Java pour Azure Application Service Web Apps

Une fois que vous avez initialisé votre application web de Java dans le [Service d’application Azure][] comme indiqué à la [Création d’une application web de Java dans le Service d’application Azure](web-sites-java-get-started.md), vous pouvez télécharger votre application en plaçant votre guerre dans le dossier **d’applications Web** .

Le chemin de navigation dans le dossier **applications Web** diffère selon la manière dont vous configurez votre instance de Web Apps.

- Si vous configurez votre application web à l’aide du marché d’Azure, le chemin d’accès au dossier **d’applications Web** est sous la forme **d:\home\site\wwwroot\bin\application\_server\webapps**, où **application\_serveur** est le nom du serveur d’application en vigueur pour votre instance de Web Apps. 
- Si vous configurez votre application web à l’aide de l’interface utilisateur de configuration Azure, le chemin d’accès au dossier **d’applications Web** est le formulaire **d:\home\site\wwwroot\webapps**. 

Notez que vous pouvez utiliser le contrôle de code source pour télécharger votre application ou des pages web, y compris des [scénarios d’intégration continue](app-service-continuous-deployment.md). FTP est également une option de téléchargement de votre application ou des pages web.

Remarque pour les applications web de Tomcat : une fois que vous avez téléchargé le fichier WAR dans le dossier **d’applications Web** , le serveur d’applications Tomcat détecte que vous avez ajouté et il chargera automatiquement. Notez que si vous copiez des fichiers (autres que les fichiers WAR) dans le répertoire racine, le serveur d’application doit être redémarrée avant que ces fichiers sont utilisés. La fonctionnalité autoload pour les applications web de Tomcat Java en cours d’exécution sur Azure est basée sur un fichier WAR ajouté, ou de nouveaux fichiers ou de répertoires ajoutés dans le dossier **d’applications Web** . 

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez le [Centre pour développeurs Java](/develop/java/).

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- External Links -->
[Service d’application Azure]: http://go.microsoft.com/fwlink/?LinkId=529714
