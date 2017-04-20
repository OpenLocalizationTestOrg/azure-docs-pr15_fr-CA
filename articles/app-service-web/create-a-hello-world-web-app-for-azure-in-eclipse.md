<properties 
    pageTitle="Créer une application de Web de Hello World pour Azure dans Éclipse" 
    description="Ce didacticiel vous montre comment utiliser le Shared Computer Toolkit Azure pour Eclipse pour créer une application Web Hello World pour Azure." 
    services="app-service\web" 
    documentationCenter="java" 
    authors="rmcmurray" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

# <a name="create-a-hello-world-web-app-for-azure-in-eclipse"></a>Créer une application de Web de Hello World pour Azure dans Éclipse

Ce didacticiel explique comment créer et déployer une application Hello World de base vers Azure sous la forme d’une application Web à l’aide de la [Shared Computer Toolkit Azure pour Eclipse]. Un exemple simple de JSP est affiché par souci de simplicité, mais une procédure très similaire serait appropriée pour un servlet Java, comme déploiement d’Azure est concerné.

Lorsque vous avez terminé ce didacticiel, votre application sera semblable à l’illustration suivante lorsque vous l’affichez dans un navigateur web :

![][01]
 
## <a name="prerequisites"></a>Conditions préalables

* Un Java Developer Kit (JDK), v 1.7 ou version ultérieure.
* Eclipse IDE pour les développeurs Java EE, Indigo ou une version ultérieure. Cela peut être téléchargé à partir de <http://www.eclipse.org/downloads/>.
* Une distribution d’un serveur web basé sur Java ou d’un serveur d’applications, tels que Apache Tomcat ou jetée.
* Un abonnement Azure, qui peut être acquis à partir de <https://azure.microsoft.com/en-us/free/> ou <http://azure.microsoft.com/pricing/purchase-options/>.
* Le Shared Computer Toolkit pour Eclipse Azure. Pour plus d’informations, consultez [installer le Shared Computer Toolkit Azure pour Eclipse].

## <a name="to-create-a-hello-world-application"></a>Pour créer une Application du monde Hello

Tout d’abord, nous commencerons à la création d’un projet Java.

1. Démarrer Eclipse et dans le menu, cliquez sur **fichier**, cliquez sur **Nouveau**, puis cliquez sur **Projet de Web dynamique**. (Si vous ne voyez pas **Dynamique Web projet** répertorié comme un projet disponible après avoir cliqué sur **fichier** et **nouvelle**, puis effectuez les opérations suivantes : cliquez sur **fichier**, cliquez sur **Nouveau**, cliquez sur le **projet...**, développer **Web**, cliquez sur **Le projet Web dynamique**et cliquez sur **suivant**.)

1. Pour les besoins de ce didacticiel, nommez le projet **MyHelloWorld**. L’écran s’affiche semblable à la suivante :

    ![][02]

1. Cliquez sur **Terminer**.

1. Dans l’affichage de l’Explorateur de projet de Eclipse, développez **MyHelloWorld**. Droit de **contenu Web**, cliquez sur **Nouveau**, puis cliquez sur **Fichier JSP**.

1. Dans la boîte de dialogue **Nouveau fichier JSP** , nommez fichier **index.jsp**. Conserver le dossier parent en tant que **MyHelloWorld/ContenuWeb**.

1. Dans la boîte de dialogue **Sélectionner un modèle JSP** , pour les besoins de ce didacticiel, sélectionnez de **Nouveau fichier JSP (html)**et puis cliquez sur **Terminer**.

1. Lorsque votre fichier index.jsp s’ouvre dans Eclipse, ajouter dans le texte à afficher de façon dynamique **Hello World !** dans les fichiers `<body>` élément. Votre mise à jour `<body>` contenu doit ressembler à l’exemple suivant :

    `<body><b><% out.println("Hello World!"); %></b></body>` 

1. Enregistrer les index.jsp.

## <a name="to-deploy-your-application-to-an-azure-web-app-container"></a>Pour déployer votre Application dans un conteneur d’application Web Azure

Il existe plusieurs moyens par lesquels vous pouvez déployer une application web de Java vers Azure. Ce didacticiel décrit l’une des plus simples : votre application sera déployée à un conteneur d’application Azure Web - aucun type de projet particulier, ni des outils supplémentaires ne sont nécessaires. Le JDK et le logiciel du conteneur web fournira vous Azure, il est inutile de télécharger votre propre ; Il vous suffit de votre application Web de Java. Par conséquent, le processus de publication de votre application prendra secondes, pas les minutes.

1. Dans l’Explorateur de projet de Eclipse, cliquez droit sur **MyHelloWorld**.

