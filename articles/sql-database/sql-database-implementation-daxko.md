<properties
   pageTitle="Base de données de SQL Azure Azure Etude de cas : Daxko/CSI | Microsoft Azure"
   description="Découvrez comment Daxko/CSI utilise SQL de base de données pour accélérer le cycle de développement et améliorer ses services clientèle et les performances"
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
   ms.date="09/08/2016"
   ms.author="carlrab"/>
   
# <a name="daxkocsi-used-azure-to-accelerate-its-development-cycle-and-to-enhance-its-customer-services-and-performance"></a>Daxko/CSI utilisé Azure pour accélérer son cycle de développement et améliorer ses services clientèle et les performances

![Logo de Daxko/CSI](./media/sql-database-implementation-daxko/csidaxkologo25.png)

Logiciel de Daxko/CSI confrontés à un défi : sa base de clients de centres de remise en forme et de loisirs accroissait rapidement, grâce à la réussite de sa solution complète de logiciels d’entreprise, mais face aux besoins d’infrastructure informatique pour ce client en pleine croissance base a test de la société du personnel informatique. La société a été plus limitée par augmentation surcharge d’opérations, en particulier pour la gestion de ses bases de données. Pire encore, cette surcharge d’opérations a été de coupe en ressources pour le développement de nouvelles initiatives, telles que les nouvelles fonctionnalités de mobilité pour les logiciels de la société.

Selon David Molina, directeur du développement de produit à Daxko/CSI, Azure fourni CSI logiciel avec le modèle plate-forme en tant que service (PaaS) qu’il est nécessaire de simplifier la gestion de la base de données, augmenter l’évolutivité et libérer des ressources pour vous concentrer sur les logiciels au lieu des opérations. « Une base de données SQL azure est une option intéressante pour nous. N’ayant ne pas à vous soucier de maintenir un SQL Server, un cluster de basculement et toutes autre l’infrastructure a besoin est idéale pour nous. »

Depuis la migration vers Azure, CSI logiciel a besoin de personnel de seulement deux à gérer des bases de données client plus de 600. La société utilise les pools élastiques de base de données de SQL Azure pour déplacer des bases de données client en fonction de la taille et devez.

Continue de Molina, « nos clients ressenti immédiatement la modification. Avant les pools élastiques, ils était parfois sujette à des délais d’attente et d’autres problèmes pendant les périodes de transmission en rafale. Avec Azure pools élastiques, ils peuvent en rafale en fonction des besoins et utiliser le logiciel sans problèmes. »

En plus de l’amélioration des performances pour les clients, Azure élastique de base de données de libérer des ressources CSI logiciel pour vous concentrer sur le développement de nouveaux services et fonctionnalités, plutôt que de gérer les opérations et la gestion des pools. Ces ressources informatiques aidé CSI logiciel amélioration de ses logiciels d’entreprise offrant, SpectrumNG, pour aider à engager les membres gym, améliorer l’efficacité du personnel et de donner le personnel et les membres de l’accès mobile pour les tâches interactives et des notifications en temps réel.

Azure a également aidé à CSI logiciel accélérer et améliorer le développement et le cycle d’assurance qualité (AQ) en activant les options d’automatisation. Avec implémentation Azure de la société, les gestionnaires de génération peuvent regrouper les composants en cliquant sur un bouton. Comme le décrit la Molina, « dans le cadre de ses parutions, QA est désormais en mesure de déployer dans un environnement de test dans Azure, qui simule avec précision notre pile de production. Nous pouvons déployer des builds immédiatement à notre environnement de développement pour valider les modifications. C’est une grande victoire pour nous, car nous n’avions pas de parité pour tester avant que. »

## <a name="offloading-to-the-cloud"></a>Transférant vers le nuage

Avant de passer au nuage, un logiciel de CSI avait correctement créé sa propre infrastructure mutualisées dans un centre de données local à Houston. Comme la société de développé, il face croissantes grandissantes à partir d’achats, de provisionnement et de maintenance de tous les matériels et logiciels nécessaires à la prise en charge de ses clients. INFORMATIQUE personnel pour gérer des opérations est devenu un autre goulet d’étranglement qui a conduit à un ralentissement de la mise en service de nouvelles ressources et de déploiement de nouveaux services aux clients.

Logiciel de CSI recherché dans les options de cloud pour éliminer cette surcharge, afin qu’il puisse se concentrer sur son code, au lieu de ses opérations. L’entreprise a découvert que la plupart des fournisseurs de nuage supérieur n’offrent que des solutions infrastructure-as-a-service (IaaS) continuent de nécessiter un personnel informatique suffisant pour gérer la pile IaaS. Au final, CSI de logiciels a signalé que la solution Azure PaaS a le mieux adaptés à ses besoins. Molina explique, « Azure obtient gêne, le matériel et le système logiciel afin que nous pouvons nous concentrer sur nos offres de logiciel, tout en réduisant ses frais de gestion informatiques. »

