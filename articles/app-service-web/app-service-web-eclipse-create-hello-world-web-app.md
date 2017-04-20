<properties 
    pageTitle="Créer une application de Web de Hello World pour Azure dans Eclipse | Microsoft Azure" 
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
    ms.date="08/26/2016" 
    ms.author="robmcm"/>

# <a name="create-a-hello-world-web-app-for-azure-in-eclipse"></a>Créer une application de Web de Hello World pour Azure dans Éclipse

Ce didacticiel explique comment créer et déployer une application Hello World de base vers Azure sous la forme d’une application Web à l’aide de la [Shared Computer Toolkit Azure pour Eclipse]. Un exemple simple de JSP est affiché par souci de simplicité, mais une procédure très similaire serait appropriée pour un servlet Java, comme déploiement d’Azure est concerné.

Lorsque vous avez terminé ce didacticiel, votre application sera semblable à l’illustration suivante lorsque vous l’affichez dans un navigateur web :

![Aperçu de Hello World app][01]
 
## <a name="prerequisites"></a>Conditions préalables

* Un Java Developer Kit (JDK), v 1,8 ou une version ultérieure.
* Eclipse IDE pour les développeurs Java EE, Luna ou version ultérieure. Cela peut être téléchargé à partir de <http://www.eclipse.org/downloads/>.
* Une distribution d’un serveur web basé sur Java ou d’un serveur d’applications, tels que Apache Tomcat ou jetée.
* Un abonnement Azure, qui peut être acquis à partir de <https://azure.microsoft.com/free/> ou <http://azure.microsoft.com/pricing/purchase-options/>.
* Le Shared Computer Toolkit pour Eclipse Azure. Pour plus d’informations, consultez [installer le Shared Computer Toolkit Azure pour Eclipse].

## <a name="to-create-a-hello-world-application"></a>Pour créer une Application du monde Hello

Tout d’abord, nous commencerons à la création d’un projet Java.

1. Démarrer Eclipse et dans le menu, cliquez sur **fichier**, cliquez sur **Nouveau**, puis cliquez sur **Projet de Web dynamique**. (Si vous ne voyez pas **Dynamique Web projet** répertorié comme un projet disponible après avoir cliqué sur **fichier** et **nouvelle**, puis effectuez les opérations suivantes : cliquez sur **fichier**, cliquez sur **Nouveau**, cliquez sur le **projet...**, développer **Web**, cliquez sur **Le projet Web dynamique**et cliquez sur **suivant**.)

1. Pour les besoins de ce didacticiel, le nom du projet **MyWebApp**. L’écran s’affiche semblable à la suivante :

    ![Création d’un projet Web dynamique][02]

1. Cliquez sur **Terminer**.

1. Dans l’affichage de l’Explorateur de projet de Eclipse, développez **MyWebApp**. Droit de **contenu Web**, cliquez sur **Nouveau**, puis cliquez sur **Fichier JSP**.

1. Dans la boîte de dialogue **Nouveau fichier JSP** , le nom du fichier **index.jsp**, conserver le dossier parent en tant que **Contenu MyWebApp/Web**, puis cliquez sur **suivant**.

1. Dans la boîte de dialogue **Sélectionner un modèle JSP** , pour les besoins de ce didacticiel, sélectionnez de **Nouveau fichier JSP (html)**et puis cliquez sur **Terminer**.

1. Lorsque votre fichier index.jsp s’ouvre dans Eclipse, ajouter dans le texte à afficher de façon dynamique **Hello World !** dans les fichiers `<body>` élément. Votre mise à jour `<body>` contenu doit ressembler à l’exemple suivant :

    `<body><b><% out.println("Hello World!"); %></b></body>` 

1. Enregistrer les index.jsp.

## <a name="to-deploy-your-application-to-an-azure-web-app-container"></a>Pour déployer votre Application dans un conteneur d’application Web Azure

Il existe plusieurs moyens par lesquels vous pouvez déployer une application web de Java vers Azure. Ce didacticiel décrit l’une des plus simples : votre application sera déployée à un conteneur d’application Azure Web - aucun type de projet particulier, ni des outils supplémentaires ne sont nécessaires. Le JDK et le logiciel du conteneur web fournira vous Azure, il est inutile de télécharger votre propre ; Il vous suffit de votre application Web de Java. Par conséquent, le processus de publication de votre application prendra secondes, pas les minutes.

1. Dans l’Explorateur de projet de Eclipse, droit **MyWebApp**.

1. Dans le menu contextuel, sélectionnez **Azure**, puis cliquez sur **Publier en tant que Azure Web App...**

    ![Publier comme Azure Web App][03]
   
    Sinon, votre projet d’application web est sélectionnée dans l’Explorateur de projets, vous pouvez cliquez sur le bouton de liste déroulante **Publier** dans la barre d’outils et sélectionnez **Publier en tant que Azure Web App** à partir de là :
   
    ![Publier comme Azure Web App][14]
   
