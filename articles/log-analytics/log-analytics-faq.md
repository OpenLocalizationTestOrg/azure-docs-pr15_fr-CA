<properties
    pageTitle="Journal Analytique FAQ | Microsoft Azure"
    description="Réponses aux questions fréquemment posées sur le service de journal Analytique."
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

# <a name="log-analytics-faq"></a>Journal Analytique Forum aux questions

Cette FAQ de Microsoft est une liste de questions fréquemment posées sur journal Analytique dans Microsoft Operations Management Suite (OMS). Si vous avez des questions supplémentaires sur journal Analytique, veuillez aller sur le [forum de discussion](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights) et publier vos questions. Une personne à partir de notre communauté vous aideront à vos réponses. Si une question est fréquemment posée, nous allons l’ajouter à cet article afin qu’il vous pouvez trouver rapidement et facilement.

## <a name="general"></a>Général

**Q. quels contrôles sont effectués par la publicité et les solutions d’évaluation de SQL ?**

A. La requête suivante affiche une description de tous les contrôles actuellement effectués :

```
(Type=SQLAssessmentRecommendation OR Type=ADAssessmentRecommendation) | dedup RecommendationId | select FocusArea, ActionArea, Recommendation, Description | sort Type, FocusArea,ActionArea, Recommendation
```

Les résultats peuvent ensuite être exportés vers Excel pour examen complémentaire.

* *Q : Pourquoi puis-je voir autre chose que *OMS* dans SCOM Administration? **

A: selon quelle mise à jour de correctif cumulatif de SCOM dans, vous pouvez voir un noeud pour *System Center Advisor*, *Perspectives opérationnelles*ou *Analytique du journal*.

La mise à jour de chaîne de texte à *OMS* est inclus dans un pack d’administration, qui doit être importé manuellement. Suivez les instructions de l’article KB de correctif cumulatif de mise à jour de SCOM et actualiser la console de l’OMS pour voir les dernières mises à jour dans le nœud de *l’OMS* .

* *Q : Y a-t-il un *local* version de OMS? **

A: n° de Analytique de journal traite et stocke de très grandes quantités de données. Sous la forme d’un service en nuage, journal Analytique peut augmenter si nécessaire ou éviter tout impact sur les performances de votre environnement.

Également, qui est un moyen de service cloud vous n’avez pas besoin de maintenir l’infrastructure Analytique du journal en cours d’exécution et peut recevoir des correctifs et des mises à jour de la fonctionnalité fréquentes.

## <a name="configuration"></a>Configuration de
**Q. est-il possible de modifier le nom du conteneur de table/blob utilisé pour lire à partir d’Azure Diagnostics (WAD) ?**  

A.  Non, ce n’est pas possible actuellement, mais est prévu pour une version future.

**Q. les adresses IP que faire l’utilisation de services OMS ? Comment s’assurer que mon pare-feu autorise uniquement le trafic vers les Services de l’OMS ?**  

