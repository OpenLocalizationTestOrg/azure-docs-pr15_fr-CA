<properties 
    pageTitle="Créer une application de Web de Hello World pour Azure dans IntelliJ | Microsoft Azure" 
    description="Ce didacticiel vous montre comment utiliser le Shared Computer Toolkit Azure pour IntelliJ pour créer une application Web Hello World pour Azure." 
    services="app-service\web" 
    documentationCenter="java" 
    authors="selvasingh" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="08/11/2016" 
    ms.author="asirveda;robmcm"/>

# <a name="create-a-hello-world-web-app-for-azure-in-intellij"></a>Créer une application de Web de Hello World pour Azure dans IntelliJ

Ce didacticiel explique comment créer et déployer une application Hello World de base vers Azure sous la forme d’une application Web à l’aide de la [Shared Computer Toolkit Azure pour IntelliJ]. Un exemple simple de JSP est affiché par souci de simplicité, mais une procédure très similaire serait appropriée pour un servlet Java, comme déploiement d’Azure est concerné.

Lorsque vous avez terminé ce didacticiel, votre application sera semblable à l’illustration suivante lorsque vous l’affichez dans un navigateur web :

![][01]
 
## <a name="prerequisites"></a>Conditions préalables

* Un Java Developer Kit (JDK), v 1,8 ou une version ultérieure.
* Édition intégrale de l’idée IntelliJ. Cela peut être téléchargé à partir de <https://www.jetbrains.com/idea/download/index.html>.
* Une distribution d’un serveur web basé sur Java ou d’un serveur d’applications, tels que Apache Tomcat ou jetée.
* Un abonnement Azure, qui peut être acquis à partir de <https://azure.microsoft.com/free/> ou <http://azure.microsoft.com/pricing/purchase-options/>.
* Le Shared Computer Toolkit Azure pour IntelliJ. Pour plus d’informations, consultez [installation du Shared Computer Toolkit Azure pour IntelliJ].

## <a name="to-create-a-hello-world-application"></a>Pour créer une Application du monde Hello

Tout d’abord, nous commencerons à la création d’un projet Java.

1. Démarrer IntelliJ et dans le menu, cliquez sur **fichier**, puis sur **Nouveau**, puis cliquez sur **projet**.

   ![][02]

1. Dans la boîte de dialogue Nouveau projet, sélectionnez **Java**, puis **Application Web**et puis cliquez sur **suivant**.

   ![][03a]

   Si vous êtes invité à continuer avec aucun kit de développement logiciel affecté, cliquez sur **Oui**.

   ![][03b]

1. Pour les besoins de ce didacticiel, nommez le projet **Java application Web sur Azure**, puis cliquez sur **Terminer**.

   ![][04]

1. Dans l’affichage de l’Explorateur de projet de IntelliJ, développez **Java application Web sur Azure**, puis **web**et double-cliquez sur **index.jsp**.

   ![][05]

1. Lorsque votre fichier index.jsp s’ouvre dans IntelliJ, ajoutez dans le texte à afficher de façon dynamique **Hello World !** dans les fichiers `<body>` élément. Votre mise à jour `<body>` contenu doit ressembler à l’exemple suivant :

   `<body><b><% out.println("Hello World!"); %></b></body>` 

1. Enregistrer les index.jsp.

## <a name="to-deploy-your-application-to-an-azure-web-app-container"></a>Pour déployer votre Application dans un conteneur d’application Web Azure

Il existe plusieurs moyens par lesquels vous pouvez déployer une application web de Java vers Azure. Ce didacticiel décrit l’une des plus simples : votre application sera déployée à un conteneur d’application Azure Web - aucun type de projet particulier, ni des outils supplémentaires ne sont nécessaires. Le JDK et le logiciel du conteneur web fournira vous Azure, il est inutile de télécharger votre propre ; Il vous suffit de votre application Web de Java. Par conséquent, le processus de publication de votre application prendra secondes, pas les minutes.

1. Dans l’Explorateur de projet de IntelliJ, cliquez droit sur le projet **Java application Web sur Azure** . Lorsque le menu contextuel s’affiche, sélectionnez **Azure**, puis cliquez sur **Publier en tant que Azure Web App...**

   ![][06]

1. Si vous n’êtes pas déjà connecté dans Azure à partir de IntelliJ, vous devrez vous connecter à votre compte Azure :

   ![][07]

   Remarque : Si vous avez plusieurs comptes d’Azure, certaines des invites pendant le processus de connexion peuvent figurer plusieurs fois, même s’ils semblent être les mêmes. Dans ce cas, continuez à suivre les instructions de connexion.

