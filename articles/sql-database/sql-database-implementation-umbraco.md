<properties
   pageTitle="Étude de cas Azure - Umbraco des bases de données SQL Azure | Microsoft Azure"
   description="Découvrez comment Umbraco utilise de la base de données SQL pour rapidement provisionner et services d’échelle pour des milliers de clients dans le nuage"
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/22/2016"
   ms.author="carlrab"/>

# <a name="umbraco-uses-azure-sql-database-to-quickly-provision-and-scale-services-for-thousands-of-tenants-in-the-cloud"></a>Umbraco utilise la base de données de SQL Azure pour rapidement des services de fourniture et de l’échelle pour des milliers de clients dans le nuage

![Umbraco Logo](./media/sql-database-implementation-umbraco/umbracologo.png)

Umbraco est un système de gestion de contenu d’open source (CMS) les plus courants qui peut exécuter quoi que ce soit à partir de petits sites de campagne ou une brochure pour des applications complexes pour des sociétés du classement Fortune 500 et les sites Web de global support. 

> « Nous disposons tout à fait une vaste communauté de développeurs qui utilisent le système, avec plus de 100 000 développeurs sur nos forums et plus de 350 000 sites actifs, exécutez Umbraco. »

> — Morten Christensen, responsable technique, Umbraco

> [AZURE.VIDEO azure-sql-database-case-study-umbraco]

Pour simplifier les déploiements clients, Umbraco ajouté Umbraco-as-a-Service (Administered Address) : une offre de logiciel-as-a-service (SaaS) qui élimine le besoin pour les déploiements sur site, offre intégrée de mise à l’échelle et supprime une surcharge de gestion en permettant aux développeurs de se concentrer sur la gestion de l’innovation plutôt que des solutions de produit. Umbraco est en mesure de fournir tous ces avantages en se basant sur le modèle (PaaS) plate-forme-as-a-service flexible offert par Microsoft Azure.

Administered Address permet aux clients de SaaS utiliser les fonctions Umbraco CMS précédemment hors de leur portée. Ces clients sont mis en service avec un environnement de CMS de travail qui inclut une base de données de production. Les clients peuvent ajouter jusqu'à deux bases de données supplémentaires pour les environnements de développement et mise en attente, en fonction de leurs besoins. Lorsqu’un environnement est demandé, un processus automatisé affecte ce client une base de données automatiquement. La nouvelle base de données est prêt en quelques secondes, car la base de données a déjà été pré-configurés par Umbraco à partir d’un pool élastique Azure des bases de données disponibles (voir Figure 1).


![Cycle de vie du provisionnement Umbraco](./media/sql-database-implementation-umbraco/figure1.png)

La figure 1. Mise en service du cycle de vie pour Umbraco en tant que Service (Administered Address)
 
##<a name="azure-elastic-pools-and-automation-simplify-deployments"></a>Simplifient les déploiements Azure pools élastiques et automatisation

Avec la base de données de SQL Azure et d’autres services Azure, Umbraco clients peuvent configurer eux-mêmes leurs environnements et Umbraco peut facilement surveiller et de gérer des bases de données dans le cadre d’un flux de travail intuitif :

1.  Disposition

    Umbraco conserve une capacité de 200 bases de données approvisionnées disponibles à partir de pools élastiques. Lorsqu’un nouveau client s’inscrit pour Administered Address, Umbraco fournit au client un nouvel environnement de CMS en temps quasi réel en leur affectant une base de données à partir du pool de disponibilité.

    Lorsqu’un pool de disponibilité atteint son seuil, création d’un nouveau pool élastique et nouvelles bases de données sont approvisionnées à affecter aux clients en fonction des besoins.

    Mise en œuvre est entièrement automatisée à l’aide de la gestion des bibliothèques de C# et des files d’attente du Bus des services Azure.

2.  Utiliser

    Clients utilisent un à trois environnements (pour la production, mise en attente, ou développement), chacun avec sa propre base de données. Les bases de données client sont dans des pools élastique de la base de données, qui permet de fournir efficace de mise à l’échelle sans avoir de surprovisionner Umbraco.

    ![Vue d’ensemble du projet Umbraco](./media/sql-database-implementation-umbraco/figure2.png)

    ![Détails du projet Umbraco](./media/sql-database-implementation-umbraco/figure3.png)

    La figure 2. Site Web du client Umbraco-as-a-Service (Administered Address), montrant des détails et vue d’ensemble du projet

    Une base de données SQL Azure utilise des unités de base de données de Transaction (DTUs) pour représenter la puissance relative requise pour les transactions de la base de données du monde réel. Pour les clients d’Administered Address, bases de données fonctionnent généralement à environ 10 DTUs, mais chacune a l’élasticité à l’échelle à la demande. Cela signifie qu’Administered Address peut garantir que les utilisateurs disposent toujours des ressources nécessaires, même pendant les heures de pointe. Par exemple, pendant un événement de sports dimanche soir récent, un Administered Address expérimenté base de données client optimale jusqu'à 100 DTUs pendant toute la durée du jeu. Les pools élastiques Azure ont permis Umbraco prendre en charge de cette demande élevée sans dégradation des performances.

