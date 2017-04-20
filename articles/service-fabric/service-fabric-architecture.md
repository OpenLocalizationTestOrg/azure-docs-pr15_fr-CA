<properties
   pageTitle="Architecture de service Fabric | Microsoft Azure"
   description="TISSU de service est une plate-forme de systèmes distribués utilisée pour créer des applications évolutives, fiables et facile à gérer pour le cloud. Cet article présente l’architecture du Service de Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="rishirsinha"
   manager="timlt"
   editor="rishirsinha"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="06/09/2016"
   ms.author="rsinha"/>

# <a name="service-fabric-architecture"></a>Architecture de service Fabric

Service de Fabric est constitué de sous-systèmes en couches. Ces sous-systèmes vous permettent d’écrire des applications qui sont :

* Haute disponibilité
* Évolutive
* Facile à gérer
* Testable

Le diagramme suivant illustre les principaux sous-systèmes de Fabric du Service.

![Diagramme de l’architecture de Service Fabric](media/service-fabric-architecture/service-fabric-architecture.png)

Dans un système distribué, la possibilité de communiquer de manière sécurisée entre les nœuds d’un cluster est cruciale. À la base de la pile est le sous-système de transport, ce qui permet une communication sécurisée entre les noeuds. Au-dessus du transport sous-système situe le sous-système de la fédération, qui les différents nœuds de clusters en une seule entité (appelée clusters) afin que le Service Fabric peut détecter les défaillances, effectuer élection du chef et fournissent le routage cohérent. Le sous-système de la fiabilité, la couche supérieure du sous-système de la fédération, est responsable de la fiabilité des services de Fabric de Service par le biais de mécanismes tels que la réplication, la gestion des ressources et le basculement. Le sous-système de fédération sous-jacente également le sous-système d’hébergement et de l’activation, qui gère le cycle de vie d’une application sur un seul nœud. Le sous-système de gestion gère le cycle de vie des applications et des services. Le sous-système de testabilité aux développeurs d’applications à tester leurs services par le biais de défaillances simulées avant et après le déploiement d’applications et services pour les environnements de production. TISSU de service permet de résoudre les emplacements de service via son sous-système de communication. Ces sous-systèmes avec le modèle d’application pour activer les outils sont superposées à l’application des modèles de programmation exposées aux développeurs.

## <a name="transport-subsystem"></a>Sous-système de transport
Le sous-système de transport implémente un canal de communication de datagrammes point à point. Ce canal est utilisé pour la communication au sein des clusters fabric du service et de la communication entre les clients et le service de cluster de fabric. Il prend en charge unidirectionnelle et des modèles de communication de demande-réponse, qui sert de base pour l’implémentation de diffusion et multidiffusion dans la couche de la fédération. Le sous-système de transport sécurise les communications à l’aide de X509 les certificats ou la sécurité Windows. Ce sous-système est utilisé en interne par le Service Fabric et n’est pas directement accessible aux développeurs pour la programmation de l’application.

## <a name="federation-subsystem"></a>Sous-système de fédération
Pour raisonner en un ensemble de nœuds dans un système distribué, vous devez ont une vue cohérente du système. Le sous-système de fédération utilise les primitives de communication assurées par le sous-système de transport et épousaient différents nœuds dans un seul cluster homogène qui il peut réfléchir à. Il fournit les primitives de systèmes distribués requises par les autres sous-systèmes - détection de défaillance, élection du chef et routage cohérent. Le sous-système de fédération est basé sur les tables de hachage distribuée avec un espace de jeton de 128 bits. Le sous-système crée une topologie en anneau sur les nœuds, chaque nœud dans l’anneau allouée un sous-ensemble de l’espace de jeton pour la propriété. Pour la détection de défaillance, la couche utilise un mécanisme de location basé sur cœur TREFILAGE et d’arbitrage. Le sous-système de fédération garantit également à jointure complexe et les protocoles de départ seulement un seul propriétaire d’un jeton existant à tout moment. Ce fournissent l’élection du chef et des garanties de routage cohérents.

## <a name="reliability-subsystem"></a>Sous-système de fiabilité
Le sous-système de fiabilité fournit le mécanisme pour rendre l’état d’un service de Fabric de Service hautement disponible en utilisant le _réplicateur_, le _Gestionnaire de basculement_et _l’Équilibrage des ressources_.

