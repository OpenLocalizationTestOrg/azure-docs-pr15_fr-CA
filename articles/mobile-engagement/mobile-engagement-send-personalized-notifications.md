<properties 
    pageTitle="Envoyer une notification personnalisée avec Azure Mobile Engagement" 
    description="Comment faire pour envoyer des notifications personnalisées en incluant des informations de profil utilisateur dans les notifications, telles que leurs noms"        
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="dwrede" 
    editor="" />

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="all" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

#<a name="personalize-notifications-by-including-user-name"></a>Personnaliser les notifications en incluant le nom d’utilisateur

Dans votre quête d’adresser des notifications plus attrayant pour les utilisateurs de votre application, vous devez envisager de les personnaliser. Une approche puissante comprend sélective à l’aide de noms utilisateurs app pour traiter les notifications pour les rendre plus personnelle. Un mot de prudence ici - vous devriez avoisiner utilisateur Ajouter des noms aux notifications avec soin car si vous abusez de cette stratégie puis il pourrait rencontrer que vous pour certains utilisateurs de l’application. Vous devez également vous assurer que vous laissez l’utilisateur choisir de fournir ces informations personnelles pour vous un consentement total dans l’application mobile avant de commencer à utiliser cette. 

Techniquement, avec l’Engagement Azure Mobile, vous pouvez accomplir de personnalisation des notifications en suivant les étapes ci-dessous, dans laquelle nous allons utiliser le scénario d’inclure le nom d’utilisateur dans les notifications. Vous allez utiliser le concept de App-Info ou dont les valeurs peuvent être transférés par les kits de développement logiciel des balises intégré dans l’application Mobile, ou via les API. Ces balises ou l’application-Infos pourraient alors être utilisées :

1. pour le ciblage des notifications à des utilisateurs spécifiques en fonction des valeurs de l’application-Info ou 
2. en tant qu’espaces réservés dans les notifications qui seront remplacées par des valeurs spécifiques à l’utilisateur d’appareil/lors de l’envoi de notifications à ce périphérique. 

> [AZURE.IMPORTANT] Notez que la vitesse d’envoi de notifications verront une réduction en raison de ce traitement supplémentaire de remplacement des valeurs de app-info avec chaque notification. 

##<a name="register-app-info-in-the-mobile-engagement-portal"></a>Registre App-Info dans le portail Mobile d’Engagement

1) Vous démarrez avec l’enregistrement d’App Info ou des balises dans le portail Azure. Accédez à **paramètres** -> cette**balise (App-Info)** .  

![][1]  

2) Cliquez sur **nouvelle balise (app-info)** et fournir le nom en tant que *nom_utilisateur* et le type de *chaîne* et cliquez sur **Envoyer**. 

![][2]

3) Vous verrez enfin cette application-info inscrit comme suit :

![][3]

##<a name="send-app-info-from-the-client-sdk"></a>Envoyer infos de l’application à partir du Kit de développement logiciel du client

Ici, nous utilisons l’exemple d’application Windows universel mais méthodes équivalentes existent pour nos autres kits SDK également. 

En supposant que vous avez une méthode dans l’application mobile où vous obtenez les informations de profil de l’utilisateur telles que leurs noms probablement après leur authentification, vous pouvez appeler `SendAppInfo` méthode ici et la valeur de la `user_name` Infos d’application que vous avez enregistré précédemment dans le back-end du service Mobile Engagement. 

    Dictionary<object, object> appInfo = new Dictionary<object, object>();
    appInfo.Add("user_name", str);
    EngagementAgent.Instance.SendAppInfo(appInfo); 

##<a name="send-personalized-notifications"></a>Envoyer des notifications personnalisées

Vous êtes maintenant tous ensemble pour envoyer des notifications à l’aide de cette **nom_utilisateur**. 

1) Accédez au portail d’Engagement de Mobile sous l’onglet **Atteindre** pour créer une notification et vous pouvez utiliser cet espace réservé dans le format suivant n’importe où dans le titre de la notification ou de l’organisme. 

![][4]  

> [AZURE.NOTE] Tous les utilisateurs pour lesquels les informations d’application nom_utilisateur ne sont pas définie, ne recevra pas de notification. Si vous exécutez la campagne de notification en mode test et si vous n’avez pas défini, nous vous enverrons de app-info ' ?' caractère à remplacer l’espace réservé. 

2) Lorsque Mobile Engagement sélectionnera un périphérique pour transmettre la notification ensuite examiner cette application-info et remplacez la valeur dans l’espace réservé.  
Par exemple, si nous avons défini `str = "Scott"` pour un utilisateur que le périphérique enregistrement obtiendra associé avec les informations de l’application de **nom_utilisateur = SCOTT** pour cet utilisateur et l’utilisateur verra une absence de notification de transmission app dans le format suivant. 

![][5]  

<!-- Images. -->
[1]: ./media/mobile-engagement-send-personalized-notifications/app-info.png
[2]: ./media/mobile-engagement-send-personalized-notifications/create-app-info.png
[3]: ./media/mobile-engagement-send-personalized-notifications/app-info-user-name.png
[4]: ./media/mobile-engagement-send-personalized-notifications/personal-notification.png
[5]: ./media/mobile-engagement-send-personalized-notifications/notification.png