1. Dans le menu contextuel, sélectionnez **Azure**, puis cliquez sur **Publier en tant que Azure Web App...**

    ![][03]
   
    Sinon, votre projet d’application web est sélectionnée dans l’Explorateur de projets, vous pouvez cliquez sur le bouton de liste déroulante **Publier** dans la barre d’outils et sélectionnez **Publier en tant que Azure Web App** à partir de là :
   
    ![][14]
   
1. Si vous n’êtes pas déjà connecté dans Azure d’Eclipse, vous devrez vous connecter à votre compte Azure :

    ![][04]
   
    Remarque : Si vous avez plusieurs comptes d’Azure, certaines des invites pendant le processus de connexion peuvent figurer plusieurs fois, même s’ils semblent être les mêmes. Dans ce cas, continuez à suivre les instructions de connexion.
1. Après que vous être connecté à votre compte Azure, la boîte de dialogue **Gérer les abonnements** affiche la liste des abonnements qui sont associées à vos informations d’identification. S’il existe plusieurs abonnements répertoriés et que vous souhaitez utiliser uniquement un sous-ensemble spécifique de ces, vous pouvez éventuellement désactiver ceux que vous ne souhaitez pas utiliser. Lorsque vous avez sélectionné vos abonnements, cliquez sur **Fermer**.

    ![][05]
   
1. Lorsque la boîte de dialogue **déployer pour conteneur d’application Azure Web** s’affiche, elle affiche tous les conteneurs d’application Web que vous avez créé précédemment ; Si vous n’avez pas créé tous les conteneurs, la liste sera vide.

    ![][06]
   
1. Si vous n’avez pas créé un conteneur d’application Web Azure avant, ou si vous souhaitez publier votre application vers un nouveau conteneur, utilisez la procédure suivante. Dans le cas contraire, sélectionnez un conteneur d’application Web existant et passez à l’étape 7 ci-dessous.

    1. Cliquez sur **nouveau...**

    1. La boîte de dialogue **Nouveau conteneur d’application Web** s’affiche :

        ![][07]

    1. Entrez un **nom DNS** pour le conteneur de votre application Web ; Ceci va former le nom DNS de niveau feuille de l’URL de l’hôte de votre application web dans Azure. Remarque : Le nom doit être disponible et sont conformes aux exigences en matière d’attribution de noms de Azure Web App.

    1. Dans le menu déroulant du **Conteneur Web** , sélectionnez le logiciel approprié pour votre application.

        Actuellement, vous pouvez choisir de Tomcat 8, Tomcat 7 ou 9 de la jetée. Une distribution récente du logiciel sélectionné sera fournie par Azure, et il s’exécutera sur une distribution récente de JDK 8 créé par Oracle et fourni par Azure.

    1. Dans le menu déroulant **d’abonnement** , sélectionnez l’abonnement que vous souhaitez utiliser pour ce déploiement.

    1. Dans le menu déroulant **Groupe de ressources** , sélectionnez le groupe de ressources que vous souhaitez associer votre application Web.

        Remarque : Azure des groupes de ressources permettent de regrouper les ressources connexes afin que, par exemple, ils peuvent être supprimés ensemble.

        Vous pouvez sélectionner un groupe de ressources existant (si vous en avez un) et le passer à l’étape g ci-dessous ou utilisez ce qui suit ces étapes pour créer un nouveau groupe de ressources :

        * Cliquez sur **nouveau...**

        * La boîte de dialogue **Nouveau groupe de ressources** s’affiche :

            ![][08]

        * Dans la la zone de texte **nom** , spécifiez un nom pour votre nouveau groupe de ressources.

        * Dans la la **zone** déroulante, sélectionnez emplacement pour votre groupe de ressources du centre de données Azure appropriées.

        * Cliquez sur **OK**.

    1. La liste déroulante **Plan de Service d’application** répertorie les plans de service d’application qui sont associés au groupe de ressources que vous avez sélectionné.

        Remarque : Un Plan de Service d’application spécifie des informations telles que l’emplacement de votre application Web, le niveau de tarification et de la taille d’instance de calcul. Un Plan de Service application unique peut être utilisé pour plusieurs applications Web, c’est pourquoi il est gérée séparément à partir d’un déploiement d’application Web spécifique.

        Vous pouvez sélectionner un existant App Plan de Service (si vous en avez un) et passer à l’étape h ci-dessous ou utilisez ce qui suit ces étapes pour créer un nouveau Plan de Service App :

        * Cliquez sur **nouveau...**

        * La boîte de dialogue **Plan de nouvelle application de Service** s’affiche :

            ![][09]

        * Dans la la zone de texte **nom** , spécifiez un nom pour votre nouveau Plan de Service App.

        * Dans le le menu déroulant **emplacement** , sélectionnez emplacement du plan du centre de données Azure appropriées.

        * Dans la la liste déroulante **Niveau de prix** , sélectionnez la tarification approprié pour le plan. Aux fins de test, vous pouvez choisir **libre**.

        * Dans le le menu déroulant **Taille d’Instance** , sélectionnez la taille de l’instance appropriée pour le plan. Aux fins de test, vous pouvez choisir **petit**.

    1. Une fois que vous avez terminé toutes les étapes ci-dessus, la boîte de dialogue nouveau conteneur d’application Web doit ressembler à l’illustration suivante :

        ![][10]

    1. Cliquez sur **OK** pour terminer la création de votre nouveau conteneur Web App.

        Patientez quelques secondes pour obtenir la liste des conteneurs Web App pour être actualisé et votre conteneur d’application web de nouvellement créée doit maintenant être sélectionné dans la liste.

