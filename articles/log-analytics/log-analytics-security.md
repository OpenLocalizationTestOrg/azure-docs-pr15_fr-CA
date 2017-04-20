<properties
    pageTitle="Journal de la sécurité des données Analytique | Microsoft Azure"
    description="Découvrez comment journal Analytique protège la confidentialité de vos informations et sécurise vos données."
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
    ms.date="09/23/2016"
    ms.author="banders"/>

# <a name="log-analytics-data-security"></a>Journal de la sécurité des données Analytique

Microsoft s’engage à protéger votre confidentialité et à la sécurisation de vos données, tout en proposant des logiciels et des services qui vous aident à gérer l’infrastructure informatique de votre organisation. Nous sommes conscients que lorsque vous confier vos données à d’autres personnes, cette approbation nécessite une sécurité rigoureuse. Microsoft respecte les directives de sécurité et de conformité strictes, depuis le codage pour l’exploitation d’un service.

Sécurisation et de protection des données sont une priorité de Microsoft. Veuillez nous contacter avec des questions, des suggestions ou problèmes sur une des informations suivantes, y compris les mesures de sécurité à [Azure prend en charge les options](http://azure.microsoft.com/support/options/).

Cet article explique comment les données sont collectées, traitées et sécurisées par Analytique de journal dans la Suite de gestion des opérations (OMS). Vous pouvez utiliser des agents pour se connecter au service web, utilisez System Center Operations Manager pour collecter des données opérationnelles ou récupérer des données de diagnostics de Windows Azure pour une utilisation par journal Analytique. Les données collectées sont envoyées via Internet à l’aide de l’authentification basée sur les certificats et SSL 3 au service Analytique du journal, qui est hébergé dans Microsoft Azure. Les données sont compressées par l’agent avant de l’envoyer.

Le service de journal Analytique gère vos données en nuage en toute sécurité à l’aide des méthodes suivantes :

- segmentation des données
- rétention des données
- sécurité physique
- gestion des incidents
- respect de la réglementation
- certifications des normes de sécurité


## <a name="data-segregation"></a>Segmentation des données

Les données client sont logiquement séparées sur chaque composant dans le service OMS. Toutes les données est balisé par l’organisation. Ce balisage persiste tout au long du cycle de vie des données, et elle est appliquée au niveau de chaque couche du service. Chaque client possède un blob Azure dédié qui héberge les données à long terme

## <a name="data-retention"></a>Rétention des données

Données de recherche de journal indexées sont stockées et conservées en fonction de votre plan de tarification. Pour plus d’informations, reportez-vous à la section [Analytique de journal de prix](https://azure.microsoft.com/pricing/details/log-analytics/).

Microsoft supprime les données client 30 jours après la fermeture de l’espace de travail de l’OMS. Microsoft supprime également le compte de stockage Azure où résident les données. Lorsque les données du client sont supprimées, aucun des lecteurs physiques ne sont détruits.

Le tableau suivant répertorie quelques-unes des solutions disponibles dans l’OMS et des exemples de types de données qu’ils recueillent.

| **Solution** | **Types de données** |
| --- | --- |
| Évaluation de la configuration | Données de configuration, les métadonnées et les données d’état |
| Planification de la capacité | Métadonnées et les données de performance |
| Contre les logiciels malveillants | Métadonnées et les données de configuration |
| Évaluation du système de mise à jour | Données d’état et de métadonnées |
| Gestion du journal | Défini par l’utilisateur journaux des événements, les journaux des événements Windows ou les journaux IIS |
| Suivi des modifications | Inventaire des logiciels et des métadonnées de Service Windows |
| SQL et évaluation de Active Directory | Afficher les résultats de données WMI, les données du Registre, les données de performances et gestion dynamique de SQL Server |

Le tableau suivant montre des exemples de types de données :

| **Type de données** | **Champs** |
| --- | --- |
| Alerte | Alerte nom, Description de l’alerte, BaseManagedEntityId, identification du problème, IsMonitorAlert, ID de la règle, ResolutionState, priorité, gravité, catégorie, propriétaire, résolu par, heure de déclenchement, TimeAdded, LastModified, LastModifiedBy, LastModifiedExceptRepeatCount, TimeResolved, TimeResolutionStateLastModified, TimeResolutionStateLastModifiedInDB, RepeatCount |
| Configuration de | CustomerID, AgentID, EntityID, ManagedTypeID, ManagedTypePropertyID, CurrentValue, ChangeDate |
| Événement | ID de l’événement, EventOriginalID, BaseManagedEntityInternalId, ID de la règle, PublisherId, PublisherName, FullNumber, numéro, catégorie, ChannelLevel, LoggingComputer, EventData, objets EventParameter, TimeGenerated, TimeAdded <br>**Remarque :** Lorsque vous enregistrez des événements avec des champs personnalisés dans le journal des événements Windows, OMS collecte les. |
| Métadonnées | BaseManagedEntityId, ObjectStatus, unité d’organisation, ActiveDirectoryObjectSid, PhysicalProcessors, NetworkName, IPAddress, ForestDNSName, Nom_ordinateur_netbios, VirtualMachineName, LastInventoryDate, HostServerNameIsVirtualMachine, adresse IP, NetbiosDomainName, LogicalProcessors, NomDNS, DisplayName, DomainDnsName, ActiveDirectorySite, au PrincipalName, OffsetInMinuteFromGreenwichTime |
| Performances | NomObjet, CounterName, PerfmonInstanceName, PerformanceDataId, PerformanceSourceInternalID, SampleValue, TimeSampled, TimeAdded |
| État | StateChangeEventId, StateId, NewHealthState, OldHealthState, contexte, TimeGenerated, TimeAdded, StateId2, BaseManagedEntityId, MonitorId, HealthState, LastModified, LastGreenAlertGenerated, DatabaseTimeModified |

## <a name="physical-security"></a>Sécurité physique

L’Analytique de journal dans le service OMS est constituée par le personnel de Microsoft et toutes les activités sont enregistrées et peuvent être auditées. Le service s’exécute entièrement dans Azure et respecte les critères d’ingénierie communs Azure. Vous pouvez afficher plus d’informations sur la sécurité physique des ressources Azure page 18 de la [Vue d’ensemble de la sécurité Microsoft Azure](http://download.microsoft.com/download/6/0/2/6028B1AE-4AEE-46CE-9187-641DA97FC1EE/Windows%20Azure%20Security%20Overview%20v1.01.pdf). Les droits d’accès physique pour sécuriser les zones sont modifiées au sein d’un jour ouvrable pour toute personne qui n’a plus la responsabilité pour le service OMS, y compris le transfert et l’arrêt. Vous pouvez lire sur l’infrastructure physique global, que nous utilisons à des [Centres de données Microsoft](https://www.microsoft.com/en-us/server-cloud/cloud-os/global-datacenters.aspx).

## <a name="incident-management"></a>Gestion des incidents

OMS a un processus de gestion des incidents qui respectent tous les services Microsoft. Pour résumer, nous avons :

- Utiliser un modèle de responsabilité partagée où une partie de la responsabilité de sécurité appartient Microsoft pendant une partie appartient au client
- Gestion des incidents de sécurité Azure
  - Détecter un incident dès le premier signe, pour démarrer une enquête
  - Évaluez l’impact et la gravité d’un incident par un membre d’équipe de réponse aux incidents d’appel. En fonction de la preuve, l’évaluation peut ou peut ne pas donner davantage d’escalade à l’équipe de réponse sécurité.
  - Diagnostiquer un incident par des experts de réponse de sécurité pour mener l’investigation technique ou légale, d’identifier les stratégies de contenance, des risques et de solution de contournement. Si l’équipe de sécurité pense que les données client ont peuvent être exposées à un individu illégal ou non autorisé, l’exécution en parallèle de la procédure de Notification d’Incident client commence en parallèle.  
  - Stabiliser et récupérer à partir de l’incident. L’équipe de réponse aux incidents crée un plan de récupération pour résoudre le problème. Étapes de relation contenant-contenu de crise tels que la mise en quarantaine des systèmes touchés peuvent se produire immédiatement et en parallèle avec diagnostic. Les atténuations de durée plus longues peuvent être planifiées qui se produisent une fois passé le risque immédiat.  
  - Fermer l’incident et procéder à une analyse post-mortem. L’équipe de réponse aux incidents crée une analyse post-mortem qui met en évidence les détails de l’incident, avec l’intention de modifier les stratégies, procédures et processus pour éviter une récurrence de l’événement.
- Informer les clients des incidents de sécurité
  - Déterminer l’étendue d’impactés clients et à fournir à quiconque est concerné détaillées que possible un avis de
  - Créer un avis pour fournir aux clients de détails suffisamment d’informations afin qu’ils peuvent mener une investigation sur leur fin et les engagements qu’ils ont apportées à leurs utilisateurs finaux lors sans toutefois retarder indûment le processus de notification.
  - Confirmer et déclarer l’incident, si nécessaire.
  - Informer les clients avec une notification d’incident sans retard déraisonnable et conformément à un engagement juridique ou contractuelle. Notification des incidents de sécurité arrive à un ou plusieurs administrateurs de clients de par tout moyen que Microsoft sélectionne, y compris par courrier électronique.
- Dispenser une formation et préparation de l’équipe
  - Personnel de Microsoft est nécessaire à la réalisation de sécurité et la formation de sensibilisation, ce qui leur permet d’identifier et de signaler les problèmes de sécurité potentiel.  
  - Opérateurs travaillant sur le service Microsoft Azure ont des obligations de formation addition autour de leur accès aux systèmes sensibles qui héberge les données client.
  - Personnel de réponse de sécurité Microsoft formation spécialisée pour leurs rôles


En cas de perte des données de n’importe quel client, nous notifier chaque client dans la journée. Toutefois, les pertes de données client se sont jamais produite avec OMS. En outre, nous tenir à jour les copies de données qui a été créées et il est répartie géographiquement.

Pour plus d’informations sur la façon dont Microsoft répond aux incidents de sécurité, consultez [Réponse de sécurité Microsoft Azure dans le nuage](https://gallery.technet.microsoft.com/Azure-Security-Response-in-dd18c678/file/150826/1/Microsoft Azure Security Response in the cloud.pdf).

## <a name="compliance"></a>Respect de la réglementation

Programme de sécurité et de gouvernance d’informations OMS logiciels de développement et de service de l’équipe en charge ses besoins et est conforme aux lois et règlements décrite au [Centre de confidentialité de Microsoft Azure](https://azure.microsoft.com/support/trust-center/) et [Respect de la réglementation de Microsoft Trust Center](https://www.microsoft.com/en-us/TrustCenter/Compliance/default.aspx). L’OMS établit les exigences de sécurité identifie les contrôles de sécurité, gère et surveille les risques sont également décrites il. Chaque année, nous effectuons une révision des stratégies, de normes, de procédures et d’instructions.

Chaque membre de l’équipe développement OMS bénéficie d’une formation de sécurité formelle pour l’application. En interne, nous utilisons un système de contrôle de version pour le développement de logiciels. Chaque projet de logiciel est protégé par le système de contrôle de version.

Microsoft dispose d’une équipe de sécurité et de conformité qui supervise et évalue tous les services de Microsoft. Responsables de la sécurité des informations constituent l’équipe et ils ne sont pas associés avec les services d’ingénierie qui développent l’OMS. Les agents de sécurité ont leur propre chaîne de gestion et mener des évaluations indépendantes des produits et des services garantissant la sécurité et conformité.

Conseil de Microsoft d’administration est notifié par et programmes d’un rapport annuel sur l’ensemble de la sécurité des informations chez Microsoft.

L’équipe de développement et de service de logiciel OMS travaille activement avec les équipes Microsoft légales et de conformité et d’autres partenaires du secteur pour acquérir une variété de certifications.

## <a name="security-standards-certifications"></a>Certifications des normes de sécurité

Analytique de journal dans l’OMS répondent à des normes de sécurité suivantes :

- [ISO/IEC 27001](http://www.iso.org/iso/home/standards/management-standards/iso27001.htm) et [ISO/IEC 27018:2014](http://www.iso.org/iso/home/store/catalogue_tc/catalogue_detail.htm?csnumber=61498) conforme
- Paiement carte (PCI conforme) données norme de sécurité (PCI DSS) par le Conseil de normes de sécurité PCI.
- [Type d’organisation de Service contrôles (SOC) 1 1 et SOC 2 Type 1](https://www.microsoft.com/en-us/TrustCenter/Compliance/SOC1-and-2) conforme
- Critères techniques communs de Windows
- Certification Trustworthy Computing de Microsoft
- Comme un service Azure, OMS utilise les composants sont conformes aux exigences de conformité Azure. Vous pouvez en savoir plus sur la [Conformité du centre de confiance Microsoft](https://www.microsoft.com/en-us/TrustCenter/Compliance/default.aspx).


## <a name="cloud-computing-security-data-flow"></a>Le cloud computing des flux de données de sécurité
Le diagramme suivant montre une architecture de sécurité du nuage comme le flux d’informations de votre entreprise et comment il est sécurisé est déplace vers le service de journal Analytique, finalement visible par vous dans le portail de l’OMS. Plus d’informations sur chaque étape suit le diagramme.

![Image de la collection de données OMS et sécurité](./media/log-analytics-security/log-analytics-security-diagram.png)

## <a name="1-sign-up-for-log-analytics-and-collect-data"></a>1. Inscrivez-vous Analytique de journal et de collecte de données

Pour votre organisation d’envoyer des données d’Analytique de journal, vous configurez agents Windows, les agents qui s’exécutent sur des machines virtuelles Azure, ou Agents de l’OMS pour Linux. Si vous utilisez des agents d’Operations Manager, puis vous utiliserez un Assistant de configuration dans la console opérateur pour les configurer. Utilisateurs (qui peuvent être vous, d’autres utilisateurs individuels ou d’un groupe de personnes) créer un ou plusieurs comptes d’OMS (espaces de travail OMS) et inscrire les agents à l’aide d’un des comptes suivants :


- [ID d’organisation](../active-directory/sign-up-organization.md)

- [Compte Microsoft - Outlook, Office Live, MSN](http://www.microsoft.com/account/default.aspx)

Un espace de travail OMS est où les données sont collectées, agrégées, analysées et présentées. Un espace de travail est essentiellement utilisée comme un moyen de partitionner les données, et chaque espace de travail est unique. Par exemple, vous pouvez souhaiter avoir vos données de production gérées avec un espace de travail OMS et vos données de test gérées avec un autre espace de travail. Également, les espaces de travail aident un administrateur contrôle utilisateur de l’accès aux données. Chaque espace de travail peut avoir plusieurs comptes d’utilisateurs associés, et que chaque compte d’utilisateur peut accéder à plusieurs espace de travail OMS. Vous créez des espaces de travail en fonction de la région de centre de données. Chaque espace de travail est répliquée vers les autres centres de données dans la région, principalement pour la disponibilité du service OMS.

Pour Operations Manager, lors de la configuration est terminée, chaque groupe d’administration Operations Manager établit une connexion avec le service de journal Analytique. L’Assistant Ajout d’ordinateurs permet ensuite de choisir les ordinateurs du groupe d’administration sont autorisés à envoyer des données au service. Pour les autres types d’agent, chacun se connecte en toute sécurité au service OMS.

Toutes les communications entre les systèmes connectés et le service de journal Analytique sont chiffrée.  Le protocole TLS (HTTPS) est utilisé pour le cryptage.  Le processus du SDL de Microsoft est suivi pour garantir le que journal Analytique est à jour avec les progrès les plus récents dans les protocoles de chiffrement.

Chaque type d’agent collecte des données d’Analytique de journal. Le type de données qui sont collectées dépend des types de solutions utilisées. Vous pouvez voir un résumé de la collecte des données à des [solutions d’Analytique de journal ajouter à partir de la galerie de Solutions](log-analytics-add-solutions.md). En outre, des informations plus détaillées de collection ne sont disponibles pour la plupart des solutions. Une solution est un ensemble de vues prédéfinies, journal des requêtes de recherche, les règles de collecte de données et une logique de traitement. Seuls les administrateurs peuvent utiliser des Analytique de journal importer une solution. Après l’importation de la solution, il est déplacé vers les serveurs d’administration MOM (si utilisé), puis de tous les agents que vous avez choisi. Par la suite, les agents collectent les données.

## <a name="2-send-data-from-agents"></a>2. envoyer des données à partir d’agents

Vous inscrivez avec une clé d’inscription de tous les types d’agent et d’une connexion sécurisée est établie entre l’agent et le service de journal Analytique à l’aide de l’authentification basée sur les certificats et SSL port 443. OMS utilise un magasin des secrets pour générer et mettre à jour les clés. Les clés privées sont pivotés tous les 90 jours et sont stockés dans Azure et sont gérés par les opérations d’Azure qui suivent des pratiques réglementaires et de conformité strictes.

Avec Operations Manager, vous enregistrez un espace de travail avec le service de journal Analytique et une connexion HTTPS sécurisée est établie entre le serveur d’administration Operations Manager.

Pour les agents Windows s’exécutant sur des machines virtuelles Azure, une clé de stockage en lecture seule est utilisée pour lire des événements de diagnostic dans les tables Azure.

Si un agent ne peut pas communiquer au service pour une raison quelconque, les données collectées sont stockées localement dans un cache temporaire et le serveur d’administration essaie de renvoyer les données toutes les 8 minutes pendant 2 heures. Données mises en cache de l’agent sont protégées par la banque d’informations d’identification du système d’exploitation. Si le service ne peut pas traiter les données après 2 heures, les agents seront file d’attente les données. Si la file d’attente est pleine, OMS commence la suppression des types de données, à partir des données de performances. La limite de file d’attente de l’agent est une clé de Registre afin que vous puissiez modifier, si nécessaire. Les données collectées sont compressées et envoyées au service, sans passer par les bases de données sur site, afin qu’il n’ajoute pas de toute charge leur. Une fois les données collectées sont envoyées, il est supprimé du cache.

Comme décrit ci-dessus, les données à partir de vos agents sont envoyées via SSL aux centres de données Microsoft Azure. Si vous le souhaitez, vous pouvez utiliser ExpressRoute pour fournir une sécurité supplémentaire pour les données. ExpressRoute est un moyen pour se connecter à Azure directement à partir de votre réseau WAN, par exemple un multi-protocol label commutation VPN (MPLS), fourni par un fournisseur de services réseau. Pour plus d’informations, consultez [ExpressRoute](https://azure.microsoft.com/services/expressroute/).


## <a name="3-the-log-analytics-service-receives-and-processes-data"></a>3. le service de journal Analytique reçoit et traite les données

Le service journal Analytique garantit que les données entrantes à partir d’une source fiable en validant les certificats et l’intégrité des données avec Azure authentification. Les données brutes non traitées sont ensuite stockées sous la forme d’un objet blob dans le [Stockage Azure de Microsoft](../storage/storage-introduction.md) et ne sont pas chiffrées. Toutefois, chaque blob Azure stockage possède un ensemble de jeu unique de clés, qui est accessible uniquement à l’utilisateur. Le type de données qui sont stockées dépend des types de solutions qui ont été importées et utilisées pour collecter des données. Ensuite, le service de journal Analytique traite les données brutes pour le blog du stockage Azure.

## <a name="4-use-log-analytics-to-access-the-data"></a>4. Utilisation des journaux Analytique pour accéder aux données

Vous pouvez vous connecter au journal Analytique dans le portail de l’OMS en utilisant le compte d’organisation ou un compte Microsoft que vous avez paramétrée précédemment. Tout le trafic entre le portail de l’OMS et l’Analytique de journal dans l’OMS est envoyé sur un canal HTTPS sécurisé. Lorsque vous utilisez le portail de l’OMS, un ID de session est généré sur le client (navigateur web) de l’utilisateur et les données sont stockées dans un cache local jusqu'à ce que la session est terminée. À l’arrêt, le cache est supprimé. Les cookies côté client, qui ne contiennent pas d’informations d’identification personnelle, ne sont pas supprimés automatiquement. Les cookies de session sont marqués HTTPOnly et sont sécurisés. Après une période d’inactivité prédéterminée, fin de la session de portail OMS.

À l’aide du portail de l’OMS, vous pouvez exporter des données vers un fichier CSV, et vous pouvez accéder aux données à l’aide des API de recherche. Exportation de fichiers CSV est limitée à 50 000 lignes par exportation et données API sont limitées à 5 000 lignes par recherche.

## <a name="next-steps"></a>Étapes suivantes

- [Mise en route de journal Analytique](log-analytics-get-started.md) pour en savoir plus sur les journaux Analytique et get opérationnel en quelques minutes.
