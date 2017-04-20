<properties 
    pageTitle="Déboguer une application Web de Java sur Azure dans Eclipse | Microsoft Azure" 
    description="Ce didacticiel vous montre comment utiliser le Shared Computer Toolkit Azure pour Eclipse pour déboguer une application Web de Java en cours d’exécution sur Azure." 
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

# <a name="debug-a-java-web-app-on-azure-in-eclipse"></a>Déboguer une application Web de Java sur Azure dans Éclipse

Ce didacticiel explique comment déboguer une application Web de Java en cours d’exécution sur Azure avec [Shared Computer Toolkit Azure pour Eclipse]. Par souci de simplicité, vous utiliserez un exemple simple de Java Server Page (JSP) pour ce didacticiel, mais la procédure est similaire pour un servlet Java lorsque vous déboguez sur Azure.

Lorsque vous avez terminé ce didacticiel, votre application sera semblable à l’illustration suivante lorsque vous effectuez le débogage dans Eclipse :

![][01]
 
## <a name="prerequisites"></a>Conditions préalables

* Un Java Developer Kit (JDK), v 1,8 ou une version ultérieure.
* Eclipse IDE pour les développeurs Java EE, Indigo ou une version ultérieure. Cela peut être téléchargé à partir de <http://www.eclipse.org/downloads/>.
* Une distribution d’un serveur web basé sur Java ou d’un serveur d’applications, tels que Apache Tomcat ou jetée.
* Un abonnement Azure, qui peut être acquis à partir de <https://azure.microsoft.com/en-us/free/> ou <http://azure.microsoft.com/pricing/purchase-options/>.
* Le Shared Computer Toolkit pour Eclipse Azure. Pour plus d’informations, consultez [installer le Shared Computer Toolkit Azure pour Eclipse].
* Un projet Web dynamiques créés et déployés sur Azure Application Service ; par exemple, voir [créer une application Web Hello World pour Azure dans Eclipse].

## <a name="to-debug-a-java-web-app-on-azure"></a>Pour déboguer une application Web de Java sur Azure

Pour effectuer ces étapes dans cette section, vous pouvez utiliser un projet Web dynamique existant que vous avez déjà déployé sous la forme d’une application Web de Java sur Azure, vous téléchargez un [Exemple de la dynamique du projet Web] et suivez les étapes dans [créer une application Web Hello World pour Azure dans Eclipse] pour le déployer sur Azure. 

1. Ouvrez Eclipse.

1. Configurer les délais d’attente pour le débogage distant :

    1. Cliquez sur le menu **fenêtres** de Eclipse, puis cliquez sur **Préférences**.
    1. Développez le nœud **Java** , puis sélectionnez **Déboguer**.
    1. Configurer les paramètres du **délai d’attente du débogueur (ms)** et **lancer le délai d’attente (ms)** pour `120000`.

        ![][02]

    1. Cliquez sur **OK** pour fermer la boîte de dialogue **Préférences** .

1. Dans Explorateur de projet de Eclipse, cliquez avec le bouton droit sur le projet Web dynamique que vous avez déployés sur Azure. Lorsque le menu contextuel s’affiche, sélectionnez **Déboguer en tant que**, puis cliquez sur **Azure Web App**.

    ![][03]

1. Si c’est la première fois que vous déboguez votre projet Web dynamique, la boîte de dialogue **Configurations de débogage** s’ouvre ; Vous pouvez accepter les valeurs par défaut qui sont spécifiées par le Shared Computer Toolkit sur l’onglet **connexion** . Dans l’onglet **Source** , cliquez sur **Ajouter**, puis sur **projet de Java**, sélectionnez **Dynamic Web projet**, puis cliquez sur **OK**. Une fois ces étapes effectuées, cliquez sur **Debug**.

    ![][04]

1. Lorsque vous êtes invité à **Activer le débogage distant dans l’application Web à distance maintenant ?**, cliquez sur **OK**.

1. **votre application web est maintenant prête pour le débogage distant**, cliquez sur **OK**.

    ![][05]

1. Lorsque la boîte de dialogue **Configurations de débogage** s’affiche de nouveau, cliquez sur **Debug**.

1. Invite de commande Windows ou shell Unix va ouvrir et préparer la connexion nécessaire pour le débogage ; Vous devez attendre jusqu'à ce que la connexion à votre application Web de Java à distance est établie avant de continuer. Si vous utilisez Windows, il ressemblera à l’illustration suivante.

    ![][06]

1. Insérer un point d’arrêt dans votre page JSP, puis ouvrez l’URL pour votre application Web de Java dans un navigateur :

    1. Ouvrez **L’Explorateur Azure** dans Eclipse.
    1. Accédez à des **Applications Web** et l’application de Web Java que vous souhaitez déboguer.
    1. Cliquez avec le bouton droit sur l’application Web, puis cliquez sur **Ouvrir dans le navigateur**.
    1. Eclipse entrent maintenant en mode débogage.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’utilisation d’Azure avec Java, consultez le [Centre pour développeurs Java Azure].

Pour plus d’informations sur la création d’applications Web de Azure, consultez la [Vue d’ensemble des applications Web].

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[Azure App Service]: http://go.microsoft.com/fwlink/?LinkId=529714
[Azure Shared Computer Toolkit pour Éclipse]: ../azure-toolkit-for-eclipse.md
[L’installation de la Shared Computer Toolkit Azure pour Éclipse]: ../azure-toolkit-for-eclipse-installation.md
[Créer une application de Web de Hello World pour Azure dans Éclipse]: ./app-service-web-eclipse-create-hello-world-web-app.md
[Exemple de projet Web dynamique]: http://go.microsoft.com/fwlink/?LinkId=817337

[Centre de développement Java Azure]: https://azure.microsoft.com/develop/java/
[Vue d’ensemble des applications Web]: ./app-service-web-overview.md

<!-- IMG List -->

[01]: ./media/app-service-web-debug-java-web-app-in-eclipse/01-debug-java-web-app-in-eclipse.png
[02]: ./media/app-service-web-debug-java-web-app-in-eclipse/02-configure-eclipse-remote-debug.png
[03]: ./media/app-service-web-debug-java-web-app-in-eclipse/03-debug-as.png
[04]: ./media/app-service-web-debug-java-web-app-in-eclipse/04-debug-configurations.png
[05]: ./media/app-service-web-debug-java-web-app-in-eclipse/05-ready-for-remote-debugging.png
[06]: ./media/app-service-web-debug-java-web-app-in-eclipse/06-windows-command-prompt-connection-successful-to-remote.png
