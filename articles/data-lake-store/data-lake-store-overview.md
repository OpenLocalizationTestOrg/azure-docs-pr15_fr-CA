<properties
   pageTitle="Vue d’ensemble Azure lac de magasin de données | Microsoft Azure"
   description="Comprendre ce qu’est le lac Azure Data Store et la valeur qu’il fournit sur les autres banques de données"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/28/2016"
   ms.author="nitinme"/>

# <a name="overview-of-azure-data-lake-store"></a>Vue d’ensemble Azure lac de magasin de données

Magasin de LAC de données Azure est un référentiel d’une évolutivité à l’échelle de l’entreprise pour les charges de travail données analytiques. Lac de données Azure vous permet capturer des données de n’importe quelle vitesse ingestion, type et taille dans un seul emplacement pour analytique exploratoires et opérationnels.

> [AZURE.TIP] Commencer à Explorer le service magasin de LAC de données Azure, utilisez [magasin de données lac cursus](https://azure.microsoft.com/documentation/learning-paths/data-lake-store-self-guided-training/) .

Le lac Azure Data Store est accessible à partir d’Hadoop (disponible avec les clusters HDInsight) à l’aide de l’API reste compatible avec WebHDFS. Il est spécialement conçu pour activer analytique sur les données stockées et réglé pour les performances pour les scénarios de données analytique. Prêts à l’emploi, elle inclut toutes les fonctionnalités de niveau entreprise : sécurité, la facilité de gestion, d’évolutivité, fiabilité et disponibilité, essentielles pour les utilisations d’entreprise réel.


![Lac de données Azure](./media/data-lake-store-overview/data-lake-store-concept.png)

Voici quelques-unes des fonctionnalités clées du lac de données Azure.

### <a name="built-for-hadoop"></a>Conçu pour Hadoop

La banque du lac de données Azure est un système de fichiers Apache Hadoop compatible avec le système de fichier distribué (très) Hadoop et fonctionne avec l’écosystème Hadoop.  Votre HDInsight les applications ou les services qui utilisent l’API WebHDFS existantes peuvent intégrer facilement avec la banque de données lac. Magasin de données lac expose également une interface compatible WebHDFS reste pour les applications

Les données stockées dans le magasin de données lac peuvent être analysées à l’aide d’Hadoop des infrastructures analytiques telles que MapReduce ou de la ruche. Les clusters Microsoft Azure HDInsight peuvent être mis en service et configurés pour accéder directement aux données stockées dans le magasin de données lac.

### <a name="unlimited-storage-petabyte-files"></a>Stockage illimité, fichiers de pétaoctets

Magasin de LAC de données Azure offre un stockage illimité et est adapté au stockage de diverses données d’analytique. Il n’impose aucune limite à la taille de compte, les tailles de fichier ou la quantité de données qui peuvent être stockées dans un lac de données. Les fichiers individuels peuvent être comprise entre kilo-octets à plusieurs pétaoctets, taille rend un très bon choix pour stocker tout type de données. Les données sont stockées durablement en créant des copies multiples et qu’il n’y a aucune limite quant à la durée pour laquelle les données peuvent être stockées dans le lac de données.

### <a name="performance-tuned-for-big-data-analytics"></a>Performances optimisées pour l’analytique des données big

Magasin de LAC de données Azure est construit pour l’exécution des systèmes d’analyse nécessitant un énorme débit pour interroger et analysez de grandes quantités de données à grande échelle. Le lac de données propage les parties d’un fichier sur un nombre de serveurs de stockage individuel. Cela permet d’améliorer le débit de lecture lors de la lecture du fichier en parallèle pour effectuer l’analytique des données.


### <a name="enterprise-ready-highly-available-and-secure"></a>Prêt à l’emploi : Hautement disponibles et sécurisés

Magasin de LAC de données Azure offre la fiabilité et la disponibilité des normes de l’industrie. Vos ressources de données sont stockés durablement en créant des copies redondantes pour vous protéger contre les défaillances inattendues. Les entreprises peuvent recourir lac de données Azure dans leurs solutions sous la forme d’une partie importante de leur plate-forme de données existante.

Magasin de données lac offre également une sécurité de niveau entreprise pour les données stockées. Pour plus d’informations, consultez [sécurisation des données dans le magasin de LAC de données Azure](#DataLakeStoreSecurity).


### <a name="all-data"></a>Toutes les données

Magasin de LAC de données Azure peut stocker des données dans leur format natif, en l’état, sans nécessiter aucune transformation préalable. Magasin de données lac ne nécessite pas d’un schéma à être définis avant le chargement des données, laissant le cadre analytique individuel pour interpréter les données et définir un schéma au moment de l’analyse. Sa capacité à stocker des fichiers de tailles arbitraires et de formats permet de magasin de données lac gérer des données structurées, semi-structurées et non structurées.

Conteneurs d’Azure données lac banque de données sont essentiellement des dossiers et des fichiers. Vous opèrent sur les données stockées à l’aide de Powershell d’Azure, Azure Portal et kits de développement logiciel. Dans la mesure où vous mettez vos données dans le magasin de l’utilisation de ces interfaces et les conteneurs appropriés, vous pouvez stocker n’importe quel type de données. Magasin lac de données n’effectue aucun traitement spécial des données en fonction du type de données qu’il stocke.


## <a name="DataLakeStoreSecurity"></a>Sécurisation des données dans le magasin de LAC de données Azure

Magasin de LAC de données Azure utilise Azure Active Directory pour l’authentification et la liste de contrôle d’accès (ACL) pour gérer l’accès à vos données.

| Fonctionnalité                                 | Description                              |
|-----------------------------------------|------------------------------------------|
| Authentification | Le lac Azure Data Store s’intègre avec Azure Active Directory (DAS) pour la gestion des identités et des accès pour toutes les données stockées dans le magasin de LAC de données Azure. Grâce à l’intégration, les avantages du lac de données Azure à partir de toutes les fonctionnalités de DAS, y compris plusieurs facteurs d’authentification, accès conditionnel, contrôle d’accès basé sur les rôles, contrôle de l’utilisation des applications, sécurité, de surveillance et d’alerte, etc. Magasin de LAC de données Azure prend en charge le protocole OAuth 2.0 pour l’authentification avec l’interface REST. |
| Contrôle d’accès                          | Magasin de LAC de données Azure fournit un contrôle d’accès en prenant en charge les autorisations POSIX-style exposées par le protocole de WebHDFS. Dans la version actuelle, les ACL peut être activées sur le dossier racine, les sous-dossiers, ainsi que des fichiers individuels. Les ACL que vous appliquez au dossier racine seront également applicable à tous les enfants dossiers/fichiers également.|

Vous souhaitez en savoir plus sur la sécurisation des données dans le magasin de données lac. Suivez les liens ci-dessous.

* Pour obtenir des instructions sur la façon de sécuriser les données dans le magasin de données lac, consultez [sécurisation des données dans le magasin de LAC de données Azure](data-lake-store-secure-data.md).
* Préférez des vidéos ? [Regardez cette vidéo](https://mix.office.com/watch/1q2mgzh9nn5lx) sur la façon de sécuriser les données stockées dans le magasin de données lac.

## <a name="applications-compatible-with-azure-data-lake-store"></a>Applications compatibles avec le lac Azure Data Store

Le lac Azure Data Store est compatible avec les composants source plus ouverts dans l’écosystème Hadoop. Il s’intègre également parfaitement avec d’autres services Azure. Magasin de données lac ainsi une option idéale pour vos besoins de stockage de données. Suivez les liens ci-dessous pour en savoir plus sur la banque de données lac peut être utilisé avec les composants open source ainsi que les autres services Azure.

* Voir des [Applications et des services compatibles avec le lac Azure Data Store](data-lake-store-compatible-oss-other-applications.md) pour obtenir la liste des applications à code source ouvert interopérables avec le magasin de données lac.
* Consultez l' [intégration avec d’autres services Azure](data-lake-store-integrate-with-other-services.md) pour comprendre comment le magasin de LAC de données peut être utilisé avec d’autres services Azure pour permettre un plus large éventail de scénarios.
* Consultez les [scénarios d’utilisation de magasin de données lac](data-lake-store-data-scenarios.md) pour apprendre à utiliser le magasin de données lac dans des scénarios tels que l’ingestion des données, de traitement des données, de télécharger des données et de visualisation de données.

## <a name="what-is-azure-data-lake-store-file-system-adl"></a>Quel est le système de fichiers de banque de LAC de données Azure (adl : / /) ?

Magasin de données lac est accessible via le nouveau système de fichiers, le AzureDataLakeFilesystem (adl : / /), dans les environnements d’Hadoop (disponibles avec le cluster de HDInsight). Applications et services qui utilisent adl : / / sont en mesure de tirer parti de davantage l’optimisation des performances qui ne sont pas actuellement disponibles dans WebHDFS. Par conséquent, banque de données lac vous offre la possibilité soit bénéficier les meilleures performances avec l’option recommandée d’utilisation adl : / / ou conserver le code existant en continuant à utiliser l’API WebHDFS directement. HDInsight Azure tire pleinement parti de la AzureDataLakeFilesystem pour fournir les meilleures performances sur le magasin de données lac.

Vous pouvez accéder à vos données de la banque de données lac en utilisant `adl://<data_lake_store_name>.azuredatalakestore.net`. Pour plus d’informations sur la façon d’accéder aux données du magasin de données lac, reportez-vous à la section [Afficher les propriétés des données stockées](data-lake-store-get-started-portal.md#properties)

## <a name="how-do-i-start-using-azure-data-lake-store"></a>Comment démarrer à l’aide du magasin de LAC de données Azure ?

Reportez-vous à la section [mise en route de la banque de données lac utilisant le portail Azure](data-lake-store-get-started-portal.md), sur la façon de configurer un magasin de LAC de données en utilisant le portail Azure. Une fois que vous avez déployé lac de données Azure, vous pouvez apprendre à utiliser les offres de données Azure données lac Analytique ou Azure HDInsight avec le magasin de données lac. Vous pouvez également créer une application .NET pour créer un compte Azure données lac banque et effectuer des opérations telles que les données de téléchargement, télécharger des données, etc..

- [Mise en route de données Azure lac Analytique](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Utilisez HDInsight Azure avec le magasin de données lac](data-lake-store-hdinsight-hadoop-use-portal.md)
- [Mise en route de magasin de LAC de données Azure à l’aide du Kit de développement .NET](data-lake-store-get-started-net-sdk.md)


## <a name="data-lake-store-videos"></a>Vidéos de magasin de données lac

Si vous préférez le visionnage de vidéos pour en savoir plus, banque de données lac fournit des vidéos sur un éventail de fonctionnalités.

* [Créer un compte de banque de LAC de données Azure](https://mix.office.com/watch/1k1cycy4l4gen)
* [Utilisez l’Explorateur de données pour gérer les données dans le magasin de LAC de données Azure](https://mix.office.com/watch/icletrxrh6pc)
* [Connecter des données Azure lac Analytique à la banque de LAC de données Azure](https://mix.office.com/watch/qwji0dc9rx9k)
* [Magasin de LAC Azure données Access via données lac Analytique](https://mix.office.com/watch/1n0s45up381a8)
* [HDInsight Azure de se connecter au magasin de LAC de données Azure](https://mix.office.com/watch/l93xri2yhtp2)
* [Magasin de LAC Azure données Access via la ruche et de porcins](https://mix.office.com/watch/1n9g5w0fiqv1q)
* [DistCp (copie distribué d’Hadoop) permet de copier des données vers et à partir du magasin de LAC de données Azure](https://mix.office.com/watch/1liuojvdx6sie)
* [Apache Sqoop permet de déplacer des données entre des sources relationnelles et magasin de LAC de données Azure](https://mix.office.com/watch/1butcdjxmu114)
* [Orchestration de données à l’aide de la fabrique de données Azure Azure magasin de données lac](https://mix.office.com/watch/1oa7le7t2u4ka)
* [Sécurisation des données dans le magasin de LAC de données Azure](https://mix.office.com/watch/1q2mgzh9nn5lx)



