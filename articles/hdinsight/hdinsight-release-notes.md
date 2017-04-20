<properties
    pageTitle="Notes de version pour les composants d’Hadoop sur Azure HDInsight | Microsoft Azure"
    description="Dernières notes de version et les versions des composants Hadoop pour Azure HDInsight. Obtenir des détails et des conseils de développement pour Hadoop, Apache tempête et HBase."
    services="hdinsight"
    documentationCenter=""
    editor="cgronlun"
    manager="jhubbard"
    authors="nitinme"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/27/2016"
    ms.author="nitinme"/>


# <a name="release-notes-for-hadoop-components-on-azure-hdinsight"></a>Notes de version pour les composants d’Hadoop sur Azure HDInsight

## <a name="notes-for-10262016-release-of-r-server-on-hdinsight"></a>Notes de version 26/10/2016 du serveur R sur HDInsight

- Serveur R HDInsight la mise en service de cluster a été rationalisé.
- Serveur R HDInsight est désormais disponible en tant que HDInsight régulière « Serveur R » type de cluster et n’est plus installé comme une application séparée de la HDInsight. Le nœud de bord et les fichiers binaires du serveur de R sont désormais mis en service dans le cadre du déploiement de cluster de serveur de R. Cela améliore la vitesse et la fiabilité de la mise en service. Modèle de tarification de R Server est mis à jour en conséquence.
- Prix de cluster Server de R est désormais basée sur prix de couche Standard plus les prix de surcharge de serveur de R. Niveau de prime sera maintenant réservée pour les fonctionnalités Premium disponibles parmi les différents types de cluster différent et ne sera pas utilisé pour le type de cluster Server de R. Cette modification n’affecte pas de tarification efficace du serveur de R, il modifie uniquement la façon dont les frais sont présentés dans la nomenclature. Tous les clusters serveur de R existants continueront de fonctionner, et les modèles ARM continuera de fonctionner jusqu'à ce que l’avis de la désapprobation. **Il est recommandé de bien que mettre à jour vos déploiements de script pour utiliser le nouveau modèle ARM.**


## <a name="notes-for-08302016-release-of-r-server-on-hdinsight"></a>Notes de version du 30/08/2016 du serveur R sur HDInsight

Les numéros de version complète pour les clusters basés sur Linux de HDInsight déployés dans cette version :

|HDI |Version de cluster HDI   |HDP |Génération HDP   |Génération de Ambari |
|----|----------------------|----|------------|-------------|
|3.2 |3.2.1000.0.8268980    |2.2 |2.2.9.1-19  |2.2.1.12-4   |
|3.3 |3.3.1000.0.8268980    |2.3 |2.3.3.1-25  |2.2.1.12-4   |
|3.4 |3.4.1000.0.8269383    |2.4 |2.4.2.4-5   |2.2.1.12-4   |

Les numéros de version complète pour les clusters basés sur Windows de HDInsight déployés dans cette version :

|HDI |Version de cluster HDI   |HDP |Génération HDP     |
|----|----------------------|----|--------------|
|2.1 |2.1.10.1033.2559206   |1.3 |1.3.12.0-01795|
|3.0 |3.0.6.1033.2559206    |2.0 |2.0.13.0-2117 |
|3.1 |3.1.4.1033.2559206    |2.1 |2.1.16.0-2374 |
|3.2 |3.2.7.1033.2559206    |2.2 |2.2.9.1-11    |
|3.3 |3.3.0.1033.2559206    |2.3 |2.3.3.1-25    |

## <a name="notes-for-08172016-release-of-r-server-on-hdinsight"></a>Notes de version du 17/08/2016 du serveur R sur HDInsight

