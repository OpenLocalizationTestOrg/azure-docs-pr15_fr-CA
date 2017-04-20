## <a name="prepare-to-authenticate-azure-resource-manager-requests"></a>Préparer authentifier les demandes du Gestionnaire de ressources Azure

Vous devez vous authentifier toutes les opérations que vous effectuez sur les ressources à l’aide du [Gestionnaire de ressources Azure] [ lnk-authenticate-arm] avec Azure Active Directory (AD). Le moyen le plus simple pour configurer cela consiste à utiliser PowerShell ou CLI d’Azure.

Vous devez installer [Azure PowerShell 1.0] [ lnk-powershell-install] ou une version ultérieure avant de continuer.

Les étapes suivantes indiquent comment configurer l’authentification par mot de passe pour une application d’Active Directory à l’aide de PowerShell. Vous pouvez exécuter ces commandes dans une session PowerShell standard.

1. Connectez-vous à votre abonnement Azure à l’aide de la commande suivante :

    ```
    Login-AzureRmAccount
    ```

2. Prenez note de vos **TenantId** **SubscriptionId**. Vous en aurez besoin ultérieurement.

3. Créez une nouvelle application Azure Active Directory à l’aide de la commande suivante, en remplaçant les espaces réservés :

    - **{Nom complet} :** un nom d’affichage de votre application comme **MySampleApp**
    - **{URL de la page d’accueil} :** l’URL de la page d’accueil de votre application, par exemple **http://mysampleapp/home**. Cette URL n’a pas besoin pointer vers une application réelle.
    - **{Identificateur} :** Un identificateur unique, tel que **http://mysampleapp**. Cette URL n’a pas besoin pointer vers une application réelle.
    - **{Mot de passe} :** Un mot de passe que vous utiliserez pour authentifier avec votre application.

    ```
    New-AzureRmADApplication -DisplayName {Display name} -HomePage {Home page URL} -IdentifierUris {Application identifier} -Password {Password}
    ```
    
4. Prenez note des **ApplicationId** de l’application que vous avez créé. Vous en avez besoin plus tard.

5. Créer un nouveau service principal à l’aide de la commande suivante, en remplaçant les **{MyApplicationId}** par **ApplicationId** à partir de l’étape précédente :

    ```
    New-AzureRmADServicePrincipal -ApplicationId {MyApplicationId}
    ```
    
6. Configurer une attribution de rôle à l’aide de la commande suivante, en remplaçant les **{MyApplicationId}** par votre **ApplicationId**.

    ```
    New-AzureRmRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName {MyApplicationId}
    ```
    
Vous avez terminé de créer l’application Azure AD qui vous permettra de s’authentifier à partir de votre application C#. Vous aurez besoin des valeurs suivantes plus loin dans ce didacticiel :

- TenantId
- SubscriptionId
- ApplicationId
- Mot de passe

[lnk-authenticate-arm]: https://msdn.microsoft.com/library/azure/dn790557.aspx
[lnk-powershell-install]: ../articles/powershell-install-configure.md