## <a name="making-the-transition-to-azure"></a>La transition vers Azure

Après avoir sélectionné Azure pour sa solution PaaS, CSI logiciel a commencé à la migration de son infrastructure de back-end et les bases de données dans le nuage. Avant d’Azure, les clients SpectrumNG nécessaire pour installer une application cliente qui a communiqué avec un service Windows Communication Foundation (WCF) sur le serveur principal. Selon Molina, « bien que certains clients hébergés tout dans leurs propres centres de données, nous avons conçu le produit pour être partagé. Nous avons tout un centre de données à Houston, hébergé à l’aide de SQL Server comme magasin de données.

« Notre produit offre également inclus un membre web portal côté (écrit en ASP.net), qui devait être étiquetés blanc pour correspondre à la présence du client sur le web et une API SOAP pour prendre en charge les pages en ligne et une intégration de tiers. »

La migration vers le nuage n’a pas pris de temps de l’architecture. En fonction de Molina, « La majorité de l’effort de traité modifiant la façon que nous lisons des informations sur les fichiers de configuration, une modification de la chaîne de connexion centralisée et l’automatisation de l’emballage, le chargement et le déploiement de nos versions ».

Pour développer l’automatisation de la génération, ingénieurs CSI utilisé PowerShell d’Azure et d’autres API pour créer des packages et les télécharger vers un environnement de test pour mise à jour chaque nuit.
La transition globale pour un déploiement d’Azure nuage a rapidement et facilement pour l’équipe informatique de logiciels CSI. Explique de Molina, « dans l’ensemble, nous avions un environnement bêta dans le nuage au sein de trois à quatre semaines, prise sur le projet de. C’était une victoire surprenant pour nous. »

Après avoir configuré et l’environnement de test, CSI logiciel a entamé le passage aux clients. Pour les clients utilisant déjà l’hébergement de logiciels du CSI, la transition a été presque transparente. Pour les clients de migration à partir d’un déploiement sur site, la migration vers le nuage a eu peu de temps, mais était toujours principalement sans difficulté pour les clients et les logiciels de CSI.

Pour les du nouveaux clients, CSI logiciel personnel informatique utiliser le processus suivant pour intégrée pour Azure :

1.  Les scripts PowerShell Azure sont utilisés pour faire tourner une nouvelle base de données pour le client. tous les clients commencent sur une couche de prime afin de garantir le débit suffisant initial pour la transition.
2.  Dans la mesure du possible, CSI logiciel utilise l’Assistant de Migration SQL Azure pour déplacer les données existantes à une instance de base de données de SQL Azure.
3.  Enfin, Microsoft SQL Server Integration Services (SSIS) sont utilisés pour harmoniser les différences dans les données ou d’effectuer tout nettoyage de données selon les besoins.

Aujourd'hui, environ 99 % des clients CSI sont hébergés dans Azure, sur quatre centres de données régionaux (centrale Nord, Sud, est et l’ouest). Par des centres de données dans la zone géographique de chaque client, les temps de latence est réduit au minimum.

## <a name="azure-elastic-database-pools-free-up-it-resources"></a>Pools d’une base de données élastique Azure libérer des ressources informatiques

Plusieurs fonctionnalités d’Azure ont aidé CSI logiciel MAJ soit axées à la fonctionnalité et développement axés des opérations et infrastructures. Peut-être l’avantage le plus important a été à partir de pools d’élastique de la base de données.

Logiciel de CSI fournit actuellement environ 550 bases de données pour les clients. Avant les pools élastiques, il était difficile à gérer que de nombreuses bases de données dans une structure à niveaux. Responsables des opérations a dû attribuer les niveaux de performances basés sur les besoins en rafale des clients, ce qui nécessitait d’importantes ressources informatiques surcharge. Avec des pools élastique de la base de données, gestionnaires peuvent affecter les locataires une prime ou un pool standard, selon le cas, puis déplacer des clients en fonction de la taille et devez. Clients ressenti les effets des pools élastique de la base de données immédiatement ou presque ; avant de pools élastiques, clients avaient des délais d’attente et d’autres problèmes pendant les périodes de l’utilisation de la transmission en rafale, mais avec les pools élastiques, les clients peuvent rencontrer des pics d’activité en fonction des besoins, et ils peuvent continuer à utiliser des SpectrumNG sans problèmes.

## <a name="azure-active-geo-replication-accelerates-reporting"></a>Azure géo-réplication Active accélère la création de rapports

