<properties 
    pageTitle="Créer une application Web dans le Service d’application Azure à l’aide de l’Azure SDK pour Java" 
    description="Apprenez à créer une application Web sur le Service d’application Azure par programme à l’aide de l’Azure SDK pour Java." 
    tags="azure-classic-portal"
    services="app-service\web" 
    documentationCenter="Java" 
    authors="donntrenton" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="multiple" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="02/25/2016" 
    ms.author="v-donntr"/>


# <a name="create-a-web-app-in-azure-app-service-using-the-azure-sdk-for-java"></a>Créer une application Web dans le Service d’application Azure à l’aide de l’Azure SDK pour Java

<!-- Azure Active Directory workflow is not yet available on the Azure Portal -->

## <a name="overview"></a>Vue d’ensemble

Cette procédure pas à pas vous montre comment créer un Azure SDK pour les applications Java qui crée une application Web dans le [Service d’application Azure][], puis déployer une application vers le serveur. Il se compose de deux parties :

- Partie 1 montre comment générer une application Java qui crée une application web.
- Partie 2 montre comment créer un simple JSP « Hello World » application client, puis utiliser FTP pour déployer le code de Service de l’application.


## <a name="prerequisites"></a>Conditions préalables

### <a name="software-installations"></a>Installations de logiciels

Le code de l’application AzureWebDemo dans cet article a été écrit à l’aide d’Azure Java SDK 0.7.0, que vous pouvez installer à l’aide [Web Platform Installer][] (WebPI). En outre, assurez-vous d’utiliser la version la plus récente de [Shared Computer Toolkit Azure pour Eclipse][]. Après avoir installé le Kit de développement logiciel, mettre à jour les dépendances dans votre projet Eclipse par **Mettre à jour l’Index** en cours d’exécution dans les **Référentiels de Maven**, puis ajouter à nouveau la dernière version de chaque lot dans la fenêtre des **dépendances** . Vous pouvez vérifier la version de votre logiciel installé dans Eclipse en cliquant sur **Aide > Détails de l’Installation**; Vous devez avoir au moins les versions suivantes :

- Package pour les bibliothèques Microsoft Azure pour Java 0.7.0.20150309
- Eclipse IDE pour les développeurs 4.4.2.20150219 de Java EE


### <a name="create-and-configure-cloud-resources-in-azure"></a>Créer et configurer les ressources de Cloud dans Azure

Avant de commencer cette procédure, vous devez posséder un abonnement Azure actif et définir une valeur par défaut de Active Directory (AD) sur Azure.


### <a name="create-an-active-directory-ad-in-azure"></a>Créer un serveur Active Directory (AD) dans Azure

Si vous ne disposez pas déjà d’un Active Directory (AD) sur votre abonnement Azure, ouvrez une session dans [Azure portal classique][] avec votre compte Microsoft. Si vous disposez de plusieurs abonnements, cliquez sur **abonnements** , puis sélectionnez le répertoire par défaut pour l’abonnement que vous souhaitez utiliser pour ce projet. Puis, cliquez sur **Appliquer** pour passer à cet affichage de l’abonnement.

1. Sélectionnez **Active Directory** dans le menu à gauche. **Cliquez sur Nouveau > répertoire > personnalisé créer**.

2. Dans **Ajout d’un répertoire**, sélectionnez **Créer un nouveau répertoire**.

3. Dans la zone **nom**, entrez un nom de répertoire.

4. Dans **domaine**, entrez un nom de domaine. Il s’agit d’un nom de domaine de base qui est inclus par défaut dans votre répertoire ; Elle présente la forme : `<domain_name>.onmicrosoft.com`. Vous pouvez la nommer basé sur le nom du répertoire ou un autre nom de domaine que vous possédez. Par la suite, vous pouvez ajouter un autre nom de domaine que votre organisation utilise déjà.

5. Dans un **pays ou une région**, sélectionnez vos paramètres régionaux.

Pour plus d’informations sur Active Directory, reportez-vous à la section [qu’est un répertoire AD Azure][]?


### <a name="create-a-management-certificate-for-azure"></a>Créer un certificat de gestion pour Azure

Azure SDK pour Java utilise les certificats de gestion de se pour authentifier auprès d’abonnements Azure. Il s’agit des certificats X.509 v3 que vous permet d’authentifier une application cliente qui utilise l’API de gestion de Service pour agir pour le compte du propriétaire pour gérer les ressources de l’abonnement.

Le code dans cette procédure utilise un certificat auto-signé pour authentifier avec Azure. Pour cette procédure, vous devez créer un certificat et le télécharger vers [Azure portal classique][] au préalable. Cela implique les étapes suivantes :

- Générer un fichier PFX représentant votre certificat client et l’enregistrer localement.
- Générer un certificat de gestion (fichier CER) à partir du fichier PFX.
- Téléchargez le fichier de région d’exécution limitée à votre abonnement Azure.
- Convertir le fichier PFX JKS, car Java utilise ce format pour s’authentifier à l’aide de certificats.
- Écrire du code d’authentification de l’application, qui fait référence au fichier JKS local.

