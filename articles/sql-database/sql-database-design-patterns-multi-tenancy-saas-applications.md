<properties
   pageTitle="Concevoir des modèles pour les applications SaaS partagées et la base de données de SQL Azure | Microsoft Azure"
   description="Cet article décrit la configuration requise et les modèles d’architecture de données commun partagé de base de données ont besoin des applications s’exécutant dans un environnement de cloud à prendre en compte et les compromis différentes associées à ces modèles. Il explique également comment base de données de SQL Azure, avec ses pools élastiques et les outils élastiques, aider à répondre à ces exigences de manière sans compromis."
   keywords=""
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
   ms.workload="sqldb-design"
   ms.date="08/24/2016"
   ms.author="carlrab"/>

# <a name="design-patterns-for-multitenant-saas-applications-and-azure-sql-database"></a>Concevoir des modèles pour les applications SaaS partagées et la base de données de SQL Azure

Dans cet article, vous pouvez en savoir plus sur les exigences et les données communes des modèles d’architecture du logiciel partagé sous la forme d’une application de base de données de service (SaaS) qui s’exécutent dans un environnement de cloud. Il explique également les facteurs que vous devez prendre en compte et les compromis de modèles de conception différents. Pools élastiques et outils élastiques dans la base de données de SQL Azure peuvent vous aider à satisfaire vos besoins spécifiques sans compromettre d’autres objectifs.

Parfois, les développeurs faire des choix qui fonctionnent par rapport à leur intérêt à long terme lorsqu’ils conçoivent les modèles de location pour les niveaux de données d’applications mutualisées. Au départ, au moins, un développeur peut noter facilité de développement et de réduire les coûts cloud service fournisseur comme plus important que l’isolement des clients ou à l’évolutivité d’une application. Ce choix peut entraîner les problèmes de satisfaction client et une correction des cours coûteuse ultérieurement.

Une application partagée est une application hébergée dans un environnement de cloud et qui fournit le même ensemble de services à des centaines ou des milliers de clients qui ne partagent pas ou n’ont pas voir des uns et des autres données. Un exemple est une application SaaS qui fournit des services aux locataires dans un environnement hébergé sur le nuage.

## <a name="multitenant-applications"></a>Applications mutualisées

Dans les applications mutualisées, la charge de travail et les données peuvent être partitionnées facilement. Vous pouvez partitionner données et charge de travail, par exemple, le long des frontières du locataire, car la plupart des requêtes se produisent dans les limites d’un client. Cette propriété est inhérente dans les données et la charge de travail, et elle favorise les modèles d’application abordés dans cet article.

Les développeurs utilisent ce type d’application sur tout le spectre des applications sur le cloud, notamment :

- Applications de base de données de partenaires qui sont en cours de transition vers le nuage en tant qu’applications de SaaS
- Applications SaaS élaborées pour le nuage à partir du sol
- Direct des applications côté client
- Employé-faisant face à des applications d’entreprise

SaaS les applications conçues pour le nuage et les racines comme partenaire généralement des applications de base de données sont des applications mutualisées. Ces applications SaaS livrer une application spécialisée en tant que service à leurs locataires. Locataires peuvent accéder au service de l’application et ont la pleine propriété de données associées stockées dans le cadre de l’application. Mais pour tirer parti des avantages de SaaS, locataires doivent renoncer à contrôler leurs propres données. Ils faites confiance au fournisseur de service de SaaS pour protéger leurs données sûr et isolé à partir des données des autres locataires. MYOB, SnelStart et Salesforce.com sont des exemples de ce type d’application de SaaS partagé. Chacune de ces applications peut être partitionnée selon les limites de clients et de prise en charge des modèles abordés dans cet article de conception de l’application.

