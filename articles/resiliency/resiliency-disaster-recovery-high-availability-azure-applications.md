<properties
   pageTitle="Reprise après sinistre et une haute disponibilité pour les Applications Azure | Microsoft Azure"
   description="Présentations techniques et des informations détaillées sur la conception d’applications à haute disponibilité et reprise après sinistre d’applications basées sur Microsoft Azure."
   services=""
   documentationCenter="na"
   authors="adamglick"
   manager="saladki"
   editor=""/>

<tags
   ms.service="resiliency"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/18/2016"
   ms.author="aglick"/>

#<a name="disaster-recovery-and-high-availability-for-applications-built-on-microsoft-azure"></a>Reprise après sinistre et une haute disponibilité des applications basées sur Microsoft Azure

##<a name="introduction"></a>Introduction

Cet article se concentre sur la haute disponibilité des applications en cours d’exécution dans Azure. Une stratégie globale pour une haute disponibilité comprend également l’aspect de la reprise après sinistre. Planification de défaillances et de catastrophes naturelles dans le nuage vous oblige à reconnaître les problèmes rapidement. Puis, vous implémentez une stratégie qui correspond à votre tolérance pour les temps d’arrêt de l’application. En outre, vous devez prendre en compte de l’ampleur de la perte de données que l’application peut tolérer sans entraîner des conséquences économiques défavorables lors de la restauration.

La plupart des sociétés dites qu’ils sont préparés pour des défaillances temporaires et à grande échelle. Toutefois, avant de vous répondez à cette question par vous-même, est votre société la visionnez ces défaillances ? Tester la restauration des bases de données pour vous assurer que les processus appropriés en place ? Probablement pas. C’est parce que la reprise après sinistre réussie commence par lots de planification et de conception pour implémenter ces processus. À l’instar de nombreux autres impératifs non fonctionnels, tels que sécurité, reprise après sinistre obtient rarement l’analyse en amont et l’allocation de temps qu’il requiert. En outre, la plupart des entreprises n’avez pas le budget pour des zones géographiquement distribués avec capacité redondante. Par conséquent, même les applications critiques sont fréquemment exclues de planification de la récupération après sinistre approprié.

Plateformes en nuage, par exemple d’Azure, fournissent des zones géographiquement dispersés dans le monde entier. Ces plates-formes offrent également des fonctionnalités qui prennent en charge une variété de scénarios de récupération après sinistre et de disponibilité. Maintenant, chaque application critique dans le nuage de mission peut être donnée dûment prises en considération pour la reprise après sinistre de vérification du système. Azure a la résilience et la reprise après sinistre intégrée à la plupart de ses services. Vous devez étudier avec soin de ces fonctionnalités de la plate-forme et compléter avec des stratégies d’application.