1. Si vous n’êtes pas déjà connecté dans Azure d’Eclipse, vous devrez vous connecter à votre compte Azure :

    ![Azure boîte de dialogue Connexion][04]
   
    Si vous disposez de plusieurs comptes d’Azure, certaines des invites pendant le processus de connexion peuvent figurer plusieurs fois, même s’ils semblent être les mêmes. Dans ce cas, continuez à suivre les instructions de connexion.

1. Après que vous être connecté à votre compte Azure, la boîte de dialogue **Gérer les abonnements** affiche la liste des abonnements qui sont associées à vos informations d’identification. S’il existe plusieurs abonnements répertoriés et que vous souhaitez utiliser uniquement un sous-ensemble spécifique de ces, vous pouvez éventuellement désactiver ceux que vous ne souhaitez pas utiliser. Lorsque vous avez sélectionné vos abonnements, cliquez sur **Fermer**.

    ![Gérer la boîte de dialogue abonnements][05]
   
1. Lorsque la boîte de dialogue **déployer pour conteneur d’application Azure Web** s’affiche, elle affiche tous les conteneurs d’application Web que vous avez créé précédemment ; Si vous n’avez pas créé tous les conteneurs, la liste sera vide.

    ![Déployer dans la boîte de dialogue Azure Web application conteneur][06]
   
1. Si vous n’avez pas créé un conteneur d’application Web Azure avant, ou si vous souhaitez publier votre application vers un nouveau conteneur, utilisez la procédure suivante. Dans le cas contraire, sélectionnez un conteneur d’application Web existant et passez à l’étape 7 ci-dessous.

    1. Cliquez sur **nouveau...**

        ![Déployer dans la boîte de dialogue Azure Web application conteneur][15]

    1. La boîte de dialogue **Nouveau conteneur d’application Web** s’affiche :

        ![Boîte de dialogue nouveau conteneur d’application Web][07a]

    1. Entrez un **nom DNS** pour le conteneur de votre application Web ; Ceci va former le nom DNS de niveau feuille de l’URL de l’hôte de votre application web dans Azure. (Notez que le nom doit être disponible et sont conformes aux exigences en matière d’attribution de noms de Azure Web App).

    1. Dans le menu déroulant du **Conteneur Web** , sélectionnez le logiciel approprié pour votre application.

        Actuellement, vous pouvez choisir de Tomcat 8, Tomcat 7 ou 9 de la jetée. Une distribution récente du logiciel sélectionné sera fournie par Azure, et il s’exécutera sur une distribution récente de JDK 8 créé par Oracle et fourni par Azure.

    1. Dans le menu déroulant **d’abonnement** , sélectionnez l’abonnement que vous souhaitez utiliser pour ce déploiement.

    1. Dans le menu déroulant **Groupe de ressources** , sélectionnez le groupe de ressources que vous souhaitez associer votre application Web. (Les groupes de Ressources azure vous autorise à regrouper les ressources connexes afin que, par exemple, ils peuvent être supprimés ensemble.)

        Vous pouvez sélectionner un groupe de ressources existant (si vous en avez un) et le passer à l’étape g ci-dessous ou utilisez ce qui suit ces étapes pour créer un nouveau groupe de ressources :

        * Cliquez sur **nouveau...**

        * La boîte de dialogue **Nouveau groupe de ressources** s’affiche :

            ![Boîte de dialogue Nouveau groupe de ressources][08]

        * Dans la la zone de texte **nom** , spécifiez un nom pour votre nouveau groupe de ressources.

        * Dans la la **zone** déroulante, sélectionnez emplacement pour votre groupe de ressources du centre de données Azure appropriées.

        * FACULTATIF : Par défaut, une distribution récente du Java 8 sera déployée par Azure automatiquement à votre conteneur d’application web en tant que votre machine virtuelle Java. Toutefois, vous pouvez spécifier une version différente et la distribution de la machine virtuelle Java si votre application Web requiert. Pour spécifier le JDK pour votre application Web, cliquez sur l’onglet **JDK** et sélectionnez une des options suivantes :

            * **Déployer le JDK offerte par Azure Web Apps service par défaut**: cette option va déployer une distribution récente du Java 8.

            * **Déployer un 3ème partie JDK disponible sur Azure**: cette option vous permet de choisir à partir de la liste des kits JDK qui sont fournis par Microsoft Azure.

            * **Déployer mon propre JDK à partir de cet emplacement de téléchargement**: cette option vous permet de spécifier votre propre distribution JDK, qui doit être empaquetée sous forme d’un fichier ZIP et téléchargée à un emplacement de téléchargement disponibles au public ou à un compte de stockage Azure pour lequel vous avez accès.

            ![Boîte de dialogue nouveau conteneur d’application Web][07b]

    * Cliquez sur **OK**.

    1. La liste déroulante **Plan de Service d’application** répertorie les plans de service d’application qui sont associés au groupe de ressources que vous avez sélectionné. (Les Plans de Service d’application spécifier des informations telles que l’emplacement de votre application Web, le niveau de tarification et de la taille d’instance de calcul. Un Plan de Service application unique peut être utilisé pour plusieurs applications Web, c’est pourquoi il est gérée séparément à partir d’un déploiement d’application Web spécifique.)

        Vous pouvez sélectionner un existant App Plan de Service (si vous en avez un) et passer à l’étape h ci-dessous ou utilisez ce qui suit ces étapes pour créer un nouveau Plan de Service App :

        * Cliquez sur **nouveau...**

        * La boîte de dialogue **Plan de nouvelle application de Service** s’affiche :

            ![Boîte de dialogue Nouveau Plan de Service d’application][09]

        * Dans la la zone de texte **nom** , spécifiez un nom pour votre nouveau Plan de Service App.

        * Dans le le menu déroulant **emplacement** , sélectionnez emplacement du plan du centre de données Azure appropriées.

        * Dans la la liste déroulante **Niveau de prix** , sélectionnez la tarification approprié pour le plan. Aux fins de test, vous pouvez choisir **libre**.

        * Dans le le menu déroulant **Taille d’Instance** , sélectionnez la taille de l’instance appropriée pour le plan. Aux fins de test, vous pouvez choisir **petit**.

    1. Une fois que vous avez terminé toutes les étapes ci-dessus, la boîte de dialogue nouveau conteneur d’application Web doit ressembler à l’illustration suivante :

        ![Boîte de dialogue nouveau conteneur d’application Web][10]

    1. Cliquez sur **OK** pour terminer la création de votre nouveau conteneur Web App.

        Patientez quelques secondes pour obtenir la liste des conteneurs Web App pour être actualisé et votre conteneur d’application web de nouvellement créée doit maintenant être sélectionné dans la liste.

