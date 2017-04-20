<properties
    pageTitle="Solution de gestion de capacité de journal Analytique | Microsoft Azure"
    description="Vous pouvez utiliser la solution de planification de la capacité dans journal Analytique pour vous aider à comprendre la capacité de vos serveurs Hyper-V gérés par System Center Virtual Machine Manager"
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="banders"/>

# <a name="capacity-management-solution-in-log-analytics"></a>Solution de gestion de capacité de journal Analytique


Vous pouvez utiliser la solution de planification de la capacité dans journal Analytique pour vous aider à comprendre la capacité de vos serveurs Hyper-V gérés par System Center Virtual Machine Manager. Cette solution nécessite de System Center Operations Manager et System Center Virtual Machine Manager. Planification de la capacité n’est pas disponible si vous utilisez uniquement les agents directement connecté. Vous installez la solution pour la mise à jour de l’agent Operations Manager. La solution lit les compteurs de performance sur le serveur analysé et envoie les données d’utilisation au service OMS dans le nuage pour traitement. La logique est appliquée pour les données d’utilisation et le service en nuage enregistre les données. Au fil du temps, des modèles d’utilisation sont identifiés et capacité est projetée, en fonction de la consommation de courant.

Par exemple, une projection peut identifier lorsque de la mémoire supplémentaire ou cœurs de processeurs supplémentaires est nécessaires pour un serveur individuel. Dans cet exemple, la projection peut indiquer que des 30 derniers jours le serveur devra davantage de mémoire. Cela peut vous aider à planifier une mise à niveau de la mémoire au cours de la fenêtre de maintenance suivante du serveur, qui peut se produire une fois toutes les deux semaines.

>[AZURE.NOTE] La solution de gestion de la capacité n’est pas disponible pour être ajouté à des espaces de travail. Les clients qui ont installé la solution de gestion de capacité peuvent continuer à utiliser la solution.  

Solution de planification de la capacité est en cours de mise à jour pour résoudre le client suivant a signalé défis :

- Obligation d’utiliser Virtual Machine Manager et Operations Manager
- Impossibilité de personnaliser ou un filtre en fonction des groupes
- Agrégation des données horaires ne fréquentes pas assez
- Aucun aperçu au niveau machine virtuelle
- Fiabilité des données

Avantages de la nouvelle solution de capacité :

- Prise en charge de la collecte de données précises avec une précision et une fiabilité améliorée
- Prise en charge Hyper-V sans nécessiter de VMM
- Visualisation des mesures de PowerBI
- Conseils sur l’utilisation du niveau de machine virtuelle


## <a name="installing-and-configuring-the-solution"></a>L’installation et la configuration de la solution
Utilisez les informations suivantes pour installer et configurer la solution.

