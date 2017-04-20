<properties
   pageTitle="Application de cycle de vie en Service Fabric | Microsoft Azure"
   description="Décrit le développement, déploiement, test, mise à niveau, mise à jour et suppression d’applications de Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>


<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/25/2016"
   ms.author="ryanwi"/>


# <a name="service-fabric-application-lifecycle"></a>Cycle de vie des applications Fabric de service
Comme avec d’autres plates-formes, une application dans Azure Service Fabric passe généralement par les phases suivantes : conception, développement, test, déploiement, mise à niveau, maintenance et suppression. Fabric du service prend en charge de première classe du cycle de vie complet des applications du nuage, du développement jusqu’au déploiement, la gestion quotidienne et maintenance au déclassement éventuelle. Le modèle de service permet à plusieurs rôles différents de participation indépendamment du cycle de vie d’application. Cet article fournit une vue d’ensemble des API et comment elles sont utilisées par les différents rôles tout au long des phases du cycle de vie application Fabric du Service.

## <a name="service-model-roles"></a>Rôles de modèle de service
Les rôles de modèle de service sont :

- **Développeur de service**: développe les services génériques et modulaires qui peuvent être réutilisées et utilisés dans plusieurs applications du même type ou de types différents. Par exemple, un service de file d’attente peut être utilisé pour la création d’une application de gestion des tickets (support technique) ou d’une application de commerce électronique (panier).

- **Développeur d’applications**: crée des applications en intégrant un ensemble de services pour répondre à certains besoins spécifiques ou les scénarios. Par exemple, un site de commerce électronique peut-être intégrer des « JSON de Service frontaux sans état », « Enchères avec état Services » et « file d’attente avec état » pour créer une solution de mise aux ENCHERES.

- **Administrateur de l’application**: prend des décisions sur la configuration de l’application (en remplissant les paramètres de modèle de configuration), le déploiement (mappage aux ressources disponibles) et la qualité de service. Par exemple, un administrateur de l’application détermine le paramètres régionaux de langue (anglais pour les États-Unis) ou japonais pour le Japon, par exemple de l’application. Une autre application déployée peut avoir différents paramètres.

- **Opérateur**: déploie des applications basées sur la configuration de l’application et les exigences spécifiées par l’administrateur de l’application. Par exemple, un opérateur dispositions et déploie l’application et permet de s’assurer qu’il s’exécute dans Azure. Opérateurs de surveillent les informations de santé et les performances d’application et mettre à jour l’infrastructure physique en fonction des besoins.


## <a name="develop"></a>Développer
1. Un *développeur de service* développe des différents types de services à l’aide du modèle de programmation [Fiable d’acteurs](service-fabric-reliable-actors-introduction.md) ou de [Services fiables](service-fabric-reliable-services-introduction.md) .
2. Un *développeur de service* décrit par déclaration les types de service développé dans un fichier de manifeste de service consistant en un ou plusieurs des modules de code et données de configuration.
3. Puis, un *développeur d’application* crée une application à l’aide de différents types de service.
4. Un *développeur d’application* décrit par déclaration du type d’application dans un manifeste d’application en référençant les manifestes de service des services constitutifs et correctement substitution de paramétrage des différents paramètres de configuration et de déploiement des services constitutifs.

Pour obtenir des exemples, reportez-vous à la section [mise en route avec les acteurs fiable](service-fabric-reliable-actors-get-started.md) et [mise en route des Services fiables](service-fabric-reliable-services-quick-start.md) .

## <a name="deploy"></a>Déployer
1. Un *administrateur d’application* personnalise le type d’application à une application spécifique soit déployé sur un cluster Service Fabric en spécifiant les paramètres appropriés de l’élément **ApplicationType** dans le manifeste d’application.

2. Un *opérateur* télécharge le package d’application dans le magasin d’image de cluster à l’aide de la [méthode de le **CopyApplicationPackage** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage.aspx) ou l' [applet de commande **Copy-ServiceFabricApplicationPackage** ](https://msdn.microsoft.com/library/azure/mt125905.aspx). Le package d’application contient le manifeste d’application et la collection de services. Service de Fabric déploie des applications à partir du package d’application stocké dans le magasin d’image, qui peut être un magasin blob Azure ou le service système Service Fabric.

