<properties
   pageTitle="Haute disponibilité des applications Azure | Microsoft Azure"
   description="Vue d’ensemble technique et des informations détaillées sur la conception et la création d’applications pour une haute disponibilité sur Microsoft Azure."
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

#<a name="high-availability-for-applications-built-on-microsoft-azure"></a>Haute disponibilité des applications basées sur Microsoft Azure

Une application hautement disponible absorbe les fluctuations de la disponibilité, la charge et des défaillances temporaires dans le matériel et les services dépendants. L’application continue de fonctionner à un niveau de réponse systémiques et d’un utilisateur acceptable comme défini par les besoins de l’entreprise ou d’application les accords de niveau de service (SLA).

##<a name="azure-high-availability-features"></a>Fonctions de haute disponibilité Azure

Azure propose de nombreuses fonctionnalités de la plate-forme intégrée qui prennent en charge des applications hautement disponibles. Cette section décrit certaines des fonctionnalités essentielles. Pour une analyse plus complète de la plate-forme, consultez [les conseils techniques résilience Azure](./resiliency-technical-guidance.md).

###<a name="fabric-controller"></a>Contrôleur de tissu

Le contrôleur de tissu Azure dispositions et surveille l’état des instances Azure compute. Le contrôleur de tissu vérifie l’état du matériel et des logiciels des instances machine hôte et invité. Lorsqu’il détecte une défaillance, il applique les SLA en déplaçant automatiquement les instances de la machine virtuelle. Le concept de panne et la mise à niveau des domaines plus prend en charge le calcul SLA.

Lorsque plusieurs instances de rôle de Service de Cloud sont déployés, Azure déploie ces instances sur les domaines d’erreur différents. Une limite de domaine d’erreur est en fait un rack de matériel différent dans la même région. Domaines d’erreur de réduire la probabilité qu’une défaillance matérielle localisée va interrompre le service d’une application. Vous ne pouvez pas gérer le nombre de domaines d’erreur qui sont attribuées à votre travail ou les rôles web. Le contrôleur de fabric utilise des ressources dédiées qui sont séparés des applications hébergées sur Azure. Il a 100 pour cent, car il sert le noyau du système Azure. Il surveille et gère des instances de rôle sur les domaines de la panne.

Le diagramme suivant illustre les ressources partagées Azure que le contrôleur de tissu déploie et gère entre domaines d’erreur différents.

![Vue simplifiée de pannes du domaine](./media/resiliency-high-availability-azure-applications/fault-domain-isolation.png)

Domaines de mise à niveau sont similaires aux domaines d’erreur dans la fonction, mais ils prennent en charge les mises à niveau plutôt que des échecs. Un domaine de mise à niveau est une unité logique de séparation d’instance qui détermine les instances dans un service en particulier la mise à niveau à un point dans le temps. Par défaut, pour le déploiement de votre service hébergé, cinq domaines de mise à niveau sont définis. Toutefois, vous pouvez modifier cette valeur dans le fichier de définition de service. Par exemple, supposons que vous disposez de huit instances de votre rôle de web. Il y a deux instances dans les trois domaines de mise à niveau et deux instances dans un domaine de mise à niveau. Azure définit la séquence de mise à jour, mais il est basé sur le nombre de domaines de mise à niveau. Pour plus d’informations sur les domaines de mise à niveau, reportez-vous à la section [mise à jour d’un service en nuage](../cloud-services/cloud-services-update-azure-service.md).

###<a name="features-in-other-services"></a>Fonctionnalités dans d’autres services

Outre les fonctionnalités de plate-forme qui prend en charge le calcul de haute disponibilité, Azure incorpore des fonctions de haute disponibilité dans ses autres services. Par exemple, le stockage Azure gère trois réplicas de tous les blob, table et les données de file d’attente. Il permet également l’option de réplication géographique stocker les sauvegardes des objets BLOB et des tableaux dans une zone secondaire. Le réseau de diffusion de contenu Azure permet de BLOB à mettre en cache dans le monde entier pour la redondance et de l’évolutivité. Une base de données SQL Azure gère plusieurs réplicas ainsi.

