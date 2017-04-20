<properties
    pageTitle="Pour activer la mise en service automatique des utilisateurs et des groupes d’Azure Active Directory pour les applications à l’aide de SCIM | Microsoft Azure"
    description="Azure Active Directory peut configurer automatiquement les utilisateurs et les groupes à n’importe quel magasin d’application ou à l’identité qui est fronted par un service Web avec l’interface définie dans la spécification du protocole SCIM"
    services="active-directory"
    documentationCenter=""
    authors="asmalser-msft"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/09/2016"
    ms.author="asmalser-msft"/>

#<a name="using-scim-to-enable-automatic-provisioning-of-users-and-groups-from-azure-active-directory-to-applications"></a>Pour activer la mise en service automatique des utilisateurs et des groupes d’Azure Active Directory pour les applications à l’aide de SCIM

##<a name="overview"></a>Vue d’ensemble

Azure Active Directory peut automatiquement configurer les utilisateurs et les groupes à n’importe quel magasin d’application ou à l’identité qui est fronted par un service Web avec l’interface définie dans la [spécification du protocole SCIM 2.0](https://tools.ietf.org/html/draft-ietf-scim-api-19). Azure Active Directory peut envoyer des demandes pour créer, modifier et supprimer des utilisateurs et des groupes à ce service Web, ce qui peut traduire ces demandes en opérations sur le stockage d’identité cible. 

![][1]
*Figure : Mise en service d’Azure Active Directory pour une banque d’identités via un service Web*

Cette fonctionnalité peut servir en association avec la fonction «[mettre votre application](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx)» dans Azure AD pour activer l’ouverture de session unique et automatique de l’utilisateur pour les applications qui fournissent ou sont fronted par un service web SCIM.

Il existe deux cas d’utilisation de SCIM dans Azure Active Directory :

* **Mise en service des utilisateurs et des groupes pour les applications qui prennent en charge SCIM** - Applications qui prennent en charge SCIM 2.0 et utiliser les jetons de support OAuth pour l’authentification fonctionne avec Azure AD de la boîte.

* **Créer votre propre solution de déploiement pour les applications qui prennent en charge les autres API de provisionnement** - pour les applications non-SCIM, vous pouvez créer un point de terminaison SCIM traduire entre Azure annonce SCIM le point de terminaison et tout ce qui prend en charge les API de l’application pour l’approvisionnement de l’utilisateur.  Pour vous aider dans le développement d’un point de terminaison SCIM, nous fournissons des bibliothèques CLI ainsi que des exemples de code qui vous montrent comment convertir les messages SCIM et fournir un point de terminaison SCIM.  

##<a name="provisioning-users-and-groups-to-applications-that-support-scim"></a>Mise en service des utilisateurs et des groupes d’Applications prenant en charge SCIM

Azure Active Directory peut être configuré pour automatiquement les utilisateurs de prestation attribuée et les groupes d’applications qui implémentent un site Web de [système de gestion des identités entre domaines 2 (SCIM)](https://tools.ietf.org/html/draft-ietf-scim-api-19) et acceptent des jetons de support OAuth pour l’authentification. Au sein de la spécification SCIM 2.0, applications doivent remplir ces conditions :

* Prend en charge la création d’utilisateurs et/ou groupes, conformément au point 3.3 du protocole SCIM.  

* Prend en charge la modification des utilisateurs et/ou des groupes de demandes de correctif suivant le point 3.5.2 du protocole SCIM.  

* Prend en charge la récupération d’une ressource connue conformément au point 3.4.1 du protocole SCIM.  

*  Prend en charge l’interrogation des utilisateurs et/ou groupes, suivant le point 3.4.2 du protocole SCIM.  Par défaut, les utilisateurs sont interrogés par externalId et groupes sont interrogés par displayName.  

* Prend en charge l’interrogation utilisateur par ID, par le gestionnaire suivant le point 3.4.2 du protocole SCIM.  

* Prend en charge l’interrogation des groupes par ID, par le membre suivant le point 3.4.2 du protocole SCIM.  

* Accepte les jetons OAuth porteur d’autorisation conformément à la section 2.1 du protocole SCIM.

Vous devez vérifier avec votre fournisseur d’application ou de documentation de votre fournisseur d’application pour les instructions de compatibilité à ces exigences.
 
###<a name="getting-started"></a>Mise en route

Les applications qui prennent en charge le profil SCIM décrit ci-dessus peuvent être connectées à Azure Active Directory à l’aide de la fonctionnalité de l’application « personnalisé » dans la galerie des applications Azure AD. Une fois connecté, AD Azure exécute toutes les 5 minutes où il interroge le point de terminaison de l’application SCIM pour les utilisateurs et les groupes et crée ou modifie les selon les détails de l’affectation à un processus de synchronisation.

**Pour vous connecter à une application qui prend en charge SCIM :**

1.  Dans un navigateur web, lance le portail de gestion Azure à https://manage.windowsazure.com.
2.  Accédez à **Active Directory > Directory > [votre répertoire] > Applications**, puis sélectionnez **Ajouter > Ajouter une application à partir de la galerie de**.
3.  Sélectionnez l’onglet **personnalisé** sur la gauche, entrez un nom pour votre application et cliquez sur l’icône de coche pour créer un objet d’application.

![][2]

4.  Dans l’écran, sélectionnez le deuxième bouton **configurer la mise en service de compte** .
5.  Dans le champ **URL de point de terminaison de mise en service** , entrez l’URL du point de terminaison de l’application SCIM.
6.  Si le point de terminaison SCIM requiert un jeton de support OAuth à partir d’un émetteur de publicité Azure, puis copiez le jeton de support OAuth requis dans le champ **Jeton d’authentification (facultatif)** . Est ce champ est laissé vide, puis AD Azure inclut un jeton de support OAuth émis à partir d’AD Azure avec chaque demande. Les applications qui utilisent Active Directory Azure comme un fournisseur d’idenity pouvez valider cette annonce Azure-émis le jeton.
7.  Cliquez sur **suivant**, puis cliquez sur le bouton **Démarrer le Test** pour que Azure Active Directory tente de se connecter au point de terminaison SCIM. Si les tentatives échouent, des informations de diagnostic seront affichera.  
8.  Si les tentatives de connexion à réussir de l’application, puis cliquez sur **suivant** dans les autres écrans et cliquez sur **Terminer** pour quitter la boîte de dialogue.
9.  Dans l’écran, sélectionnez le troisième bouton **Assigner des comptes** . Dans la section utilisateurs et groupes qui en résulte, affecter les utilisateurs ou les groupes souhaités à la disposition de l’application.
10. Une fois les utilisateurs et les groupes affectés, cliquez sur l’onglet **configurer** dans la partie supérieure de l’écran.
11. Sous **Mise en service de compte**, vérifiez que l’état est activé. 
12. Sous **Outils**, cliquez sur **redémarrer le service de compte** pour démarrer le processus de déploiement.

Notez que 5 à 10 minutes peuvent s’écouler avant que le processus de déploiement commence à envoyer des demandes au point de terminaison SCIM.  Un résumé des tentatives de connexion est fourni sur un onglet de tableau de bord de l’application, et à la fois un rapport des activités d’approvisionnement, ainsi que les erreurs de mise en service peuvent être téléchargés à partir de l’onglet Rapports du répertoire.

##<a name="building-your-own-provisioning-solution-for-any-application"></a>Création de votre propre configuration de Solution pour n’importe quelle Application

En créant un service web SCIM qui sert d’interface avec Azure Active Directory, vous pouvez activer l’unique utilisateur d’ouverture de session automatique et de provisionnement pour pratiquement n’importe quelle application qui permet à un utilisateur de repos ou SOAP API de mise en service.

Voici comment il fonctionne :

1.  Annonce Azure fournit une bibliothèque d’infrastructure langage commun nommée [Microsoft.SystemForCrossDomainIdentityManagement](https://www.nuget.org/packages/Microsoft.SystemForCrossDomainIdentityManagement/). Les développeurs et les intégrateurs de systèmes peuvent utiliser cette bibliothèque pour créer et déployer un site web SCIM point de terminaison capable de se connecter Azure AD pour le stockage d’identité d’une application.
2.  Les mappages sont implémentées dans le service web pour mapper le schéma utilisateur standard pour le protocole requis par l’application et du schéma utilisateur.
3.  L’URL du point de terminaison est enregistré dans Azure AD dans le cadre d’une application personnalisée dans la bibliothèque de l’application.
4.  Les utilisateurs et les groupes sont affectés à cette application dans Azure AD. Lors de l’affectation, ils sont placés dans une file d’attente pour être synchronisé à l’application cible. Gestion de la file d’attente du processus de synchronisation s’exécute toutes les 5 minutes.

###<a name="code-samples"></a>Exemples de code

Pour faciliter ce processus, un ensemble [d’exemples de code](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master) sont fournis qui illustrent la mise en service automatique et de créer un point de terminaison du service web SCIM. Un échantillon est un fournisseur qui gère un fichier avec des lignes de valeurs séparées par des virgules représentant des utilisateurs et des groupes.  L’autre est d’un fournisseur qui s’exécute sur le service de gestion d’accès et d’identité de Amazon Web Services.  

**Conditions préalables**

* Visual Studio 2013 ou version ultérieure
* [Azure SDK pour .NET](https://azure.microsoft.com/downloads/)
* Ordinateur Windows qui prend en charge l’infrastructure ASP.NET 4.5 à utiliser comme point de terminaison SCIM. Cet ordinateur doit être accessible à partir du cloud
* [Un abonnement Azure avec une version d’évaluation ou sous licence de Azure AD Premium](https://azure.microsoft.com/services/active-directory/)
* L’exemple de Amazon AWS requiert les bibliothèques d' [AWS les Shared Computer Toolkit pour Visual Studio](http://docs.aws.amazon.com/AWSToolkitVS/latest/UserGuide/tkv_setup.html). Consultez le fichier Lisezmoi inclus avec l’exemple pour plus de détails

###<a name="getting-started"></a>Mise en route

La façon la plus simple pour implémenter un point de terminaison SCIM qui peut accepter des demandes de configuration d’Active Directory Azure doit générer et déployer l’exemple de code qui renvoie les utilisateurs mis en service dans un fichier de valeurs séparées par des virgules (CSV).

**Pour créer un point de terminaison SCIM exemple :**

1.  Télécharger le package d’exemple de code à [https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master)
2.  Décompressez le package et placez-le sur votre ordinateur Windows à un emplacement tel que C:\AzureAD-BYOA-Provisioning-Samples\.
3.  Dans ce dossier, lancement de la solution FileProvisioningAgent dans Visual Studio.
4.  Sélectionnez **Outils > Gestionnaire de package de bibliothèque > Console de Gestionnaire de package**et exécutez les commandes ci-dessous pour le projet FileProvisioningAgent résoudre les références de la solution :

    Package d’installation Microsoft.SystemForCrossDomainIdentityManagement Installer-Package Package d’installation Microsoft.IdentityModel.Clients.ActiveDirectory Microsoft.Owin.Diagnostics Install-Package Microsoft.Owin.Host.SystemWeb

5.  Générez le projet FileProvisioningAgent.
6.  Lancer l’application de l’invite de commandes de Windows (en tant qu’administrateur) et utilisez la commande **cd** pour changer le répertoire dans le dossier **\AzureAD-BYOA-Provisioning-Samples\ProvisioningAgent\bin\Debug** .
7.  Exécutez la commande ci-dessous, en remplaçant < adresse ip > avec le nom de domaine de l’ordinateur Windows.

    FileAgnt.exe http://<ip-address>:9000 TargetFile.csv

8.  Dans Windows, sous **Paramètres Windows > Paramètres Internet et réseau**, sélectionnez le **pare-feu Windows > Paramètres avancés**et créez une **Règle de trafic entrant** qui permet l’accès entrant au port 9000.
9.  Si l’ordinateur Windows se trouve derrière un routeur, le routeur devez être configuré pour effectuer la traduction de l’accès réseau entre son port 9000 qui est exposé à internet et le port 9000 sur l’ordinateur Windows. Ceci est nécessaire pour AD Azure pouvoir accéder à ce point de terminaison dans le nuage.


**Pour enregistrer le point de terminaison SCIM exemple dans Azure AD :**

1.  Dans un navigateur web, lance le portail de gestion Azure à https://manage.windowsazure.com.
2.  Accédez à **Active Directory > Directory > [votre répertoire] > Applications**, puis sélectionnez **Ajouter > Ajouter une application à partir de la galerie de**.
3.  Sélectionnez l’onglet **personnalisé** sur la gauche, entrez un nom tel que « Application de Test de SCIM » et cliquez sur l’icône de coche pour créer un objet d’application. Notez que la création de l’objet application est l’intention pour représenter l’application cible, vous être provisionnement et mise en œuvre de l’authentification unique pour et pas seulement le point de terminaison SCIM.

![][2]

4.  Dans l’écran, sélectionnez le deuxième bouton **configurer la mise en service de compte** .
5.  Dans la boîte de dialogue, entrez l’URL et le port de votre point de terminaison SCIM exposées à internet. Ce serait quelque chose comme http://testmachine.contoso.com:9000 ou http://<ip-address>:9000/, où < adresse ip > est internet exposés IP adresse.  
6.  Cliquez sur **suivant**, puis cliquez sur le bouton **Démarrer le Test** pour que Azure Active Directory tente de se connecter au point de terminaison SCIM. Si les tentatives échouent, des informations de diagnostic seront affichera.  
7.  Si les tentatives de connexion à votre service Web échoue, cliquez sur **suivant** à l’écran, puis cliquez sur **Terminer** pour quitter la boîte de dialogue.
8.  Dans l’écran, sélectionnez le troisième bouton **Assigner des comptes** . Dans la section utilisateurs et groupes qui en résulte, affecter les utilisateurs ou les groupes souhaités à la disposition de l’application.
9.  Une fois les utilisateurs et les groupes affectés, cliquez sur l’onglet **configurer** dans la partie supérieure de l’écran.
10. Sous **Mise en service de compte**, vérifiez que l’état est activé. 
11. Sous **Outils**, cliquez sur **redémarrer le service de compte** pour démarrer le processus de déploiement.

Notez que 5 à 10 minutes peuvent s’écouler avant que le processus de déploiement commence à envoyer des demandes au point de terminaison SCIM.  Un résumé des tentatives de connexion est fourni sur un onglet de tableau de bord de l’application, et à la fois un rapport des activités d’approvisionnement, ainsi que les erreurs de mise en service peuvent être téléchargés à partir de l’onglet Rapports du répertoire.

La dernière étape de vérification de l’échantillon est d’ouvrir le fichier TargetFile.csv dans le dossier \AzureAD-BYOA-Provisioning-Samples\ProvisioningAgent\bin\Debug sur votre ordinateur Windows. Une fois le processus de déploiement est exécuté, ce fichier montre les détails de tous affectés et mise en service des utilisateurs et des groupes.

###<a name="development-libraries"></a>Bibliothèques de développement

Pour développer votre propre service Web conforme à la spécification SCIM, tout d’abord vous familiariser avec les bibliothèques suivantes fournies par Microsoft pour aider à accélérer le processus de développement : 

**1:**  Bibliothèques d’Infrastructure du langage communs sont proposés pour une utilisation avec les langages basés sur cette infrastructure, tels que C#.  Une de ces bibliothèques, [Microsoft.SystemForCrossDomainIdentityManagement.Service](https://www.nuget.org/packages/Microsoft.SystemForCrossDomainIdentityManagement/), déclare une interface, Microsoft.SystemForCrossDomainIdentityManagement.IProvider, illustrée dans la figure ci-dessous.  Un développeur qui utilise les bibliothèques implémente cette interface avec une classe qui peut être désignée, générique, en tant que fournisseur.  Les bibliothèques permettent au développeur de déployer facilement un service Web qui est conforme à la spécification SCIM, hébergé dans les services Internet (IIS), ou n’importe quel assembly exécutable de l’Infrastructure du langage commun.  Demandes à ce service Web doivent être convertis en appels aux méthodes du fournisseur, qui devrait être programmés par le développeur pour fonctionner sur une banque d’identités.    

![][3]

**2:** [Gestionnaires d’itinéraire de Express](http://expressjs.com/guide/routing.html) sont disponibles pour l’analyse des objets de requête node.js représentant des appels (tel que défini par la spécification de SCIM), faite à un service Web de node.js.     

###<a name="building-a-custom-scim-endpoint"></a>Création d’un point de terminaison personnalisé SCIM

En utilisant les bibliothèques décrits ci-dessus, les développeurs qui utilisent ces bibliothèques peuvent héberger leurs services dans n’importe quel assembly exécutable de l’Infrastructure du langage commun, ou Internet Information Services.  Voici un exemple de code pour l’hébergement d’un service dans un assembly exécutable, à l’adresse http://localhost : 9000 : 

    private static void Main(string[] arguments)
    {
    // Microsoft.SystemForCrossDomainIdentityManagement.IMonitor, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IProvider and 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service are all defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service.dll.  
    
    Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor = 
      new DevelopersMonitor();
    Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider = 
      new DevelopersProvider(arguments[1]);
    Microsoft.SystemForCrossDomainIdentityManagement.Service webService = null;
    try
    {
        webService = new WebService(monitor, provider);
        webService.Start("http://localhost:9000");

        Console.ReadKey(true);
    }
    finally
    {
        if (webService != null)
        {
            webService.Dispose();
            webService = null;
        }
    }
    }

    public class WebService : Microsoft.SystemForCrossDomainIdentityManagement.Service
    {
    private Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor;
    private Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider;

    public WebService(
      Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitoringBehavior, 
      Microsoft.SystemForCrossDomainIdentityManagement.IProvider providerBehavior)
    {
        this.monitor = monitoringBehavior;
        this.provider = providerBehavior;
    }

    public override IMonitor MonitoringBehavior
    {
        get
        {
            return this.monitor;
        }

        set
        {
            this.monitor = value;
        }
    }

    public override IProvider ProviderBehavior
    {
        get
        {
            return this.provider;
        }

        set
        {
            this.provider = value;
        }
    }
    }

Il est important de noter que ce service doit avoir un HTTP adresse et serveur de certificat d’authentification dont l’autorité de certification racine est une des opérations suivantes : 

* CNNIC
* Comodo
* CyberTrust
* DigiCert
* GeoTrust
* GlobalSign
* Passez de Papa
* VeriSign
* WoSign

Un certificat d’authentification de serveur peut être lié à un port sur un hôte Windows à l’aide de l’utilitaire réseau du shell, comme suit : 

    netsh http add sslcert ipport=0.0.0.0:443 certhash=0000000000003ed9cd0c315bbb6dc1c08da5e6 appid={00112233-4455-6677-8899-AABBCCDDEEFF}  
 
La valeur fournie pour l’argument certhash est ici l’empreinte numérique du certificat, alors que la valeur fournie pour l’argument appid est un identificateur global unique arbitraire.  

Pour héberger le service dans les services Internet (IIS), un développeur peut générer un assembly de bibliothèque de code Common Language Infrastructure avec une classe nommée démarrage dans l’espace de noms par défaut de l’assembly.  Voici un exemple d’une telle classe : 

    public class Startup
    {
    // Microsoft.SystemForCrossDomainIdentityManagement.IWebApplicationStarter, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IMonitor and  
    // Microsoft.SystemForCrossDomainIdentityManagement.Service are all defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service.dll.  

    Microsoft.SystemForCrossDomainIdentityManagement.IWebApplicationStarter starter;

    public Startup()
    {
        Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor = 
          new DevelopersMonitor();
        Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider = 
          new DevelopersProvider();
        this.starter = 
          new Microsoft.SystemForCrossDomainIdentityManagement.WebApplicationStarter(
            provider, 
            monitor);
    }

    public void Configuration(
      Owin.IAppBuilder builder) // Defined in in Owin.dll.  
    {
        this.starter.ConfigureApplication(builder);
    }
    }

###<a name="handling-endpoint-authentication"></a>Gère l’authentification du point de terminaison

Demandes d’Azure Active Directory incluent un jeton de support OAuth 2.0.   Un service de réception de la demande doit authentifier l’émetteur comme étant Azure Active Directory pour le locataire Azure Active Directory attendu, pour accéder au service Web du graphique de Azure d’Active Directory.  Dans le jeton, l’émetteur est identifié par une demande d’indemnisation iss, comme « iss » : « https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/ ».  Dans cet exemple, l’adresse de base de la valeur de la revendication, https://sts.windows.net, identifie Azure Active Directory en tant que l’émetteur, alors que le segment d’adresse relative, cbb1a5ac-f33b-45fa-9bf5-f37db0fed422, est un identificateur unique du locataire Azure Active Directory pour le compte duquel le jeton a été délivré.  Si le jeton a été délivré pour accéder au service Web du graphique de Azure d’Active Directory, l’identificateur de ce service, 00000002-0000-0000-c000-type "000000000000", doit être dans la valeur de revendication d’aud du jeton.  

Les développeurs qui utilisent les bibliothèques de l’Infrastructure du langage commun fournis par Microsoft pour la création d’un service SCIM peuvent authentifier les demandes d’Azure Active Directory à l’aide du package Microsoft.Owin.Security.ActiveDirectory en suivant ces étapes : 

**1:**  Dans un fournisseur, vous devez mettre en œuvre la propriété Microsoft.SystemForCrossDomainIdentityManagement.IProvider.StartupBehavior en le faisant revenir une méthode à appeler à chaque fois que le service est démarré : 

    public override Action\<Owin.IAppBuilder, System.Web.Http.HttpConfiguration.HttpConfiguration\> StartupBehavior
    {
      get
      {
        return this.OnServiceStartup;
      }
    }

    private void OnServiceStartup(
      Owin.IAppBuilder applicationBuilder,  // Defined in Owin.dll.  
      System.Web.Http.HttpConfiguration configuration)  // Defined in System.Web.Http.dll.  
    {
    }

**2:**  Ajoutez le code suivant à la méthode à toute demande à un des points de terminaison du service authentifiés comme portant un jeton émis par Azure Active Directory pour le compte d’un client spécifié, pour accéder au service Web du graphique de Azure d’Active Directory : 

    private void OnServiceStartup(
      Owin.IAppBuilder applicationBuilder IAppBuilder applicationBuilder, 
      System.Web.Http.HttpConfiguration HttpConfiguration configuration)
    {
      // IFilter is defined in System.Web.Http.dll.  
      System.Web.Http.Filters.IFilter authorizationFilter = 
        new System.Web.Http.AuthorizeAttribute(); // Defined in System.Web.Http.dll.configuration.Filters.Add(authorizationFilter);

      // SystemIdentityModel.Tokens.TokenValidationParameters is defined in    
      // System.IdentityModel.Token.Jwt.dll.
      SystemIdentityModel.Tokens.TokenValidationParameters tokenValidationParameters =     
        new TokenValidationParameters()
        {
          ValidAudience = "00000002-0000-0000-c000-000000000000"
        };

      // WindowsAzureActiveDirectoryBearerAuthenticationOptions is defined in 
      // Microsoft.Owin.Security.ActiveDirectory.dll
      Microsoft.Owin.Security.ActiveDirectory.
      WindowsAzureActiveDirectoryBearerAuthenticationOptions authenticationOptions =
        new WindowsAzureActiveDirectoryBearerAuthenticationOptions()    {
        TokenValidationParameters = tokenValidationParameters,
        Tenant = "03F9FCBC-EA7B-46C2-8466-F81917F3C15E" // Substitute the appropriate tenant’s 
                                                      // identifier for this one.  
      };

      applicationBuilder.UseWindowsAzureActiveDirectoryBearerAuthentication(authenticationOptions);
    }

##<a name="user-and-group-schema"></a>Schéma de groupe et d’utilisateur

Azure Active Directory peuvent fournir deux types de ressources à des Services Web SCIM.  Ces types de ressources sont des utilisateurs et des groupes.  

Ressources de l’utilisateur sont identifiés par l’identificateur de schéma, urn : ietf:params:scim:schemas:extension:enterprise:2.0:User, qui est inclus dans cette spécification de protocole : http://tools.ietf.org/html/draft-ietf-scim-core-schema.  Le mappage par défaut des attributs d’utilisateurs dans Azure Active Directory les attributs de ressources de l’urn : ietf:params:scim:schemas:extension:enterprise:2.0:User est fourni dans le tableau 1, ci-dessous.  

Ressources du groupe sont identifiés par l’identificateur de schéma, http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group.  Le tableau 2, ci-dessous, montre le mappage par défaut des attributs de groupes dans Azure Active Directory les attributs de ressources de http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group.  

###<a name="table-1-default-user-attribute-mapping"></a>Tableau 1 : La mappage d’attributs de l’utilisateur par défaut

| Azure utilisateur d’Active Directory | urn : ietf:params:scim:schemas:extension:enterprise:2.0:User |
| ------------- | ------------- |
| IsSoftDeleted | Active |
| displayName | displayName |
| Télécopie-numéro de téléphone | valeur de phoneNumbers [type eq « télécopie »] |
| givenName | name.givenName |
| jobTitle | titre |
| courrier | valeur d’e-mails [type eq « travail »] |
| mailNickname | externalId |
| Gestionnaire de | Gestionnaire de |
| Mobile | valeur de phoneNumbers [type eq « mobile »] |
| objectId | ID |
| code postal | .postalCode d’adresses [type eq « travail »] |
| Adresses proxy | e-mails [Tapez eq « autre »]. Valeur |
| physique-livraison-OfficeName | adresses [Tapez eq « autre »]. Mise en forme |
| streetAddress | .streetAddress d’adresses [type eq « travail »] |
| nom de famille | name.familyName |
| Numéro de téléphone | valeur de phoneNumbers [type eq « travail »] |
| au PrincipalName de l’utilisateur | nom d’utilisateur |


###<a name="table-2-default-group-attribute-mapping"></a>Tableau 2 : Mappage d’attribut de groupe par défaut

| Groupe de Active Directory Azure | http://schemas.Microsoft.com/2006/11/ResourceManagement/ADSCIM/Group |
| ------------- | ------------- |
| displayName | externalId |
| courrier | valeur d’e-mails [type eq « travail »] |
| mailNickname | displayName |
| membres | membres |
| objectId | ID |
| proxyAddresses | e-mails [Tapez eq « autre »]. Valeur |


##<a name="user-provisioning-and-de-provisioning"></a>Mise en service de l’utilisateur et la mise hors service

La figure ci-dessous illustre les messages que Azure Active Directory envoie à un service SCIM à gérer le cycle de vie d’un utilisateur dans une autre identité stockent.  Le diagramme montre également comment un service SCIM implémenté en utilisant les bibliothèques de l’Infrastructure du langage commun fournis par Microsoft pour la création de ces services convertit ces demandes en appels aux méthodes d’un fournisseur.  

![][4]
*Figure : Utilisateur mise en service et mise hors service de séquence*

**1:**  Azure Active Directory interroge le service d’un utilisateur avec une valeur d’attribut externalId correspondant à la valeur de l’attribut mailNickname d’un utilisateur dans Active Directory de Azure.  Les requêtes sont exprimées en tant que protocole de transfert hypertexte demande comme celui-ci, où jyoung est un exemple d’un mailNickname d’un utilisateur dans Active Directory de Azure : 

    GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
    Authorization: Bearer ...

Si le service a été créé en utilisant les bibliothèques de l’Infrastructure du langage commun fournis par Microsoft pour la mise en œuvre de services SCIM, la demande sera traduite en un appel à la méthode de requête de fournisseur du service.  Voici la signature de cette méthode : 

    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Protocol.  

    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource[]> Query(
      Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters parameters, 
      string correlationIdentifier);

Voici la définition de l’interface Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters : 

    public interface IQueryParameters: 
      Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
    {
        System.Collections.Generic.IReadOnlyCollection <Microsoft.SystemForCrossDomainIdentityManagement.IFilter> AlternateFilters 
        { get; }
    }

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
    {
      system.Collections.Generic.IReadOnlyCollection<string> ExcludedAttributePaths 
      { get; }
      System.Collections.Generic.IReadOnlyCollection<string> RequestedAttributePaths 
      { get; }
      string SchemaIdentifier 
      { get; }
    }

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IFilter
    {
        Microsoft.SystemForCrossDomainIdentityManagement.IFilter AdditionalFilter 
          { get; set; }
        string AttributePath 
          { get; } 
        Microsoft.SystemForCrossDomainIdentityManagement.ComparisonOperator FilterOperator 
          { get; }
        string ComparisonValue 
          { get; }
    }
    
    public enum Microsoft.SystemForCrossDomainIdentityManagement.ComparisonOperator
    {
        Equals
    }

Dans le cas de l’exemple ci-dessus d’une requête d’un utilisateur avec une valeur donnée de l’attribut externalId, valeurs des arguments passés à la méthode de requête sera comme suit : 

* paramètres. AlternateFilters.Count : 1
* paramètres. AlternateFilters.ElementAt(0). AttributePath : « externalId »
* paramètres. AlternateFilters.ElementAt(0). OpérateurComparaison : ComparisonOperator.Equals
* paramètres. AlternateFilter.ElementAt(0). ComparisonValue : « jyoung »
* correlationIdentifier : System.Net.Http.HttpRequestMessage.GetOwinEnvironment["owin. RequestId »] 

**2:**  Si la réponse à une requête au service d’un utilisateur avec une valeur d’attribut externalId correspondant à la valeur de l’attribut mailNickname d’un utilisateur dans Active Directory de Azure ne renvoie pas tous les utilisateurs, puis Azure Active Directory demande que le service de mise en service un utilisateur correspondant à celui de l’Azure Active Directory.  Voici un exemple d’une telle demande : 

    POST https://.../scim/Users HTTP/1.1
    Authorization: Bearer ...
    Content-type: application/json
    {
      "schemas":
      [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0User"],
      "externalId":"jyoung",
      "userName":"jyoung",
      "active":true,
      "addresses":null,
      "displayName":"Joy Young",
      "emails": [
        {
          "type":"work",
          "value":"jyoung@Contoso.com",
          "primary":true}],
      "meta": {
        "resourceType":"User"},
       "name":{
        "familyName":"Young",
        "givenName":"Joy"},
      "phoneNumbers":null,
      "preferredLanguage":null,
      "title":null,
      "department":null,
      "manager":null}

Les bibliothèques de l’Infrastructure du langage commun fournis par Microsoft pour la mise en œuvre de services SCIM traduira dans un appel à la méthode Create de fournisseur de service le demande.  La méthode Create a cette signature : 

    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  

    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> Create(
      Microsoft.SystemForCrossDomainIdentityManagement.Resource resource, 
      string correlationIdentifier);

Dans le cas d’une demande de configurer un utilisateur, la valeur de l’argument de la ressource sera une instance de le Microsoft.SystemForCrossDomainIdentityManagement. Classe de Core2EnterpriseUser, définie dans la bibliothèque Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  Si la demande de mise en service l’utilisateur réussit, alors que l’implémentation de la méthode doit retourner une instance de la la Microsoft.SystemForCrossDomainIdentityManagement. Classe de Core2EnterpriseUser, avec la valeur de la propriété d’identificateur définie pour l’identificateur unique de l’utilisateur qui vient d’être mis en service.  

**3:**  Pour mettre à jour un utilisateur connu pour être présent dans une banque d’identités fronted par un SCIM, Azure Active Directory se poursuit par la demande de l’état actuel de cet utilisateur le service avec une requête comme celle-ci : 

    GET ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...

Dans un service intégré en utilisant les bibliothèques de l’Infrastructure du langage commun fournis par Microsoft pour la mise en œuvre de services SCIM, la demande sera traduite en un appel à la méthode de récupération du fournisseur du service.  Voici la signature de la méthode de récupération : 

    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource and 
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters 
    // are defined in Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> 
       Retrieve(
         Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters 
           parameters, 
           string correlationIdentifier);
    
    public interface 
      Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters:   
        IRetrievalParameters
        {
          Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
            ResourceIdentifier 
              { get; }
    }
    public interface Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier
    {
        string Identifier 
          { get; set; }
        string Microsoft.SystemForCrossDomainIdentityManagement.SchemaIdentifier 
          { get; set; }
    }

Dans le cas de l’exemple précédent d’une requête pour extraire l’état actuel d’un utilisateur, les valeurs des propriétés de l’objet fourni comme valeur de l’argument parameters sera comme suit : 

* Identificateur : « 54D382A4-2050-4C03-94D1-E769F1D15682 »
* SchemaIdentifier : « urn : ietf:params:scim:schemas:extension:enterprise:2.0:User »

**4:**  Si un attribut de référence est mise à jour, puis Azure Active Directory interrogent le service pour déterminer si la valeur actuelle de l’attribut de référence dans la banque d’identités fronted déjà par le service correspond à la valeur de l’attribut dans Azure Active Directory.  Dans le cas d’utilisateurs, le seul attribut dont la valeur actuelle sera interrogée de cette façon est l’attribut de gestionnaire.  Voici un exemple d’une demande pour déterminer si l’attribut de gestionnaire d’un objet utilisateur particulier a actuellement une certaine valeur : 

    GET ~/scim/Users?filter=id eq 54D382A4-2050-4C03-94D1-E769F1D15682 and manager eq 2819c223-7f76-453a-919d-413861904646&attributes=id HTTP/1.1
    Authorization: Bearer ...

La valeur du paramètre de requête attributs, id, signifie que si un objet utilisateur qui correspond à l’expression fournie comme valeur du paramètre de requête de filtre, puis le service doit répondre avec une ressource urn : ietf:params:scim:schemas:core:2.0:User ou urn : ietf:params:scim:schemas:extension:enterprise:2.0:User, y compris uniquement la valeur de l’attribut d’id de cette ressource.  Bien entendu, la valeur de l’attribut id est connue au demandeur, il est inclus dans la valeur du paramètre de requête de filtre ; l’objectif de la demande pour qu’il est en fait de demander une représentation minimale d’une ressource répondant à l’expression de filtre comme une indication d’ou non un tel objet existe.   

Si le service a été créé en utilisant les bibliothèques de l’Infrastructure du langage commun fournis par Microsoft pour la mise en œuvre de services SCIM, la demande sera traduite en un appel à la méthode de requête de fournisseur du service.  La valeur des propriétés de l’objet fourni comme valeur de l’argument parameters sera comme suit : 

* paramètres. AlternateFilters.Count : 2
* paramètres. AlternateFilters.ElementAt(x). AttributePath : « id »
* paramètres. AlternateFilters.ElementAt(x). OpérateurComparaison : ComparisonOperator.Equals
* paramètres. AlternateFilter.ElementAt(x). ComparisonValue : « 54D382A4-2050-4C03-94D1-E769F1D15682 »
* paramètres. AlternateFilters.ElementAt(y). AttributePath : « manager »
* paramètres. AlternateFilters.ElementAt(y). OpérateurComparaison : ComparisonOperator.Equals
* paramètres. AlternateFilter.ElementAt(y). ComparisonValue : « 2819c223-7f76-453a-919d-413861904646 »
* paramètres. RequestedAttributePaths.ElementAt(0) : « id »
* paramètres. SchemaIdentifier : « urn : ietf:params:scim:schemas:extension:enterprise:2.0:User »

Ici, la valeur de l’index x peut être 0 et la valeur de l’index y peut être 1, ou la valeur de x est peut-être 1 et la valeur de y peut être 0, selon l’ordre des expressions de paramètre de la requête de filtre.   

**5:**  Voici un exemple d’une demande d’Azure Active Directory à un service SCIM pour mettre à jour d’un utilisateur : 

    PATCH ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
    Content-type: application/json
    {
      "schemas": 
      [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"],
      "Operations":
      [
        {
          "op":"Add",
          "path":"manager",
          "value":
            [
              {
                "$ref":"http://.../scim/Users/2819c223-7f76-453a-919d-413861904646",
                "value":"2819c223-7f76-453a-919d-413861904646"}]}]}

Les bibliothèques de l’Infrastructure du langage commun Microsoft pour l’implémentation des services SCIM traduirait la demande dans un appel à la méthode de mise à jour du fournisseur du service.  Voici la signature de cette méthode : 

    // System.Threading.Tasks.Tasks and 
    // System.Collections.Generic.IReadOnlyCollection<T>
    // are defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IPatch, 
    // Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
    // Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation, 
    // Microsoft.SystemForCrossDomainIdentityManagement.OperationName, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IPath and 
    // Microsoft.SystemForCrossDomainIdentityManagement.OperationValue 
    // are all defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 

    System.Threading.Tasks.Task Update(
      Microsoft.SystemForCrossDomainIdentityManagement.IPatch patch, 
      string correlationIdentifier);

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IPatch
    {
    Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase 
      PatchRequest 
        { get; set; }
    Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
      ResourceIdentifier 
        { get; set; }        
    }

    public class PatchRequest2: 
      Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase
    {
    public System.Collections.Generic.IReadOnlyCollection
      <Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation> 
        Operations
        { get;}

    public void AddOperation(
      Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation operation);
    }

    public class PatchOperation
    {
    public Microsoft.SystemForCrossDomainIdentityManagement.OperationName 
      Name
      { get; set; }
    
    public Microsoft.SystemForCrossDomainIdentityManagement.IPath 
      Path
      { get; set; }

    public System.Collections.Generic.IReadOnlyCollection
      <Microsoft.SystemForCrossDomainIdentityManagement.OperationValue> Value
      { get; }

    public void AddValue(
      Microsoft.SystemForCrossDomainIdentityManagement.OperationValue value);
    }

    public enum OperationName
    {
      Add,
      Remove,
      Replace
    }

    public interface IPath
    {
      string AttributePath { get; }
      System.Collections.Generic.IReadOnlyCollection<IFilter> SubAttributes { get; }
      Microsoft.SystemForCrossDomainIdentityManagement.IPath ValuePath { get; }
    }

    public class OperationValue
    {
      public string Reference
      { get; set; }
      
      public string Value
      { get; set; }
    }



Dans le cas de l’exemple précédent d’une demande de mise à jour d’un utilisateur, l’objet fourni comme valeur de l’argument correctif aura à ces valeurs de propriété : 

* ResourceIdentifier.Identifier : « 54D382A4-2050-4C03-94D1-E769F1D15682 »
* ResourceIdentifier.SchemaIdentifier : « urn : ietf:params:scim:schemas:extension:enterprise:2.0:User »
* (PatchRequest en tant que PatchRequest2). Operations.Count : 1
* (PatchRequest en tant que PatchRequest2). Operations.ElementAt(0). OperationName : OperationName.Add
* (PatchRequest en tant que PatchRequest2). Operations.ElementAt(0). Path.AttributePath : « manager »
* (PatchRequest en tant que PatchRequest2). Operations.ElementAt(0). Value.Count : 1
* (PatchRequest en tant que PatchRequest2). Operations.ElementAt(0). Value.ElementAt(0). Référence : http://.../scim/Users/2819c223-7f76-453a-919d-413861904646
* (PatchRequest en tant que PatchRequest2). Operations.ElementAt(0). Value.ElementAt(0). Valeur : 2819c223-7f76-453a-919d-413861904646

**6:**  Pour mettre hors service un utilisateur à partir d’une banque d’identités fronted par un service SCIM, Azure Active Directory envoie une demande comme celui-ci : 

    DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
    
Si le service a été créé en utilisant les bibliothèques de l’Infrastructure du langage commun fournis par Microsoft pour la mise en œuvre de services SCIM, la demande sera traduite en un appel à la méthode de suppression du fournisseur du service.   Cette méthode a cette signature : 

    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
    // is defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 
    System.Threading.Tasks.Task Delete(
      Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier  
        resourceIdentifier, 
      string correlationIdentifier);
 
L’objet fourni comme valeur de l’argument resourceIdentifier a ces valeurs de propriété dans le cas de l’exemple précédent d’une demande pour mettre hors service un utilisateur : 

* ResourceIdentifier.Identifier : « 54D382A4-2050-4C03-94D1-E769F1D15682 »
* ResourceIdentifier.SchemaIdentifier : « urn : ietf:params:scim:schemas:extension:enterprise:2.0:User »

##<a name="group-provisioning-and-de-provisioning"></a>Mise en service du groupe et la mise hors service

La figure ci-dessous illustre les messages que Azure Active Directory envoie à un service SCIM à gérer le cycle de vie d’un groupe dans une autre identité stockent.  Ces messages diffèrent des messages relatifs à des utilisateurs de trois manières : 

* Le schéma d’une ressource de groupe est identifié en tant que http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group.  
* Requêtes pour récupérer des groupes déterminera que l’attribut de membres est à exclure à partir de n’importe quelle ressource fournie en réponse à la demande.  
* Requêtes pour déterminer si un attribut de référence a une certaine valeur seront des demandes sur l’attribut de membres.  

![][5]
*Figure : Groupe de mise en service et mise hors service de séquence*

##<a name="related-articles"></a>Articles connexes

- [Index des articles pour la gestion d’Application dans Azure Active Directory](active-directory-apps-index.md)
- [Automatiser l’utilisateur mise en service/hors service pour les applications SaaS](active-directory-saas-app-provisioning.md)
- [Personnalisation des mappages d’attributs pour l’approvisionnement de l’utilisateur](active-directory-saas-customizing-attribute-mappings.md)
- [Écriture d’Expressions pour les mappages d’attributs](active-directory-saas-writing-expressions-for-attribute-mappings.md)
- [Portée des filtres pour l’approvisionnement de l’utilisateur](active-directory-saas-scoping-filters.md)
- [Mise en service des Notifications de compte](active-directory-saas-account-provisioning-notifications.md)
- [Liste des didacticiels sur la façon d’intégrer les applications SaaS](active-directory-saas-tutorial-list.md)


    
<!--Image references-->
[1]: ./media/active-directory-scim-provisioning/scim-figure-1.PNG
[2]: ./media/active-directory-scim-provisioning/scim-figure-2.PNG
[3]: ./media/active-directory-scim-provisioning/scim-figure-3.PNG
[4]: ./media/active-directory-scim-provisioning/scim-figure-4.PNG
[5]: ./media/active-directory-scim-provisioning/scim-figure-5.PNG