Une fois cette procédure terminée, le certificat de la région d’exécution limitée doit résider dans votre abonnement Azure et le certificat JKS doit résider sur votre disque local. Pour plus d’informations sur les certificats de gestion, voir [créer et télécharger un certificat de gestion pour Azure][].


#### <a name="create-a-certificate"></a>Créer un certificat

Pour créer votre propre certificat auto-signé, ouvrez une console de commande de votre système d’exploitation et exécutez les commandes suivantes.

> **Remarque :**  L’ordinateur sur lequel vous exécutez cette commande doit être le JDK installé. En outre, le chemin d’accès à l’outil de clés dépend de l’emplacement dans lequel vous installez le JDK. Pour plus d’informations, consultez [clé et outil de gestion des certificats (keytool)][] dans la documentation en ligne de Java.

Pour créer le fichier .pfx :

    <java-install-dir>/bin/keytool -genkey -alias <keystore-id>
     -keystore <cert-store-dir>/<cert-file-name>.pfx -storepass <password>
     -validity 3650 -keyalg RSA -keysize 2048 -storetype pkcs12
     -dname "CN=Self Signed Certificate 20141118170652"

Pour créer le fichier .cer :

    <java-install-dir>/bin/keytool -export -alias <keystore-id>
     -storetype pkcs12 -keystore <cert-store-dir>/<cert-file-name>.pfx
     -storepass <password> -rfc -file <cert-store-dir>/<cert-file-name>.cer

où :

- `<java-install-dir>`est le chemin d’accès au répertoire dans lequel vous avez installé Java.
- `<keystore-id>`est l’identificateur d’entrée keystore (par exemple, `AzureRemoteAccess`).
- `<cert-store-dir>`est le chemin d’accès au répertoire dans lequel vous souhaitez stocker les certificats (par exemple `C:/Certificates`).
- `<cert-file-name>`est le nom du fichier de certificat (par exemple `AzureWebDemoCert`).
- `<password>`le mot de passe que vous choisissez pour protéger le certificat ; Il doit comporter au moins 6 caractères. Vous ne pouvez entrer aucun mot de passe, même si cela n’est pas recommandé.
- `<dname>`est le nom unique X.500 à associer à l’alias et est utilisé en tant que les champs émetteur et le sujet dans le certificat auto-signé.

Pour plus d’informations, consultez [créer et télécharger un certificat de gestion pour Azure][].


#### <a name="upload-the-certificate"></a>Télécharger le certificat

Pour charger un certificat auto-signé sur Azure, accédez à la page **paramètres** du portail classique, puis cliquez sur l’onglet de **Gestion des certificats** . Cliquez sur **Télécharger** en bas de la page, naviguez jusqu'à l’emplacement du fichier CER que vous avez créé.


#### <a name="convert-the-pfx-file-into-jks"></a>Convertir le fichier PFX JKS

Dans l’invite de commandes (en cours d’exécution en tant qu’administrateur), cd vers le répertoire contenant les certificats et exécutez la commande suivante, où `<java-install-dir>` est le répertoire dans lequel vous avez installé Java sur votre ordinateur :

    <java-install-dir>/bin/keytool.exe -importkeystore
     -srckeystore <cert-store-dir>/<cert-file-name>.pfx
     -destkeystore <cert-store-dir>/<cert-file-name>.jks
     -srcstoretype pkcs12 -deststoretype JKS

1. Lorsque vous y êtes invité, entrez le mot de passe du fichier keystore de clés de destination ; Il s’agit du mot de passe pour le fichier JKS.

2. Lorsque vous y êtes invité, entrez le mot de passe du keystore de source ; C’est le mot de passe que vous avez spécifié pour le fichier PFX.

Les deux mots de passe n’ont pas la même. Vous ne pouvez entrer aucun mot de passe, même si cela n’est pas recommandé.


## <a name="build-a-web-app-creation-application"></a>Créer une application de création Web App

### <a name="create-the-eclipse-workspace-and-maven-project"></a>Créer l’espace de travail Eclipse et Maven projet

Dans cette section, vous créez un espace de travail et un projet Maven pour l’application de création de l’application web, nommé AzureWebDemo.

1. Créez un nouveau projet Maven. Cliquez sur **fichier > Nouveau > projet de Maven**. Dans le **Nouveau projet Maven**, sélectionnez **créer un projet simple** et **emplacement d’espace de travail utilisé par défaut**.

2. Sur la deuxième page du **Nouveau projet Maven**, spécifiez les éléments suivants :

    - ID de groupe :`com.<username>.azure.webdemo`
    - ID de l’artefact : AzureWebDemo
    - Version : 0.0.1-SNAPSHOT
    - Emballage : jar
    - Nom : AzureWebDemo

    Cliquez sur **Terminer**.

3. Dans l’Explorateur de projets, ouvrez le nouveau fichier du projet pom.xml. Sélectionnez l’onglet **dépendances** . Comme il s’agit d’un nouveau projet, aucun package ne figurent encore.

4. Ouvrez la vue Maven référentiels. **Cliquez sur la fenêtre > Afficher > autres > Maven > Maven référentiels** , puis cliquez sur **OK**. L’affichage **Des référentiels Maven** apparaîtra au bas de l’IDE.

