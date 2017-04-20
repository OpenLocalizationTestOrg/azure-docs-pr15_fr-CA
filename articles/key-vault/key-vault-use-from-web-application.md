<properties
    pageTitle="Utilisez Azure coffre-fort de clé à partir d’une Application Web | Microsoft Azure"
    description="Utilisez ce didacticiel pour vous aider à apprendre à utiliser un coffre-fort de clé Azure à partir d’une application web."
    services="key-vault"
    documentationCenter=""
    authors="adhurwit"
    manager="mbaldwin"
    tags="azure-resource-manager"/>

<tags
    ms.service="key-vault"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/05/2016"
    ms.author="adhurwit"/>

# <a name="use-azure-key-vault-from-a-web-application"></a>Utilisez Azure coffre-fort de clé à partir d’une Application Web #

## <a name="introduction"></a>Introduction  
Utilisez ce didacticiel pour vous aider à apprendre à utiliser un coffre-fort de clé Azure à partir d’une application web dans Azure. Il vous guide dans le processus d’accès à un secret à partir d’une chambre de clé Azure afin qu’il peut être utilisé dans votre application web.

**Les temps estimé pour terminer :** les 15 minutes


Pour plus d’informations sur le coffre-fort de clé d’Azure, consultez [Quel est le coffre-fort de clé Azure ?](key-vault-whatis.md)

## <a name="prerequisites"></a>Conditions préalables

Pour terminer ce didacticiel, vous devez disposer des éléments suivants :

- Un URI à un secret dans un coffre-fort de clé Azure
- Un ID de Client et un code Secret pour une application web Client enregistrement avec Azure Active Directory qui a accès à votre clé de coffre-fort
- Une application web. Nous présentera les étapes d’une application ASP.NET MVC déployé dans Azure sous la forme d’une application Web.

> [AZURE.NOTE]  Il est essentiel que vous avez effectué les étapes répertoriées dans la [Mise en route de la chambre forte de clé Azure](key-vault-get-started.md) pour ce didacticiel, afin que vous ayez l’URI à un secret et l’ID de Client et le Secret de Client pour une application web.

L’application web accédant à la chambre forte de clé est celui qui est enregistré dans Azure Active Directory et qui est autorisé à accéder à votre coffre-fort de clé. Si ce n’est pas le cas, revenez au Registre une Application dans le didacticiel mise en route et répétez les étapes répertoriées.

Ce didacticiel est conçu pour les développeurs web qui comprennent les principes fondamentaux de la création d’applications web sur Azure. Pour plus d’informations sur les applications Web Azure, consultez [vue d’ensemble des applications Web](../app-service-web/app-service-web-overview.md).



## <a id="packages"></a>Ajouter des Packages Nuget ##
Il existe deux packages de votre application web doit être installé.

- Bibliothèque de l’authentification Active Directory - contient des méthodes pour interagir avec Azure Active Directory et la gestion d’identité de l’utilisateur
- Bibliothèque de coffre-fort clé Azure - contient les méthodes d’interaction avec Azure clé coffre-fort


Deux de ces packages peuvent être installés à l’aide de la Console du Gestionnaire de package à l’aide de la commande Install-Package.

    // this is currently the latest stable version of ADAL
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.16.204221202

    Install-Package Microsoft.Azure.KeyVault


## <a id="webconfig"></a>Modifiez le fichier Web.Config ##
Il existe trois paramètres de l’application qui doivent être ajoutés au fichier web.config comme suit.

    <!-- ClientId and ClientSecret refer to the web application registration with Azure Active Directory -->
    <add key="ClientId" value="clientid" />
    <add key="ClientSecret" value="clientsecret" />

    <!-- SecretUri is the URI for the secret in Azure Key Vault -->
    <add key="SecretUri" value="secreturi" />


Si vous n’allez pas héberger votre application comme une application Web de Azure, vous devez ajouter les valeurs réelles de ClientId Client Secret et Secret URI dans le fichier web.config. Sinon, laissez ces valeurs factices parce que nous ajouterons des valeurs réelles dans le portail Azure pour un niveau de sécurité supplémentaire.


## <a id="gettoken"></a>Ajoutez la méthode pour obtenir le jeton d’accès ##
Pour pouvoir utiliser l’API de stockage en chambre forte de clé, vous avez besoin d’un jeton d’accès. Le Client de coffre-fort clé gère les appels à l’API de stockage en chambre forte de clé, mais vous devez lui fournir une fonction qui obtient le jeton d’accès.  

Voici le code permettant d’obtenir un accès à un jeton d’Azure Active Directory. Ce code peut trouver n’importe où dans votre application. J’aime ajouter une classe Utils ou EncryptionHelper.  

    //add these using statements
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System.Threading.Tasks;
    using System.Web.Configuration;

    //this is an optional property to hold the secret after it is retrieved
    public static string EncryptSecret { get; set; }

    //the method that will be provided to the KeyVaultClient
    public static async Task<string> GetToken(string authority, string resource, string scope)
    {
        var authContext = new AuthenticationContext(authority);
        ClientCredential clientCred = new ClientCredential(WebConfigurationManager.AppSettings["ClientId"],
                    WebConfigurationManager.AppSettings["ClientSecret"]);
        AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

        if (result == null)
            throw new InvalidOperationException("Failed to obtain the JWT token");

        return result.AccessToken;
    }