1. Vous êtes maintenant prêt à effectuer le déploiement initial de votre application Web pour Azure :

    ![Déployer dans la boîte de dialogue Azure Web application conteneur][11]

    Cliquez sur **OK** pour déployer votre application Java au conteneur sélectionné Web App.

    Par défaut, votre application sera déployée en tant que sous-répertoire du serveur d’application. Si vous le souhaitez pour être déployé en tant que l’application racine, cochez la case de **déployer à la racine** avant de cliquer sur **OK**.

1. Ensuite, vous devez voir l’affichage du **Journal d’activité Azure** , qui indique l’état du déploiement de votre application Web.

    ![Journal d’activité Azure][12]

    Le processus de déploiement de votre application Web vers Azure doit prendre que quelques secondes. Lorsque votre prêt de l’application, vous verrez un lien nommé **publié** dans la colonne **état** . Lorsque vous cliquez sur le lien, il vous prendra à la page d’accueil de votre application Web déployée.

## <a name="updating-your-web-app"></a>Mise à jour de votre application Web

Mise à jour d’une exécution d’Azure Web application existante est un processus simple et rapide, et que vous disposez de deux options de mise à jour :

* Vous pouvez mettre à jour le déploiement d’une application Web de Java existant.
* Vous pouvez publier une application Java supplémentaire sur le même conteneur d’application Web.

Dans les deux cas, le processus est identique et ne prend que quelques secondes :

1. Dans l’Explorateur de projet Eclipse, cliquez droit sur l’application Java que vous souhaitez mettre à jour ou à ajouter à un conteneur d’application Web existant.

1. Lorsque le menu contextuel s’affiche, cliquez sur **Azure** , puis les **Publier en tant que Azure Web App...**

1. Dans la mesure où vous êtes déjà connecté précédemment, vous verrez une liste de vos conteneurs d’application Web existants. Sélectionnez celui que vous souhaitez publier ou republier votre application Java et cliquez sur **OK**.

Quelques secondes plus tard, l’affichage du **Journal d’activité Azure** affiche votre déploiement mis à jour comme étant **publiée** et vous serez en mesure de vérifier votre application mis à jour dans un navigateur web.

## <a name="stopping-an-existing-web-app"></a>Arrêt d’une application Web existante

Pour arrêter un Azure Web application conteneur existant, (qu’il contient, y compris toutes les applications Java déployées), vous pouvez utiliser le mode **Explorateur d’Azure** .