1. Après que vous être connecté à votre compte Azure, la boîte de dialogue **Gérer les abonnements** affiche la liste des abonnements qui sont associées à vos informations d’identification. S’il existe plusieurs abonnements répertoriés et que vous souhaitez utiliser uniquement un sous-ensemble spécifique de ces, vous pouvez éventuellement désactiver ceux que vous ne souhaitez pas utiliser. Lorsque vous avez sélectionné vos abonnements, cliquez sur **Fermer**.

   ![][08]

1. Lorsque la boîte de dialogue **déployer pour conteneur d’application Azure Web** s’affiche, elle affiche tous les conteneurs d’application Web que vous avez créé précédemment ; Si vous n’avez pas créé tous les conteneurs, la liste sera vide.   

   ![][09]

1. Si vous n’avez pas créé un conteneur d’application Web Azure avant, ou si vous souhaitez publier votre application vers un nouveau conteneur, utilisez la procédure suivante. Dans le cas contraire, sélectionnez un conteneur d’application Web existant et passez à l’étape 6 ci-dessous.

  1. Cliquez sur**+**

        ![][10]

  1. La boîte de dialogue **Nouveau conteneur d’application Web** s’affichera qui sera utilisé pour les étapes suivantes.

        ![][11]

  1. Entrez un **nom DNS** pour le conteneur de votre application Web ; Ceci va former le nom DNS de niveau feuille de l’URL de l’hôte de votre application web dans Azure. Remarque : Le nom doit être disponible et sont conformes aux exigences en matière d’attribution de noms de Azure Web App.

  1. Dans le menu déroulant du **Conteneur Web** , sélectionnez le logiciel approprié pour votre application.

        Actuellement, vous pouvez choisir de Tomcat 8, Tomcat 7 ou 9 de la jetée. Une distribution récente du logiciel sélectionné sera fournie par Azure, et il s’exécutera sur une distribution récente de JDK 8 créé par Oracle et fourni par Azure.

  1. Dans le menu déroulant **d’abonnement** , sélectionnez l’abonnement que vous souhaitez utiliser pour ce déploiement.

  1. Dans le menu déroulant **Groupe de ressources** , sélectionnez le groupe de ressources que vous souhaitez associer votre application Web.

        Remarque : Azure des groupes de ressources permettent de regrouper les ressources connexes afin que, par exemple, ils peuvent être supprimés ensemble.

        Vous pouvez sélectionner un groupe de ressources existant (si vous en avez un) et le passer à l’étape g ci-dessous ou utilisez ce qui suit ces étapes pour créer un nouveau groupe de ressources :

      * Cliquez sur **nouveau...**

      * La boîte de dialogue **Nouveau groupe de ressources** s’affiche :

            ![][12]

      * Dans la la zone de texte **nom** , spécifiez un nom pour votre nouveau groupe de ressources.

      * Dans la la **zone** déroulante, sélectionnez emplacement pour votre groupe de ressources du centre de données Azure appropriées.

      * Cliquez sur **OK**.

  1. La liste déroulante **Plan de Service d’application** répertorie les plans de service d’application qui sont associés au groupe de ressources que vous avez sélectionné.

        Remarque : Un Plan de Service d’application spécifie des informations telles que l’emplacement de votre application Web, le niveau de tarification et de la taille d’instance de calcul. Un Plan de Service application unique peut être utilisé pour plusieurs applications Web, c’est pourquoi il est gérée séparément à partir d’un déploiement d’application Web spécifique.

        Vous pouvez sélectionner un existant App Plan de Service (si vous en avez un) et passer à l’étape h ci-dessous ou utilisez ce qui suit ces étapes pour créer un nouveau Plan de Service App :

      * Cliquez sur **nouveau...**

      * La boîte de dialogue **Plan de nouvelle application de Service** s’affiche :

            ![][13]

      * Dans la la zone de texte **nom** , spécifiez un nom pour votre nouveau Plan de Service App.

      * Dans le le menu déroulant **emplacement** , sélectionnez emplacement du plan du centre de données Azure appropriées.

      * Dans la la liste déroulante **Niveau de prix** , sélectionnez la tarification approprié pour le plan. Aux fins de test, vous pouvez choisir **libre**.

      * Dans le le menu déroulant **Taille d’Instance** , sélectionnez la taille de l’instance appropriée pour le plan. Aux fins de test, vous pouvez choisir **petit**.

  1. Une fois que vous avez terminé toutes les étapes ci-dessus, la boîte de dialogue nouveau conteneur d’application Web doit ressembler à l’illustration suivante :

        ![][14]

  1. Cliquez sur **OK** pour terminer la création de votre nouveau conteneur Web App.

        Patientez quelques secondes pour obtenir la liste des conteneurs Web App pour être actualisé et votre conteneur d’application web de nouvellement créée doit maintenant être sélectionné dans la liste.