5. Ouvrir **Des référentiels globaux**, le référentiel **central** d’avec le bouton droit et sélectionnez **Reconstruire l’Index**.

    ![][1]
    
    Cette étape peut prendre plusieurs minutes selon la vitesse de votre connexion. Lorsque l’index est reconstruit, vous devriez voir les packages dans le référentiel **central** Maven Microsoft Azure.

6. Dans **dépendances**, cliquez sur **Ajouter**. Dans le **Groupe d’entrée ID...** entrez `azure-management`. Sélectionnez les modules de gestion de base et des application Service Web Apps :

        com.microsoft.azure  azure-management
        com.microsoft.azure  azure-management-websites

    > **Remarque :** Si vous mettez à jour les dépendances après une nouvelle version de version, vous devez ajouter à nouveau chaque dépendance dans cette liste.
    > Une fois que vous cliquez sur **Ajouter** et sélectionnez chaque dépendance, il s’affiche avec le nouveau numéro de version dans la liste des **dépendances** .

Cliquez sur **OK**. Les packages Azure apparaissent ensuite dans la liste des **dépendances** .


### <a name="writing-java-code-to-create-a-web-app-by-calling-the-azure-sdk"></a>Écriture de Code Java pour créer une application Web en appelant le SDK Azure

Ensuite, écrivez le code qui appelle des API dans Azure SDK pour Java pour créer l’application web de Service de l’application.

1. Créez une classe Java pour contenir le code de point d’entrée principal. Dans l’Explorateur de projet, avec le bouton droit sur le nœud du projet et sélectionnez **Nouveau > classe**.

2. Dans la **Nouvelle classe de Java**, nommez la classe `WebCreator` et cochez la case **principale de void statique publique** . Les sélections doivent apparaître comme suit :

    ![][2]

3. Cliquez sur **Terminer**. Le fichier WebCreator.java s’affiche dans l’Explorateur de projets.


### <a name="calling-the-azure-api-to-create-an-app-service-web-app"></a>Appel de l’API Azure pour créer une application Service Web App


#### <a name="add-necessary-imports"></a>Ajoutez les importations nécessaires

Dans WebCreator.java, ajoutez les importations ci-après ; ces importations fournissent l’accès aux classes dans les bibliothèques de gestion pour l’utilisation des API d’Azure :

    // General imports
    import java.net.URI;
    import java.util.ArrayList;
    
    // Imports for Exceptions
    import java.io.IOException;
    import java.net.URISyntaxException;
    import javax.xml.parsers.ParserConfigurationException;
    import com.microsoft.windowsazure.exception.ServiceException;
    import org.xml.sax.SAXException;
    
    // Imports for Azure App Service management configuration
    import com.microsoft.windowsazure.Configuration;
    import com.microsoft.windowsazure.management.configuration.ManagementConfiguration;
    
    // Service management imports for App Service Web Apps creation
    import com.microsoft.windowsazure.management.websites.*;
    import com.microsoft.windowsazure.management.websites.models.*;
    
    // Imports for authentication
    import com.microsoft.windowsazure.core.utils.KeyStoreType;


#### <a name="define-the-main-entry-point-class"></a>Définir la classe de point d’entrée principal

Étant donné que l’objectif de l’application AzureWebDemo est de créer une application de Service Web App, nom de la classe principale pour cette application `WebAppCreator`. Cette classe fournit le code de point d’entrée principal qui appelle l’API de gestion de Service Azure pour créer l’application web.

