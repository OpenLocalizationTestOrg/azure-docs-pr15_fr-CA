<properties
    pageTitle="Optimisez votre environnement grâce à la solution de Service Fabric dans journal Analytique | Microsoft Azure"
    description="Vous pouvez utiliser la solution de Service Fabric pour évaluer les risques et la santé de votre structure de Service applications, micro-services, nœuds et les clusters."
    services="log-analytics"
    documentationCenter=""
    authors="niniikhena"
    manager="jochan"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/21/2016"
    ms.author="nini"/>



# <a name="service-fabric-solution-in-log-analytics"></a>Solution de service de Fabric dans journal Analytique

> [AZURE.SELECTOR]
- [Gestionnaire de ressources](log-analytics-service-fabric-azure-resource-manager.md)
- [PowerShell](log-analytics-service-fabric.md)

Cet article décrit comment utiliser la solution de Service Fabric dans Analytique du journal afin d’identifier et de résoudre les problèmes sur votre cluster Service Fabric.

La solution de Service Fabric utilise des données de Diagnostics d’Azure à partir de vos ordinateurs virtuels Fabric de Service, par la collecte des données de vos tables Azure WAD. Analytique de journal lit ensuite les événements de framework Service Fabric, y compris **Les événements de Service fiables**, acteur, **Les événements opérationnels**et **Événements** **événements personnalisés ETW**. Avec le tableau de bord de solution, vous êtes en mesure d’afficher les problèmes et les événements pertinents dans votre environnement Fabric du Service.

Pour vous familiariser avec la solution, vous devez connecter votre cluster Service Fabric à un espace de travail du journal Analytique. Voici trois scénarios à prendre en compte :

1. Si vous n’avez pas déployé votre cluster Service Fabric, utilisez les étapes de ***déployer un Service de Cluster de Fabric connecté à un espace de travail du journal Analytique*** pour déployer un nouveau cluster et avez configuré pour un rapport Analytique du journal.

2. Si vous avez besoin collecter les compteurs de performance à partir de vos hôtes à utiliser d’autres solutions OMS, telles que la sécurité sur votre structure de Service de Cluster, suivez les étapes de ***déployer un Service de Cluster de Fabric connecté à un espace de travail OMS avec Extension VM installée.***

3. Si vous avez déjà déployé votre cluster à structure de Service et les que vous souhaitez pour le connecter à un journal Analytique, suivez les étapes de ***l’ajout d’un compte de stockage existant au journal Analytique.***


##<a name="deploy-a-service-fabric-cluster-connected-to-a-log-analytics-workspace"></a>Déployer un Service de Cluster Fabric connecté à un espace de travail du journal Analytique.
Ce modèle effectue les opérations suivantes :


1. Déploie un cluster Service de Azure Fabric déjà connecté à un espace de travail du journal Analytique. Vous avez la possibilité de créer un nouvel espace de travail lors du déploiement du modèle ou entrez le nom d’un espace de travail Analytique du journal déjà existant.
2. Ajoute le compte de stockage de diagnostic à l’espace de travail du journal Analytique.
3. Permet à la solution de Service Fabric dans votre espace de travail du journal Analytique.