- Operations Manager est requis pour la solution de gestion de la capacité.
- Virtual Machine Manager est requis pour la solution de gestion de la capacité.
- Operations Manager connectivité avec Virtual Machine Manager (VMM) est requise. Pour plus d’informations sur la connexion des systèmes, voir [comment connecter de VMM avec Operations Manager](http://technet.microsoft.com/library/hh882396.aspx).
- Gestionnaire des opérations doit être connecté au journal Analytique.
- Ajouter la solution de gestion de la capacité de votre espace de travail de l’OMS à l’aide de la procédure décrite dans les [solutions d’Analytique de journal ajouter à partir de la galerie de Solutions](log-analytics-add-solutions.md).  Il n’y a aucune configuration supplémentaire requise.


## <a name="capacity-management-data-collection-details"></a>Capacité détails de collecte de données de gestion

Gestion de la capacité collecte les données de performances, les métadonnées et les données d’état à l’aide d’agents que vous avez activés.

Le tableau suivant indique les méthodes de collecte de données et d’autres détails sur la manière dont les données sont collectées pour la gestion de la capacité.

| plate-forme | Agent direct | Agent SCOM | Stockage Azure | SCOM requis ? | Données de l’agent SCOM envoyées par groupe d’administration | fréquence de collection |
|---|---|---|---|---|---|---|
|Windows|![N°](./media/log-analytics-capacity/oms-bullet-red.png)|![Oui](./media/log-analytics-capacity/oms-bullet-green.png)|![N°](./media/log-analytics-capacity/oms-bullet-red.png)|            ![Oui](./media/log-analytics-capacity/oms-bullet-green.png)|![Oui](./media/log-analytics-capacity/oms-bullet-green.png)| toutes les heures|

Le tableau suivant montre des exemples de types de données collectées par la gestion de la capacité :

|**Type de données**|**Champs**|
|---|---|
|Métadonnées|BaseManagedEntityId, ObjectStatus, unité d’organisation, ActiveDirectoryObjectSid, PhysicalProcessors, NetworkName, IPAddress, ForestDNSName, Nom_ordinateur_netbios, VirtualMachineName, LastInventoryDate, HostServerNameIsVirtualMachine, adresse IP, NetbiosDomainName, LogicalProcessors, NomDNS, DisplayName, DomainDnsName, ActiveDirectorySite, au PrincipalName, OffsetInMinuteFromGreenwichTime|
|Performances|NomObjet, CounterName, PerfmonInstanceName, PerformanceDataId, PerformanceSourceInternalID, SampleValue, TimeSampled, TimeAdded|
|État|StateChangeEventId, StateId, NewHealthState, OldHealthState, contexte, TimeGenerated, TimeAdded, StateId2, BaseManagedEntityId, MonitorId, HealthState, LastModified, LastGreenAlertGenerated, DatabaseTimeModified|

## <a name="capacity-management-page"></a>Page de gestion de capacité


 Après l’installation de la solution de planification de la capacité, vous pouvez afficher la capacité de vos serveurs surveillés à l’aide de la mosaïque de la **Planification de la capacité** sur la page de **vue d’ensemble** de l’OMS.

![image de mosaïque de planification de la capacité](./media/log-analytics-capacity/oms-capacity01.png)

La mosaïque ouvre le tableau de bord de **Gestion de la capacité** dans laquelle vous pouvez afficher un résumé de la capacité de votre serveur. La page affiche les mosaïques suivantes que vous pouvez cliquer sur :

- *Nombre de machine virtuelle*: affiche le nombre de jours restants pour la capacité de machines virtuelles
- *Calculer*: affiche les cœurs de processeur et la mémoire disponible
- *Stockage*: affiche l’espace disque utilisé et la latence de disque moyenne
- *Recherche*: l’Explorateur de données que vous pouvez utiliser pour rechercher des données dans le système de l’OMS

![image du tableau de bord de gestion de la capacité](./media/log-analytics-capacity/oms-capacity02.png)


### <a name="to-view-a-capacity-page"></a>Pour afficher une page de capacité

- Dans la page **vue d’ensemble** , cliquez sur **Gestion de la capacité**, puis cliquez sur **Calculer** ou **stockage**.

## <a name="compute-page"></a>Page de calcul

Vous pouvez utiliser le tableau de bord **de calcul** dans Microsoft Azure OMS pour afficher les informations de capacité à propos de l’utilisation projetée des jours de la capacité et d’efficacité liés à votre infrastructure. La zone **d’utilisation** vous permet d’afficher le noyau et mémoire UC dans vos hôtes d’ordinateur virtuel. Vous pouvez utiliser l’outil de projection d’estimer la capacité doit être disponible pour une période donnée. Vous pouvez utiliser la zone **d’efficacité** pour voir l’efficacité de vos hôtes d’ordinateur virtuel. Vous pouvez afficher des détails sur les articles liés en cliquant dessus.

Vous pouvez générer un classeur Excel pour les catégories suivantes :

- Hôtes supérieur avec une utilisation plus élevée core
- Hôtes supérieur avec une utilisation plus élevée de la mémoire
- Hôtes principaux avec des machines virtuelles inefficaces
- Principales vues par utilisation
- Hôtes de bas par utilisation

![image de la page de gestion de capacité Compute](./media/log-analytics-capacity/oms-capacity03.png)


Les domaines suivants sont affichés sur le tableau de bord **de calcul** :

**Utilisation**: affichage du processeur principal et la capacité mémoire dans vos hôtes d’ordinateur virtuel.

- *Utiliser les cœurs*: somme, pour tous les hôtes (% de l’utilisation du processeur multiplié par le nombre de cœurs physiques sur l’hôte).
- *Noyaux libre*: nombre Total de cœurs physiques moins MANDRINS usagés.
- *Pourcentage de cœurs disponibles*: libérer des cœurs physiques, divisés par le nombre total de cœurs physiques.
- *Noyaux virtuels par machine virtuelle*: nombre Total de noyaux virtuels dans le système divisé par le nombre total d’ordinateurs virtuels dans le système.
- *Noyaux virtuels rapport de cœurs physiques*: rapport de cœurs physiques total de cœurs physiques qui sont utilisés par les ordinateurs virtuels dans le système.
- *Nombre de cœurs de virtuel disponible*: virtuel principal rapport de cœurs physiques multiplié par les cœurs physiques disponibles.
- *Mémoire utilisée*: somme de la mémoire qui est utilisée par tous les hôtes.
- *Libérer de la mémoire*: mémoire utilisée de moins de mémoire physique totale.
- *Pourcentage de mémoire disponible*: libérer de la mémoire physique divisé par la quantité totale de mémoire physique.
- *Mémoire virtuelle par la machine virtuelle*: Total de mémoire virtuelle dans le système divisé par le nombre total d’ordinateurs virtuels dans le système.
- *Mémoire virtuelle rapport de mémoire physique*: Total de mémoire virtuelle dans le système divisé par la quantité totale de mémoire physique dans le système.
- *Mémoire virtuelle disponible*: mémoire virtuelle au rapport entre la mémoire physique est multipliée par la mémoire physique disponible.

**Outil de projection**

À l’aide de l’outil de projection, vous pouvez afficher des tendances historiques pour l’utilisation de vos ressources. Cela inclut les tendances d’utilisation pour les machines virtuelles, de mémoire, de noyau et de stockage. La capacité de projection utilise un algorithme de projection pour vous aider à connaître lors de l’exécution de chacune des ressources. Cela vous permet de calculer la planification appropriée afin que vous sachiez lorsque vous avez besoin d’acheter plus de capacité (par exemple, mémoire, stockage ou cœurs).

**Efficacité**

- *Inactivité de machine virtuelle*: à l’aide de moins de 10 % de la mémoire du processeur et de 10 % pour la période spécifiée.
- *VM de surutilisée*: à l’aide de plus de 90 % de la mémoire du processeur et de 90 % pour la période spécifiée.
- *Hôte d’inactivité*: à l’aide de moins de 10 % de la mémoire du processeur et de 10 % pour la période spécifiée.
- *Hôte de surutilisée*: à l’aide de plus de 90 % de la mémoire du processeur et de 90 % pour la période spécifiée.

### <a name="to-work-with-items-on-the-compute-page"></a>Pour travailler avec des éléments sur la page de calcul

1. Sur le tableau de bord **de calcul** , dans la zone **d’utilisation** , permet d’afficher les informations de capacité sur les cœurs de processeur et la mémoire en cours d’utilisation.
2. Cliquez sur un élément pour l’ouvrir dans la page de **recherche** et d’afficher des informations détaillées le concernant.
3. Dans l’outil de **Projection** , déplacez le curseur de la date pour afficher une projection de la capacité qui sera utilisée à la date que vous choisissez.
4. Dans la zone de **l’efficacité** , permet d’afficher des informations de l’efficacité de capacité sur les machines virtuelles et les hôtes d’ordinateurs virtuels.

## <a name="direct-attached-storage-page"></a>Page de stockage en attachement direct

Vous pouvez utiliser le tableau de bord du **Stockage en attachement Direct** dans OMS pour afficher les informations de capacité des jours prévus de capacité de disque, les performances du disque et l’utilisation du stockage. La zone **d’utilisation** vous permet d’afficher l’espace disque de vos hôtes d’ordinateur virtuel. Vous pouvez utiliser la zone **Performances de disque** pour afficher le débit du disque et la latence dans vos hôtes d’ordinateur virtuel. Vous pouvez également utiliser l’outil de projection d’estimer la capacité doit être disponible pour une période donnée. Vous pouvez afficher des détails sur les articles liés en cliquant dessus.

Vous pouvez générer un classeur Excel à partir de ces informations de capacité pour les catégories suivantes :

- Utilisation de l’espace disque supérieur par hôte
- Latence moyenne supérieure par hôte

Les zones suivantes sont affichées sur la page de **stockage** :

- *Utilisation*: permet d’afficher l’espace disque de vos hôtes d’ordinateur virtuel.
- *Espace disque total*: somme (espace disque logique) pour tous les hôtes
- *L’espace disque utilisé*: somme (espace disque logique utilisé) pour tous les hôtes
- *Espace disque disponible*: espace disque moins d’espace disque utilisé Total
- *Pourcentage disque utilisé*: espace disque divisé par l’espace disque total utilisé
- *Pourcentage disque disponible*: espace disque disponible est divisée par l’espace disque total

![image de la page de stockage à attachement Direct capacité gestion](./media/log-analytics-capacity/oms-capacity04.png)

**Performances de disque**

À l’aide d’OMS, vous pouvez afficher la tendance historique de l’utilisation de l’espace disque. La capacité de projection utilise un algorithme pour une utilisation future du projet. Pour l’utilisation de l’espace, notamment la capacité de projection vous permet de projet lorsque vous risquez de manquer d’espace disque. Cela vous permettra de planifier le stockage approprié et savoir lorsque vous avez besoin d’acheter plus de stockage.

**Outil de projection**

À l’aide de l’outil de projection, vous pouvez afficher des tendances historiques pour l’utilisation d’espace disque. La capacité de projection vous permet également de projet lorsque vous manquez d’espace disque. Cela vous permettra de planifier la capacité appropriée et lorsque vous devez acheter de la capacité de stockage plus.

### <a name="to-work-with-items-on-the-direct-attached-storage-page"></a>Pour travailler avec des éléments sur la page de stockage en attachement Direct

1. Sur le tableau de bord du **Stockage en attachement Direct** , dans la zone **d’utilisation** , vous pouvez afficher les informations de l’utilisation du disque.
2. Cliquez sur un élément lié pour l’ouvrir dans la page de **recherche** et d’afficher des informations détaillées le concernant.
3. Dans la zone **Performances de disque** , vous pouvez afficher des informations de débit et la latence du disque.
4. Dans l' **outil de Projection**, déplacez le curseur de la date pour afficher une projection de la capacité qui sera utilisée à la date que vous choisissez.


## <a name="next-steps"></a>Étapes suivantes

- [Recherche de journal de journal Analytique](log-analytics-log-searches.md) permet d’afficher les données de gestion détaillée de la capacité.
