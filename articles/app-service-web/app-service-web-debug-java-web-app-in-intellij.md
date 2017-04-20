<properties 
    pageTitle="Déboguer une application Web de Java sur Azure dans IntelliJ | Microsoft Azure" 
    description="Ce didacticiel vous montre comment utiliser le Shared Computer Toolkit Azure pour IntelliJ pour déboguer une application Web de Java en cours d’exécution sur Azure." 
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
    ms.date="09/20/2016" 
    ms.author="asirveda;robmcm"/>

# <a name="debug-a-java-web-app-on-azure-in-intellij"></a>Déboguer une application Web de Java sur Azure dans IntelliJ

Ce didacticiel explique comment déboguer une application Web de Java en cours d’exécution sur Azure avec [Shared Computer Toolkit Azure pour IntelliJ]. Par souci de simplicité, vous utiliserez un exemple simple de Java Server Page (JSP) pour ce didacticiel, mais la procédure est similaire pour un servlet Java lorsque vous déboguez sur Azure.

Lorsque vous avez terminé ce didacticiel, votre application sera semblable à l’illustration suivante lorsque vous effectuez le débogage dans IntelliJ :

![][01]
 
## <a name="prerequisites"></a>Conditions préalables

* Un Java Developer Kit (JDK), v 1,8 ou une version ultérieure.
* Édition intégrale de l’idée IntelliJ. Cela peut être téléchargé à partir de <https://www.jetbrains.com/idea/download/index.html>.
* Une distribution d’un serveur web basé sur Java ou d’un serveur d’applications, tels que Apache Tomcat ou jetée.
* Un abonnement Azure, qui peut être acquis à partir de <https://azure.microsoft.com/en-us/free/> ou <http://azure.microsoft.com/pricing/purchase-options/>.
* Le Shared Computer Toolkit Azure pour IntelliJ. Pour plus d’informations, consultez [installation du Shared Computer Toolkit Azure pour IntelliJ].
* Un projet Web dynamiques créés et déployés sur Azure Application Service ; par exemple, voir [créer une application Web Hello World pour Azure dans IntelliJ].

## <a name="to-debug-a-java-web-app-on-azure"></a>Pour déboguer une application Web de Java sur Azure

Pour effectuer ces étapes dans cette section, vous pouvez utiliser un projet Web dynamique existant que vous avez déjà déployé sous la forme d’une application Web de Java sur Azure, vous téléchargez un [Exemple de la dynamique du projet Web] et suivez les étapes dans [créer une application Web Hello World pour Azure dans IntelliJ] pour le déployer sur Azure. 

1. Ouvrez le projet d’application Web de Java dont vous avez déployé sur Azure dans IntelliJ.

1. Cliquez sur le menu **exécuter** , puis cliquez sur **Modifier les Configurations de...**.

    ![][02]

1. Lorsque la boîte de dialogue **Exécution/débogage Configurations** s’ouvre : 

    1. Sélectionnez **l’application Web Azure**.
    1. Cliquez sur **+** pour ajouter une nouvelle configuration.
    1. Fournir un **nom** pour la configuration.
    1. Accepter les valeurs par défaut restantes qui sont proposées par le Shared Computer Toolkit d’Azure, puis cliquez sur **OK**.

        ![][03]

1. Sélectionnez la configuration de débogage Azure Web App que vous venez de créer sur la partie supérieure droite du menu et cliquez sur **Debug**

    ![][04]

1. Lorsque vous êtes invité à **Activer le débogage distant dans l’application Web à distance maintenant ?**, cliquez sur **OK**.

1. **votre application web est maintenant prête pour le débogage distant**, cliquez sur **OK**.

    ![][05]

1. Sélectionnez la configuration de débogage Azure Web App que vous venez de créer sur la partie supérieure droite du menu, puis sur **Déboguer**.

1. Invite de commande Windows ou shell Unix va ouvrir et préparer la connexion nécessaire pour le débogage ; Vous devez attendre jusqu'à ce que la connexion à votre application Web de Java à distance est établie avant de continuer. Si vous utilisez Windows, il ressemblera à l’illustration suivante :

    ![][06]

1. Insérer un point d’arrêt dans votre page JSP, puis ouvrez l’URL pour votre application Web de Java dans un navigateur :

    1. Ouvrez **L’Explorateur Azure** dans IntelliJ.
    1. Accédez à des **Applications Web** et l’application de Web Java que vous souhaitez déboguer.
    1. Cliquez avec le bouton droit sur l’application Web, puis cliquez sur **Ouvrir dans le navigateur**.
    1. IntelliJ entrent maintenant en mode débogage.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’utilisation d’Azure avec Java, consultez le [Centre pour développeurs Java Azure].

Pour plus d’informations sur la création d’applications Web de Azure, consultez la [Vue d’ensemble des applications Web].

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[Azure App Service]: http://go.microsoft.com/fwlink/?LinkId=529714
[Azure Shared Computer Toolkit pour IntelliJ]: ../azure-toolkit-for-intellij.md
[L’installation de la Shared Computer Toolkit Azure pour IntelliJ]: ../azure-toolkit-for-intellij-installation.md
[Créer une application de Web de Hello World pour Azure dans IntelliJ]: ./app-service-web-intellij-create-hello-world-web-app.md
[Exemple de projet Web dynamique]: http://go.microsoft.com/fwlink/?LinkId=817337

[Centre de développement Java Azure]: https://azure.microsoft.com/develop/java/
[Vue d’ensemble des applications Web]: ./app-service-web-overview.md

<!-- IMG List -->

[01]: ./media/app-service-web-debug-java-web-app-in-intellij/01-debug-java-web-app-in-intellij.png
[02]: ./media/app-service-web-debug-java-web-app-in-intellij/02-configure-intellij-remote-debug.png
[03]: ./media/app-service-web-debug-java-web-app-in-intellij/03-debug-configuration.png
[04]: ./media/app-service-web-debug-java-web-app-in-intellij/04-select-debug.png
[05]: ./media/app-service-web-debug-java-web-app-in-intellij/05-ready-for-remote-debugging.png
[06]: ./media/app-service-web-debug-java-web-app-in-intellij/06-windows-command-prompt-connection-successful-to-remote.png
