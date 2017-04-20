<properties 
    pageTitle="Authentifier avec les API de reste Engagement Mobile - installation manuelle"
    description="Décrit comment configurer manuellement l’authentification pour Mobile Engagement reste API" 
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo"
    manager="erikre"
    editor=""/>

<tags
    ms.service="mobile-engagement"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="mobile-multiple"
    ms.workload="mobile" 
    ms.date="08/19/2016"
    ms.author="piyushjo"/>

# <a name="authenticate-with-mobile-engagement-rest-apis---manual-setup"></a>Authentifier avec les API de reste Engagement Mobile - installation manuelle

Il s’agit d’une documentation annexe à [authentifier avec les API reste Mobile Engagement](mobile-engagement-api-authentication.md). Assurez-vous de que lire tout d’abord, pour obtenir le contexte. Ceci décrit une autre façon d’effectuer le programme d’installation unique pour configurer l’authentification pour les API de reste d’Engagement Mobile via le portail d’Azure. 

>[AZURE.NOTE] Les instructions ci-dessous sont basées sur ce [guide Active Directory](../resource-group-create-service-principal-portal.md) et personnalisées pour ce qui est nécessaire pour l’authentification des API d’Engagement Mobile. Par conséquent, faire si vous voulez comprendre les étapes ci-dessous en détail. 

1. Connectez-vous à votre compte Azure via le [portail classique](https://manage.windowsazure.com/).

2. Dans le volet gauche, sélectionnez **Active Directory** .

     ![Sélectionnez Active Directory][1]

3. Choisissez le **Répertoire actif par défaut** sur votre portail Azure. 

     ![choix d’un répertoire][2]

    >[AZURE.IMPORTANT] Cette approche fonctionne uniquement lorsque vous travaillez dans l’Active Directory de votre compte par défaut et ne fonctionnera pas si vous effectuez cette opération dans Active Directory que vous avez créés dans votre compte. 

4. Pour afficher les applications dans votre répertoire, cliquez sur **Applications**.

     ![afficher les applications][3]

5. Cliquez sur **Ajouter**. 

     ![Ajouter une application][4]

6. Cliquez sur **Ajouter une application du développement de mon organisation**

     ![nouvelle application][5]

6. Renseignez le nom de l’application et sélectionnez le type d’application **WEB APPLICATION et/ou WEB API** et cliquez sur le bouton suivant.

     ![application de nom][6]

7. Vous pouvez fournir des URL factices pour **Session URL** et les **URI d’ID APP**. Ils ne sont pas utilisés pour notre scénario, et les URL eux-mêmes ne sont pas validés.  

     ![propriétés de l’application][7]

8. À la fin de la collection, vous avez une application DAS avec le nom que vous avez fournies précédemment comme suit. Il s’agit de votre **AD\_APP\_nom de** et prenez note de ce dernier.  

     ![nom de l’application][8]

9. Cliquez sur le nom de l’application et cliquez sur **configurer**.

     ![configurer l’application][9]

10. Prenez note de l’ID du CLIENT qui sera utilisé en tant que **CLIENT\_ID de** correspondant à votre API appelle. 

     ![configurer l’application][10]

11. Faites défiler jusqu'à la section **clés** et ajoutez une clé avec une durée de 2 ans (expiration), de préférence et cliquez sur **Enregistrer**. 

     ![configurer l’application][11]


12. Copie immédiate de la valeur qui est affichée pour la clé, tel qu’il apparaît désormais uniquement et n’est pas stocké donc pas affichera jamais. Si vous le perdez vous devrez générer une nouvelle clé. Il s’agit de la **CLIENT_SECRET** de vos appels d’API. 

     ![configurer l’application][12]

    >[AZURE.IMPORTANT] Cette clé arrivera à expiration à la fin de la durée que vous avez spécifiée et veillez à le renouveler le moment venu sinon votre authentification API ne fonctionnera pas plus. Vous pouvez également supprimer et recréer cette clé si vous pensez qu’il a été compromis.
 
13. Cliquez sur le bouton des **Points de terminaison de vue** maintenant qui ouvrira la boîte de dialogue **Application de points de terminaison** . 

    ![][13]

14. À partir de la boîte de dialogue de terminaison d’application, copiez **Point de terminaison jeton OAUTH 2.0**. 

    ![][14]

15. Ce point de terminaison sera sous la forme suivante, où le GUID dans l’URL est votre **TENANT_ID** Notez donc de celui-ci : 

        https://login.microsoftonline.com/<GUID>/oauth2/token

16. Nous allons maintenant configurer les autorisations sur cette application. Pour cela, vous devrez ouvrir le [portail Azure](https://portal.azure.com). 

17. Cliquez sur **Groupes de ressources** et de rechercher le groupe de ressources de **Mobile Engagement** .  

    ![][15]

18. Cliquez sur le groupe de ressources de **Mobile Engagement** et accédez à la lame de **paramètres** ici. 

    ![][16]

19. Cliquez sur **les utilisateurs** dans la lame de paramètres et puis cliquez sur **Ajouter** pour ajouter un utilisateur. 

    ![][17]

20. Cliquez sur **Sélectionner un rôle**

    ![][18]

21. Cliquez sur le **propriétaire**

    ![][19]

22. Recherchez le nom de votre application **AD\_APP\_nom de** dans la zone de recherche. Vous n'allez pas voir ceci par défaut ici. Une fois que vous le trouvez, sélectionnez-le, puis cliquez sur **Sélectionner** en bas de la lame. 

    ![][20]

23. Sur la blade **D’ajout d’accès** , il apparaîtra en tant que **1 utilisateur, des groupes de 0**. Sur cette blade pour confirmer la modification, cliquez sur **OK** . 

    ![][21]

Vous avez maintenant terminé la configuration requise de DAS et vous êtes fin prêt à appeler les API. 

<!-- Images -->
[1]: ./media/mobile-engagement-api-authentication-manual/active-directory.png
[2]: ./media/mobile-engagement-api-authentication-manual/active-directory-details.png
[3]: ./media/mobile-engagement-api-authentication-manual/view-applications.png
[4]: ./media/mobile-engagement-api-authentication-manual/add-icon.png
[5]: ./media/mobile-engagement-api-authentication-manual/what-do-you-want-to-do.png
[6]: ./media/mobile-engagement-api-authentication-manual/tell-us-about-your-application.png
[7]: ./media/mobile-engagement-api-authentication-manual/app-properties.png
[8]: ./media/mobile-engagement-api-authentication-manual/aad-app.png
[9]: ./media/mobile-engagement-api-authentication-manual/configure-menu.png
[10]: ./media/mobile-engagement-api-authentication-manual/client-id.png
[11]: ./media/mobile-engagement-api-authentication-manual/client_secret.png
[12]: ./media/mobile-engagement-api-authentication-manual/keys.png
[13]: ./media/mobile-engagement-api-authentication-manual/view-endpoints.png
[14]: ./media/mobile-engagement-api-authentication-manual/app-endpoints.png
[15]: ./media/mobile-engagement-api-authentication-manual/resource-groups.png
[16]: ./media/mobile-engagement-api-authentication-manual/resource-groups-settings.png
[17]: ./media/mobile-engagement-api-authentication-manual/add-users.png
[18]: ./media/mobile-engagement-api-authentication-manual/add-role.png
[19]: ./media/mobile-engagement-api-authentication-manual/select-role.png
[20]: ./media/mobile-engagement-api-authentication-manual/add-user-select.png
[21]: ./media/mobile-engagement-api-authentication-manual/add-access-final.png



