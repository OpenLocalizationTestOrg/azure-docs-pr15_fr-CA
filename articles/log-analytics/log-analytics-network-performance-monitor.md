<properties
    pageTitle="Solution de l’Analyseur de performances dans OMS réseau | Microsoft Azure"
    description="Moniteur de performances réseau vous aide à vous surveillez les performances de vos réseaux dans près de réel-temps à détecter et localiser les goulots d’étranglement du réseau."
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
    ms.date="07/28/2016"
    ms.author="banders"/>

# <a name="network-performance-monitor-preview-solution-in-oms"></a>Solution de l’Analyseur de performances (aperçu) dans OMS réseau

>[AZURE.NOTE] Il s’agit d’une [solution d’aperçu](log-analytics-add-solutions.md#log-analytics-preview-solutions-and-features).

Ce document décrit comment à installer et à utiliser la solution de l’Analyseur de performances réseau dans OMS, qui vous aide à vous surveillez les performances de vos réseaux dans près de réel-temps à détecter et localiser les réseau les goulots d’étranglement de performances. Avec la solution d’analyseur de performances réseau, vous pouvez surveiller la perte et la latence entre deux réseaux, sous-réseaux ou les serveurs. Moniteur de performances réseau détecte des problèmes de réseau comme le trafic blackholing, routage, problèmes et erreurs que les méthodes de surveillance de réseau classiques ne sont pas en mesure de détecter. Moniteur de performances réseau génère des alertes et notifie au fur et à mesure de violation d’un seuil pour un lien de réseau. Ces seuils peuvent être apprises automatiquement par le système, ou vous pouvez les configurer pour utiliser les règles d’alerte personnalisés. Moniteur de performances réseau garantit une détection opportune de problèmes de performances réseau et localise la source du problème à un segment de réseau particulier ou un périphérique.

Vous pouvez détecter les problèmes de réseau avec le tableau de bord de solution qui affiche des informations récapitulatives sur votre réseau, y compris les récents événements d’état de réseau, les liens de réseau défectueux et des liens de sous-réseau qui sont confrontés à la latence et les pertes de paquets. Vous pouvez inférieurs d’un lien de réseau pour afficher l’état actuel des liens de sous-réseau ainsi que les liens du nœud-nœud. Vous pouvez également afficher la tendance historique de perte et de latence sur le réseau, de sous-réseau et nœud-nœud. Vous pouvez détecter les problèmes de réseau temporaires en affichant des graphiques de tendances historiques pour la perte de paquets et la latence et localiser les goulots d’étranglement du réseau sur une carte topologique. Le graphique de la topologie interactive vous permet de visualiser les itinéraires du réseau de saut par saut et de déterminer la source du problème. Comme d’autres solutions, vous pouvez utiliser la recherche de journal de diverses exigences analytique pour créer des rapports personnalisés à partir des données collectées par le moniteur de performances réseau.

La solution utilise des transactions synthétiques en tant que principal mécanisme pour détecter les erreurs de réseau. Ainsi, vous pouvez l’utiliser sans tenir compte de modèle ou le fournisseur du périphérique d’un réseau spécifique. Il fonctionne à travers les locaux, le cloud (IaaS) et environnements hybrides. La solution détecte automatiquement la topologie du réseau et des itinéraires différents dans votre réseau.

Produits de contrôle de réseau classiques se concentrent sur la surveillance de la santé de périphériques (routeurs, commutateurs, etc.) du réseau mais ne fournissent pas de vue d’ensemble de la qualité réelle de la connectivité réseau entre les deux points, ce qui fait du moniteur de performances réseau.

### <a name="using-the-solution-standalone"></a>À l’aide de la version autonome de solution

Si vous souhaitez surveiller la qualité des connexions de réseau entre leurs charges de travail critiques, réseaux, des centres de données ou des sites d’office, puis vous pouvez utiliser la solution de l’Analyseur de performances réseau en lui-même pour surveiller l’état de la connectivité entre :

- plusieurs sites de centres de données ou office qui sont connectés à l’aide d’un réseau public ou privé
- charges de travail critiques qui exécutent des applications professionnelles
- comme de services en nuage public Microsoft Azure ou Amazon Web Services (AWS) et les réseaux locaux, si IaaS (machine virtuelle) disponible et que vous avez les passerelles configurées pour permettre la communication entre les réseaux locaux et cloud
- Réseaux d’Azure et local lorsque vous utilisez Express itinéraire

### <a name="using-the-solution-with-other-networking-tools"></a>À l’aide de la solution avec d’autres outils de gestion de réseau

Si vous voulez surveiller une ligne d’applications d’entreprise, vous pouvez utiliser la solution de l’Analyseur de performances de réseau sous la forme d’une solution le Guide et les autres outils réseau. Un réseau lent peut conduire à des applications lentes et moniteur de performances réseau peut vous aider à étudier des problèmes de performances d’application qui sont provoqués par des problèmes de réseau sous-jacents. Étant donné que la solution ne nécessite pas l’accès à vos équipements réseau, l’administrateur de l’application n’a pas besoin de compter sur une équipe de mise en réseau pour fournir des informations à propos de la façon dont le réseau est affecter les applications.

Également, si vous investissez déjà dans d’autre outils de surveillance du réseau, puis la solution peut venir en complément des outils parce que les solutions de contrôle de réseau plus traditionnelles ne fournissent pas d’appréhender les métriques de performances de réseau de bout en bout telles que la perte ou l’attente.  La solution d’analyseur de performances de réseau peut aider à combler cette lacune.


## <a name="installing-and-configuring-agents-for-the-solution"></a>L’installation et la configuration des agents de la solution

Les processus de base permet d’installer des agents sur les [ordinateurs Windows de se connecter au journal Analytique](log-analytics-windows-agents.md) et [Connecter Operations Manager pour l’Analytique de journal](log-analytics-om-agents.md).

>[AZURE.NOTE]
Vous devez installer au moins 2 agents afin de disposer de suffisamment de données pour découvrir et surveiller les ressources de votre réseau. Dans le cas contraire, la solution reste dans un état de configuration jusqu'à ce que vous installez et configurez des agents supplémentaires.

### <a name="where-to-install-the-agents"></a>Où installer les agents

Avant d’installer les agents, pensez à la topologie de votre réseau et quelles parties du réseau que vous souhaitez surveiller. Nous vous conseillons d’installer plusieurs agents pour chaque sous-réseau que vous souhaitez surveiller. En d’autres termes, pour chaque sous-réseau que vous souhaitez surveiller, choisir deux ou plusieurs serveurs ou ordinateurs virtuels et installer l’agent sur ceux-ci.

Si vous doutez de la topologie de votre réseau, installer les agents sur des serveurs avec des charges de travail critiques où vous souhaitez surveiller les performances du réseau. Par exemple, vous pouvez souhaiter garder une trace d’une connexion réseau entre un serveur Web et un serveur exécutant SQL Server. Dans cet exemple, vous devez installer un agent sur les deux serveurs.

Agents surveillent la connectivité réseau (liens) entre les hôtes--pas les hôtes eux-mêmes. Ainsi, pour surveiller un lien de réseau, vous devez installer les agents sur les deux points de terminaison de ce lien.

### <a name="configure-agents"></a>Configurer les agents

Après avoir installé les agents, vous devrez ouvrir les ports du pare-feu pour les ordinateurs pour vous assurer que les agents peuvent communiquer. Vous devez télécharger et puis exécutez le [script EnableRules.ps1 PowerShell](https://gallery.technet.microsoft.com/OMS-Network-Performance-04a66634) sans aucun paramètre dans une fenêtre PowerShell avec des privilèges d’administrateur

Le script crée des clés de Registre requises par le moniteur de performances réseau et crée des règles de pare-feu Windows pour autoriser les agents à créer des connexions TCP entre eux. Les clés de Registre créées par le script également spécifient s’il faut enregistrer les journaux de débogage et le chemin d’accès pour les fichiers journaux. Il définit également le port TCP de l’agent utilisé pour la communication. Les valeurs de ces clés sont automatiquement définis par le script, afin que vous ne devez pas modifier manuellement ces clés.

Le port ouvert par défaut est 8084. Vous pouvez utiliser un port personnalisé en fournissant le paramètre `portNumber` pour le script. Toutefois, le même port doit être utilisé sur tous les ordinateurs où le script est exécuté.

>[AZURE.NOTE] Le script EnableRules.ps1 configure les règles de pare-feu Windows uniquement sur l’ordinateur sur lequel le script est exécuté. Si vous disposez d’un pare-feu de réseau, il se peut que vous devez vous assurer qu’il permet le trafic destiné au port TCP utilisé par le moniteur de performances réseau.


## <a name="configuring-the-solution"></a>Configuration de la solution

Utilisez les informations suivantes pour installer et configurer la solution.

1. La solution d’analyseur de performances réseau obtient des données à partir d’ordinateurs exécutant Windows Server 2008 SP 1 ou version ultérieure ou Windows 7 SP1 ou une version ultérieure, qui sont les mêmes exigences que l’Agent Microsoft surveillance (MMA).
2. Ajouter la solution d’analyseur de performances de réseau à votre espace de travail de l’OMS à l’aide de la procédure décrite dans les [solutions d’Analytique de journal ajouter à partir de la galerie de Solutions](log-analytics-add-solutions.md).  
  ![Symbole de l’Analyseur de performances réseau](./media/log-analytics-network-performance-monitor/npm-symbol.png)
3. Dans le portail de l’OMS, vous verrez une nouvelle mosaïque intitulée du **Moniteur de performances réseau** avec le message de *Solution nécessite une configuration supplémentaire*. Vous devez configurer la solution pour ajouter des réseaux basés sur des sous-réseaux et des nœuds qui sont découverts par les agents. Cliquez sur **Moniteur de performances réseau** pour démarrer la configuration du réseau par défaut.  
  ![solution nécessite une configuration supplémentaire](./media/log-analytics-network-performance-monitor/npm-config.png)


### <a name="configure-the-solution-with-a-default-network"></a>Configuration de la solution avec un réseau par défaut

Sur la page de configuration, vous verrez un réseau unique, nommé **par défaut**. Lorsque vous n’avez pas défini tous les réseaux, tous les sous-réseaux détectés automatiquement sont placés dans le réseau par défaut.

Lorsque vous créez un réseau, vous ajoutez un sous-réseau et ce sous-réseau est supprimé à partir du réseau par défaut. Si vous supprimez un réseau, tous les sous-réseaux sont automatiquement renvoyés vers le réseau par défaut.

En d’autres termes, le réseau par défaut est le conteneur de l’ensemble des sous-réseaux qui ne figurent pas dans n’importe quel réseau défini par l’utilisateur. Impossible de modifier ou de supprimer le réseau par défaut. Il reste toujours dans le système. Toutefois, vous pouvez créer autant de réseaux que nécessaire.

Dans la plupart des cas, les sous-réseaux de votre organisation seront organisées dans plus d’un réseau, vous devez créer un ou plusieurs réseaux afin de regrouper vos sous-réseaux.

### <a name="create-new-networks"></a>Créer de nouveaux réseaux

Un réseau dans le moniteur de performances réseau est un conteneur pour les sous-réseaux. Vous pouvez créer un réseau avec n’importe quel nom que vous souhaitez et ajoutez les sous-réseaux au réseau. Par exemple, vous pouvez créer un réseau nommé *bâtiment 1* et puis ajouter des sous-réseaux, ou vous pouvez créer un réseau de *DMZ* et puis ajoutez tous les sous-réseaux appartenant à la zone démilitarisée à ce réseau.

#### <a name="to-create-a-new-network"></a>Pour créer un nouveau réseau

1. Cliquez sur **Ajouter un réseau** , puis tapez le nom de réseau et la description.
2.  Sélectionnez un ou plusieurs sous-réseaux, puis cliquez sur **Ajouter**.
3. Cliquez sur **Enregistrer** pour enregistrer la configuration.  
  ![Ajouter un réseau](./media/log-analytics-network-performance-monitor/npm-add-network.png)



### <a name="wait-for-data-aggregation"></a>Attente d’agrégation de données

Une fois que vous avez enregistré la configuration pour la première fois, la solution commence à collecter des informations de latence et de perte de paquet réseau entre les nœuds où les agents sont installés. Ce processus peut prendre un certain temps, parfois 30 minutes. Pendant cet état, la mosaïque du moniteur de performances réseau dans la page Vue d’ensemble affiche un message indiquant les *agrégation de données dans le processus*.

![agrégation de données en cours](./media/log-analytics-network-performance-monitor/npm-aggregation.png)


Lorsque les données ont été téléchargées, vous verrez les données affichant mosaïque mise à jour de l’Analyseur de performances réseau.

![Mosaïque de l’Analyseur de performances réseau](./media/log-analytics-network-performance-monitor/npm-tile.png)

Cliquez sur la vignette pour afficher le tableau de bord du moniteur de performances réseau.

![Tableau de bord de l’Analyseur de performances réseau](./media/log-analytics-network-performance-monitor/npm-dash01.png)

### <a name="edit-monitoring-settings-for-subnets"></a>Modifier les paramètres de contrôle des sous-réseaux

Tous les sous-réseaux où au moins un agent a été installé sont répertoriés sous l’onglet **sous-réseaux** dans la page de configuration.

#### <a name="to-enable-or-disable-monitoring-for-particular-subnetworks"></a>Pour activer ou désactiver la surveillance des sous-réseaux particuliers

1. Sélectionnez ou désactivez la case en regard de l' **ID de sous-réseau** et assurez-vous que **l’utilisation de la surveillance** est sélectionnée ou désactivée, le cas échéant. Vous pouvez sélectionner ou effacer plusieurs sous-réseaux. Si désactivée, sous-réseaux ne sont pas surveillés comme les agents seront mis à jour pour arrêter la commande ping des autres agents.
2. Sélectionnez les nœuds que vous souhaitez surveiller pour un sous-réseau particulier en sélectionnant le sous-réseau dans la liste et en déplaçant les nœuds requis entre les listes contenant des nœuds non surveillés et contrôlés.
Vous pouvez ajouter une **description** de personnalisée pour le sous-réseau, si vous le souhaitez.
3. Cliquez sur **Enregistrer** pour enregistrer la configuration.  
  ![modifier le sous-réseau](./media/log-analytics-network-performance-monitor/npm-edit-subnet.png)

### <a name="choose-nodes-to-monitor"></a>Choisir de nœuds à surveiller

Tous les nœuds disposent d’un agent sur ceux-ci sont répertoriés dans l’onglet de **nœuds** .

#### <a name="to-enable-or-disable-monitoring-for-nodes"></a>Pour activer ou désactiver l’analyse pour les nœuds

1. Activez ou désactivez les nœuds que vous souhaitez surveiller ou arrêter la surveillance.
2. Cliquez sur **utiliser pour l’analyse**, ou désactivez, selon le cas.
3. Cliquez sur **Enregistrer**.  
  ![Activer l’analyse du nœud](./media/log-analytics-network-performance-monitor/npm-enable-node-monitor.png)


### <a name="set-monitoring-rules"></a>Ensemble de règles d’analyse

Moniteur de performances réseau génère des événements d’état à propos de la connectivité entre une paire de nœuds ou de liens d’un sous-réseau ou un réseau lorsqu’un seuil est violé. Ces seuils peuvent être apprises automatiquement par le système, ou vous pouvez configurer les règles d’alerte personnalisés.

La *règle par défaut* est créé par le système et crée un événement d’état chaque fois que la perte ou la latence entre toute paire de réseaux ou de sous-réseau lie les violations de seuil système apprises. Vous pouvez choisir de désactiver la règle par défaut et créer des règles d’analyse personnalisées

#### <a name="to-create-custom-monitoring-rules"></a>Pour créer des règles d’analyse personnalisées

1. Cliquez sur **Ajouter une règle** dans l’onglet **Moniteur** , puis entrez le nom de la règle et la description.
2. Sélectionnez la paire de liaisons réseau ou sous-réseau à surveiller dans les listes.
3. Tout d’abord sélectionner le réseau qui contient le premier sous-réseau/s d’intérêt dans la liste déroulante du réseau et sélectionnez le sous-réseau/s dans la liste déroulante de sous-réseau correspondant.
Sélectionnez **tous les sous-réseaux** , si vous souhaitez analyser tous les sous-réseaux dans un lien de réseau. De la même façon, sélectionnez l’autre sous-réseau/s d’intérêt. Et bien, vous pouvez cliquer sur **Ajouter une Exception** pour exclure la surveillance pour les liens d’un sous-réseau particulier à partir de la sélection que vous avez apportées.
4. Si vous ne souhaitez pas créer d’événements d’état pour les éléments que vous avez sélectionné, puis désactivez **Activer les liens couvertes par cette règle de contrôle d’état**.
5. Choisissez la surveillance des conditions.
Vous pouvez définir des seuils personnalisés pour la génération d’événements de santé en tapant des valeurs de seuil. Chaque fois que la valeur de la condition dépasse son seuil sélectionné pour la paire de réseau/sous-réseau sélectionné, un événement d’état est généré.
6. Cliquez sur **Enregistrer** pour enregistrer la configuration.  
  ![créer des règles de contrôle personnalisé](./media/log-analytics-network-performance-monitor/npm-monitor-rule.png)


## <a name="data-collection-details"></a>Détails de collecte de données

Moniteur de performances réseau utilise des paquets de négociation TCP SYN-ACK-SYNACK pour collecter des pertes et traceroute et informations de latence est également utilisé pour obtenir des informations de topologie.

Le tableau suivant indique les méthodes de collecte de données et d’autres détails sur la manière dont les données sont collectées pour le moniteur de performances réseau.

| plate-forme | Agent direct | Agent SCOM | Stockage Azure | SCOM requis ? | Données de l’agent SCOM envoyées par groupe d’administration | fréquence de collection |
|---|---|---|---|---|---|---|
| Windows |![Oui](./media/log-analytics-network-performance-monitor/oms-bullet-green.png)|![Oui](./media/log-analytics-network-performance-monitor/oms-bullet-green.png)|![N°](./media/log-analytics-network-performance-monitor/oms-bullet-red.png)|            ![N°](./media/log-analytics-network-performance-monitor/oms-bullet-red.png)|![N°](./media/log-analytics-network-performance-monitor/oms-bullet-red.png)| Les négociations TCP toutes les 5 secondes, données envoyés toutes les trois minutes |

La solution utilise des transactions synthétiques pour évaluer l’intégrité du réseau. Agents d’OMS installés à divers moment dans les paquets réseau exchange TCP avec l’autre et, dans le processus, découvrez la perte de temps et de paquets d’aller-retour, le cas échéant. Périodiquement, chaque agent effectue également un itinéraire de suivi à d’autres agents pour trouver tous les itinéraires différents dans le réseau qui doit être testé. En utilisant ces données, les agents sont en mesure de déduire la latence du réseau et les chiffres de perte de paquets. Les tests sont répétées toutes les cinq secondes et données sont agrégées pendant une période de trois minutes par les agents avant de le télécharger à l’OMS.

>[AZURE.NOTE] Bien que les agents communiquent souvent entre eux, ils ne génèrent pas un trafic réseau important lors de la conduite des essais. Agents reposent uniquement sur des paquets de négociation TCP SYN-ACK-SYNACK pour déterminer la perte et la période de latence – aucune donnée, les paquets sont échangés. Pendant ce processus, les agents communiquent entre eux uniquement si nécessaire et la topologie de communication de l’agent est optimisée pour réduire le trafic réseau.

## <a name="using-the-solution"></a>À l’aide de la solution

Cette section explique le tableau de bord fonctions et comment les utiliser.

### <a name="solution-overview-tile"></a>Mosaïque de présentation de solution

Une fois que vous avez activé la solution d’analyseur de performances réseau, la mosaïque de solution sur la page de vue d’ensemble de l’OMS fournit une vue d’ensemble rapide de l’état de santé du réseau. Il affiche un graphique en anneau affiche le nombre de liens de sous-réseau corrects et incorrects. Lorsque vous cliquez sur la mosaïque, il ouvre le tableau de bord de solution.

![Mosaïque de l’Analyseur de performances réseau](./media/log-analytics-network-performance-monitor/npm-tile.png)


### <a name="network-performance-monitor-solution-dashboard"></a>Tableau de bord solution de l’Analyseur de performances réseau

La lame **Réseau résumé** affiche un résumé des réseaux ainsi que de leur taille relative. Elle est suivie de mosaïques affiche le nombre total de liaisons réseau, les liens de sous-réseau et les chemins d’accès dans le système (un chemin d’accès est constitué des adresses IP des deux hôtes avec agents et tous les tronçons entre eux).

La blade **d’Événements d’état réseau haut** fournit une liste des événements d’état la plus récentes et alertes dans le système et l’heure dans la mesure où l’événement a été active. Un événement d’état ou d’une alerte est générée chaque fois que la perte de paquets ou la latence d’une liaison réseau ou sous-réseau dépasse un seuil.

La lame de **Liaisons réseau défectueux de haut** affiche la liste des liaisons réseau incorrecte. Ce sont les liaisons de réseau qui ont un ou plusieurs événements néfaste sur la santé pour eux pour le moment.

Les lames de **Liens de sous-réseau haut avec plus de perte** et de **Liens de sous-réseau avec une latence plus** affichent les liens de sous-réseau supérieur par la perte de paquets et les liens de sous-réseau supérieur à la latence respectivement. Une forte latence ou un montant de perte de paquets peut être attendu sur certains liens de réseau. Ces liens apparaissent dans les listes des dix premiers, mais ne sont pas marquées comme étant non fonctionnelle.

La lame de **Requêtes communes** contient un ensemble de requêtes de recherche qui extraire directement les données de surveillance de réseau brut. Vous pouvez utiliser ces requêtes comme point de départ pour créer vos propres requêtes personnalisées.

![Tableau de bord de l’Analyseur de performances réseau](./media/log-analytics-network-performance-monitor/npm-dash01.png)


### <a name="drill-down-for-depth"></a>Zoom de profondeur

Vous pouvez cliquer sur les différents liens de la solution du tableau de bord à l’exploration plus approfondie dans une zone d’intérêt. Par exemple, lorsque vous voyez une alerte ou une liaison réseau incorrecte s’affiche sur le tableau de bord, vous pouvez cliquer dessus pour approfondir l’analyse. Vous serez redirigé vers une page qui répertorie tous les liens de sous-réseau pour le lien de réseau particulier. Vous ne pourrez pas voir le statut de perte, de latence et de la santé de chaque lien de sous-réseau et rapidement trouver les liens de sous-réseau sont la cause du problème. Vous pouvez ensuite cliquer sur **Afficher les liens de noeud** pour voir toutes les liaisons de nœud pour le lien de sous-réseau incorrecte. Ensuite, vous pouvez voir les liens de nœud à nœud et trouver les liens du nœud défectueux.

Vous pouvez cliquer sur la **topologie de la vue** pour afficher la topologie de saut par saut d’itinéraires entre les nœuds source et de destination. Les itinéraires incorrects ou le houblon est affiché en rouge afin que vous pouvez identifier rapidement le problème à une partie spécifique du réseau.

![données de détail](./media/log-analytics-network-performance-monitor/npm-drill.png)


#### <a name="trend-charts"></a>Graphiques de tendances

À chaque niveau que vous avez détaillé, vous pouvez voir l’évolution de la perte ou l’attente d’une liaison de réseau. Les graphiques de tendances sont également disponibles pour les liens de sous-réseau et le nœud. Vous pouvez modifier l’intervalle de temps du graphe à tracer en utilisant le contrôle d’heure en haut du graphique.

Les graphiques de tendances indiquent une perspective historique des performances d’une liaison réseau. Des problèmes de réseau sont transitoires de nature et seraient difficiles à intercepter en consultant l’état actuel du réseau. C’est parce que les problèmes peuvent rapidement de surface et disparaître avant tout le monde d’avertissements, seulement réapparaissent à un point ultérieur dans le temps. Ce type de problème transitoire peut également être difficile pour les administrateurs d’application, car les problèmes souvent surface comme inexpliquée augmente dans le temps de réponse de l’application, même lorsque tous les composants de l’application semblent s’exécuter sans problème.

Vous pouvez facilement détecter ces types de problèmes en regardant un graphique de tendances où le problème s’affiche sous la forme d’un pic d’activité soudaine de la latence du réseau ou de perte de paquets.

![graphique de tendances](./media/log-analytics-network-performance-monitor/npm-trend.png)

#### <a name="hop-by-hop-topology-map"></a>Carte topologique de saut par saut

Moniteur de performances réseau affiche la topologie de saut par saut d’itinéraires entre deux nœuds dans une vue topologique interactive. Vous pouvez afficher la carte topologique en sélectionnant un nœud lien puis en cliquant sur la **topologie de la vue**. Vous pouvez également afficher la carte topologique en cliquant sur la mosaïque de **chemins d’accès** sur le tableau de bord. Lorsque vous cliquez sur **chemins d’accès** sur le tableau de bord, vous devrez sélectionner les nœuds source et de destination à partir du Panneau de gauche, puis que vous cliquez sur le **tracé** pour tracer les routes entre les deux nœuds.

La carte topologique affiche le nombre d’itinéraires entre les deux nœuds et ce que les paquets de données prennent des chemins. Goulots d’étranglement des performances réseau sont marqués en rouge sur la carte topologique. Vous pouvez rechercher une connexion réseau défectueuse ou un périphérique réseau défaillant en examinant les éléments de couleur rouges sur la carte topologique.

Lorsque vous cliquez sur un nœud ou un pointer dessus sur la carte topologique, vous verrez les propriétés du nœud comme nom de domaine complet et l’adresse IP. Cliquez sur un tronçon est l’adresse IP. Vous pouvez mettre en surbrillance les itinéraires particuliers en désactivant, puis en sélectionnant uniquement les itinéraires dont vous souhaitez mettre en surbrillance sur la carte. Vous pouvez effectuer un zoom dans la carte topologique ou à l’aide de la roulette de votre souris.

Notez que la topologie indiquée dans le plan de la topologie de la couche 3 et ne contient pas de connexions et les périphériques de couche 2.

![carte topologique de saut par saut](./media/log-analytics-network-performance-monitor/npm-topology.png)

#### <a name="fault-localization"></a>Localisation des pannes

Moniteur de performances réseau est en mesure de trouver les goulots d’étranglement du réseau sans se connecter aux périphériques réseau. Moniteur de performances réseau basé sur les données collectées à partir du réseau et en appliquant des algorithmes avancés sur le graphique de réseau, permet une estimation probabiliste des parties du réseau qui sont probablement la source du problème.

Cette approche est utile pour déterminer les goulots d’étranglement du réseau lors de l’accès au secteur du houblon n’est pas disponible car il ne nécessite pas de données doivent être collectées à partir des périphériques réseau tels que des routeurs ou des commutateurs. Cela est également utile lorsque les tronçons entre deux nœuds ne sont pas dans votre contrôle administratif. Par exemple, le houblon peut-être être des routeurs ISP.

### <a name="log-analytics-search"></a>Recherche des journaux d’Analytique

Toutes les données exposées sous forme graphique via le tableau de bord du moniteur de performances réseau et d’exploration des pages est également disponible en mode natif dans la recherche du journal Analytique. Vous pouvez interroger les données à l’aide de la langue de requête de recherche et créer des rapports personnalisés en exportant les données vers Excel ou PowerBI. La lame de **Requêtes communes** dans le tableau de bord a certaines requêtes utiles que vous pouvez utiliser comme point de départ pour créer vos propres requêtes et rapports.

![requêtes de recherche](./media/log-analytics-network-performance-monitor/npm-queries.png)


## <a name="investigate-the-root-cause-of-a-health-alert"></a>Recherchez la cause d’une alerte de santé

Maintenant que vous avez lu sur le moniteur de performances réseau, examinons un simple examen la cause d’un événement d’état.

1. Dans la page Vue d’ensemble, vous obtiendrez un aperçu rapide de l’état de santé de votre réseau en observant le **Moniteur de performances réseau** en mosaïque. Notez que sur les liens de 80 sous-réseaux surveillés, 43 sont défectueux. Ceci garantit que d’enquête. Cliquez sur la vignette pour afficher le tableau de bord de solution.  
  ![Mosaïque de l’Analyseur de performances réseau](./media/log-analytics-network-performance-monitor/npm-investigation01.png)

2. Dans l’image d’exemple ci-dessous, vous remarquerez qu’il existe actuellement des événements d’état 4 et 4 liens de réseau qui sont défectueux. Vous décidez d’analyser le problème et cliquez sur le lien de réseau **Web de Sharepoint** pour découvrir la cause du problème.  
  ![exemple de lien réseau défectueux](./media/log-analytics-network-performance-monitor/npm-investigation02.png)

3. La page détaillée affiche tous les liens d’un sous-réseau de liaison réseau de **Web de Sharepoint** . Vous remarquerez que pour à la fois les liens d’un sous-réseau, le temps de latence a atteint le seuil effectue la liaison réseau incorrecte. Vous pouvez également voir les tendances de latence des liens à la fois un sous-réseau. Vous pouvez utiliser la sélection de temps contrôle dans le graphique pour vous concentrer sur la plage de temps requis. Vous pouvez voir l’heure de la journée lorsque les temps de latence a atteint son pic. Vous pouvez ensuite rechercher les journaux pour cette période de temps étudier le problème. Cliquez sur **Afficher les liens de nœud** au détail supplémentaire.  
  ![exemple de liens défectueux de sous-réseau](./media/log-analytics-network-performance-monitor/npm-investigation03.png)

4.  Similaire à la page précédente, la page détaillée du lien d’un sous-réseau particulier répertorie ses liens constitutifs de nœud vers le bas. Vous pouvez exécuter des actions similaires ici, comme vous l’avez fait à l’étape précédente. Cliquez sur la **topologie de la vue** pour afficher la topologie entre les 2 nœuds.  
  ![exemple de liens nœud défectueux](./media/log-analytics-network-performance-monitor/npm-investigation04.png)

5. Tous les chemins entre les 2 nœuds sélectionnés sont tracées dans la carte topologique. Vous pouvez visualiser la topologie de saut par saut d’itinéraires entre deux nœuds sur la carte topologique. Il vous donne une vision claire de combien d’itinéraires existent entre les deux nœuds et les chemins que prennent les paquets de données. Goulots d’étranglement des performances réseau sont marqués en rouge. Vous pouvez rechercher une connexion réseau défectueuse ou un périphérique réseau défaillant en examinant les éléments de couleur rouges sur la carte topologique.  
  ![exemple de vue topologie défectueux](./media/log-analytics-network-performance-monitor/npm-investigation05.png)

6. La perte, la latence et le nombre de sauts dans chaque chemin d’accès peuvent être consultées dans le volet **d’Informations de chemin d’accès** . Dans cet exemple, vous pouvez voir qu’il existe des chemins d’accès incorrects 3 comme indiqué dans le volet. Utilisez la barre de défilement pour afficher les détails de ces chemins d’accès incorrects.  Utilisez les cases à cocher pour sélectionner l’un des chemins afin que la topologie pour qu’un seul chemin est tracée. Vous pouvez utiliser la roulette de votre souris pour effectuer un zoom ou depuis la carte topologique.

  Dans le sous l’image vous pouvez le constater la cause racine des zones à problèmes pour la section spécifique du réseau en examinant les chemins d’accès et les tronçons de couleur rouge. En cliquant sur un nœud dans la carte topologique révèle les propriétés du nœud, y compris le nom de domaine complet et l’adresse IP. En cliquant sur un tronçon affiche l’adresse IP du saut.  
  ![topologie défectueux - exemple de détails de chemin d’accès](./media/log-analytics-network-performance-monitor/npm-investigation06.png)

## <a name="next-steps"></a>Étapes suivantes

- [Journaux de recherche](log-analytics-log-searches.md) pour afficher les enregistrements de données de performances réseau détaillé.