3. L' *opérateur* configure ensuite le type d’application dans le cluster cible à partir du package d’application chargés à l’aide de la [méthode de **ProvisionApplicationAsync** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync.aspx), l' [applet de commande **Register-ServiceFabricApplicationType** ](https://msdn.microsoft.com/library/azure/mt125958.aspx)ou l' [opération de reste de la **disposition d’une Application** ](https://msdn.microsoft.com/library/azure/dn707672.aspx).

4. Après avoir déployé l’application, un *opérateur* démarre l’application avec les paramètres fournis par l' *administrateur de l’application* à l’aide de la [méthode de **CreateApplicationAsync** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.createapplicationasync.aspx), l' [applet de commande **New-ServiceFabricApplication** ](https://msdn.microsoft.com/library/azure/mt125913.aspx)ou l' [opération de reste de **Créer l’Application** ](https://msdn.microsoft.com/library/azure/dn707676.aspx).

5. Une fois que l’application a été déployée, un *opérateur* utilise la [méthode de **CreateServiceAsync** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.servicemanagementclient.createserviceasync.aspx), l' [applet de commande **New-ServiceFabricService** ](https://msdn.microsoft.com/library/azure/mt125874.aspx)ou l' [opération de **Création de Service** reste](https://msdn.microsoft.com/library/azure/dn707657.aspx) à créer de nouvelles instances de service pour l’application basée sur les types de services disponibles.

6. L’application est en cours d’exécution dans le cluster Service Fabric.

Pour obtenir des exemples, voir [déploiement d’une application](service-fabric-deploy-remove-applications.md) .

## <a name="test"></a>Test
1. Après le déploiement du cluster de développement local ou d’un cluster de test, un *développeur de service* s’exécute sur le scénario de test de reprise intégré en utilisant les classes [**FailoverTestScenarioParameters**](https://msdn.microsoft.com/library/azure/system.fabric.testability.scenario.failovertestscenarioparameters.aspx) et [**FailoverTestScenario**](https://msdn.microsoft.com/library/azure/system.fabric.testability.scenario.failovertestscenario.aspx) ou l' [applet de commande **Invoke-ServiceFabricFailoverTestScenario** ](https://msdn.microsoft.com/library/azure/mt644783.aspx). Le scénario de test de basculement s’exécute un service spécifié par le biais des transitions importantes et les basculements pour s’assurer qu’il est toujours disponible et qu’il fonctionne.

2. Le *développeur du service* exécute ensuite le scénario de test de chaos intégrées en utilisant les classes [**ChaosTestScenarioParameters**](https://msdn.microsoft.com/library/azure/system.fabric.testability.scenario.chaostestscenarioparameters.aspx) et [**ChaosTestScenario**](https://msdn.microsoft.com/library/azure/system.fabric.testability.scenario.chaostestscenario.aspx) ou l' [applet de commande **Invoke-ServiceFabricChaosTestScenario** ](https://msdn.microsoft.com/library/azure/mt644774.aspx). Le scénario de test de chaos induit aléatoirement plusieurs nœud package de code et erreurs de réplica dans le cluster.

3. Le *développeur du service de* [communication de service-service de tests](service-fabric-testability-scenarios-service-communication.md) en créant des scénarios de test déplacement les réplicas principales dans le cluster.

Pour plus d’informations, consultez [Introduction au service d’analyse de panne](service-fabric-testability-overview.md) .

## <a name="upgrade"></a>Mise à niveau
1. Un *développeur de service* met à jour les services constitutifs de l’application instanciée et/ou résout les bogues et propose une nouvelle version du manifeste de service.

2. Un *développeur d’applications* se substitue et paramètre les paramètres de configuration et de déploiement des services cohérents et propose une nouvelle version du manifeste d’application. Puis, le développeur d’applications intègre les nouvelles versions des manifestes de service dans l’application et propose une nouvelle version du type d’application dans un package d’application mis à jour.

3. Un *administrateur de l’application* intègre la nouvelle version du type d’application dans l’application cible en le mettant à jour les paramètres appropriés.

5. Un *opérateur* télécharge le package d’application mis à jour dans le magasin d’image de cluster à l’aide de la [méthode de le **CopyApplicationPackage** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage.aspx) ou l' [applet de commande **Copy-ServiceFabricApplicationPackage** ](https://msdn.microsoft.com/library/azure/mt125905.aspx). Le package d’application contient le manifeste d’application et la collection de services.

6. Un *opérateur* met la nouvelle version de l’application dans le cluster cible à l’aide de la [méthode de **ProvisionApplicationAsync** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync.aspx), l' [applet de commande **Register-ServiceFabricApplicationType** ](https://msdn.microsoft.com/library/azure/mt125958.aspx)ou l' [opération de reste de la **disposition d’une Application** ](https://msdn.microsoft.com/library/azure/dn707672.aspx).

7. Un *opérateur* met à niveau l’application cible pour la nouvelle version à l’aide de la [méthode de **UpgradeApplicationAsync** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.upgradeapplicationasync.aspx), l' [applet de commande **Start-ServiceFabricApplicationUpgrade** ](https://msdn.microsoft.com/library/azure/mt125975.aspx)ou l' [opération de **mise à niveau d’une Application** de reste](https://msdn.microsoft.com/library/azure/dn707633.aspx).

8. Un *opérateur* vérifie l’état d’avancement de la mise à niveau à l’aide de la [méthode de **GetApplicationUpgradeProgressAsync** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.getapplicationupgradeprogressasync.aspx), l' [applet de commande **Get-ServiceFabricApplicationUpgrade** ](https://msdn.microsoft.com/library/azure/mt125988.aspx)ou l' [opération de **Progression de mise à niveau de l’Application obtention** reste](https://msdn.microsoft.com/library/azure/dn707631.aspx).

9. Si nécessaire, l' *opérateur* modifie et applique à nouveau les paramètres de la mise à niveau des applications en cours à l’aide de la [méthode de **UpdateApplicationUpgradeAsync** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.updateapplicationupgradeasync.aspx), l' [applet de commande **Update-ServiceFabricApplicationUpgrade** ](https://msdn.microsoft.com/library/azure/mt126030.aspx)ou l' [opération de **Mise à jour de l’Application mise à niveau** des autres](https://msdn.microsoft.com/library/azure/mt628489.aspx).

10. Si nécessaire, l' *opérateur* annule la mise à niveau des applications en cours à l’aide de la [méthode de **RollbackApplicationUpgradeAsync** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.rollbackapplicationupgradeasync.aspx), l' [applet de commande **Start-ServiceFabricApplicationRollback** ](https://msdn.microsoft.com/library/azure/mt125833.aspx)ou l' [opération de **Restauration Application mise à niveau** des autres](https://msdn.microsoft.com/library/azure/mt628494.aspx).

11. Service de Fabric met à niveau l’application cible en cours d’exécution dans le cluster sans perdre la disponibilité d’un de ses services constitutifs.

Consultez [didacticiel de mise à niveau des applications](service-fabric-application-upgrade-tutorial.md) pour obtenir des exemples.

## <a name="maintain"></a>Mettre à jour
1. Des mises à niveau du système d’exploitation et des correctifs, Service Fabric interfaces avec l’infrastructure Azure afin de garantir la disponibilité de toutes les applications en cours d’exécution dans le cluster.

2. Des mises à niveau et des correctifs pour la plate-forme de Service Fabric, Service Fabric met lui-même à niveau sans perdre la disponibilité de toutes les applications en cours d’exécution sur le cluster.

3. Un *administrateur d’application* approuve l’ajout ou la suppression de nœuds d’un cluster après avoir analysé les données historiques de capacité d’utilisation et la demande prévue.

4. Un *opérateur* ajoute et supprime les nœuds spécifiés par l' *administrateur de l’application*.

5. Lorsque les nouveaux nœuds sont ajoutés à ou de nœuds existants sont supprimés du cluster, Service Fabric répartit automatiquement la charge les applications en cours d’exécution sur tous les nœuds dans le cluster pour obtenir des performances optimales.

## <a name="remove"></a>Supprimer
1. Un *opérateur* peut supprimer une instance spécifique d’un service en cours d’exécution dans le cluster sans supprimer l’ensemble de l’application à l’aide de la [méthode de **DeleteServiceAsync** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.servicemanagementclient.deleteserviceasync.aspx), l' [applet de commande **Remove-ServiceFabricService** ](https://msdn.microsoft.com/library/azure/mt126033.aspx)ou l' [opération de **Service de suppression de** reste](https://msdn.microsoft.com/library/azure/dn707687.aspx).  

2. Un *opérateur* peut également supprimer une instance d’application et tous ses services à l’aide de la [méthode de **DeleteApplicationAsync** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.deleteapplicationasync.aspx), l' [applet de commande **Remove-ServiceFabricApplication** ](https://msdn.microsoft.com/library/azure/mt125914.aspx)ou l' [opération de reste de **Supprimer l’Application** ](https://msdn.microsoft.com/library/azure/dn707651.aspx).

3. Une fois les applications et les services sont arrêtés, l' *opérateur* peut annule la configuration du type d’application à l’aide de la [méthode de **UnprovisionApplicationAsync** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.unprovisionapplicationasync.aspx), l' [applet de commande **Unregister-ServiceFabricApplicationType** ](https://msdn.microsoft.com/library/azure/mt125885.aspx)ou l' [opération de reste de **la mise hors service une Application** ](https://msdn.microsoft.com/library/azure/dn707671.aspx). Annuler déploiement le type d’application ne supprime pas le package d’application à partir de la ImageStore. Vous devez supprimer le package d’application manuellement.

4. Un *opérateur* supprime le package d’application de la ImageStore à l’aide de la [méthode de le **RemoveApplicationPackage** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.removeapplicationpackage.aspx) ou l' [applet de commande **Remove-ServiceFabricApplicationPackage** ](https://msdn.microsoft.com/library/azure/mt163532.aspx).

Pour obtenir des exemples, voir [déploiement d’une application](service-fabric-deploy-remove-applications.md) .

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le développement, test et la gestion des applications de tissu de Service et les services, consultez :

- [Acteurs fiables](service-fabric-reliable-actors-introduction.md)
- [Services fiables](service-fabric-reliable-services-introduction.md)
- [Déploiement d’une application](service-fabric-deploy-remove-applications.md)
- [Mise à niveau de l’application](service-fabric-application-upgrade.md)
- [Vue d’ensemble de la testabilité](service-fabric-testability-overview.md)
- [Exemple de cycle de vie d’application basée sur le reste](service-fabric-rest-based-application-lifecycle-sample.md)