3.  Moniteur

    Umbraco moniteurs de base de données de l’activité à l’aide de tableaux de bord au sein du portail Azure, ainsi que des alertes par courrier électronique personnalisé.

4.  Reprise après sinistre

    Azure propose deux options de reprise après sinistre (DR) : géo-réplication Active et géo-restauration. L’option de reprise après sinistre une société doit sélectionner dépend de ses [objectifs de continuité d’activité](sql-database-business-continuity.md).

    Geo-réplication Active fournit le niveau le plus rapide de réponse en cas d’indisponibilité. À l’aide de géo-réplication Active, vous pouvez créer jusqu'à quatre secondaires lisibles sur serveurs dans différentes régions, et vous pouvez lancer le basculement à une des copies secondaires en cas de panne.

    Umbraco ne nécessite pas de réplication géographique, mais elle ne tire pas parti de Azure géo-restauration afin de garantir les temps d’arrêt minimum en cas de panne. Geo-restauration s’appuie sur des sauvegardes de base de données dans le stockage Azure geo redondant. Qui permet aux utilisateurs de restaurer à partir d’une copie de sauvegarde en cas d’arrêt dans la région principale.

5.  Fourniture de déduplication

    Lorsqu’un environnement de projet est supprimé, les bases de données associées (développement, intermédiaire ou direct) sont supprimées pendant le nettoyage de file d’attente de Bus des services Azure. Ce processus automatisé restaure les bases de données inutilisées au pool d’élastique de disponibilité de base de données de Umbraco, ce qui les rend disponibles pour la fourniture ultérieure lors de l’utilisation maximale de la mise à jour.

##<a name="elastic-pools-allow-uaas-to-scale-with-ease"></a>Les pools élastiques permettent Administered Address d’évoluer en toute simplicité

En tirant parti des pools d’une base de données élastique Azure, Umbraco peut optimiser les performances pour ses clients sans avoir à la sur - ou la prestation incomplète. Umbraco dispose actuellement de près de 3 000 bases de données dans 19 pools élastique de la base de données, avec la possibilité d’évoluer en fonction des besoins pour prendre en charge leurs 325 000 clients ou les clients qui sont prêts à déployer un CMS dans le nuage.

En fait, selon Morten Christensen, responsable technique chez Umbraco, « Administered Address maintenant connaît la croissance d’environ 30 nouveaux clients par jour. Nos clients sont très heureux avec l’avantage de pouvoir provisionner de nouveaux projets en quelques secondes, publier instantanément les mises à jour à leurs sites actifs à partir d’un environnement de développement à l’aide de « déploiement d’un seul clic » et apporter des modifications plus rapidement si vous trouvez des erreurs. »

Si un client ne nécessite pas un environnement de deuxième ou troisième plus, elle peut simplement supprimer ces environnements. Qui libère des ressources qui peuvent être utilisés pour les autres clients dans le cadre du pool Umbraco élastique de disponibilité de base de données.

![Architecture de déploiement Umbraco](./media/sql-database-implementation-umbraco/figure4.png)

La figure 3. Architecture de déploiement Administered Address sur Microsoft Azure

##<a name="the-path-from-datacenter-to-cloud"></a>Le chemin d’accès à partir du centre de données vers le cloud

Lorsque les développeurs Umbraco effectuées initialement la décision de passer à un modèle SaaS, ils savaient qu’ils doivent économique et évolutive permet de créer le service.

> « Les pools élastique de la base de données sont parfaitement à nos offres SaaS, car nous pouvons composer capacité haut et le bas en fonction des besoins. Mise en service est facile, et notre installation, nous pourrons l’utilisation maximale. »

> — Morten Christensen, responsable technique, Umbraco

« Nous souhaitions notre temps sur la résolution des problèmes de nos clients, ne pas gestion de l’infrastructure. Nous souhaitions simplifier nos clients à tirer le meilleur parti, » affirme Niels Hartvig, fondateur de Umbraco. « Nous initialement envisagé d’hébergement gérons nous-mêmes les serveurs, mais la planification de capacité aurait dû un cauchemar. » Par coïncidence, Umbraco n’utilise pas les administrateurs de base de données, qui met en évidence une proposition de valeur de clé pour l’utilisation d’Administered Address.

Un objectif important pour les développeurs Umbraco était de fournir un moyen pour les clients Administered Address fournir des environnements rapidement et sans les limitations de capacité. Mais fournissant un service hébergé dédié dans les centres de données Umbraco serait ont beaucoup de capacité excédentaire pour gérer les pics de traitement. Qui serait ont favorisé Ajout d’infrastructure de calcul considérable qui aurait été régulièrement sous-utilisent.

