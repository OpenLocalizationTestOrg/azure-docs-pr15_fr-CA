<properties 
   pageTitle="Publier Assistant Application Azure | Microsoft Azure"
   description="Publier Assistant Application Azure"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="multiple"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="publish-azure-application-wizard"></a>Publier Assistant Application Azure

## <a name="overview"></a>Vue d’ensemble

Une fois que vous développez une application web dans Visual Studio, vous pouvez publier cette application plus facilement à un service de cloud Azure à l’aide de l’Assistant **Publier des applications Azure** . La première section décrit les étapes que vous devez effectuer avant de l’utilisation de l’Assistant, et les sections restantes décrivent les fonctionnalités de l’Assistant.

>[AZURE.NOTE] Cette rubrique est sur le déploiement de services dans le nuage, pas aux sites web. Pour plus d’informations sur le déploiement de sites web, consultez [comment déployer un Site Web de Azure](https://social.msdn.microsoft.com/Search/windowsazure?query=How%20to%20Deploy%20an%20Azure%20Web%20Site&Refinement=138&ac=4#refinementChanges=117&pageNumber=1&showMore=false).

## <a name="prerequisites"></a>Conditions préalables

Avant de publier votre application web d’Azure, vous devez disposer d’un compte Microsoft et un abonnement Azure, et vous devez associer votre application web avec un service cloud Azure. Si vous avez déjà effectué ces tâches, vous pouvez passer à la section suivante.

1. Obtenez un compte Microsoft et un abonnement Azure. Vous pouvez essayer un abonnement Azure libre libre d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/)