Plusieurs clients CSI sont également profiter d’Azure géo-réplication Active. Avec Active géo-réplication, jusqu'à quatre bases de données secondaires lisibles peut être configurée dans les régions de centre de données identiques ou différents. Logiciel de CSI utilise de géo-réplication Active de deux manières : tout d’abord, les bases de données secondaires sont disponibles dans le cas d’une panne du centre de données ou l’impossibilité de se connecter à la base de données principale ; et, en second lieu, les bases de données secondaires sont lisibles et peuvent être utilisés pour décharger les charges de travail en lecture seule telles que les travaux de création de rapports. Certains clients CSI utilisent cet avantage pour accélérer la création de rapports de flux de travail.

## <a name="csi-software-application-logic-and-architecture"></a>Architecture et la logique de l’application logicielle de CSI

SpectrumNG utilise les rôles web. Dans la mesure où l’application est partagée, un service WCF est utilisé pour traiter la demande de connexion initiale à partir de clients. Comme Molina indique, « la demande identifie chaque client, ce qui nous permet ensuite de créer une chaîne de connexion à leurs bases de données à faire tout ce que nous devons faire. »

Pour la couche web, de son service, CSI logiciel bénéficie de mise à l’échelle automatique Azure, en fonction de la date et l’heure. Les ressources disponibles sont automatiquement augmentées pour s’adapter à une utilisation plus importante pendant les heures ouvrables, selon le fuseau horaire de chaque centre de données régional. Les ressources sont également définies à l’échelle vers le bas le week-end, lorsque les besoins des clients sont inférieurs.

     
![Architecture de Daxko/CSI](./media/sql-database-implementation-daxko/figure1.png)

La figure 1. Un rôle de collaborateur de services cloud dessine des données structurées à partir de la base de données de SQL Azure et les données semi-structurées à partir du stockage de la table. SpectrumNG interactifs que rôle web de services de données via un nuage.

## <a name="using-web-apps-and-a-web-plan-tier-for-mobile-apps"></a>À l’aide d’applications web et un niveau de plan web pour les applications mobiles

À l’aide de base de données SQL Azure libérer des ressources pour les logiciels CSI pour activer de nouvelles initiatives, y compris une plate-forme mobile complète basée sur une API personnalisée hébergée dans les applications web Azure. La plate-forme permet les membres gym et du personnel à utiliser des périphériques mobiles pour vérifier les planifications, de classes de livres et de recevoir des messages.

La plate-forme utilise l’architecture orientée services (SOA) pour un composant unique, comme un système de point de vente (PDV) ou à un système de ventes, déplacez-le vers un autre plan de web à la volée et ensuite faire tourner un service pour prendre en charge ce composant, tout en laissant tout le reste sur le plan de web d’origine. Cette possibilité offre une flexibilité considérable pour les logiciels CSI, et il permet de maintenir des coûts bas.

## <a name="azure-lets-csi-software-developers-focus-on-apps-and-services"></a>Azure permet aux développeurs de CSI logiciel se concentrer sur les applications et les services

Une base de données SQL Azure n’est pas simplement une aubaine pour les clients de SpectrumNG, qui bénéficient du service rapide et fiable, c’est également une grande victoire pour CSI du personnel informatique et les développeurs. En déchargeant les opérations vers Azure dans le nuage, CSI logiciel réduit ses frais généraux de l’infrastructure et les ressources, considérablement accéléré ses cycles de développement et ne sont plus des besoins à micromanage de bases de données pour optimiser les performances de ses locataires.

## <a name="more-information"></a>Plus d’informations

- Pour plus d’informations sur les pools d’élastique Azure de la base de données, voir [pools d’élasticité de la base de données](sql-database-elastic-pool.md).

- Pour plus d’informations sur les outils de base de données et la mise à l’échelle élastique, voir [Outils de base de données élastique et élastique de mise à l’échelle](sql-database-elastic-scale-get-started.md).

- Pour en savoir plus sur la migration d’une base de données SQL Server, consultez [l’Assistant de Migration SQL Azure](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md).

- Pour en savoir plus sur géo-réplication Active, consultez [Géo-réplication](sql-database-geo-replication-overview.md)Active.

- Pour plus d’informations sur les rôles Web et worker, consultez [rôles worker](../fundamentals-introduction-to-azure.md#compute). 

- Pour en savoir plus sur le Bus des services Azure, consultez le [Bus des services Azure](https://azure.microsoft.com/services/service-bus/).

- Pour en savoir plus sur l’échelle automatique, reportez-vous à la section [mise à l’échelle des services en nuage](../cloud-services/cloud-services-how-to-scale.md).
