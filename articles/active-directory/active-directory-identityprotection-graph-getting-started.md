<properties
    pageTitle="Mise en route avec Protection d’identité Azure Active Directory et Microsoft Graph | Microsoft Azure"
    description="Fournit une introduction à Microsoft Graph de requête pour une liste des événements de risque et les informations associées dans Azure Active Directory."
    services="active-directory"
    keywords="protection d’identité Azure ActiveDirectory, événement à risque, vulnérabilité, stratégie de sécurité, Microsoft Graph"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/22/2016"
    ms.author="markvi"/>

# <a name="get-started-with-azure-active-directory-identity-protection-and-microsoft-graph"></a>Mise en route avec Protection d’identité Azure Active Directory et de Microsoft Graph

Microsoft Graph est le point de terminaison de API unifiée de Microsoft et de la page d’accueil de [Azure Active Directory Protection d’identité](active-directory-identityprotection.md) d’API. Notre première API, **identityRiskEvents**, permet de requête Microsoft Graph pour obtenir la liste des [événements de risque](active-directory-identityprotection-risk-events-types.md) et les informations associées. Cet article vous permet de vous familiariser d’interrogation de cette API. Pour des informations détaillées introduction, une documentation complète et l’accès à l’Explorateur de graphique, consultez le [site de Microsoft Graph](https://graph.microsoft.io/).

Il existe trois étapes pour accéder aux données de la Protection d’identité via Microsoft Graph :

1. Ajouter une application avec un mot de passe du client. 

2. Utilisez ce mot de passe et de quelques autres éléments d’information pour s’authentifier auprès de Microsoft Graph, dans lequel vous recevez un jeton d’authentification. 

3. Ce jeton permet de faire des demandes pour le point de terminaison API et d’obtenir des données de Protection de l’identité.

Avant de commencer, vous devez :

- Privilèges d’administrateur pour créer l’application dans Azure AD
- Le nom de domaine de vos clients (par exemple, contoso.onmicrosoft.com)



## <a name="add-an-application-with-a-client-secret"></a>Ajouter une application avec un mot de passe client


1. [Vous connecter](https://manage.windowsazure.com) à votre portail Azure classique en tant qu’administrateur. 

1. Dans le volet de navigation de gauche, cliquez sur **Active Directory**. 

    ![Création d’une application](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_01.png)

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Dans le menu du haut, cliquez sur **Applications**.

    ![Création d’une application](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_02.png)

4. Cliquez sur **Ajouter** au bas de la page.

    ![Création d’une application](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_03.png)

5. Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à mon organisation développe**.

    ![Création d’une application](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_04.png)


5. Dans la boîte de dialogue **nous dire sur votre application** , effectuez les opérations suivantes :

    ![Création d’une application](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_05.png)

    une barre d’outils. Dans la zone de texte **nom** , tapez un nom pour votre application (par exemple : Application de API d’événements risque AADIP).

    b. En tant que **Type**, sélectionnez **Web API et/Web ou Application**.

    c. Cliquez sur **suivant**.


5. Dans la boîte de dialogue **Propriétés de l’application** , effectuez les opérations suivantes :

    ![Création d’une application](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_06.png)

    une barre d’outils. Dans la zone de texte **URL de session** , tapez `http://localhost`.

    b. Dans la zone de texte **URI d’ID App** , tapez `http://localhost`.

    c. Cliquez sur **terminé**.


Maintenant, vous pouvez configurer votre application.

![Création d’une application](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_07.png)



## <a name="grant-your-application-permission-to-use-the-api"></a>Accorder l’autorisation de votre application d’utiliser l’API


1. Sur la page de votre application, dans le menu de la partie supérieure, cliquez sur **configurer**. 

    ![Création d’une application](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_08.png)

2. Dans la section **autorisations à d’autres applications** , cliquez sur **Ajouter application**.

    ![Création d’une application](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_09.png)


2. Dans la boîte de dialogue **autorisations à d’autres applications** , effectuez les opérations suivantes :

    ![Création d’une application](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_10.png)

    une barre d’outils. Sélectionnez **Microsoft Graph**.

    b. Cliquez sur **terminé**.


1. Cliquez sur **les autorisations d’Application : 0**, puis sélectionnez **lire toutes les informations d’événement de risque identité**.

    ![Création d’une application](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_11.png)

1. Cliquez sur **Enregistrer** en bas de la page.

    ![Création d’une application](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_12.png)


## <a name="get-an-access-key"></a>Obtenir une clé d’accès

1. Sur la page de votre application, dans la section **touches** , sélectionnez 1 an sous forme de durée.

    ![Création d’une application](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_13.png)

1. Cliquez sur **Enregistrer** en bas de la page.

    ![Création d’une application](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_12.png)

1. dans la section touches, copiez la valeur de la clé qui vient d’être créée et puis le coller dans un emplacement sûr.

    ![Création d’une application](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_14.png)

    > [AZURE.NOTE] Si vous perdez cette clé, vous devrez revenir à cette section et créer une nouvelle clé. Conservez cette clé un secret : toute personne qui l’a peut accéder à vos données.


1. Dans la section **Propriétés** , copiez l' **ID de Client**et puis le coller dans un emplacement sûr. 


## <a name="authenticate-to-microsoft-graph-and-query-the-identity-risk-events-api"></a>Authentification Microsoft Graph et interroger l’API d’événements de risque identité

À ce stade, vous devriez :

- L’ID de client que vous avez copié ci-dessus

- La clé que vous avez copié ci-dessus

- Le nom de domaine de vos clients


Pour l’authentification, envoyez une demande post à `https://login.microsoft.com` avec les paramètres suivants dans le corps :

- grant_type : «**client_credentials**»

- ressource : «**https://graph.microsoft.com**»

- identifiant_client :<your client ID>

- client_secret :<your key>


> [AZURE.NOTE] Vous devez fournir des valeurs pour **identifiant_client** et le paramètre **client_secret** .

En cas de réussite, retourne un jeton d’authentification.  
Pour appeler l’API, créez un en-tête avec le paramètre suivant :

    `Authorization`=”<token_type> <access_token>"


Lors de l’authentification, vous pouvez trouver le type de jeton et un jeton d’accès dans le jeton retourné.

Envoyer cet en-tête sous la forme d’une demande vers l’URL d’API suivantes :`https://graph.microsoft.com/beta/identityRiskEvents`

La réponse, en cas de réussite, est une collection d’événements de risque d’identité et les données associées au format JSON d’OData, qui peut être analysé et géré en fonction de vos besoins.

Voici un exemple de code pour l’authentification et l’appel de l’API à l’aide de Powershell.  
Il suffit d’ajouter votre ID client, clé et client du domaine.

    $ClientID       = "<your client ID here>"        # Should be a ~36 hex character string; insert your info here
    $ClientSecret   = "<your client secret here>"    # Should be a ~44 character string; insert your info here
    $tenantdomain   = "<your tenant domain here>"    # For example, contoso.onmicrosoft.com

    $loginURL       = "https://login.microsoft.com"
    $resource       = "https://graph.microsoft.com"

    $body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
    $oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

    Write-Output $oauth

    if ($oauth.access_token -ne $null) {
        $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

        $url = "https://graph.microsoft.com/beta/identityRiskEvents"
        Write-Output $url

        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)

        foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
            Write-Output $event
        }

    } else {
        Write-Host "ERROR: No Access Token"
    } 