Les applications qui fournissent un service directement aux clients ou aux employés d’une organisation (souvent appelés utilisateurs, plutôt que de locataires) sont une autre catégorie sur le spectre d’application partagé. Les clients s’abonner au service et ne possèdent pas les données que le fournisseur de service collecte et stocke. Fournisseurs de services ont des exigences moins strictes afin de protéger les données de leurs clients isolées des autres au-delà des réglementations sur la confidentialité imposées par le gouvernement. Fournisseurs de contenu de support tels que Netflix, Spotify et Xbox LIVE sont des exemples de ce type d’applications mutualisées du côté client. D’autres exemples d’applications facilement partitionnables sont orientés vers le client, les applications à l’échelle d’Internet ou applications Internet des objets (IoT) dans qui chaque client ou périphérique peut servent de partition. Limites de partition peuvent séparer les utilisateurs et périphériques.

Partition d’applications pas toutes facilement le long d’une propriété unique, tels que clients, client, utilisateur ou périphérique. Une application (ERP), de planification des ressources d’entreprise complexe a, par exemple, des produits, des commandes et des clients. Elle a généralement un schéma complexe avec des milliers de tables fortement interconnectés.

Aucune stratégie de partition unique peut s’appliquent à toutes les tables et fonctionnent sur la charge de travail d’une application. Cet article se concentre sur les applications mutualisées ayant des charges de travail et données partitionnables facilement.

## <a name="multitenant-application-design-trade-offs"></a>Compromis de conception application partagé

Le modèle de design, un développeur d’applications mutualisées choisit généralement est fondée sur un examen des facteurs suivants :

-   **Isolement des clients**. Le développeur doit garantir ne qu’aucune locataire indésirables d’accès pour les données des autres locataires. L’exigence d’isolation s’étend aux autres propriétés, comme offrant une protection aux voisins bruyants, qui est en mesure de restaurer les données d’un client et l’implémentation des personnalisations spécifiques au client.
-   **Coût des ressources de cloud**. Une application SaaS doit être compétitif. Un développeur d’applications mutualisées peut choisir de les optimiser pour la réduction des coûts de l’utilisation des ressources de cloud, tels que le stockage et de calcul des coûts.
-   **Facilité de DevOps**. Un développeur d’applications mutualisées doit intégrer la protection d’isolation, maintenir et surveiller la santé de leurs application et de schéma de base de données et résoudre les problèmes des clients. La complexité dans le développement d’applications et l’opération se traduit directement par une augmentation des coûts et les défis posés par la satisfaction des clients.
-   **L’évolutivité**. La possibilité d’ajouter plusieurs locataires et la capacité de locataires qui en ont besoin est impérative pour un bon fonctionnement de SaaS.

Chacun de ces facteurs a compromis par rapport à l’autre. Le nuage de coût le plus bas offrant ne peuvent ne pas offrir l’expérience de développement plus pratique. Il est important pour un développeur d’effectuer des choix éclairés sur ces options et leurs compromis pendant le processus de conception d’application.

Un modèle de développement les plus courants est pour compresser les locataires multiples dans une ou plusieurs bases de données. Les avantages de cette approche sont un coût inférieur, car vous payez quelques bases de données et de la relative simplicité de l’utilisation d’un nombre limité de bases de données. Mais au fil du temps, un développeur d’applications mutualisées SaaS bénéficiera que ce choix a des inconvénients importants dans l’isolation des clients et de l’évolutivité. Si l’isolement de locataire devient important, effort supplémentaire est nécessaire pour protéger les données de clients dans le stockage partagé à partir de tout accès non autorisé ou voisins bruyants. Cet effort supplémentaire peut augmenter considérablement les efforts de développement et des coûts de maintenance d’isolation. De la même façon, si il est nécessaire d’ajouter les locataires, ce modèle de design requiert généralement expertise de redistribuer les données partagées entre les bases de données à l’échelle correctement de la couche de données d’une application.  