1. Vous êtes maintenant prêt à lancer le déploiement initial de votre application Web vers Azure ; Cliquez sur **OK** pour déployer votre application Java au conteneur sélectionné Web App.

    ![][15]

    Remarque : Par défaut, votre application est déployée en tant que sous-répertoire du serveur d’application. Si vous le souhaitez pour être déployé en tant que l’application racine, cochez la case de **déployer à la racine** avant de cliquer sur **OK**.

1. Ensuite, vous devez voir l’affichage du **Journal d’activité Azure** , qui indique l’état du déploiement de votre application Web.

    ![][16]

    Le processus de déploiement de votre application Web vers Azure doit prendre que quelques secondes. Lorsque votre prêt de l’application, vous verrez un lien nommé **publié** dans la colonne **état** . Lorsque vous cliquez sur le lien, il vous amènera à la page d’accueil de votre application Web déployée, ou vous pouvez utiliser les étapes dans la section suivante pour accéder à votre application web.

## <a name="browsing-to-your-web-app-on-azure"></a>La navigation à votre application Web sur Azure

Pour accédez à votre application Web sur Azure, vous pouvez utiliser le mode **Explorateur d’Azure** .

Si le mode **Explorateur d’Azure** n’est pas déjà ouvert, vous pouvez ouvrir en cliquant sur le menu **affichage** dans IntelliJ, puis puis cliquez sur **Outil de Windows**, puis cliquez sur **Service Explorateur**. Si vous n’êtes pas déjà connecté, il vous invite à le faire.

Lorsque l’affichage **Explorateur d’Azure** , utilisation, procédez comme suit pour arrêter votre application Web : 

1. Développez le nœud **d’Azure** .

1. Développez le nœud **Applications Web** . 

1. Cliquez droit sur l’application Web de votre choix.

1. Lorsque le menu contextuel s’affiche, cliquez sur **Ouvrir dans le navigateur**.

    ![][17]

## <a name="updating-your-web-app"></a>Mise à jour de votre application Web

Mise à jour d’une exécution d’Azure Web application existante est un processus simple et rapide, et que vous disposez de deux options de mise à jour :

* Vous pouvez mettre à jour le déploiement d’une application Web de Java existant.
* Vous pouvez publier une application Java supplémentaire sur le même conteneur d’application Web.

Dans les deux cas, le processus est identique et ne prend que quelques secondes :

1. Dans l’Explorateur de projet IntelliJ, cliquez droit sur l’application Java que vous souhaitez mettre à jour ou à ajouter à un conteneur d’application Web existant.

1. Lorsque le menu contextuel s’affiche, cliquez sur **Azure** , puis les **Publier en tant que Azure Web App...**

1. Dans la mesure où vous êtes déjà connecté précédemment, vous verrez une liste de vos conteneurs d’application Web existants. Sélectionnez celui que vous souhaitez publier ou republier votre application Java et cliquez sur **OK**.

Quelques secondes plus tard, l’affichage du **Journal d’activité Azure** affiche votre déploiement mis à jour comme étant **publiée** et vous serez en mesure de vérifier votre application mis à jour dans un navigateur web.

## <a name="starting-or-stopping-an-existing-web-app"></a>Démarrage ou arrêt d’une application Web existante

Pour démarrer ou arrêter un conteneur Azure Web App existant, (qu’il contient, y compris toutes les applications Java déployées), vous pouvez utiliser le mode **Explorateur d’Azure** .

Si le mode **Explorateur d’Azure** n’est pas déjà ouvert, vous pouvez ouvrir en cliquant sur le menu **affichage** dans IntelliJ, puis puis cliquez sur **Outil de Windows**, puis cliquez sur **Service Explorateur**. Si vous n’êtes pas déjà connecté, il vous invite à le faire.

Lorsque l’affichage **Explorateur d’Azure** , utilisation, procédez comme suit pour démarrer ou arrêter votre application Web : 

1. Développez le nœud **d’Azure** .

1. Développez le nœud **Applications Web** . 

1. Cliquez droit sur l’application Web de votre choix.