## <a name="next-steps"></a>Étapes suivantes

Félicitations, vous venez de créer votre premier appel dans Microsoft Graph.  
Maintenant, vous pouvez interroger les événements de risque d’identité et utiliser les données de façon la plus appropriée.

Pour en savoir plus sur Microsoft Graph et comment créer des applications à l’aide de l’API de graphique, consultez la [documentation](https://graph.microsoft.io/docs) et beaucoup plus sur le [site de Microsoft Graph](https://graph.microsoft.io/). Assurez-vous également de mettre en signet [Azure AD identité Protection API](https://graph.microsoft.io/docs/api-reference/beta/resources/identityprotection_root) qui répertorie toutes les API de Protection d’identité disponibles dans le graphique. Comme nous ajoutons de nouvelles méthodes de travail avec Protection d’identité via l’API, vous verrez les sur cette page.


## <a name="additional-resources"></a>Ressources supplémentaires

- [Protection d’identité Azure Active Directory](active-directory-identityprotection.md)

- [Types d’événements risque détectés par la Protection d’identité Azure Active Directory](active-directory-identityprotection-risk-events-types.md)

- [Microsoft Graph](https://graph.microsoft.io/)

- [Vue d’ensemble de Microsoft Graph](https://graph.microsoft.io/docs)

- [Racine du Service Protection des identités AD Azure](https://graph.microsoft.io/docs/api-reference/beta/resources/identityprotection_root)