[![Déployer vers Azure](./media/log-analytics-service-fabric/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fazure-quickstart-templates%2Fmaster%2Fservice-fabric-oms%2F%2Fazuredeploy.json)


Une fois que vous sélectionnez le bouton déployer ci-dessus, vous arrivera sur le portail Azure avec des paramètres pour les modifier. Veillez à créer un nouveau groupe de ressources, si vous entrez un nouveau nom d’espace de travail de journal Analytique : ![Service de Fabric](./media/log-analytics-service-fabric/2.png)

![TISSU de service](./media/log-analytics-service-fabric/3.png)

Accepter les conditions juridiques et cliquez sur « Créer » pour commencer le déploiement. Une fois le déploiement terminé, vous devez voir le nouvel espace de travail et cluster créé et le WADServiceFabric * événements, WADWindowsEventLogs et WADETWEvent des tables ajoutées :

![TISSU de service](./media/log-analytics-service-fabric/4.png)

##<a name="deploy-a-service-fabric-cluster-connected-to-an-oms-workspace-with-vm-extension-installed"></a>Déployer un Service de Cluster Fabric connecté à un espace de travail OMS avec Extension VM installée.
Ce modèle effectue les opérations suivantes :

1. Déploie un cluster Service de Azure Fabric déjà connecté à un espace de travail du journal Analytique. Vous pouvez créer un espace de travail ou en utiliser une existante.
2. Ajouter les comptes de stockage de diagnostic dans l’espace de journal Analytique.
3. Permet à la solution de Service Fabric dans l’espace de travail du journal Analytique.
4. Installe l’extension de l’agent MMA dans chaque échelle de machine virtuelle définie dans votre cluster Service Fabric. Avec l’agent MMA installé, vous êtes en mesure d’afficher les mesures de performances sur vos nœuds de.


[![Déployer vers Azure](./media/log-analytics-service-fabric/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fazure-quickstart-templates%2Fmaster%2Fservice-fabric-vmss-oms%2F%2Fazuredeploy.json)


Suivant les mêmes étapes ci-dessus, les paramètres de saisie et lancer un déploiement. Une fois de plus, vous devriez voir le nouvel espace de travail, les clusters et les tables WAD créés tous les :

![TISSU de service](./media/log-analytics-service-fabric/5.png)

###<a name="viewing-performance-data"></a>Affichage des données de performances

Pour afficher les données de performance à partir de vos nœuds :
</br>
- Lancer l’espace de travail du journal Analytique à partir du portail Azure.

![TISSU de service](./media/log-analytics-service-fabric/6.png)

- Pointez sur paramètres dans le volet de gauche, puis sélectionnez les données >> les compteurs de Performance Windows >> « Ajouter des compteurs de performance sélectionnés » : ![Service de Fabric](./media/log-analytics-service-fabric/7.png)

- Dans recherche, utilisez les requêtes suivantes pour plonger dans les statistiques clés sur les nœuds :
</br>

    une barre d’outils. Comparer l’utilisation CPU moyenne sur tous les nœuds de votre dans la dernière heure pour afficher les nœuds qui rencontrent des problèmes et à quel intervalle de temps, un nœud avait un pic :

    ``` Type=Perf ObjectName=Processor CounterName="% Processor Time"|measure avg(CounterValue) by Computer Interval 1HOUR. ```

    ![TISSU de service](./media/log-analytics-service-fabric/10.png)


    b. Afficher les graphiques en courbes similaire pour la mémoire disponible sur chaque nœud de cette requête :

    ```Type=Perf ObjectName=Memory CounterName="Available MBytes Memory" | measure avg(CounterValue) by Computer Interval 1HOUR.```

    Pour afficher une liste de tous les nœuds, montrant la valeur moyenne exacte de méga-octets disponibles pour chaque nœud, utilisez cette requête :

    ```Type=Perf (ObjectName=Memory) (CounterName="Available MBytes") | measure avg(CounterValue) by Computer ```

    ![TISSU de service](./media/log-analytics-service-fabric/11.png)


    c. Dans le cas où vous voulez approfondir un nœud spécifique en examinant la moyenne horaire, l’utilisation de l’UC minimale, maximale et 75-centile, vous ne pouvez effectuer cette opération à l’aide de cette requête (remplacez le champ de l’ordinateur) :

    ```Type=Perf CounterName="% Processor Time" InstanceName=_Total Computer="BaconDC01.BaconLand.com"| measure min(CounterValue), avg(CounterValue), percentile75(CounterValue), max(CounterValue) by Computer Interval 1HOUR```

    ![TISSU de service](./media/log-analytics-service-fabric/12.png)

    En savoir plus sur les mesures de performances dans le journal les Analytique [here]. (https://blogs.technet.microsoft.com/msoms/tag/metrics/)


##<a name="adding-an-existing-storage-account-to-log-analytics"></a>Ajout d’un compte de stockage existant pour l’Analytique de journal

Ce modèle ajoute simplement vos comptes de stockage existant à un espace de travail existant ou nouveau journal Analytique.
</br>

[![Déployer vers Azure](./media/log-analytics-service-fabric/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Foms-existing-storage-account%2Fazuredeploy.json)

>[AZURE.NOTE] Pour sélectionner un groupe de ressources, si vous travaillez avec un espace de travail Analytique du journal déjà existant, sélectionnez « Utiliser existant » et recherche pour le groupe de ressources contenant l’espace de travail de l’OMS. Créez une nouvelle un if dans le cas contraire.
![TISSU de service](./media/log-analytics-service-fabric/8.png)

Une fois que ce modèle a été déployé, vous serez en mesure de voir le compte de stockage connecté à votre espace de travail du journal Analytique. Dans ce cas, j’ai ajouté un compte de stockage plus à l’espace de travail Exchange créé ci-dessus.
![TISSU de service](./media/log-analytics-service-fabric/9.png)

## <a name="view-service-fabric-events"></a>Afficher les événements de Service Fabric

Une fois les déploiements sont terminées et la solution de Fabric du Service a été activée dans votre espace de travail, sélectionnez la mosaïque **Fabric du Service** dans le portail du journal Analytique pour lancer le tableau de bord de Service Fabric. Le tableau de bord inclut les colonnes dans le tableau suivant. Chaque colonne répertorie les événements de dix supérieur en nombre correspondant aux critères de cette colonne pour la plage de temps spécifiée. Vous pouvez exécuter une recherche de journal qui fournit la liste complète en cliquant sur **Afficher tout** en bas, à droite de chaque colonne, ou en cliquant sur l’en-tête de colonne.

| **Événements de service Fabric** | **Description** |
| --- | --- |
| Problèmes | Un affichage des problèmes de RunAsyncFailures RunAsynCancellations et le nœud downloads. |
| Événements opérationnels | Événements opérationnels importantes telles que la mise à niveau de l’application et de déploiement. |
| Événements de Service fiable | Une tel Runasyncinvocations les événements notables de service fiable. |
| Événements de l’acteur | Acteur notables générés par votre micro-services, tels que les exceptions levées par une méthode d’acteur, acteur activations et désactivations et ainsi de suite. |
| Événements d’application | Tous les événements ETW personnalisés générés par vos applications. |

![Tableau de bord de service Fabric](./media/log-analytics-service-fabric/sf3.png)

![Tableau de bord de service Fabric](./media/log-analytics-service-fabric/sf4.png)


Le tableau suivant indique les méthodes de collecte de données et d’autres détails sur la manière dont les données sont collectées pour Service Fabric.

| plate-forme | Agent direct | Agent SCOM | Stockage Azure | SCOM requis ? | Données de l’agent SCOM envoyées par groupe d’administration | fréquence de collection |
|---|---|---|---|---|---|---|
|Windows|![N°](./media/log-analytics-malware/oms-bullet-red.png)|![N°](./media/log-analytics-malware/oms-bullet-red.png)| ![Oui](./media/log-analytics-malware/oms-bullet-green.png)|            ![N°](./media/log-analytics-malware/oms-bullet-red.png)|![N°](./media/log-analytics-malware/oms-bullet-red.png)|10 minutes |


>[AZURE.NOTE] Vous pouvez modifier la portée de ces événements dans la solution de Service Fabric en cliquant sur les **données basées sur les 7 derniers jours** en haut du tableau de bord. Vous pouvez également afficher les événements générés dans les 7 derniers jours, 1 jour, ou six heures. Ou bien, vous pouvez sélectionner **personnalisée** pour spécifier une plage de dates personnalisée.


## <a name="next-steps"></a>Étapes suivantes

- [Recherche de journal de journal Analytique](log-analytics-log-searches.md) permet d’afficher les données détaillées de l’événement Service Fabric.
