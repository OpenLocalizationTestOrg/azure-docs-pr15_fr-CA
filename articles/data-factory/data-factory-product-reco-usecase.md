<properties 
    pageTitle="Cas d’utilisation données Factory - recommandations de produits" 
    description="Obtenir des informations sur un cas d’usage implémenté à l’aide de la fabrique de données Azure ainsi que d’autres services." 
    services="data-factory" 
    documentationCenter="" 
    authors="sharonlo101" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/01/2016" 
    ms.author="shlo"/>

# <a name="use-case---product-recommendations"></a>Utiliser la casse - recommandations de produits 

Azure Data Factory est un des nombreux services utilisés pour implémenter l’Intelligence de Cortana Suite d’accélérateurs de solutions.  Voir [Cortana Intelligence Suite](http://www.microsoft.com/cortanaanalytics) page pour plus d’informations à propos de cette suite. Dans ce document, nous décrire un cas d’usage commun Azure utilisateurs déjà résolu et implémenté à l’aide de la fabrique de données Azure et autres services de composants Cortana Intelligence.

## <a name="scenario"></a>Scénario

Magasins en ligne que généralement inciter leurs clients d’acheter des produits en leur présentant des produits qu’ils sont susceptibles de les intéresser et par conséquent plus susceptibles d’acheter. Pour ce faire, les détaillants en ligne ont besoin personnaliser leurs l’expérience l’utilisateur en ligne à l’aide personnalisée de produits recommandés pour un utilisateur spécifique. Ces recommandations personnalisées sont en fonction de leurs en cours et l’historique de vos achats de données de comportement, les informations de produit, marques nouvellement introduites et produit et segmentation des données client.  En outre, elles peuvent fournir les recommandations de produits utilisateur basées sur l’analyse du comportement de l’utilisation globale de tous les utilisateurs de leurs combinés.

L’objectif de ces détaillants est d’optimiser pour les conversions de clic à la vente d’utilisateur et de gagner des affaires plus élevé.  Ils atteignent cette conversion en fournissant des recommandations produit contextuelles, basée sur le comportement en fonction des intérêts du client et des actions. Pour ce cas d’usage, nous utilisons des détaillants en ligne sous la forme d’un exemple des entreprises qui souhaitent optimiser pour leurs clients. Toutefois, ces principes s’appliquent à toute entreprise qui souhaite exercer ses clients autour de ses biens et de services et d’améliorer l’expérience d’achat leurs clients avec les recommandations de produits personnalisés.

## <a name="challenges"></a>Défis

Il sont a de nombreux défis auxquels sont confrontées les détaillants en ligne lorsque vous tentez d’implémenter ce type de cas d’usage. 

Tout d’abord, les données de différentes tailles et formes doivent être ingérées à partir de plusieurs sources de données, à la fois sur site et dans le cloud. Ces données comprennent des données sur les produits, les données de comportement historique et les données utilisateur que l’utilisateur navigue sur le site de vente au détail en ligne. 

Recommandations de produit personnalisé, deuxième doivent raisonnablement et correctement calculées et prévisible. En plus de produit, de marque et les données de comportement et le navigateur client, détaillants en ligne également besoin d’inclure des commentaires des clients sur ses achats antérieurs à facteur dans la détermination des meilleures recommandations de produit pour l’utilisateur. 

Troisièmement, les recommandations doivent être immédiatement livrables à l’utilisateur de fournir une navigation transparente et achats d’expérience et de fournir les recommandations les plus récentes et pertinentes. 

Enfin, les détaillants ont besoin mesurer l’efficacité de leur approche par le suivi des ventes globales et -conversion en un clic des réussites ventes croisées et ajuster à leurs recommandations futures.

## <a name="solution-overview"></a>Présentation de la solution

Cet exemple de cas d’utilisation a été résolu et implémentée par les utilisateurs réels Azure à l’aide de la fabrique de données Azure et autres services de composants Cortana Intelligence, y compris [HDInsight](https://azure.microsoft.com/services/hdinsight/) et [Alimentation BI](https://powerbi.microsoft.com/).

Le détaillant en ligne utilise un magasin Azure Blob, un local SQL server, base de données SQL Azure et un Datamart relationnelle comme leurs options de stockage des données tout au long du flux de travail.  Le magasin de blob contient des informations sur les clients, les données de comportement client et données des informations de produit. Les données d’informations produits contient des informations de marque de produit et un produit du catalogue stockées sur site dans un entrepôt de données SQL. 

Toutes les données sont combinées et intégrées dans un système de recommandation de produit pour fournir des recommandations personnalisées basées sur les intérêts du client et des actions, tandis que l’utilisateur navigue sur les produits dans le catalogue sur le site Web. Les clients voient également les produits qui sont associés au produit qu’ils cherchent à basé sur des modèles d’utilisation globale de site Web qui ne sont pas liées à un utilisateur.

![diagramme de cas d’utilisation](./media/data-factory-product-reco-usecase/diagram-1.png)

Gigaoctets de fichiers journaux web brutes sont générés tous les jours à partir du site marchand en tant que fichiers semi-structurées. Les fichiers journaux web bruts et les informations clients et les produits du catalogue étant ingérée régulièrement dans un stockage Azure Blob à l’aide de mouvement de données déployés globalement de fabrique de données en tant que service. Les fichiers journaux brutes du jour sont partitionnés (par année et le mois) dans le stockage blob pour le stockage à long terme.  [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) est utilisé pour les fichiers journaux brutes dans le magasin de blob de la partition et traiter les journaux ingérés à grande échelle à l’aide de scripts de ruche et de porcins. Le web partitionné les journaux de données est ensuite traitée pour extraire les entrées nécessaires pour une machine de formation de système de recommandation pour générer les recommandations de produits personnalisés.

Le système de recommandation utilisé pour la machine d’apprentissage dans cet exemple est une machine de source ouverte plate-forme de recommandation [d’Apache Mahout](http://mahout.apache.org/)de formation.  Toute [Formation de Machine Azure](https://azure.microsoft.com/services/machine-learning/) ou d’un modèle personnalisé peut être appliqué au scénario.  Le modèle Mahout est utilisé pour prévoir la similitude entre les éléments sur le site Web basé sur des modèles d’utilisation globale et de générer des recommandations personnalisées en fonction de l’utilisateur individuel.

Enfin, le jeu de résultats des recommandations de produits personnalisés est déplacé vers un Datamart relationnelle pour la consommation par le site Web du détaillant.  Le jeu de résultats peut également être accessible directement depuis le stockage blob par une autre application ou déplacé vers des banques supplémentaires pour d’autres consommateurs et les cas d’usage.

## <a name="benefits"></a>Avantages

Par l’optimisation de leur stratégie de recommandation de produit et alignez-le sur les objectifs de l’entreprise, la solution remplies de merchandising du détaillant en ligne et des objectifs de marketing. En outre, ils ont pu mettre et gérer le flux de travail de recommandation de produit de manière efficace, fiable et économique. L’approche simplifiée pour les mettre à jour leur modèle et améliorer son efficacité, basée sur les mesures de ventes succès de conversion en un clic. En utilisant le Factory de données Azure, ils ont pu abandonner leur gestion des ressources de cloud de manuels fastidieux et onéreux et de passer à la gestion des ressources de cloud de la demande. Par conséquent, ils ont été en mesure de gagner du temps, argent et réduire le temps de déploiement de la solution. Vues de lignage des données et l’intégrité du service opérationnel est devenu faciles à visualiser et résoudre les problèmes liés à la surveillance de Data Factory intuitif et à la gestion de l’interface utilisateur disponible à partir du portail Azure. Leur solution peut maintenant être planifiée et gérée afin que les données terminée sont fiable produites et livrées aux utilisateurs et données et traitement des dépendances sont gérées automatiquement sans intervention humaine.

Grâce à cette expérience d’achat personnalisée, le détaillant en ligne créé un client plus compétitif, agréable expérience et donc augmenter la satisfaction client globale et de vente.



  