1. Créer un service cloud et un compte de stockage sur Azure. Pour cela, à partir de l’Explorateur de serveurs dans Visual Studio, ou en utilisant le [Azure portal classique](http://go.microsoft.com/fwlink/?LinkID=213885).

1. Permettez à votre application web pour Azure. Pour permettre à votre application web doit être publié sur Azure à partir de Visual Studio, vous devez l’associer à un projet de service cloud Azure dans Visual Studio. Pour créer le projet de service cloud associé, ouvrez le menu contextuel du projet pour votre application web et choisissez Convertir, **convertir en projet de Service Cloud Azure**.

1. Une fois le projet de service cloud est ajouté à votre solution, ouvrez de nouveau le même menu contextuel, puis choisissez **Publier**. Pour plus d’informations sur l’activation des applications Azure, consultez [Comment : migrer et publier une Application Web à un Service de Cloud Azure à partir de Visual Studio](https://msdn.microsoft.com/library/azure/hh420322.aspx).

>[AZURE.NOTE] Veillez à démarrer Visual Studio en tant qu’administrateur (exécuter en tant qu’administrateur).

1. Lorsque vous êtes prêt à publier votre application, ouvrez le menu contextuel pour le projet de service cloud Azure et puis cliquez sur **Publier**. Les étapes suivantes montrent l’Assistant Publier des applications Azure.

## <a name="choosing-your-subscription"></a>Choix de votre abonnement

### <a name="to-choose-a-subscription"></a>Pour choisir un abonnement

1. Avant d’utiliser l’Assistant pour la première fois, vous devez vous connecter. Cliquez sur le lien de **Connexion** . Connectez-vous au portail Azure lorsque vous y êtes invité et fournir votre nom d’utilisateur d’Azure et d’un mot de passe. 

    ![Il s’agit d’un des écrans d’Assistant Publication](./media/vs-azure-tools-publish-azure-application-wizard/IC799159.png)

    La liste des abonnements remplit avec les abonnements associés à votre compte. Vous pouvez également voir des abonnements à partir des fichiers d’abonnement que vous avez importé précédemment.

1. Dans la liste **Choisir votre abonnement** , cliquez sur l’abonnement à utiliser pour ce déploiement.

   Si vous choisissez **< gérer... >**, la boîte de dialogue **Gérer les abonnements** apparaît et vous pouvez choisir l’abonnement et le compte utilisateur à utiliser. L’onglet **comptes** affiche tous vos comptes, et l’onglet **abonnements** affiche tous les abonnements associés aux comptes. Vous pouvez également choisir une région à partir de laquelle utiliser les ressources Azure, ainsi que créer ou importer des certificats pour votre abonnement à partir du portail Azure. Si vous avez importé tous les abonnements à partir d’un fichier d’abonnement, les certificats associés seront affiche sous l’onglet **certificats** . Lorsque vous avez terminé, cliquez sur le bouton **Fermer** .

    ![Gérer les abonnements](./media/vs-azure-tools-publish-azure-application-wizard/IC799160.png)

    >[AZURE.NOTE] Un fichier d’abonnement peut contenir plus d’un abonnement.

1. Cliquez sur le bouton **suivant** pour continuer. 

    Si les services en nuage ne sont pas dans votre abonnement, vous devez créer un service cloud dans Azure pour héberger votre projet. La boîte de dialogue **créer un Service Cloud et le compte de stockage** s’affiche.

    Spécifiez un nouveau nom pour le service en nuage. Le nom doit être unique dans Azure. Spécifiez ensuite une région ou un groupe d’affinité pour un centre de données proche de vous ou la plupart de vos clients. Ce nom est également utilisé pour un nouveau compte de stockage Azure crée pour votre service cloud.

1. Modifier les paramètres que vous souhaitez pour ce déploiement, puis le publier en cliquant sur le bouton **Publier** (la section suivante fournit plus d’informations sur les différents paramètres). Pour passer en revue les paramètres avant la publication, cliquez sur le bouton **suivant** .

    >[AZURE.NOTE] Si vous avez choisi de publier dans cette étape, vous pouvez surveiller l’état de ce déploiement dans Visual Studio.

Vous pouvez modifier des paramètres courants et avancées pour un déploiement à l’aide de l’Assistant **Publier des applications Azure** . Par exemple, vous pouvez choisir un paramètre pour déployer votre application dans un environnement de test avant de la diffuser. L’illustration suivante montre l’onglet **Paramètres communs** pour un déploiement d’Azure.

![Paramètres courants](./media/vs-azure-tools-publish-azure-application-wizard/IC749013.png)

## <a name="configuring-your-publish-settings"></a>Configuration de vos paramètres de publication

### <a name="to-configure-the-publish-settings"></a>Pour configurer les paramètres de publication

1. Dans la liste **service Cloud** , effectuez l’une des ensembles d’étapes suivants :

   1. Dans la zone de liste déroulante, choisissez un service cloud existant. L’emplacement du centre de données pour le service s’affiche. Vous devez noter cet emplacement et assurez-vous que votre emplacement de compte de stockage est dans le même centre de données.

    1. Sélectionnez **Créer un nouveau** pour créer un service en nuage qui héberge Azure. Dans la boîte de dialogue **Créer un Service Cloud** , fournissez un nom pour le service et spécifiez une région ou un groupe d’affinité pour spécifier l’emplacement du centre de données que vous souhaitez héberger ce service cloud. Le nom doit être unique dans Azure.

1. Dans la liste de **l’environnement** , choisissez **Production** ou **intermédiaire**. Choisissez l’environnement intermédiaire si vous souhaitez déployer votre application dans un environnement de test. Vous pouvez déplacer ultérieurement votre application dans l’environnement de production.

1. Dans la liste **configuration de Build** , cliquez sur **Debug** ou **Release**.

1. Dans la liste de la **configuration du Service** , choisissez **nuage** ou **Local**.

    Activez la case à cocher **Activer le Bureau à distance pour tous les rôles** si vous voulez être en mesure de se connecter à distance au service. Cette option est principalement utilisée pour le dépannage. Lorsque vous sélectionnez cette case à cocher, la boîte de dialogue **Configuration du Bureau à distance** s’affiche. Cliquez sur le lien paramètres pour modifier la configuration.

    Activez la case à cocher **Activer le déploiement Web pour tous les rôles de web** pour activer le déploiement web pour le service. Vous devez activer le Bureau à distance utiliser cette fonctionnalité. Pour plus d’informations, voir [[publication d’un Service Cloud à l’aide d’outils Azure](https://msdn.microsoft.com/library/azure/ff683672.aspx)](https://msdn.microsoft.com/library/azure/ff683672.aspx). Pour plus d’informations sur le déploiement Web, consultez [[publication d’un Service Cloud à l’aide d’outils Azure](https://msdn.microsoft.com/library/azure/ff683672.aspx)](https://msdn.microsoft.com/library/azure/ff683672.aspx).

1. Cliquez sur l’onglet **Paramètres avancés** . Dans le champ **étiquette de déploiement** , acceptez le nom par défaut ou entrez un nom de votre choix. Pour ajouter la date sur l’étiquette du déploiement, laissez la case à cocher.

    ![Troisième écran de l’Assistant Publication](./media/vs-azure-tools-publish-azure-application-wizard/IC749014.png)

1. Dans la liste **compte de stockage** , sélectionnez le compte de stockage à utiliser pour ce déploiement. Comparez les emplacements des centres de données pour votre service cloud et votre compte de stockage. Idéalement, ces emplacements doivent être les mêmes.

    >[AZURE.NOTE] Le compte de stockage Azure stocke le package pour le déploiement de l’application. Une fois que l’application est déployée, le package est supprimé à partir du compte de stockage.

1. Activez la case à cocher **mise à jour du déploiement** si vous voulez déployer uniquement des composants mis à jour. Ce type de déploiement peut être plus rapide qu’un déploiement complet. Cliquez sur le lien **paramètres** pour ouvrir la boîte de dialogue **mettre à jour les paramètres de déploiement** , indiquée dans l’illustration suivante. 

    ![Paramètres de déploiement](./media/vs-azure-tools-publish-azure-application-wizard/IC617060.png)

    Vous pouvez choisir une des deux options pour le déploiement de la mise à jour, incrémentielle ou simultané. Un déploiement incrémentiel met à jour une instance déployée à la fois, afin que votre application reste en ligne et disponible pour les utilisateurs. Un déploiement simultané met à jour toutes les instances déployées à la fois. Mise à jour simultanée est plus rapide que la mise à jour incrémentielle, mais si vous choisissez cette option, votre application ne soient pas disponible au cours du processus de mise à jour.

    Vous devez sélectionner la case à cocher si le déploiement ne peut pas être mis à jour, effectuez un déploiement complet si vous souhaitez que le déploiement complet pour avoir lieu automatiquement en cas d’échec d’un déploiement de mise à jour. Un déploiement complet réinitialise l’adresse IP virtuelle (VIP) pour le service en nuage. Pour plus d’informations, consultez [Comment : conserver une adresse IP virtuelle de constante pour un Service en nuage](https://msdn.microsoft.com/library/azure/jj614593.aspx).


1. Pour déboguer votre service, activez la case à cocher **Activer IntelliTrace** , ou si vous déployez une configuration **Debug** et que vous souhaitez déboguer votre service cloud dans Azure, activez la case à cocher **Activer le débogueur distant pour tous les rôles** pour déployer les services de débogage distants.

2. Pour profiler l’application, activez la case à cocher **Activer le profilage** et puis cliquez sur le lien **paramètres** pour afficher les options de profilage. 


    >[AZURE.NOTE] Vous devez utiliser Visual Studio Ultimate pour activer IntelliTrace ou profilage Interaction de couche (TIP), et vous ne pouvez pas activer les deux en même temps.

    Pour plus d’informations, consultez [débogage d’un Service Cloud publié avec IntelliTrace et Visual Studio](https://msdn.microsoft.com/library/azure/ff683671.aspx) et de [tester les performances d’un Service Cloud](https://msdn.microsoft.com/library/azure/hh369930.aspx).

1. Cliquez sur **suivant** pour afficher la page Résumé de l’application.

## <a name="publishing-your-application"></a>Publication de votre Application

1. Vous pouvez choisir de créer un profil de publication à partir des paramètres que vous avez choisi. Par exemple, vous pouvez créer un profil pour un environnement de test et un autre pour la production. Pour enregistrer ce profil, cliquez sur l’icône **Enregistrer** . L’Assistant crée le profil et l’enregistre dans le projet Visual Studio. Pour modifier le nom du profil, ouvrez la liste des **profils de la cible** et puis choisissez **< gérer... >**.

    ![Écran de résumé de l’Assistant Publication](./media/vs-azure-tools-publish-azure-application-wizard/IC749015.png)

    >[AZURE.NOTE] Le profil de publication s’affiche dans l’Explorateur de solutions dans Visual Studio, et les paramètres du profil sont écrites dans un fichier avec une extension .azurePubxml. Les paramètres sont enregistrés en tant qu’attributs des balises XML.

1. Cliquez sur **Publier** pour publier votre application. Vous pouvez surveiller l’état de processus dans la fenêtre **sortie** dans Visual Studio.

## <a name="see-also"></a>Voir aussi

[Comment : migrer et publier une Application Web à un Service Cloud Azure à partir de Visual Studio](https://msdn.microsoft.com/library/azure/hh420322.aspx)

[Publication d’un Service Cloud à l’aide d’outils Azure](https://msdn.microsoft.com/library/azure/ff683672.aspx)

[Débogage d’un Service Cloud publié avec IntelliTrace et Visual Studio](https://msdn.microsoft.com/library/azure/ff683671.aspx)

[Tester les performances d’un Service Cloud](https://msdn.microsoft.com/library/azure/hh369930.aspx)