Cet article explique les étapes nécessaires d’architecture nécessaires à la reprise après sinistre à l’épreuve un déploiement d’Azure. Vous pouvez implémenter le processus de continuité d’activité d’entreprise plus grand. Un plan de continuité d’activité est une feuille de route pour la poursuite des opérations dans des conditions défavorables. Cela peut être un échec grâce à la technologie, par exemple un service en panne ou une catastrophe naturelle, par exemple une orage ou interruption de service. Souplesse d’application de catastrophes est uniquement un sous-ensemble du processus de récupération après sinistre plus grand, comme décrit dans ce document NIST : [Guide de planification d’urgence pour les systèmes informatiques](https://www.fismacenter.com/sp800-34.pdf).

Les sections suivantes définissent différents niveaux de défaillances techniques pour y faire face, ainsi que les architectures qui prennent en charge de ces techniques. Ces informations fournissent les entrées à votre processus de récupération d’urgence et les procédures, pour garantir que votre stratégie de récupération d’urgence fonctionne correctement et efficacement.

##<a name="characteristics-of-resilient-cloud-applications"></a>Caractéristiques des applications de cloud souple

Une application bien conçue peut résister aux défaillances de fonctionnalité à un niveau stratégique et tolérable également des défaillances système stratégiques au niveau de la région. Les sections suivantes définissent la terminologie référencée dans tout le document pour décrire les différents aspects des services de cloud souple.

###<a name="high-availability"></a>Haute disponibilité

Une application en nuage hautement disponible met en œuvre les stratégies pour absorber la panne de dépendances, tels que les services managés offertes par la plateforme en nuage. Malgré les défaillances possibles des capacités de la plate-forme nuage, cette approche permet à l’application de continuer à présentant les caractéristiques systémiques fonctionnels et non fonctionnels attendus. Cela est examinée en détail dans la série vidéo de Channel 9, [Failsafe : conseils pour des Architectures de Cloud souple](https://channel9.msdn.com/Series/FailSafe).

Lorsque vous implémentez l’application, vous devez prendre en compte la probabilité d’une panne de capacité. En outre, pensez à l’impact d’une panne de l’application à partir du point de vue commercial, avant de plonger dans les stratégies de mise en œuvre. Sans date d’échéance en considération l’impact et la probabilité d’atteinte de la condition du risque, l’implémentation peut être coûteux et inutiles.

Envisagez une analogie automobile pour une haute disponibilité. Même des pièces de qualité et de l’ingénierie supérieure n’empêche pas les défaillances occasionnelles. Par exemple, lorsque votre voiture Obtient un pneu à flancs renforcés, le véhicule continue de s’exécuter, mais il fonctionne avec la fonctionnalité de dégradé. Si vous prévu pour cette occurrence potentielle, vous pouvez utiliser une de ces pneumatiques de secours avec bordure fine jusqu'à ce que vous atteigniez un atelier de réparation. Bien que la roue de secours ne permet pas une vitesse plus rapide, vous pouvez fonctionne toujours le véhicule jusqu'à ce que vous remplaciez le pneu. De même, un service en nuage qui prévoit le risque de perte de fonctionnalités peut empêcher un problème relativement mineur interrompre l’application entière. Cela est vrai même si le service en nuage doit s’exécuter avec la fonctionnalité de dégradé.

Il existe quelques caractéristiques clés des services en nuage hautement disponibles : disponibilité, d’évolutivité et de tolérance aux pannes. Bien que ces caractéristiques sont liées entre elles, il est important de comprendre chacune, et comment ils contribuent à la disponibilité globale de la solution.

###<a name="availability"></a>Disponibilité

Une application disponible prend en compte la disponibilité de son infrastructure sous-jacente et les services qui en dépendent. Applications disponibles supprimer des points uniques de défaillance grâce à la redondance et de conception robuste. Lorsque vous étendez la portée à prendre en compte la disponibilité dans Azure, il est important de comprendre le concept de la disponibilité effective de la plate-forme. Disponibilité efficace considère que les accords de niveau de Service (SLA) de chaque service dépendant et leur effet cumulatif sur la disponibilité totale du système.

Disponibilité du système est la mesure du pourcentage d’une fenêtre de temps que le système sera en mesure d’effectuer. Par exemple, la disponibilité du SLA au moins deux instances d’un rôle web ou travailleur dans Azure est 99,95 % (de 100 pour cent). Elle ne mesure pas les performances ou les fonctionnalités des services en cours d’exécution sur ces rôles. Toutefois, la disponibilité efficace de votre service cloud est également affectée par les accords différents des autres services dépendants. Les éléments plus mobiles au sein du système, le plus d’attention que vous devez prendre afin de garantir l’application peut répondre élastique aux exigences de disponibilité de ses utilisateurs finaux.

Considérez les SLA suivantes pour un service Azure qui utilise des services Azure : stockage Azure Compute et base de données de SQL Azure.

|Service Azure|CONTRAT SLA   |Potentiel minutes temps d’arrêt par mois (30 jours)|
|:------------|:-----|:----------------------------------------:|
|Calculer      |99,95 %|21,6 minutes                              |
|Base de données SQL |99,99 %|4.3 minutes                              |
|Stockage      |99,90 %|43,2 minutes                              |

Vous devez planifier pour tous les services de potentiellement à des moments différents. Dans cet exemple simplifié, le nombre total de minutes par mois qui l’application est en panne est 108 minutes. Un mois de 30 jours a un total de 43,200 minutes. 108 minutes est %.25 du nombre total de minutes dans un mois de 30 jours (43,200 minutes). Cela vous donne une disponibilité efficace de 99.75 % pour le service en nuage.

Toutefois, à l’aide de techniques de disponibilité décrits dans ce document peut améliorer cette situation. Par exemple, si vous concevez votre application de continuer à fonctionner lorsque la base de données SQL n’est pas disponible, vous pouvez supprimer de l’équation. Cela peut signifier que l’application s’exécute avec des fonctionnalités réduites, il existe également des besoins de l’entreprise à prendre en compte. Pour obtenir une liste complète des SLA d’Azure, consultez [Les contrats de niveau de Service](https://azure.microsoft.com/support/legal/sla/).

###<a name="scalability"></a>Évolutivité

Évolutivité affecte directement la disponibilité. Une application qui échoue dans l’accroissement de la charge n’est plus disponible. Des applications évolutives sont en mesure de répondre à la demande accrue avec des résultats cohérents dans les fenêtres de temps acceptable. Lorsqu’un système est évolutif, il met à l’échelle horizontalement ou verticalement pour gérer les augmentations de charge tout en préservant la cohérence des performances. En termes de base, mise à l’échelle horizontale ajoute plus de machines de la même taille (processeur, mémoire et bande passante), lors des augmentations de mise à l’échelle verticales la taille des ordinateurs existants. Pour Azure, vous disposez des options de mise à l’échelle verticales de sélection de différentes tailles de machine pour le calcul. Cependant, la modification de la taille de l’ordinateur nécessite un redéploiement. Par conséquent, les solutions plus souples sont conçues pour la mise à l’échelle horizontale. Cela est particulièrement vrai pour le calcul, car vous pouvez augmenter facilement le nombre d’instances de n’importe quel rôle web ou travailleur en cours d’exécution. Ces instances supplémentaires gérer une augmentation du trafic via le portail Web d’Azure, scripts PowerShell ou du code. Baser cette décision sur les augmentations d’indicateurs spécifiques. Dans ce scénario, les performances utilisateur ou des mesures ne subissent pas une baisse notable sous charge. En règle générale, les rôles web et worker stockent tout état en externe. Cela permet l’équilibrage de charge flexible et gestion progressive de toute modification apportée au compte de l’instance. Mise à l’échelle horizontale fonctionne également bien avec les services, tels que le stockage Azure, qui ne fournissent pas de plusieurs niveaux d’options de mise à l’échelle verticale.

Déploiements dans le cloud doivent être considérées comme une collection d’unités de l’échelle. Cela permet à l’application d’être élastique en charge les besoins en débit des utilisateurs finaux. Les unités d’échelle sont plus faciles à visualiser au niveau du serveur web et d’application. Il s’agit, car Azure fournit déjà des nœuds de calcul sans état par le biais de rôles web et worker. Ajout de que plusieurs unités d’échelle pour le déploiement de calcul n’entraîne pas application état gestion des effets secondaires, car le calcul des unités d’échelle sont sans état. Une échelle-unité de stockage est responsable de la gestion d’une partition de données (structuré ou non structurés). Partition de Table d’Azure, conteneur d’Azure Blob et base de données de SQL Azure constituent des exemples d’échelle-unités de stockage. Même l’utilisation de plusieurs comptes de stockage Azure a un impact direct sur l’évolutivité de l’application. Vous devez créer un service en nuage hautement évolutive pour intégrer plusieurs échelle-unités de stockage. Par exemple, si une application utilise des données relationnelles, vous pouvez partitionner les données entre plusieurs bases de données SQL. Ainsi, le stockage suivre le modèle d’unité d’échelle de calcul élastique. De même, le stockage Azure permet de jeux nécessitant des conceptions délibérées pour satisfaire les besoins en débit de la couche de calcul de la segmentation des données. Pour obtenir une liste des meilleures pratiques pour la conception de services de cloud évolutives, consultez [Méthodes conseillées pour la conception de Services à grande échelle sur les Services en nuage Azure](https://azure.microsoft.com/blog/best-practices-for-designing-large-scale-services-on-windows-azure/).

###<a name="fault-tolerance"></a>Tolérance de pannes

Applications doivent supposer que chaque fonction de nuage dépendant peut et va tomber en panne à un moment donné dans le temps. Une application à tolérance de pannes détecte et manœuvres concurrentielles autour des éléments a échoué, continuer et renvoient les résultats corrects au cours d’une période spécifique. Pour les conditions d’erreur transitoire, un système à tolérance de pannes emploie une stratégie de nouvelle tentative. Pour les problèmes plus graves, l’application peut détecter les problèmes et basculer sur un matériel de secours ou les plans d’urgence jusqu'à ce que le problème a été corrigé. Une application fiable peut correctement gérer la défaillance d’un ou de plusieurs parties et continuer à fonctionner correctement. Applications à tolérance de pannes peuvent utiliser une ou plusieurs stratégies de conception, telles que la redondance, la réplication ou dégradation des fonctionnalités.

##<a name="disaster-recovery"></a>Reprise après sinistre

Un déploiement de nuage peut cesser de fonctionner en raison d’une panne systémique des services dépendants ou de l’infrastructure sous-jacente. Dans ces conditions, un plan de continuité d’activité déclenche le processus de récupération après sinistre. Ce processus implique en général au personnel des opérations et procédures automatisées pour réactiver l’application dans une région disponibles. Cette opération nécessite le transfert des utilisateurs de l’application, données et services vers la nouvelle zone. Il implique également l’utilisation de supports de sauvegarde ou de réplication en cours.

Envisagez l’analogie précédente par rapport à haute disponibilité, la capacité à récupérer à partir d’un pneu à flancs renforcés à l’aide d’un disque de secours. En revanche, la récupération d’urgence implique les étapes effectuées après une panne de voiture, où la voiture n’est plus opérationnelle. Dans ce cas, la meilleure solution est de trouver un moyen efficace pour modifier des voitures, en appelant un service de voyage ou d’un ami. Dans ce scénario, il va probablement être un plus long délai de retour sur la route. Il est également plus de complexité dans la réparation et le retour sur le véhicule d’origine. De la même façon, la reprise après sinistre dans une autre région est une tâche complexe qui implique généralement des temps d’arrêt et la perte potentielle de données. Pour mieux comprendre et évaluer les stratégies de récupération d’urgence, il est important de définir deux termes : objectif de temps de récupération (RTO) et de la récupération de point (RPO).

###<a name="recovery-time-objective"></a>Objectif de temps de récupération

Le RTO est la durée maximale allouée pour la restauration des fonctionnalités de l’application. Elle est basée sur les besoins de l’entreprise, et il est lié à l’importance de l’application. Les applications critiques de l’entreprise nécessitent un faible RTO.

###<a name="recovery-point-objective"></a>Objectif de point de récupération

Le RPO est la fenêtre de temps acceptable de perte de données en raison du processus de récupération. Par exemple, si le RPO est d’une heure, vous devez complètement sauvegarder ou répliquer les données au moins toutes les heures. Une fois l’application dans une autre région, les données de sauvegarde peuvent manquer jusqu'à une heure de données. Comme le RTO, applications critiques ciblent un RPO de plus petit quantité.

##<a name="checklist"></a>Liste de vérification

Nous allons résumer les points clés traités dans cet article (et ses articles connexes sur la [haute disponibilité](./resiliency-high-availability-azure-applications.md) et [reprise après sinistre](./resiliency-disaster-recovery-azure-applications.md) pour les applications Azure). Ce résumé agit comme une liste de contrôle d’éléments, que vous devez envisager pour votre disponibilité et la planification de la récupération après sinistre. Il s’agit des méthodes conseillées qui vous ont été utiles pour les clients qui cherchent à obtenir grave sur l’implémentation d’une solution efficace.

1. Procéder à une évaluation des risques pour chaque application, dans la mesure où chacun peut avoir des exigences différentes. Certaines applications sont plus critiques que d’autres personnes et seraient de justifier le surcoût pour leur architecture de reprise après sinistre.
1. Utilisez ces informations pour définir les RTO et RPO pour chaque application.
1. Conception pour la panne, en commençant par l’architecture de l’application.
1. Mettre en œuvre les meilleures pratiques pour une haute disponibilité, tout en équilibrant les coûts, la complexité et les risques.
1. Mettre en œuvre des plans de reprise et de processus.
  * Prendre en compte les défaillances qui s’étendent sur le niveau du module à une panne complète de nuage.
  * Établir des stratégies de sauvegarde pour toutes les données transactionnelles et de référence.
  * Choisir une architecture de reprise après sinistre multisite.
1. Permet de définir un propriétaire spécifique pour le processus de récupération d’urgence, l’automatisation et de test. Le propriétaire doit gérer et possèdent l’ensemble du processus.
1. Documenter les processus afin qu’elles soient facilement reproductibles. Bien qu’il y a un seul propriétaire, plusieurs personnes doivent être en mesure de comprendre et de suivre les processus en cas d’urgence.
1. Former le personnel pour implémenter le processus.
1. Utiliser des simulations de reprise d’activité régulière de formation et de validation du processus.

##<a name="summary"></a>Résumé

Lorsque le matériel ou les applications échouent dans Azure, les techniques et les stratégies pour les gérer sont différents en cas de panne sur les systèmes locaux. La principale raison à cela est que les solutions de cloud ont généralement plus de dépendances sur l’infrastructure qui est distribué sur une zone Azure et géré comme des services distincts. Vous devez traiter les défaillances partielles à l’aide de techniques de haute disponibilité. Pour gérer les défaillances plus graves, probablement en raison d’un événement de reprise après sinistre, utilisez stratégies de récupération d’urgence.

Azure détecte et gère de nombreuses défaillances, mais il existe de nombreux types d’échecs qui nécessitent des stratégies spécifiques à l’application. Vous devez activement préparer et gérer les défaillances des applications, des services et des données.

Lors de la création de votre application de disponibilité et de récupération d’urgence, prendre en compte les conséquences de l’entreprise de défaillance de l’application. Définition des processus, des stratégies et des procédures pour restaurer les systèmes critiques après qu’un événement catastrophique prend du temps, planification et l’engagement. Et une fois que vous établissez les plans, vous ne pouvez pas arrêter il. Vous devez analyser régulièrement, tester et améliorer en permanence les plans en fonction de votre portefeuille d’applications, des besoins de l’entreprise et les technologies disponibles pour vous. Azure offre de nouvelles capacités et génère de nouveaux défis à la création d’applications robustes qui résister aux défaillances.

##<a name="additional-resources"></a>Ressources supplémentaires

[Haute disponibilité des applications basées sur Microsoft Azure](resiliency-high-availability-azure-applications.md)

[Reprise après sinistre pour les applications basées sur Microsoft Azure](resiliency-disaster-recovery-azure-applications.md)

[Guide technique de résilience Azure](resiliency-technical-guidance.md)

[Vue d’ensemble : Cloud business continuité d’activité et de la base de données de récupération après sinistre de la base de données SQL](../sql-database/sql-database-business-continuity.md)

[Haute disponibilité et reprise après sinistre pour SQL Server dans Azure Virtual Machines](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md)

[Sécurité intégrée : Conseils pour des architectures de cloud souple](https://channel9.msdn.com/Series/FailSafe)

[Méthodes conseillées pour la conception de services à grande échelle sur les Services en nuage Azure](https://azure.microsoft.com/blog/best-practices-for-designing-large-scale-services-on-windows-azure/)

##<a name="next-steps"></a>Étapes suivantes

Cet article fait partie d’une série d’articles consacré à la reprise après sinistre et une haute disponibilité pour les applications Azure. Le prochain article de cette série est la [haute disponibilité des applications basées sur Microsoft Azure](resiliency-high-availability-azure-applications.md).