Isolation du locataire souvent constitue un impératif fondamental dans les applications mutualisées SaaS adapté aux entreprises et organisations. Un développeur peut être tenté par des avantages escomptés de simplicité et de coût sur l’isolation des clients et de l’évolutivité. Ce compromis peut s’avérer complexe et coûteux que le service s’agrandit et aux exigences d’isolation de clients deviennent plus importantes et gérés au niveau de la couche application. Toutefois, dans les applications mutualisées qui fournissent un service direct, pour les consommateurs aux clients, isolation de clients peut être une priorité inférieure à optimisation de coût de ressource de nuage.

## <a name="multitenant-data-models"></a>Modèles de données partagé

Pratiques de conception commune pour placer des données clients suivent trois modèles distincts, illustrés à la Figure 1.


  ![Modèles de données d’application partagé](./media/sql-database-design-patterns-multi-tenancy-saas-applications/sql-database-multi-tenant-data-models.png)
 Figure 1 : les pratiques de conception commune pour les modèles de données partagé

-   **Base de données-par-locataire**. Chaque client a sa propre base de données. Toutes les données de clients spécifiques est limité à la base de données du locataire et isolé des autres locataires et de leurs données.
-   **Shared sharded de base de données**. Plusieurs locataires partagent parmi plusieurs bases de données. Un ensemble distinct de locataires est affecté à chaque base de données à l’aide d’une stratégie de partitionnement telles que le hachage, la plage ou la liste de partitionnement. Cette stratégie de distribution des données souvent est appelée ont.
-   **Base de données Shared - unique**. Une base de données unique et parfois, contient des données pour tous les locataires, qui sont désambiguïsés dans une colonne d’ID de client.

> [AZURE.NOTE] Un développeur d’application peut choisir de placer les locataires différents dans les schémas de base de données différente et puis utiliser le nom de schéma pour lever l’ambiguïté les locataires différents. Nous ne recommandons pas cette approche car elle requiert généralement l’utilisation de SQL dynamique, et il ne peut pas être efficace dans la mise en cache du plan. Dans le reste de cet article, nous concentrer sur l’approche de la table partagée pour cette catégorie d’application partagée.

## <a name="popular-multitenant-data-models"></a>Modèles de données partagé courants

Il est important d’évaluer les différents types de modèles de données partagé en ce qui concerne l’application design compromis que nous avons déjà identifié. Ces facteurs permettent de caractériser les trois modèles de données partagé plus courants décrits précédemment et leur utilisation de base de données, comme illustré à la Figure 2.

-   **L’isolation**. Le degré d’isolation entre les utilisateurs peut être une mesure du degré d’isolation de clients permet d’obtenir un modèle de données.
-   **Coût des ressources de cloud**. La quantité de ressources entre les locataires peut optimiser le coût des ressources de cloud. Une ressource peut être définie comme le coût de calcul et de stockage.
-   **Coût de DevOps**. La facilité de développement d’applications, de déploiement et de gestion réduit le coût de fonctionnement global SaaS.  

Dans la Figure 2, l’axe des Y indique le niveau d’isolation des clients. L’axe des X indique le niveau de partage des ressources. Le gris, flèche diagonale du milieu indique la direction des coûts DevOps, tendant à augmenter ou diminuer.

![Les modèles de conception courants applications mutualisées](./media/sql-database-design-patterns-multi-tenancy-saas-applications/sql-database-popular-application-patterns.png) Figure 2 : les modèles de données partagé courants

Le quadrant inférieur droit à la Figure 2 montre un modèle d’application qui utilise un potentiellement volumineux, base de données unique et la table partagée (ou schéma distinct) approche. C’est bon pour le partage, car tous les locataires utilisent les mêmes ressources de base de données (processeur, mémoire, entrée/sortie) dans une seule base de données des ressources. Mais l’isolation des clients est limitée. Vous devrez peut-être prendre des mesures supplémentaires pour protéger les locataires contre eux au niveau de la couche application. Ces étapes supplémentaires peuvent augmenter considérablement le coût DevOps de développement et de gestion de l’application. L’évolutivité est limitée par l’échelle du matériel qui héberge la base de données.

