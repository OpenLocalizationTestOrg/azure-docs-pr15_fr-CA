<properties
    pageTitle="Méthodes de routage de trafic Traffic Manager - | Microsoft Azure"
    description="Cet article vous aidera à comprendre les méthodes de routage de trafic différent utilisés par le Gestionnaire de trafic"
    services="traffic-manager"
    documentationCenter=""
    authors="sdwheeler"
    manager="carmonm"
    editor=""
/>
<tags
    ms.service="traffic-manager"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="10/11/2016"
    ms.author="sewhee"
/>

# <a name="traffic-manager-traffic-routing-methods"></a>Méthodes de routage de trafic Traffic Manager

Azure Traffic Manager prend en charge trois méthodes de routage de trafic pour déterminer comment router le trafic réseau pour les différents points de terminaison de service. Traffic Manager applique la méthode de routage de trafic pour chaque requête DNS qu’il reçoit. La méthode de routage de trafic détermine à quel point de terminaison est retourné dans la réponse DNS.

La prise en charge du Gestionnaire de ressources Azure de Traffic Manager utilise une terminologie différente que le modèle de déploiement classique. Le tableau suivant montre les différences entre les termes de gestionnaire de ressources et classique :

| Terme du Gestionnaire de ressources | Terme classique |
|-----------------------|--------------|
| Méthode de routage de trafic | Méthode d’équilibrage de la charge |
| Méthode de priorité | Méthode de basculement |
| Méthode pondérée | Méthode de répétition alternée |
| Méthode de performances | Méthode de performances |

En fonction des commentaires des clients, nous avons modifié la terminologie pour améliorer la clarté et de réduire les malentendus courants. Il n’y a aucune différence de fonctionnalité.

Il existe trois méthodes de routage de trafic dans le Gestionnaire de trafic :

- **Priorité :** Sélectionnez 'Priority' lorsque vous souhaitez utiliser un point de terminaison du service principal pour tout le trafic et fournir des sauvegardes dans le cas où le serveur principal ou les points de terminaison de sauvegarde ne sont pas disponibles.
- **Pondérée :** Sélectionnez « pondérée » lorsque vous souhaitez répartir le trafic entre un ensemble de points de terminaison, régulièrement ou en fonction du poids, que vous définissez.
- **Performances :** Sélectionnez 'Performances' lorsque vous avez des points de terminaison dans des lieux géographiques différents et vous souhaitez que les utilisateurs finaux utilisent le point de terminaison « plus proche » en fonction de la latence du réseau le plus bas.

Tous les profils de Traffic Manager comprennent la surveillance de la santé du point de terminaison et de basculement de point de terminaison automatique. Pour plus d’informations, consultez [Analyse de point de terminaison de Traffic Manager](traffic-manager-monitoring.md). Un seul profil de Traffic Manager peut utiliser qu’une seule méthode de routage du trafic. Vous pouvez sélectionner une méthode de routage de trafic différent pour votre profil à tout moment. Les modifications sont appliquées en une minute, et sans interruption de service est née. Méthodes de routage de trafic peuvent être combinées à l’aide de profils de Traffic Manager imbriqués. L’imbrication permet flexibles et élaborées le routage du trafic des configurations qui répondent aux besoins des applications de plus grandes et complexes. Pour plus d’informations, voir [les profils de Traffic Manager imbriqués](traffic-manager-nested-profiles.md).

## <a name="priority-traffic-routing-method"></a>Méthode de routage du trafic de priorité

Souvent, une organisation souhaite fournir la fiabilité de ses services en déployant un ou plusieurs services de sauvegarde au cas où leur service principal tombe en panne. La méthode de routage de trafic 'Priority' permet à des clients Azure facilement implémenter ce modèle de basculement.

![Gestionnaire de trafic Azure 'Priority' méthode de routage de trafic][1]

Le profil de Traffic Manager contient une liste hiérarchisée des points de terminaison de service. Par défaut, Traffic Manager envoie tout le trafic vers le point de terminaison (priorité haute) principal. Si le point de terminaison principal n’est pas disponible, Traffic Manager achemine le trafic vers le deuxième point de terminaison. Si les deux points de terminaison principales et secondaires ne sont pas disponibles, le trafic passe à la troisième et ainsi de suite. Disponibilité du point de terminaison est basée sur l’état configuré (activé ou désactivé) et le point de terminaison en cours de surveillance.