Ajoutez les définitions de paramètre suivantes pour l’application web et webspace. Vous devez fournir vos propres informations d’identification et le certificat abonnement Azure.

    public class WebAppCreator {
    
        // Parameter definitions used for authentication.
        private static String uri = "https://management.core.windows.net/";
        private static String subscriptionId = "<subscription-id>";
        private static String keyStoreLocation = "<certificate-store-path>";
        private static String keyStorePassword = "<certificate-password>";
    
        // Define web app parameter values.
        private static String webAppName = "WebDemoWebApp";
        private static String domainName = ".azurewebsites.net";
        private static String webSpaceName = WebSpaceNames.WESTUSWEBSPACE;
        private static String appServicePlanName = "WebDemoAppServicePlan";

où :

- `<subscription-id>`est l’ID d’abonnement Azure dans lequel vous souhaitez créer la ressource.
- `<certificate-store-path>`est le chemin d’accès et le nom de fichier pour le fichier JKS dans le répertoire du magasin de certificats local. Par exemple, `C:/Certificates/CertificateName.jks` pour Linux et `C:\Certificates\CertificateName.jks` pour Windows.
- `<certificate-password>`est le mot de passe que vous avez spécifié lorsque vous avez créé votre certificat JKS.
- `webAppName`peut être n’importe quel nom que vous choisissez ; Cette procédure utilise le nom de `WebDemoWebApp`. Le nom de domaine complet est le `webAppName` avec la `domainName` est ajouté, par conséquent, dans ce cas le domaine complet est `webdemowebapp.azurewebsites.net`.
- `domainName`doit être spécifié comme indiqué ci-dessus.
- `webSpaceName`doit être une des valeurs définies dans la classe [WebSpaceNames][] .
- `appServicePlanName`doit être spécifié comme indiqué ci-dessus.

> **Remarque :** Chaque fois que vous exécutez cette application, vous devez modifier la valeur de `webAppName` et `appServicePlanName` (ou supprimer l’application web sur le portail Azure) avant d’exécuter à nouveau l’application. Dans le cas contraire, l’exécution échoue parce que la même ressource existe déjà dans Azure.


#### <a name="define-the-web-creation-method"></a>Définir la méthode de création web

Ensuite, définissez une méthode pour créer l’application web. Cette méthode, `createWebApp`, spécifie les paramètres de l’application web et la webspace. Il crée et configure le client de gestion App Service Web Apps, qui est défini par l’objet [WebSiteManagementClient][] . Le client de gestion est indispensable pour créer des applications Web. Il fournit des services web RESTful qui permettent aux applications de gérer les applications web (des opérations telles que la création, mise à jour et suppression) en appelant l’API de gestion de service.

    private static void createWebApp() throws Exception {

        // Specify configuration settings for the App Service management client.
        Configuration config = ManagementConfiguration.configure(
            new URI(uri),
            subscriptionId,
            keyStoreLocation,  // Path to the JKS file
            keyStorePassword,  // Password for the JKS file
            KeyStoreType.jks   // Flag that you are using a JKS keystore
        );

        // Create the App Service Web Apps management client to call Azure APIs
        // and pass it the App Service management configuration object.
        WebSiteManagementClient webAppManagementClient = WebSiteManagementService.create(config);

        // Create an App Service plan for the web app with the specified parameters.
        WebHostingPlanCreateParameters appServicePlanParams = new WebHostingPlanCreateParameters();
        appServicePlanParams.setName(appServicePlanName);
        appServicePlanParams.setSKU(SkuOptions.Free);
        webAppManagementClient.getWebHostingPlansOperations().create(webSpaceName, appServicePlanParams);

        // Set webspace parameters.
        WebSiteCreateParameters.WebSpaceDetails webSpaceDetails = new WebSiteCreateParameters.WebSpaceDetails();
        webSpaceDetails.setGeoRegion(GeoRegionNames.WESTUS);
        webSpaceDetails.setPlan(WebSpacePlanNames.VIRTUALDEDICATEDPLAN);
        webSpaceDetails.setName(webSpaceName);

        // Set web app parameters.
        // Note that the server farm name takes the Azure App Service plan name.
        WebSiteCreateParameters webAppCreateParameters = new WebSiteCreateParameters();
        webAppCreateParameters.setName(webAppName);
        webAppCreateParameters.setServerFarm(appServicePlanName);
        webAppCreateParameters.setWebSpace(webSpaceDetails);

        // Set usage metrics attributes.
        WebSiteGetUsageMetricsResponse.UsageMetric usageMetric = new WebSiteGetUsageMetricsResponse.UsageMetric();
        usageMetric.setSiteMode(WebSiteMode.Basic);
        usageMetric.setComputeMode(WebSiteComputeMode.Shared);

        // Define the web app object.
        ArrayList<String> fullWebAppName = new ArrayList<String>();
        fullWebAppName.add(webAppName + domainName);
        WebSite webApp = new WebSite();
        webApp.setHostNames(fullWebAppName);

        // Create the web app.
        WebSiteCreateResponse webAppCreateResponse = webAppManagementClient.getWebSitesOperations().create(webSpaceName, webAppCreateParameters);

        // Output the HTTP status code of the response; 200 indicates the request succeeded; 4xx indicates failure.
        System.out.println("----------");
        System.out.println("Web app created - HTTP response " + webAppCreateResponse.getStatusCode() + "\n");

        // Output the name of the web app that this application created.
        String shinyNewWebAppName = webAppCreateResponse.getWebSite().getName();
        System.out.println("----------\n");
        System.out.println("Name of web app created: " + shinyNewWebAppName + "\n");
        System.out.println("----------\n");
    }

Le code affiche l’état HTTP de la réponse indiquant le succès ou l’échec et si l’opération réussit, affiche le nom de l’application web créée.


#### <a name="define-the-main-method"></a>Définir la méthode main()

Fournir le code de la méthode main() qui appelle createWebApp() pour créer l’application web.

Enfin, appelez `createWebApp` de `main`:

        public static void main(String[] args)
            throws IOException, URISyntaxException, ServiceException,
            ParserConfigurationException, SAXException, Exception {

            // Create web app
            createWebApp();

        }  // end of main()

    }  // end of WebAppCreator class


#### <a name="run-the-application-and-verify-web-app-creation"></a>Exécutez l’application et vérifier la création d’applications web

Pour vérifier que votre application s’exécute, cliquez sur **Exécuter > exécuter**. Lorsque l’application est terminée, vous devriez voir la sortie suivante dans la console d’Eclipse :

    ----------
    Web app created - HTTP response 200
    
    ----------
    
    Name of web app created: WebDemoWebApp
    
    ----------

Connectez-vous au portail classique Azure et cliquez sur des **Applications Web**. La nouvelle application web doit apparaître dans la liste des applications Web dans quelques minutes.


## <a name="deploying-an-application-to-the-web-app"></a>Déploiement d’une Application à l’application Web