> [AZURE.NOTE] 
> À l’aide d’un ID de Client et le code Secret du Client est le meilleur moyen d’authentifier une application Azure AD. Et l’utiliser dans votre application web permet une séparation des tâches et de mieux contrôler la gestion de vos clée. Mais il ne dépend pas placer le Secret Client dans vos paramètres de configuration qui, pour certains, peuvent être aussi dangereux en plaçant le code secret que vous souhaitez protéger dans vos paramètres de configuration. Voir ci-dessous pour plus d’informations sur l’utilisation d’un ID de Client et d’un certificat au lieu de l’ID Client et Client Secret pour authentifier l’application Azure AD.



## <a id="appstart"></a>Récupérez le secret sur le début de l’Application ##
Nous devons maintenant le code pour appeler l’API de stockage en chambre forte de clé et récupérer le mot de passe. Le code suivant peut être placé n’importe où, tant qu’elle est appelée avant de l’utiliser. J’ai inséré ce code dans l’événement démarrer l’Application dans Global.asax afin qu’elle s’exécute une seule fois au démarrage et permet le secret de l’application.

    //add these using statements
    using Microsoft.Azure.KeyVault;
    using System.Web.Configuration;

    // I put my GetToken method in a Utils class. Change for wherever you placed your method.
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

    var sec = kv.GetSecretAsync(WebConfigurationManager.AppSettings["SecretUri"]).Result.Value;

    //I put a variable in a Utils class to hold the secret for general  application use.
    Utils.EncryptSecret = sec;



## <a id="portalsettings"></a>Ajouter des paramètres d’application dans Azure Portal (facultatif) ##
Si vous disposez d’une application Web de Azure, vous pouvez maintenant ajouter les valeurs réelles de la AppSettings dans le portail Azure. De cette manière, les valeurs réelles ne sera pas dans le fichier web.config, mais protégée via le portail sur lequel vous disposez des fonctionnalités de contrôle d’accès distincts. Ces valeurs seront remplacés par les valeurs que vous avez entrées dans votre fichier web.config. Assurez-vous que les noms sont identiques.

![Paramètres de l’application affichées dans Azure Portal][1]


## <a name="authenticate-with-a-certificate-instead-of-a-client-secret"></a>Authentification avec un certificat au lieu d’un Secret de Client
Il est un autre moyen d’authentifier une application Azure AD à l’aide d’un ID de Client et d’un certificat au lieu d’un ID de Client et le code Secret du Client. Voici les étapes pour utiliser un certificat dans une application Web de Azure :

1. Obtenir ou créer un certificat
2. Associez le certificat à une application Azure AD
3. Ajoutez du code à votre application Web pour utiliser le certificat
4. Ajouter un certificat à votre application Web


**Obtenir ou créer un certificat** Dans notre cas, nous fera un certificat de test. Voici quelques exemples de commandes dans une invite de commande développeur vous permet de créer un certificat. Placez-vous à l’endroit où les fichiers de certificat doit être créé.

    makecert -sv mykey.pvk -n "cn=KVWebApp" KVWebApp.cer -b 07/31/2015 -e 07/31/2016 -r
    pvk2pfx -pvk mykey.pvk -spc KVWebApp.cer -pfx KVWebApp.pfx -po test123

Prenez note de la date de fin et le mot de passe pour le .pfx (dans cet exemple : 31/07/2016 et test123). Vous en aurez besoin ci-dessous.

Pour plus d’informations sur la création d’un certificat de test, consultez [Comment : créer votre propre Test de certificat de](https://msdn.microsoft.com/library/ff699202.aspx)


**Associez le certificat avec une application Azure AD** Maintenant que vous disposez d’un certificat, vous devez l’associer à une application Azure AD. Le portail de gestion Azure charge mais pas cela maintenant. Vous devez plutôt utiliser Powershell. Voici les commandes dont vous avez besoin pour exécuter :

    $x509 = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2

    PS C:\> $x509.Import("C:\data\KVWebApp.cer")

    PS C:\> $credValue = [System.Convert]::ToBase64String($x509.GetRawCertData())

    PS C:\> $now = [System.DateTime]::Now

    # this is where the end date from the cert above is used
    PS C:\> $yearfromnow = [System.DateTime]::Parse("2016-07-31")

    PS C:\> $adapp = New-AzureRmADApplication -DisplayName "KVWebApp" -HomePage "http://kvwebapp" -IdentifierUris "http://kvwebapp" -KeyValue $credValue -KeyType "AsymmetricX509Cert" -KeyUsage "Verify" -StartDate $now -EndDate $yearfromnow

    PS C:\> $sp = New-AzureRmADServicePrincipal -ApplicationId $adapp.ApplicationId

    PS C:\> Set-AzureRmKeyVaultAccessPolicy -VaultName 'contosokv' -ServicePrincipalName $sp.ServicePrincipalName -PermissionsToSecrets all -ResourceGroupName 'contosorg'

    # get the thumbprint to use in your app settings
    PS C:\>$x509.Thumbprint

Après avoir exécuté ces commandes, vous pouvez voir l’application dans Azure AD. Si vous ne voyez pas l’application au premier, rechercher « Applications propriétaire de ma société » plutôt que « Applications ma société utilise ».

Pour plus d’informations sur les objets d’Application Azure AD et ServicePrincipal, consultez les [objets d’Application et les objets d’entité de Service](../active-directory/active-directory-application-objects.md)



**Ajout de code pour votre application Web pour utiliser le certificat** Nous allons maintenant ajouter le code à votre application Web pour accéder au certificat et l’utiliser pour l’authentification.

Tout d’abord, il est code d’accès du certificat.

    public static class CertificateHelper
    {
        public static X509Certificate2 FindCertificateByThumbprint(string findValue)
        {
            X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);
            try
            {
                store.Open(OpenFlags.ReadOnly);
                X509Certificate2Collection col = store.Certificates.Find(X509FindType.FindByThumbprint,
                    findValue, false); // Don't validate certs, since the test root isn't installed.
                if (col == null || col.Count == 0)
                    return null;
                return col[0];
            }
            finally
            {
                store.Close();
            }
        }
    }