### <a name="configuring-endpoints"></a>Configuration des points de terminaison

Avec le Gestionnaire de ressources d’Azure, vous configurez la priorité de point de terminaison explicitement à l’aide de la propriété 'priority' pour chaque point de terminaison. Cette propriété est une valeur comprise entre 1 et 1000. Les valeurs faibles représentent une priorité plus élevée. Points de terminaison ne peuvent pas partager des valeurs de priorité. Définition de la propriété est facultative. S’il est omis, une priorité par défaut basée sur la commande de point de terminaison est utilisée.

Avec l’interface classique, la priorité de point de terminaison est configurée implicitement. La priorité est basée sur l’ordre dans lequel les points de terminaison sont répertoriés dans la définition de profil.

## <a name="weighted-traffic-routing-method"></a>Méthode de routage de trafic pondérée

La méthode de routage de trafic « Pondérée » vous permet de répartir le trafic ou d’utiliser une pondération prédéfinie.

![Méthode de routage de trafic 'Pondérée' Azure Traffic Manager][2]

Dans la méthode de routage de trafic pondérée, vous affectez un poids à chaque point de terminaison dans la configuration du profil Traffic Manager. Le poids est un entier compris entre 1 et 1000. Ce paramètre est facultatif. Si omis, gestionnaires de trafic utilise un poids par défaut de '1'.

Pour chaque requête DNS reçu, Traffic Manager choisit au hasard un point de terminaison disponible. La probabilité de choisir un point de terminaison est basée sur les poids affectés à tous les points de terminaison disponibles. Utilisant le même poids sur tous les résultats des points de terminaison dans une distribution de trafic même. À l’aide de poids supérieurs ou inférieurs de points de terminaison spécifiques provoque ces points de terminaison à retourner plus ou moins fréquemment dans les réponses DNS.

La méthode pondérée permet des scénarios utiles :

- Mise à niveau de l’application progressive : affecter un pourcentage de trafic pour router vers un nouveau point de terminaison et augmentez progressivement le trafic au fil du temps à 100 %.
- Migration des applications vers Azure : créez un profil avec les points de terminaison Azure et externes. Ajuster le poids des points de terminaison à préférer les nouveaux points de terminaison.
- Éclatement de cloud pour une capacité supplémentaire : rapidement développer un déploiement local dans le nuage en le plaçant derrière un profil de gestionnaire de trafic. Lorsque vous avez besoin d’une capacité supplémentaire dans le nuage, vous pouvez ajouter ou activer plusieurs points de terminaison et spécifier quelle partie du trafic passe à chaque point de terminaison.

Le nouveau portail Azure prend en charge la configuration de routage du trafic pondéré. Poids ne peut pas être configurés dans le portail classique. Vous pouvez également configurer à l’aide du Gestionnaire de ressources et des versions standard de PowerShell d’Azure, CLI et les API reste de poids.

Il est important de comprendre que les réponses DNS sont mises en cache par les clients et les serveurs DNS récursives que les clients utilisent pour résoudre les noms DNS. Cette mise en cache peut d’avoir un impact sur les distributions de trafic pondérée. Lorsque le nombre de clients et les serveurs DNS récursive est important, la distribution du trafic fonctionne comme prévu. Toutefois, lorsque le nombre de clients ou les serveurs DNS récursive est petit, la mise en cache peut fausser sensiblement la distribution du trafic.

Scénarios d’utilisation courants incluent :

- Environnements de test et de développement
- Communications d’application à application
- Applications visant une étroite à base d’utilisateurs qui partagent une infrastructure DNS de commune récursive (par exemple, les employés de la société via un proxy)

Ces effets de mise en cache de DNS sont communes à tout le trafic DNS des systèmes de routage, pas seulement Azure Traffic Manager. Dans certains cas, l’explicitement effacer le cache DNS peut fournir une solution de contournement. Dans les autres cas, une autre méthode de routage du trafic peut être plus appropriée.

## <a name="performance-traffic-routing-method"></a>Méthode de routage du trafic de performances

Déploiement des points de terminaison dans deux ou plusieurs emplacements dans le monde entier, la réactivité, de nombreuses applications peut améliorer en routant le trafic à l’emplacement « plus proche » de vous. La méthode de routage de trafic 'Performances' offre cette possibilité.