En outre, l’équipe de développement Umbraco souhaitait une solution qui leur permettrait de réutiliser autant de leur code existant que possible. En tant que développeur de Umbraco États Mikkel Madsen, « nous avons satisfaits avec les outils de développement Microsoft que nous avons déjà familiers, tels que Microsoft SQL Server, base de données SQL de Microsoft Azure, ASP.net et Internet Information Services (IIS). Avant d’investir dans un IaaS ou un PaaS cloud solution, nous souhaitons vous assurer qu’il prend en charge nos plates-formes et les outils de Microsoft afin que nous n’aurions pas effectuer des modifications massives dans notre code base. »

Pour répondre à tous ses critères, Umbraco recherché à un partenaire de nuage avec les qualifications suivantes :

-   Suffisamment de capacité et de la fiabilité
-   Prise en charge des outils de développement Microsoft, afin que les ingénieurs Umbraco pas sera obligés de réinventer complètement de leur environnement de développement
-   Présence dans tous les marchés géographiques dans lequel Administered Address concurrence (entreprises ont besoin pour s’assurer qu’ils peuvent accéder à leurs données rapidement et que leurs données sont stockées dans un emplacement qui répond à leurs exigences réglementaires régionales)

##<a name="why-umbraco-chose-azure-for-uaas"></a>Pourquoi Umbraco a choisi d’Azure pour Administered Address

Selon Morten Christensen « après avoir envisagé tous nos options, nous avons sélectionné Azure car il satisfait tous nos critères, de la facilité de gestion et de l’évolutivité de la connaissance et de rapport coût-efficacité. Nous définissons les environnements sur Azure VM, et chaque environnement possède sa propre instance de base de données de SQL Azure, avec toutes les instances dans des pools d’élasticité de la base de données. En séparant les bases de données entre les environnements de développement, intermédiaire et direct, nous offrir à nos clients d’isolation de performances robustes mis en correspondance à l’échelle : une grande victoire. »

Morten continue, « avant, nous avons dû fournir des serveurs de bases de données web manuellement. Maintenant, nous n’avons pas à y penser. Tout est automatisé — à partir de la mise en service de nettoyage. »

Morten est également heureux avec les capacités de mise à l’échelle par Azure. « Les pools élastique de la base de données sont parfaitement à nos offres SaaS, car nous pouvons composer capacité haut et le bas en fonction des besoins. Mise en service est facile, et notre installation, nous pourrons l’utilisation maximale. » États Morten, « la simplicité des pools élastiques et l’assurance de DTUs basés sur la couche de service, nous offre la possibilité de configurer de nouveaux pools de ressources à la demande. Récemment, un de nos gros clients pointu à 100 DTUs dans son environnement d’exploitation. À l’aide d’Azure, nos pools élastiques fourni bases de données du client avec les ressources qui en temps réel sans avoir à prévoir les besoins de DTU. Tout simplement, nos clients à tirer le turn-around time qu’ils attendent, et nous pouvons répondre à nos contrats de niveau de service de performances. »

Mikkel Madsen l’additionne : « Nous avons adopté l’algorithme Azure puissant qui se connecte à un scénario courant de SaaS (intégration nouveaux clients en temps réel à l’échelle) pour notre modèle d’application (avant mise en service des bases de données, à la fois le développement et en direct) sur la technologie sous-jacente (à l’aide de files d’attente du Bus des services Azure conjointement avec une base de données SQL Azure). »

##<a name="with-azure-uaas-is-exceeding-customer-expectations"></a>Avec Azure, Administered Address dépasse les attentes du client

Depuis le choix d’Azure comme son partenaire de nuage, Umbraco a été en mesure de fournir les clients Administered Address avec optimisation des performances de gestion de contenu, sans l’investissement de ressources informatiques requise d’une solution autonome. Comme le dit Morten, « nous aimons le confort du développeur et l’évolutivité qui nous donne le Azure et nos clients sont sans attendre avec les fonctionnalités et la fiabilité. En général, il a été un grand pas en avant pour nous ! ».
 
## <a name="more-information"></a>Plus d’informations

- Pour plus d’informations sur les pools d’élastique Azure de la base de données, voir [pools d’élasticité de la base de données](sql-database-elastic-pool.md).

- Pour en savoir plus sur le Bus des services Azure, consultez le [Bus des services Azure](https://azure.microsoft.com/services/service-bus/).

- Pour plus d’informations sur les rôles Web et worker, consultez [rôles worker](../fundamentals-introduction-to-azure.md#compute). 

- Pour en savoir plus sur les réseaux virtuel, consultez [un réseau virtuel](https://azure.microsoft.com/documentation/services/virtual-network/).    

- Pour plus d’informations sur la sauvegarde et de restauration, reportez-vous à [la continuité d’activité](sql-database-business-continuity.md).  

- Pour en savoir plus sur l’analyse des ppols, consultez [analyse des pools](sql-database-elastic-pool-manage-portal.md). 

- Pour en savoir plus sur Umbraco en tant que Service, reportez-vous à la section [Umbraco](https://umbraco.com/cloud).

