<properties 
    pageTitle="Connexion à un compte de Services de support à l’aide de .NET" 
    description="Cette rubrique montre comment se connecter à des Services de support en utilisant .NET." 
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="09/26/2016"
    ms.author="juliako"/>


# <a name="connecting-to-media-services-account-using-media-services-sdk-for-net"></a>Connexion à un compte de Services de support à l’aide de Media Services SDK pour .NET

> [AZURE.SELECTOR]
- [RESTE](media-services-rest-connect-programmatically.md)
- [.NET](media-services-dotnet-connect-programmatically.md)


Cette rubrique décrit comment obtenir une connexion par programme pour Microsoft Azure Media Services lors de la programmation avec le Kit de développement de Services de support pour .NET.


## <a name="connecting-to-media-services"></a>Connexion aux Services de support

Pour vous connecter à des Services multimédias par programme, vous devez ont déjà configuré un compte Azure, configuré Media Services sur ce compte et ensuite configurer un projet Visual Studio pour le développement avec le Kit de développement de Services de support pour .NET. Pour plus d’informations, consultez le programme d’installation pour le développement avec le Kit de développement de Services de support pour .NET.

À la fin de la procédure d’installation du compte des Services de support, vous avez obtenu les valeurs suivantes de connexion requises. Utilisez-les pour établir des connexions par programmation avec les Services de support.

- Nom de votre compte de Services de support.

- Votre clé de compte de Services de support.

Pour rechercher ces valeurs, consultez le portail de gestion Azure, sélectionnez votre compte de Service de support et cliquez sur l’icône «**Gérer les clés**» dans la partie inférieure de la fenêtre du portail. En cliquant sur l’icône en regard de chaque zone de texte de copie la valeur dans le Presse-papiers système.


## <a name="creating-a-cloudmediacontext-instance"></a>Création d’une Instance de CloudMediaContext

Pour démarrer la programmation des Services de support, vous devez créer une instance de **CloudMediaContext** qui représente le contexte de serveur. Le **CloudMediaContext** inclut des références à des collections importantes, y compris les tâches, ressources, fichiers, les stratégies d’accès et localisateurs.

>[AZURE.NOTE] La classe **CloudMediaContext** n’est pas thread-safe. Vous devez créer un nouveau CloudMediaContext par thread ou par jeu d’opérations.


CloudMediaContext a cinq surcharges de constructeur. Il est recommandé d’utiliser les constructeurs qui prennent des **MediaServicesCredentials** en tant que paramètre. Pour plus d’informations, consultez la **Réutilisation de jetons de Service contrôle accès** qui suit. 

L’exemple suivant utilise le constructeur CloudMediaContext(MediaServicesCredentials credentials) public :

    // _cachedCredentials and _context are class member variables. 
    _cachedCredentials = new MediaServicesCredentials(
                    _mediaServicesAccountName,
                    _mediaServicesAccountKey);
    
    _context = new CloudMediaContext(_cachedCredentials);


## <a name="reusing-access-control-service-tokens"></a>Réutilisation de jetons de Service de contrôle de l’accès

Cette section indique comment réutiliser des jetons du Service de contrôle d’accès à l’aide des constructeurs de CloudMediaContext qui prennent des MediaServicesCredentials en tant que paramètre.


