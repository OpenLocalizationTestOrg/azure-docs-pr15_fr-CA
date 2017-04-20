<properties
   pageTitle="Moniteur de dépendance de l’application (ADM) dans la Suite de gestion des opérations (OMS) | Microsoft Azure"
   description="Moniteur de dépendance d’application (ADM) est une solution de Suite de gestion des opérations (OMS) qui détecte les composants de l’application sur les systèmes Windows et Linux et mappe la communication entre services automatiquement.  Cet article fournit des détails pour le déploiement des ADM dans votre environnement et de l’utiliser dans une variété de scénarios."
   services="operations-management-suite"
   documentationCenter=""
   authors="daseidma"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/28/2016"
   ms.author="daseidma;bwren" />

# <a name="using-application-dependency-monitor-solution-in-operations-management-suite-oms"></a>À l’aide de solutions de moniteur de dépendance d’Application dans Operations Management Suite (OMS)
![Icône de gestion des alerte](media/operations-management-suite-application-dependency-monitor/icon.png) Moniteur de dépendance d’application (ADM) détecte les composants de l’application sur les systèmes Windows et Linux et mappe la communication entre services automatiquement. Il vous permet d’afficher vos serveurs, que vous en pensez – comme les systèmes interconnectés qui offrent des services critiques.  Moniteur de dépendance d’application affiche les connexions entre les serveurs, les processus, et les ports sur n’importe quelle architecture de connexion TCP sans configuration requis autre que l’installation d’un agent.

Cet article décrit les détails de l’utilisation du moniteur de dépendances d’Application.  Pour plus d’informations sur la configuration des agents ADM et d’intégration, voir [Moniteur de dépendance Application de configuration de solution dans la Suite Gestion des opérations (OMS)](operations-management-suite-application-dependency-monitor-configure.md)

