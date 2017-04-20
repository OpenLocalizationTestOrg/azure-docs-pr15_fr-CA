<properties
    pageTitle="Mise en route avec Azure Mobile Engagement pour les applications Web | Microsoft Azure"
    description="Découvrez comment utiliser Azure Mobile Engagement analytique et la diffusion des notifications pour les applications Web."
    services="mobile-engagement"
    documentationCenter="Mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="js"
    ms.topic="hero-article"
    ms.date="06/01/2016"
    ms.author="piyushjo" />

# <a name="get-started-with-azure-mobile-engagement-for-web-apps"></a>Mise en route avec Azure Mobile Engagement pour les applications Web

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Cette rubrique vous indique comment utiliser Azure Mobile Engagement de comprendre l’utilisation de votre application Web.

Ce didacticiel requiert les éléments suivants :

+ Visual Studio 2015 ou tout autre éditeur de votre choix.
+ [Kit de développement de Web](http://aka.ms/P7b453) 

Ce kit de développement Web est en mode Aperçu avant et seulement prend en charge Analytique pour le moment et ne gère pas encore d’envoyer des notifications push navigateur ou dans l’application. 

> [AZURE.NOTE] Pour terminer ce didacticiel, vous devez disposer d’un compte Azure actif. Si vous n’avez pas un compte, vous pouvez créer un compte d’essai gratuit dans quelques minutes. Pour plus d’informations, reportez-vous à la section [D’essai Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-web-app-get-started).

##<a name="setup-mobile-engagement-for-your-web-app"></a>Le programme d’installation de Mobile Engagement pour votre application Web

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

##<a id="connecting-app"></a>Connecter votre application à la principale mission de Mobile

Ce didacticiel présente une « base intégration », qui est l’ensemble minimal requis pour collecter des données.

Nous allons créer une application web de base avec Visual Studio pour illustrer l’intégration, bien que vous pouvez suivre les étapes avec n’importe quelle application web également créée en dehors de Visual Studio. 

###<a name="create-a-new-web-app"></a>Créer une nouvelle application Web

Les étapes suivantes supposent l’utilisation de Visual Studio 2015, bien que les étapes sont identiques dans les versions antérieures de Visual Studio. 

1. Démarrez Visual Studio et dans l’écran **d’accueil** , sélectionnez **Nouveau projet**.

2. Dans le menu contextuel, sélectionnez le **site Web** -> **Application Web ASP.Net**. Renseignez l’application **nom**, **emplacement** et le **nom de la Solution**et puis cliquez sur **OK**.

3. Dans le menu contextuel **Sélectionnez un modèle** , sélectionnez **vide** sous **ASP.Net 4.5 modèles** , puis cliquez sur **OK**. 

Vous avez maintenant créé un nouveau projet d’application Web vide dans lequel nous intégreront le Kit de développement Web de mission Mobile Azure.

###<a name="connect-your-app-to-mobile-engagement-backend"></a>Votre application de se connecter au back-end de l’Engagement de Mobile

1. Créer un nouveau dossier appelé **javascript** dans votre solution et ajouter fichier de Web SDK JS **azure-engagement.js** dedans. 

2. Ajouter un nouveau fichier nommé **main.js** dans ce dossier de javascript par le code suivant. Veillez à mettre à jour la chaîne de connexion. Cette `azureEngagement` objet sera utilisé pour accéder aux méthodes du Kit de développement Web. 

        var azureEngagement = {
            debug: true,
            connectionString: 'xxxxx'
        };

    ![Visual Studio avec des fichiers js][1]

##<a name="enable-real-time-monitoring"></a>Activer l’analyse en temps réel

Avant de commencer l’envoi de données et de veiller à ce que les utilisateurs sont actifs, vous devez envoyer au moins une activité sur le back-end de l’Engagement de Mobile. Une activité dans le contexte d’une application web est une page web. 

1. Créez une page appelée **home.html** dans votre solution et le définir comme la page de démarrage pour votre application web. 
2. Inclure les deux scripts de JavaScript que nous ajoutés précédemment dans cette page en ajoutant le code suivant dans la balise body. 

        <script type="text/javascript" src="javascript/main.js"></script>
        <script type="text/javascript" src="javascript/azure-engagement.js"></script>

3. Mise à jour de la balise body pour appeler EngagementAgent `startActivity` méthode
        
        <body onload="engagement.agent.startActivity('Home')">

4. Voici à quoi ressemblera votre **home.html**
        
        <html>
        <head>
            ...
        </head>
        <body onload="engagement.agent.startActivity('Home')">
            <script type="text/javascript" src="javascript/main.js"></script>
            <script type="text/javascript" src="javascript/azure-engagement.js"></script>
        </body>
        </html>

##<a name="connect-app-with-real-time-monitoring"></a>Application de se connecter grâce à une surveillance en temps réel

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

![][2]

##<a name="extend-analytics"></a>Étendre analytique

Voici toutes les méthodes actuellement disponibles avec le Kit de développement Web que vous pouvez utiliser pour analytique :

1. Pages Web/activités :

        engagement.agent.startActivity(name);
        engagement.agent.endActivity();

2. Événements
        
        engagement.agent.sendEvent(name, extras);

3. Erreurs

        engagement.agent.sendError(name, extras);

4. Travaux

        engagement.agent.startJob(name);
        engagement.agent.endJob(name);

<!-- Images. -->
[1]: ./media/mobile-engagement-web-app-get-started/visual-studio-solution-js.png
[2]: ./media/mobile-engagement-web-app-get-started/session.png

