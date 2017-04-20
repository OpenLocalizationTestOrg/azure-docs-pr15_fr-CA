<properties 
    pageTitle="Authentifier avec Engagement Mobile reste API"
    description="Décrit comment authentifier avec Azure Mobile Engagement reste API" 
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
    ms.date="10/05/2016"
    ms.author="wesmc;ricksal"/>

# <a name="authenticate-with-mobile-engagement-rest-apis"></a>Authentifier avec Engagement Mobile reste API

## <a name="overview"></a>Vue d’ensemble

Ce document décrit comment obtenir un Oauth DAS valide le jeton pour authentifier avec les API de reste d’Engagement Mobile. 

Il est supposé que vous disposez d’un abonnement Azure valide et que vous avez créé une application Mobile Engagement à l’aide d’un de nos [Didacticiels de développeur](mobile-engagement-windows-store-dotnet-get-started.md).

## <a name="authentication"></a>Authentification

Un Microsoft Azure Active Directory basée OAuth jeton est utilisé pour l’authentification. 

Dans l’ordre à la demande d’authentification une API, un en-tête d’autorisation doit être ajouté à chaque demande, qui est de la forme suivante :

    Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGmJlNmV2ZWJPamg2TTNXR1E...

>[AZURE.NOTE] Les jetons de Active Directory Azure expirent dans 1 heure.

Il existe plusieurs façons d’obtenir un jeton. Dans la mesure où les API sont généralement appelées à partir d’un service en nuage, que vous souhaitez utiliser une clé d’API. Une clé d’API dans la terminologie Azure est appelée un mot de passe principal de Service. La procédure suivante décrit comment configurer manuellement.

### <a name="one-time-setup-using-script"></a>Programme d’installation unique (à l’aide du script)

Vous devez suivre l’ensemble des instructions ci-dessous pour effectuer l’installation à l’aide d’un script PowerShell qui prend l’heure minimale pour l’installation, mais utilise les valeurs par défaut plus admissibles. Si vous le souhaitez, vous pouvez également suivre les instructions fournies dans le [manuel d’installation](mobile-engagement-api-authentication-manual.md) de le faire directement à partir du portail Azure et effectuer une configuration plus fine. 

