<properties
   pageTitle="Liste de vérification de résilience | Microsoft Azure"
   description="Liste de contrôle qui fournit des informations sur les problèmes de résilience lors de la conception."
   services=""
   documentationCenter="na"
   authors="petertaylor9999"
   manager="christb"
   editor=""
   tags=""/>

<tags
   ms.service="best-practice"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="petertay"/>

# <a name="azure-resiliency-guidance-resiliency-checklist"></a>Conseils de résilience Azure : liste de contrôle de résilience

Conception de votre application de reprise nécessite pour la planification et l’atténuation des différents modes de défaillance qui peut se produire. Passez en revue les éléments de cette liste de vérification sur la conception de votre application pour la rendre plus robuste.

## <a name="requirements"></a>Configuration requise

- **Définir les exigences de disponibilité de votre client.** Votre client aura des exigences de disponibilité des composants dans votre application, et cela affectera la conception de votre application. Obtenir l’accord du client pour les objectifs de disponibilité de chaque élément de votre application, sinon votre conception ne répondent pas aux attentes des clients. Pour plus d’informations, consultez la section [définition de vos besoins en matière de résilience](guidance-resiliency-overview.md#defining-your-resiliency-requirements) du document de [conception d’applications robustes pour Azure](guidance-resiliency-overview.md) .

## <a name="failure-mode-analysis"></a>Analyse en Mode d’échec

- **Effectuer une analyse de mode de défaillance (FMA) pour votre application.** FMA est un processus pour la création de résilience dans une application très tôt dans la phase de conception. Les objectifs d’un FMA incluent :  

    - Identifier les types de défaillances qu’une application peut rencontrer. 
    - Capturer les effets et l’impact de chaque type de défaillance de l’application.
    - Identifier les stratégies de récupération. 

    Pour plus d’informations, consultez [concevoir des applications robustes pour Azure : analyse de l’échec en mode][fma].  

## <a name="application"></a>Application

- **Déployer plusieurs instances de services.** Services inévitablement échouera, et si votre application dépend d’une seule instance d’un service inévitablement échouera également. Pour configurer plusieurs instances de [Service d’application Azure](../app-service/app-service-value-prop-what-is.md), sélectionnez un [Plan de Service d’application](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) qui offre plusieurs instances. Pour les Services en nuage Azure, configurer chacun des rôles d’utiliser [plusieurs instances](../cloud-services/cloud-services-choose-me.md#scaling-and-management). Pour les [ordinateurs virtuels de Azure (VMs)](../virtual-machines/virtual-machines-windows-about.md), assurez-vous que votre architecture de machine virtuelle plus d’un ordinateur virtuel ainsi que chaque machine virtuelle est inclus dans un [jeu de disponibilité][availability-sets].   

- **Utilisez un équilibreur de charge pour distribuer des demandes.** Un équilibreur de charge répartit les demandes de votre application à des instances de service sain en supprimant les instances défectueux de rotation. Si votre service utilise l’application Azure ou les Services de nuage d’Azure, il est déjà équilibré pour vous. Toutefois, si votre application utilise les ordinateurs virtuels d’Azure, vous devrez mettre en service un équilibreur de charge. Consultez la vue d’ensemble de [l’Équilibrage de la charge Azure](../load-balancer/load-balancer-overview.md) pour plus de détails. 

- **Configurer les passerelles d’Application Azure pour utiliser plusieurs instances.** Selon les spécifications de votre application, une [Passerelle d’Application Azure](../application-gateway/application-gateway-introduction.md) peuvent être mieux adaptés à la distribution des demandes vers les services de votre application. Toutefois, les instances uniques du service Gateway de l’Application ne sont pas garantis par un contrat SLA, il est possible que votre application peut échouer en cas d’échec de l’instance d’Application Gateway. Configurer plus d’une instance de passerelle d’Application moyenne ou plue afin de garantir la disponibilité du service dans les conditions prévues par le [contrat SLA](https://azure.microsoft.com/support/legal/sla/application-gateway/v1_0/).

- **Utilisation des ensembles de disponibilité pour chaque couche de l’application**. Placer vos instances dans un [jeu de disponibilité] [ availability-sets] garantit la connectivité au moins une instance de machine virtuelle dans les conditions de l' [entente SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_2/). Si vos ordinateurs virtuels ne sont pas dans un jeu de disponibilité que vous n’avez pas garantit leur protection et il est possible qu’ils tous tombent en panne ou être mis à jour simultanément. 

- **Envisagez de déployer votre application dans plusieurs régions.** Si votre application est déployée dans une seule région, dans les rares cas l’ensemble de la région n’est plus disponible, votre application n’est également pas disponible. Ceci peut être inacceptable aux termes du contrat SLA de votre application. Si tel est le cas, pensez à déployer votre application et ses services dans plusieurs régions. Un déploiement de plusieurs région peut utiliser un modèle actif-actif (répartissant les requêtes sur plusieurs instances actives) ou un modèle actif-passif (garder en réserve, une instance « à chaud » en cas d’échec de l’instance principale). Nous vous recommandons de déployer plusieurs instances de services de votre application sur des paires régionaux. Pour plus d’informations, consultez [Business continuité d’activité et reprise après sinistre (BCDR) : régions associés d’Azure](../best-practices-availability-paired-regions.md).

- **Le cas échéant, mettre en œuvre des modèles de résilience pour les opérations distantes.** Si votre application dépend de la communication entre les services à distance, le chemin de communication échoue inévitablement. S’il existe plusieurs échecs, les autres instances de bon fonctionnement de services de votre application peuvent être submergés de demandes. Il existe plusieurs modèles utiles pour traiter des défaillances courantes, y compris le modèle de délai d’attente, [recommencer le motif]de la[retry-pattern], le [disjoncteur] [ circuit-breaker] modèle et autres. Pour plus d’informations, consultez [conception d’applications robustes pour Azure](guidance-resiliency-overview.md#implementing-resiliency-strategies). 

- **Autoscaling permet de répondre à l’augmentation de la charge.** Si votre application n’est pas configurée pour évoluer automatiquement à mesure que la charge augmente, il est possible que les services de votre application échouera si elles deviennent saturés par les demandes de l’utilisateur. Pour plus d’informations, consultez les rubriques suivantes :

    - Général : [liste de contrôle de l’évolutivité](../best-practices-scalability-checklist.md) 
    - Service d’application Azure : [nombre d’instances à l’échelle manuellement ou automatiquement][app-service-autoscale]
    - Les Services en nuage : [comment un service en nuage à l’échelle automatique][cloud-service-autoscale]
    - Les ordinateurs virtuels : [échelle de machine virtuelle et de la mise à l’échelle automatique définit][vmss-autoscale]


- **Mettre en œuvre des opérations asynchrones, à chaque fois que possible.** Les opérations synchrones peuvent monopoliser les ressources et bloquer d’autres opérations pendant que l’appelant attend la fin du processus. Concevoir chaque partie de votre application pour permettre des opérations asynchrones, à chaque fois que possible. Pour plus d’informations sur la façon d’implémenter la programmation asynchrone dans C#, consultez [la programmation asynchrone avec async et await][asynchronous-c-sharp].

- **Utilisez le Gestionnaire de trafic d’Azure pour acheminer le trafic de votre application à différentes régions.**  [Azure Traffic Manager] [ traffic-manager] exécute l’équilibrage au niveau du DNS et peut router le trafic pour le [routage du trafic] en fonction de différentes régions[ traffic-manager-routing] méthode que vous spécifiez et l’état de santé des points de terminaison de votre application. 

- **Configurer et tester les sondes de santé pour votre équilibreurs de charge et les responsables du trafic.** Assurez-vous que votre logique de santé vérifie les parties critiques du système et répond correctement aux sondes de santé.

    - L’état de santé des sondes pour [Azure Traffic Manager] [ traffic-manager] et [d’Équilibrage de la charge Azure] [ load-balancer] remplissent une fonction spécifique. Pour le Gestionnaire de trafic, la sonde de santé détermine s’il faut basculer vers une autre région. Pour un équilibrage de la charge, il détermine s’il faut supprimer une machine virtuelle à partir de la rotation.      

    - Pour une sonde de Traffic Manager, votre point de terminaison de santé doit vérifier toutes les dépendances critiques qui sont déployés dans la même région, et dont l’échec doit déclencher un basculement vers une autre région.  

    - Pour un équilibreur de charge, le point de terminaison de santé doit signaler l’état de santé de la machine virtuelle. Ne pas inclure les autres couches ou services externes. Dans le cas contraire, une erreur qui se produit à l’extérieur de la machine virtuelle entraînera l’équilibreur de charge supprimer la machine virtuelle à partir de la rotation.

    - Pour obtenir des instructions sur l’implémentation du contrôle d’état dans votre application, consultez [Modèle de surveillance de point de terminaison de santé](https://msdn.microsoft.com/library/dn589789.aspx).

- **Surveiller les services tiers.** Si votre application a des dépendances sur les services tiers, identifier l’emplacement où et comment ces services tiers peuvent échouer et les effets que ces échecs auront sur votre application. Un service tiers ne peut pas inclure la surveillance et diagnostic, il est important de connecter vos appels d’eux et de les mettre en corrélation avec la santé de votre application et de diagnostic de journalisation à l’aide d’un identificateur unique. Pour plus d’informations sur les meilleures pratiques pour la surveillance et de diagnostic, consultez le [Guide de surveillance et de diagnostic] [ monitoring-and-diagnostics-guidance] document.

- **Assurez-vous que tout service tiers que vous consommez fournit un contrat SLA.** Si votre application dépend d’un service tiers, mais le tiers ne fournit aucune garantie de disponibilité sous la forme d’un contrat SLA, disponibilité de votre application aussi ne peut pas être garantie. Votre accord SLA n’est efficace que si le composant moins disponible de votre application.


## <a name="data-management"></a>Gestion des données

- **Comprendre les méthodes de réplication pour les sources de données de votre application.** Vos données d’application seront stockées dans différentes sources de données et ont des exigences de disponibilité différentes. Évaluer les méthodes de réplication pour chaque type de stockage de données dans Azure, y compris [La réplication du stockage Azure](../storage/storage-redundancy.md) et [SQL de base de données géo-réplication Active](../sql-database/sql-database-geo-replication-overview.md) pour vous assurer que les exigences de votre application sont satisfaites.

- **Assurez-vous qu’aucun compte d’utilisateur unique n’a accès aux données de production et de sauvegarde.** Sauvegardes de données sont compromis si un seul compte d’utilisateur a l’autorisation d’écrire dans la production et les sources de sauvegarde. Un utilisateur malveillant pourrait supprimer volontairement de toutes vos données, tandis que l’utilisateur normal peut accidentellement supprimer. Concevez votre application pour limiter les autorisations de chaque compte d’utilisateur afin que seuls les utilisateurs qui requièrent un accès en écriture ont un accès en écriture et qu’il est uniquement à la production ou de sauvegarde, mais pas les deux.

- **Document basculer votre source de données et la reprise, traiter et testez-le.** Dans le cas où la source de données échoue inopinée, un opérateur humain doivent suivre un ensemble d’instructions relatives à basculer vers une nouvelle source de données. Si les étapes documentées disposent erreurs, un opérateur ne sera pas en mesure de les suivre et basculement de la ressource. Tester régulièrement les étapes de l’instruction pour vérifier si un opérateur suit les est en mesure de basculer correctement et de basculer de la source de données.

- **Valider vos sauvegardes de données.** Vérifier régulièrement que vos données de sauvegarde sont bien celui prévu en exécutant un script pour valider l’intégrité des données, un schéma et requêtes. Il n’y a aucun point d’avoir une sauvegarde s’il n’est pas utile restaurer vos sources de données. Consigner et de signaler les incohérences afin que le service de sauvegarde peut être réparé.

- **Envisagez d’utiliser un type de compte de stockage géo-redondantes.** Données stockées dans un compte de stockage Azure sont toujours répliquées localement. Toutefois, il y a plusieurs stratégies de réplication à choisir lors d’un compte de stockage est mis en service. Sélectionnez [Stockage redondant Geo de Azure-accès en lecture (RA-GRS)](../storage/storage-redundancy.md#read-access-geo-redundant-storage) pour protéger vos données d’application contre les rares cas lorsqu’une région entière n’est pas disponible.

    > [AZURE.NOTE] Pour les machines virtuelles, ne comptez pas sur la réplication de RA-GRS pour restaurer les disques de l’ordinateur virtuel (fichiers VHD). À la place, utilisez [Azure sauvegarde][azure-backup].   

## <a name="operations"></a>Opérations

- **Implémenter une surveillance et des alertes de meilleures pratiques dans votre application.** Sans bon de surveillance, de diagnostic et d’alerte, il n’y a aucun moyen de détecter les défaillances dans votre application et prévenir un opérateur pour les résoudre. Pour plus d’informations sur les meilleures pratiques, consultez les [conseils de surveillance et de diagnostic] [ monitoring-and-diagnostics-guidance] document.

- **Mesure des statistiques d’appel à distance et les informations disponibles à l’équipe de l’application.**  Si vous ne le suivi et rapport des statistiques d’appel à distance en temps réel et fournissent un moyen facile de consulter ces informations, l’équipe n’aura pas une vue instantanée à la santé de votre application. Et si vous uniquement durée moyenne des appels à distance de mesure, vous n’aura suffisamment d’informations pour faire apparaître les problèmes dans les services. Résument les métriques, appel à distance, tels que la latence, le débit et les erreurs dans les centiles 99 et 95. Effectuer une analyse statistique sur les mesures à identifier les erreurs qui se produisent dans chaque position.

- **Suivre le nombre d’exceptions transitoires et de nouvelles tentatives sur une période appropriée.** Si vous ne suivre et surveiller des exceptions en régime transitoire et nouvelles tentatives dans le temps, il est possible qu’un problème ou une défaillance pourrait provenir de logique des nouvelles tentatives de votre application. C'est-à-dire que si votre analyse et la journalisation uniquement indique la réussite ou l’échec d’une opération, le fait que l’opération devait être tentée à nouveau plusieurs fois en raison des exceptions est masqué. Une tendance d’augmentation des exceptions au fil du temps indique que le service a rencontré un problème et peut échouer. Pour plus d’informations, reportez-vous à la section [Réessayer les orientations spécifiques de service][retry-service-guidance].

- **Mettre en œuvre un système d’avertissement rapide qui alerte un opérateur.** Identifier la clé des performances latence des indicateurs de la santé de votre application, par exemple les exceptions en régime transitoire et à distance et définir les valeurs seuils appropriées pour chacun d’eux. Envoyer une alerte aux opérations lorsque le seuil est atteint. Définissez ces seuils à des niveaux qui identifient les problèmes avant qu’ils deviennent critiques et exigent une réponse de récupération.

- **Documenter le processus de publication de votre application.** Sans documentation du processus de lancement détaillé, un opérateur peut déployer une mise à jour incorrecte ou mal configurer les paramètres de votre application. Définir clairement et votre processus de publication de document et ce qu’il soit disponible à toute l’équipe des opérations. Meilleures pratiques de déploiement souple de votre application sont détaillées dans le [déploiement souple] [ guidance-resilient-deployment] section du document Guide de résilience.

- **S’assurer que plusieurs personnes de l’équipe est formée pour contrôler l’application et effectuer toutes les étapes de récupération manuelles.** Si vous avez uniquement un opérateur unique de l’équipe, qui permet de contrôler l’application et de lancer une procédure de récupération, celui-ci devient un point de défaillance unique. Formez plusieurs personnes sur la détection et de récupération et assurez-vous qu’il existe toujours au moins un actif à tout moment.

- **Automatiser le processus de déploiement de votre application.** Si votre personnel d’exploitation est nécessaire pour déployer manuellement votre application, une erreur humaine peut provoquer le déploiement échoue. Pour plus d’informations sur les meilleures pratiques pour l’automatisation du déploiement d’applications, consultez le [déploiement souple] [ guidance-resilient-deployment] section du document Guide de résilience.

- **Concevoir votre processus de publication afin d’optimiser la disponibilité des applications.** Si votre processus de publication nécessite des services pour passer en mode hors connexion pendant le déploiement, votre application n’est pas disponible jusqu'à ce qu’ils sont en ligne. Utiliser la technique de déploiement [bleu/vert](http://martinfowler.com/bliki/BlueGreenDeployment.html) ou [libérer des Canaries](http://martinfowler.com/bliki/CanaryRelease.html) pour déployer votre application à la production. Ces deux techniques impliquent le déploiement de votre code avec le code de production de la version pour les utilisateurs de code de version peuvent être redirigés vers code de production en cas de panne. Pour plus d’informations, consultez le [déploiement souple] [ guidance-resilient-deployment] section du document Guide de résilience.

- **Journalisation et audit des déploiements de votre application.** Si vous utilisez mis en place des techniques de déploiement, telles que des versions bleu/vert ou canary qu’il y aura plus d’une version de votre application en production. Si un problème survient, il est essentiel de déterminer la version de votre application est à l’origine un problème. Implémenter une stratégie de journalisation fiable pour capturer autant d’informations spécifiques à la version que possible. 

- **Assurez-vous que votre application ne s’exécute pas se [limite d’abonnement Azure](../azure-subscription-service-limits.md).** Abonnements Azure ont des limites sur certains types de ressources, telles que le nombre de groupes de ressources, le nombre de cœurs et nombre de comptes de stockage.  Si les exigences de votre application dépassent les limites de l’abonnement Azure, créez un autre Azure abonnement et mise à disposition des ressources suffisantes il.

- **Assurez-vous que votre application ne s’exécute pas se [limite de service par service](../azure-subscription-service-limits.md).** Services Azure individuels ont des limites de consommation &mdash; par exemple, la limite de stockage, le débit, le nombre de connexions, requêtes par seconde et les autres mesures. Votre application échouera s’il tente d’utiliser des ressources situées au-delà de ces limites. Ainsi, service limitation possible de périodes d’interruption pour les utilisateurs affectés. 

    Selon le service et les exigences de votre application, vous pouvez souvent éviter ces limites par l’évolution verticale (par exemple, en choisissant un autre niveau de tarification) ou horizontale (ajout de nouvelles instances).  

- **Concevoir les besoins de stockage de votre application appartenant à des cibles de performances et l’évolutivité du stockage Azure.** Stockage Azure est conçu pour fonctionner dans des cibles prédéfinies de l’évolutivité et les performances, vous devez concevoir votre application pour utiliser le stockage au sein de ces cibles. Si vous dépassez ces cibles de votre application rencontreront la limitation de stockage. Pour résoudre ce problème, configurer des comptes de stockage supplémentaires. Si vous exécutez contre la limite du compte de stockage, mise en service des abonnements supplémentaires Azure puis de provisionner et autres comptes de stockage. Pour plus d’informations, consultez [cibles de performances et de l’évolutivité du stockage Azure](../storage/storage-scalability-targets.md).

- **Sélectionnez la machine virtuelle de taille adaptée à votre application.** Mesurer le CPU, mémoire, disque et e/s de vos ordinateurs virtuels dans la production réelle et vérifiez que la taille de la mémoire virtuelle que vous avez sélectionné est suffisante. Dans le cas contraire, votre application peut rencontrer des problèmes de capacité que les ordinateurs virtuels approchent de ses limites. Taille de la mémoire virtuelle est décrites en détail dans le document de [tailles pour les machines virtuelles dans Azure](../virtual-machines/virtual-machines-windows-sizes.md) .

- **Déterminer si la charge de travail de votre application est stable ou fluctuante au fil du temps.** Si votre charge de travail varie dans le temps, utilisez Azure VM échelle définit à l’échelle automatiquement le nombre d’instances de la machine virtuelle. Dans le cas contraire, vous devrez manuellement augmenter ou diminuer le nombre de machines virtuelles. Pour plus d’informations, consultez la [Vue d’ensemble des jeux d’ordinateur virtuel échelle](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md).

- **Sélectionnez le niveau de service adéquat pour la base de données de SQL Azure.** Si votre application utilise la base de données de SQL Azure, assurez-vous que vous avez sélectionné le niveau de service approprié. Si vous sélectionnez un niveau qui n’est pas en mesure de gérer les besoins de l’unité (DTU) de transaction de base de données de votre application, l’utilisation de données est accélérée. Pour plus d’informations sur la sélection du plan de service correct, consultez le [les options de base de données SQL et les performances : comprendre ce qui est disponible dans chaque niveau de service](../sql-database/sql-database-service-tiers.md) document. 

- **Avoir un plan de restauration pour le déploiement.** Il est possible que votre déploiement de l’application peut échouer et provoquer l’indisponibilité de votre application. Conception d’un processus de retour arrière pour revenir à une dernière bonne version connue et réduire les interruptions de service. Consultez le [déploiement souple] [ guidance-resilient-deployment] section du document Guide de la résilience pour plus d’informations. 

- **Créer un processus pour l’interaction avec la prise en charge Azure.** Si le processus de contact [d’Azure prend en charge](https://azure.microsoft.com/support/plans/) n’est pas défini avant que le besoin de contacter le support technique, temps d’indisponibilité est prolongée comme cible de la navigation pour la première fois le processus de prise en charge. Inclure le processus pour contacter le support technique et de remontée des problèmes dans le cadre de la résistance de votre application dès le début.

- **Assurez-vous que votre application n’utilise pas plus que le nombre maximal de comptes de stockage par abonnement.** Azure autorise un maximum de 200 comptes de stockage par abonnement. Si votre application requiert plusieurs comptes de stockage actuellement disponibles dans votre abonnement, vous devrez créer un nouvel abonnement et créer des comptes supplémentaires de stockage. Pour plus d’informations, reportez-vous à la section [abonnement Azure et les limites de service, les quotas et les contraintes](../azure-subscription-service-limits.md#storage-limits).

- **Assurez-vous que votre application ne dépasse pas les objectifs d’évolutivité pour les disques de l’ordinateur virtuel.** Un ordinateur virtuel IaaS de Azure prend en charge l’attachement d’un nombre de disques de données en fonction de plusieurs facteurs, notamment la taille de mémoire virtuelle et le type de compte de stockage. Si votre application dépasse les objectifs d’évolutivité pour les disques de l’ordinateur virtuel, configurer des comptes de stockage supplémentaires et créer les disquettes de la machine virtuelle il. Pour plus d’informations, reportez-vous à la section [l’évolutivité du stockage Azure et des cibles de performances] (... / storage/storage-scalability-targets.md#scalability-targets-for-virtual-machine-disks)

## <a name="test"></a>Test

- **Effectuer le basculement et restauration automatique de test pour votre application.** Si vous n’avez pas testés de basculement et restauration automatique, vous ne peut pas être certain que les services associés dans votre application revenir de manière synchronisée au cours de la reprise après sinistre. Assurez-vous que dépend de votre application services de basculement sur incident et dans l’ordre correct.

- **Effectuer des tests pour votre application d’injection.** Votre application peut échouer pour différentes raisons, d’expiration du certificat, épuisement des ressources du système dans une machine virtuelle, ou les défaillances de stockage. Testez votre application dans un environnement aussi proche que possible de la production, par simulation ou de déclencher des défaillances réelles. Par exemple, supprimer les certificats, artificiellement consomment des ressources système ou une source de stockage. Vérifiez la capacité de votre application pour récupérer à partir de tous les types de défaillances, seuls et dans une combinaison. Vérifiez que les défaillances ne sont pas matériels de multiplication ou en cascade par le biais de votre système.

- **Exécuter des tests de production à l’aide des données utilisateur synthétiques et réel.** Test et production sont rarement identiques, il est donc important d’utiliser bleu/vert ou un déploiement canary et tester votre application en production. Cela vous permet de tester votre application en production en charge réel et s’assurer qu’il fonctionnera comme prévu lorsque le déploiement complet.

## <a name="security"></a>Sécurité

- **Mettre en œuvre une protection au niveau de l’application distribuée attaques par déni de service (distribué DDoS).** Services Azure sont protégés contre des attaques DDos au niveau de la couche réseau. Toutefois, Azure ne peut pas protéger contre les attaques de couche d’application, car il est difficile de faire la distinction entre les demandes de l’utilisateur true à partir de demandes de l’utilisateur malveillant. Pour plus d’informations sur la protection contre les attaques par déni de couche d’application, consultez la section « Protection contre DDoS » de la [Sécurité du réseau Microsoft Azure](http://download.microsoft.com/download/C/A/3/CA3FC5C0-ECE0-4F87-BF4B-D74064A00846/AzureNetworkSecurity_v3_Feb2015.pdf) (téléchargement PDF).

- **Mettre en œuvre le principe du moindre privilège pour l’accès aux ressources de l’application.** La valeur par défaut pour l’accès aux ressources de l’application doit être aussi restrictive que possible. Accorder des autorisations de niveau supérieur sur une base d’approbation. L’octroi de l’accès aux ressources de votre application trop permissif par défaut peut entraîner une personne intentionnellement ou accidentellement supprimer des ressources. Azure fournit un [contrôle d’accès basé sur des rôles](../active-directory/role-based-access-built-in-roles.md) pour gérer les privilèges de l’utilisateur, mais il est important de vérifier les autorisations de privilège minimum pour les autres ressources disposant de leurs propres systèmes d’autorisations tels que SQL Server. 

## <a name="telemetry"></a>Télémétrie

- **Enregistre les données de télémétrie pendant l’exécution de l’application dans l’environnement de production.** Capturer les informations télémétriques robuste pendant que l’application s’exécute dans l’environnement de production ou que vous n’aurez pas suffisamment d’informations pour diagnostiquer la cause des problèmes pendant qu’il est occupé par les utilisateurs. Informations supplémentaires sont disponibles dans la consignation des meilleures pratiques est disponible dans le [Guide de surveillance et de diagnostic] de la[ monitoring-and-diagnostics-guidance] document.

- **Implémenter la journalisation à l’aide d’un modèle asynchrone.** Si les opérations de journalisation sont synchrones, ils peuvent bloquer votre code d’application. Assurez-vous que vos opérations de journalisation sont implémentées comme des opérations asynchrones. 

- **Permet de mettre en corrélation les données du journal au-delà des limites de service.** Dans une application multicouche classique, une demande de l’utilisateur peut parcourir plusieurs limites de service. Par exemple, une demande de l’utilisateur provient généralement de la couche web et est passée à la couche d’entreprise et enfin rendues persistantes dans la couche de données. Dans les scénarios plus complexes, une demande de l’utilisateur peut-être être distribuée à plusieurs magasins de données et des services différents. Assurez-vous que votre système de journalisation met en corrélation les appels au-delà des limites de service afin de suivre la demande tout au long de votre application.

##  <a name="azure-resources"></a>Ressources Azure 

- **Utiliser des modèles d’Azure le Gestionnaire de ressources à disposition des ressources.** Modèles du Gestionnaire de ressources rendent plus faciles à automatiser les déploiements via PowerShell ou l’interface CLI Azure, qui conduit à un processus de déploiement plus fiable. Pour plus d’informations, consultez [vue d’ensemble du Gestionnaire de ressources Azure][resource-manager].

- **Fournir les ressources des noms explicites.** Donner des noms significatifs aux ressources rend plus facile de localiser une ressource spécifique et de comprendre son rôle. Pour plus d’informations, consultez [recommandé de conventions d’appellation des ressources Azure](guidance-naming-conventions.md) 

- **Utiliser le contrôle d’accès basé sur les rôles (RBAC)**. RBAC permet de contrôler l’accès aux ressources Azure que vous déployez. RBAC permet d’assigner des rôles d’autorisations aux membres de votre équipe DevOps, pour empêcher la suppression accidentelle ou modifications de ressources déployées. Pour plus d’informations, consultez [mise en route de la gestion de l’accès dans le portail Azure](../active-directory/role-based-access-control-what-is.md) 

- **Utiliser les verrouillages des ressources pour les ressources critiques, tels que les ordinateurs virtuels.** Verrouillages des ressources empêchent la suppression accidentelle d’une ressource d’un opérateur. Pour plus d’informations, consultez les [ressources avec le Gestionnaire de ressources Azure](../resource-group-lock-resources.md) 

- **Paires régionaux.** Lors du déploiement de deux régions, choisissez régions à partir de la même paire régionale. En cas de panne large, récupération d’une région est prioritaire sur toutes les paires. Certains services tels que le stockage redondant Geo fournissent une réplication automatique vers la zone de paires. Pour plus d’informations, consultez [Business continuité d’activité et reprise après sinistre (BCDR) : régions de Azure associés](../best-practices-availability-paired-regions.md) 

- **Organiser les groupes de ressources par fonction et du cycle de vie**.  En règle générale, un groupe de ressources doit contenir des ressources qui partagent le même cycle de vie. Cela rend plus facile à gérer les déploiements, supprimez les déploiements de test et assigner des droits d’accès, réduisant ainsi le risque qu’un déploiement de production est accidentellement supprimé ou modifié. Créer des groupes de ressources distincts pour la production, de développement et les environnements de test. Dans un déploiement de plusieurs région, mettre des ressources pour chaque région dans les groupes de ressources distincts. Cela facilite la redéployer une région sans affecter l’ou les autre régions. 

## <a name="azure-services"></a>Services Azure 

Les éléments de liste de contrôle suivants s’appliquent à des services spécifiques dans Azure.

###  <a name="app-service"></a>Service d’application 

- **Utiliser la couche Standard ou Premium.** Ces niveaux de prise en charge des emplacements de la zone de transit et sauvegardes automatisées. Pour plus d’informations, reportez-vous à la section [présentation approfondie des plans de Service d’application Azure](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) 

- **Éviter l’évolution vers le haut ou vers le bas.** Au lieu de cela, sélectionnez un niveau et une taille d’instance qui répondent à vos besoins de performances sous charge classique, puis [mise à l’échelle](../app-service-web/web-sites-scale.md) les instances pour gérer les changements de volume de trafic. Mise à l’échelle et vers le bas jusqu'à peut déclencher le redémarrage de l’application.  

- **Configuration de la banque en tant que paramètres de l’application.** Utiliser les paramètres de l’application pour stocker des paramètres de configuration en tant que paramètres de l’application. Définissez les paramètres dans les modèles de votre gestionnaire de ressources ou à l’aide de PowerShell, afin que vous pouvez les appliquer dans le cadre d’un déploiement automatisé ou des mises à jour, qui est la plus fiable. Pour plus d’informations, consultez [Configuration des applications web dans le Service d’application Azure](../app-service-web/web-sites-configure.md). 

- **Créer des plans de Service d’application distincts pour la production et de test.** N’utilisez pas emplacements sur votre déploiement de production pour le test.  Toutes les applications appartenant au même plan de Service d’application partagent les mêmes instances de machine virtuelle. Si vous placez les déploiements de test et de production dans le même plan, elle peut nuire au déploiement en production. Par exemple, les tests de charge peuvent se dégrader le site de production en direct. En plaçant des déploiements de test dans un autre plan, vous isolez les à partir de la version de production.  

- **Applications web distinctes à partir des API de web**. Si votre solution comporte un site web frontal et une API web, envisagez de les décomposer dans les applications de Service d’application distinctes. Cette conception facilite la décomposer la solution par charge de travail. Vous pouvez exécuter l’application web et l’API dans les plans de Service d’application distincts, afin qu’ils peuvent être mis à l’échelle indépendamment. Si vous n’avez pas besoin que niveau d’évolutivité à tout d’abord, vous pouvez déployer les applications dans le même plan et les déplacer dans des plans distincts par la suite, si nécessaire.

- **Évitez d’utiliser la fonctionnalité de Service de l’application de sauvegarde pour sauvegarder des bases de données SQL d’Azure.** À la place, utilisez [SQL de base de données des sauvegardes automatisées][sql-backup]. Sauvegarde du Service de l’application exporte la base de données dans un fichier de .bacpac SQL, qui coûte DTUs.  

- **Déployer vers un emplacement intermédiaire.** Créer un emplacement de déploiement pour la mise en attente. Déployer des mises à jour de l’application à l’emplacement intermédiaire et vérifiez le déploiement avant qu’il échange en production. Cela réduit le risque d’un échec de la mise à jour dans la production. Elle garantit également que toutes les instances sont échauffés avant permutés en production. De nombreuses applications ont une importante préparation et le temps de démarrage à froid. Pour plus d’informations, voir [configurer mise en environnements pour les applications web dans le Service d’application Azure](../app-service-web/web-sites-staged-publishing.md). 

-  **Créer un emplacement de déploiement pour le déploiement (connue) de dernière bonne.** Lorsque vous déployez une mise à jour en production, déplacez le déploiement en production précédente dans l’emplacement de la dernière bonne configuration connue. Cela rend plus facile à restaurer un déploiement défectueux. Si vous découvrez un problème plus tard, vous pouvez rapidement revenir à la version de la dernière bonne configuration connue. Pour plus d’informations, consultez [architecture de référence Azure : application web de base](guidance-web-apps-basic.md). 

- **Activer l’enregistrement des diagnostics**, y compris la journalisation de l’application et la journalisation d’un serveur web. La journalisation est importante pour la surveillance et de diagnostic. Reportez-vous à la section [Activer l’enregistrement des diagnostics pour les applications web dans le Service d’application Azure](../app-service-web/web-sites-enable-diagnostic-log.md)

- **Journal de stockage blob**. Cela rend plus facile collecter et analyser les données. 

- **Créez un compte de stockage séparé pour les journaux.** N’utilisez pas le même compte de stockage pour les journaux et les données d’application. Cela permet d’empêcher l’enregistrement provenant de la réduction des performances de l’application. 

- **Surveiller les performances.** Utiliser un service tel que [Relic nouvelle](http://newrelic.com/) ou [d’Idées d’Application](../application-insights/app-insights-overview.md) pour surveiller les performances et le comportement sous la charge d’analyse des performances.  Surveillance des performances vous offre une visibilité en temps réel de l’application. Il vous permet de diagnostiquer les problèmes et effectuer l’analyse des causes des défaillances. 


###  <a name="application-gateway"></a>Passerelle d’application 

- **Configurer au moins deux instances.** Déployer la passerelle d’Application avec au moins deux instances. Une seule instance est un point de défaillance unique. Utiliser deux ou plusieurs instances de redondance et d’évolutivité. Pour bénéficier de l' [entente SLA](https://azure.microsoft.com/support/legal/sla/application-gateway/v1_0/), vous devez configurer deux ou plusieurs instances de taille moyennes ou plus. 

### <a name="azure-search"></a>Recherche Azure

- **Configurer plus d’un réplica.** Utilisez au moins deux réplicas de lecture haute disponibilité, ou trois pour la haute disponibilité en lecture-écriture.

- **Configurer les indexeurs pour les déploiements de plusieurs régions.** Si vous avez un déploiement de plusieurs région, examinez vos options de continuité d’activité dans l’indexation.

    - Si la source de données est répliquée géo, pointez chaque indexeur de chaque service de recherche d’Azure régional sa duplication de source de données.  

    - Si la source de données n’est pas répliquée géo, point indexeurs multiples à la même source de données, afin que les services de recherche d’Azure dans plusieurs régions en permanence et de façon indépendante d’index à partir de la source de données. Pour plus d’informations, consultez [Considérations sur les performances et l’optimisation recherche d’Azure][search-optimization].

###  <a name="azure-storage"></a>Stockage Azure 

- **Données d’application, utilisez stockage geo redondant d’accès en lecture (RA-GRS).** Stockage de RA-GRS réplique les données à une zone secondaire et fournit un accès en lecture seule à partir de la zone secondaire. S’il existe une panne de stockage dans la région principale, l’application peut lire les données de la zone secondaire. Pour plus d’informations, consultez [réplication du stockage Azure](../storage/storage-redundancy.md).

- **Les disques de la VM, utilisez le stockage de prime** Pour plus d’informations, consultez [prime stockage : stockage hautes performances pour les charges de travail de Machine virtuelle Azure](../storage/storage-premium-storage.md).

- **Pour le stockage de la file d’attente, créez une file d’attente de sauvegarde dans une autre région.** Pour le stockage de la file d’attente, un réplica en lecture seule s’en trouve limité, car vous ne pouvez en file d’attente ou les éléments de file d’attente. Au lieu de cela, créez une file d’attente de sauvegarde dans un compte de stockage dans une autre région. S’il existe une panne de stockage, l’application peut utiliser la file d’attente de sauvegarde, jusqu'à ce que la zone primaire devient à nouveau disponible. De cette façon, l’application peut continuer à traiter les nouvelles demandes.  

###  <a name="documentdb"></a>DocumentDB 

- **Répliquer la base de données sur plusieurs régions.** Avec un compte plusieurs région, votre base de données DocumentDB a une écriture région et plusieurs zones en lecture. S’il existe une erreur dans la zone d’écriture, vous pouvez lire à partir d’un autre réplica. Le kit SDK Client gère cette valeur automatiquement. Vous pouvez également basculer vers la région de l’écriture dans une autre région. Pour plus d’informations, consultez [données de distribuer globalement avec DocumentDB](../documentdb/documentdb-distribute-data-globally.md).


###  <a name="sql-database"></a>Base de données SQL 

- **Utiliser la couche Standard ou Premium.** Ces niveaux fournissent un point de restauration plus du point-à-temps (35 jours). Pour plus d’informations, consultez les [performances et les options de base de données SQL](../sql-database/sql-database-service-tiers.md).

- **Activer l’audit de base de données de SQL.** L’audit permet de diagnostiquer les attaques ou les erreurs humaines. Pour plus d’informations, consultez [mise en route de l’audit de base de données SQL](../sql-database/sql-database-auditing-get-started.md). 

- **Utilisation Active géo-réplication** Geo-réplication Active permet de créer des lisible secondaire dans une région différente.  Si votre base de données principale tombe en panne, ou doit simplement être mis hors ligne, d’effectuer un basculement manuel de la base de données secondaire.  Jusqu'à ce que vous le basculement, la base de données secondaire reste en lecture seule.  Pour plus d’informations, consultez [SQL de base de données géo-réplication](../sql-database/sql-database-geo-replication-overview.md)Active. 

- **Ont d’utilisation**. Envisagez d’utiliser ont pour partitionner horizontalement de la base de données. Ont peut fournir des pannes. Pour plus d’informations, reportez-vous à la section [mise à l’échelle des base de données de SQL Azure](../sql-database/sql-database-elastic-scale-introduction.md). 

- **Restauration de point-à-temps permet de récupérer suite à une erreur humaine.**  Restauration de point-à-temps retourne votre base de données à un point antérieur dans le temps. Pour plus d’informations, consultez [restaurer une base de données Azure SQL à l’aide de sauvegardes de base de données automatisée][sql-restore].

- **Geo-restauration permet de restaurer à partir d’une interruption de service.** Geo-restauration restaure une base de données à partir d’une sauvegarde geo redondant.  Pour plus d’informations, consultez [restaurer une base de données Azure SQL à l’aide de sauvegardes de base de données automatisée][sql-restore].


###  <a name="sql-server-running-in-a-vm"></a>SQL Server (en cours d’exécution sur un ordinateur virtuel)

- **Répliquer la base de données.** Permet de répliquer la base de données SQL Server toujours sur les groupes de disponibilité. Assure une disponibilité élevée en cas de défaillance d’une seule instance de SQL Server. Pour plus d’informations, reportez-vous à la section [plus d’informations...](guidance-compute-n-tier-vm.md) 

- **Sauvegarder la base de données**. Si vous utilisez déjà [Azure Backup](https://azure.microsoft.com/documentation/services/backup/) pour sauvegarder vos ordinateurs virtuels, envisagez d’utiliser la [Sauvegarde Azure pour les charges de travail de SQL Server à l’aide de DPM](../backup/backup-azure-backup-sql.md). Avec cette approche, il existe un rôle d’administrateur de la sauvegarde de l’entreprise et une procédure de récupération unifiée pour les ordinateurs virtuels et de SQL Server. Dans le cas contraire, utilisez [SQL Server des sauvegardes gérées par Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx). 


###  <a name="traffic-manager"></a>Traffic Manager 

- **Effectuer un retour arrière manuel.** Après un basculement de Traffic Manager, effectuez une restauration manuelle, plutôt que d’effectuer automatiquement une reprise. Avant d’effectuer une reprise, vérifiez que tous les sous-systèmes d’application sont en bon état.  Dans le cas contraire, vous pouvez créer une situation où l’application retourne en arrière entre les centres de données. Pour plus d’informations, consultez [Exécution de machines virtuelles dans plusieurs régions](guidance-compute-multiple-datacenters.md).

- **Créer un point de terminaison de sonde de la santé**. Créer un point de terminaison personnalisé qui génère des rapports sur l’état de santé global de l’application. Cela permet au Gestionnaire de trafic de basculer en cas d’échec de n’importe quel chemin critique, non seulement la partie frontale. Le point de terminaison doit renvoyer un code d’erreur HTTP si toute dépendance critique est incorrect ou inaccessible. Ne pas signaler les erreurs pour les services non essentiels, toutefois. Dans le cas contraire, la sonde de santé peut déclencher le basculement lorsqu’il n’est pas nécessaire, création de faux positifs. Pour plus d’informations, consultez [contrôle de point de terminaison de Traffic Manager et le basculement](../traffic-manager/traffic-manager-monitoring.md).


###  <a name="virtual-machines"></a>Ordinateurs virtuels 

- **Évitez d’exécuter un travail de production sur une machine virtuelle unique.** Un seul déploiement de machine virtuelle n’est pas résistant à une maintenance planifiée ou non. Au lieu de cela, placez plusieurs ordinateurs virtuels dans un ensemble de disponibilité ou un ensemble d’échelle de machine virtuelle, avec un équilibreur de charge à l’avant. Pour obtenir le contrat SLA, au moins deux Machines virtuelles doivent être déployés dans le même jeu de disponibilité. Pour plus d’informations, consultez [Vue d’ensemble des jeux d’ordinateur virtuel échelle](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md). 

- **Spécifier la disponibilité définie lorsque vous configurez la machine virtuelle.** Actuellement, il n’existe aucun moyen d’ajouter un gestionnaire de ressources de VM pour une disponibilité définie après que la machine virtuelle est mis en service. Lorsque vous ajoutez un nouvel ordinateur virtuel pour une disponibilité existante définie, assurez-vous de créer une carte d’interface réseau de la machine virtuelle et ajouter la carte réseau au pool d’adresses de back-end de l’équilibreur de charge. Dans le cas contraire, l’équilibreur de charge ne sont pas router le trafic réseau à cette machine virtuelle. 

- **Placez chaque couche application dans un jeu distinct de disponibilité.** Dans une application multicouche, ne placez ordinateurs virtuels à partir de différents niveaux dans le même jeu de disponibilité. Ordinateurs virtuels dans un jeu de disponibilité sont placés entre des domaines d’erreur (FDs) et mettre à jour des domaines (UD). Cependant, pour profiter de la redondance de FDs et UDs, chaque ordinateur virtuel dans l’ensemble de la disponibilité doit être en mesure de gérer les demandes du client même. 

- **Choisissez la taille de mémoire virtuelle droite en fonction des besoins de performances.** Lors du déplacement d’une charge de travail existante vers Azure, démarrez avec la taille de la mémoire virtuelle qui correspond le plus à vos serveurs locaux. Mesurer les performances de votre charge de travail réelle en ce qui concerne le processeur, la mémoire et e/s de disque, puis ajuster la taille, si nécessaire. Cela permet de garantir que l’application se comporte comme prévu dans un environnement de cloud. Si vous avez besoin de plusieurs cartes réseau, sachez également de la limite de la carte d’interface réseau pour chaque taille. 

- **Utilisez le stockage de la prime pour les disques durs virtuels.** Le stockage de prime Azure fournit la prise en charge des disques hautes performances et à faible latence. Pour plus d’informations, consultez [prime stockage : stockage hautes performances pour les charges de travail de Machine virtuelle Azure](../storage/storage-premium-storage.md) choisir une taille de mémoire virtuelle qui prend en charge le stockage de la prime. 

- **Créer un compte de stockage distincte pour chaque machine virtuelle.** Placez les disques durs virtuels pour un ordinateur virtuel dans un compte de stockage distinct. Cela permet d’éviter de toucher les limites des Ops ES/s pour les comptes de stockage. Pour plus d’informations, consultez [cibles de performances et de l’évolutivité du stockage Azure](../storage/storage-scalability-targets.md). Toutefois, si vous déployez un grand nombre d’ordinateurs virtuels, gardez à l’esprit de la limite par abonnement pour les comptes de stockage. Voir les [limites de stockage](../azure-subscription-service-limits.md#storage-limits).

- **Créer un compte de stockage séparé pour les journaux de diagnostic**. Journaux de diagnostic d’écriture pour le même compte de stockage que les disques durs virtuels, afin d’éviter que l’enregistrement des diagnostics n’affectent pas les e/s pour les disques de l’ordinateur virtuel. Un compte standard de stockage redondants localement (LRS) est suffisant pour les journaux de diagnostic. 

- **Installer des applications sur un disque de données, n’est pas le disque du système d’exploitation.** Dans le cas contraire, vous risquez d’atteindre la limite de taille de disque. 

- **Sauvegarde d’Azure permet de sauvegarder des ordinateurs virtuels.** Les sauvegardes protègent contre la perte accidentelle de données. Pour plus d’informations, reportez-vous à la section [Protéger Azure ordinateurs virtuels avec un coffre-fort de services de récupération](../backup/backup-azure-vms-first-look-arm.md).

- **Activer les journaux de diagnostic**, y compris les mesures de santé de base, les journaux de l’infrastructure et [Démarrage diagnostic][boot-diagnostics]. Diagnostics de démarrage peut vous aider à diagnostiquer un échec de démarrage si votre ordinateur virtuel est dans un état non-amorçable. Pour plus d’informations, consultez [Vue d’ensemble d’Azure journaux de Diagnostic][diagnostics-logs].

- **Utilisez l’extension AzureLogCollector**. (VMs Windows uniquement). Cette extension regroupe les journaux de la plateforme Azure et les transfère vers le stockage Azure, sans l’opérateur journalisation à distance de la machine virtuelle. Pour plus d’informations, consultez [Extension de AzureLogCollector](../virtual-machines/virtual-machines-windows-log-collector-extension.md).


###  <a name="virtual-network"></a>Réseau virtuel 

- **Pour la liste d’autorisation ou de bloquer des adresses IP publiques, ajouter un NSG au sous-réseau.** Bloquer l’accès d’utilisateurs malveillants, ou autoriser l’accès uniquement à partir des utilisateurs disposant de privilèges d’accès à l’application.  

- **Créer une sonde d’état personnalisé.** Sondes de santé équilibreur de charge peut tester TCP ou HTTP. Si une machine virtuelle s’exécute un serveur HTTP, la sonde HTTP est un meilleur indicateur de l’état de santé à une sonde TCP. Pour une sonde HTTP, utilisez un point de terminaison personnalisé qui signale l’état de santé global de l’application, y compris toutes les dépendances critiques. Pour plus d’informations, consultez [vue d’ensemble de l’équilibrage de la charge Azure](../load-balancer/load-balancer-overview.md).

- **Ne pas bloquer la sonde de santé.** La sonde de santé d’équilibrage de la charge est envoyée à partir d’une adresse IP connue, 168.63.129.16. Ne pas bloquer le trafic vers ou à partir de cette adresse IP dans les stratégies de pare-feu ou les règles de groupe (NSG) de sécurité de réseau. Blocage de la sonde de santé provoquerait l’équilibreur de charge supprimer la machine virtuelle à partir de la rotation. 

- **Activer la journalisation de l’équilibreur de charge.** Les journaux indiquent le nombre de machines virtuelles en back-end ne reçoivent pas le trafic du réseau en raison de réponses de sonde a échoué. Pour plus d’informations, reportez-vous à la section [analytique de journal pour l’équilibrage de la charge Azure](../load-balancer/load-balancer-monitor-log.md).


<!-- links -->
[app-service-autoscale]: ../monitoring-and-diagnostics/insights-how-to-scale.md
[asynchronous-c-sharp]:https://msdn.microsoft.com/library/mt674882.aspx
[availability-sets]:../virtual-machines/virtual-machines-windows-manage-availability.md
[azure-backup]: https://azure.microsoft.com/documentation/services/backup/
[boot-diagnostics]: https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/
[circuit-breaker]: https://msdn.microsoft.com/library/dn589784.aspx
[cloud-service-autoscale]: ../cloud-services/cloud-services-how-to-scale.md
[diagnostics-logs]: ../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md
[fma]: guidance-resiliency-failure-mode-analysis.md
[guidance-resilient-deployment]: guidance-resiliency-overview.md#resilient-deployment
[load-balancer]: load-balancer/load-balancer-overview.md
[monitoring-and-diagnostics-guidance]: ../best-practices-monitoring.md
[resource-manager]: ../azure-resource-manager/resource-group-overview.md
[retry-pattern]: https://msdn.microsoft.com/library/dn589788.aspx
[retry-service-guidance]: ../best-practices-retry-service-specific.md
[search-optimization]: ../search/search-performance-optimization.md
[sql-backup]: ../sql-database/sql-database-automated-backups.md
[sql-restore]: ../sql-database/sql-database-recovery-using-backups.md
[traffic-manager]: ../traffic-manager/traffic-manager-overview.md
[traffic-manager-routing]: ../traffic-manager/traffic-manager-routing-methods.md
[vmss-autoscale]: ../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview.md