A. Le service de journal Analytique repose sur Azure et les points de terminaison reçoivent IPs qui se trouvent dans les [Plages IP de Microsoft Azure Datacenter](http://www.microsoft.com/download/details.aspx?id=41653).

Comme les déploiements de service sont effectuées, les adresses IP réelles des services OMS modifier. Les noms DNS pour permettre à travers votre pare-feu sont documentés à [configurer les paramètres de pare-feu et de proxy dans journal Analytique](log-analytics-proxy-firewall.md).

**Q. J’utilise ExpressRoute pour la connexion à Azure. Le trafic de mon journal Analytique utilise ma connexion ExpressRoute ?**  

A. Les différents types de trafic de ExpressRoute sont décrites dans la [documentation de ExpressRoute](./expressroute/expressroute-faqs.md#supported-services).

Le trafic vers le journal Analytique utilise le circuit de ExpressRoute-peering public.

**Q. existe-t-il un moyen simple de déplacer un espace de travail Analytique de journal existant à un autre abonnement d’espace de travail/Azure Analytique de journal ?**  Nous avons OMS espaces de travail plusieurs clients que nous avons tester et évaluer dans notre abonnement Azure et ils passent à la production, nous voulons déplacer vers leur propre abonnement Azure/OMS.  

A. Le `Move-AzureRmResource` applet de commande vous permet de déplacer un espace de travail du journal Analytique et aussi un compte Automation à partir d’un abonnement Azure à un autre. Pour plus d’informations, consultez [Déplacement-AzureRmResource](http://msdn.microsoft.com/library/mt652516.aspx).

Cette modification peut également être effectuée dans le portail Azure.

Vous ne peut pas déplacer les données à partir d’un espace de travail Analytique de journal à un autre ou modifier la zone de données d’Analytique de journal sont stockées dans.

**Q : comment ajouter des OMS SCOM ?**

A : mise à jour vers le dernier correctif cumulatif de mise à jour et l’importation de packs d’administration vous permettra se SCOM Analytique du journal.

Notez que la connexion de SCOM au journal Analytique n’est disponible pour SCOM 2012 SP1 et versions ultérieures.

**Q : Comment puis-je confirmer qu’un agent est en mesure de communiquer avec le journal Analytique ?**

A: pour vous assurer que l’agent peut communiquer avec OMS, accédez à : panneau de configuration, de sécurité et de paramètres, **L’Agent de surveillance de Microsoft**.

Sous l’onglet **Journal de Azure Analytique (OMS)** , recherchez une coche verte. Une icône de coche verte confirme que l’agent est en mesure de communiquer avec le service OMS.

Une icône d’avertissement jaune signifie que l’agent a communication de problèmes avec l’OMS. Une raison commune est le service de l’Agent de surveillance de Microsoft a été arrêté et doit être redémarré.

**Q : comment pour arrêter un agent de communiquer avec le journal Analytique ?**

A: dans SCOM, retirez l’ordinateur de la liste managée OMS. Toutes les communications par le biais de SCOM pour que l’agent s’arrête. Pour les agents d’OMS directement connectés, vous pouvez les arrêter de communiquer avec l’OMS via : panneau de configuration, de sécurité et de paramètres, **L’Agent de surveillance de Microsoft**.
Sous **Azure journal Analytique (OMS)**, supprimez tous les espaces de travail répertoriés.

## <a name="agent-data"></a>Données de l’agent

**Q. combien de données puis-je envoyer par le biais de l’agent à Analytique de journal ? Y a-t-il un volume maximal de données par client ?**  
A. Le plan libre définit une extrémité quotidienne de 500 Mo par espace de travail. Les plans standard et premium ne sont pas limitée dans la quantité de données qui sont téléchargées. Sous la forme d’un service en nuage, Analytique de journal dans l’OMS conçu pour automatiquement une échelle jusqu'à la poignée du volume en provenance d’un client – même si elle est téraoctets par jour.

L’agent de journal Analytique a été conçu pour vous assurer qu’il a un faible encombrement et que vous ne la compression des données de base. Un de nos clients ont écrit un blog sur les tests qu’ils effectués par rapport à notre agent et impressionné comment ils ont été. Le volume de données varie selon les solutions permet à vos clients. Vous pouvez trouver des informations détaillées sur le volume de données et les pertes en solution dans **l’utilisation** mosaïque dans la page de présentation d’OMS.

Pour plus d’informations, vous pouvez lire un [blog du client](http://thoughtsonopsmgr.blogspot.com/2015/09/one-small-footprint-for-server-one.html) sur le faible encombrement de l’agent de l’OMS.

**Q. la bande passante de réseau est utilisé par l’Agent de gestion Microsoft (MMA) lors de l’envoi des données d’Analytique de journal ?**

A. La bande passante est une fonction de la quantité de données envoyées. Données sont compressées lorsqu’elles sont envoyées sur le réseau.

**Q. combien de données est envoyée par l’agent ?**

A. Cela dépend :

- les solutions que vous avez activé
- le nombre de journaux et les compteurs de performances collectés
- le volume de données dans les journaux

Le niveau de tarification libre est un bon moyen intégré plusieurs serveurs et la jauge du volume de données par défaut. Globalement, l’utilisation est indiquée sur la page de **l’utilisation** .
Pour les ordinateurs qui sont en mesure d’exécuter l’agent WireData, vous pouvez voir la quantité de données est envoyé à l’aide de la requête suivante :

```
Type=WireData (ProcessName="C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\MonitoringHost.exe") (Direction=Outbound) | measure Sum(TotalBytes) by Computer
```



## <a name="next-steps"></a>Étapes suivantes

- [Mise en route de journal Analytique](log-analytics-get-started.md) pour en savoir plus sur les journaux Analytique et get opérationnel en quelques minutes.