![Gestionnaire de trafic Azure 'Performances' méthode de routage de trafic][3]

Le point de terminaison « plus proche » n’est pas nécessairement le plus proche en termes de distance géographique. Au lieu de cela, la méthode de routage de trafic 'Performances' détermine le point de terminaison le plus proche par la mesure de la latence du réseau. Traffic Manager gère une Table de latence d’Internet pour suivre le temps d’aller-retour entre les plages d’adresses IP et de centre de données Azure.

Traffic Manager recherche l’adresse IP source de la demande DNS entrante dans la Table de latence d’Internet. Traffic Manager choisit un point de terminaison disponible dans le centre de données Azure qui a le temps de latence plus faible pour la plage d’adresses IP, puis renvoie ce point de terminaison dans la réponse DNS.

Comme expliqué dans le [Mode de fonctionnement de Traffic Manager](traffic-manager-how-traffic-manager-works.md), Gestionnaire de trafic ne reçoit pas les requêtes DNS directement à partir de clients. Au lieu de cela, les requêtes DNS proviennent du service DNS récursives que les clients sont configurés pour utiliser. Par conséquent, l’adresse IP est utilisée pour déterminer le point de terminaison « plus proche » n’est pas l’adresse IP du client, mais il s’agit de l’adresse IP du service DNS récursive. Dans la pratique, cette adresse IP est un bon proxy pour le client.

Traffic Manager met régulièrement à jour la Table de latence d’Internet pour prendre en compte les modifications dans l’Internet global et les nouvelles zones Azure. Toutefois, les performances des applications varient en fonction des variations de charge en temps réel sur Internet. Performances-le routage du trafic ne surveille pas la charge sur un point de terminaison de service donné. Toutefois, si un point de terminaison devient indisponible, Traffic Manager n’existe pas dans les réponses de requêtes DNS.

Points à noter :

- Si votre profil contient plusieurs points de terminaison dans la même région d’Azure, puis Traffic Manager répartit le trafic uniformément sur les points de terminaison disponibles dans cette région. Si vous préférez une distribution de trafic différents au sein d’une région, vous pouvez utiliser [des profils de Traffic Manager imbriqués](traffic-manager-nested-profiles.md).

- Si tous les points de terminaison activés dans une région donnée de Azure sont défectueux, Traffic Manager répartit le trafic sur tous les autres points de terminaison disponibles au lieu du point de terminaison le plus proche de suivant. Cette logique empêche un échec en cascade à ne pas surcharger le point de terminaison le plus proche de suivant. Si vous souhaitez définir une séquence de basculement sur incident par défaut, utiliser [des profils de Traffic Manager imbriqués](traffic-manager-nested-profiles.md).

- Lors de l’utilisation de la méthode de routage de trafic de performances avec les points de terminaison externes ou des points de terminaison imbriqués, vous devez spécifier l’emplacement de ces points de terminaison. Choisissez la région Azure le plus proche de votre déploiement. Ces emplacements sont les valeurs prises en charge par la Table de latence d’Internet.

- L’algorithme qui choisit le point de terminaison est déterministe. Répétition de requêtes DNS à partir du même client est dirigées vers le même point de terminaison. En règle générale, les clients utilisent les serveurs DNS récursive différents lors de vos déplacements. Le client peut être routé vers un autre point de terminaison. Routage peut également être affecté par les mises à jour dans la Table de latence d’Internet. Par conséquent, la méthode de routage du trafic de performances ne garantit pas qu’un client est toujours acheminé au même point de terminaison.

- Lorsque la Table de latence d’Internet change, vous pouvez remarquer que certains clients sont dirigés vers un autre point de terminaison. Cette modification de routage est plus précise en fonction des données de latence en cours. Ces mises à jour sont essentiels pour conserver l’exactitude de performances-le routage du trafic qu’Internet évolue en permanence.

## <a name="next-steps"></a>Étapes suivantes

Apprenez à développer des applications à haute disponibilité à l’aide du [contrôle de point de terminaison de Traffic Manager](traffic-manager-monitoring.md)

Découvrez comment [créer un profil de Traffic Manager](traffic-manager-manage-profiles.md)

<!--Image references-->
[1]: ./media/traffic-manager-routing-methods/priority.png
[2]: ./media/traffic-manager-routing-methods/weighted.png
[3]: ./media/traffic-manager-routing-methods/performance.png
