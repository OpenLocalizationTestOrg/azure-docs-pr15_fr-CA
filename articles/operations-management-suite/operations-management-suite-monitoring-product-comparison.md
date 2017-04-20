<properties 
   pageTitle="Microsoft analyse comparative | Microsoft Azure"
   description="Suite de gestion des opérations Microsoft (OMS) est nuage de Microsoft solution de gestion informatique qui vous aide à gérer et protéger vos locaux et cloud d’infrastructure.  Cet article identifie les différents services inclus dans OMS et fournit des liens vers leur contenu détaillé."
   services="operations-management-suite"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/27/2016"
   ms.author="bwren" />

# <a name="microsoft-monitoring-product-comparison"></a>Comparaison de produits de surveillance Microsoft

Cet article fournit une comparaison entre le journal Analytique dans Operations Management Suite (OMS) et de System Center Operations Manager (SCOM) en termes de leur architecture, la logique de leur comment surveillent les ressources et comment ils effectuent une analyse des données qu’ils recueillent.  Il s’agit de vous donner une compréhension fondamentale de leurs différences et les points forts relatifs.  

## <a name="basic-architecture"></a>Architecture de base
### <a name="system-center-operations-manager"></a>System Center Operations Manager

Tous les composants SCOM sont installés dans votre centre de données.  [Les agents sont installés](http://technet.microsoft.com/library/hh551142.aspx) sur des ordinateurs Windows et Linux sont gérés par SCOM.  Agents de se connectent aux [Serveurs d’administration](https://technet.microsoft.com/library/hh301922.aspx) qui communiquent avec l’entrepôt de données et la base de données SCOM.  Agents reposent sur l’authentification de domaine pour se connecter aux serveurs d’administration.  Ceux en dehors d’un domaine approuvé peuvent effectuer l’authentification par certificat ou se connecter à un [Serveur de passerelle](https://technet.microsoft.com/library/hh212823.aspx).

SCOM nécessite deux bases de données SQL, l’autre pour les données opérationnelles et un autre entrepôt de données pour prendre en charge la création de rapports et analyse de données.  Un [Serveur de rapports](https://technet.microsoft.com/library/hh298611.aspx) exécute SQL Reporting Services pour générer des rapports sur les données de l’entrepôt de données. 

SCOM peut surveiller les ressources de cloud à l’aide de packs d’administration pour les produits tels que [AWS](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/AWSManagementPack.html) [Azure](https://www.microsoft.com/download/details.aspx?id=38414)et [Office 365](https://www.microsoft.com/download/details.aspx?id=43708).  Ces packs d’administration utilisent un ou plusieurs agents locaux comme proxies pour les workflows en cours d’exécution et de découverte de ressources de cloud pour mesurer leurs performances et disponibilité.  Les agents proxy sont également utilisés pour la [surveillance des unités de réseau](https://technet.microsoft.com/library/hh212935.aspx) et d’autres ressources externes.

La Console est une application Windows qui se connecte à l’un des serveurs d’administration et permet à l’administrateur d’afficher et d’analyser les données recueillies et de configurer l’environnement de SCOM.  Une console web peut être hébergée sur un serveur IIS et fournit une analyse des données via un navigateur.

![Architecture SCOM](media/operations-management-suite-monitoring-product-comparison/scom-architecture.png)

### <a name="log-analytics"></a>Analytique de journal

La plupart des composants de l’OMS sont dans le nuage Azure afin de pouvoir déployer et gérer à moindre coût et l’effort administratif.  Toutes les données collectées par Analytique du journal est stocké dans le référentiel de l’OMS.

Analytique de journal peut collecter des données à partir d’une des trois sources :

- Machines physiques et virtuelles exécutant Windows et l' [Agent de surveillance de Microsoft (MMA)](https://technet.microsoft.com/library/mt484108.aspx) ou Linux et les [Opérations de gestion Suite Agent pour Linux](https://technet.microsoft.com/library/mt622052.aspx).  Ces ordinateurs peuvent être locaux ou ordinateurs virtuels dans Azure, ou un autre nuage.
- Un compte de stockage Azure avec [Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md) collectées par rôle de travail Azure, rôle web ou ordinateur virtuel.
- [Connexion à un groupe de gestion de SCOM](https://technet.microsoft.com/library/mt484104.aspx).  Dans cette configuration, les agents de communiquent avec les serveurs de gestion de SCOM qui fournissent les données à la base de données SCOM où il est ensuite remis à la banque de données OMS.
Les administrateurs analyse des données collectées et configurer Analytique de journal avec le portail OMS qui est hébergé dans Azure et est accessible à partir de n’importe quel navigateur.  Les applications mobiles pour accéder à ces données sont disponibles pour les plates-formes standard.

![Architecture du journal Analytique](media/operations-management-suite-monitoring-product-comparison/log-analytics-architecture.png)

### <a name="integrating-scom-and-log-analytics"></a>Intégration de SCOM et Analytique du journal

Lorsque SCOM est utilisé comme source de données d’Analytique de journal, vous pouvez exploiter les fonctionnalités de ces deux produits dans un hybride de surveillance d’environnement.  Vous pouvez configurer les agents SCOM existants par le biais de la Console d’opérations d’être géré par l’OMS, en plus de continuer à exécuter des packs d’administration à partir de SCOM.  
Les données à partir d’un groupe de gestion de SCOM connecté sont remises au journal Analytique à l’aide d’une des quatre méthodes :

- Les événements et les données de performance sont collectées par l’agent et remies à SCOM.  Serveurs de gestion de SCOM alors de fournir les données d’Analytique de journal.
- Certains événements tels que les journaux IIS et les événements de sécurité continuent d’être remis directement au journal Analytique à partir de l’agent.
- Certaines solutions va fournir des logiciels supplémentaires à l’agent ou exigent l’installation de logiciel pour collecter des données supplémentaires.  Ces données sont généralement recevront directement au journal Analytique.
- Certaines solutions de collecte des données directement à partir de serveurs de gestion de SCOM qui ne provient pas de l’agent.  Par exemple, la [solution de gestion des alertes](https://technet.microsoft.com/library/mt484092.aspx) collecte les alertes à partir de SCOM après que qu’ils ont été créés.

## <a name="monitoring-logic"></a>Logique de surveillance
SCOM et Analytique du journal fonctionnent avec des données collectées auprès des agents similaires, mais présentent des différences fondamentales dans comment ils définissent et implémentent la logique de la collecte de données et comment ils analyser les données qu’ils recueillent.

### <a name="operations-manager"></a>Gestionnaire des opérations
Logique de surveillance de SCOM est implémenté dans [les packs d’administration](https://technet.microsoft.com/library/hh457558.aspx) qui contiennent une logique permettant de découvrir les composants à surveiller, mesurer le fonctionnement de ces composants et pour la collecte de données à analyser.  Données d’analyse peuvent être aussi simple que la collecte d’un compteur d’événement ou de performance, ou elle peut utiliser une logique complexe implémentée dans un script.  Les packs d’administration qui comprennent la surveillance complète sont disponibles pour une variété de [Microsoft et des applications tierces](http://go.microsoft.com/fwlink/?LinkId=82105) en plus de périphériques matériels et réseau.  Vous pouvez [créer vos propres packs d’administration](http://aka.ms/mpauthor) pour les applications personnalisées.

Packs d’administration contiennent plusieurs workflows qui effectuent une fonction de surveillance distincte comme un compteur de performance de l’échantillonnage, vérification de l’état d’un service ou l’exécution d’un script.  Chaque flux de travail s’exécute de façon indépendante et définit ses propres résultats, tels que la base de données elle écrira dans et si elle génère une alerte. 

Vous pouvez substituer les détails du flux de travail, tels que la fréquence de que leur exécution, le seuil considèrent une erreur et la gravité de l’alerte qu’ils génèrent.  Vous pouvez également fournir des fonctionnalités supplémentaires en ajoutant vos propres flux de travail.

![Substitue](media/operations-management-suite-monitoring-product-comparison/scom-overrides.png)

Les packs d’administration installés dans la base de données d’Operations Manager et automatiquement distribuées aux agents par les serveurs d’administration.  Chaque agent automatiquement télécharger les packs d’administration et charger le flux de travail pertinents pour les applications qu’ils ont installé.  Les données collectées par l’agent sont rendues sur le serveur de gestion pour l’insérer dans l’entrepôt de données et la base de données SCOM.  La Console d’opérations vous permet d’afficher et d’analyser ces données par l’intermédiaire de vues personnalisées, les tableaux de bord et les rapports inclus dans le pack d’administration.

La distribution des packs d’administration est illustrée dans le diagramme suivant.

![Flux de pack de gestion](media/operations-management-suite-monitoring-product-comparison/scom-mpflow.png)

### <a name="log-analytics"></a>Analytique de journal
#### <a name="event-and-performance-collection"></a>Événements et collecte des performances
Analytique de journal collecte les événements et les compteurs de performance à partir des systèmes de l’agent à l’aide des sources de journal des événements Windows et Syslog journaux IIS.  Vous pouvez définir des critères pour lesquels les données sont collectées via le portail de journal Analytique et ensuite créer des requêtes de journaux pour analyser les données collectées.  Un ensemble de critères standard est défini lorsque vous créez votre espace de travail de l’OMS, et que vous pouvez définir des données supplémentaires pour des applications particulières. 

![Définition des journaux des événements dans le journal Analytique](media/operations-management-suite-monitoring-product-comparison/log-analytics-definedata.png)

Tandis que SCOM possède de nombreux flux de travail détaillées généralement définir des critères spécifiques pour les données et l’action qui doit être effectuée en réponse, journal Analytique a des critères plus générales pour la collecte de données.  Journal des requêtes et des solutions fournissent plus de critères ciblés pour l’analyse et agissant sur des données spécifiques dans le nuage après sa collecte.

#### <a name="solutions"></a>Solutions
Solutions fournissent une logique supplémentaire pour l’analyse et la collecte de données.  Vous pouvez sélectionner les solutions à ajouter à votre abonnement de l’OMS, à partir de la galerie de solutions.

![Galerie de solutions](media/operations-management-suite-monitoring-product-comparison/log-analytics-solutiongallery.png)

Les solutions exécutées principalement dans le nuage fournissant l’analyse des événements et des compteurs de performance collectés dans le référentiel de l’OMS.  Ils peuvent également définir des données supplémentaires à collecter et qui peuvent être analysées avec le journal des requêtes ou par l’interface utilisateur supplémentaire fournie par la solution dans le tableau de bord OMS. 

Par exemple, la [solution de suivi des modifications](https://technet.microsoft.com/library/mt484099.aspx) détecte les modifications de configuration sur les systèmes de l’agent et écrit dans le référentiel de l’OMS qui peut être analysé avec plusieurs affichages graphiques qui résument les événements a détecté des modifications.  Vous pouvez accéder à partir de la vue résumée dans les requêtes de journaux qui affichent les données détaillées collectées par la solution.


Alors que vous pouvez sélectionner les solutions que vous ajoutez à votre abonnement, vous ne disposez pas de la possibilité de créer vos propres solutions.  Vous pouvez sélectionner les événements et les compteurs de performance à collecter et à créer des vues personnalisées basées sur votre propre journal des requêtes.

La logique de surveillance pour journal Analytique est résumée dans le diagramme suivant.

![Flux de solutions d’Analytique de journal](media/operations-management-suite-monitoring-product-comparison/log-analytics-solution-flow.png)

## <a name="health-monitoring"></a>Surveillance de l’état
### <a name="operations-manager"></a>Gestionnaire des opérations
SCOM peut modéliser les différents composants d’une application et fournir un état en temps réel pour chaque.  Cela vous permet non seulement afficher a détecté les erreurs et les performances au fil du temps, mais également pour valider le fonctionnement réel d’une application ou système et chacun de ses composants à un moment donné.  Dans la mesure où elle comprend les périodes de temps, une application n’est disponible, le moteur de santé dans SCOM prend également en charge les accords de niveau de Service (SLA) qui analyse et rapport sur la disponibilité d’une application dans le temps.

Par exemple, la vue ci-dessous affiche l’état en temps réel de moteurs de base de données SQL surveillés par SCOM.  L’état de santé de chacune des bases de données d’un des moteurs de base de données est affichée en bas de la vue.

![Affichage de l’état](media/operations-management-suite-monitoring-product-comparison/scom-state-view.png)

L’Explorateur d’intégrité pour un des moteurs de base de données est illustré ci-après avec les moniteurs sont utilisés pour déterminer son état de santé général.  Ces moniteurs sont définis dans le pack de gestion SQL et exécuter sur tous les moteurs de base de données SQL découverts par SCOM.

![Explorateur d’intégrité](media/operations-management-suite-monitoring-product-comparison/scom-health-explorer.png)

Sur plusieurs systèmes, les composants peuvent être combinées pour mesurer l’état de santé d’une application distribuée.  Cela peut être particulièrement utile pour les applications d’entreprise qui incluent plusieurs composants distribués.  Vous pouvez créer un modèle qui mesure l’état de chaque composant qui seront reportées dans la disponibilité de l’application.

Active Directory est un exemple d’un pack d’administration qui fournit un modèle pour analyser les composants distribués.  Le diagramme ci-dessous illustre le fonctionnement de l’environnement général et de la relation entre les contrôleurs de domaine, des domaines et des forêts.  Chacun de ces composants inclut les sous-composants et plusieurs moniteurs similaires à l’exemple SQL ci-dessus.

![Affichage de diagramme SCOM](media/operations-management-suite-monitoring-product-comparison/scom-diagram-view.png)

### <a name="log-analytics"></a>Analytique de journal
OMS ne pas inclure un moteur commun aux applications de modèle ou mesurer leur santé en temps réel.  Solutions individuelles peuvent évaluer l’intégrité globale des services particuliers en fonction des données collectées, et ils peuvent installer une logique personnalisée sur l’agent pour effectuer une analyse en temps réel.  Dans la mesure où les solutions exécutent dans le nuage avec accès au référentiel de l’OMS, elles peuvent souvent fournir une analyse plus approfondie qu’est généralement effectué par les packs d’administration. 

Par exemple, les [solutions d’évaluation de la publicité et d’évaluation de SQL](https://technet.microsoft.com/library/mt484102.aspx) analyse des données collectées et fournir une évaluation pour différents aspects de l’environnement.  Il fournit des recommandations pour les améliorations qui peuvent être apportées pour améliorer la disponibilité et les performances de l’environnement.

![Solution d’évaluation AD](media/operations-management-suite-monitoring-product-comparison/log-analytics-ad-assessment.png)

## <a name="data-analysis"></a>Analyse des données
SCOM et journal Analytique offrent différentes fonctionnalités pour analyser les données collectées.  SCOM a vues et tableaux de bord dans la Console d’opérations pour l’analyse des données récentes dans une variété de formats et de rapports pour présenter les données de l’entrepôt de données sous forme de tableau.  Analytique de journal fournit une interface et un langage de requête journal complet d’analyse des données dans le référentiel de l’OMS.  Lorsque SCOM est utilisé comme source de données d’Analytique de journal, le référentiel comprend les données collectées par le SCOM afin que les outils d’Analytique de journal peuvent être utilisés pour analyser les données des deux systèmes.

### <a name="operations-manager"></a>Gestionnaire des opérations

#### <a name="views"></a>Affichage
Les vues dans la Console opérateur permettent d’afficher différents types de données collectées par SCOM dans différents formats, généralement sous forme de tableau pour les événements, les alertes et les données d’état et de graphiques pour les données de performance de la ligne.  Les vues effectuent une analyse minimale ou la consolidation des données, mais vous permettent de filtrer selon des critères particuliers. 

![Affichage](media/operations-management-suite-monitoring-product-comparison/scom-views.png)

Les packs d’administration fournira généralement plusieurs vues prenant en charge de l’application ou le système qu’il surveille.  Il peut s’agir de vues pour les différents objets qui le pack d’administration découvre, affichages des alertes pour les problèmes détectés et des affichages de performance pour les compteurs d’état.

Les vues sont particulièrement adaptés à l’analyse de l’état actuel de l’environnement, y compris les alertes actives et l’état de santé des systèmes surveillés et des objets.  Vous pouvez accéder à l’événement détaillée ou une alerte de support afin de diagnostiquer sa cause des données de performances. De même, vous pouvez afficher les performances et le fonctionnement des différents composants d’une application à évaluer son état actuel.

#### <a name="dashboards"></a>Tableaux de bord
Tableaux de bord dans la Console d’opérations fonctionne principalement avec les mêmes données que les vues, mais sont plus personnalisables et peuvent inclure des visualisations plus riches.  Un ensemble de tableaux de bord standard ne sont disponibles que vous pouvez facilement personnaliser pour vos propres besoins.  Vous pouvez également utiliser un widget de PowerShell qui peut afficher des données retournées par une requête de PowerShell.

![Tableau de bord](media/operations-management-suite-monitoring-product-comparison/scom-dashboard.png)

Les développeurs ont la possibilité d’ajouter des composants personnalisés pour les tableaux de bord qu’ils incluent dans leurs packs d’administration.  Ces peuvent être hautement spécialisés pour une application particulière comme le tableau de bord dans le pack de gestion SQL ci-dessous.  Ce tableau de bord peut également servir en tant que modèle pour des versions personnalisées.

![Tableau de bord SQL](media/operations-management-suite-monitoring-product-comparison/scom-sql-dashboard.png)

#### <a name="reports"></a>Rapports
Rapports dans SCOM analysent les données de l’entrepôt de données sous forme de tableau.  Ils peuvent être imprimés et prévues pour une livraison automatique dans différents formats de fichier, y compris Word, PDF et CSV.  Les rapports fonctionnent avec les données de l’entrepôt de données afin qu’elles soient plus particulièrement appropriés pour l’analyse des tendances à long terme.

Les packs d’administration fournit généralement des rapports personnalisés pour une application particulière.  Vous pouvez également sélectionner à partir d’une bibliothèque de rapports génériques que vous pouvez personnaliser pour vos propres applications ou pour effectuer une analyse ad hoc.

Voici un exemple de rapport de performances affichant les données collectées par Active Directory Management Pack.

![Rapport](media/operations-management-suite-monitoring-product-comparison/scom-report.png)

### <a name="log-analytics"></a>Analytique de journal
Analytique de journal a un [langage de requête](https://technet.microsoft.com/library/mt484120.aspx) que vous pouvez utiliser pour analyser les données provenant de plusieurs applications sans avoir besoin de créer un affichage personnalisé ou un état.  OMS est implémentée dans le nuage, les performances des requêtes et des analyses de données ne sont pas soumis à des restrictions de matériel et permet d’analyser rapidement les requêtes incluant des millions d’enregistrements. 

Requêtes de journal Analytique sont également la base d’autres fonctionnalités.  Vous pouvez enregistrer une requête, exporter les résultats vers Excel ou automatiquement exécuté à intervalles réguliers afin de générer une alerte si ses résultats correspondent à des critères particuliers.  

![Flux de requête journal](media/operations-management-suite-monitoring-product-comparison/log-analytics-query-flow.png)

Vous trouverez ci-dessous un exemple de requête Analytique du journal.  Dans cet exemple, tous les événements avec « démarré » dans le nom sont retournés et regroupés par événement ID.  L’utilisateur fournit simplement la requête et Analytique de journal génère dynamiquement l’interface utilisateur pour effectuer l’analyse.  Sélection d’un élément dans la liste renvoie les données d’événements détaillées.

![Requête du journal](media/operations-management-suite-monitoring-product-comparison/log-analytics-query.png)

En plus de fournir l’analyse ad hoc, les requêtes dans journal Analytique peuvent être enregistrés pour une utilisation ultérieure et également ajoutés à votre [tableau de bord OMS](http://technet.microsoft.com/library/mt484090.aspx) , comme illustré dans l’exemple suivant.

![Tableau de bord OMS](media/operations-management-suite-monitoring-product-comparison/log-analytics-dashboard.png)

## <a name="next-steps"></a>Étapes suivantes

- Déploiement de [System Center Operations Manager (SCOM)](https://technet.microsoft.com/library/hh205987.aspx).
- Inscrivez-vous pour [l’Analytique de journal](https://azure.microsoft.com/documentation/services/log-analytics).  