1. Lorsque le menu contextuel s’affiche, cliquez sur **Démarrer** ou **Arrêter**. Notez que les options de menu sont sensibles au contexte, vous pouvez uniquement arrêter une application web en cours d’exécution ou démarrer une application web qui n’est pas en cours d’exécution.

    ![][18]

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les boîtes à outils Azure pour IDE de Java, consultez les liens suivants :

- [Azure Shared Computer Toolkit pour Éclipse]
  - [L’installation de la Shared Computer Toolkit Azure pour Éclipse]
  - [Créer une application de Web de Hello World pour Azure dans Éclipse]
  - [Quelles sont les nouveautés dans la Azure Shared Computer Toolkit pour Éclipse]
- [Azure Shared Computer Toolkit pour IntelliJ]
  - [L’installation de la Shared Computer Toolkit Azure pour IntelliJ]
  - *Créer une application de Web de Hello World pour Azure dans IntelliJ (Cet Article)*
  - [Nouveautés de la Shared Computer Toolkit Azure pour IntelliJ]

Pour plus d’informations sur l’utilisation d’Azure avec Java, consultez le [Centre pour développeurs Java Azure].

Pour plus d’informations sur la création d’applications Web de Azure, consultez la [Vue d’ensemble des applications Web].

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[Azure Shared Computer Toolkit pour Éclipse]: ../azure-toolkit-for-eclipse.md
[Azure Shared Computer Toolkit pour IntelliJ]: ../azure-toolkit-for-intellij.md
[Créer une application de Web de Hello World pour Azure dans Éclipse]: ./app-service-web-eclipse-create-hello-world-web-app.md
[Create a Hello World Web App for Azure in IntelliJ]: ./app-service-web-intellij-create-hello-world-web-app.md
[L’installation de la Shared Computer Toolkit Azure pour Éclipse]: ../azure-toolkit-for-eclipse-installation.md
[L’installation de la Shared Computer Toolkit Azure pour IntelliJ]: ../azure-toolkit-for-intellij-installation.md
[Quelles sont les nouveautés dans la Azure Shared Computer Toolkit pour Éclipse]: ../azure-toolkit-for-eclipse-whats-new.md
[Nouveautés de la Shared Computer Toolkit Azure pour IntelliJ]: ../azure-toolkit-for-intellij-whats-new.md

[Centre de développement Java Azure]: https://azure.microsoft.com/develop/java/
[Vue d’ensemble des applications Web]: ./app-service-web-overview.md

<!-- IMG List -->

[01]: ./media/app-service-web-intellij-create-hello-world-web-app/01-Web-Page.png
[02]: ./media/app-service-web-intellij-create-hello-world-web-app/02-File-New-Project.png
[03a]: ./media/app-service-web-intellij-create-hello-world-web-app/03-New-Project-Dialog.png
[03b]: ./media/app-service-web-intellij-create-hello-world-web-app/03-No-SDK-Specified.png
[04]: ./media/app-service-web-intellij-create-hello-world-web-app/04-New-Project-Dialog.png
[05]: ./media/app-service-web-intellij-create-hello-world-web-app/05-Open-Index-Page.png
[06]: ./media/app-service-web-intellij-create-hello-world-web-app/06-Azure-Publish-Context-Menu.png
[07]: ./media/app-service-web-intellij-create-hello-world-web-app/07-Azure-Log-In-Dialog.png
[08]: ./media/app-service-web-intellij-create-hello-world-web-app/08-Manage-Subscriptions.png
[09]: ./media/app-service-web-intellij-create-hello-world-web-app/09-App-Containers.png
[10]: ./media/app-service-web-intellij-create-hello-world-web-app/10-Add-App-Container.png
[11]: ./media/app-service-web-intellij-create-hello-world-web-app/11-New-App-Container.png
[12]: ./media/app-service-web-intellij-create-hello-world-web-app/12-New-Resource-Group.png
[13]: ./media/app-service-web-intellij-create-hello-world-web-app/13-New-App-Service-Plan.png
[14]: ./media/app-service-web-intellij-create-hello-world-web-app/14-New-App-Container.png
[15]: ./media/app-service-web-intellij-create-hello-world-web-app/15-Deploy-To-Azure.png
[16]: ./media/app-service-web-intellij-create-hello-world-web-app/16-Progress-Indicator.png
[17]: ./media/app-service-web-intellij-create-hello-world-web-app/17-Browse-Web-App.png
[18]: ./media/app-service-web-intellij-create-hello-world-web-app/18-Stop-Web-App.png
