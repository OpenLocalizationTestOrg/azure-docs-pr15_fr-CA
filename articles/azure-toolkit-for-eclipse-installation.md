<properties
    pageTitle="L’installation de la Shared Computer Toolkit Azure pour Eclipse | Microsoft Azure"
    description="Découvrez comment installer le Shared Computer Toolkit Azure pour Eclipse."
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690946.aspx -->

# <a name="installing-the-azure-toolkit-for-eclipse"></a>L’installation de la Shared Computer Toolkit Azure pour Éclipse

Le Shared Computer Toolkit Azure pour Eclipse fournit les modèles et les fonctionnalités qui vous permettent de facilement créer, développer, tester et déployer des applications Azure à l’aide de l’environnement de développement Eclipse. Le Shared Computer Toolkit Azure pour Eclipse est un projet Open Source, dont le code source est disponible sous licence MIT à partir du site du projet sur GitHub à l’adresse suivante :

<https://github.com/Microsoft/Azure-Tools-for-Java>

Les étapes suivantes vous indiquent comment installer le Shared Computer Toolkit Azure pour Eclipse.

[AZURE.INCLUDE [azure-toolkit-for-eclipse-prerequisites](../includes/azure-toolkit-for-eclipse-prerequisites.md)]

## <a name="to-install-the-azure-toolkit-for-eclipse"></a>Pour installer le Shared Computer Toolkit Azure pour Éclipse

1. Démarrez Eclipse.

1. Lorsque Eclipse s’ouvre, cliquez sur le menu **aide** , puis cliquez sur **Installer un nouveau logiciel**, comme indiqué dans l’illustration suivante.

    ![L’installation de la Shared Computer Toolkit Azure pour Éclipse][01]

1. Dans la boîte de dialogue de **Logiciels disponibles** , dans la zone de texte **travail avec** , tapez **http://dl.microsoft.com/eclipse** , suivie de la touche **entrée** .

1. Dans le volet **nom** , vérifiez **Shared Computer Toolkit Azure pour Eclipse**et décochez la case **Contact tout mettre à jour les sites lors de l’installation pour trouver des logiciels requis**. Votre écran doit ressembler à ce qui suit :

    ![L’installation de la Shared Computer Toolkit Azure pour Éclipse][02]

1. Si vous développez le **Shared Computer Toolkit Azure pour Eclipse**, vous verrez les éléments suivants :

    * **Application Java plug-in perspectives**: ce composant vous permet d’utiliser les services de journalisation et d’analyse de télémétrie pour vos instances de serveur et les applications d’Azure.
    * **Filtre de Services de contrôle de l’accès Azure**: ce composant prend en charge l’authentification des utilisateurs d’application Azure ACS, ce qui permet des scénarios d’ouverture de session unique et externalisation logique d’identité de l’application.
    * **Plug-in d’Azure courantes**: ce composant fournit les fonctionnalités communes nécessaires par d’autres composants de la boîte à outils.
    * **Explorateur d’Azure pour Eclipse**: ce composant fournit les fonctionnalités communes nécessaires par d’autres composants de la boîte à outils.
    * **Plug-in Eclipse avec Java Azure**: ce composant fournit la prise en charge pour le développement de projets qui vous permettent de créer, tester et déployer des applications Java pour le nuage Microsoft Azure dans Eclipse et via la ligne de commande.
    * **Plug-in d’applications Azure Web avec Java**: ce composant fournit la prise en charge pour le déploiement d’applications web de Java à des conteneurs de Microsoft Azure Web App.
    * **4.2 de pilote JDBC de Microsoft pour SQL Server**: ce composant fournit l’API JDBC pour SQL Server et la base de données SQL de Microsoft Azure pour Java Platform Enterprise Edition 8.
    * **Package pour les bibliothèques de Client Apache Qpid pour JMS**: ce composant fournit le composant client JMS à partir du projet Apache Qpid pour permettre à votre application utiliser la messagerie AMQP dans Microsoft Azure.
    * **Package Microsoft Azure bibliothèques de pour Java**: ce composant fournit des API d’accès aux services Microsoft Azure, tels que le stockage, le bus des services, exécution du service, etc..

1. Cliquez sur **suivant**. (Si vous rencontrez des retards inhabituels lors de l’installation de la boîte à outils, vérifiez que le **Contact tout mettre à jour les sites lors de l’installation pour trouver des logiciels requis** est désactivée.)

1. Dans la boîte de dialogue **Détails de l’installation** , cliquez sur **suivant**.

    ![Examinez les détails de l’Installation][03]

1. Dans la boîte de dialogue **Vérifier les licences** , lisez les termes du contrat de licence. Si vous acceptez les termes du contrat de licence, cliquez sur **J’accepte les termes du contrat de licence** , puis cliquez sur **Terminer**. (Les étapes restantes supposent que vous n’acceptez pas les termes de ce contrat. Si vous n’acceptez pas les termes de ce contrat, quittez le processus d’installation.)

    ![Consultez les licences][04]

    Eclipse va télécharger et installer les packages nécessaires.

    ![Progression de l’installation][05]

1. Si vous êtes invité à redémarrer Eclipse pour terminer l’installation, cliquez sur **Oui**.

    ![Invite de redémarrage][06]

## <a name="see-also"></a>Voir aussi

Pour plus d’informations sur les boîtes à outils Azure pour IDE de Java, consultez les liens suivants :

- [Azure Shared Computer Toolkit pour Éclipse]
  - *L’installation de la Shared Computer Toolkit Azure pour Eclipse (Cet Article)*
  - [Créer une application de Web de Hello World pour Azure dans Éclipse]
  - [Quelles sont les nouveautés dans la Azure Shared Computer Toolkit pour Éclipse]
- [Azure Shared Computer Toolkit pour IntelliJ]
  - [L’installation de la Shared Computer Toolkit Azure pour IntelliJ]
  - [Créer une application de Web de Hello World pour Azure dans IntelliJ]
  - [Nouveautés de la Shared Computer Toolkit Azure pour IntelliJ]

Pour plus d’informations sur l’utilisation d’Azure avec Java, consultez le [Centre pour développeurs Java Azure].

<!-- URL List -->

[Azure Shared Computer Toolkit pour Éclipse]: ./azure-toolkit-for-eclipse.md
[Azure Shared Computer Toolkit pour IntelliJ]: ./azure-toolkit-for-intellij.md
[Créer une application de Web de Hello World pour Azure dans Éclipse]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[Créer une application de Web de Hello World pour Azure dans IntelliJ]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[Installing the Azure Toolkit for Eclipse]: ./azure-toolkit-for-eclipse-installation.md
[L’installation de la Shared Computer Toolkit Azure pour IntelliJ]: ./azure-toolkit-for-intellij-installation.md
[Quelles sont les nouveautés dans la Azure Shared Computer Toolkit pour Éclipse]: ./azure-toolkit-for-eclipse-whats-new.md
[Nouveautés de la Shared Computer Toolkit Azure pour IntelliJ]: ./azure-toolkit-for-intellij-whats-new.md

[Centre de développement Java Azure]: https://azure.microsoft.com/develop/java/

<!-- IMG List -->

[01]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-01.png
[02]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-02.png
[03]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-03.png
[04]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-04.png
[05]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-05.png
[06]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-06.png

