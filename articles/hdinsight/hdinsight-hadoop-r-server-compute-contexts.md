<properties
   pageTitle="Calculer les options de contexte pour le serveur de R sur HDInsight (aperçu) | Microsoft Azure"
   description="Découvrez les options de contexte compute différents disponibles pour les utilisateurs avec un serveur de R sur HDInsight (aperçu)"
   services="HDInsight"
   documentationCenter=""
   authors="jeffstokes72"
   manager="jhubbard"
   editor="cgronlun"
/>

<tags
   ms.service="HDInsight"
   ms.devlang="R"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-services"
   ms.date="10/18/2016"
   ms.author="jeffstok"
/>

# <a name="compute-context-options-for-r-server-on-hdinsight-preview"></a>Calculer les options de contexte pour le serveur de R sur HDInsight (aperçu)

Microsoft R Server sur HDInsight d’Azure (aperçu) fournit les capacités les plus récentes pour l’analytique de base R. Il utilise les données stockées dans très dans un conteneur dans votre compte de stockage(../storage/storage-introduction.md "stockage Azure Blob") [Azure Blob]ou le système de fichiers Linux locaux. Dans la mesure où R Server repose sur Ouvrir la source de R, les applications R que vous créez peuvent exploiter les packages 8000 + R ouvrir la source de. Ils peuvent également exploiter les routines dans de [DETARTREUR](http://www.revolutionanalytics.com/revolution-r-enterprise-scaler "ScaleR Analytique de révolution"), groupes de package analytique de données volumineuses de Microsoft fourni avec le serveur de R.  

Le nœud de bord d’un cluster de prime fournit un emplacement pratique pour se connecter au cluster et exécuter vos scripts de R. Avec un nœud, vous avez la possibilité d’exécuter des fonctions de distribuées parallélisées de DETARTREUR sur les cœurs du serveur edge nœud. Vous avez également la possibilité de les exécuter sur les nœuds du cluster à l’aide Hadoop carte réduire de DETARTREUR ou étincelle calculer des contextes.

## <a name="compute-contexts-for-an-edge-node"></a>Calculer des contextes pour un nœud

En général, un R de script qui est exécuté dans R Server sur le nœud du bord s’exécute dans l’interpréteur R sur ce nœud. L’exception est ces étapes qui appellent une fonction ScaleR. Les appels ScaleR exécutent dans un environnement de calcul qui est déterminé par la façon dont vous définissez le contexte de calcul ScaleR.  Lorsque vous exécutez votre script R à partir d’un nœud, les valeurs possibles du contexte compute sont local séquentiel (« local »), parallèle local ('localpar'), de réduire de mappage et allumage.

Les options 'locales' et 'localpar' diffèrent uniquement dans l’exécution des appels de rxExec. Ils sont tous deux exécutent autres appels de fonction rx de manière parallèle sur tous les cœurs, sauf spécification contraire à l’aide de l’option numCoresToUse ScaleR, par exemple, rxOptions(numCoresToUse=6). La liste suivante récapitule les différentes options de contexte de calcul

| Contexte de calcul  | Comment définir                      | Contexte d’exécution                                                                     |
|------------------|---------------------------------|---------------------------------------------------------------------------------------|
| Local séquentiel | rxSetComputeContext('local')    | L’exécution de parallélisée sur les cœurs du serveur edge nœud, à l’exception de rxExec appelle qui sont exécutés en série |
| Parallèle local   | rxSetComputeContext('localpar') | Exécution de parallélisée sur les cœurs du serveur edge nœud                                 |
| Allumage            | RxSpark()                       | Parallélisée exécution distribuée via un allumage sur les nœuds du cluster HDI      |
| Plan réduire       | RxHadoopMR()                    | Parallélisée exécution distribuée via la réduction de mappage entre les nœuds du cluster HDI |


En supposant que vous souhaitez que l’exécution parallélisée aux fins de performances, il existe trois options. L’option que vous choisissez dépend de la nature de votre travail analytique et que la taille et l’emplacement de vos données.

## <a name="guidelines-for-deciding-on-a-compute-context"></a>Conseils pour le choix d’un contexte de calcul

Actuellement, il n’existe pas de formule qui vous indique qui calculent le contexte à utiliser. Toutefois, il existe certains principes directeurs qui peuvent vous aider à faire le bon choix, ou au moins vous aider à affiner vos choix avant d’exécuter un banc d’essai. Ces principes sont les suivants :

1.  Le système de fichiers Linux locaux est plus très rapide.
2.  Analyses répétées sont plus rapides si les données sont locales et s’il s’agit de XDF.
3.  Il est préférable de flux de petites quantités de données à partir d’une source de données texte ; Si la quantité de données est plus grande, convertir en XDF avant l’analyse.
4.  La surcharge de copie ou de transmission en continu de données pour le nœud d’arête pour l’analyse devient ingérable de très grandes quantités de données.
5.  Allumage est plus rapide que la carte de réduire pour analyse dans Hadoop.

Compte tenu de ces principes, certaines règles générales pour la sélection d’un contexte de calcul sont les suivants :

### <a name="local"></a>Local

- Si la quantité de données à analyser est faible et ne requiert pas une analyse répétée, puis de l’y directement dans la routine d’analyse et utiliser 'local' ou 'localpar'.
- Si la quantité de données à analyser est petite ou moyenne taille et requiert des analyses répétées, puis copier sur le système de fichiers local, l’importer dans XDF et analyser via 'local' ou 'localpar'.

### <a name="hadoop-spark"></a>Hadoop ETINCELLE

- Si la quantité de données à analyser est importante, puis l’importer sur XDF dans très (sauf si le stockage est un problème) et de les analyser via « Étincelle ».

### <a name="hadoop-map-reduce"></a>Hadoop plan réduire

- À utiliser uniquement si vous rencontrez un problème insurmontable avec utilisation d’étincelle calculer contexte car généralement il sera plus lent.  

## <a name="inline-help-on-rxsetcomputecontext"></a>Aide en ligne sur rxSetComputeContext

Pour plus d’informations et des exemples de contextes de calcul ScaleR, consultez inline à l’aide de R sur la méthode rxSetComputeContext, par exemple :

    > ?rxSetComputeContext

Vous pouvez également faire référence à « ScaleR Distributed Computing Guide » qui est disponible à partir de [MSDN de serveur R](https://msdn.microsoft.com/library/mt674634.aspx "Serveur R sur MSDN") library.


## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris comment créer un nouveau cluster HDInsight qui inclut le serveur de R. Vous avez également appris les notions de base de l’utilisation de la console R à partir d’une session SSH. Maintenant, vous pouvez lire les articles suivants pour découvrir d’autres méthodes de travail avec le serveur R HDInsight :

- [Vue d’ensemble du serveur R Hadoop](hdinsight-hadoop-r-server-overview.md)
- [Mise en route avec les serveur R pour Hadoop](hdinsight-hadoop-r-server-get-started.md)
- [Ajouter les serveur de RStudio à HDInsight prime](hdinsight-hadoop-r-server-install-r-studio.md)
- [Options de stockage Azure pour R Server sur HDInsight prime](hdinsight-hadoop-r-server-storage.md)