* Le réplicateur garantit que les modifications d’état du réplica principal service seront automatiquement répliquées à des réplicas secondaires, maintenir la cohérence entre des réplicas dans un jeu de réplicas de service principales et secondaires. Le réplicateur est responsable de la gestion de quorum entre les réplicas du jeu de réplicas. Il interagit avec l’unité de basculement sur incident pour obtenir la liste des opérations à répliquer, et l’agent de reconfiguration fournit la configuration du jeu de réplicas. Cette configuration indique les réplicas, les opérations doivent être répliqués. TISSU de service fournit un réplicateur par défaut appelé réplicateur de tissu, qui peut être utilisé par l’API de modèle de programmation pour rendre l’état du service hautement disponible et fiable.
* Le Gestionnaire de basculement garantit que, lorsque les nœuds sont ajoutés ou supprimés du cluster, la charge est automatiquement redistribuée entre les nœuds disponibles. En cas de défaillance d’un nœud dans le cluster, le cluster va reconfigurer automatiquement les répliques pour maintenir la disponibilité du service.
* Le Gestionnaire de ressources place des réplicas de service dans tout domaine de défaillance dans le cluster et garantit que toutes les unités de basculement sont opérationnelles. Le Gestionnaire de ressources équilibre également les ressources de service sur le pool partagé sous-jacent des nœuds de cluster pour obtenir la répartition de la charge optimale uniforme.

## <a name="management-subsystem"></a>Sous-système de gestion
Le sous-système de gestion fournit des services de bout en bout et la gestion du cycle de vie des applications. Applets de commande PowerShell et les API d’administration permettre de provisionner, déployer, correctif, mise à niveau, et mettre hors service les applications sans perte de disponibilité. Le sous-système de gestion effectue cela via les services suivants.

* **Le Gestionnaire de cluster**: il s’agit du service principal qui interagit avec le Gestionnaire de basculement à partir de la fiabilité de placer les applications sur les nœuds selon les contraintes de placement du service. Le Gestionnaire de ressources dans le sous-système de basculement garantit que les contraintes ne sont jamais interrompues. Le Gestionnaire de cluster gère le cycle de vie des applications à disposition pour mettre hors service. Il s’intègre avec le Gestionnaire d’état pour vous assurer que la disponibilité des applications n’est pas perdue à partir d’un point de vue sémantique de santé lors de mises à niveau.
* **Gestionnaire d’état**: ce service permet la surveillance de l’intégrité des applications, des services et des entités de cluster. Les entités de cluster (par exemple, les nœuds, service des partitions et duplications) peuvent signaler les informations de santé, qui sont ensuite regroupées dans le magasin d’état centralisée. Ces informations de santé fournissent un instantané de l’état de santé global point-à-temps des services et des nœuds distribués sur plusieurs nœuds dans le cluster, ce qui vous permet de prendre les mesures correctives nécessaires. Requête de santé Qu'api permettre d’interroger les événements d’état signalé au sous-système de la santé. La requête de santé API retournent les données de santé bruts stockées dans le magasin de santé ou agrégées, interprété des données de santé pour une entité spécifique du cluster.
* **Magasin d’image**: ce service fournit le stockage et la distribution des fichiers binaires d’application. Ce service propose un magasin de fichiers distribué simple où les applications sont téléchargées vers et téléchargées à partir de.


## <a name="hosting-subsystem"></a>Sous-système d’hébergement
Le Gestionnaire de cluster informe le sous-système hébergement (s’exécutant sur chaque nœud), les services qu’il doit gérer pour un nœud particulier. Le sous-système d’hébergement gère ensuite le cycle de vie de l’application sur ce nœud. Il interagit avec les composants de la fiabilité et de la santé pour s’assurer que les réplicas sont placés correctement et fonctionnent correctement.

## <a name="communication-subsystem"></a>Sous-système de communication
Ce sous-système fournit une messagerie fiable dans la cluster et découverte de service par le biais du service d’attribution de noms. Le service d’attribution de noms résout les noms de service vers un emplacement dans le cluster et permet aux utilisateurs de gérer des propriétés et des noms de service. À l’aide du service d’attribution de noms, les clients peuvent communiquer de manière sécurisée avec n’importe quel nœud dans le cluster pour résoudre un nom de service et de récupérer les métadonnées de service. Un API simple d’attribution de noms client, les utilisateurs du Service Fabric peuvent de développer les services et clients capables de résoudre l’emplacement réseau actuel en dépit du dynamisme de nœud ou re-dimensionnement du cluster.

## <a name="testability-subsystem"></a>Sous-système de testabilité
Elle est une suite d’outils spécialement conçus pour le test de services basés sur le Fabric du Service. Les outils permettent à un développeur facilement induire des erreurs significatives et exécuter des scénarios de test pour l’exercice et la validation des nombreux États et transitions qui connaissent un service tout au long de sa durée de vie, de manière sécurisée et contrôlée. Testabilité fournit également un mécanisme pour exécuter des tests plus qui peuvent itérer par le biais de diverses pannes possibles sans perdre de disponibilité. Cela vous offre un environnement de test-dans-production.