Si le mode **Explorateur d’Azure** n’est pas déjà ouvert, vous pouvez ouvrir en cliquant sur le menu **fenêtre** dans Eclipse, puis **Afficher la vue**, **autres...**, cliquez sur puis sur **Azure**puis puis cliquez sur **Explorateur d’Azure**. Si vous n’êtes pas déjà connecté, il vous invite à le faire.

Lorsque l’affichage **Explorateur d’Azure** , utilisation, procédez comme suit pour arrêter votre application Web : 

1. Développez le nœud **d’Azure** .

1. Développez le nœud **Applications Web** . 

1. Cliquez droit sur l’application Web de votre choix.

1. Lorsque le menu contextuel s’affiche, cliquez sur **Arrêter**.

    ![Arrêt d’une application Web existante][13]

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les boîtes à outils Azure pour IDE de Java, consultez les liens suivants :

- [Azure Shared Computer Toolkit pour Éclipse]
  - [L’installation de la Shared Computer Toolkit Azure pour Éclipse]
  - *Créer une application de Web de Hello World pour Azure dans Eclipse (Cet Article)*
  - [Quelles sont les nouveautés dans la Azure Shared Computer Toolkit pour Éclipse]
- [Azure Shared Computer Toolkit pour IntelliJ]
  - [L’installation de la Shared Computer Toolkit Azure pour IntelliJ]
  - [Créer une application de Web de Hello World pour Azure dans IntelliJ]
  - [Nouveautés de la Shared Computer Toolkit Azure pour IntelliJ]

Pour plus d’informations sur l’utilisation d’Azure avec Java, consultez le [Centre pour développeurs Java Azure].

Pour plus d’informations sur la création d’applications Web de Azure, consultez la [Vue d’ensemble des applications Web].

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[Azure Shared Computer Toolkit pour Éclipse]: ../azure-toolkit-for-eclipse.md
[Azure Shared Computer Toolkit pour IntelliJ]: ../azure-toolkit-for-intellij.md
[Create a Hello World Web App for Azure in Eclipse]: ./app-service-web-eclipse-create-hello-world-web-app.md
[Créer une application de Web de Hello World pour Azure dans IntelliJ]: ./app-service-web-intellij-create-hello-world-web-app.md
[L’installation de la Shared Computer Toolkit Azure pour Éclipse]: ../azure-toolkit-for-eclipse-installation.md
[L’installation de la Shared Computer Toolkit Azure pour IntelliJ]: ../azure-toolkit-for-intellij-installation.md
[Quelles sont les nouveautés dans la Azure Shared Computer Toolkit pour Éclipse]: ../azure-toolkit-for-eclipse-whats-new.md
[Nouveautés de la Shared Computer Toolkit Azure pour IntelliJ]: ../azure-toolkit-for-intellij-whats-new.md

[Centre de développement Java Azure]: https://azure.microsoft.com/develop/java/
[Vue d’ensemble des applications Web]: ./app-service-web-overview.md

<!-- IMG List -->

[01]: ./media/app-service-web-eclipse-create-hello-world-web-app/01-Web-Page.png
[02]: ./media/app-service-web-eclipse-create-hello-world-web-app/02-Dynamic-Web-Project.png
[03]: ./media/app-service-web-eclipse-create-hello-world-web-app/03-Context-Menu.png
[04]: ./media/app-service-web-eclipse-create-hello-world-web-app/04-Log-In-Dialog.png
[05]: ./media/app-service-web-eclipse-create-hello-world-web-app/05-Manage-Subscriptions-Dialog.png
[06]: ./media/app-service-web-eclipse-create-hello-world-web-app/06-Deploy-To-Azure-Web-Container.png
[07a]: ./media/app-service-web-eclipse-create-hello-world-web-app/07a-New-Web-App-Container-Dialog.png
[07b]: ./media/app-service-web-eclipse-create-hello-world-web-app/07b-New-Web-App-Container-Dialog.png
[08]: ./media/app-service-web-eclipse-create-hello-world-web-app/08-New-Resource-Group-Dialog.png
[09]: ./media/app-service-web-eclipse-create-hello-world-web-app/09-New-Service-Plan-Dialog.png
[10]: ./media/app-service-web-eclipse-create-hello-world-web-app/10-Completed-Web-App-Container-Dialog.png
[11]: ./media/app-service-web-eclipse-create-hello-world-web-app/11-Completed-Deploy-Dialog.png
[12]: ./media/app-service-web-eclipse-create-hello-world-web-app/12-Activity-Log-View.png
[13]: ./media/app-service-web-eclipse-create-hello-world-web-app/13-Azure-Explorer-Web-App.png
[14]: ./media/app-service-web-eclipse-create-hello-world-web-app/14-publishDropdownButton.png
[15]: ./media/app-service-web-eclipse-create-hello-world-web-app/15-New-Azure-Web-Container.png