- Serveur R 8.0.5 – principalement une version de correctif de bogue. Consultez les [Notes de mise à jour du serveur R](https://msdn.microsoft.com/microsoft-r/notes/r-server-notes) pour plus d’informations. 
- Package AzureML sur le nœud de bord – [ce package R](https://cran.r-project.org/web/packages/AzureML/vignettes/getting_started.html) permet de modèles R soit publié et consommées dans un service web de ML d’Azure.  Consultez la section [« effectuent un modèle »](hdinsight-hadoop-r-server-overview.md#operationalize-a-model) de notre article [« vue d’ensemble de R Server sur HDInsight »](hdinsight-hadoop-r-server-overview.md) pour plus d’informations.
- Dépendances de Linux [top 100 plus populaires R les packages](https://github.com/metacran/cranlogs) – ces dépendances du package sont désormais pré-installé de Linux.  
- Option à utiliser le mis en pension CRAN lorsque R l’ajout de packages pour les nœuds de données. Consultez la section [« Packages d’installation R »](hdinsight-hadoop-r-server-get-started.md#install-r-packages) de notre article [« Mise en route à l’aide du serveur R HDInsight »](hdinsight-hadoop-r-server-get-started.md) pour plus d’informations.
- Amélioration de la fiabilité de la mise en service de R Server lors de la création de clusters.


## <a name="notes-for-08012016-release-of-hdinsight"></a>Notes de version de 08/01/2016 de HDInsight

Les numéros de version complète pour les clusters basés sur Linux de HDInsight déployés dans cette version :

|HDI |Version de cluster HDI   |HDP |Génération HDP   |Génération de Ambari |
|----|----------------------|----|------------|-------------|
|3.2 |3.2.1000.0.8028416    |2.2 |2.2.9.1-19  |2.2.1.12-4   |
|3.3 |3.3.1000.0.8028416    |2.3 |2.3.3.1-25  |2.2.1.12-4   |
|3.4 |3.4.1000.0.8053402    |2.4 |2.4.2.4-5   |2.2.1.12-4   |

Les numéros de version complète pour les clusters basés sur Windows de HDInsight déployés dans cette version :

|HDI |Version de cluster HDI   |HDP |Génération HDP     |
|----|----------------------|----|--------------|
|2.1 |2.1.10.1005.2488842   |1.3 |1.3.12.0-01795|
|3.0 |3.0.6.1005.2488842    |2.0 |2.0.13.0-2117 |
|3.1 |3.1.4.1005.2488842    |2.1 |2.1.16.0-2374 |
|3.2 |3.2.7.1005.2488842    |2.2 |2.2.9.1-11    |
|3.3 |3.3.0.1005.2488842    |2.3 |2.3.3.1-25    |

Cette version contient les mises à jour suivantes.

| Titre                                           | Description                                          | Zone d’impactés (par exemple, Service, composant ou SDK) | Type de cluster (par exemple étincelle, Hadoop, HBase ou tempête) | JIRA (si applicable) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Modifications apportées aux clusters de HDInsight 3.4 | La valeur par défaut pour les configurations de ruche suivantes sont modifiées pour améliorer les performances <ul><li>`hive.vectorized.execution.reduce.enabled=true`</li><li>`hive.tez.min.partition.factor=1f`</li><li>`hive.tez.max.partition.factor=3f`</li><li>`tez.shuffle-vertex-manager.min-src-fraction=0.9`</li><li>`tez.shuffle-vertex-manager.max-src-fraction=0.95`</li><li>`tez.runtime.shuffle.connect.timeout= 30000`</li></ul>| Service    | Tous les| N/A|
| Les correctifs suivants sont inclus dans cette version | RUCHE-13632, HIVE-12897,HIVE-12907,HIVE-12908,HIVE-12988,HIVE-13510,HIVE-13572,HIVE-13716,HIVE-13726,HIVE-12505,HIVE-13632,HIVE-13661,HIVE-13705,HIVE-13743,HIVE-13810,HIVE-13857,HIVE-13902,HIVE-13911,HIVE-13933| Service    | Tous les| N/A

## <a name="notes-for-07142016-release-of-hdinsight"></a>Notes de version 07/14/2016 de HDInsight

Les numéros de version complète pour les clusters basés sur Linux de HDInsight déployés dans cette version :

|HDI |Version de cluster HDI   |HDP |Génération HDP   |Génération de Ambari |
|----|----------------------|----|------------|-------------|
|3.2 |3.2.1000.0.7932505    |2.2 |2.2.9.1-11  |2.2.1.12-2   |
|3.3 |3.3.1000.0.7932505    |2.3 |2.3.3.1-18  |2.2.1.12-2   |
|3.4 |3.4.1000.0.7933003    |2.4 |2.4.2.0     |2.2.1.12-2   |

Les numéros de version complète pour les clusters basés sur Windows de HDInsight déployés dans cette version :

|HDI |Version de cluster HDI   |HDP |Génération HDP     |
|----|----------------------|----|--------------|
|2.1 |2.1.10.989.2441725    |1.3 |1.3.12.0-01795|
|3.0 |3.0.6.989.2441725     |2.0 |2.0.13.0-2117 |
|3.1 |3.1.4.989.2441725     |2.1 |2.1.16.0-2374 |
|3.2 |3.2.7.989.2441725     |2.2 |2.2.9.1-11    |
|3.3 |3.3.0.989.2441725     |2.3 |2.3.3.1-21    |

## <a name="notes-for-07072016-release-of-hdinsight"></a>Notes de version de 07/07/2016 de HDInsight

Les numéros de version complète pour les clusters basés sur Linux de HDInsight déployés dans cette version :

|HDI |Version de cluster HDI   |HDP |Génération HDP   |
|----|----------------------|----|------------|
|3.2 |3.2.1000.0.7864996    |2.2 |2.2.9.1-11  |
|3.3 |3.3.1000.0.7864996    |2.3 |2.3.3.1-18  |
|3.4 |3.4.1000.0.7861906    |2.4 |2.4.2.0     |

Les numéros de version complète pour les clusters basés sur Windows de HDInsight déployés dans cette version :

|HDI |Version de cluster HDI   |HDP |Génération HDP     |
|----|----------------------|----|--------------|
|2.1 |2.1.10.977.2413853    |1.3 |1.3.12.0-01795|
|3.0 |3.0.6.977.2413853     |2.0 |2.0.13.0-2117 |
|3.1 |3.1.4.977.2413853     |2.1 |2.1.16.0-2374 |
|3.2 |3.2.7.977.2413853     |2.2 |2.2.9.1-11    |
|3.3 |3.3.0.977.2413853     |2.3 |2.3.3.1-21    |

Cette version contient les mises à jour suivantes.

| Titre                                           | Description                                          | Zone d’impactés (par exemple, Service, composant ou SDK) | Type de cluster (par exemple étincelle, Hadoop, HBase ou tempête) | JIRA (si applicable) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| [Outils d’HDInsight de IntelliJ](hdinsight-apache-spark-intellij-tool-plugin.md) | Plug-in de l’idée d’IntelliJ pour les clusters de HDInsight Spark est maintenant intégré à Azure Shared Computer Toolkit pour IntelliJ. Il prend en charge les v2.9.1 Azure SDK, kits de développement Java plus récente et inclut toutes les fonctionnalités de la version autonome de HDInsight Plugin pour IntelliJ.| Outils    | Allumage| N/A|
| [Outils d’HDInsight pour Éclipse](hdinsight-apache-spark-eclipse-tool-plugin.md) | Azure Shared Computer Toolkit pour Eclipse prend désormais en charge les clusters HDInsight Spark. Il active les fonctionnalités suivantes. <ul><li>Créer et écrire une application d’allumage facilement dans Scala et Java, avec prise en charge d’IntelliSense, la mise en forme automatique, la vérification des erreurs, etc. de la création de première classe.</li><li>Testez l’application étincelle localement.</li><li>Soumettre les tâches au cluster de HDInsight Spark et récupérer les résultats.</li><li>Ouvrez une session dans Azure et accéder à tous les clusters d’allumage associés vos abonnements Azure.</li><li>Accédez à toutes les ressources de stockage associée de votre cluster HDInsight Spark.</li></ul>| Outils    | Allumage| N/A

À partir de cette version, nous avons modifié la stratégie de gestion des correctifs du système d’exploitation invité pour les clusters de HDInsight de fonctionnant sous Linux. L’objectif de la nouvelle stratégie est de réduire sensiblement le nombre de redémarrages en raison de l’application de correctifs. La nouvelle stratégie continuera correctif des machines virtuelles (VM) dans les clusters Linux chaque lundi ou un jeudi commençant à 12 : 00 UTC, Universal Time de manière échelonnée sur plusieurs nœuds dans un cluster donné. Toutefois, une machine virtuelle donnée redémarre uniquement au maximum une fois tous les 30 jours en raison de l’application des correctifs du système d’exploitation invité. En outre, le premier redémarrage pour un nouveau cluster ne se reproduira plus tôt que 30 jours à compter de la date de création du cluster.

>[AZURE.NOTE] Ces modifications s’appliquent uniquement aux clusters nouvellement créés égales ou supérieurs à cette version.

## <a name="notes-for-06062016-release-of-hdinsight"></a>Notes de release 06/06/2016 de HDInsight

Les numéros de version complète des clusters HDInsight déployés dans cette version :

|HDP    |Version HDI    |Version d’allumage  |Numéro de version de Ambari    |Numéro de Build HDP|
|-------|---------------|---------------|-----------------------|----------------|
|2.3    |3.3.1000.0.7702215|    1.5.2|  2.2.1.8-2|  2.3.3.1-18|
|2.4    |3.4.1000.0.7702224|    1.6.1|  2.2.1.8-2|  2.4.2.0|


Cette version contient les mises à jour suivantes.

| Titre                                           | Description                                          | Zone d’impactés (par exemple, Service, composant ou SDK) | Type de cluster (par exemple étincelle, Hadoop, HBase ou tempête) | JIRA (si applicable) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Explosion de HDInsight est généralement disponible | Cette version apporte des améliorations de la disponibilité, l’évolutivité et la productivité pour ouvrir source étincelle Apache sur HDInsight. <ul><li>Pointe de disponibilité SLA de 99,9 %, qui la rend utilisable pour des charges de l’entreprise.</li><li>Couche de stockage évolutif à l’aide du magasin de LAC de données Azure.</li><li>Outils de productivité pour chaque phase de développement et d’exploration de données. Portables Jupyter avec noyau d’allumage personnalisé activer l’exploration de données interactive, intégration avec les tableaux de bord analyse Décisionnelle telles que Power BI, Tableau et Qlik est utile pour le partage de données rapides et continue de création de rapports, plug-in de IntelliJ est une option fiable pour le développement à long terme du artefact de code et de débogage.</li></ul>| Service    | Allumage| N/A|
| Outils d’HDInsight de IntelliJ | Il s’agit d’un plug-in de l’idée d’IntelliJ pour les clusters de HDInsight Spark. Il active les fonctionnalités suivantes.<ul><li>Créer et écrire une application d’allumage facilement dans Scala et Java, avec prise en charge d’IntelliSense, la mise en forme automatique, la vérification des erreurs, etc. de la création de première classe.</li><li>Testez l’application étincelle localement.</li><li>Soumettre les tâches au cluster de HDInsight Spark et récupérer les résultats.</li><li>Ouvrez une session dans Azure et accéder à tous les clusters d’allumage associés vos abonnements Azure.</li><li>Accédez à toutes les ressources de stockage associée de votre cluster HDInsight Spark.</li><li>Accédez à toutes les informations de travail pour votre cluster HDInsight Spark et l’historique des travaux.</li><li>Déboguer les travaux d’allumage commandé à distance à partir de votre ordinateur de bureau.</li></ul>| Outils    | Allumage| N/A

## <a name="notes-for-05132016-release-of-hdinsight"></a>Notes de version 13/05/2016 de HDInsight

Les numéros de version complète des clusters HDInsight déployés dans cette version :

* HDInsight (Windows) 2.1.10.875.2159884 (1.3.12.0-01795 HDP - inchangée)
* HDInsight (Windows) 3.0.6.875.2159884 (2.0.13.0-2117 HDP - inchangée)
* HDInsight (Windows) 3.1.4.922.2266903 (2.1.15.0-2374 HDP - inchangée)
* HDInsight (Windows) 3.2.7.922.2266903 (HDP 2.2.9.1-11)
* HDInsight (Windows) 3.3.0.922.2266903 (HDP 2.3.3.1-18)
* HDInsight (Linux) 3.2.1000.0.7565644 (HDP 2.2.9.1-11)
* HDInsight (Linux) 3.3.1000.0.7565644 (HDP 2.3.3.1-18)
* HDInsight (Linux) 3.4.1000.0.7548380 (HDP 2.4.2.0)

Cette version contient les mises à jour suivantes.

| Titre                                           | Description                                          | Zone d’impactés (par exemple, Service, composant ou SDK) | Type de cluster (par exemple étincelle, Hadoop, HBase ou tempête) | JIRA (si applicable) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Service de mise à jour de la version et d’autres correctifs de bogue | Cette version met à jour la version d’allumage HDInsight cluster 1.6.1 et résout d’autres bogues.| Service    | Allumage| N/A

## <a name="notes-for-04112016-release-of-hdinsight"></a>Notes de version 11/04/2016 de HDInsight

Les numéros de version complète des clusters HDInsight déployés dans cette version :

* HDInsight (Windows) 2.1.10.889.2191206 (1.3.12.0-01795 HDP - inchangée)
* HDInsight (Windows) 3.0.6.889.2191206 (2.0.13.0-2117 HDP - inchangée)
* HDInsight (Windows) 3.1.4.889.2191206 (2.1.15.0-2374 HDP - inchangée)
* HDInsight (Windows) 3.2.7.889.2191206 (HDP 2.2.9.1-10)
* HDInsight (Windows) 3.3.0.889.2191206 (2.3.3.1-16 HDP-inchangée)
* HDInsight (Linux) 3.2.1000.0.7339916 (HDP 2.2.9.1-10)
* HDInsight (Linux) 3.3.1000.0.7339916 (HDP 2.3.3.1-16)
* HDInsight (Linux) 3.4.1000.0.7338911 (HDP 2.4.1.1-3)
* KIT DE DÉVELOPPEMENT LOGICIEL 1.5.8

Cette version contient les mises à jour suivantes.

| Titre                                           | Description                                          | Zone d’impactés (par exemple, Service, composant ou SDK) | Cluster de Type (par exemple, Hadoop, HBase ou tempête) | JIRA (si applicable) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Problèmes de mise à niveau de metastore personnalisé pour HDI 3.4 | Création d’un cluster échoue si vous avez utilisé un metastore personnalisé, ce qui a été utilisé précédemment dans une version antérieure d’un autre cluster HDInsight. Cela est dû à une erreur de script de mise à niveau qui a désormais été résolue| Création d’un cluster    | Tous les | N/A
| Récupération après incident de Livy | Fournit la tolérance d’état de tâche pour tout projet soumis par le biais de Livy | Fiabilité | Allumage sous Linux| N/A
| Contenu de Jupyter HA | Offre la possibilité d’enregistrer et de charger le contenu du bloc-notes Jupyter vers et à partir du compte de stockage associé au cluster. Pour plus d’informations, reportez-vous à la section [noyaux disponibles pour les ordinateurs portables de Jupyter](hdinsight-apache-spark-jupyter-notebook-kernels.md).| Ordinateurs portables | Allumage sous Linux| N/A
| Suppression de hiveContext dans les ordinateurs portables de Jupter | Utilisez `%%sql` magique plutôt `%%hive` magique. SqlContext est équivalent à hiveContext. Pour plus d’informations, consultez [noyaux disponibles pour les ordinateurs portables de Jupyter](hdinsight-apache-spark-jupyter-notebook-kernels.md)| Ordinateurs portables    | Allumage des clusters sous Linux| N/A
| Désapprobation d’anciennes versions d’allumage | Ancienne version 1.3.1 d’allumage sera retirée du service sur 31/5 | Service | Allumage des clusters sous Windows | N/A

## <a name="notes-for-03292016-release-of-hdinsight"></a>Notes de version de 29/03/2016 de HDInsight

Les numéros de version complète des clusters HDInsight déployés dans cette version :

* HDInsight (Windows) 2.1.10.875.2159884 (1.3.12.0-01795 HDP - inchangée)
* HDInsight (Windows) 3.0.6.875.2159884 (2.0.13.0-2117 HDP - inchangée)
* HDInsight (Windows) 3.1.4.875.2159884 (2.1.15.0-2374 HDP - inchangée)
* HDInsight (Windows) 3.2.7.875.2159884 (2.2.9.1-7 HDP - inchangée)
* HDInsight (Windows) 3.3.0.875.2159884 (HDP 2.3.3.1-16)
* HDInsight (Linux) 3.2.1000.0.7193255 (2.2.9.1-8 HDP - inchangée)
* HDInsight (Linux) 3.3.1000.0.7193255 (2.3.3.1-7 HDP - inchangée)
* HDInsight (Linux) 3.4.1000.0.7195842 (HDP 2.4.1.0-327)
* KIT DE DÉVELOPPEMENT LOGICIEL 1.5.8

Cette version contient les mises à jour suivantes.

| Titre                                           | Description                                          | Zone d’impactés (par exemple, Service, composant ou SDK) | Cluster de Type (par exemple, Hadoop, HBase ou tempête) | JIRA (si applicable) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Ajouté HDInsight 3.4 et les versions HDP mis à jour pour tous les clusters HDInsight | Dans cette version, nous avez ajouté v3.4 HDInsight (basé sur HDP 2.4) et ont également mis à jour d’autres versions HDP. Notes de publication de HDP 2.4 sont disponibles [ici](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.4.0/bk_HDP_RelNotes/content/ch_relnotes_v240.html) et plus d’informations sur les HDInsight les versions peuvent être trouvés [ici](hdinsight-component-versioning.md).| Service    | Tous les clusters Linux| N/A
| Prime de HDInsight | HDInsight est désormais disponible en deux catégories : Standard et Premium. HDInsight prime est actuellement en mode Aperçu et disponible uniquement pour les Hadoop et érosion clusters sous Linux. Pour plus d’informations, voir [ici](hdinsight-component-versioning.md#hdinsight-standard-and-hdinsight-premium).| Service    | Hadoop et allumage sous Linux| N/A
| Serveur de Microsoft R | HDInsight prime fournit Microsoft R qui peut être inclus avec Hadoop et érosion clusters sous Linux. Pour plus d’informations, consultez [Vue d’ensemble du serveur R sur HDInsight](hdinsight-hadoop-r-server-overview.md).| Service    | Hadoop et allumage sous Linux| N/A
| Allumage 1.6.0 | Les clusters HDInsight 3.4 incluent désormais étincelle 1.6.0| Service    | Allumage des clusters sous Linux| N/A
| Améliorations de portable Jupyter | Disponible avec les clusters d’allumage à Jupyter ordinateurs portables désormais une étincelle supplémentaire noyaux. Ils incluent également des améliorations telles que l’utilisation de %% magic, visualisation automatique et l’intégration avec les bibliothèques de visualisation de Python (par exemple, matplotlib). Pour plus d’informations, reportez-vous à la section [noyaux disponibles pour les ordinateurs portables de Jupyter](hdinsight-apache-spark-jupyter-notebook-kernels.md). | Service | Allumage des clusters sous Linux | N/A

## <a name="notes-for-03222016-release-of-hdinsight"></a>Notes de version de 22/03/2016 de HDInsight

Les numéros de version complète des clusters HDInsight déployés dans cette version :

* HDInsight (Windows) 2.1.10.875.2159884 (1.3.12.0-01795 HDP - inchangée)
* HDInsight (Windows) 3.0.6.875.2159884 (2.0.13.0-2117 HDP - inchangée)
* HDInsight (Windows) 3.1.4.875.2159884 (2.1.15.0-2374 HDP - inchangée)
* HDInsight (Windows) 3.2.7.875.2159884 (2.2.9.1-7 HDP - inchangée)
* HDInsight (Windows) 3.3.0.875.2159884 (HDP 2.3.3.1-16)
* HDInsight (Linux) 3.2.1000.0.7193255 (2.2.9.1-8 HDP - inchangée)
* HDInsight (Linux) 3.3.1000.0.7193255 (2.3.3.1-7 HDP - inchangée)
* KIT DE DÉVELOPPEMENT LOGICIEL 1.5.8

Cette version contient les mises à jour suivantes.

| Titre                                           | Description                                          | Zone d’impactés (par exemple, Service, composant ou SDK) | Cluster de Type (par exemple, Hadoop, HBase ou tempête) | JIRA (si applicable) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Versions HDInsight mises à jour pour tous les clusters HDInsight | Avec cette version, nous avons mis à jour de versions de HDInsight pour tous les clusters HDInsight| Service    | Tous les| N/A


## <a name="notes-for-03102016-release-of-hdinsight"></a>Notes de version 10/03/2016 de HDInsight

Les numéros de version complète des clusters HDInsight déployés dans cette version :

* HDInsight (Windows) 2.1.10.859.2123216 (1.3.12.0-01795 HDP - inchangée)
* HDInsight (Windows) 3.0.6.859.2123216 (2.0.13.0-2117 HDP - inchangée)
* HDInsight (Windows) 3.1.4.859.2123216 (2.1.15.0-2374 HDP - inchangée)
* HDInsight (Windows) 3.2.7.859.2123216 (HDP 2.2.9.1-7)
* HDInsight (Windows) 3.3.0.859.2123216 (2.3.3.1-5 HDP - inchangée)
* HDInsight (Linux) 3.2.1000.7076817 (HDP 2.2.9.1-8)
* HDInsight (Linux) 3.3.1000.7076817 (HDP 2.3.3.1-7)
* KIT DE DÉVELOPPEMENT LOGICIEL 1.5.8

Cette version contient les mises à jour suivantes.

| Titre                                           | Description                                          | Zone d’impactés (par exemple, Service, composant ou SDK) | Cluster de Type (par exemple, Hadoop, HBase ou tempête) | JIRA (si applicable) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Versions HDInsight mises à jour pour tous les clusters HDInsight | Avec cette version, nous avons mis à jour de versions de HDInsight pour tous les clusters HDInsight| Service    | Tous les| N/A

## <a name="notes-for-01272016-release-of-hdinsight"></a>Notes de version de 27/01/2016 de HDInsight

Les numéros de version complète des clusters HDInsight déployés dans cette version :

* HDInsight (Windows) 2.1.10.817.2028315 (1.3.12.0-01795 HDP - inchangée)
* HDInsight (Windows) 3.0.6.817.2028315 (2.0.13.0-2117 HDP - inchangée)
* HDInsight (Windows) 3.1.4.817.2028315 (2.1.15.0-2374 HDP - inchangée)
* HDInsight (Windows) 3.2.7.817.2028315 (HDP 2.2.9.1-1)
* HDInsight (Windows) 3.3.0.817.2028315 (2.3.3.1-5 HDP - inchangée)
* HDInsight (Linux) 3.2.1000.4072335 (HDP 2.2.9.1-1)
* HDInsight (Linux) 3.3.1000.4072335 (HDP 2.3.3.1-1)
* KIT DE DÉVELOPPEMENT LOGICIEL 1.5.8

Cette version contient les mises à jour suivantes.

| Titre                                           | Description                                          | Zone d’impactés (par exemple, Service, composant ou SDK) | Cluster de Type (par exemple, Hadoop, HBase ou tempête) | JIRA (si applicable) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Versions HDInsight mises à jour pour tous les clusters HDInsight | Avec cette version, nous avons mis à jour de versions de HDInsight pour tous les clusters HDInsight| Service    | Tous les| N/A

## <a name="notes-for-12022015-release-of-hdinsight"></a>Notes de version 12/02/2015 de HDInsight

Les numéros de version complète des clusters HDInsight déployés dans cette version :

* HDInsight (Windows) 2.1.10.763.1931434 (1.3.12.0-01795 HDP - inchangée)
* HDInsight (Windows) 3.0.6.763.1931434 (2.0.13.0-2117 HDP - inchangée)
* HDInsight (Windows) 3.1.4.763.1931434 (2.1.15.0-2374 HDP - inchangée)
* HDInsight (Windows) 3.2.7.763.1931434 (2.2.7.1-34 HDP - inchangée)
* HDInsight (Windows) 3.3.1000.0 (HDP 2.3.3.1-5)
* HDInsight (Linux) 3.2.1000.0.6392801 (2.2.7.1-34 HDP - inchangée)
* HDInsight (Linux) 3.3.1000.0 (HDP 2.3.3.0-3039)
* KIT DE DÉVELOPPEMENT LOGICIEL 1.5.8

Cette version contient les mises à jour suivantes.

| Titre                                           | Description                                          | Zone d’impactés (par exemple, Service, composant ou SDK) | Cluster de Type (par exemple, Hadoop, HBase ou tempête) | JIRA (si applicable) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Ajouté HDInsight 3.3 et les versions HDP mis à jour pour tous les clusters HDInsight | Dans cette version, nous avez ajouté v3.3 HDInsight (basé sur HDP 2.3) et ont également mis à jour d’autres versions HDP. Notes de publication de HDP 2.3 sont disponibles [ici](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.3.0/bk_HDP_RelNotes/content/ch_relnotes_v230.html) et plus d’informations sur les HDInsight les versions peuvent être trouvés [ici](hdinsight-component-versioning.md).| Service    | Tous les| N/A

## <a name="notes-for-11302015-release-of-hdinsight"></a>Notes de version 11/30/2015 de HDInsight

Les numéros de version complète des clusters HDInsight déployés dans cette version :

* HDInsight (Windows) 2.1.10.757.1923908 (1.3.12.0-01795 HDP - inchangée)
* HDInsight (Windows) 3.0.6.757.1923908 (2.0.13.0-2117 HDP - inchangée)
* HDInsight (Windows) 3.1.4.757.1923908 (2.1.15.0-2374 HDP - inchangée)
* HDInsight (Windows) 3.2.7.757.1923908 (HDP 2.2.7.1-34)
* HDInsight (Linux) 3.2.1000.0.6392801 (HDP 2.2.7.1-34)
* KIT DE DÉVELOPPEMENT LOGICIEL 1.5.8

Cette version contient les mises à jour suivantes.

| Titre                                           | Description                                          | Zone d’impactés (par exemple, Service, composant ou SDK) | Cluster de Type (par exemple, Hadoop, HBase ou tempête) | JIRA (si applicable) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Versions HDInsight mises à jour pour tous les clusters de HDInsight et les versions HDP pour les clusters HDInsight 3.2 (Windows et Linux) | Avec cette version, les versions HDInsight et HDP ont été mis à jour | Service    | Tous les| N/A


## <a name="notes-for-10272015-release-of-hdinsight"></a>Notes de version de 27/10/2015 de HDInsight

Les numéros de version complète des clusters HDInsight déployés dans cette version :

* HDInsight (Windows) 2.1.10.726.1866228 (1.3.12.0-01795 HDP - inchangée)
* HDInsight (Windows) 3.0.6.726.1866228 (2.0.13.0-2117 HDP - inchangée)
* HDInsight (Windows) 3.1.4.726.1866228 (2.1.15.0-2374 HDP - inchangée)
* HDInsight (Windows) 3.2.7.726.1866228 (HDP 2.2.7.1-33)
* HDInsight (Linux) 3.2.1000.0.6035701 (HDP 2.2.7.1-33)
* KIT DE DÉVELOPPEMENT LOGICIEL 1.5.8

Cette version contient les mises à jour suivantes.

| Titre                                           | Description                                          | Zone d’impactés (par exemple, Service, composant ou SDK) | Cluster de Type (par exemple, Hadoop, HBase ou tempête) | JIRA (si applicable) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Versions HDInsight mises à jour pour tous les clusters de HDInsight (Windows et Linux) | Avec cette version, les versions HDInsight et HDP ont été mis à jour | Service    | Tous les| N/A
| Clusters Jupyter pour Windows allumage fixes avec des clusters de lettres majuscules | Clusters qui était spécifiés en majuscules les noms DNS avaient des problèmes avec les ordinateurs portables de Jupyter en raison d’une demande de vérification d’origine. Le correctif a été de modifier le nom DNS de la configuration de Jupyter en minuscules. | Service    | HDInsight allumage (Windows)| N/A


## <a name="notes-for-10202015-release-of-hdinsight"></a>Notes de version de 20/10/2015 de HDInsight

Les numéros de version complète des clusters HDInsight déployés dans cette version :

* HDInsight 2.1.10.716.1846990 (Windows) (1.3.12.0-01795 HDP - inchangée)
* HDInsight 3.0.6.716.1846990 (Windows) (2.0.13.0-2117 HDP - inchangée)
* HDInsight 3.1.4.716.1846990 (Windows) (HDP 2.1.16.0-2374)
* HDInsight 3.2.7.716.1846990 (Windows) (HDP 2.2.7.1-0004)
* HDInsight 3.2.1000.0.5930166 (Linux) (HDP 2.2.7.1-0004)
* KIT DE DÉVELOPPEMENT LOGICIEL 1.5.8

Cette version contient les mises à jour suivantes.

| Titre                                           | Description                                          | Zone d’impactés (par exemple, Service, composant ou SDK) | Cluster de Type (par exemple, Hadoop, HBase ou tempête) | JIRA (si applicable) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Version HDP par défaut devient HDP 2.2 | La version par défaut pour les clusters Windows de HDInsight est modifiée en HDP 2.2. HDInsight version 3.2 (HDP 2.2) est généralement disponible dans depuis février 2015. Cette modification retourne uniquement la version de cluster par défaut, lors de la sélection explicite n’a pas été effectuée lors de la mise en service de cluster en utilisant le portail Azure, les applets de commande PowerShell ou le Kit de développement logiciel. | Service    | Tous les| N/A                  |
|Les modifications apportées au format de nom de machine virtuelle pour le déploiement de plusieurs HDInsight dans les clusters Linux dans un seul réseau virtuel | Prise en charge pour le déploiement de plusieurs clusters HDInsight Linux dans un seul réseau virtuel est ajouté dans cette version. Dans le cadre de ce, le format des noms de machine virtuelle du cluster a été modifié à partir de headnode\*, workernode\* et zookeepernode\* à hn\*, wn\*et zk\* respectivement. Il n’est pas une pratique recommandée pour prendre une dépendance directe sur le format des noms de machine virtuelle, dans la mesure où cela est sujet à modification. Utilisez « nom d’hôte -f » sur l’ordinateur local ou d’un Ambari APIs pour déterminer la liste des hôtes et du mappage de composants à des hôtes. Vous trouverez plus d’informations sur [https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/hosts.md](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/hosts.md) et [https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/host-components.md](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/host-components.md). | Service | Clusters HDInsight sous Linux | N/A |
| Modifications de configuration | Pour les clusters de HDInsight 3.1, les configurations suivantes sont maintenant activées : <ul><li>tez.yarn.ATS.Enabled et yarn.log.server.url. Cela permet le montage de serveur d’applications et sur le serveur de journal être en mesure de proposer des journaux.</li></ul>Pour les clusters de HDInsight 3.2, les configurations suivantes ont été modifiées : <ul><li>MapReduce.fileoutputcommitter.Algorithm.version a été défini à 2. Cela permet d’utiliser la version V2 de la FileOutputCommitter.</li></ul> | Service | Tous les | N/A |


## <a name="notes-for-09092015-release-of-hdinsight"></a>Notes de version 09/09/2015 de HDInsight

Les numéros de version complète des clusters HDInsight déployés dans cette version :

* HDInsight 2.1.10.675.1768697 (1.3.12.0-01795 HDP - inchangée)
* HDInsight 3.0.6.675.1768697 (2.0.13.0-2117 HDP - inchangée)
* HDInsight 3.1.4.675.1768697 (2.1.15.0-2334 HDP - inchangée)
* HDInsight 3.2.6.675.1768697 (2.2.6.1-0012 HDP - inchangée)
* KIT DE DÉVELOPPEMENT LOGICIEL 1.5.8

Cette version contient les mises à jour suivantes.

| Titre                                           | Description                                          | Zone d’impactés (par exemple, Service, composant ou SDK) | Cluster de Type (par exemple, Hadoop, HBase ou tempête) | JIRA (si applicable) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Versions HDInsight mises à jour pour tous les clusters HDInsight | Avec cette version, les versions HDInsight ont été mis à jour | Service    | Tous les| N/A                  |

## <a name="notes-for-07312015-release-of-hdinsight"></a>Notes de version de 31/07/2015 de HDInsight

Les numéros de version complète des clusters HDInsight déployés dans cette version :

* HDInsight 2.1.10.640.1695824 (1.3.12.0-01795 HDP - inchangée)
* HDInsight 3.0.6.640.1695824 (2.0.13.0-2117 HDP - inchangée)
* HDInsight 3.1.4.640.1695824 (2.1.15.0-2334 HDP - inchangée)
* HDInsight 3.2.6.640.1695824 (2.2.6.1-0012 HDP - inchangée)
* KIT DE DÉVELOPPEMENT LOGICIEL 1.5.8

Cette version contient les mises à jour suivantes.

| Titre                                           | Description                                          | Zone d’impactés (par exemple, Service, composant ou SDK) | Cluster de Type (par exemple, Hadoop, HBase ou tempête) | JIRA (si applicable) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Corriger étincelle workflow redéfinition d’image de nœud de cluster | Corrige un bogue qui a été à l’origine allumage pas récupérer après l’image, les nœuds de cluster | Service    | Allumage| N/A                  |


## <a name="notes-for-07312015-release-of-hdinsight"></a>Notes de version de 31/07/2015 de HDInsight

Les numéros de version complète des clusters HDInsight déployés dans cette version :

* HDInsight 2.1.10.635.1684502 (1.3.12.0-01795 HDP - inchangée)
* HDInsight 3.0.6.635.1684502 (2.0.13.0-2117 HDP - inchangée)
* HDInsight 3.1.4.635.1684502 (2.1.15.0-2334 HDP - inchangée)
* HDInsight 3.2.6.635.1684502 (2.2.6.1-0012 HDP - inchangée)
* KIT DE DÉVELOPPEMENT LOGICIEL 1.5.8

Cette version contient les mises à jour suivantes.

| Titre                                           | Description                                          | Zone d’impactés (par exemple, Service, composant ou SDK) | Cluster de Type (par exemple, Hadoop, HBase ou tempête) | JIRA (si applicable) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Versions HDInsight mises à jour pour tous les clusters HDInsight | Avec cette version, les versions HDInsight ont été mis à jour | Service    | Tous les| N/A                  |


## <a name="notes-for-07072015-release-of-hdinsight"></a>Notes de version de 07/07/2015 de HDInsight

Les numéros de version complète des clusters HDInsight déployés dans cette version :

* HDInsight 2.1.10.610.1630216 (1.3.12.0-01795 HDP - inchangée)
* HDInsight 3.0.6.610.1630216 (2.0.13.0-2117 HDP - inchangée)
* HDInsight 3.1.4.610.1630216 (2.1.15.0-2334 HDP - inchangée)
* HDInsight 3.2.4.610.1630216 (2.2.6.1-0012 HDP)
* KIT DE DÉVELOPPEMENT LOGICIEL 1.5.8


Cette version contient les mises à jour suivantes.

| Titre                                           | Description                                          | Zone d’impactés (par exemple, Service, composant ou SDK) | Cluster de Type (par exemple, Hadoop, HBase ou tempête) | JIRA (si applicable) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Versions HDP mises à jour pour les clusters de HDInsight 3.2 | Cette version 3.2 de HDInsight déploie HDP 2.2.6.1-0012 | Service    | Tous les                                                 | N/A                  |


## <a name="notes-for-06262015-release-of-hdinsight"></a>Notes de version 26/06/2015 de HDInsight

Les numéros de version complète des clusters HDInsight déployés dans cette version :

* HDInsight 2.1.10.601.1610731 (1.3.12.0-01795 HDP - inchangée)
* HDInsight 3.0.6.601.1610731 (2.0.13.0-2117 HDP - inchangée)
* HDInsight 3.1.4.601.1610731 (2.1.15.0-2334 HDP - inchangée)
* HDInsight 3.2.4.601.1610731 (2.2.6.1-0011 HDP)
* KIT DE DÉVELOPPEMENT LOGICIEL 1.5.8


Cette version contient les mises à jour suivantes.

<table border="1">
<tr>
<th>Titre</th>
<th>Description</th>
<th>Zone d’impactés (par exemple, Service, composant ou SDK)</p></th>
<th>Cluster de Type (par exemple, Hadoop, HBase ou tempête)</th>
<th>JIRA (si applicable)</th>
</tr>


<tr>
<td>Versions HDP mises à jour pour les clusters de HDInsight 3.2</td>
<td>Cette version 3.2 de HDInsight déploie HDP points 2.2.6.1</td>
<td>Service</td>
<td>Tous les</td>
<td>N/A</td>
</tr>

</table>

## <a name="notes-for-06182015-release-of-hdinsight"></a>Notes de version de 18/06/2015 de HDInsight

Les numéros de version complète des clusters HDInsight déployés dans cette version :

* HDInsight 2.1.10.596.1601657 (1.3.12.0-01795 HDP - inchangée)
* HDInsight 3.0.6.596.1601657 (2.0.13.0-2117 HDP - inchangée)
* HDInsight 3.1.4.596.1601657 (2.1.15.0-2334 HDP)
* HDInsight 3.2.4.596.1601657 (2.2.6.1-0002 HDP)
* KIT DE DÉVELOPPEMENT LOGICIEL 1.5.8


Cette version contient les mises à jour suivantes.

<table border="1">
<tr>
<th>Titre</th>
<th>Description</th>
<th>Zone d’impactés (par exemple, Service, composant ou SDK)</p></th>
<th>Cluster de Type (par exemple, Hadoop, HBase ou tempête)</th>
<th>JIRA (si applicable)</th>
</tr>


<tr>
<td>Ports supplémentaires HTTPS ouverts</td>
<td>Le service en nuage ouvre désormais 5 ports 8001 à 8005 sur le cluster, par exemple https://<clustername>.azurehdinsight.net:8001/. Demandes à ces URL sont authentifiés à l’aide du même mécanisme de mot de passe de l’authentification de base en tant que port 443. Ces ports se lier au même port sur la headnode active. Actions de script peuvent être utilisées pour rendre les services client écoute sur ces ports sur les headnode et un itinéraire vers l’extérieur du cluster.</td>
<td>Service de cloud</td>
<td>Tous les</td>
<td>N/A</td>
</tr>

<tr>
<td>Problème de lecture aléatoire MapReduce intermittent pour HDInsight 3.2</td>
<td>Correction d’une condition de concurrence rares, par intermittence dans mappage réduit de lecture aléatoire sur des clusters plus importants, ce qui entraîne des échecs de tâche occasionnelle. Pour plus d’informations, reportez-vous à la section <a href="https://issues.apache.org/jira/browse/MAPREDUCE-6361" target="_blank">MAPREDUCE-6361</a> .</td>
<td>Hadoop Core</td>
<td>Tous les</td>
<td><a href="https://issues.apache.org/jira/browse/MAPREDUCE-6361" target="_blank">MAPREDUCE-6361</a></td>
</tr>

<tr>
<td>Déplacer vers la dernière version Java Azure SDK 2.2 pour HDInsight 3.2</td>
<td>Déplacée vers la version la plus récente du Kit de développement Azure pour Java utilisée par le pilote WASB. La dernière version du SDK a quelques correctifs et les notes de publication pour le même sont disponibles à l’adresse https://github.com/Azure/azure-storage-java/blob/master/ChangeLog.txt.</td>
<td>Hadoop Core</td>
<td>Tous les</td>
<td><a href="https://issues.apache.org/jira/browse/HADOOP-11959" target="_blank">HADOOP-11959</a></td>
</tr>

<tr>
<td>Déplacer vers HDP 2.1.15 pour les clusters de HDInsight 3.1</td>
<td>Notes de publication de Hortonworks pour la mise à jour sont disponibles <a href="http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.15-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.15.html" target="_blank">ici</a>.</td>
<td>HDP</td>
<td>Tous les</td>
<td>N/A</td>
</tr>

</table>

## <a name="notes-for-06042015-release-of-hdinsight"></a>Notes de version du 06/04/2015 de HDInsight

Les numéros de version complète des clusters HDInsight déployés dans cette version :

* HDInsight 2.1.10.583.1575584 (1.3.12.0-01795 HDP - inchangée)
* HDInsight 3.0.6.583.1575584 (2.0.13.0-2117 HDP - inchangée)
* HDInsight 3.1.3.583.1575584 (2.1.12.1-0003 HDP - inchangée)
* HDInsight 3.2.4.583.1575584 (2.2.6.1-1 HDP)
* KIT DE DÉVELOPPEMENT LOGICIEL 1.5.8


Cette version contient les mises à jour suivantes.

<table border="1">
<tr>
<th>Titre</th>
<th>Description</th>
<th>Zone d’impactés (par exemple, Service, composant ou SDK)</p></th>
<th>Cluster de Type (par exemple, Hadoop, HBase ou tempête)</th>
<th>JIRA (si applicable)</th>
</tr>


<tr>
<td>Résoudre pour 502 Erreur de passerelle incorrecte pour les clusters de tempête</td>
<td>Cette version résout un bogue affectant la API qui a provoqué le site Web est inactif après un redémarrage de la soumission de travaux.</td>
<td>Service</td>
<td>Orage</td>
<td>N/A</td>
</tr>

</table>

## <a name="notes-for-06012015-release-of-hdinsight"></a>Notes de version 06/01/2015 de HDInsight

Les numéros de version complète des clusters HDInsight déployés dans cette version :

* HDInsight 2.1.10.577.1563827 (1.3.12.0-01795 HDP - inchangée)
* HDInsight 3.0.6.577.1563827 (2.0.13.0-2117 HDP - inchangée)
* HDInsight 3.1.3.577.1563827 (2.1.12.1-0003 HDP - inchangé))
* HDInsight 3.2.4.577.1563827 (2.2.6.0-2800 HDP - inchangée)
* KIT DE DÉVELOPPEMENT LOGICIEL 1.5.8


Cette version contient les mises à jour suivantes.

<table border="1">
<tr>
<th>Titre</th>
<th>Description</th>
<th>Zone d’impactés (par exemple, Service, composant ou SDK)</p></th>
<th>Cluster de Type (par exemple, Hadoop, HBase ou tempête)</th>
<th>JIRA (si applicable)</th>
</tr>


<tr>
<td>Divers correctifs de bogues</td>
<td>Cette version résout les bogues liés à la mise en service de cluster.</td>
<td>Service</td>
<td>Tous les types de cluster</td>
<td>N/A</td>
</tr>

</table>

## <a name="notes-for-05272015-release-of-hdinsight"></a>Notes de version de 27/05/2015 de HDInsight

Les numéros de version complète des clusters HDInsight déployés dans cette version :

* HDInsight 3.2.4.570.1554102 (2.2.6.0-2800 HDP)
* Autres versions de cluster et le Kit de développement ne sont pas déployés dans le cadre de cette version.


Cette version contient les mises à jour suivantes.

<table border="1">
<tr>
<th>Titre</th>
<th>Description</th>
<th>Zone d’impactés (par exemple, Service, composant ou SDK)</p></th>
<th>Cluster de Type (par exemple, Hadoop, HBase ou tempête)</th>
<th>JIRA (si applicable)</th>
</tr>


<tr>
<td>Mise à jour de HDP 2.2</td>
<td>Cette version de HDInsight 3.2 contient HDP 2.2.6 et apporte plusieurs correctifs de bogues importants à HDInsight. Les notes de version complète est disponible à l’adresse <a href="http://dev.hortonworks.com.s3.amazonaws.com/HDPDocuments/HDP2/HDP-2.2.6/HDP_RelNotes_v226/index.html">HDP 2.2.6 Notes de version</a>.</td>
<td>HDP</td>
<td>Tous les types de cluster</td>
<td>N/A</td>
</tr>

<tr>
<td>Modifier la configuration de mémoire du conteneur par défaut fils</td>
<td>Dans cette mise à jour, la mémoire disponible de par défaut aux conteneurs de fils (yarn.nodemanager.resource.memory-Mo et allocation de yarn.scheduler.maximum-Mo), lancée par le Gestionnaire de nœud, est augmentée à 5632 Mo. Précédemment a été réduit à 4608 Mo, ce en fonction de différentes séries de travail, la nouvelle valeur doit offrir une meilleure fiabilité et des performances pour la plupart des tâches, est donc plus par défaut. Comme si d’habitude vous une dépendance critique sur cette configuration de mémoire, veuillez le définir explicitement lors de la création du cluster.</td>
<td>HDP</td>
<td>Tous les types de cluster</td>
<td>N/A</td>
</tr>

<tr>
<td>Parité de configuration par défaut pour les clusters de HBase et Storm</td>
<td>Cette mise à jour restaure les clusters Hbase et Storm pour utiliser les mêmes valeurs de fils configurations en clusters d’Hadoop. Pour ce faire de parité sur tous les types de cluster.</td>
<td>HDP</td>
<td>HBase, tempête</td>
<td>N/A</td>
</tr>

</table>

## <a name="notes-for-05202015-release-of-hdinsight"></a>Notes de version de 20/05/2015 de HDInsight

Les numéros de version complète des clusters HDInsight déployés dans cette version :

* HDInsight 2.1.10.564.1542093 (1.3.12.0-01795 HDP - inchangée)
* HDInsight 3.0.6.564.1542093 (2.0.13.0-2117 HDP - inchangée)
* HDInsight 3.1.3.564.1542093 (2.1.12.1-0003 HDP)
* HDInsight 3.2.4.564.1542093 (2.2.4.6-2 HDP)
* KIT DE DÉVELOPPEMENT LOGICIEL 1.5.8

Cette version contient les mises à jour suivantes.

<table border="1">
<tr>
<th>Titre</th>
<th>Description</th>
<th>Zone d’impactés (par exemple, Service, composant ou SDK)</p></th>
<th>Cluster de Type (par exemple, Hadoop, HBase ou tempête)</th>
<th>JIRA (si applicable)</th>
</tr>


<tr>
<td>Prise en charge de la EventHub SCP.NET</td>
<td>Les packages de mise à jour de cluster pour HDInsight tempête permettent de nouvelles fonctionnalités SCP.NET. Vous avez maintenant accès aux nouvelles API de générateur de topologie qui le rendent plus facile à utiliser EventHubSpout ou Java becs verseurs amovibles. Vous devez mettre à jour votre client SCP.NET SDK pour travailler avec les nouveaux clusters comme les contrats ont été mis à jour. Pour plus d’informations sur les nouvelles API, utilisation et release notes (correctifs de bugs, y compris), veuillez consulter le fichier Readme inclus dans le package nuget SCP.NET.</td>
<td>Outillage de VS</td>
<td>Tempête HDInsight 3,2 clusters</td>
<td>N/A</td>
</tr>

<tr>
<td>Mise à jour du pilote JDBC</td>
<td>Le pilote mis à jour à la version de SQL Server prises en charge dans sqljdbc_4.1.5605.100.</td>
<td>Metastore</td>
<td>Tous les</td>
<td>N/A</td>
</tr>
</table>

## <a name="notes-for-04272015-release-of-hdinsight"></a>Notes de version de 27/04/2015 de HDInsight

Les numéros de version complète des clusters HDInsight déployés dans cette version :

* HDInsight 2.1.10.537.1486660 (1.3.12.0-01795 HDP - inchangée)
* HDInsight 3.0.6.537.1486660 (2.0.13.0-2117 HDP - inchangée)
* HDInsight 3.1.3.537.1486660 (2.1.12.0-2329 HDP - inchangée)
* HDInsight 3.2.3.537.1486660 (2.2.2.2-4 HDP)
* KIT DE DÉVELOPPEMENT LOGICIEL 1.5.8

Cette version contient les mises à jour suivantes.

<table border="1">
<tr>
<th>Titre</th>
<th>Description</th>
<th>Zone d’impactés (par exemple, Service, composant ou SDK)</p></th>
<th>Cluster de Type (par exemple, Hadoop, HBase ou tempête)</th>
<th>JIRA (si applicable)</th>
</tr>


<tr>
<td>Résoudre la dépendance de la DLL</td>
<td>Supprime la dépendance de HDInsight de l’infrastructure des tests unitaires.</td>
<td>KIT DE DÉVELOPPEMENT LOGICIEL</td>
<td>Hadoop</td>
<td>N/A</td>
</tr>

<tr>
<td>Résolution de bogue pour une condition de concurrence critique</td>
<td>Un cluster Créer demande maintenant attend sur PUT demande soit acceptée avant d’interrogation sur l’état</td>
<td>KIT DE DÉVELOPPEMENT LOGICIEL</td>
<td>Hadoop</td>
<td>N/A</td>
</tr>
</table>

## <a name="notes-for-04142015-release-of-hdinsight"></a>Notes de version de 14/04/2015 de HDInsight

Les numéros de version complète des clusters HDInsight déployés dans cette version :

* HDInsight 2.1.10.521.1453250 (1.3.12.0-01795 HDP - inchangée)
* HDInsight 3.0.6.521.1453250 (2.0.13.0-2117 HDP - inchangée)
* HDInsight 3.1.3.521.1453250 (2.1.12.0-2329 HDP - inchangée)
* HDInsight 3.2.3.525.1459730 (2.2.2.2-2 HDP)
* KIT DE DÉVELOPPEMENT LOGICIEL 1.5.6

Cette version contient les mises à jour suivantes.

<table border="1">
<tr>
<th>Titre</th>
<th>Description</th>
<th>Zone d’impactés (par exemple, Service, composant ou SDK)</p></th>
<th>Cluster de Type (par exemple, Hadoop, HBase ou tempête)</th>
<th>JIRA (si applicable)</th>
</tr>


<tr>
<td>Correctifs de bogues tez</td>
<td>Correctifs pour Apache TEZ 2214 et TEZ 1923 sont inclus dans cette version de HDI 3.2. Ceux-ci sont spécifiquement nécessaires pour certaines requêtes de ruche sur Tez qui nécessitent une quantité importante de données de réorganisation.
</td>
<td>HDP</td>
<td>Hadoop</td>
<td><a href="https://issues.apache.org/jira/browse/TEZ-2214">TEZ 2214</a></br><a href="https://issues.apache.org/jira/browse/TEZ-1923">TEZ 1923</a></td>
</tr>
</table>

## <a name="notes-for-04062015-release-of-hdinsight"></a>Notes de version de 04/06/2015 de HDInsight

Les numéros de version complète des clusters HDInsight déployés dans cette version :

* HDInsight 2.1.10.521.1453250 (1.3.12.0-01795 HDP - inchangée)
* HDInsight 3.0.6.521.1453250 (2.0.13.0-2117 HDP - inchangée)
* HDInsight 3.1.3.521.1453250 (2.1.12.0-2329 HDP - inchangée)
* HDInsight 3.2.3.521.1453250 (2.2.2.2-1 HDP)
* KIT DE DÉVELOPPEMENT LOGICIEL 1.5.6

Cette version contient les mises à jour suivantes.

<table border="1">
<tr>
<th>Titre</th>
<th>Description</th>
<th>Zone d’impactés (par exemple, Service, composant ou SDK)</p></th>
<th>Cluster de Type (par exemple, Hadoop, HBase ou tempête)</th>
<th>JIRA (si applicable)</th>
</tr>


<tr>
<td>Kit de développement .NET HDInsight 1.5.6</td>
<td>Mises à jour pour supprimer des classes internes d’HDInsight sous Linux.</td>
<td>KIT DE DÉVELOPPEMENT LOGICIEL</td>
<td>Hadoop</td>
<td>N/A</td>
</tr>

<tr>
<td>Bibliothèque de Avro 1.5.6</td>
<td><b>KnownTypeAttribute</b> ajoutés pour la méthode <b>GetAllKnownTypes</b>. Fixe l’exception NullReferenceException lorsqu’un type est null pour GetAllKnownTypes méthode.</td>
<td>KIT DE DÉVELOPPEMENT LOGICIEL</td>
<td>Hadoop</td>
<td>N/A</td>
</tr>

<tr>
<td>Correctifs de bogues</td>
<td>Divers correctifs de bogues pour le service</td>
<td>Service</td>
<td>Tous les</td>
<td>N/A</td>
</tr>

</table>
<br>

## <a name="notes-for-04012015-release-of-hdinsight"></a>Notes de version du 04/01/2015 de HDInsight

Les numéros de version complète des clusters HDInsight déployés dans cette version :

* HDInsight 2.1.10.513.1431705 (1.3.12.0-01795 HDP)
* HDInsight 3.0.6.513.1431705 (2.0.13.0-2117 HDP)
* HDInsight 3.1.3.513.1431705 (2.1.12.0-2329 HDP)
* HDInsight 3.2.3.513.1431705 (2.2.2.1-2600 HDP)
* KIT DE DÉVELOPPEMENT LOGICIEL 1.5.5

Cette version contient les mises à jour suivantes.

<table border="1">
<tr>
<th>Titre</th>
<th>Description</th>
<th>Zone d’impactés (par exemple, Service, composant ou SDK)</p></th>
<th>Cluster de Type (par exemple, Hadoop, HBase ou tempête)</th>
<th>JIRA (si applicable)</th>
</tr>


<tr>
<td>Possibilité d’activer/désactiver les informations d’identification de bureau à distance sur les clusters Windows via le Kit de développement .NET</td>
<td>Assistance de programmation pour activer ou désactiver les informations d’identification RDP sur les clusters Windows.</td>
<td>KIT DE DÉVELOPPEMENT LOGICIEL</td>
<td>Tous les</td>
<td>N/A</td>
</tr>

<tr>
<td>Possibilité d’activer les informations d’identification de bureau à distance sur des clusters pendant qu’ils sont en cours de provision</td>
<td>Assistance de programmation pour activer des informations d’identification du Bureau à distance que le cluster est en cours de création. Cette opération supprime le processus en deux étapes pour la première mise en service de cluster et puis l’activation du Bureau à distance.</td>
<td>KIT DE DÉVELOPPEMENT LOGICIEL</td>
<td>Tous les</td>
<td>N/A</td>
</tr>

<tr>
<td>Python mis à niveau au point 2.7.8</td>
<td>Les Python mis à jour sur les Clusters de HDInsight pour les Python point 2.7.8, qui contient une sécurité importante corrige pour HDInsight les versions 2.1, 3.0, 3.1 et 3.2</td>
<td>Service</td>
<td>Tous les</td>
<td>N/A</td>
</tr>

<tr>
<td>Modification de la configuration fils</td>
<td>Modifié fils yarn.resourcemanager.max-terminé-applications de configuration à 1000 pour tous les types de cluster pour HDInsight les versions 3.1 et 3.2. Cette valeur contrôle uniquement la liste des applications terminées dans l’interface utilisateur de fils. Pour obtenir des informations sur les applications qui ont été envoyés avant la liste des applications affichée dans l’interface utilisateur, vous pouvez atteindre directement le serveur de l’historique.</td>
<td>FILS</td>
<td>Tous les</td>
<td>N/A</td>
</tr>

<tr>
<td>Redimensionnement des nœuds dans un cluster HBase</td>
<td>Les clusters de HBase permettent désormais de redimensionnement de nœuds (haut et bas) pour HDInsight les versions 3.1 et 3.2</td>
<td>Service</td>
<td>HBase</td>
<td>N/A</td>
</tr>

<tr>
<td>Mise à niveau JDBC</td>
<td>Pilote SQL JDBC est mis à niveau vers la version sqljdbc_4.0.2206.100 pour HDInsight la version 3.2. Cette version contient des améliorations importantes de la sécurité.</td>
<td>HDP</td>
<td>Tous les</td>
<td>N/A</td>
</tr>

<tr>
<td>Mise à jour de configuration JVM</td>
<td>Mise à jour JVM configuration networkaddress.cache.ttl à 300 secondes à partir de la valeur par défaut de -1 pour les HDInsight versions 3.1 et 3.2. Cette valeur contrôle la stratégie de mise en cache pour les recherches de nom réussie à partir du nom. Cela résout un bogue lié à la croissance et la réduction des clusters de HBase.</td>
<td>Service</td>
<td>HBase</td>
<td>N/A</td>
</tr>

<tr>
<td>Mise à niveau vers le stockage Azure SDK pour Java 2.0</td>
<td>HDInsight version 3.2 est mis à niveau pour utiliser la dernière version du SDK stockage Azure pour Java. Contient plusieurs correctifs de bogues importants sur la 0.6.0 actuelle version.</td>
<td>HDP</td>
<td>Tous les</td>
<td>N/A</td>
</tr>

<tr>
<td>Mise à niveau vers le code source de WASB d’Apache</td>
<td>HDInsight version 3.2 maintenant plus tard utilise du code pour le pilote de système de fichiers WASB Apache Hadoop. Avec cette modification, le pilote WASB est désormais fournie en un fichier jar séparé. Ceci est uniquement un changement d’emballage et ne contient-elle pas les modifications apportées au comportement du pilote WASB. Le nom de ce fichier JAR est hadoop-azure-2.6.0.jar.</td>
<td>HDP</td>
<td>Tous les</td>
<td>N/A</td>
</tr>

<tr>
<td>JAR mises à jour du nom de fichier HDInsight 3.2</td>
<td>Cette mise à jour HDInsight version 3.2 contient plusieurs correctifs de bogues et quelques bocaux interne empaquetés en tant que partie de HDP ont été mis à niveau. Notez que ces fichiers JAR sont internes au package HDP et pas pour une utilisation directe par les applications client. Applications doivent empaqueter leur propre version des fichiers JAR afin qu’une mise à niveau pour les fichiers JAR HDP internes ne pas casser les applications client.</td>
<td>HDP</td>
<td>Tous les</td>
<td>N/A</td>
</tr>

</table>
<br>

## <a name="notes-for-03032015-release-of-hdinsight"></a>Notes de version du 03/03/2015 de HDInsight

Les numéros de version complète des clusters HDInsight déployés dans cette version :

* HDInsight 2.1.10.488.1375841 (1.3.9.0-01351 HDP - inchangée)
* HDInsight 3.0.6.488.1375841 (2.0.9.0-2097 HDP - inchangée)
* HDInsight 3.1.3.488.1375841 (2.1.10.0-2290 HDP - inchangée)
* HDInsight 3.2.3.488.1375841 (HDP-2.2.10.0-2340 - inchangée)
* Kit de développement logiciel 1.5.0 (inchangée)

Cette version contient la mise à jour suivante.

<table border="1">
<tr>
<th>Titre</th>
<th>Description</th>
<th>Zone d’impactés (par exemple, service, composant ou SDK)</p></th>
<th>Cluster de Type (par exemple, Hadoop, HBase ou tempête)</th>
<th>JIRA</th>
</tr>


<tr>
<td>Améliorations de la fiabilité</td>
<td>Nous avons apporté des correctifs qui permettent au service mieux avec l’augmentation de la charge en ce qui concerne la création du cluster.</td>
<td>Service</td>
<td>Tous les</td>
<td>N/A</td>
</tr>



</table>
<br>

## <a name="notes-for-02182015-release-of-hdinsight"></a>Notes de version de 18/02/2015 de HDInsight

Les numéros de version complète des clusters HDInsight déployés dans cette version :

* HDInsight 2.1.10.471.1342507 (1.3.9.0-01351 HDP - inchangée)
* HDInsight 3.0.6.471.1342507 (2.0.9.0-2097 HDP - inchangée)
* HDInsight 3.1.3.471.1342507 (2.1.10.0-2290 HDP - inchangée)
* HDInsight 3.2.3.471.1342507 (HDP-2.2.10.0-2340)
* KIT DE DÉVELOPPEMENT LOGICIEL 1.5.0

Cette version contient les mises à jour suivantes.

<table border="1">
<tr>
<th>Titre</th>
<th>Description</th>
<th>Zone d’impactés (par exemple, Service, composant ou SDK)</p></th>
<th>Cluster de Type (par exemple, Hadoop, HBase ou tempête)</th>
<th>JIRA (si applicable)</th>
</tr>


<tr>
<td>Clusters HDInsight 3.2</td>
<td>Hadoop 2.6/HDP2.2 est disponible avec les clusters HDInsight 3.2. Il contient des mises à jour majeures pour tous les composants open source. Pour plus d’informations, consultez Nouveautés dans les HDInsight et les <a href ="http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.2.0/HDP_2.2.0_Release_Notes_20141202_version/index.html" target="_blank">notes de mise à jour de HDP 2.2.0.0</a>.</td>
<td>Logiciels Open source</td>
<td>Tous les</td>
<td>N/A</td>
</tr>

<tr>
<td>HDinsight sous Linux (aperçu)</td>
<td>Les clusters peuvent être déployées sous Ubuntu Linux. Pour plus d’informations, consultez Mise en route de HDInsight sous Linux.</td>
<td>Service</td>
<td>Hadoop</td>
<td>N/A</td>
</tr>

<tr>
<td>Disponibilité générale de tempête</td>
<td>Tempête de Apache clusters sont généralement disponibles. Pour plus d’informations, voir mise en route à l’aide de la tempête dans HDInsight.</td>
<td>Service</td>
<td>Orage</td>
<td>N/A</td>
</tr>

<tr>
<td>Tailles de machine virtuelle</td>
<td>HDInsight Azure est disponible dans plusieurs tailles et types de machine virtuelle. HDInsight pouvez utiliser A2 aux tailles A7 créés à des fins générales ; Nœuds de série D équipés de disques SSD (SSD) et 60 pour cent des processeurs plus rapides ; et prend en charge les tailles de A8 et A9 ont InfiniBand pour la mise en réseau rapide.</td>
<td>Service</td>
<td>Tous les</td>
<td>N/A</td>
</tr>

<tr>
<td>Mise à l’échelle du cluster</td>
<td>Vous pouvez modifier le nombre de nœuds de données pour un cluster de HDInsight en cours d’exécution sans avoir à supprimer ou de le recréer. Actuellement, seuls les types de cluster Hadoop requête et Apache tempête ont cette capacité, mais la prise en charge de HBase Apache type de cluster est bientôt à suivre. Pour plus d’informations, reportez-vous à la section HDInsight de gérer les clusters.</td>
<td>Service</td>
<td>Hadoop, tempête</td>
<td>N/A</td>
</tr>

<tr>
<td>Outils de Visual Studio</td>
<td>En plus d’outils complète pour Apache tempête, les outils d’Apache ruche dans Visual Studio a été mis à jour pour inclure la saisie semi-automatique des instructions, la validation locale et prise en charge du débogage améliorée. Pour plus d’informations, consultez Mise en route avec les outils d’Hadoop HDInsight pour Visual Studio.</td>
<td>Outillage</td>
<td>Hadoop</td>
<td>N/A</td>
</tr>

<tr>
<td>Connecteur d’Hadoop pour DocumentDB</td>
<td>Avec le connecteur de Hadoop pour DocumentDB, vous pouvez effectuer des agrégations complexes, d’analyse et de manipulations sur vos documents JSON sans schéma stockés dans les collections de DocumentDB ou des comptes de la base de données. Pour plus d’informations et un didacticiel, reportez-vous à la section Hadoop d’exécuter les travaux à l’aide de DocumentDB et HDInsight.</td>
<td>Service</td>
<td>Hadoop</td>
<td>N/A</td>
</tr>

<tr>
<td>Correctifs de bogues</td>
<td>Nous avons plusieurs correctifs mineurs pour les services de HDInsight. Aucune modification du comportement des clients n’est attendues.</td>
<td>Service</td>
<td>Tous les</td>
<td>N/A</td>
</tr>

</table>
<br>

## <a name="notes-for-02062015-release-of-hdinsight"></a>Notes de version de 02/06/2015 de HDInsight

Les numéros de version complète des clusters HDInsight déployés dans cette version :

* HDInsight 2.1.10.463.1325367 (1.3.9.0-01351 HDP - inchangée)
* HDInsight 3.0.6.463.1325367 (2.0.9.0-2097 HDP - inchangée)
* HDInsight 3.1.2.463.1325367 (2.1.10.0-2290 HDP)
* N/A DU KIT DE DÉVELOPPEMENT LOGICIEL

Cette version contient les mises à jour suivantes.

<table border="1">
<tr>
<th>Titre</th>
<th>Description</th>
<th>Zone d’impactés (par exemple, Service, composant ou SDK)</p></th>
<th>Cluster de Type (par exemple, Hadoop, HBase ou tempête)</th>
<th>JIRA (si applicable)</th>
</tr>


<tr>
<td>Correctifs de bogues</td>
<td>Nous avons plusieurs correctifs mineurs pour les services de HDInsight. Aucune modification du comportement des clients n’est attendues.</td>
<td>Service</td>
<td>Tous les</td>
<td>N/A</td>
</tr>

<tr>
<td>Mise à jour de maintenance HDP 2.1</td>
<td>HDInsight 3.1 est mis à jour pour déployer HDP 2.1.10.0. Pour plus d’informations, consultez <a href ="http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.10/bk_releasenotes_hdp_2.1/content/ch_relnotes-HDP-2.1.10.html" target="_blank">les Notes de version HDP-2.1.10</a>. </td>
<td>Logiciels Open source</td>
<td>Tous les</td>
<td>N/A</td>
</tr>

<tr>
<td>Mises à jour binaires HDP</td>
<td>Il existe quelques fichiers JAR dans HBase pour le fichier de noms ont été mis à jour. Ces fichiers JAR sont utilisés en interne par HBase, et il est peu probable que les clients ont une dépendance sur les noms de ces fichiers JAR. Celles-ci comprennent :
<ul>
<li>./lib/jetty-6.1.26.hwx.jar</li>
<li>./lib/jetty-sslengine-6.1.26.hwx.jar</li>
<li>./lib/jetty-util-6.1.26.hwx.jar</li>
</ul>
</td>
<td>Logiciels Open source</td>
<td>HBase</td>
<td>N/A</td>
</tr>

</table>
<br>

## <a name="notes-for-1292015-release-of-hdinsight"></a>Notes de version de 1/29/2015 de HDInsight

Les numéros de version complète des clusters HDInsight déployés dans cette version :

* HDInsight 2.1.10.455.1309616 (1.3.9.0-01351 HDP - inchangée)
* HDInsight 3.0.6.455.1309616 (2.0.9.0-2097 HDP - inchangée)
* HDInsight 3.1.2.455.1309616 (2.1.9.0-2196 HDP - inchangée)
* N/A DU KIT DE DÉVELOPPEMENT LOGICIEL

Cette version contient la mise à jour suivante.

<table border="1">
<tr>
<th>Titre</th>
<th>Description</th>
<th>Zone d’impactés (par exemple, Service, composant ou SDK)</p></th>
<th>Cluster de Type (par exemple, Hadoop, HBase ou tempête)</th>
<th>JIRA (si applicable)</th>
</tr>


<tr>

<td>Correctifs de bogues</td>
<td>Nous avons apporté quelques correctifs de bogues importants qui améliorent la fiabilité des Clusters HDInsight au cours des mises à niveau Azure.</td>
<td>Service</td>
<td>Tous les</td>
<td>N/A</td>
</tr>



</table>
<br>

## <a name="notes-for-152015-release-of-hdinsight"></a>Notes de version de 1/5/2015 de HDInsight

Les numéros de version complète des clusters HDInsight déployés dans cette version :

* HDInsight 2.1.10.420.1246118 (1.3.9.0-01351 HDP - inchangée)
* HDInsight 3.0.6.420.1246118 (2.0.9.0-2097 HDP - inchangée)
* HDInsight 3.1.2.420.1246118 (2.1.9.0-2196 HDP - inchangée)


Cette version contient les mises à jour suivantes.

<table border="1">
<tr>
<th>Titre</th>
<th>Description</th>
<th>Composant</th>
<th>Type de cluster</th>
<th>JIRA (si applicable)</th>
</tr>


<tr>
<td>Exemples d’analyse des tendances Twitter et les recommandations basées sur les Mahout de film</td>
<td><p>Dans cette version, la console de la requête de HDInsight a deux échantillons supplémentaires :</p>

<p><b>Analyse des tendances Twitter</b><br>
Les API publiques fournies par des sites tels que Twitter sont une source utile de données d’analyse et de présentation de tendances les plus courants. Dans ce didacticiel, apprenez à utiliser la ruche pour obtenir une liste d’utilisateurs Twitter qui a envoyé le plus tweet contenant un mot particulier. </p>

<p><b>Recommandation de film de mahout</b><br>
Apache Mahout est une machine Apache Hadoop apprentissage. Mahout contient les algorithmes de traitement de données (par exemple, le filtrage, la classification et gestion de clusters). Dans ce didacticiel, utilisez un moteur de recommandation pour générer des recommandations de film basées sur les films que vos amis ont vu.</p></td>
<td>Console de requête</td>
<td>Hadoop</td>
<td>N/A</td>
</tr>

<tr>
<td>Modifier la valeur par défaut de la configuration de la ruche : hive.auto.convert.join.noconditionaltask.size</td>
<td><p>Cette configuration de taille s’applique aux jointures de mappage automatiquement converti. La valeur représente la somme des tailles de tables qui peuvent être converties en plans de hachage qui tiennent dans la mémoire. Dans une version antérieure, cette valeur augmentée de la valeur par défaut de 10 Mo à 128 Mo. Toutefois, la nouvelle valeur de 128 Mo a été à l’origine de travaux Échec dû à une insuffisance de mémoire. Cette version rétablit la valeur par défaut à 10 Mo. Les clients peuvent toujours choisir de substituer cette valeur lors de la création du cluster, étant donné leurs tailles de tables et de requêtes. Pour plus d’informations sur ce paramètre et comment la substituer, voir <a href="http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.0.2/ds_Hive/optimize-joins.html#JoinOptimization-OptimizeAutoJoinConversion" target="_blank">Optimiser la Conversion de jointure automatique</a> dans la documentation de Hortonworks. </p></td>
<td>La ruche</td>
<td>Hadoop, Hbase</td>
<td>N/A</td>
</tr>

</table>
<br>

## <a name="notes-for-12232014-release-of-hdinsight"></a>Notes de version de 23/12/2014 de HDInsight

Les numéros de version complète des clusters HDInsight déployés dans cette version sont les suivantes :

* HDInsight 2.1.10.420.1246783 (version HDP inchangée)
* HDInsight 3.0.6.420.1246783 (version HDP inchangée)
* HDInsight 3.1.1.420.1246783 (version HDP inchangée)

Cette version contient la mise à jour suivante.

<table border="1">
<tr>
<th>Titre</th>
<th>Description</th>
<th>Composant</th>
<th>Type de cluster</th>
<th>JIRA (si applicable)</th>
</tr>


<tr>
<td>Échecs de création de cluster intermittente en raison d’une charge excessive</td>
<td><p>Un algorithme amélioré pour le téléchargement des packages HDP lors de la création du cluster permet un traitement plus robuste des défaillances en raison d’une charge excessive.</p></td>
<td>Service</td>
<td>Hadoop, Hbase, tempête</td>
<td>N/A</td>
</tr>



</table>
<br>

## <a name="notes-for-12182014-release-of-hdinsight"></a>Notes de version de 18/12/2014 de HDInsight

Cette version contient la mise à jour du composant suivant.

<table border="1">
<tr>
<th>Titre</th>
<th>Description</th>
<th>Composant</th>
<th>Type de cluster</th>
<th>JIRA (si applicable)</th>
</tr>

<tr>
<td><a href = "hdinsight-hadoop-customize-cluster.md" target="_blank">Personnalisation de cluster général Avalability</a></td>
<td><p>Personnalisation permet de vous permettant de personnaliser vos clusters Azure HDInsight avec les projets qui sont disponibles à partir de l’écosystème Apache Hadoop. Avec cette nouvelle fonctionnalité, vous pouvez tester et déployer des projets d’Hadoop sur Azure HDInsight. Cette option est activée par le biais de la fonctionnalité de **Script Action** , ce qui peut modifier des clusters d’Hadoop de manière arbitraire à l’aide de scripts personnalisés. Cette personnalisation est disponible sur tous les types de clusters HDInsight notamment Hadoop, HBase et Storm. Pour illustrer la puissance de cette fonctionnalité, nous avons documentées la procédure pour installer populaire <a href = "hdinsight-hadoop-spark-install.md" target="_blank">étincelle</a> <a href = "hdinsight-hadoop-r-scripts.md" target="_blank">R</a>, <a href = "hdinsight-hadoop-solr-install.md" target="_blank">mode série sur LAN.r</a>et modules de <a href = "hdinsight-hadoop-giraph-install.md" target="_blank">Giraph</a> . Cette version ajoute également de la possibilité pour les clients de spécifier leur action de script personnalisée via le portail Azure, fournit des instructions et recommandations sur la façon de créer des actions de script personnalisé à l’aide de méthodes d’assistance et fournit des instructions sur le test de l’action du script. </p></td>
<td>Disponibilité de la fonctionnalité générale</td>
<td>Tous les</td>
<td>N/A</td>
</tr>


</table>
<br>

## <a name="notes-for-12052014-release-of-hdinsight"></a>Notes de version 12/05/2014 de HDInsight

Les numéros de version complète des clusters HDInsight déployés dans cette version sont les suivantes :

* HDInsight 2.1.9.406.1221105 (1.3.9.0-01351 HDP)
* HDInsight 3.0.5.406.1221105 (2.0.9.0-2097 HDP)
* HDInsight 3.1.1.406.1221105 (2.1.9.0-2196 HDP)
* Kit de développement logiciel HDInsight n/a

Cette version contient les mises à jour du composant suivant.

<table border="1">
<tr>
<th>Titre</th>
<th>Description</th>
<th>Composant</th>
<th>Type de cluster</th>
<th>JIRA (si applicable)</th>
</tr>

<tr>
<td>Correctif : une erreur intermittente lors de l’ajout d’un nombre important de partitions à une table dans un DDL de la ruche </td>
<td><p>S’il existe une erreur de connexion intermittents avec la base de données de ruche metastore lors de l’ajout d’un grand nombre de partitions à une table de la ruche, le script DDL de la ruche peut échouer. L’instruction suivante s’affiche dans le journal des erreurs de ruche si cet échec se produit : </p><p>« Erreur [main] : ql. Pilote (SessionState.java:printError(547)) - a échoué : erreur d’exécution, code de retour 1 de org.apache.hadoop.hive.ql.exec.DDLTask. MetaException (message:java.lang.RuntimeException : commitTransaction a été appelée mais openTransactionCalls = 0. Ceci indique probablement qu’il y a des appels non soldés à openTransaction/commitTransaction) »</p></td>
<td>La ruche</td>
<td>Hadoop, Hbase</td>
<td>RUCHE-482 (il s’agit d’un JIRA interne, afin qu’il ne peut pas être mis entre guillemets en externe. Noter ici à titre de référence.)</td>
</tr>

<tr>
<td>Correctif de bogue : blocage occasionnel dans la Console de requête HDInsight</td>
<td>Lorsque cela se produit, l’instruction suivante peut s’afficher dans le journal des WebHCat pour le travail du Lanceur WebHCat : <p>« org.apache.hive.hcatalog.templeton.CatchallExceptionMapper | org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.yarn.exceptions.YarnRuntimeException) : pas pu charger le fichier historique {wasb de l’url pour le fichier d’historique} »</p></td>
<td>WebHCat</td>
<td>Hadoop</td>
<td>RUCHE-482 (il s’agit d’un JIRA interne, afin qu’il ne peut pas être mis entre guillemets en externe. Noter ici à titre de référence.)</td>
</tr>

<tr>
<td>Correctif de bogue : pointe occasionnel de latence des requêtes de Hbase</td>
<td>Dans ce cas, les utilisateurs remarqueront un pic occasionnel de 3 secondes dans la latence des requêtes de Hbase. </td>
<td>Passerelle de Cluster de HDInsight</td>
<td>HBase</td>
<td>N/A</td>
</tr>

<tr>
<td>Modification du nom de fichier JAR de HDP</td>
<td>Pour HDI cluster version 3.0, il y quelques modifications aux fichiers JAR installés par HDP internes. jetée-6.1.26.jar a été remplacé par 6.1.26.hwx.jar de la jetée. jetée-util-6.1.26.jar a été remplacé par jetée-util-6.1.26.hwx.jar. Ces modifications s’appliquent aux projets de Hadoop, Mahout, WebHCat et Oozie.</td>
<td>Hadoop, Mahout, WebHCat, Oozie</td>
<td>Hadoop, HBase</td>
<td>N/A</td>
</tr>

</table>
<br>


## <a name="notes-for-11212014-release-of-hdinsight"></a>Notes de version 11/21/2014 de HDInsight

Les numéros de version complète des clusters HDInsight déployés dans cette version sont les suivantes :

* HDInsight 2.1.9.382.1169709 (aucune modification de 14/11/2014)
* HDInsight 3.0.5.382.1169709 (aucune modification de 14/11/2014)
* HDInsight 3.1.1.382.1169709 (aucune modification de 14/11/2014)
* Kit de développement logiciel HDINsight 1.4.0

Cette version contient les mises à jour du composant suivant.

<table border="1">
<tr>
<th>Titre</th>
<th>Description</th>
<th>Composant</th>
<th>Type de cluster</th>
<th>JIRA (si applicable)</th>
</tr>

<tr>
<td>L’accès aux journaux d’application</td>
<td>Possibilité pour énumérer par programme des applications qui ont été exécutées sur vos clusters et de télécharger les journaux spécifiques à l’application ou spécifique au conteneur appropriés pour déboguer des applications problématiques.</td>
<td>KIT DE DÉVELOPPEMENT LOGICIEL</td>
<td>Hadoop</td>
<td>N/A</td>
</tr>

<tr>
<td>Permet de spécifier le nom de la région de IHdInsightClient.DeleteCluster </td>
<td>Le Kit de développement logiciel HDInsight Azure fournit la possibilité de spécifier un nom de région lors de l’utilisation de **DeleteCluster**. Cela vous permet de débloquer les clients qui avaient deux personnes avec le même nom dans différentes régions et n’avaient pas pu supprimer une d’elles.</td>
<td>KIT DE DÉVELOPPEMENT LOGICIEL</td>
<td>Tous les</td>
<td>N/A</td>
</tr>

<tr>
<td>ClusterDetails.DeploymentId</td>
<td>L’objet **ClusterDetails** renvoie un champ de **DeploymentID** qui représente un identificateur unique pour le cluster. Elle est garantie pour être unique pour les tentatives de création de cluster portant le même nom.</td>
<td>KIT DE DÉVELOPPEMENT LOGICIEL</td>
<td>Tous les</td>
<td>N/A</td>
</tr>
</table>
<br>

## <a name="notes-for-11142014-release-of-hdinsight"></a>Notes de version 11/14/2014 de HDInsight

Les numéros de version complète des clusters HDInsight déployés dans cette version sont les suivantes :

* HDInsight 2.1.9.382.1169709
* HDInsight 3.0.5.382.1169709
* HDInsight 3.1.1.382.1169709

Cette version contient les nouvelles fonctionnalités suivantes, mises à jour de composant et résolutions de bogues.

<table border="1">
<tr>
<th>Titre</th>
<th>Description</th>
<th>Composant</th>
<th>Type de cluster</th>
<th>JIRA (si applicable)</th>
</tr>

<tr>
<td>Action de script (aperçu)</td>
<td>Aperçu de la fonction de personnalisation de cluster qui permet la modification d’Hadoop clusters de manière arbitraire à l’aide de scripts personnalisés. Grâce à cette fonctionnalité, les utilisateurs peuvent expérimenter et déployer des projets qui sont disponibles à partir de l’écosystème Apache Hadoop pour Azure HDInsight clusters. Cette fonction de personnalisation est disponible sur tous les types de clusters HDInsight, y compris Hadoop, HBase et Storm.</td>
<td>Nouvelle fonctionnalité</td>
<td>Tous les</td>
<td>N/A</td>
</tr>

<tr>
<td>Travaux prédéfinis pour l’analyse des journaux des sites Web et le stockage Azure</td>
<td>La Console de la requête HDInsight a une galerie mise en route qui prend en charge des solutions qui fonctionnent sur vos données ou sur les exemples de données.
<p>**Les solutions qui fonctionnent sur vos données**:<br>
Nous avons créé des travaux pour certains des scénarios plus courants analyse des données afin de fournir un point de départ pour la création de vos propres solutions. Vous pouvez utiliser vos données avec la tâche pour voir comment il fonctionne. Lorsque vous êtes prêt, utilisez ensuite ce que vous avez appris à créer une solution qui est modélisée d’après le travail prédéfini.</p>
<p>**Les solutions qui fonctionnent sur les exemples de données**:<br>
Apprenez à travailler avec HDInsight en progressant dans quelques scénarios de base (par exemple, l’analyse des fichiers journaux web et données de capteur). Vous apprendrez comment utiliser HDInsight pour analyser ces données et comment vous pouvez connecter des autres applications et services à ces données. Visualisation de données en vous connectant à Microsoft Excel fournit un exemple de la puissance de cette approche.</p></td>
<td>Console de requête</td>
<td>Hadoop</td>
<td>N/A</td>
</tr>

<tr>
<td>Correctif de fuite de mémoire dans Templeton</td>
<td>Une fuite de mémoire dans Templeton qui concernée les clients qui avaient des clusters à exécution longue ou envoi de 100 s de travail ont été les demandes par seconde a été résolue. Le problème représenté sous Templeton 5xx erreurs et la solution de contournement a été de redémarrer le service. La solution de contournement n’est plus nécessaire.</td>
<td>Templeton</td>
<td>Tous les</td>
<td>https://issues.Apache.org/jira/Browse/HADOOP-11248</td>
</tr>
</table>
<br>


**Remarque**: pour présenter les nouvelles fonctionnalités mises à disposition par la personnalisation du cluster, les procédures d’installation des modules étincelle et R sur un cluster à l’aide d’une Action de Script ont été documentés. Pour plus d’informations, voir :

* [Installer et utiliser l’allumage 1.0 sur les clusters de HDInsight](hdinsight-hadoop-spark-install.md)
* [Installer et utiliser R sur les clusters de HDInsight Hadoop](hdinsight-hadoop-r-scripts.md)




## <a name="notes-for-11072014-release-of-hdinsight"></a>Notes de version 11/07/2014 de HDInsight

Les numéros de version complète des clusters HDInsight déployés dans cette version sont les suivantes :

* 2.1 de HDInsight 2.1.9.374.1153876
* HDInsight 3.0 3.0.5.374.1153876
* 3.1 de HDInsight 3.1.1.374.1153876

Cette version contient les mises à jour du composant suivant.

<table border="1">
<tr>
<th>Titre</th>
<th>Description</th>
<th>Composant</th>
<th>Type de cluster</th>
<th>JIRA (si applicable)</th>
</tr>

<tr>
<td>HDP 2.1.7</td>
<td>Cette version est basée sur 2.1.7 plate-forme Hortonworks données (HDP). Pour plus d’informations, consultez les <a href="http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html" target="_blank">Notes de publication pour HDP 2.1.7</a>.</td>
<td>HDP</td>
<td>Tous les</td>
<td>N/A</td>
</tr>

<tr>
<td>Serveur de montage de fils</td>
<td>Le serveur de montage de fils (également connu sous le nom générique Application historique serveur) a été activé par défaut. Le serveur de la chronologie fournit des informations génériques sur les applications terminées (par exemple, l’ID de l’application, nom de l’application, état de l’application, heure de soumission d’application et temps d’exécution d’application).

Ces informations d’application peuvent être récupérées du nœud de tête en accédant à l’URI http://headnodehost:8188 ou en exécutant la commande de fils : fils application – liste des appStates – toutes les.

Cette information peut également être récupérée à distance si une API REST à https://{ClusterDnsName}. azurehdinsight.NET/ws/v1/applicationhistory/.

Pour plus d’informations, voir les <a href="http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html" target="_blank">Fils de barre de Planning Server</a>.</td>
<td>Service, fils</td>
<td>Hadoop, HBase</td>
<td>N/A</td>
</tr>

<tr>
<td>ID de déploiement du cluster</td>
<td>À partir du Kit de développement logiciel version 1.3.3.1.5426.29232, les utilisateurs peuvent accéder à un ID unique pour chaque cluster, qui est émise à HDInsight. Cela permet aux clients de déterminer des instances uniques des clusters lorsqu’un nom DNS est réutilisé travers de créer ou de supprimer des scénarios.</td>
<td>KIT DE DÉVELOPPEMENT LOGICIEL</td>
<td>Tous les</td>
<td>N/A</td>
</tr>
</table>
<br>

**Remarque**: le bogue qui a empêché le numéro de version complet d’apparaître dans le portail ou d’être renvoyé par le Kit de développement ou par Windows PowerShell a été corrigé dans cette version.

## <a name="notes-for-10152014-release"></a>Notes de version du 15/10/2014

Cette version du correctif corrige une fuite de mémoire dans Templeton qui a touché les gros utilisateurs de Templeton. Dans certains cas, qui a exercé Templeton fortement visualiseraient reconnus comme les codes d’erreur 500 car les demandes n’aurait pas assez de mémoire pour exécuter des erreurs. La solution de contournement pour ce problème a été pour redémarrer le service Templeton. Ce problème a été résolu.


## <a name="notes-for-1072014-release"></a>Notes de version de 10/7/2014.

* Lors de l’utilisation du point de terminaison Ambari, « https://{clusterDns}.azurehdinsight.net/ambari/api/v1/clusters/{clusterDns}.azurehdinsight.net/services/{servicename}/components/{componentname} », le champ *host_name* renvoie le nom de domaine pleinement qualifié (FQDN) du nœud et non uniquement le nom d’hôte. Par exemple, au lieu de renvoyer «**headnode0**», vous obtenez le nom de domaine complet «**headnode0. { ClusterDNS} .azurehdinsight .net**». Cette modification était nécessaire pour faciliter des scénarios où plusieurs types de cluster (par exemple, HBase et Hadoop) peuvent être déployés dans un réseau virtuel. Cela se produit, par exemple, lors de l’utilisation de HBase comme une plate-forme de back-end pour Hadoop.

* Nous avons fourni des nouveaux paramètres de la mémoire pour le déploiement du cluster HDInsight par défaut. Les paramètres de mémoire par défaut précédents ne tenait pas compte adéquate pour le Guide pour le nombre de cœurs de processeur en cours de déploiement. Ces nouveaux paramètres mémoire doivent fournir les meilleures valeurs par défaut (en fonction des recommandations de Hortonworks). Pour modifier ces paramètres, reportez-vous à la documentation de référence SDK sur la modification de la configuration du cluster. Les nouveaux paramètres de mémoire qui sont utilisées par le cluster de HDInsight par défaut 4 processeur core (conteneur 8) sont répertoriés dans le tableau suivant. (Les valeurs utilisées avant cette version sont également cités entre parenthèses ci-dessus.)

<table border="1">
<tr><th>Composant</th><th>Allocation de mémoire</th></tr>
<tr><td> allocation de yarn.Scheduler.minimum</td><td>768 Mo (précédemment 512 Mo)</td></tr>
<tr><td> allocation de yarn.Scheduler.maximum</td><td>Bande 6144 Mo (inchangée)</td></tr>
<tr><td>yarn.NodeManager.Resource.Memory</td><td>Bande 6144 Mo (inchangée)</td></tr>
<tr><td>MapReduce.Map.Memory</td><td>768 Mo (précédemment 512 Mo)</td></tr>
<tr><td>MapReduce.Map.Java.OPTS</td><td>choisit =-Xmx512m de (précédemment - Xmx410m)</td></tr>
<tr><td>MapReduce.reduce.Memory</td><td>1 536 Mo (précédemment 1024 Mo)</td></tr>
<tr><td>MapReduce.reduce.Java.OPTS</td><td>choisit =-Xmx1024m de (précédemment - Xmx819m)</td></tr>
<tr><td>yarn.app.MapReduce.AM.Resource</td><td>768 Mo (précédemment 1024 Mo)</td></tr>
<tr><td>yarn.app.MapReduce.AM.Command</td><td>choisit =-Xmx512m de (précédemment - Xmx819m)</td></tr>
<tr><td>MapReduce.Task.IO.Sort</td><td>256 Mo (précédemment 200 Mo)</td></tr>
<tr><td>tez.AM.Resource.Memory</td><td>1 536 Mo (inchangée)</td></tr>

</table><br>

Pour plus d’informations sur les paramètres de configuration de mémoire utilisés par fils et MapReduce sur la plate-forme de données de Hortonworks qui est utilisé par HDInsight, voir [Déterminer les paramètres de Configuration de la mémoire de HDP](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1-latest/bk_installing_manually_book/content/rpm-chap1-11.html). Hortonworks également un outil pour calculer les paramètres de mémoire appropriée.

Concernant le PowerShell Azure et le message d’erreur HDInsight SDK : «*Cluster n’est pas configuré pour l’accès aux services HTTP*» :

* Cette erreur est un [problème de compatibilité](https://social.msdn.microsoft.com/Forums/azure/a7de016d-8de1-4385-b89e-d2e7a1a9d927/hdinsight-powershellsdk-error-cluster-is-not-configured-for-http-services-access?forum=hdinsight) connu qui peut se produire en raison d’une différence dans la version de la SDK HDInsight ou Azure PowerShell et la version du cluster. Clusters créés sur 15/8 ou une version ultérieure sont prise en charge de la nouvelle fonctionnalité de mise en service dans des réseaux virtuels. Mais cette fonctionnalité n’est pas interprétée correctement par les versions antérieures du ou Azure PowerShell de HDInsight SDK. Le résultat est un échec de certaines opérations de soumission de projet. Si vous utilisez l’API du Kit de développement logiciel HDInsight ou les applets de commande PowerShell de Azure (**Utilisation-AzureRmHDInsightCluster** ou **AzureRmHDInsightHiveJob-Invoke**) pour soumettre les tâches, les opérations peuvent échouer avec le message d’erreur «*Cluster <clustername> n’est pas configuré pour l’accès aux services HTTP*. » Ou (en fonction de l’opération), vous pouvez obtenir d’autres messages d’erreur, tel que «*Impossible de se connecter au cluster*».

* Ces problèmes de compatibilité sont résolues dans les dernières versions du HDInsight SDK et Azure PowerShell. Nous vous recommandons de mettre à jour le SDK HDInsight à la version 1.3.1.6 ou ultérieure et Azure outils PowerShell version 0.8.8 ou une version ultérieure. Vous pouvez accéder au SDK HDInsight plus récente à partir de [](http://nuget.codeplex.com/wikipage?title=Getting%20Started) et les outils de PowerShell Azure comment [installer et configurer Azure PowerShell](../powershell-install-configure.md).



## <a name="notes-for-9122014-release-of-hdinsight-31"></a>Notes de version 9/12/2014 HDInsight 3.1

* Cette version est basée sur l’Hortonworks données plate-forme (HDP) 2.1.5. Pour obtenir une liste des bogues résolus dans cette version, consultez la page [fixe dans cette version](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.5/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.5-fixed.html) sur le site Hortonworks.
* Dans le dossier Librairies de porc, le fichier « avro-mapred-1.7.4.jar » est devenu « avro-mapred-1.7.4-hadoop2.jar. » Le contenu de ce fichier contient un correctif de bogue mineur qui est sans rupture. Il est recommandé que les clients ne faites pas une dépendance directe sur le nom du fichier JAR pour éviter les sauts lorsque les fichiers sont renommés.


## <a name="notes-for-8212014-release"></a>Notes de version du 21/8/2014

* Nous ajoutons la configuration WebHCat suivante (ruche-7155), qui définit la limite de mémoire par défaut pour une tâche de contrôleur Templeton à 1 Go. (La valeur par défaut précédente était de 512 Mo.)

     templeton.mapper.Memory.MB (= 1024)

    * Cette modification résout le message d’erreur suivant laquelle certaines requêtes de ruche avaient exécuté en raison des limites de mémoire inférieures : « Conteneur exécute au-delà des limites de mémoire physique ».
    * Pour rétablir les valeurs par défaut à l’anciens, vous pouvez définir cette valeur à 512 et Azure PowerShell au moment de la création de cluster à l’aide de la commande suivante :

        Ajouter-AzureRmHDInsightConfigValues-Core@{"templeton.mapper.memory.mb"="512";}


* Le nom d’hôte du rôle soigneur a été modifié pour *soigneur*. Cela affecte la résolution de noms au sein du cluster, mais elle n’affecte pas les API reste externes. Si vous avez des composants qui utilisent le nom d’hôte *zookeepernode* , vous devez les mettre à jour pour utiliser le nouveau nom. Les nouveaux noms pour les trois nœuds de soigneur sont :
    * zookeeper0
    * zookeeper1
    * zookeeper2
* Matrice de prise en charge de version HBase est mis à jour. Seulement HDInsight version 3.1 (HBase version 0,98) est pris en charge pour la production de charges de travail HBase. Version 3.0 (qui était disponible pour l’aperçu) Impossible de déplacement vers l’avant.

## <a name="notes-about-clusters-created-prior-to-8152014"></a>Notes sur les clusters créés avant le 8/15/2014.

Un message d’erreur Azure PowerShell ou HDInsight SDK « Cluster <clustername> n’est pas configuré pour l’accès aux services HTTP » (ou en fonction de l’opération, d’autre messages d’erreur tels que : « Impossible de se connecter au cluster ») pouvant survenir en raison d’une différence de version entre le SDK HDInsight ou Azure PowerShell et d’un cluster. Clusters créés sur 15/8 ou une version ultérieure sont prise en charge de la nouvelle fonctionnalité de mise en service dans des réseaux virtuels. Cette fonctionnalité n’est pas interprétée correctement par les anciennes versions de PowerShell Azure ou le SDK HDInsight, qui se traduit par des échecs des opérations de soumission de projet. Si vous utilisez l’API du Kit de développement logiciel HDInsight ou Azure PowerShell applets de commande (par exemple, utilisez-AzureRmHDInsightCluster ou AzureRmHDInsightHiveJob-Invoke) pour soumettre les tâches, les opérations peuvent échouer avec l’un des messages d’erreur décrits.

Ces problèmes de compatibilité sont résolues dans les dernières versions du HDInsight SDK et Azure PowerShell. Nous vous recommandons de mettre à jour le SDK HDInsight à la version 1.3.1.6 ou ultérieure et Azure outils PowerShell version 0.8.8 ou une version ultérieure. Vous pouvez accéder au SDK HDInsight plus récente à partir de [NuGet][nuget-link]. Vous pouvez accéder à l’outils de PowerShell Azure à l’aide de [Microsoft Web Platform Installer][webpi-link].


## <a name="notes-for-7282014-release"></a>Notes de version de 7/28/2014.

* **HDInsight disponible dans nouvelles régions**: nous avons étendu la présence géographique de HDInsight à trois régions. Les clients de HDInsight peuvent créer des clusters dans ces régions :
    * Asie de l’est
    * États-Unis centre nord
    * États-Unis centre sud
* Version 1.6 (HDP 1.1 et Hadoop 1.0.3) de HDInsight et de HDInsight version 2.1 (HDP1.3 et Hadoop 1.2) sont supprimées à partir du portail Azure. Vous pouvez continuer à créer des clusters d’Hadoop pour ces versions à l’aide de l’applet de commande PowerShell d’Azure, [Nouvelle-AzureRmHDInsightCluster](http://msdn.microsoft.com/library/dn593744.aspx) ou à l’aide du [Kit de développement logiciel HDInsight](http://msdn.microsoft.com/library/azure/dn469975.aspx). Reportez-vous à la page de [Gestion des versions des composants de HDInsight](hdinsight-component-versioning.md) pour plus d’informations.
* Plate-forme de données Hortonworks (HDP) des modifications dans cette version :

<table border="1">
<tr><th>HDP</th><th>Modifications</th></tr>
<tr><td>HDP 1.3 / HDI 2.1</td><td>Aucune modification</td></tr>
<tr><td>HDP 2.0 / HDI 3.0</td><td>Aucune modification</td></tr>
<tr><td>HDP 2.1 / HDI 3.1</td><td>soigneur : ['3.4.5.2.1.3.0-1948'] -> ['3.4.5.2.1.3.2-0002']</td></tr>


</table><br>

## <a name="notes-for-6242014-release"></a>Notes de version de 24/6/2014

Cette version contient des améliorations pour le service HDInsight :

* **Disponibilité de HDP 2.1**: 3.1 HDInsight (qui contient HDP 2.1) est généralement disponible et la version par défaut pour les nouveaux clusters.
* **HBase – les améliorations de portail Azure**: nous faisons HBase clusters disponibles dans l’aperçu. Vous pouvez créer des clusters de HBase à partir du portail en seulement quelques clics. 

Avec HBase, vous pouvez créer une variété de charges de travail en temps réel sur HDInsight, à partir de sites Web interactifs qui fonctionnent avec grands ensembles de données pour stocker des données de capteur et de télémétrie provenant de millions de points de terminaison des services. L’étape suivante est d’analyser les données de ces charges de travail avec les travaux d’Hadoop, et cela est possible dans les HDInsight de via Azure PowerShell et le tableau de bord ruche de cluster.

### <a name="apache-mahout-preinstalled-on-hdinsight-31"></a>Mahout Apache préinstallé sur HDInsight 3.1

 [Mahout](http://hortonworks.com/hadoop/mahout/) est préinstallé sur les clusters de HDInsight 3.1 Hadoop, vous pouvez exécuter des tâches de Mahout sans la nécessité d’une configuration de cluster supplémentaires. Par exemple, vous pouvez à distance dans un cluster Hadoop à l’aide du protocole RDP (Remote Desktop), et sans étapes supplémentaires, vous pouvez exécuter la commande Hello World Mahout suivante :

        mahout org.apache.mahout.classifier.df.tools.Describe -p /user/hdp/glass.data -f /user/hdp/glass.info -d I 9 N L  

        mahout org.apache.mahout.classifier.df.BreimanExample -d /user/hdp/glass.data -ds /user/hdp/glass.info -i 10 -t 100

Pour une explication plus détaillée de cette procédure, consultez la documentation pour obtenir un [Exemple de Breiman](https://mahout.apache.org/users/classification/breiman-example.html) sur le site Web de Apache Mahout.


### <a name="hive-queries-can-use-tez-in-hdinsight-31"></a>Les requêtes de ruche peuvent utiliser Tez HDInsight 3.1

Ruche 0,13 pouce est disponible dans HDInsight 3.1, et il est capable d’exécuter des requêtes à l’aide de Tez, qui peut être exploitée pour des améliorations de performances substantiel.
Tez n’est pas activer par défaut pour les requêtes de la ruche. Pour l’utiliser, vous devez choisir. Vous pouvez activer Tez en exécutant l’extrait de code suivant :

        set hive.execution.engine=tez;
        select sc_status, count(*), histogram_numeric(sc_bytes,5) from website_logs_orc_local group by sc_status;

Hortonworks a publié une analyse détaillée des améliorations de performances de requête ruche avec Tez comme livrés dans les bancs d’essai standard. Pour plus d’informations, voir [évaluation des 13 la ruche Apache pour entreprise Hadoop](http://hortonworks.com/blog/benchmarking-apache-hive-13-enterprise-hadoop/).

Pour plus d’informations sur l’utilisation de la ruche avec Tez, consultez [sur Tez la ruche](https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez).

###<a name="global-availability"></a>Disponibilité globale
Avec la version HDInsight sur Hadoop 2.2, Microsoft a fait HDInsight disponible dans toutes les principales zones géographiques où Azure est disponible. En particulier, les centres de données en Europe et en Asie du Sud-est du ouest ont été mis en ligne. Cela permet aux clients de localiser des clusters dans un centre de données qui est proche et, éventuellement, dans une zone similaire d’exigences de conformité.


###<a name="dos--donts-between-cluster-versions"></a>A faire & n’entre versions de cluster

**Metastores Oozie utilisés avec un cluster 3.1 de HDInsight ne sont pas compatibles avec les clusters HDInsight 2.1, et ils ne peuvent pas être utilisés avec cette version précédente**.

Une base de données de metastore Oozie personnalisé déployé avec un cluster 3.1 de HDInsight ne peut pas être réutilisé avec un cluster HDInsight 2.1. C’est le cas même si le metastore provenait avec un cluster HDInsight 2.1. Ce scénario n’est pas pris en charge, car le schéma metastore mis à jour lorsqu’il est utilisé avec un cluster HDInsight 3.1, donc il n’est plus compatible avec le metastore requis par les clusters HDInsight 2.1. Toute tentative de réutiliser une metastore Oozie qui a été utilisé avec un cluster HDInsight 3.1 rend le cluster HDInsight 2.1 inutiles.

**Oozie metastores ne peut pas être partagé entre les clusters.**

Metastores de Oozie sont liés aux clusters spécifiques, et ils ne peuvent pas être partagées entre les clusters.

###<a name="breaking-changes"></a>Modifications avec rupture

**Syntaxe du préfixe**: uniquement le « wasbs : / / » syntaxe est pris en charge dans HDInsight 3.1 et 3.0 clusters. L’ancien « asv : / / » syntaxe est pris en charge dans HDInsight 2.1 et 1,6 clusters, mais il n’est pas pris en charge dans HDInsight 3.1 ou 3.0 clusters. Cela signifie que toutes les tâches soumises à une 3.1 HDInsight ou cluster 3.0 explicitement utilisent le « asv : / / » syntaxe échouera. Le « wasbs : / / » syntaxe doit être utilisée à la place. En outre, les travaux soumis à n’importe quel 3.1 HDInsight ou 3.0 clusters qui sont créés avec un metastore existant qui contient des références explicites aux ressources à l’aide de la « asv : / / » syntaxe échouera. Ces metastores doivent être recréés à l’aide de la « wasbs : / / » syntaxe aux ressources de l’adresse.


**Ports**: les ports utilisés par le service de HDInsight ont été modifiées. Les numéros de port qui ont été utilisés étaient dans la plage de ports éphémères, le système d’exploitation Windows. Ports sont affectés automatiquement à partir d’une plage prédéfinie éphémère pour des communications basées sur le protocole Internet courtes. La nouvelle série de numéros de port de service plate-forme de données Hortonworks (HDP) autorisées sont en dehors de cette plage pour éviter de rencontrer des conflits qui pourraient se poser avec les ports utilisés par les services exécutés sur le nœud principal. Les nouveaux numéros de port ne doivent pas provoquer des modifications avec rupture. Les numéros utilisés sont les suivants :

 **HDInsight 1.6 (HDP 1.1)**
<table border="1">
<tr><th>Nom</th><th>Valeur</th></tr>
<tr><td>DFS.http.Address</td><td>namenodehost:30070</td></tr>
<tr><td>DFS.datanode.Address</td><td>0.0.0.0:30010</td></tr>
<tr><td>DFS.datanode.http.Address</td><td>0.0.0.0:30075</td></tr>
<tr><td>DFS.datanode.IPC.Address</td><td>0.0.0.0:30020</td></tr>
<tr><td>DFS.Secondary.http.Address</td><td>0.0.0.0:30090</td></tr>
<tr><td>mapred.job.Tracker.http.Address</td><td>jobtrackerhost:30030</td></tr>
<tr><td>mapred.Task.Tracker.http.Address</td><td>0.0.0.0:30060</td></tr>
<tr><td>MapReduce.History.Server.http.Address</td><td>0.0.0.0:31111</td></tr>
<tr><td>templeton.port</td><td>30111</td></tr>
</table><br>

 **HDInsight 3.1 et 3.0 (HDP 2.1 et 2.0)**
<table border="1">
<tr><th>Nom</th><th>Valeur</th></tr>
<tr><td>adresse de DFS.namenode.http</td><td>namenodehost:30070</td></tr>
<tr><td>adresse de DFS.namenode.HTTPS</td><td>headnodehost:30470</td></tr>
<tr><td>DFS.datanode.Address</td><td>0.0.0.0:30010</td></tr>
<tr><td>DFS.datanode.http.Address</td><td>0.0.0.0:30075</td></tr>
<tr><td>DFS.datanode.IPC.Address</td><td>0.0.0.0:30020</td></tr>
<tr><td>adresse de DFS.namenode.Secondary.http</td><td>0.0.0.0:30090</td></tr>
<tr><td>yarn.NodeManager.WebApp.Address</td><td>0.0.0.0:30060</td></tr>
<tr><td>templeton.port</td><td>30111</td></tr>
</table><br>

###<a name="dependencies"></a>Dépendances

Les dépendances suivantes ont été ajoutées dans HDInsight 3.x (HDP2.x) :

* guice-servlet
* optiq-core
* javax.inject
* activation
* jsr305
* geronimo-jaspic_1.0_spec
* juil-à-slf4j
* Java-xmlbuilder
* ant
* compilateur Commons
* jdo-api
* Commons-math3
* paranamer
* impl-jaxb
* stringtemplate
* eigenbase-xom
* Jersey-servlet
* Commons-exec
* jaxb-api
* jetée-tout-serveur
* janino
* xercesImpl
* optiq-avatica
* jta
* eigenbase-propriétés
* une super-tous
* hamcrest-core
* courrier
* linq4j
* jpam
* Jersey-client
* aopalliance
* geronimo-annotation_1.0_spec
* Lanceur de l’ant
* Jersey-guice
* API-XML
* stax-api
* ASM-commons
* arborescence de l’asm
* wadl
* geronimo-jta_1.1_spec
* guice
* leveldbjni-tous
* vitesse
* larguer
* java qui saute aux yeux
* jetée-tous
* Commons-dbcp

Les dépendances suivantes n’existent plus dans HDInsight 3.x (HDP2.x) :

* jdeb
* KFS
* SQLUltraLite
* lierre
* aspectjrt
* JSON
* principaux
* jdo2-api
* avro-mapred
* datanucleus-enhancer
* JSP
* api de journalisation Commons
* Commons mathématique
* JavaEWAH
* aspectjtools
* javolution
* hdfsproxy
* hbase
* qui saute aux yeux

###<a name="version-changes"></a>Modifications de version

Les changements de version suivants ont été effectués entre HDInsight 2.x (HDP1.x) et HDInsight 3.x (HDP2.x) :

* mesures-core : ['2.1.2'] -> ['3.0.0']
* derbynet : ['10.4.2.0'] -> ['10.10.1.1']
* datanucleus : [' SGBDR-3.0.8'] -> [' SGBDR-3.2.9']
* compilateur JASPER : ['5.5.12'] -> ['5.5.23']
* log4j : ['1.2.15', '1.2.16'] -> ['1.2.16', '1.2.17']
* derbyclient : ['10.4.2.0'] -> ['10.10.1.1']
* httpcore : ['4.2.4'] -> ['4.2.5']
* hsqldb : ['1.8.0.10'] -> ['2.0.0']
* jets3t : réglementaires ['0.6.1 '] -> ['0.9.0']
* protobuf-java : ['2.4.1'] -> ['2.5.0']
* Derby : ['10.4.2.0'] -> ['10.10.1.1']
* JASPER : [' exécution-5.5.12'] -> [' exécution-5.5.23']
* démon de Commons : ['1.0.1'] -> ['1.0.13']
* datanucleus-core : ['3.0.9'] -> ['3.2.10']
* api-datanucleus-jdo : ['3.0.7'] -> ['3.2.6']
* soigneur : ['3.4.5.1.3.9.0-01320'] -> ['3.4.5.2.1.3.0-1948']
* bonecp : ['0.7.1.RELEASE'] -> ['
* 0.8.0.RELEASE']


### <a name="drivers"></a>Pilotes
Le pilote de Connnectivity de base de données Java (JDBC) pour SQL Server est utilisé en interne par HDInsight et n’est pas utilisé pour les opérations externes. Si vous souhaitez vous connecter à HDInsight à l’aide de la connectivité de base de données ouverte (ODBC), utilisez le pilote Microsoft ODBC de la ruche. Pour plus d’informations, consultez [Excel de se connecter à HDInsight avec le pilote ODBC de Microsoft de la ruche](hdinsight-connect-excel-hive-odbc-driver.md).


### <a name="bug-fixes"></a>Correctifs de bogues

Dans cette version, nous avons actualisé les versions suivantes de HDInsight avec plusieurs résolutions de bogue :

* HDInsight 2.1 (HDP 1.3)
* HDInsight 3.0 (HDP 2.0)
* HDInsight 3.1 (HDP 2.1)


## <a name="hortonworks-release-notes"></a>Notes de mise à Hortonworks

Notes de publication pour les plates-formes de données Hortonworks (HDPs) qui sont utilisés par les clusters de version HDInsight sont disponibles aux emplacements suivants :

* HDInsight version 3.1 utilise une distribution Hadoop qui est basée sur la [Plate-forme de données Hortonworks 2.1.7][hdp-2-1-7]. Il s’agit de cluster Hadoop par défaut créé lors de l’utilisation du portail Azure après 11/7/2014. Clusters HDInsight 3.1 créés avant le 11/7/2014 étaient basées sur la [Plate-forme de données Hortonworks 2.1.1][hdp-2-1-1]

* HDInsight version 3.0 utilise une distribution Hadoop qui est basée sur la [plate-forme de données Hortonworks 2.0][hdp-2-0-8].

* HDInsight version 2.1 utilise une distribution Hadoop qui est basée sur la [Hortonworks données plate-forme 1.3][hdp-1-3-0].

* HDInsight version 1.6 utilise une distribution Hadoop qui est basée sur la version [1.1 de plate-forme de données Hortonworks][hdp-1-1-0].

[hdp-2-1-7]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html

[hdp-2-1-1]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html

[hdp-2-0-8]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html

[nuget-link]: https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.HDInsight/

[webpi-link]: http://go.microsoft.com/?linkid=9811175&clcid=0x409

[hdinsight-install-spark]: ../hdinsight-hadoop-spark-install/
[hdinsight-r-scripts]: ../hdinsight-hadoop-r-scripts/
 