[Contrôle d’accès Azure Active Directory](https://msdn.microsoft.com/library/hh147631.aspx) (également appelé Service de contrôle d’accès ou ACS) est un service en nuage qui offre un moyen facile de les authentifier et autoriser les utilisateurs à accéder à leurs applications web. Microsoft Azure Media Services contrôle l’accès à ses services via protocole OAuth qui requiert un jeton d’ACS. Media Services reçoit les jetons ACS à partir d’un serveur d’autorisation.

Lorsque vous développez avec le Kit de développement de Services de support, vous pouvez choisir de ne traite ne pas des jetons car le Kit de développement de code responsables de résoudre pour vous. Toutefois, le Kit de développement vous permettant de gérer complètement les jetons ACS permet les requêtes inutiles de jetons. Demande de jetons prend du temps et consomme les ressources client et le serveur. En outre, le serveur ACS limite les requêtes si le taux est trop élevé. La limite est de 30 requêtes par seconde, consultez [Limitations du Service ACS](https://msdn.microsoft.com/library/gg185909.aspx) pour plus de détails.

À partir de la version 3.0.0.0 de Media Services SDK, vous pouvez réutiliser les jetons ACS. Les constructeurs **CloudMediaContext** qui prennent les **MediaServicesCredentials** en tant que paramètre Activer partage les jetons ACS entre plusieurs contextes. La classe MediaServicesCredentials encapsule les informations d’identification de Media Services. Si un jeton ACS est disponible et son délai d’expiration est connu, vous pouvez créer une nouvelle instance de MediaServicesCredentials avec le jeton et le passer au constructeur de CloudMediaContext. Notez que les Media Services SDK actualise automatiquement les jetons dès qu’ils arrivent à expiration. Il existe deux façons de réutiliser les jetons ACS, comme illustré dans les exemples ci-dessous.

- Vous pouvez mettre en cache l’objet **MediaServicesCredentials** en mémoire (par exemple, dans une variable de classe statique). Puis, passez l’objet mis en cache au constructeur CloudMediaContext. L’objet MediaServicesCredentials contient un jeton ACS qui peut être réutilisé si elle est encore valide. Si le jeton n’est pas valide, elle sera actualisée par le Kit de développement de Services média en utilisant les informations d’identification fournies au constructeur MediaServicesCredentials.

    Notez que l’objet **MediaServicesCredentials** un jeton valide après l’appel de la RefreshToken. Le **CloudMediaContext** appelle la méthode **RefreshToken** dans le constructeur. Si vous envisagez d’enregistrer les valeurs de jeton pour un stockage externe, veillez à vérifier si la valeur de TokenExpiration est valide avant d’enregistrer les données de jetons. S’il n’est pas valide, appelez RefreshToken avant la mise en cache.

        // Create and cache the Media Services credentials in a static class variable.
        _cachedCredentials = new MediaServicesCredentials(_mediaServicesAccountName, _mediaServicesAccountKey);

        
        // Use the cached credentials to create a new CloudMediaContext object.
        if(_cachedCredentials == null)
        {
            _cachedCredentials = new MediaServicesCredentials(_mediaServicesAccountName, _mediaServicesAccountKey);
        }
        
        CloudMediaContext context = new CloudMediaContext(_cachedCredentials);

- Vous pouvez également mettre en cache la chaîne AccessToken et les valeurs de TokenExpiration. Les valeurs plus tard peuvent servir à créer un objet MediaServicesCredentials avec les données mises en cache de jetons.  Ceci est particulièrement utile pour les scénarios où le jeton peut être partagé en toute sécurité entre plusieurs processus ou des ordinateurs.

    Les extraits de code suivants appellent les méthodes SaveTokenDataToExternalStorage, GetTokenDataFromExternalStorage et UpdateTokenDataInExternalStorageIfNeeded qui ne sont pas définies dans cet exemple. Vous pouvez définir ces méthodes pour stocker, récupérer et mettre à jour des données de jetons dans un système de stockage externe. 

        CloudMediaContext context1 = new CloudMediaContext(_mediaServicesAccountName, _mediaServicesAccountKey);
        
        // Get token values from the context.
        var accessToken = context1.Credentials.AccessToken;
        var tokenExpiration = context1.Credentials.TokenExpiration;
        
        // Save token values for later use. 
        // The SaveTokenDataToExternalStorage method should check 
        // whether the TokenExpiration value is valid before saving the token data. 
        // If it is not valid, call MediaServicesCredentials’s RefreshToken before caching.
        SaveTokenDataToExternalStorage(accessToken, tokenExpiration);
        
    Utilisez les valeurs de jetons enregistrées pour créer des MediaServicesCredentials.


        var accessToken = "";
        var tokenExpiration = DateTime.UtcNow;
        
        // Retrieve saved token values.
        GetTokenDataFromExternalStorage(out accessToken, out tokenExpiration);
        
        // Create a new MediaServicesCredentials object using saved token values.
        MediaServicesCredentials credentials = new MediaServicesCredentials(_mediaServicesAccountName, _mediaServicesAccountKey)
        {
            AccessToken = accessToken,
            TokenExpiration = tokenExpiration
        };
        
        CloudMediaContext context2 = new CloudMediaContext(credentials);

    Mettre à jour la copie du jeton dans le cas où le jeton a été mis à jour par le Kit de développement de Services de support. 
    
        if(tokenExpiration != context2.Credentials.TokenExpiration)
        {
            UpdateTokenDataInExternalStorageIfNeeded(accessToken, context2.Credentials.TokenExpiration);
        }
        

- Si vous avez plusieurs comptes de Services de support (par exemple, pour le partage de charge à des fins ou géo-distribution) vous pouvez mettre en cache les objets de MediaServicesCredentials à l’aide de la collection System.Collections.Concurrent.ConcurrentDictionary (la collection ConcurrentDictionary représente une collection thread-safe des paires clé/valeur qui peuvent être accessibles simultanément par plusieurs threads). Vous pouvez ensuite utiliser la méthode GetOrAdd pour obtenir les informations d’identification mises en cache. 

        // Declare a static class variable of the ConcurrentDictionary type in which the Media Services credentials will be cached.  
        private static readonly ConcurrentDictionary<string, MediaServicesCredentials> mediaServicesCredentialsCache = 
            new ConcurrentDictionary<string, MediaServicesCredentials>();
        

        // Cache (or get already cached) Media Services credentials. Use these credentials to create a new CloudMediaContext object.
        static public CloudMediaContext CreateMediaServicesContext(string accountName, string accountKey)
        {
            CloudMediaContext cloudMediaContext;
            MediaServicesCredentials mediaServicesCredentials;
        
            mediaServicesCredentials = mediaServicesCredentialsCache.GetOrAdd(
                accountName,
                valueFactory => new MediaServicesCredentials(accountName, accountKey));
        
            cloudMediaContext = new CloudMediaContext(mediaServicesCredentials);
        
            return cloudMediaContext;
        }
        
## <a name="connecting-to-a-media-services-account-located-in-the-north-china-region"></a>Connexion à un compte de Services de support que qui se trouve dans la région en Chine du Nord

Si votre compte se trouve dans la région en Chine du Nord, utilisez le constructeur suivant :

    public CloudMediaContext(Uri apiServer, string accountName, string accountKey, string scope, string acsBaseAddress)

Par exemple :


    _context = new CloudMediaContext(
        new Uri("https://wamsbjbclus001rest-hs.chinacloudapp.cn/API/"),
        _mediaServicesAccountName,
        _mediaServicesAccountKey,
        "urn:WindowsAzureMediaServices",
        "https://wamsprodglobal001acs.accesscontrol.chinacloudapi.cn");


## <a name="storing-connection-values-in-configuration"></a>Stockage des valeurs de connexion de Configuration

Il est fortement recommandé pour stocker des valeurs de connexion, en particulier les valeurs sensibles telles que votre nom de compte et le mot de passe, dans la configuration. En outre, il est recommandé pour chiffrer les données de configuration sensibles. Vous pouvez crypter le fichier de configuration en utilisant le système de fichiers EFS Windows (EFS). Pour activer le système EFS sur un fichier, cliquez droit sur le fichier, sélectionnez **Propriétés**et activer le chiffrement dans l’onglet Paramètres **avancés** . Ou vous pouvez créer une solution personnalisée pour le cryptage des parties sélectionnées d’un fichier de configuration en utilisant la configuration protégée. Voir [cryptage Configuration Information Using Protected Configuration](https://msdn.microsoft.com/library/53tyfkaw.aspx).

Le fichier App.config suivant contient les valeurs de connexion requises. Les valeurs dans le <appSettings> élément sont les valeurs requises que vous avez obtenu à partir de la procédure d’installation du compte des Services de support.

    <configuration>
      <appSettings>
        <add key="MediaServicesAccountName" value="Media-Services-Account-Name" />
        <add key="MediaServicesAccountKey" value="Media-Services-Account-Key" />
      </appSettings>
    </configuration>


Pour récupérer les valeurs de connexion de configuration, vous pouvez utiliser la classe **ConfigurationManager** et puis assigner les valeurs aux champs dans votre code :
    
    private static readonly string _accountName = ConfigurationManager.AppSettings["MediaServicesAccountName"];
    private static readonly string _accountKey = ConfigurationManager.AppSettings["MediaServicesAccountKey"];



##<a name="media-services-learning-paths"></a>Cursus Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