Le quadrant inférieur gauche dans la Figure 2 illustre les différents utilisateurs sharded sur plusieurs bases de données (en général, un matériel différent mise à l’échelle unités). Chaque base de données héberge un sous-ensemble de clients, qui résout le problème de l’évolutivité d’autres modèles. Si plus de capacité est obligatoire pour les locataires de plus, vous pouvez facilement la placer les locataires sur nouvelles bases de données sont affectés à des unités d’échelle de nouveau matériel. Toutefois, le montant de partage des ressources est réduit. Uniquement les locataires placés sur la même unité d’échelle partager les ressources. Cette approche offre peu d’amélioration pour client d’isolation dans la mesure où de nombreux locataires sont toujours colocalisés sans automatiquement protégée contre les actions des autres. La complexité des applications reste élevée.

Le quadrant supérieur gauche dans la Figure 2 est la troisième approche. Il place les données de chaque client dans sa propre base de données. Cette approche a des propriétés d’une bonne isolation locataire mais limite le partage des ressources lors de chaque base de données possède ses propres ressources dédiées. Cette approche est utile si tous les locataires ont des charges de travail prévisibles. Si les charges de travail de clients deviennent moins prévisibles, le fournisseur ne peut pas optimiser le partage des ressources. L’imprévisibilité est courante pour les applications SaaS. Le fournisseur doit sur-approvisionner pour répondre aux demandes ou ressources inférieurs. Soit qui entraîne des coûts plus élevés ou de faible satisfaction des clients. Un degré plus élevé de ressource partage entre les locataires devient souhaitable pour que la solution plus économique. L’augmentation du nombre de bases de données augmente également le coût DevOps pour le déploiement et la maintenance de l’application. Malgré ces inquiétudes, cette méthode fournit l’isolation meilleure et la plus facile pour les locataires.

Ces facteurs influencent également un client choisit le modèle de conception :

-   **Propriété de données de clients**. Une application dans laquelle locataires de conservent la propriété de leurs propres données favorise le motif d’une seule base de données par les clients.
-   **Échelle**. Une application qui cible des centaines de milliers ou des millions de clients privilégie les approches comme ont le partage de la base de données. Toujours aux exigences d’isolation peuvent poser des problèmes.
-   **Valeur et le modèle de l’entreprise**. Si une application par-locataire produit si petite (moins d’un dollar), les exigences d’isolement qui deviennent moins cruciales et une base de données partagée est logique. Si le chiffre d’affaires par-clients est quelques dollars ou plus, un modèle de base de données-par-locataire n’est plus possible. Cela peut vous aider à réduire les coûts de développement.

Compte tenu des conception des compromis illustrés à la Figure 2, un modèle partagé idéal doit incorporer des propriétés d’isolation des bons clients à partager entre les locataires optimale des ressources. Ce modèle s’intègre dans la catégorie décrite dans le quadrant supérieur droit de la Figure 2.

## <a name="multitenancy-support-in-azure-sql-database"></a>Prise en charge de multitenancy dans la base de données de SQL Azure

Une base de données SQL Azure prend en charge tous les modèles d’application partagé indiquées dans la Figure 2. Avec les pools élastiques, il prend également en charge un modèle d’application qui combine le partage des ressources et les avantages de l’isolation de la base de données par client approchent (voir le quadrant supérieur droit dans la Figure 3). Outils d’élastique de la base de données et des fonctions de base de données de SQL permettent de réduire le coût pour développer et faire fonctionner une application qui possède de nombreuses bases de données (indiqués dans la zone ombrée dans la Figure 3). Ces outils peuvent vous aider à créer et gérer des applications qui utilisent les modèles de bases de données multiples.

![Modèles de base de données de SQL Azure](./media/sql-database-design-patterns-multi-tenancy-saas-applications/sql-database-patterns-sqldb.png) Figure 3 : modèles d’application partagé dans la base de données de SQL Azure

