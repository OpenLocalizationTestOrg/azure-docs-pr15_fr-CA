<properties
    pageTitle="Sécurité OAUTH dans les applications d’API et de connecteurs de SaaS | Azure"
    description="En savoir plus sur la sécurité OAUTH dans les connecteurs et les applications d’API dans le Service d’application Azure ; architecture de microservices ; SaaS"
    services="logic-apps"
    documentationCenter=""
    authors="MandiOhlinger"
    manager="dwrede"
    editor="cgronlun"/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/23/2016"
    ms.author="mandia"/>


# <a name="learn-about-oauth-security-in-saas-connectors"></a>En savoir plus sur la sécurité de OAUTH dans les connecteurs de SaaS

>[AZURE.NOTE] Cette version de l’article s’applique à la version du schéma logique apps 2014-12-01-aperçu.

La plupart des logiciels en tant que les connecteurs tels que Facebook, Twitter, échange et ainsi de suite demander aux utilisateurs de s’authentifier en utilisant le protocole OAUTH un Service (SaaS).  Lorsque vous utilisez ces connecteurs SaaS à partir de la logique d’applications, nous fournir une expérience utilisateur simplifiée où vous cliquez sur « Autoriser » dans le Concepteur d’applications de logique. Lorsque vous **Autoriser**, vous êtes invité à vous connecter à (si ce n’est pas déjà) et fournissent le consentement pour se connecter au service SaaS à votre place. Après que vous fournissent le consentement et autoriser, vos applications de logique peut ensuite accéder à ces services de SaaS.

## <a name="create-your-own-saas-app"></a>Créer votre propre application de SaaS
Cette expérience simplifiée est possible parce que nous avons précédemment créé et inscrit votre application dans ces services SaaS.  Dans certains cas, vous souhaiterez peut-être enregistrer et utiliser votre propre application.  Cela est nécessaire, par exemple, lorsque vous souhaitez utiliser ces connecteurs SaaS dans vos applications personnalisées. Cet exemple utilise le connecteur de l’échange, mais le processus est le même pour tous les connecteurs qui s’appuient sur OAUTH.

Même dans le cadre de la logique d’applications, vous pouvez utiliser votre propre application au lieu d’utiliser l’application par défaut que nous fournissons. Si le bouton « Autoriser » ne parvient pas à se connecter, vous pouvez essayer de créer votre propre application. Voici ces étapes pour le connecteur Twitter :

1. Ouvrez votre connecteur Twitter dans le portail Azure aperçu. Accédez à **Parcourir** > **applications d’API**. Sélectionnez votre connecteur Twitter :  
    ![][1]

2. Sélectionnez **paramètres** > **l’authentification**:  
    ![][2]

3. Copie la valeur de **l’URI de redirection** :  
    ![][3]

4. Accédez à [Twitter](http://apps.twitter.com) et **créer une nouvelle application**. Dans la propriété **URL de rappel** , collez la valeur de **Rediriger l’URI** copiée à partir de votre connecteur Twitter : ![][4]  
5. Lors de la création de votre application Twitter, sélectionnez **clé et jetons d’accès**. Copiez ces valeurs.
6. Dans vos paramètres d’authentification du connecteur Twitter, coller ces valeurs dans les propriétés de **l’ID de Client** et le **Secret de Client** :   
    ![][5]  
7. Enregistrer vos paramètres de connecteur.  

Maintenant, vous devez être en mesure d’utiliser votre connecteur à partir de la logique d’applications. Lorsque vous utilisez ce connecteur à partir d’applications de logique, il utilise votre application au lieu de l’application par défaut.  

> [AZURE.NOTE] Si vous avez autorisé une application précédemment, vous devrez peut-être autoriser l’application.


<!--Image references-->
[1]: ./media/app-service-logic-oauth-security/TwitterConnector.png
[2]: ./media/app-service-logic-oauth-security/Authentication.png
[3]: ./media/app-service-logic-oauth-security/RedirectURI.png
[4]: ./media/app-service-logic-oauth-security/TwitterApp.png
[5]: ./media/app-service-logic-oauth-security/TwitterKeys.png