Après avoir exécuter AzureWebDemo et créé la nouvelle application web, connectez-vous au portail classique et cliquez sur **Les applications Web**, sélectionnez **WebDemoWebApp** dans la liste des **Applications Web** . Dans la page de tableau de bord de l’application web, cliquez sur **Parcourir** (ou cliquez sur l’URL, `webdemowebapp.azurewebsites.net`) pour naviguer jusqu'à lui. Vous verrez une page espace réservé vide, dans la mesure où aucun contenu n’a encore été publiée à l’application web.

Ensuite vous créez une application « Hello World » et le déployer sur l’application web.


### <a name="create-a-jsp-hello-world-application"></a>Créer une application Hello World JSP

#### <a name="create-the-application"></a>Création de l’application

Pour montrer comment déployer une application sur le web, la procédure suivante vous indique comment créer une application Java de « Hello World » simple et le télécharger vers le Service Web App App créés par votre application.

1. Cliquez sur **fichier > Nouveau > projet Web dynamique**. Le nom `JSPHello`. Vous n’avez pas besoin de modifier d’autres paramètres dans cette boîte de dialogue. Cliquez sur **Terminer**.

    ![][3]

2. Dans l’Explorateur de projets, développez le projet **JSPHello** , cliquez droit sur le **contenu Web**, puis cliquez sur **Nouveau > fichier JSP**. Dans la boîte de dialogue Nouveau fichier JSP, nommez le nouveau fichier `index.jsp`. Cliquez sur **suivant**.

3. Dans la boîte de dialogue **Sélectionner un modèle JSP** , sélectionnez de **Nouveau fichier JSP (html)** et cliquez sur **Terminer**.

4. Dans index.jsp, ajoutez le code suivant dans le `<head>` et `<body>` sections de la balise :

        <head>
          ...
          java.util.Date date = new java.util.Date();
        </head>
    
        <body>
          Hello, the time is <%= date %> 
        </body>


#### <a name="run-the-hello-world-application-in-localhost"></a>Exécutez l’application Hello World dans localhost

Avant d’exécuter cette application, vous devez configurer certaines propriétés.

1. Cliquez sur le projet **JSPHello** et sélectionnez **Propriétés**.

2. Dans la boîte de dialogue **Propriétés** : sélectionnez le **Chemin d’accès de la génération de Java**, sélectionnez l’onglet **ordre et exportation** , **Bibliothèque de système de JRE**, puis cliquez sur **haut** pour le déplacer vers le haut de la liste.

    ![][4]

3. Également dans la boîte de dialogue **Propriétés** : sélectionnez **Ciblé des Runtimes** et cliquez sur **Nouveau**.

4. Dans la boîte de dialogue **Nouvel environnement d’exécution de serveur** , sélectionnez un serveur **Apache Tomcat v7.0** et cliquez sur **suivant**. Dans la boîte de dialogue **Serveur Tomcat** , définissez le **nom** sur `Apache Tomcat v7.0`et de définir le **Répertoire d’Installation de Tomcat** dans le répertoire dans lequel vous avez installé la version du serveur Tomcat que vous souhaitez utiliser.

    ![][5]

    Cliquez sur **Terminer**.

5. Vous renvoie à la page **Cible les Runtimes** de la boîte de dialogue **Propriétés** . Sélectionnez **Apache Tomcat v7.0**, puis cliquez sur **OK**.

    ![][6]

6. Dans le menu Eclipse à **exécuter** , cliquez sur **exécuter**. Dans la boîte de dialogue **Exécuter en tant que** , sélectionnez **exécuter sur le serveur**. Dans la boîte de dialogue **exécuter sur le serveur** , sélectionnez **Tomcat v7.0 Server**:

    ![][7]

    Cliquez sur **Terminer**.

7. Lorsque l’application s’exécute, vous devez voir la page **JSPHello** apparaissent dans une fenêtre de localhost dans Eclipse (`http://localhost:8080/JSPHello/`), affichant le message suivant :

    `Hello World, the time is Tue Mar 24 23:21:10 GMT 2015`


#### <a name="export-the-application-as-a-war"></a>Exporter l’application sous la forme d’une guerre

Exporter les fichiers projet web sous la forme d’un fichier archive (WAR) web afin que vous pouvez la déployer vers l’application web. Les fichiers projet web suivants se trouvent dans le dossier de contenu Web :

    META-INF
    WEB-INF
    index.jsp

1. Cliquez sur le dossier de contenu Web et sélectionnez **Exporter**.

2. Dans la boîte de dialogue **Exporter le sélectionner** , cliquez sur **Web > guerre** de fichier, puis cliquez sur **suivant**.

3. Dans la boîte de dialogue **Exporter de guerre** , sélectionnez le répertoire src dans le projet en cours et inclure le nom du fichier WAR à la fin. Par exemple :

    `<project-path>/JSPHello/src/JSPHello.war`

Pour plus d’informations sur le déploiement de fichiers WAR, voir [Ajouter une application Java pour Azure Application Service Web Apps](web-sites-java-add-app.md).


### <a name="deploying-the-hello-world-application-using-ftp"></a>Déploiement de l’Application Hello World FTP

Sélectionnez un client FTP de tiers pour publier l’application. Cette procédure décrit les deux options : la console Kudu intégrée dans Azure ; et FileZilla, un célèbre outil avec une interface graphique pratique.