Notez que l’emplacement StoreLocation est CurrentUser place LocalMachine. Et que nous sommes en fournissant « false » à la méthode Find, car nous utilisons un certificat de test.


Vient ensuite le code qui utilise le CertificateHelper crée un ClientAssertionCertificate qui est nécessaire pour l’authentification.

    public static ClientAssertionCertificate AssertionCert { get; set; }

    public static void GetCert()
    {
        var clientAssertionCertPfx = CertificateHelper.FindCertificateByThumbprint(WebConfigurationManager.AppSettings["thumbprint"]);
        AssertionCert = new ClientAssertionCertificate(WebConfigurationManager.AppSettings["clientid"], clientAssertionCertPfx);
    }


Voici le nouveau code pour obtenir le jeton d’accès. Cela remplace la méthode GetToken ci-dessus. J’ai donné un nom différent pour des raisons pratiques.

    public static async Task<string> GetAccessToken(string authority, string resource, string scope)
    {
        var context = new AuthenticationContext(authority, TokenCache.DefaultShared);
        var result = await context.AcquireTokenAsync(resource, AssertionCert);
        return result.AccessToken;
    }

J’ai inséré tout ce code dans la classe Utils de de mon projet application Web pour une utilisation simplifiée.

La dernière modification de code se trouve dans la méthode Application_Start. Nous devons tout d’abord appeler la méthode GetCert() pour charger le ClientAssertionCertificate. Et modifiez ensuite la méthode de rappel que vous nous fournissez lors de la création d’un nouveau KeyVaultClient. Notez que ce message remplace le code que nous avions ci-dessus.

    Utils.GetCert();
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetAccessToken));


**Ajouter un certificat à votre application Web via le portail Azure** Ajouter un certificat à votre application Web est un processus simple en deux étapes. Tout d’abord, accédez au portail Azure et accédez à votre application Web. Sur la lame de paramètres pour votre application Web, cliquez sur l’entrée « Custom domains et SSL ». Sur la lame qui s’ouvre, vous pourrez télécharger le certificat que vous avez créé ci-dessus, KVWebApp.pfx, assurez-vous que vous n’oubliez pas le mot de passe pour le pfx.

![Ajouter un certificat à une application Web sur le portail Azure][2]


La dernière chose que vous devez faire est d’ajouter un paramètre d’Application à votre application Web avec le nom du site Web de\_charge\_certificats et la valeur *. Cela garantit que tous les certificats sont chargés. Si vous souhaitez charger uniquement les certificats que vous avez téléchargé, vous pouvez entrer une liste séparée par des virgules de leurs empreintes.

Pour en savoir plus sur l’ajout d’un certificat pour une application Web, consultez [Utilisation des certificats dans les Applications de sites Web d’Azure](https://azure.microsoft.com/blog/2014/10/27/using-certificates-in-azure-websites-applications/)


**Ajouter un certificat à un stockage en chambre forte de la clé sous la forme d’un secret** Au lieu de télécharger votre certificat au service Web App directement, vous pouvez stocker dans le stockage en chambre forte de la clé sous la forme d’un code secret et le déployer à partir de là. Il s’agit d’un processus en deux étapes qui est décrite dans le billet de blog suivant, [Déploiement d’Azure Web App certificat par le biais de chambre forte de clé](https://blogs.msdn.microsoft.com/appserviceteam/2016/05/24/deploying-azure-web-app-certificate-through-key-vault/)



## <a id="next"></a>Étapes suivantes ##


Pour les références de programmation, consultez [Azure clé coffre-fort C# Client API Reference](https://msdn.microsoft.com/library/azure/dn903628.aspx).


<!--Image references-->
[1]: ./media/key-vault-use-from-web-application/PortalAppSettings.png
[2]: ./media/key-vault-use-from-web-application/PortalAddCertificate.png