## <a name="database-per-tenant-model-with-elastic-pools-and-tools"></a>Modèle de base de données-par-locataire des pools élastiques et des outils

Pools élastiques dans la base de données de SQL combinent isolation des clients avec le partage de ressources entre bases de données clients pour mieux prendre en charge l’approche de la base de données-par-locataire. Base de données de SQL est une solution de couche de données pour les fournisseurs de SaaS qui créent des applications mutualisées. La charge de travail de partage de ressources parmi les locataires déplace à partir de la couche d’application à la couche de service de base de données. La complexité de gestion et d’interrogation à l’échelle entre les bases de données est simplifiée avec les travaux élastiques, requête élastique, transactions élastiques et la bibliothèque cliente élastique de la base de données.

| Exigences de l’application | Fonctionnalités de base de données SQL |
| ------------------------ | ------------------------- |
| Isolation des clients et le partage des ressources | [Pools élastiques](sql-database-elastic-pool.md): allouer un pool de ressources de base de données de SQL et de partager les ressources entre les différentes bases de données. En outre, des bases de données peuvent dessiner autant de ressources à partir du pool en fonction des besoins pour prendre en charge les pics de la demande de capacité en raison de modifications dans les charges de travail de clients. Le pool élastique lui-même peut évoluer vers le haut ou vers le bas en fonction des besoins. Pools élastiques fournissent également la facilité de gestion et de surveillance et de dépannage au niveau du pool. |
| Facilité de DevOps entre bases de données | [Pools élastiques](sql-database-elastic-pool.md): comme indiqué précédemment.|
||[Requête élastique](sql-database-elastic-query-horizontal-partitioning.md): requête entre les bases de données de création de rapports ou le cross-locataire analyse.|
||[Travaux élastiques](sql-database-elastic-jobs-overview.md): Package et déployer fiable des opérations de maintenance de base de données ou les modifications apportées au schéma de base de données vers plusieurs bases de données.|
||[Transactions élastiques](sql-database-elastic-transactions-overview.md): processus change à plusieurs bases de données de façon atomique et isolée. Transactions élastiques sont nécessaires lorsque les applications doivent « tout ou rien » des garanties sur plusieurs opérations de base de données. |
||[Bibliothèque client de base de données élastique](sql-database-elastic-database-client-library.md): gérer les distributions de données et de mapper les locataires aux bases de données. |

## <a name="shared-models"></a>Modèles partagés

Comme indiqué précédemment, pour la plupart des fournisseurs de SaaS, une approche de modèle partagé peut poser des problèmes avec les clients d’isolation problèmes et complexités avec le développement d’applications et de maintenance. Toutefois, pour les applications mutualisées qui fournissent un service directement aux consommateurs, aux exigences d’isolation de clients peut-être pas une priorité aussi importante qu’en réduisant les coûts. Ils peuvent être en mesure de compresser les locataires dans une ou plusieurs bases de données à une haute densité pour réduire les coûts. Les modèles de base de données partagée à l’aide d’une base de données unique ou de plusieurs bases de données sharded peuvent entraîner une efficacité accrue dans le coût d’une ressource globale et de partage. Une base de données SQL Azure offre certaines fonctionnalités qui aident les clients à créer d’isolement pour améliorer la sécurité et de gestion à l’échelle de la couche de données.