En plus de [l’assistance technique de résilience](https://aka.ms/bctechguide) série d’articles, reportez-vous au livre de [Meilleures pratiques pour la conception de Services à grande échelle sur les Services en nuage Azure](https://azure.microsoft.com/blog/best-practices-for-designing-large-scale-services-on-windows-azure/) . Ces articles fournissent plus d’informations sur les fonctionnalités de disponibilité de plateforme Azure.

Bien que Azure fournit plusieurs fonctionnalités qui prennent en charge la haute disponibilité, il est important de comprendre leurs limites :

- Pour le calcul, Azure garantit que vos rôles sont disponibles et en cours d’exécution, mais il ne sait pas si votre application est en cours d’exécution ou surchargé.
- Pour la base de données de SQL Azure, les données sont répliquées synchrone au sein de la région. Vous pouvez choisir active géo-réplication, ce qui permet jusqu'à quatre copies supplémentaires de la base de données dans la même région (ou des régions différentes). Ces duplications de base de données ne sont pas des sauvegardes de point-à-temps. Bases de données SQL ne fournissent pas les fonctionnalités de sauvegarde de point-à-temps. Pour en savoir plus sur les fonctionnalités de base de données de SQL point-à-temps, lire le [Point de base de données SQL Azure dans temps de restauration](https://azure.microsoft.com/blog/azure-sql-database-point-in-time-restore/).
- Pour le stockage Azure, table et blob sont répliquées par défaut à une autre région. Toutefois, vous ne peut pas accéder les réplicas jusqu'à ce que Microsoft décide de basculer vers l’autre site. Un basculement de région uniquement dans le cas d’une interruption prolongée à l’échelle de la zone de service, et qu’il n’y a pas de SLA pour les temps de basculement de la zone géographique. Il est également important de noter que toute corruption de données se propage rapidement dans les réplicas.

Pour ces raisons, vous devez compléter les fonctionnalités de disponibilité de plate-forme avec disponibilité spécifiques à l’application. Les fonctionnalités spécifiques à l’application disponibilité incluent la fonctionnalité de capture instantanée de blob pour créer des sauvegardes de point-à-temps des données blob.

###<a name="availability-sets-for-azure-virtual-machines"></a>Disponibilité définit pour Azure Virtual Machines

La majeure partie de cet article se concentre sur les services en nuage, qui utilisent une plate-forme en tant que service (PaaS) modèle. Toutefois, il existe également des fonctionnalités de disponibilité spécifiques pour les ordinateurs virtuels Azure, qui utilise une infrastructure en tant que service (IaaS) modèle. Pour atteindre une haute disponibilité avec des Machines virtuelles, vous devez utiliser des jeux de disponibilité. Un jeu de disponibilité est une fonction similaire à l’erreur et la mise à niveau des domaines. Dans un jeu de disponibilité, Azure positionne les ordinateurs virtuels d’une manière qui empêche des défaillances matérielles localisées et les activités de maintenance de mettre hors service toutes les machines de ce groupe. Jeux de disponibilité sont nécessaires pour atteindre les SLA Azure pour la disponibilité des ordinateurs virtuels.

Le diagramme suivant fournit une représentation sous forme de deux jeux de disponibilité que web de groupe et les machines virtuelles de SQL Server, respectivement.

![Disponibilité définit pour Azure Virtual Machines](./media/resiliency-high-availability-azure-applications/availability-set-for-azure-virtual-machines.png)

>[AZURE.NOTE] Dans le diagramme précédent, SQL Server est installé et exécuté sur des machines virtuelles. Cela est différent de la discussion précédente de la base de données SQL Azure qui fournit une base de données sous la forme d’un service managé.

##<a name="application-strategies-for-high-availability"></a>Stratégies d’application pour une haute disponibilité

La plupart des stratégies d’application pour une haute disponibilité implique la redondance ou la suppression d’un disque durs dépendances entre les composants de l’application. Conception de l’application doit prendre en charge la tolérance de panne pendant les temps d’arrêt sporadique d’Azure ou des services tiers. Les sections suivantes décrivent des modèles d’application pour augmenter la disponibilité de vos services en nuage.

###<a name="asynchronous-communication-and-durable-queues"></a>Les files d’attente durables et de la communication asynchrone

Envisagez une communication asynchrone entre services couplés pour accroître la disponibilité des applications Azure. Dans ce modèle, écrire des messages dans les files d’attente de stockage ou de files d’attente de Bus des services Azure pour un traitement ultérieur. Lorsque vous écrivez le message dans la file d’attente, le contrôle retourne immédiatement à l’expéditeur du message. Un autre niveau de l’application gère le message traitement, généralement mis en œuvre sous la forme d’un rôle worker. Si le rôle de travail tombe en panne, les messages s’accumulent dans la file d’attente jusqu'à ce que le service de traitement est restauré. Tant que la file d’attente est disponible, il n’existe pas de dépendance directe entre l’expéditeur frontal et le processeur de messages. Ceci élimine la nécessité pour les appels de service synchrones qui peut être un goulot d’étranglement de débit dans des applications distribuées.

Une variante de ce utilise le stockage Azure (objets BLOB, les tableaux, files d’attente) ou les files d’attente de Bus de Service comme un emplacement de basculement pour les appels de base de données a échoué. Par exemple, un appel synchrone au sein d’une application à un autre service (par exemple, de base de données SQL Azure) échoue à plusieurs reprises. Vous ne pourrez pas sérialiser des données dans le stockage durable. À un stade ultérieur lorsque le service ou une base de données est en ligne, l’application peut soumettre à nouveau la demande à partir du stockage. La différence dans ce modèle est que l’emplacement intermédiaire n’est pas une partie du flux de travail d’application. Il est utilisé uniquement dans les scénarios de défaillance.

Dans les deux scénarios, communication asynchrone et le stockage intermédiaire empêchent un service back-end en panne interrompre l’application entière. Files d’attente servent un intermédiaire logique. Pour plus de conseils sur le choix du service de file d’attente approprié, consultez [Azure files d’attente et Bus des services Azure--comparées et contrairement](../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md).

###<a name="fault-detection-and-retry-logic"></a>Logique de détection et réessayer de panne

Un point important dans la conception d’une application hautement disponible est utilisation logique des nouvelles tentatives dans le code pour gérer correctement un service qui est temporairement hors service. Le [Bloc d’Application de gestion des pannes transitoires](https://msdn.microsoft.com/library/hh680934.aspx), développé par l’équipe Microsoft Patterns and Practices, aide les développeurs d’applications dans ce processus. Le mot « transitoires » signifie une condition temporaire qui ne dure qu’un temps relativement court. Dans le cadre de cet article, la gestion des défaillances temporaires sont partie du développement d’une application hautement disponible. Erreurs réseau intermittente des exemples de conditions temporaires et les connexions de base de données perdues.

Le bloc d’Application de gestion des pannes transitoires est un moyen simple vous permettant de gérer les défaillances dans votre code de façon appropriée. Vous pouvez l’utiliser pour améliorer la disponibilité de vos applications par l’ajout d’une logique de gestion des erreurs transitoire robuste. Dans la plupart des cas, la logique des nouvelles tentatives gère l’interruption brève et reconnecte l’expéditeur et le destinataire après un ou plusieurs échecs des tentatives. Une tentative réussie passe généralement inaperçue pour les utilisateurs de l’application.

Les développeurs disposent de trois options pour la gestion de leur logique de nouvelle tentative : intervalle fixe, incrémentielle et exponentielle. Incrémentielle attend plus avant chaque nouvelle tentative de manière augmentant linéaire (par exemple, 1, 2, 3 et 4 secondes). Intervalle fixe attend le même laps de temps entre chaque tentative (par exemple, 2 secondes). Pour une option plus aléatoire, l’exponentiel REDUCTION attend plus entre les tentatives. Toutefois, il utilise le comportement exponentiel (par exemple, 2, 4, 8, 16 secondes).

La stratégie de haut niveau au sein de votre code est :

1. Définir votre stratégie de reprise et de la stratégie.
1. Essayez l’opération peut entraîner une défaillance transitoire.
1. En cas de défaillance transitoire, appeler la stratégie de nouvelle tentative.
1. Si toutes les tentatives échouent, intercepter une exception finale.

Tester votre logique de nouvelle tentative dans les échecs simulés pour garantir que les tentatives sur des opérations successives n’entraînent pas dans un délai non anticipé. Pour ce faire, avant de décider de faire échouer la tâche globale.

###<a name="reference-data-pattern-for-high-availability"></a>Modèle de données de référence pour la haute disponibilité

Données de référence sont les données en lecture seule d’une application. Ces données fournissent le contexte de l’entreprise dans lequel l’application génère des données transactionnelles au cours d’une opération commerciale. Données transactionnelles sont une fonction de point-à-temps pour les données de référence. Par conséquent, son intégrité dépend de la capture instantanée des données de référence au moment de la transaction. Il s’agit d’une définition un peu vague, mais elle devrait suffire pour notre but ici.

Les données de référence dans le contexte d’une application sont nécessaires pour le fonctionnement de l’application. Les applications respectifs, créer et mettre à jour les données de référence ; souvent, les systèmes de gestion (MDM) de données de base exécutent cette fonction. Ces systèmes sont chargés pour le cycle de vie des données de référence. Catalogue de produits, principal employé, maître de pièces et maître de l’équipement sont des exemples de données de référence. Les données de référence peuvent également issus de l’extérieur de l’organisation, par exemple, codes postaux ou taux de taxes. Stratégies d’augmentation de la disponibilité des données de référence sont généralement moins difficiles que celles des données transactionnelles. Les données de référence possèdent l’avantage d’être principalement immuable.

Vous pouvez créer des rôles web et worker Azure qui utilisent des données de référence autonome lors de l’exécution en déployant les données de référence avec l’application. Si la taille du stockage local permet à un tel déploiement, il s’agit d’un état parfaitement. Les bases de données embarquées (SQL, NoSQL) ou des fichiers XML déployés sur un système de fichiers local vous aide avec l’autonomie des unités d’échelle Azure compute. Toutefois, vous devez avoir un mécanisme pour mettre à jour les données dans chaque rôle sans nécessiter de redéploiement. Pour ce faire, placez les mises à jour les données de référence pour un point de terminaison du stockage de cloud (par exemple, stockage des objets Blob Azure ou base de données SQL). Ajoutez du code pour chaque rôle qui télécharge les mises à jour de données dans les nœuds de calcul au démarrage du rôle. Vous pouvez également ajouter du code qui permet à un administrateur d’effectuer un transfert forcé dans les instances de rôle.

Pour augmenter la disponibilité, les rôles doivent également contenir un ensemble de données de référence en cas de stockage est en panne. Ainsi, les rôles de démarrer avec un ensemble de base de données de référence, jusqu'à ce que la ressource de stockage est disponible pour les mises à jour.

![Haute disponibilité des applications via les nœuds de calcul autonome](./media/resiliency-high-availability-azure-applications/application-high-availability-through-autonomous-compute-nodes.png)

Une considération de ce modèle est le déploiement et la vitesse de démarrage pour vos rôles. Si vous déployez ou téléchargeant de grandes quantités de données de référence au démarrage, cela peut augmenter la quantité de temps que nécessaire pour commencer à tourner nouveaux déploiements ou instances de rôles. Il peut s’agir d’un compromis acceptable pour l’autonomie de présentant les données de référence immédiatement disponibles sur chaque rôle, plutôt qu’en fonction des services de stockage externe.

###<a name="transactional-data-pattern-for-high-availability"></a>Modèle de données transactionnelles pour une haute disponibilité

Les données transactionnelles sont les données générés par l’application dans un contexte d’entreprise. Données transactionnelles sont une combinaison de l’ensemble des processus d’entreprise que l’application et les données de référence qui prend en charge ces processus. Exemples de données transactionnelles peuvent inclure des commandes, des avis d’expédition avancées, des factures et des opportunités de gestion (CRM) de relation client. Les données transactionnelles ainsi obtenues sont réinjectées aux systèmes externes pour l’enregistrement ou pour un traitement ultérieur.

Gardez à l’esprit que les données peuvent changer dans les systèmes qui sont responsables de ces données de référence. Pour cette raison, les données transactionnelles doivent enregistrer le contexte de données de référence de point-à-temps afin qu’il comporte des dépendances externes minimales pour sa cohérence sémantique. Par exemple, considérez la suppression d’un produit du catalogue quelques mois après qu’une commande est exécutée. La meilleure pratique consiste à incorporer les contexte de données de référence que possible dans la transaction. Cela préserve la sémantique associée à la transaction, même si les données de référence est modifiée une fois que la transaction est capturée.

Comme indiqué précédemment, les architectures qui utilisent un couplage lâche et communication asynchrone se prêtent à des niveaux plus élevés de disponibilité. Cela est vrai pour les données transactionnelles, mais l’implémentation est plus complexe. Notions de transactionnelles traditionnelles s’appuient généralement sur la base de données pour garantir la transaction. Lorsque vous introduisez des couches intermédiaires, le code de l’application doit gérer correctement les données au niveau des différentes couches, pour garantir la cohérence et la durabilité suffisante.

La séquence suivante décrit un flux de travail qui sépare la capture des données transactionnelles à partir de son traitement :

1. Nœud de calcul Web : présentes font référence aux données.
1. Stockage externe : enregistrer les données transactionnelles intermédiaires.
1. Nœud de calcul Web : finaliser la transaction de l’utilisateur final.
1. Nœud de calcul Web : envoyer les données transactionnelles, ainsi que le contexte de données de référence, pour stockage durable temporaire qui est garanti pour donner une réponse prévisible.
1. Nœud de calcul Web : signaler l’achèvement de l’utilisateur final de la transaction.
1. Nœud de calcul en arrière-plan : extraire les données transactionnelles, post-traitement, le cas échéant et l’envoyer à son emplacement final de stockage dans le système actuel.

Le diagramme suivant illustre une implémentation possible de cette conception dans un service cloud hébergé d’Azure.

![Disponibilité élevée via un couplage lâche](./media/resiliency-disaster-recovery-high-availability-azure-applications/application-high-availability-through-loose-coupling.png)

Les flèches en pointillés dans le diagramme précédent indiquent le traitement asynchrone. Le rôle de frontal web n’a pas connaissance de ce traitement asynchrone. Cela entraîne le stockage de la transaction à sa destination finale en ce qui concerne le système actuel. En raison de la latence introduit par ce modèle asynchrone, les données transactionnelles ne sont pas immédiatement disponibles pour la requête. Par conséquent, chaque unité de données transactionnelles doit être enregistré dans un cache ou une session de l’utilisateur pour répondre à l’interface utilisateur immédiate a besoin.

Le rôle web est autonome du reste de l’infrastructure. Son profil de disponibilité est une combinaison de rôle web et la file d’attente Azure et pas l’ensemble de l’infrastructure. En plus de la haute disponibilité, cette approche permet le rôle web mise à l’échelle horizontalement, indépendamment du stockage back-end. Ce modèle haute disponibilité peut avoir un impact sur le coût des opérations. Des composants supplémentaires comme les rôles worker et de files d’attente Azure peuvent affecter des coûts de l’utilisation mensuelle.

Notez que le diagramme précédent illustre une implémentation de cette approche découplée de données transactionnelles. Il existe de nombreuses autres implémentations possibles. La liste suivante fournit quelques alternatives :

 * Un rôle worker peut être placé entre le rôle web et de la file d’attente de stockage.
 * Une file d’attente de Bus de Service peut être utilisé au lieu d’une file d’attente de stockage Azure.
 * La destination finale peut être le stockage Azure ou un fournisseur de base de données différente.
 * Cache Azure permet au niveau de la couche web pour assurer la mise en cache immédiate après la transaction.

###<a name="scalability-patterns"></a>Motifs de l’évolutivité

Il est important de noter que l’évolutivité du service nuage affecte directement la disponibilité. Si l’augmentation de la charge provoque votre service ne répond pas, l’impression de l’utilisateur est que l’application est en panne. Suivez les méthodes conseillées pour l’évolutivité en fonction de votre charge de l’application prévue et les attentes futures. L’échelle plus élevé implique de nombreuses considérations, telles que l’utilisation d’un ou plusieurs comptes de stockage, le partage entre plusieurs bases de données et la mise en cache des stratégies. Pour une analyse détaillée de ces modèles, consultez [Méthodes conseillées pour la conception de Services à grande échelle sur les Services en nuage Azure](https://azure.microsoft.com/blog/best-practices-for-designing-large-scale-services-on-windows-azure/).

##<a name="next-steps"></a>Étapes suivantes

Cet article fait partie d’une série d’articles consacré à la [reprise après sinistre et une haute disponibilité des applications basées sur Microsoft Azure](./resiliency-disaster-recovery-high-availability-azure-applications.md). Le prochain article de cette série est la [reprise après sinistre pour les applications basées sur Microsoft Azure](./resiliency-disaster-recovery-azure-applications.md).
