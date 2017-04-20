## <a name="obtain-an-azure-resource-manager-token"></a>Obtenir un jeton d’Azure le Gestionnaire de ressources

Azure Active Directory doivent s’authentifier toutes les tâches que vous effectuez sur les ressources à l’aide du Gestionnaire de ressources Azure. L’exemple présenté ici utilise l’authentification de mot de passe, pour [les demandes d’authentification Azure Resource Manager]autres approches, consultez[lnk-authenticate-arm].

1. Ajoutez le code suivant à la méthode **Main** dans Program.cs afin de récupérer un jeton Azure AD à l’aide de l’id d’application et d’un mot de passe.

    ```
    var authContext = new AuthenticationContext(string.Format  
      ("https://login.windows.net/{0}", tenantId));
    var credential = new ClientCredential(applicationId, password);
    AuthenticationResult token = authContext.AcquireTokenAsync
      ("https://management.core.windows.net/", credential).Result;
    
    if (token == null)
    {
      Console.WriteLine("Failed to obtain the token");
      return;
    }
    ```

2. Créer un objet **ResourceManagementClient** qui utilise le jeton en ajoutant le code suivant à la fin de la méthode **Main** :

    ```
    var creds = new TokenCredentials(token.AccessToken);
    var client = new ResourceManagementClient(creds);
    client.SubscriptionId = subscriptionId;
    ```

3. Créer ou obtenir une référence pour le groupe de ressources que vous utilisez :

    ```
    var rgResponse = client.ResourceGroups.CreateOrUpdate(rgName,
        new ResourceGroup("East US"));
    if (rgResponse.Properties.ProvisioningState != "Succeeded")
    {
      Console.WriteLine("Problem creating resource group");
      return;
    }
    ```

[lnk-authenticate-arm]: https://msdn.microsoft.com/library/azure/dn790557.aspx