1. Obtenir la dernière version de PowerShell d’Azure à partir [d’ici](http://aka.ms/webpi-azps). Pour plus d’informations sur les instructions de téléchargement, vous pouvez voir ce [lien](../powershell-install-configure.md).  

2. Une fois, Azure PowerShell est installé, utilisez les commandes suivantes pour vous assurer que vous avez installé le **module Azure** :

    une barre d’outils. Assurez-vous que le module PowerShell de Azure est disponible dans la liste des modules disponibles. 
    
        Get-Module –ListAvailable 

    ![Modules Azure disponibles][1]
        
    b. Si vous ne trouvez pas le module PowerShell de Azure dans la liste ci-dessus, vous devez exécuter ce qui suit :
        
        Import-Module Azure 
        
3. Connexion au Gestionnaire de ressources Azure à partir de PowerShell en exécutant la commande suivante et en fournissant votre nom d’utilisateur et le mot de passe pour votre compte Azure : 
        
        Login-AzureRmAccount

4. Si vous disposez de plusieurs abonnements vous devez exécuter les opérations suivantes :

    une barre d’outils. Obtenir une liste de tous vos abonnements et copier le SubscriptionId de l’abonnement que vous souhaitez utiliser. Assurez-vous que cet abonnement est le même que celui qui a l’application d’Engagement Mobile dont vous souhaitez interagir avec l’aide des API. 

        Get-AzureRmSubscription

    b. Exécutez la commande suivante, fournissant le SubscriptionId pour configurer l’abonnement à utiliser.

        Select-AzureRmSubscription –SubscriptionId <subscriptionId>

5. Copier le texte du script [New-AzureRmServicePrincipalOwner.ps1](https://raw.githubusercontent.com/matt-gibbs/azbits/master/src/New-AzureRmServicePrincipalOwner.ps1) sur votre ordinateur local et l’enregistrer sous la forme d’une applet de commande PowerShell (par exemple, `APIAuth.ps1`) et de l’exécuter `.\APIAuth.ps1`. 
    
6. Le script vous demande de fournir une entrée **au principalName**. Fournir un nom approprié que vous souhaitez utiliser pour créer votre application de Active Directory (par exemple, APIAuth). 

7. Une fois le script terminé, il affiche les éléments suivants quatre valeurs dont vous aurez besoin de s’authentifier par programmation avec AD Assurez-vous donc de les copier. 
        
    **TenantId**, **SubscriptionId**, **ApplicationId**et **Secret**.

    Vous allez utiliser TenantId comme `{TENANT_ID}`, ApplicationId en tant que `{CLIENT_ID}` et Secret en tant que `{CLIENT_SECRET}`.

    > [AZURE.NOTE] Votre stratégie de sécurité par défaut peut-être vous empêcher d’exécuter un scripts PowerShell. Dans ce cas, vous configurez temporairement votre stratégie d’exécution pour permettre l’exécution de script à l’aide de la commande suivante :

        > Set-ExecutionPolicy RemoteSigned

8. Voici comment le jeu d’applets de commande PS ressemblerait à. 

    ![][3]

9. Dans le portail de gestion d’Azure, vérifiez qu’une nouvelle application de publicité a été créée avec le nom fourni pour le script appelé **au principalName** sous **Qu'afficher les Applications de mon entreprise possède**.

    ![][4]

#### <a name="steps-to-get-a-valid-token"></a>Étapes à suivre pour obtenir un jeton valide

1. Appeler l’API avec les paramètres suivants et veillez à remplacer le LOCATAIRE\_ID, le CLIENT\_ID et CLIENT\_SECRET :

    - **URL de la demande** en tant que *https://login.microsoftonline.com/ {LOCATAIRE\_ID} jeton/oauth2/*
    - **En-tête HTTP Content-Type** en tant *qu’application/x-www-form-urlencoded*
    - Le **Corps de la demande HTTP** en *accorder\_type = client\_informations d’identification & identifiant_client = {CLIENT\_ID} & client_secret = {CLIENT\_SECRET} & resource=https%3A%2F%2Fmanagement.core.windows.net%2F*

    Voici un exemple de demande :

        POST /{TENANT_ID}/oauth2/token HTTP/1.1
        Host: login.microsoftonline.com
        Content-Type: application/x-www-form-urlencoded
        grant_type=client_credentials&client_id={CLIENT_ID}&client_secret={CLIENT_SECRET}&reso
        urce=https%3A%2F%2Fmanagement.core.windows.net%2F

    Voici une réponse d’exemple :

        HTTP/1.1 200 OK
        Content-Type: application/json; charset=utf-8
        Content-Length: 1234
    
        {"token_type":"Bearer","expires_in":"3599","expires_on":"1445395811","not_before":"144
        5391911","resource":"https://management.core.windows.net/","access_token":{ACCESS_TOKEN}}

    Cet exemple montre comment inclure codage d’URL des paramètres POST, `resource` valeur est en fait `https://management.core.windows.net/`. Veillez à URL également coder `{CLIENT_SECRET}` qu’il peut contenir des caractères spéciaux.

    > [AZURE.NOTE] Pour les tests, vous pouvez utiliser l’outil client HTTP [Fiddler](http://www.telerik.com/fiddler) ou [extension de Chrome Postman](https://chrome.google.com/webstore/detail/postman/fhbjgbiflinjbdggehcddcbncdddomop) 

2. Maintenant dans chaque appel de l’API, vous devez inclure l’en-tête de la demande d’autorisation :

        Authorization: Bearer {ACCESS_TOKEN}

    Si vous recevez un code de 401 état renvoyé, vérifiez le corps de la réponse, il peut vous indiquer le jeton est arrivé à expiration. Dans ce cas, obtenir un nouveau jeton.

##<a name="using-the-apis"></a>À l’aide de l’API

Maintenant que vous disposez d’un jeton valide, vous êtes prêt à effectuer les appels d’API.

1. Dans chaque demande d’API, vous devez passer un jeton valide, non expiré que vous avez obtenu dans la section précédente.

2. Vous devez brancher dans certains paramètres dans la demande URI qui identifie votre application. URI de la demande est similaire au suivant

        https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/
        providers/Microsoft.MobileEngagement/appcollections/{app-collection}/apps/{app-resource-name}/

    Pour obtenir les paramètres, cliquez sur le nom de votre application et cliquez sur le tableau de bord et vous verrez une page similaire à celle-ci avec les 3 paramètres.

    - **1**`{subscription-id}`
    - **2**`{app-collection}`
    - **3**`{app-resource-name}`
    - **4** nom de votre groupe de ressources va être **MobileEngagement** , sauf si vous avez créé un nouveau. 

    ![Paramètres URI de API d’Engagement mobile][2]

>[AZURE.NOTE] <br/>
>1. Ignorer l’adresse de racine API comme cela a été pour l’API précédentes.<br/>
>2. Si vous avez créé l’application à l’aide classique Azure portal vous devez utiliser le nom de ressource de l’Application qui est différent du nom de l’Application elle-même. Si vous avez créé l’application dans Azure Portal vous devez utiliser le nom de l’application elle-même (il n’y a pas de différenciation entre le nom de ressource de l’Application et le nom de l’application pour les applications créées dans le nouveau portail).  

<!-- Images -->
[1]: ./media/mobile-engagement-api-authentication/azure-module.png
[2]: ./media/mobile-engagement-api-authentication/mobile-engagement-api-uri-params.png
[3]: ./media/mobile-engagement-api-authentication/ps-cmdlets.png
[4]: ./media/mobile-engagement-api-authentication/ad-app-creation.png



