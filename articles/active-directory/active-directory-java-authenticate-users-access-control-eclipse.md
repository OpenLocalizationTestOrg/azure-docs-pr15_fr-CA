<properties
    pageTitle="Comment utiliser le contrôle d’accès (Java) | Microsoft Azure"
    description="Découvrez comment développer et utiliser le contrôle d’accès avec Java dans Azure."
    services="active-directory" 
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor="" />

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016" 
    ms.author="robmcm" />

# <a name="how-to-authenticate-web-users-with-azure-access-control-service-using-eclipse"></a>Comment faire pour authentifier les utilisateurs Web avec le Service de contrôle d’accès de Azure utilisant Eclipse

Ce guide vous explique comment utiliser le service Azure Access Control Service (ACS) au sein de la Shared Computer Toolkit Azure pour Eclipse. Pour plus d’informations sur ACS, consultez la section [étapes suivantes](#next_steps) .

> [AZURE.NOTE]
> Le filtre de contrôle Azure Access Services est un aperçu de la technologie de communauté. En tant que version préliminaire du logiciel, il n'est pas officiellement pris en charge par Microsoft.

## <a name="what-is-acs"></a>Nouveautés d’ACS ?

La plupart des développeurs ne sont pas des experts de l’identité et ne voulez généralement pas que de consacrer des mécanismes d’authentification et d’autorisation développement temps pour leurs applications et services. ACS est un service Azure offre un moyen simple d’authentification des utilisateurs qui ont besoin d’accéder à vos applications et services web sans devoir la logique complexe de l’authentification dans votre code.

Les fonctionnalités suivantes sont disponibles dans ACS :

-   Intégration avec Windows Identity Foundation (WIF).
-   Prise en charge pour les fournisseurs d’identité web les plus courants (IPs), y compris de Windows Live ID, Google, Yahoo! et Facebook.
-   Prise en charge des Services de fédération Active Directory (Active Directory Federation Services) 2.0.
-   Un Open Data Protocol (OData)-en fonction du service de gestion qui fournit l’accès par programme aux paramètres de l’ACS.
-   Un portail de gestion qui permet l’accès administratif aux paramètres ACS.

Pour plus d’informations sur ACS, consultez [Service de contrôle de Access 2.0][].

## <a name="concepts"></a>Concepts

Azure ACS est basé sur les principes de l’identité basée sur les revendications, une approche cohérente pour la création de mécanismes d’authentification pour les applications en cours d’exécution sur site ou dans le nuage. Identité basée sur les revendications offre un moyen commun d’applications et de services obtenir les informations d’identité que dont ils ont besoin sur les utilisateurs au sein de leur organisation, d’autres organisations et sur Internet.

Pour exécuter les tâches présentées dans ce guide, vous devez comprendre les concepts suivants :

**Client** - dans le cadre de ce manuel, il s’agit d’un navigateur qui tente d’accéder à votre application web.

**Applications Relying (RP)** - application d’un RP est un site Web ou un service qui externalise l’authentification auprès d’une autorité externe. Dans le jargon d’identité, nous dire que le RP fait confiance à cette autorité. Ce guide explique comment configurer votre application pour approbation ACS.

**Jeton** - un jeton est une collection de données de sécurité sont généralement émises lors de l’authentification d’un utilisateur. Il contient un ensemble de *revendications*, les attributs de l’utilisateur authentifié. Une demande de remboursement peut représenter un nom d’utilisateur, un identificateur pour un rôle d’un utilisateur appartient à l’âge d’un utilisateur et ainsi de suite. Un jeton est généralement numériquement signé, ce qui signifie qu’il peut toujours trouver sa source à son émetteur et son contenu ne peut pas être falsifié. Un utilisateur accède à une application de RP en présentant un jeton valide émis par une autorité de confiance de l’application du RP.

**Fournisseur d’identité (IP)** - IP est une autorité qui authentifie l’identité de l’utilisateur et émet des jetons de sécurité. Le travail réel de l’émission de jetons est mis en oeuvre si un service spécial appelé Service de jeton de sécurité (STS). Exemples d’adresses IP incluent de Windows Live ID, Facebook, business utilisateur référentiels (comme Active Directory) et ainsi de suite.
Lorsque ACS est configuré pour faire confiance à une adresse IP, le système accepte et valide des jetons émis par cette IP. ACS peut approuver plusieurs adresses IP à la fois, qui signifie que lorsque votre application approuve l’ACS, vous pouvez instantanément offrir votre application à tous les utilisateurs authentifiés à partir de toutes les adresses IP que ACS approuve en votre nom.

**Fournisseur de fédération (FP)** - IPs connaissent des utilisateurs et s’authentifier à l’aide de leurs informations d’identification et émettre les revendications sur ce qu’ils savent à leur sujet. Un fournisseur de fédération (FP) est un autre type d’autorité : au lieu de l’authentification des utilisateurs directement, il joue le rôle d’authentification intermédiaire et des courtiers entre un RP et un ou plusieurs IPs. Les fonctions IPs et IPS émettent des jetons de sécurité, donc ils utilisent tous deux Services de jeton de sécurité (STS). ACS est un FP.

**Moteur de règles ACS** - la logique utilisée pour transformer des jetons entrantes à partir des adresses IP autorisées à jetons destinés à être consommé par le RP est codifié sous forme de règles de transformation des revendications simple. ACS comporte un moteur de règles qui prend en charge de l’application de la logique de transformation que vous avez spécifié pour votre RP.

**ACS Namespace** - un espace de noms est une partition de niveau supérieur de l’ACS que vous utilisez pour organiser vos paramètres. Un espace de noms conserve une liste d’adresses IP de confiance, les applications de RP vous souhaitez servir, moteur pour traiter les jetons entrants avec les règles que vous attendez de la règle et ainsi de suite. Un espace de noms expose les différents points de terminaison qui seront utilisés par l’application et le développeur pour obtenir l’ACS pour effectuer sa fonction.

La figure suivante illustre le fonctionne de l’authentification ACS avec une application web :

![Diagramme de circuit d’ACS][acs_flow]

1.  Le client (dans ce cas un navigateur) demande une page provenant du RP.
2.  Étant donné que la demande n’est pas encore le RP authentifiées, redirige l’utilisateur pour qu’il approuve l’autorité, qui est l’ACS. ACS présente à l’utilisateur le choix des adresses IP qui ont été spécifiés pour ce RP. L’utilisateur sélectionne l’adresse IP approprié.
3.  Le client accède à la page d’authentification de la période d’enquête et invite l’utilisateur à ouvrir une session.
4.  Une fois que le client est authentifié (par exemple, l’identité figurent des informations d’identification), la période d’enquête émet un jeton de sécurité.
5.  Après l’émission d’un jeton de sécurité, l’IP redirige le client vers l’ACS et le client envoie le jeton de sécurité émis par l’adresse IP de l’ACS.
6.  ACS valide le jeton de sécurité émis par la période d’enquête, entrées l’identité prétend dans ce jeton dans le moteur de règles ACS, calcule les revendications d’identité de sortie et émet un nouveau jeton de sécurité qui contient ces déclarations de sortie.
7.  ACS redirige le client vers le RP. Le client envoie le nouveau jeton de sécurité émis par l’ACS au RP. Le RP valide la signature du jeton de sécurité émis par ACS, valide les revendications contenues dans ce jeton et renvoie la page qui a été demandée à l’origine.

## <a name="prerequisites"></a>Conditions préalables

Pour exécuter les tâches présentées dans ce guide, vous devrez les éléments suivants :

- Un Java Developer Kit (JDK), v 1.6 ou ultérieure.
- Eclipse IDE pour les développeurs Java EE, Indigo ou une version ultérieure. Cela peut être téléchargé à partir de <http://www.eclipse.org/downloads/>. 
- Une distribution d’un serveur web basé sur Java ou d’un serveur d’applications, tels que Apache Tomcat, GlassFish, serveur d’applications JBoss ou jetée.
- un abonnement Azure, qui peut être acquis à partir de <http://www.microsoft.com/windowsazure/offers/>.
- Mise à jour avril 2014 le Shared Computer Toolkit Azure pour Eclipse, ou une version ultérieure. Pour plus d’informations, consultez [installer le Shared Computer Toolkit Azure pour Eclipse](http://msdn.microsoft.com/library/windowsazure/hh690946.aspx).
- Un certificat X.509 à utiliser avec votre application. Vous aurez besoin de ce certificat dans certificat public (.cer) et échange d’informations personnelles (. Format PFX). (Options pour la création de ce certificat seront décrite plus loin dans ce didacticiel).
- Connaissance de Azure compute émulateur et les techniques de déploiement présentées à la [Création d’une Application Hello World pour Azure dans Eclipse](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx).

## <a name="create-an-acs-namespace"></a>Créer un Namespace ACS

Pour utiliser le Service de contrôle d’accès (ACS) dans Azure, vous devez créer un espace de noms ACS. L’espace de noms fournit une portée unique pour l’adressage des ressources ACS à partir de votre application.

1. Connectez-vous au [portail de gestion Azure][].
2. Cliquez sur **Active Directory**. 
3. Pour créer un nouvel espace de noms du contrôle d’accès, cliquez sur **Nouveau**et cliquez sur **Services d’application**, cliquez sur **Contrôle d’accès**, puis cliquez sur **Création rapide**. 
4. Entrez un nom pour l’espace de noms. Azure vérifie que le nom est unique.
5. Sélectionnez la zone dans laquelle l’espace de noms est utilisé. Pour des performances optimales, utilisez la zone dans laquelle vous déployez votre application.
6. Si vous avez plus d’un abonnement, sélectionnez l’abonnement que vous souhaitez utiliser pour l’espace de noms ACS.
7. Cliquez sur **créer**.

Azure crée et Active l’espace de noms. Attendez que l’état de l’espace de noms est **actif** avant de continuer. 

## <a name="add-identity-providers"></a>Ajouter des fournisseurs d’identité

Dans cette tâche, vous ajoutez des adresses IP à utiliser avec votre application RP pour l’authentification. Cette tâche montre comment ajouter des Windows Live sous la forme d’une adresse IP à des fins de démonstration, mais vous pouvez utiliser une des adresses IP répertoriée dans le portail de gestion ACS.


1.  Dans le [Portail de gestion Azure][], cliquez sur **Active Directory**, sélectionnez un espace de noms du contrôle d’accès, puis cliquez sur **Gérer**. Le portail de gestion ACS s’ouvre.
2.  Dans le volet de navigation gauche du portail de gestion d’ACS, cliquez sur **fournisseurs d’identité**.
3.  De Windows Live ID est activé par défaut et ne peut pas être supprimé. Pour les besoins de ce didacticiel, seul Windows Live ID est utilisé. Cet écran est toutefois, où vous pouvez ajouter les autres IPs, en cliquant sur le bouton **Ajouter** .

De Windows Live ID est maintenant activé sous la forme d’une adresse IP pour votre espace de noms ACS. Ensuite, vous spécifiez votre application web de Java (pour être créés plus tard) sous la forme d’un RP.

## <a name="add-a-relying-party-application"></a>Ajouter une application de tiers de confiance

Dans cette tâche, vous configurez ACS pour reconnaître votre application web de Java sous la forme d’une application de RP valide.

1.  Dans le portail de gestion ACS, cliquez sur **applications tierces de Relying**.
2.  Dans la page **Applications de tiers de confiance** , cliquez sur **Ajouter**.
3.  Sur la page **Application ajouter du tiers de confiance** , effectuez le des opérations suivantes :
    1.  Dans la zone **nom**, tapez le nom du RP. Pour les besoins de ce didacticiel, tapez **Azure Web App**.
    2.  Dans **Mode**, sélectionnez **entrer les paramètres manuellement**.
    3.  Dans **domaine**, tapez l’URI auquel s’applique le jeton de sécurité émis par l’ACS. Pour ce faire, tapez **http://localhost : 8080 /**.
        ![Domaine de partie utilisatrice pour une utilisation dans l’émulateur de calcul][relying_party_realm_emulator]
    4.  Dans **l’URL de renvoi,** tapez l’URL dans laquelle ACS retourne le jeton de sécurité. Pour ce faire, tapez **http://localhost:8080/MyACSHelloWorld/index.jsp**
        ![Relying partie renvoyer l’URL à utiliser dans l’émulateur de calcul][relying_party_return_url_emulator]
    5.  Acceptez les valeurs par défaut dans le reste des champs.

4.  Cliquez sur **Enregistrer**.

Vous avez correctement configuré votre application de web Java lorsqu’il est exécuté dans l’émulateur de calcul Azure (à http://localhost : 8080 /) pour être un RP dans votre espace de noms ACS. Ensuite, créez les règles ACS utilise pour traiter les demandes pour le RP.

## <a name="create-rules"></a>Créer des règles

Dans cette tâche, vous définissez les règles qui définissent comment les demandes sont passées à partir d’IPs vers le RP. Dans ce guide, nous allons simplement configurer ACS pour copier les valeurs et les types de revendications d’entrée directement dans le jeton de la sortie, sans filtrage ni les modifier.

1.  Dans la page principale du portail de gestion d’ACS, cliquez sur **groupes de règles**.
2.  Dans la page **Groupes de règles** , cliquez sur le **Groupe de règles par défaut pour Azure Web App**.
3.  Dans la page **Modifier le groupe de règles** , cliquez sur **Générer**.
4.  Sur le **Générer des règles : groupe de règles par défaut pour Azure Web App** page et assurez-vous de Windows Live ID est activée, puis cliquez sur **Générer**.    
5.  Dans la page **Modifier le groupe de règles** , cliquez sur **Enregistrer**.

## <a name="upload-a-certificate-to-your-acs-namespace"></a>Télécharger un certificat à votre espace de noms ACS

Dans cette tâche, vous téléchargez une. Certificat PFX qui sera utilisé pour signer des demandes de jetons créés par votre espace de noms ACS.

1.  Sur la page principale du portail de gestion d’ACS, cliquez sur **certificats et des clés**.
2.  Dans la page **certificats et clés** , cliquez sur **Ajouter** au-dessus de la **Signature des jetons**.
3.  Sur la page **Ajouter un jeton de signature de certificat ou la clé** :
    1. Dans la section **utilisé (s) pour** , cliquez sur **Compter les applications de tiers** et sélectionnez **Azure Web App** (ce que vous définissez précédemment sous le nom de votre application de tiers de confiance).
    2. Dans la section **Type** , sélectionnez le **Certificat X.509**.
    3. Dans la section **certificats** , cliquez sur le bouton Parcourir et naviguez vers le fichier de certificat X.509 que vous souhaitez utiliser. Il s’agit d’un. Fichiers PFX. Sélectionnez le fichier et cliquez sur **Ouvrir**, puis entrez le mot de passe de certificat dans la zone de texte **mot de passe** . Notez que pour des fins de test, vous pouvez utiliser un automatique-signed-certificat. Pour créer un certificat auto-signé, utilisez le bouton **Nouveau** dans la boîte de dialogue **Bibliothèque de filtres ACS** (décrite plus loin), ou utilisez l’utilitaire **encutil.exe** à partir du [site Web du projet][] du Starter Kit Azure pour Java.
    4. Assurez-vous que **Rendre principal** est activé. Votre page **Ajouter un jeton de signature de certificat ou la clé** doit être semblable à la suivante.
        ![Ajouter le certificat de signature de jetons][add_token_signing_cert]
    5. Cliquez sur **Enregistrer** pour enregistrer vos paramètres et fermer la page **Ajouter un jeton de signature de certificat ou de clé** .

Ensuite, examinez les informations dans la page de l’intégration des applications et copier l’URI dont vous aurez besoin pour configurer votre application web de Java à utiliser ACS.

## <a name="review-the-application-integration-page"></a>Examinez la page de l’intégration des applications

Vous trouverez toutes les informations et le code nécessaires à la configuration de votre application web de Java (l’application de RP) pour travailler avec ACS sur la page d’intégration d’applications du portail de gestion d’ACS. Ces informations seront nécessaires lors de la configuration de votre application web de Java pour l’authentification fédérée.

1.  Dans le portail de gestion ACS, cliquez sur **intégration de l’Application**.  
2.  Dans la page de **l’Intégration de l’Application** , cliquez sur **Pages de connexion**.
3.  Dans la page de **Connexion Page intégration** , cliquez sur **Azure Web App**.

Dans la **intégration de Page de connexion : Azure Web App** page, l’URL indiquée dans **Option 1 : lien vers une page de connexion de hébergé par l’ACS** sera utilisé dans votre application web de Java. Vous aurez besoin de cette valeur lorsque vous ajoutez la bibliothèque de filtre de Services Azure Access contrôle à votre application de Java.

## <a name="create-a-java-web-application"></a>Créer une application web de Java
1. Dans Eclipse, dans le menu, cliquez sur **fichier**, cliquez sur **Nouveau**et puis que vous cliquez sur **Le projet Web dynamique**. (Si vous ne voyez pas **Les projets Web dynamique** répertorié comme un projet disponible après avoir cliqué sur **fichier**, **Nouveau**, puis effectuez les opérations suivantes : cliquez sur **le fichier**, cliquez sur **Nouveau**, cliquez sur **projet**, développer **Web**, cliquez sur **Le projet Web dynamique**et cliquez sur **suivant**.) Pour les besoins de ce didacticiel, nommez le projet **MyACSHelloWorld**. (Assurez-vous que vous utilisez ce nom, les étapes suivantes de ce didacticiel attendent votre fichier WAR d’être nommé MyACSHelloWorld). L’écran s’affiche semblable à la suivante :

    ![Créer un projet de Hello World pour ACS exampple][create_acs_hello_world]

    Cliquez sur **Terminer**.
2. Dans l’affichage de l’Explorateur de projet de Eclipse, développez **MyACSHelloWorld**. Droit de **contenu Web**, cliquez sur **Nouveau**, puis cliquez sur **Fichier JSP**.
3. Dans la boîte de dialogue **Nouveau fichier JSP** , nommez fichier **index.jsp**. Conserver le dossier parent en tant que MyACSHelloWorld/ContenuWeb, comme illustré dans le code suivant :

    ![Ajouter un fichier JSP par exemple de l’ACS][add_jsp_file_acs]

    Cliquez sur **suivant**.

4. Dans la boîte de dialogue **Sélectionner un modèle JSP** , sélectionnez de **Nouveau fichier JSP (html)** et cliquez sur **Terminer**.
5. Ajouter texte à afficher lorsque le fichier index.jsp s’ouvre dans Eclipse, **ACS Bonjour !** dans les fichiers `<body>` élément. Votre mise à jour `<body>` contenu doit apparaître comme suit :

        <body>
          <b><% out.println("Hello ACS World!"); %></b>
        </body>
    
    Enregistrer les index.jsp.
  
## <a name="add-the-acs-filter-library-to-your-application"></a>Ajouter la bibliothèque ACS filtre à votre application

1. Dans l’Explorateur de projet de Eclipse, droit **MyACSHelloWorld**, cliquez sur **Générer le chemin d’accès**, puis cliquez sur **Configurer un chemin de Build**.
2. Dans la boîte de dialogue **Chemin d’accès de la génération de Java** , cliquez sur l’onglet **bibliothèques** .
3. Cliquez sur **Ajouter une bibliothèque**.
4. Cliquez sur **Filtre de Services Azure Access contrôle (en MS Tech ouvert)** , puis sur **suivant**. La boîte de dialogue **Filtre de Services de contrôle de l’accès Azure** s’affiche.  (Le champ **emplacement** peut avoir un chemin différent, selon où vous avez installé Eclipse, et le numéro de version peut être différent, en fonction des mises à jour logicielles).

    ![Ajouter une bibliothèque de filtre de l’ACS][add_acs_filter_lib]

5. À l’aide d’un navigateur ouvert à la page de **Connexion Page intégration** du portail de gestion, copiez l’URL figurant dans le **Option 1 : lien vers une page de connexion de hébergé par l’ACS** champ et le coller dans le champ **Point de terminaison ACS d’authentification** de la boîte de dialogue Eclipse.
6. À l’aide d’un navigateur ouvert à la page **Modifier une Application tiers compter** du portail de gestion, copiez l’URL figurant dans le champ **domaine** et collez-le dans le champ **Domaine de partie s’appuyer** de la boîte de dialogue Eclipse.
7. Dans la section **sécurité** de la boîte de dialogue Eclipse, si vous souhaitez utiliser un certificat existant, cliquez sur **Parcourir**, naviguez jusqu’au certificat que vous souhaitez utiliser, sélectionnez-le et cliquez sur **Ouvrir**. Ou bien, si vous souhaitez créer un nouveau certificat, cliquez sur **Nouveau** pour afficher la boîte de dialogue **Nouveau certificat** , puis spécifiez le mot de passe, le nom du fichier .cer et nom du fichier .pfx pour le nouveau certificat.
8. Vérifiez les **incorporer le certificat dans le fichier WAR**. L’incorporation du certificat de cette manière, il inclut dans votre déploiement sans avoir à ajouter manuellement en tant que composant. (Si à la place vous devez stocker votre certificat en externe à partir de votre fichier WAR, vous ajouter le certificat comme un composant du rôle, décochez la case **incorporer le certificat dans le fichier WAR**.)
9. [Facultatif] De conserver **les connexions Require HTTPS** vérifiée. Si vous définissez cette option, vous devez accéder à votre application à l’aide du protocole HTTPS. Si vous ne souhaitez pas requièrent des connexions HTTPS, désactivez cette option.
10. Pour un déploiement à l’émulateur de calcul, vos paramètres de **Filtre de ACS Azure** seront similaire à la suivante.

    ![Paramètres de filtre de l’ACS Azure pour un déploiement à l’émulateur de calcul][add_acs_filter_lib_emulator]

11. Cliquez sur **Terminer**.
12. Cliquez sur **Oui** lorsqu’il reçoit avec une boîte de dialogue indiquant qu’un fichier web.xml va être créé.
13. Cliquez sur **OK** pour fermer la boîte de dialogue **Chemin d’accès de la génération de Java** .

## <a name="deploy-to-the-compute-emulator"></a>Déployer sur l’émulateur de calcul

1. Dans l’Explorateur de projet de Eclipse, droit **MyACSHelloWorld**, cliquez sur **Azure**, puis cliquez sur **Package pour Azure**.
2. **Nom du projet**, tapez **MyAzureACSProject** et cliquez sur **suivant**.
3. Sélectionnez un JDK et serveur d’application. (Ces étapes sont couvertes en détail dans le didacticiel de [Création d’une Application Hello World pour Azure dans Eclipse](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx) ).
4. Cliquez sur **Terminer**.
5. Cliquez sur le bouton **exécuter dans Azure émulateur** .
6. Après le démarrage de votre application web de Java dans l’émulateur de calcul, fermez toutes les instances de votre navigateur (de sorte que les sessions de navigateur en cours n’interfèrent pas avec votre test de connexion ACS).
7. Exécutez votre application en ouvrant <http://localhost :> 8080/MyACSHelloWorld/dans votre navigateur ( <> ou/https://localhost:8080/MyACSHelloWorld/si vous avez activé **les connexions Require HTTPS**). Vous devez fournir un nom d’accès de Windows Live ID, puis vous convient à l’URL de renvoi spécifiée pour votre application de tiers de confiance.
99.  Lorsque vous avez terminé votre application d’affichage, cliquez sur le bouton **Réinitialiser un émulateur Azure** .

## <a name="deploy-to-azure"></a>Déployer vers Azure

Pour déployer sur Azure, vous devrez modifier le domaine de tiers de confiance et l’URL de renvoi de votre espace de noms ACS.

1. Dans le portail de gestion Azure, dans la page **Application modifier du tiers de confiance** , de modifier le **domaine** pour l’URL de votre site déployé. Remplacez **l’exemple** avec le nom DNS que vous avez spécifié pour votre déploiement.

    ![Domaine de partie utilisatrice pour utilisation dans la production][relying_party_realm_production]

2. Modifier l' **URL de renvoi** pour l’URL de votre application. Remplacez **l’exemple** avec le nom DNS que vous avez spécifié pour votre déploiement.

    ![URL de renvoi partie utilisatrice pour utilisation dans la production][relying_party_return_url_production]

3. Cliquez sur **Enregistrer** pour enregistrer votre domaine de partie de réponse mis à jour et renvoyer les modifications de l’URL.
4. Conservez la page de **Connexion Page intégration** ouvert dans votre navigateur, vous devrez copier à partir de celui-ci, peu de temps.
5. Dans l’Explorateur de projet de Eclipse, droit **MyACSHelloWorld**, cliquez sur **Générer le chemin d’accès**, puis cliquez sur **Configurer un chemin de Build**.
6. Cliquez sur l’onglet **bibliothèques** et cliquez sur **Filtre de Services Azure Access contrôle**, puis cliquez sur **Modifier**.
7. À l’aide d’un navigateur ouvert à la page de **Connexion Page intégration** du portail de gestion, copiez l’URL figurant dans le **Option 1 : lien vers une page de connexion de hébergé par l’ACS** champ et le coller dans le champ **Point de terminaison ACS d’authentification** de la boîte de dialogue Eclipse.
8. À l’aide d’un navigateur ouvert à la page **Modifier une Application tiers compter** du portail de gestion, copiez l’URL figurant dans le champ **domaine** et collez-le dans le champ **Domaine de partie s’appuyer** de la boîte de dialogue Eclipse.
9. Dans la section **sécurité** de la boîte de dialogue Eclipse, si vous souhaitez utiliser un certificat existant, cliquez sur **Parcourir**, naviguez jusqu’au certificat que vous souhaitez utiliser, sélectionnez-le et cliquez sur **Ouvrir**. Ou bien, si vous souhaitez créer un nouveau certificat, cliquez sur **Nouveau** pour afficher la boîte de dialogue **Nouveau certificat** , puis spécifiez le mot de passe, le nom du fichier .cer et nom du fichier .pfx pour le nouveau certificat.
10. Conserver **le certificat dans le fichier WAR d’incorporer** activée, supposons que vous souhaitez incorporer le certificat dans le fichier WAR.
11. [Facultatif] De conserver **les connexions Require HTTPS** vérifiée. Si vous définissez cette option, vous devez accéder à votre application à l’aide du protocole HTTPS. Si vous ne souhaitez pas requièrent des connexions HTTPS, désactivez cette option.
12. Pour un déploiement d’Azure, vos paramètres de filtre de ACS Azure seront similaire à la suivante.

    ![Paramètres de filtre de l’ACS Azure pour un déploiement en production][add_acs_filter_lib_production]

13. Cliquez sur **Terminer** pour fermer la boîte de dialogue **Modifier la bibliothèque** .
14. Cliquez sur **OK** pour fermer la boîte de dialogue **Propriétés de MyACSHelloWorld** .
15. Dans Eclipse, cliquez sur le bouton **Publier vers Azure Cloud** . Répondez aux invites, semblables, comme dans la section **pour déployer votre application dans Azure** de la rubrique [Création d’une Application Hello World pour Azure dans Eclipse](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx) . 

Une fois que votre application web a été déployée, fermer les sessions de navigateur ouvert, exécuter votre application web et vous êtes invité à signer à l’aide d’informations d’identification de Windows Live ID, suivies d’être envoyé à l’URL de renvoi de votre application de tiers de confiance.

Lorsque vous avez terminé votre application Hello World ACS, n’oubliez pas de supprimer le déploiement (vous pouvez apprendre à supprimer un déploiement dans la rubrique [Création d’une Application Hello World pour Azure dans Eclipse](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx) ).


## <a name="next_steps"></a>Étapes suivantes

Pour un examen de la balise langage SAML (Security Assertion) renvoyé par ACS à votre application, consultez [comment afficher SAML retourné par le Service de contrôle d’accès Azure][]. Autres Explorer les fonctionnalités d’ACS et pour tester des scénarios plus sophistiqués, voir [Service de contrôle de Access 2.0][].

En outre, cet exemple utilisé l’option **incorporer le certificat dans le fichier WAR** . Cette option rend facile à déployer le certificat. Si vous souhaitez plutôt que de séparer votre certificat de signature à partir de votre fichier WAR, vous pouvez utiliser la technique suivante :

1. Dans la section **sécurité** de la boîte de dialogue **Filtre de Services de contrôle de l’accès Azure** , tapez **${env. JAVA_HOME}/myCert.cer** et désactivez la case **incorporer le certificat dans le fichier WAR**. (Ajuster mycert.cer si le nom de votre fichier de certificat est différent). Cliquez sur **Terminer** pour fermer la boîte de dialogue.
2. Copiez le certificat en tant que composant dans votre déploiement : l’Explorateur dans Eclipse de projet, développez **MyAzureACSProject**, cliquez sur **WorkerRole1**, cliquez sur **Propriétés**, **Rôle d’Azure**et cliquez sur **composants**.
3. Cliquez sur **Ajouter**.
4. Dans la boîte de dialogue **Ajouter un composant** :
    1. Dans la section **Importer** :
        1. Utilisez **le bouton** pour naviguer vers le certificat que vous souhaitez utiliser. 
        2. Pour la **méthode**, sélectionnez **Copier**.
    2. **Sous nom**, cliquez sur la zone de texte et acceptez le nom par défaut.
    3. Dans la section **déployer** :
        1. Pour la **méthode**, sélectionnez **Copier**.
        2. **Dans répertoire**, tapez **JAVA_HOME %**.
    4. La boîte de dialogue **Ajouter un composant** doit être similaire à la suivante.

        ![Ajouter le composant de certificat][add_cert_component]

    5. Cliquez sur **OK**.

À ce stade, votre certificat est inclus dans votre déploiement. Notez que lors du incorporer le certificat dans le fichier de guerre ou de l’ajouter en tant que composant à votre déploiement, vous devez télécharger le certificat à votre espace de noms comme décrit dans la section [télécharger un certificat à votre espace de noms ACS][] .

[What is ACS?]: #what-is
[Concepts]: #concepts
[Prerequisites]: #pre
[Create a Java web application]: #create-java-app
[Create an ACS Namespace]: #create-namespace
[Add Identity Providers]: #add-IP
[Add a Relying Party Application]: #add-RP
[Create Rules]: #create-rules
[Télécharger un certificat à votre espace de noms ACS]: #upload-certificate
[Review the Application Integration Page]: #review-app-int
[Configure Trust between ACS and Your ASP.NET Web Application]: #config-trust
[Add the ACS Filter library to your application]: #add_acs_filter_library
[Deploy to the compute emulator]: #deploy_compute_emulator
[Deploy to Azure]: #deploy_azure
[Next steps]: #next_steps
[site Web de projet]: http://wastarterkit4java.codeplex.com/releases/view/61026
[Comment afficher SAML retourné par le Service de contrôle d’accès Azure]: /en-us/develop/java/how-to-guides/view-saml-returned-by-acs/
[Service de contrôle d’accès 2.0]: http://go.microsoft.com/fwlink/?LinkID=212360
[Windows Identity Foundation]: http://www.microsoft.com/download/en/details.aspx?id=17331
[Windows Identity Foundation SDK]: http://www.microsoft.com/download/en/details.aspx?id=4451
[Portail de gestion Azure]: https://manage.windowsazure.com
[acs_flow]: ./media/active-directory-java-authenticate-users-access-control-eclipse/ACSFlow.png

<!-- Eclipse-specific -->
[add_acs_filter_lib]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddACSFilterLibrary.png
[add_acs_filter_lib_emulator]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddACSFilterLibraryEmulator.png
[add_acs_filter_lib_production]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddACSFilterLibraryProduction.png

[relying_party_realm_emulator]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyRealmEmulator.png
[relying_party_return_url_emulator]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyReturnURLEmulator.png
[relying_party_realm_production]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyRealmProduction.png
[relying_party_return_url_production]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyReturnURLProduction.png
[add_cert_component]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddCertificateComponent.png
[add_jsp_file_acs]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddJSPFileACS.png
[create_acs_hello_world]: ./media/active-directory-java-authenticate-users-access-control-eclipse/CreateACSHelloWorld.png
[add_token_signing_cert]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddTokenSigningCertificate.png
 