| Exigences de l’application | Fonctionnalités de base de données SQL |
| ------------------------ | ------------------------- |
| Fonctionnalités de sécurité d’isolation | [Sécurité de niveau ligne](https://msdn.microsoft.com/library/dn765131.aspx) |
|| [Schéma de base de données](https://msdn.microsoft.com/library/dd207005.aspx) |
| Facilité de DevOps entre bases de données | [Requête élastique](sql-database-elastic-query-horizontal-partitioning.md) |
|| [Travaux élastiques](sql-database-elastic-jobs-overview.md) |
|| [Transactions élastiques](sql-database-elastic-transactions-overview.md) |
|| [Bibliothèque client de base de données élastique](sql-database-elastic-database-client-library.md) |
|| [Fusion et fractionnement de l’élasticité de la base de données](sql-database-elastic-scale-overview-split-and-merge.md) |

## <a name="summary"></a>Résumé

Clients d’isolement requis sont importants pour la plupart des applications mutualisées SaaS. La meilleure option pour assurer l’isolation utilise largement vers l’approche de la base de données-par-locataire. Les deux autres approches requièrent des investissements dans les couches d’application complexe nécessitant un personnel compétent pour assurer l’isolation, ce qui augmente considérablement les coûts et les risques. Si aux exigences d’isolation ne sont pas prises en compte pour très tôt dans le développement de service, il peut être encore plus coûteux dans les deux premiers modèles de rattrapage les. Les principaux inconvénients associés au modèle de base de données-par-clients sont liés aux coûts des ressources de cloud accrue en raison de la réduction de partage et la maintenance et la gestion de bases de données. Les développeurs d’applications SaaS éprouvent souvent des difficultés lorsqu’ils effectuent ces compromis.

Bien que des barrières majeures avec la plupart des fournisseurs de services de base de données de nuage compromis, base de données de SQL Azure permet d’éliminer les obstacles avec son pool élastique et ses fonctionnalités d’élastique de la base de données. Les développeurs de SaaS peuvent combiner les caractéristiques d’isolation d’un modèle de base de données-par-clients et optimiser le partage des ressources et des améliorations de la gestion de nombreuses bases de données à l’aide de pools élastiques et outils associés.

Les fournisseurs d’applications mutualisées qui n’avez pas besoin d’isolation des clients et qui peut compresser les locataires dans une base de données à une densité élevée peuvent trouver que les données partagées modélise des résultats en matière d’efficacité supplémentaire dans le partage des ressources et réduisent le coût global. Outils d’élastique de la base de données de la base de données SQL Azure et les bibliothèques ont des fonctionnalités de sécurité aident les fournisseurs de SaaS créer et gérer des applications mutualisées.

## <a name="next-steps"></a>Étapes suivantes

[Mise en route avec les outils de base de données élastique](sql-database-elastic-scale-get-started.md) avec un exemple d’application illustrant la bibliothèque cliente.

Créer un [tableau de bord personnalisé de pool élastique pour SaaS](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools-custom-dashboard) avec un exemple d’application qui utilise des pools élastiques pour une solution économique et évolutive de la base de données.

Utilisez les outils de base de données de SQL Azure pour [migrer des bases de données à l’échelle](sql-database-elastic-convert-to-use-elastic-tools.md).

Afficher notre didacticiel sur la [Création d’un pool élastique](sql-database-elastic-pool-create-portal.md).  

Découvrez comment [surveiller et de gérer un pool élastique](sql-database-elastic-pool-manage-portal.md).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Quel est un pool élastique Azure ?](sql-database-elastic-pool.md)
- [Mise à l’échelle avec la base de données de SQL Azure](sql-database-elastic-scale-introduction.md)
- [Applications mutualisées avec les outils de base de données élastique et la sécurité de niveau ligne](sql-database-elastic-tools-multi-tenant-row-level-security.md)
- [Authentification dans les applications mutualisées à l’aide d’Azure Active Directory et connecter des OpenID](../guidance/guidance-multitenant-identity-authenticate.md)
- [Application des enquêtes Tailspin](../guidance/guidance-multitenant-identity-tailspin.md)
- [Démarrage rapide de la solution](sql-database-solution-quick-starts.md)

## <a name="questions-and-feature-requests"></a>Questions et demandes de fonctionnalités

Pour toute question, nous trouver sur le [forum de la base de données SQL](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted). Ajouter une demande de fonctionnalité dans le [forum de commentaires de la base de données SQL](https://feedback.azure.com/forums/217321-sql-database/).