1. Vous êtes maintenant prêt à effectuer le déploiement initial de votre application Web pour Azure :

    ![][11]

    Cliquez sur **OK** pour déployer votre application Java au conteneur sélectionné Web App.

    Remarque : Par défaut, votre application est déployée en tant que sous-répertoire du serveur d’application. Si vous le souhaitez pour être déployé en tant que l’application racine, cochez la case de **déployer à la racine** avant de cliquer sur **OK**.

1. Ensuite, vous devez voir l’affichage du **Journal d’activité Azure** , qui indique l’état du déploiement de votre application Web.

    ![][12]

    Le processus de déploiement de votre application Web vers Azure doit prendre que quelques secondes. Lorsque votre prêt de l’application, vous verrez un lien nommé **publié** dans la colonne **état** . Lorsque vous cliquez sur le lien, il vous prendra à la page d’accueil de votre application Web déployée.

## <a name="updating-your-web-app"></a>Mise à jour de votre application Web

Mise à jour d’une exécution d’Azure Web application existante est un processus simple et rapide, et que vous disposez de deux options de mise à jour :

* Vous pouvez mettre à jour le déploiement d’une application Web de Java existant.
* Vous pouvez publier une application Java supplémentaire sur le même conteneur d’application Web.

Dans les deux cas, le processus est identique et ne prend que quelques secondes :

1. Dans l’Explorateur de projet Eclipse, cliquez droit sur l’application Java que vous souhaitez mettre à jour ou à ajouter à un conteneur d’application Web existant.

2. Lorsque le menu contextuel s’affiche, cliquez sur **Azure** , puis les **Publier en tant que Azure Web App...**

3. Dans la mesure où vous êtes déjà connecté précédemment, vous verrez une liste de vos conteneurs d’application Web existants. Sélectionnez celui que vous souhaitez publier ou republier votre application Java et cliquez sur **OK**.

Quelques secondes plus tard, l’affichage du **Journal d’activité Azure** affiche votre déploiement mis à jour comme étant **publiée** et vous serez en mesure de vérifier votre application mis à jour dans un navigateur web.

## <a name="stopping-an-existing-web-app"></a>Arrêt d’une application Web existante

Pour arrêter un Azure Web application conteneur existant, (qu’il contient, y compris toutes les applications Java déployées), vous pouvez utiliser le mode **Explorateur d’Azure** .

Si le mode **Explorateur d’Azure** n’est pas déjà ouvert, vous pouvez ouvrir en cliquant sur le menu **fenêtre** dans Eclipse, puis **Afficher la vue**, **autres...**, cliquez sur puis sur **Azure**puis puis cliquez sur **Explorateur d’Azure**. Si vous n’êtes pas déjà connecté, il vous invite à le faire.

Lorsque l’affichage **Explorateur d’Azure** , utilisation, procédez comme suit pour arrêter votre application Web : 

1. Développez le nœud **d’Azure** .

1. Développez le nœud **Applications Web** . 

1. Cliquez droit sur l’application Web de votre choix.

1. Lorsque le menu contextuel s’affiche, cliquez sur **Arrêter**.

    ![][13]

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les liens suivants :

* [Centre de développement Java](/develop/java/).
* [Vue d’ensemble des applications Web](app-service-web-overview.md)

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[Azure App Service]: http://go.microsoft.com/fwlink/?LinkId=529714
[Azure Shared Computer Toolkit pour Éclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[L’installation de la Shared Computer Toolkit Azure pour Éclipse]: http://go.microsoft.com/fwlink/?LinkId=699546

<!-- IMG List -->

[01]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/01-Web-Page.png
[02]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/02-Dynamic-Web-Project.png
[03]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/03-Context-Menu.png
[04]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/04-Log-In-Dialog.png
[05]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/05-Manage-Subscriptions-Dialog.png
[06]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/06-Deploy-To-Azure-Web-Container.png
[07]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/07-New-Web-App-Container-Dialog.png
[08]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/08-New-Resource-Group-Dialog.png
[09]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/09-New-Service-Plan-Dialog.png
[10]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/10-Completed-Web-App-Container-Dialog.png
[11]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/11-Completed-Deploy-Dialog.png
[12]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/12-Activity-Log-View.png
[13]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/13-Azure-Explorer-Web-App.png
[14]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/publishDropdownButton.png