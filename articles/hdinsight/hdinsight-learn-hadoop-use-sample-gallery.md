<properties
   pageTitle="Découvrez Hadoop dans HDInsight avec la galerie d’exemples de | Microsoft Azure"
   description="Apprendre rapidement Hadoop en exécutant des exemples d’applications à partir de la galerie de route HDInsight mise en route. Utilisez les exemples de données ou fournir votre propre."
   services="hdinsight"
   documentationCenter=""
   tags="azure-portal"
   authors="mumian"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.workload="big-data"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="10/21/2016"
   ms.author="jgao"/>

# <a name="learn-hadoop-by-using-the-azure-hdinsight-getting-started-gallery"></a>Découvrez Hadoop à l’aide de la galerie Azure HDInsight mise en route

La galerie de route mise en route est uniquement disponible pour les clusters basés sur Windows de HDInsight. La bibliothèque fournit une manière facile et rapidement apprendre Hadoop en exécutant les exemples d’applications dans HDInsight. Certains exemples sont fournis avec les exemples de données. Vous pouvez fournir vos propres données pour les échantillons restants. Actuellement, il n’y a six échantillons (avec les autres sont en préparation) suivants :

- Solutions avec vos données Azure
    - Analyse de journal du site Web Microsoft Azure
    - Analytique de stockage Microsoft Azure
- Solutions avec des exemples de données
    - Analyse des données de capteur
    - Analyse des tendances Twitter
    - Analyse des journaux de site Web
    - Recommandation de film mahout

![Solutions HDInsight Hadoop, tempête et Galerie de HBase mise en route, y compris des exemples de données.][hdinsight.sample.gallery]

La vidéo suivante montre comment exécuter l’exemple d’analyse de tendances Twitter :

<center><a href="https://www.youtube.com/embed/7ePbHot1SN4">https://www.YouTube.com/EMBED/7ePbHot1SN4></a></center>

Le tableau de bord peut être accessible par le biais à http://<YourHDInsightClusterName>.azurehdinsight.net/ ou à partir du portail Azure.

**Pour exécuter un exemple de la galerie de route mise en route**

1. Connectez-vous au [portail Azure][azure.portal].
2. Cliquez sur **Parcourir** dans le menu de gauche et cliquez sur **HDInsight les Clusters**, puis cliquez sur le nom de votre cluster.
3. Dans le menu supérieur, cliquez sur **tableau de bord** .
4. Entrez le nom d’utilisateur et le mot de passe pour l’utilisateur HTTP (également appelé l’utilisateur de cluster).
6. Cliquez sur **Mise en route galerie** en haut de la page.
7. Cliquez sur un des exemples. Chaque exemple fournit la procédure détaillée pour l’exécuter. L’image suivante affiche l’exemple d’analyse de tendances Twitter :

    ![Échantillon d’analyse de tendances de HDInsight Twitter][hdinsight.twitter.sample]

## <a name="next-steps"></a>Étapes suivantes
Autres façons d’obtenir des informations sur les HDInsight sont les suivantes :

- [Plan de formation de HDInsight][hdinsight.learn.map]
- [HDInsight infographic][hdinsight.infographic]

<!--Image references-->
[hdinsight.sample.gallery]: ./media/hdinsight-learn-hadoop-use-sample-gallery/HDInsight-Getting-Started-Gallery.png
[hdinsight.twitter.sample]: ./media/hdinsight-learn-hadoop-use-sample-gallery/HDInsight-Twitter-Trend-Analysis-sample.png

<!--Link references-->
[hdinsight.learn.map]: https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/
[hdinsight.infographic]: http://go.microsoft.com/fwlink/?linkid=523960
[azure.portal]:https://portal.azure.com
