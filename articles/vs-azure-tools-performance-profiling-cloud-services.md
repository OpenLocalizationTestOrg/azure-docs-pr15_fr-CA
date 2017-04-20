<properties 
   pageTitle="Tester les performances d’un service en nuage | Microsoft Azure"
   description="Tester les performances d’un service cloud avec le profileur Visual Studio"
   services="visual-studio-online"
   documentationCenter="n/a"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="visual-studio-online"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tarcher" />


# <a name="testing-the-performance-of-a-cloud-service"></a>Tester les performances d’un service cloud 

##<a name="overview"></a>Vue d’ensemble

Vous pouvez tester les performances d’un service cloud dans l’une des manières suivantes :

- Utiliser Azure Diagnostics à collecter des informations sur les connexions et les demandes et consulter des statistiques de site qui indiquent la façon dont le service s’exécute à partir d’un point de vue du client. Pour commencer à utiliser, consultez [Configuration des diagnostics pour les Services en nuage Azure et les Machines virtuelles]( http://go.microsoft.com/fwlink/p/?LinkId=623009).

- Le profileur Visual Studio permet d’obtenir une analyse approfondie des aspects calculs de la façon dont le service s’exécute. Comme décrit dans cette rubrique, vous pouvez utiliser le Générateur de profils pour mesurer les performances comme un service s’exécute dans Azure. Pour plus d’informations sur la façon d’utiliser le Générateur de profils pour mesurer les performances comme un service s’exécute localement dans un émulateur de calcul, voir [tester les performances d’un Azure Cloud Service localement dans le calcul émulateur à l’aide du Générateur de profils Visual Studio](http://go.microsoft.com/fwlink/p/?LinkId=262845).



## <a name="choosing-a-performance-testing-method"></a>Choix d’une méthode de test des performances

###<a name="use-azure-diagnostics-to-collect"></a>Utilisez Azure les Diagnostics à collecter :###

- Statistiques sur les pages web ou des services, tels que les connexions et les demandes.

- Statistiques sur les rôles, tels que la fréquence à laquelle un rôle est redémarré.

- Ensemble d’informations sur l’utilisation de la mémoire, telles que le pourcentage de temps utilisé par le garbage collector ou la mémoire définie d’un rôle en cours d’exécution.

###<a name="use-the-visual-studio-profiler-to"></a>Utilisez le profileur Visual Studio pour :###

- Déterminer les fonctions prennent le plus de temps.

- Mesurer combien de temps prend de chaque partie d’un programme exigeant en ressources.

- Comparer les rapports de performances détaillés pour les deux versions d’un service.

- Analyser plus en détail au niveau des allocations de mémoire de chaque allocation de mémoire.

- Analyser les problèmes de concurrence d’accès du code multithread.

Lorsque vous utilisez le Générateur de profils, vous pouvez collecter des données lorsqu’un service cloud s’exécute localement ou dans Azure.

###<a name="collect-profiling-data-locally-to"></a>Collecter les données de profilage localement pour :###

- Tester les performances d’une partie d’un service en nuage, telles que l’exécution de rôle de travail spécifique, qui ne nécessite pas une charge simulée réaliste.

- Tester les performances d’un service en nuage de façon isolée, dans des conditions contrôlées.

- Tester les performances d’un service en nuage avant de le déployer sur Azure.

- Tester les performances d’un service de cloud privé, sans perturber les déploiements existants.

- Tester les performances du service sans entraîner de frais pour l’exécution dans Azure.

###<a name="collect-profiling-data-in-azure-to"></a>Collecter les données de profilage dans Azure pour :###

- Tester les performances d’un service en nuage sous une charge simulée ou réel.

- Utilisez la méthode d’instrumentation de la collecte de données de profilage, comme décrit plus loin dans cette rubrique.

- Tester les performances du service dans le même environnement que lorsque le service s’exécute dans la production.

En général, vous simulez une charge pour les services en nuage test sous normal ou des conditions de stress.

## <a name="profiling-a-cloud-service-in-azure"></a>Profilage d’un service cloud dans Azure

Lorsque vous publiez votre service en nuage à partir de Visual Studio, vous pouvez le service de profil et spécifier les paramètres de profilage de vous donnent les informations que vous souhaitez. Une session de profilage est démarrée pour chaque instance d’un rôle. Pour plus d’informations sur la façon de publier votre service à partir de Visual Studio, consultez l’article [publication à un Service de Cloud Azure à partir de Visual Studio](https://msdn.microsoft.com/library/azure/ee460772.aspx).

Pour en savoir plus sur le profilage des performances dans Visual Studio, consultez le [Guide du débutant en profilage des performances](https://msdn.microsoft.com/library/azure/ms182372.aspx) et [Analyse des performances des applications par à l’aide des outils de profilage](https://msdn.microsoft.com/library/azure/z9z62c29.aspx).

>[AZURE.NOTE] Vous pouvez activer IntelliTrace ou profilage lorsque vous publiez votre service cloud. Vous ne pouvez pas activer à la fois.

###<a name="profiler-collection-methods"></a>Méthodes de collecte du Générateur de profils

Vous pouvez utiliser des méthodes de collection différente pour le profilage, en fonction de vos problèmes de performances :

- L' **échantillonnage de l’UC** - cette méthode collecte les statistiques qui sont utiles pour l’analyse initiale des problèmes d’utilisation du processeur. Échantillonnage de l’UC est suggérée pour commencer la plupart des examens de performances. Il existe un faible impact sur l’application que vous profilez lorsque vous collectez des données d’échantillonnage de l’UC.

- **Instrumentation** -cette méthode recueille des données de temporisation détaillées utiles pour une analyse ciblée et pour analyser les problèmes de performances d’entrée/sortie. La méthode d’instrumentation enregistre chaque entrée, sortie et appel de fonction des fonctions dans un module pendant une exécution du profilage. Cette méthode est utile pour collecter des informations de minutage détaillées à propos d’une section de votre code et comprendre l’impact des opérations d’entrée et de sortie sur les performances de l’application. Cette méthode est désactivée pour un ordinateur exécutant un système d’exploitation 32 bits. Cette option est disponible uniquement lorsque vous exécutez le service cloud dans Azure, pas localement dans l’émulateur de calcul.

- **Allocation de mémoire .NET** - cette méthode collecte les données d’allocation de mémoire.NET Framework à l’aide de la méthode de profilage par échantillonnage. Les données collectées incluent le nombre et la taille des objets alloués.

- **Accès concurrentiel** - cette méthode collecte les données de conflit de ressources et les données d’exécution de processus et de thread qui est utiles pour analyser les applications multithreads et multiprocessues. La méthode de concurrence collecte des données pour chaque événement que bloque l’exécution de votre code, notamment lorsqu’un thread attend verrouillé l’accès à une ressource d’application à libérer. Cette méthode est utile pour l’analyse des applications multi-threads.

- Vous pouvez également activer le **Profilage d’Interaction de couche**, qui fournit des informations supplémentaires sur les temps d’exécution de ADO.NET synchrone appelle dans les fonctions des applications multicouches qui communiquent avec une ou plusieurs bases de données. Vous pouvez collecter des données d’interaction de couche avec les méthodes de profilage. Pour plus d’informations sur le profilage d’interaction de couche, consultez [Affichage des Interactions de couche](https://msdn.microsoft.com/library/azure/dd557764.aspx).

## <a name="configuring-profiling-settings"></a>Configuration des paramètres de profilage

L’illustration suivante montre comment configurer les paramètres de profilage à partir de la boîte de dialogue Publier les applications Azure.

![Configurer les paramètres de profilage](./media/vs-azure-tools-performance-profiling-cloud-services/IC526984.png)

>[AZURE.NOTE] Pour activer la case à cocher **Activer le profilage** , il vous faut le Générateur de profils installé sur l’ordinateur local que vous utilisez pour publier votre service cloud. Par défaut, le profileur est installé lorsque vous installez Visual Studio.

### <a name="to-configure-profiling-settings"></a>Pour configurer les paramètres de profilage

1. Dans l’Explorateur de solutions, ouvrez le menu contextuel pour votre projet d’Azure et puis cliquez sur **Publier**. Pour obtenir la procédure détaillée publier un service cloud, voir [publication d’un nuage de service en utilisant les outils Azure](http://go.microsoft.com/fwlink/p?LinkId=623012).

1. Dans la boîte de dialogue **Publier les applications Azure** , choisissez l’onglet **Paramètres avancés** .

1. Pour activer le profilage, sélectionnez la case à cocher **Activer le profilage** .

1. Pour configurer vos paramètres de profilage, cliquez sur le lien **paramètres** . La boîte de dialogue Paramètres de profilage s’affiche.

1. Dans les cases d’option **quelle méthode de profilage souhaitez-vous utiliser** , choisissez le type de profil que vous avez besoin.

1. Pour collecter les données profilage d’interaction de couche, activez la case à cocher **Activer le profilage d’Interaction de couche** .

1. Pour enregistrer les paramètres, cliquez sur le bouton **OK** .

    Lors de la publication de cette application, ces paramètres sont utilisés pour créer la session de profilage pour chaque rôle.

## <a name="viewing-profiling-reports"></a>Affichage des rapports de profilage

Une session de profilage est créée pour chaque instance d’un rôle dans votre service cloud. Pour afficher vos rapports de profilage de chaque session à partir de Visual Studio, vous pouvez afficher la fenêtre de l’Explorateur de serveurs et puis cliquez sur le nœud de calcul Azure pour sélectionner une instance d’un rôle. Vous pouvez ensuite afficher le rapport de profilage comme indiqué dans l’illustration suivante.

![Afficher le rapport de profilage à partir de Azure](./media/vs-azure-tools-performance-profiling-cloud-services/IC748914.png)

### <a name="to-view-profiling-reports"></a>Pour afficher des rapports de profilage

1. Pour afficher la fenêtre de l’Explorateur de serveurs dans Visual Studio, dans la barre de menu Choisissez Affichage, Explorateur de serveurs.

1. Choisissez le nœud Azure Compute, puis le nœud déploiement d’Azure pour le service en nuage que vous avez sélectionné au profil lorsque vous avez publié à partir de Visual Studio.

1. Pour afficher des rapports de profilage d’une instance, sélectionnez le rôle dans le service, ouvrir le menu contextuel pour une instance spécifique, puis cliquez sur **Afficher le rapport de profilage**.

    Le rapport, un fichier .vsp, n’est pas téléchargé à partir d’Azure, et l’état du téléchargement s’affiche dans le journal d’activité Azure. Une fois le téléchargement terminé, le rapport de profilage s’affiche dans un onglet dans l’éditeur de Visual Studio nommée <Role name> _<Instance Number>_ <identifier>.vsp. Données de synthèse pour le rapport s’affiche.

1. Pour afficher différentes vues du rapport, dans la liste Vue courante, choisissez le type d’affichage que vous souhaitez. Pour plus d’informations, consultez [Vues des rapports des outils de profilage](https://msdn.microsoft.com/library/azure/bb385755.aspx).

## <a name="next-steps"></a>Étapes suivantes

[Débogage des Services en nuage](https://msdn.microsoft.com/library/azure/ee405479.aspx)

[Publication d’un Service Cloud Azure à partir de Visual Studio](https://msdn.microsoft.com/library/azure/ee460772.aspx)