>[AZURE.NOTE]Moniteur de dépendance d’application est en cours d’aperçu privé.  Vous pouvez demander l’accès à l’aperçu privé d’ADM à [https://aka.ms/getadm](https://aka.ms/getadm).
>
>Lors de l’aperçu privé, tous les comptes de l’OMS ont un accès illimité à ADM.  Nœuds d’ADM sont gratuites, mais comme toute autre solution de dosage données Analytique de journal pour les types AdmComputer_CL et AdmProcess_CL.
>
>Après que l’ADM entrée public preview, il sera disponible uniquement pour les clients de Insight & Analytique dans le Plan de tarification OMS gratuites et payantes.  Comptes de couche libre sera limitées à 5 nœuds ADM.  Si vous participez à l’aperçu privé et ne sont pas inscrits dans le Plan de tarification OMS lorsqu’ADM entre la version d’évaluation, ADM sera désactivée à ce moment-là. 


## <a name="use-cases-make-your-it-processes-dependency-aware"></a>Exemples d’utilisation : Rendre votre infrastructure informatique prenant en charge de la dépendance de processus

### <a name="discovery"></a>Découverte
ADM crée automatiquement un plan de référence commune de dépendances entre vos serveurs, les processus et les services tiers 3e.  Il découvre et mappe toutes les dépendances TCP, identification des connexions surprise, à distance des systèmes tiers 3e vous dépendez d’et les dépendances pour les zones sombres traditionnelles de votre réseau tels que DNS et Active Directory.  ADM découvre les connexions ayant échoué vos systèmes gérés, essayez d’établir, pour vous aider à identifier le potentiel problème de configuration du serveur, des interruptions de service et des problèmes de réseau.

### <a name="incident-management"></a>Gestion des incidents
ADM permet la devinette de problèmes en affichant la manière dont les systèmes sont connectés et affectant les uns des autres.  En plus d’un échec de connexion, d’informations sur les clients connectés aider à identifier les équilibreurs de charge de mal configurés, charge surprenant ou excessive sur les services critiques et de non fiables des clients tels que les ordinateurs des développeurs parler aux systèmes de production.  Workflows intégrés avec OMS modifier le suivi vous permet également de voir si un événement de modification sur un ordinateur principal ou service explique la cause d’un incident.

### <a name="migration-assurance"></a>Assurance de migration
ADM vous permet de planifier efficacement, accélérer et valider les migrations Azure, permet de s’assurer que rien n’est laissé et il n’y a aucune interruption subite.  Vous pouvez découvrir tous les systèmes interdépendants qui doivent migrer en même temps, évaluer la capacité et configuration du système et déterminer si un système en cours d’exécution est un candidat pour le déclassement et non migration ou sert toujours des utilisateurs.  Une fois le déplacement effectué, vous pouvez vérifier sur la charge du client et d’identité pour vérifier que les systèmes de test et les clients sont connectent.  Si vos définitions de pare-feu et de la planification de sous-réseau ont des problèmes, un échec de connexion dans les mappages d’ADM pointera vers les systèmes nécessitant une connectivité.

### <a name="business-continuity"></a>Continuité d’activité
Si vous utilisez la restauration du Site Azure et devez aide définissant la séquence de restauration de votre environnement d’application, ADM peut automatiquement vous montrer comment les systèmes s’appuient sur d’autres pour vous assurer que votre plan de récupération est fiable.  En choisissant un serveur critique et en affichant ses clients, vous pouvez identifier les systèmes frontaux qui doivent être récupérées uniquement après que ce serveur critique est restaurée et disponible.  À l’inverse, en examinant les dépendances du back-end d’un serveur critique, vous pouvez identifier les systèmes qui doivent être restaurées avant la restauration de votre système de focus.

### <a name="patch-management"></a>Gestion des correctifs
ADM améliore votre utilisation d’évaluation de mise à jour système OMS en vous montrant qui autres équipes et les serveurs dépendent de votre service, et vous pouvez les notifier à l’avance avant de prendre vos systèmes vers le bas pour la correction.  ADM améliore également la gestion des correctifs dans OMS en vous montrant si vos services sont correctement connectés et disponibles une fois qu’ils sont corrigés et redémarrés. 


## <a name="mapping-overview"></a>Vue d’ensemble de la mise en correspondance
Agents d’ADM collecter des informations sur tous les processus de connexion TCP sur le serveur où ils sont installés, ainsi que les détails sur les connexions entrantes et sortantes pour chaque processus.  Ordinateurs avec des agents ADM peuvent être sélectionnés à l’aide de la liste des ordinateurs sur le côté gauche de la solution ADM, visualiser leurs dépendances sur une plage de temps sélectionnée.  Dépendance de machine mappe le focus sur un ordinateur spécifique et afficher tous les ordinateurs qui sont les clients TCP directs ou des serveurs de l’ordinateur.

![Vue d’ensemble de l’ADM](media/operations-management-suite-application-dependency-monitor/adm-overview.png)

Machines peuvent être développés dans le plan pour afficher les processus en cours d’exécution avec les connexions réseau actives pendant la période sélectionnée.  Lorsqu’un ordinateur distant avec un agent ADM est développé pour afficher les détails du processus, que les processus de communication avec l’ordinateur de focus sont affichés.  Le nombre de machines de front-end sans agent se connectant à la machine du focus est indiqué sur le côté gauche des processus qu’ils se connectent à.  Si l’ordinateur actif effectue une connexion à un ordinateur principal sans agent, que back-end est représentée par un nœud dans le plan qui indique son adresse IPv4 et le nœud peut être développé pour afficher les ports individuels et les services qui communique avec l’ordinateur de focus.

Par défaut, les cartes ADM affichent les dix dernières minutes d’informations de dépendance.  Utilisez les contrôles dans le coin supérieur gauche, cartes peuvent être interrogés pour les plages horaires historiques, jusqu'à à l’échelle une heure, pour montrer comment les dépendances recherché dans le passé, par exemple pendant un incident ou avant une modification s’est produite.    ADM sont sauvegardées pendant 30 jours dans des espaces de travail payés et pendant 7 jours dans des espaces de travail libres.

![Carte d’ordinateur avec les propriétés de l’ordinateur sélectionné](media/operations-management-suite-application-dependency-monitor/machine-map.png)

## <a name="failed-connections"></a>Échec de connexion
Échec de connexion est affiché dans les cartes de l’ADM des processus et des ordinateurs, avec un affichage de ligne rouge en pointillés si un système client ne peut pas atteindre un processus ou un port.  Échec de connexion est signalé à partir de n’importe quel système avec un agent ADM déployé si ce système est celui qui tente la connexion a échoué.  ADM cette mesure en observant les sockets TCP qui ne parviennent pas à établir une connexion.  Cela peut provenir d’un pare-feu, une erreur de configuration dans le client ou serveur ou un service à distance n’est pas disponible. 

![Échec de connexion](media/operations-management-suite-application-dependency-monitor/failed-connections.png)

Présentation des échecs de connexion peuvent aider avec le dépannage, la validation de la migration, l’analyse de la sécurité et présentation architecturale globale.  Parfois impossible de connexions sont inoffensifs, mais ils pointent souvent directement à un problème, tel qu’un environnement de basculement soudainement devenir inaccessible,.. .ou deux niveaux de l’application n’est pas en mesure de communiquer avec après une migration de nuage.  Dans l’image ci-dessus, IIS et WebSphere sont tous deux en cours d’exécution, mais ils ne peuvent pas se connecter. 

## <a name="computer-and-process-properties"></a>Ordinateur et propriétés de processus
Lors de la navigation d’un plan d’ADM, vous pouvez sélectionner des machines et des processus pour obtenir le contexte supplémentaire relatif à leurs propriétés.  Ordinateurs fournissent des informations sur le nom DNS, adresses IPv4, capacité du processeur et la mémoire, Type de machine virtuelle, version du système d’exploitation, temps de redémarrage de dernière et les ID de leurs agents OMS et ADM.

Détails de processus sont recueillies à partir des métadonnées de système d’exploitation sur le processus, y compris le nom du processus, la description, nom d’utilisateur et domaine (sous Windows), nom de la société, nom du produit, version du produit, répertoire de travail, ligne de commande et démarrage du processus en cours d’exécution.

![Propriétés du processus](media/operations-management-suite-application-dependency-monitor/process-properties.png)

Le panneau Résumé de processus fournit des informations supplémentaires sur la connectivité de ce processus, y compris ses ports liés, les connexions entrantes et sortantes et l’échec des connexions. 

![Résumé du processus](media/operations-management-suite-application-dependency-monitor/process-summary.png)

## <a name="oms-change-tracking-integration"></a>Intégration de suivi des modifications OMS
Intégration d’ADM avec suivi des modifications est automatique lorsque les deux solutions sont activées et configurées dans votre espace de travail de l’OMS.

Le panneau Résumé de Machine indique si les événements de suivi des modifications sont sont produites sur la machine sélectionnée au cours de la période sélectionnée.

![Panneau récapitulatif de machine](media/operations-management-suite-application-dependency-monitor/machine-summary.png)

Le panneau de suivi de modifications Machine affiche une liste de toutes les modifications, dont le plus récent en premier, ainsi qu’un lien pour effectuer un zoom avant dans la recherche du journal pour plus de détails.
![Panneau de suivi de modification de machine](media/operations-management-suite-application-dependency-monitor/machine-change-tracking.png)

Voici une vue d’événement de modification de la Configuration détaillée après avoir sélectionné **afficher dans journal Analytique**.
![Événement de modification de configuration](media/operations-management-suite-application-dependency-monitor/configuration-change-event.png)


## <a name="log-analytics-records"></a>Enregistrements de journal Analytique
Ordinateur et processus de données d’inventaire ADM sont disponibles pour la [recherche](../log-analytics/log-analytics-log-searches.md) dans le journal Analytique.  Cela peut être appliqué aux scénarios, y compris la planification de la migration, analyse de la capacité, la découverte et la résolution des problèmes de performances ad hoc. 

Un enregistrement est généré par heure pour chaque ordinateur unique et les processus en plus des enregistrements généré lorsque que les processus ou ordinateur démarre ou est sur-arraisonné à ADM.  Ces enregistrements ont les propriétés dans les tables suivantes. 

Il existe des propriétés générées en interne, vous pouvez utiliser pour identifier les ordinateurs et processus uniques :

- PersistentKey_s est défini de manière unique par la configuration du processus, par exemple en ligne de commande et de l’utilisateur ID.  Il est unique pour un ordinateur donné, mais peut être répétée sur les ordinateurs.
- ProcessId_s et ComputerId_s sont globalement uniques dans le modèle ADM.



### <a name="admcomputercl-records"></a>Enregistrements de AdmComputer_CL
Enregistrements avec un type de **AdmComputer_CL** ont des données d’inventaire pour les serveurs avec les agents de l’ADM.  Ces enregistrements ont les propriétés dans le tableau suivant.  

| Propriété | Description |
|:--|:--|
| Type de | *AdmComputer_CL* |
| SourceSystem | *OpsManager* |
| ComputerName_s | Nom de l’ordinateur Windows ou Linux |
| CPUSpeed_d | Vitesse du processeur en MHz |
| DnsNames_s | Liste de tous les noms DNS pour cet ordinateur |
| IPv4s_s | Liste de toutes les adresses IPv4 utilisé par cet ordinateur. |
| IPv6s_s | Liste de toutes les adresses IPv6 utilisée par cet ordinateur.  (ADM identifie les adresses IPv6 mais ne découvre pas de dépendances de IPv6). |
| Is64Bit_b | valeur True ou false selon le type de système d’exploitation |
| MachineId_s | Un GUID interne, unique dans un espace de travail de l’OMS  |
| OperatingSystemFamily_s | Windows ou Linux |
| OperatingSystemVersion_s | Longue chaîne de version du système d’exploitation |
| TimeGenerated | Date et heure auxquelles l’enregistrement a été créé. |
| TotalCPUs_d | Nombre de coeurs de processeur |
| TotalPhysicalMemory_d | Capacité de mémoire en Mo |
| VirtualMachine_b | valeur True ou false selon que le système d’exploitation est un invité d’ordinateur virtuel |
| VirtualMachineID_g | ID d’ordinateur virtuel Hyper-V |
| VirtualMachineName_g | Nom de l’ordinateur virtuel Hyper-V |
| VirtualMachineType_s | HyperV, Vmware, Xen, Kvm, Ldom, Lpar, Virtual PC utilisera |


### <a name="admprocesscl-type-records"></a>Enregistrements de Type de AdmProcess_CL 
Enregistrements avec un type de **AdmProcess_CL** ont des données d’inventaire pour le processus de connexion TCP sur des serveurs avec des agents de l’ADM.  Ces enregistrements ont les propriétés dans le tableau suivant.

| Propriété | Description |
|:--|:--|
| Type de | *AdmProcess_CL* |
| SourceSystem | *OpsManager* |
| CommandLine_s | Ligne de commande complète du processus |
| CompanyName_s | Nom de la société (à partir de Windows PE ou Linux RPM) |
| Description_s | Description de processus long (à partir de Windows PE ou Linux RPM) |
| FileVersion_s | Version du fichier exécutable (à partir de Windows PE, Windows uniquement) |
| FirstPid_d | ID de processus du système d’exploitation |
| InternalName_s | Nom du fichier exécutable interne (à partir de Windows PE, Windows uniquement) |
| MachineId_s | GUID interne unique dans un espace de travail de l’OMS  |
| Name_s | Le nom de fichier exécutable du processus |
| Path_s | Chemin d’accès du système de fichier de l’exécutable du processus |
| PersistentKey_s | GUID interne unique au sein de cet ordinateur |
| PoolId_d | ID interne de l’agrégation des processus basés sur les lignes de commande similaires. |
| ProcessId_s | GUID interne unique dans un espace de travail de l’OMS  |
| ProductName_s | Chaîne de nom de produit (à partir de Windows PE ou Linux RPM) |
| ProductVersion_s | Chaîne de version du produit (à partir de Windows PE ou Linux RPM) |
| StartTime_t | Heure de début de processus sur l’horloge de l’ordinateur local |
| TimeGenerated | Date et heure auxquelles l’enregistrement a été créé. |
| UserDomain_s | Domaine du propriétaire du processus (Windows uniquement) |
| UserName_s | Nom du propriétaire du processus (Windows uniquement) |
| WorkingDirectory_s | Répertoire de travail de processus |


## <a name="sample-log-searches"></a>Recherches de journaux d’exemple

### <a name="list-the-physical-memory-capacity-of-all-managed-computers"></a>Liste de la capacité de mémoire physique de tous les ordinateurs gérés. 
Type = AdmComputer_CL | Sélectionnez TotalPhysicalMemory_d, ComputerName_s | ComputerName_s de Dedup

![Exemple de requête ADM](media/operations-management-suite-application-dependency-monitor/adm-example-01.png)

### <a name="list-computer-name-dns-ip-and-os-version"></a>Nom d’ordinateur de liste, la version du système d’exploitation, IP et DNS.
Type = AdmComputer_CL | Sélectionnez ComputerName_s, OperatingSystemVersion_s, DnsNames_s, IPv4s_s | dedup ComputerName_s

![Exemple de requête ADM](media/operations-management-suite-application-dependency-monitor/adm-example-02.png)

### <a name="find-all-processes-with-sql-in-the-command-line"></a>Rechercher tous les processus avec « sql » dans la ligne de commande
Type = AdmProcess_CL CommandLine_s = \*sql\* | dedup ProcessId_s

![Exemple de requête ADM](media/operations-management-suite-application-dependency-monitor/adm-example-03.png)

### <a name="after-viewing-event-data-for-given-process-use-its-machine-id-to-retrieve-the-computers-name"></a>Après avoir affiché les données d’événement pour un processus, son ID d’ordinateur permet de récupérer le nom de l’ordinateur
Type = « m!m-9bb187fa-e522-5f73-66d2-211164dc4e2b » de AdmComputer_CL | ComputerName_s distincte

![Exemple de requête ADM](media/operations-management-suite-application-dependency-monitor/adm-example-04.png)

### <a name="list-all-computers-running-sql"></a>Liste de tous les ordinateurs exécutant SQL
Type = AdmComputer_CL MachineId_s IN {Type = AdmProcess_CL \*sql\* | Distinct MachineId_s} | ComputerName_s distincte

![Exemple de requête ADM](media/operations-management-suite-application-dependency-monitor/adm-example-05.png)

### <a name="list-of-all-unique-product-versions-of-curl-in-my-datacenter"></a>Liste de toutes les versions de produit unique de la courbure dans mon centre de données
Type = AdmProcess_CL Name_s = curl | ProductVersion_s distincte

![Exemple de requête ADM](media/operations-management-suite-application-dependency-monitor/adm-example-06.png)

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>Créer un groupe d’ordinateurs de tous les ordinateurs exécutant CentOS

![Exemple de requête ADM](media/operations-management-suite-application-dependency-monitor/adm-example-07.png)



## <a name="diagnostic-and-usage-data"></a>Données de diagnostic et d’utilisation
Microsoft recueille automatiquement les données d’utilisation et de performances par le biais de votre utilisation du service Moniteur de dépendances d’Application. Microsoft utilise ces données pour fournir et améliorer la qualité, de sécurité et l’intégrité du service Moniteur de dépendances d’Application. Données inclut des informations sur la configuration de vos logiciels, comme le système d’exploitation et de version et inclut également une adresse IP, nom DNS et nom de la station de travail afin de fournir des fonctions de dépannage précises et efficaces. Nous ne collectons pas de noms, des adresses ou autres informations de contact.

Pour plus d’informations sur l’utilisation et de collecte de données, consultez [Déclaration de confidentialité de Microsoft Online Services](hhttps://go.microsoft.com/fwlink/?LinkId=512132).



## <a name="next-steps"></a>Étapes suivantes
- Plus d’informations sur la [session de recherche](../log-analytics/log-analytics-log-searches.md] in Log Analytics to retrieve data collected by Application Dependency Monitor.)