> **Remarque :** Le Shared Computer Toolkit Azure pour Eclipse prend en charge le déploiement vers les comptes de stockage et les services en nuage, mais ne prend pas en charge le déploiement d’applications web. Vous pouvez déployer des comptes de stockage et les services en nuage à l’aide d’un projet de déploiement d’Azure comme décrit dans [Création d’une Application Hello World pour Azure dans Eclipse](http://msdn.microsoft.com/library/azure/hh690944.aspx), mais pas pour les applications web. Utiliser d’autres méthodes telles que FTP ou GitHub pour transférer des fichiers vers votre application web.

> **Remarque :** Nous ne recommandons pas par FTP à partir de l’invite de commande Windows (ligne de commande FTP.EXE utilitaire fourni avec Windows). Les clients FTP qui utilisent la connexion FTP active, par exemple FTP.EXE, souvent de ne pas fonctionner via des pare-feu. Le trafic FTP actif spécifie une adresse sur le réseau local interne, à laquelle un serveur FTP risquent de ne se connecter.

Pour plus d’informations sur le déploiement d’une application web de Service de l’application à l’aide de FTP, consultez les rubriques suivantes :

- [Déployer à l’aide d’un utilitaire FTP](web-sites-deploy.md)


#### <a name="set-up-deployment-credentials"></a>Configurer les informations d’identification de déploiement

Assurez-vous que vous avez exécuté l’application **AzureWebDemo** pour créer une application web. Vous transférez des fichiers à cet emplacement.

1. Connectez-vous au portail classique et cliquez sur des **Applications Web**. Vérifiez que **WebDemoWebApp** apparaît dans la liste des applications web et assurez-vous qu’il s’exécute. Cliquez sur **WebDemoWebApp** pour ouvrir sa page de **tableau de bord** .

2. Sur la page de **tableau de bord** , sous **Aperçu rapide**, cliquez sur **configurer les informations d’identification de votre déploiement** (si vous disposez déjà des informations d’identification de déploiement, il lit de **réinitialiser vos informations d’identification de déploiement**).

    Informations d’identification de déploiement sont associées à un compte Microsoft. Vous devez spécifier un nom d’utilisateur et le mot de passe que vous pouvez utiliser pour déployer à l’aide de Git et FTP. Vous pouvez utiliser ces informations d’identification pour déployer sur n’importe quelle application web dans tous les abonnements Azure associé à votre compte Microsoft. Fournir des informations d’identification de la déploiement Git et FTP dans la boîte de dialogue et enregistrer le nom d’utilisateur et le mot de passe pour une utilisation ultérieure.


#### <a name="get-ftp-connection-information"></a>Obtenir des informations de connexion FTP

Pour utiliser FTP pour déployer des fichiers d’application à l’application de site web nouvellement créé, vous devez obtenir les informations de connexion. Il existe deux méthodes pour obtenir des informations de connexion. La première consiste à visiter la page de **tableau de bord** de l’application web ; l’autre moyen d’est à télécharger du web app profil de la publication. Le profil de publication est un fichier XML qui fournit des informations telles que les informations d’identification d’ouverture de session et de nom de hôte FTP pour vos applications web dans le Service d’application Azure. Vous pouvez utiliser ce nom d’utilisateur et le mot de passe pour déployer sur n’importe quelle application web dans tous les abonnements associés au compte Azure, pas uniquement le.

Pour obtenir des informations de connexion FTP à partir de lames de l’application web du [Portail Azure][]:

1. Sous **Essentials**, rechercher et copier le **nom d’hôte FTP**. Il s’agit d’un URI semblable à `ftp://waws-prod-bay-NNN.ftp.azurewebsites.windows.net`.

2. Sous **Essentials**, rechercher et copier le **nom d’utilisateur de déploiement/FTP**. Ceci aura le format *webappname\deployment-username*; par exemple `WebDemoWebApp\deployer77`.

Pour obtenir des informations de connexion FTP à partir du profil de la publication :

1. De lames de l’application web, cliquez sur **Get profil de la publication**. Il télécharge un fichier .publishsettings sur votre disque local.

2. Ouvrez le fichier .publishsettings dans un éditeur XML ou un éditeur de texte et recherchez le `<publishProfile>` élément contenant `publishMethod="FTP"`. Il doit se présenter comme suit :

        <publishProfile
            profileName="WebDemoWebApp - FTP"
            publishMethod="FTP"
            publishUrl="ftp://waws-prod-bay-NNN.ftp.azurewebsites.windows.net/site/wwwroot"
            ftpPassiveMode="True"
            userName="WebDemoWebApp\$WebDemoWebApp"
            userPWD="<deployment-password>"
            ...
        </publishProfile>

3. Notez que l’application web `publishProfile` mappage de paramètres pour les paramètres du Gestionnaire de Site FileZilla comme suit :

- `publishUrl`est le même que le **nom d’hôte FTP**, la valeur définie dans **l’hôte**.
- `publishMethod="FTP"`signifie que vous la valeur **protocole** **FTP - protocole de transfert de fichiers**et de **cryptage** à **utiliser le mode FTP normal**.
- `userName`et `userPWD` clés pour le nom d’utilisateur et les valeurs de mot de passe spécifié lorsque vous réinitialisez les informations d’identification de déploiement. `userName`est le même que **déploiement / FTP utilisateur**. Ils correspondent à **l’utilisateur** et le **mot de passe** dans FileZilla.
- `ftpPassiveMode="True"`signifie que le site FTP utilise les transfert FTP passif ; sous l’onglet **Paramètres de transfert** , sélectionnez **passif** .


#### <a name="configure-the-web-app-to-host-a-java-application"></a>Configurer l’application Web pour héberger une application Java

Avant de publier l’application, vous devez modifier certains paramètres de configuration afin que l’application web peut héberger une application Java.

1. Dans le portail classique, accédez à la page de **tableau de bord** de l’application web et cliquez sur **configurer**. Sur la page **configurer** , spécifiez les paramètres suivants.

2. Dans la **version de Java** , la valeur par défaut est **Off**; Sélectionnez la version de Java votre application cible ; par exemple, 1.7.0_51. Après cette opération, assurez-vous également que le **conteneur Web** est définie pour une version de serveur Tomcat.

3. Dans **Les Documents par défaut**, ajoutez index.jsp et déplacez-le vers le haut de la liste. (Le fichier par défaut pour les applications web est hostingstart.html).

4. Cliquez sur **Enregistrer**.


#### <a name="publish-your-application-using-kudu"></a>Publiez votre application à l’aide de Kudu

Une pour publier l’application consiste à utiliser la console de débogage Kudu intégrée dans Azure. Kudu est connue pour être stable et cohérente avec l’application Service Web Apps et serveur Tomcat. Pour accéder à la console de l’application web en accédant à une URL sous la forme suivante :

`https://<webappname>.scm.azurewebsites.net/DebugConsole`

1. Pour cette procédure, la console Kudu se trouve à l’adresse suivante ; Accédez à cet emplacement :

    `https://webdemowebapp.scm.azurewebsites.net/DebugConsole`

2. Dans le menu supérieur, sélectionnez **Console de débogage > CMD**.

3. Dans la ligne de commande de la console, accédez à `/site/wwwroot` (ou cliquez sur `site`, puis `wwwroot` dans l’affichage du répertoire en haut de la page) :

    `cd /site/wwwroot`

4. Une fois que vous spécifiez la **version de Java**, serveur Tomcat doit créer un répertoire d’applications Web. Dans la ligne de commande de la console, accédez au répertoire d’applications Web :

    `mkdir webapps`

    `cd webapps`

5. Faites glisser JSPHello.war de `<project-path>/JSPHello/src/` et déposez-le dans la vue du répertoire Kudu sous `/site/wwwroot/webapps`. Ne faites pas glisser il sur la zone « Faites glisser ici pour charger et le code postal », étant donné que Tomcat le décompressez.

  ![][8]

Au premier JSPHello.war s’affiche dans la zone répertoire par lui-même :

  ![][9]

En peu de temps (probablement moins de 5 minutes) serveur Tomcat va décompresser le fichier WAR dans un répertoire JSPHello décompressé. Cliquez sur le répertoire racine pour voir si index.jsp a été décompressé et y copiés. Si tel est le cas, naviguez vers le répertoire d’applications Web pour voir si le répertoire JSPHello décompressé a été créé. Si vous ne voyez pas ces éléments, patientez, répétez.

  ![][10]


#### <a name="publish-your-application-using-filezilla-optional"></a>Publiez votre application à l’aide de FileZilla (facultatif)

Un autre outil que vous pouvez utiliser pour publier l’application est FileZilla, client FTP tiers connu avec une interface utilisateur graphique pratique. Vous pouvez télécharger et installer FileZilla à partir de [http://filezilla-project.org/](http://filezilla-project.org/) si vous n’en avez pas encore il. Pour plus d’informations sur l’utilisation du client, consultez la [documentation de FileZilla](https://wiki.filezilla-project.org/Documentation) et cette entrée de blog sur [les Clients FTP - partie 4 : FileZilla](http://blogs.msdn.com/b/robert_mcmurray/archive/2008/12/17/ftp-clients-part-4-filezilla.aspx).

1. Dans FileZilla, cliquez sur **fichier > Gestionnaire de Site**.
2. Dans la boîte de dialogue **Gestionnaire de Site** , cliquez sur **Nouveau Site**. Un nouveau site FTP vierge apparaît dans **Sélectionner une entrée** , vous invitant à fournir un nom. Pour cette procédure, le nom `AzureWebDemo-FTP`.

    Sous l’onglet **Général** , spécifiez les paramètres suivants :
    - **Hôte :** Entrez le **Nom d’hôte FTP** que vous avez copié à partir du tableau de bord.
    - **Port :** (Laissez ce vide, qu’il s’agit d’un transfert passif et le serveur déterminera le port à utiliser).
    - **Protocole :** Protocole de transfert de fichiers FTP
    - **Cryptage :** Utiliser le mode FTP normal
    - **Type d’ouverture de session :** Normal
    - **Utilisateur :** Permet d’entrer le déploiement / FTP d’utilisateur que vous avez copié à partir du tableau de bord. C’est le nom d’utilisateur FTP complète, qui a la forme *webappname\username*.
    - **Mot de passe :** Entrez le mot de passe que vous avez spécifié lorsque vous définissez les informations d’identification de déploiement.

    Sous l’onglet **Paramètres de transfert** , sélectionnez **passif**.

3. Cliquez sur **se connecter**. Si elle réussit, console de FileZilla affiche une `Status: Connected` message et problème un `LIST` commande pour répertorier le contenu du répertoire.

4. Dans le panneau site **Local** , sélectionnez le répertoire source dans laquelle se trouve le fichier de JSPHello.war ; le chemin sera semblable au suivant :

    `<project-path>/JSPHello/src/`

5. Dans le panneau site **distant** , sélectionnez le dossier de destination. Vous allez déployer le fichier WAR dans le `webapps` répertoire sous la racine de l’application web. Accédez à `/site/wwwroot`, avec le bouton droit sur `wwwroot`, puis sélectionnez **créer le répertoire**. Nommez le répertoire `webapps` et entrez ce répertoire.

6. Transfert de JSPHello.war à `/site/wwwroot/webapps`. Sélectionnez JSPHello.war dans la liste de fichier **Local** , avec le bouton droit dessus et sélectionnez **charger**. Vous devez voir s’affichent dans `/site/wwwroot/webapps`.

7. Une fois que vous copiez JSPHello.war vers le répertoire d’applications Web, serveur Tomcat décompressera automatiquement (décompresser) les fichiers dans le fichier WAR. Bien que Tomcat Server commence la décompression presque immédiatement, il peut prendre un long délai (éventuellement heures) pour les fichiers apparaissent dans le client FTP.


#### <a name="run-the-hello-world-application-on-the-web-app"></a>Exécutez l’application Hello World sur l’application Web

1. Après avoir téléchargé le fichier WAR et vérifié que le serveur Tomcat a créé une décompression `JSPHello` directory, accédez à `http://webdemowebapp.azurewebsites.net/JSPHello` pour exécuter l’application.

    > **Remarque :** Si vous cliquez sur **Parcourir** à partir du portail classique, vous pouvez obtenir la page Web par défaut, indiquant que « cette application web de Java de la base a été créée. » Vous devrez peut-être actualiser la page Web pour afficher la sortie de l’application au lieu de la page Web par défaut.

2. Lorsque l’application s’exécute, vous devez voir une page web contenant le résultat suivant :

    `Hello World, the time is Tue Mar 24 23:21:10 GMT 2015`


#### <a name="clean-up-azure-resources"></a>Nettoyer les ressources Azure

Cette procédure crée une application web de Service de l’application. Vous serez facturé pour la ressource dans la mesure où il existe. Sauf si vous envisagez de continuer à utiliser l’application web de test ou de développement, vous devez envisager l’arrêt ou la suppression. Une application web qui a été arrêtée est toujours facturés petit, mais vous pouvez le redémarrer à tout moment. Suppression d’une application web efface toutes les données que vous avez téléchargé vers elle.

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

  [1]: ./media/java-create-azure-website-using-java-sdk/eclipse-maven-repositories-rebuild-index.png
  [2]: ./media/java-create-azure-website-using-java-sdk/eclipse-new-java-class.png
  [3]: ./media/java-create-azure-website-using-java-sdk/eclipse-new-dynamic-web-project.png
  [4]: ./media/java-create-azure-website-using-java-sdk/eclipse-java-build-path.png
  [5]: ./media/java-create-azure-website-using-java-sdk/eclipse-targeted-runtimes-tomcat-server.png
  [6]: ./media/java-create-azure-website-using-java-sdk/eclipse-targeted-runtimes-properties-page.png
  [7]: ./media/java-create-azure-website-using-java-sdk/eclipse-run-on-server.png
  [8]: ./media/java-create-azure-website-using-java-sdk/kudu-console-drag-drop.png
  [9]: ./media/java-create-azure-website-using-java-sdk/kudu-console-jsphello-war-1.png
  [10]: ./media/java-create-azure-website-using-java-sdk/kudu-console-jsphello-war-2.png
 

[Service d’application Azure]: http://go.microsoft.com/fwlink/?LinkId=529714
[Web Platform Installer]: http://go.microsoft.com/fwlink/?LinkID=252838
[Azure Shared Computer Toolkit pour Éclipse]: https://msdn.microsoft.com/library/azure/hh690946.aspx
[Azure portal classique]: https://manage.windowsazure.com
[Ce qu’est un répertoire AD Azure]: http://technet.microsoft.com/library/jj573650.aspx
[Créer et télécharger un certificat de gestion pour Azure]: ../cloud-services/cloud-services-certs-create.md
[Outil de gestion de certificat (keytool) et de clé]: http://docs.oracle.com/javase/6/docs/technotes/tools/windows/keytool.html
[WebSiteManagementClient]: http://azure.github.io/azure-sdk-for-java/com/microsoft/azure/management/websites/WebSiteManagementClient.html
[WebSpaceNames]: http://dl.windowsazure.com/javadoc/com/microsoft/windowsazure/management/websites/models/WebSpaceNames.html
[Azure Portal]: https://portal.azure.com
