<properties
   pageTitle="Déployer et gérer des topologies de tempête de Apache sur HDInsight | Microsoft Azure"
   description="Découvrez comment déployer, de surveiller et de gérer des topologies de tempête de Apache à l’aide du tableau de bord tempête sur HDInsight. Hadoop d’utiliser les outils de Visual Studio."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"
    tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/11/2016"
   ms.author="larryfr"/>

#<a name="deploy-and-manage-apache-storm-topologies-on-windows-based-hdinsight"></a>Déployer et gérer des topologies de tempête de Apache sur HDInsight de basées sur Windows

Le tableau de bord tempête vous permet facilement déployer et exécuter des topologies de tempête de Apache à votre cluster HDInsight à l’aide de votre navigateur web. Vous pouvez également utiliser le tableau de bord pour surveiller et gérer des topologies en cours d’exécution. Si vous utilisez Visual Studio, les outils de HDInsight pour Visual Studio offrent des fonctionnalités similaires dans Visual Studio.

Le tableau de bord de tempête et les fonctionnalités de la tempête dans les outils de HDInsight s’appuient sur l’API REST de tempête, qui peut être utilisé pour créer votre propre contrôle et les solutions de gestion.

> [AZURE.IMPORTANT] Les étapes de ce document requièrent une tempête basés sur Windows sur le cluster de HDInsight. Pour plus d’informations sur l’utilisation d’un cluster basé sur Linux, consultez [déployer et gérer des topologies de tempête de Apache sur basé sur Linux de HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)

##<a name="prerequisites"></a>Conditions préalables

* **Tempête de Apache sur HDInsight** - reportez-vous à la section <a href="../hdinsight-storm-getting-started/" target="_blank">mise en route de tempête Apache sur HDInsight</a> les étapes de création d’un cluster

* Pour le **Tableau de bord de Storm**: un navigateur web moderne qui prend en charge de HTML5

* Pour **Visual Studio** – Azure SDK 2.5.1 ou plus récent et les outils de HDInsight pour Visual Studio. Reportez-vous à la section <a href="../hdinsight-hadoop-visual-studio-tools-get-started/" target="_blank">mise en route de HDInsight les outils de Visual Studio</a> pour installer et configurer les outils de HDInsight pour Visual Studio.

    L’une des versions suivantes de Visual Studio :

    * Visual Studio 2012 avec la <a href="http://www.microsoft.com/download/details.aspx?id=39305" target="_blank">mise à jour 4</a>

    * Visual Studio 2013 avec la <a href="http://go.microsoft.com/fwlink/?LinkId=517284" target="_blank">Communauté de 2013 Visual Studio</a> ou de <a href="http://www.microsoft.com/download/details.aspx?id=44921" target="_blank">mise à jour 4</a>

    * <a href="http://visualstudio.com/downloads/visual-studio-2015-ctp-vs" target="_blank">Visual Studio CTP6 de 2015</a>

    > [AZURE.NOTE] Actuellement les outils HDInsight pour Visual Studio prennent uniquement en charge tempête sur HDInsight version de cluster 3.2.

##<a name="storm-dashboard"></a>Tableau de bord de tempête

Le tableau de bord de Storm est une page web disponible sur votre cluster de tempête. L’URL est **https://&lt;nom_cluster >.azurehdinsight.net/**, où **clustername** est le nom de votre tempête sur cluster de HDInsight.

À partir du haut du tableau de bord tempête, sélectionnez **Soumettre une topologie**. Suivez les instructions de la page pour exécuter un exemple de topologie ou pour télécharger et exécuter une topologie que vous avez créé.

![la page de la topologie d’envoi][storm-dashboard-submit]

###<a name="storm-ui"></a>L’interface utilisateur de tempête

Dans le tableau de bord tempête, sélectionnez le lien de **L’interface utilisateur de la tempête** . Il affiche des informations sur le cluster, en plus des topologies en cours d’exécution.

![l’interface utilisateur de tempête][storm-dashboard-ui]

> [AZURE.NOTE] Avec certaines versions d’Internet Explorer, vous pouvez constater que l’interface utilisateur de tempête ne s’actualise pas après que vous avez visités tout d’abord qu’il. Par exemple, il peut ne pas affiche les nouvelles topologies vous avez soumis, ou il peut présenter une topologie active lorsque vous l’avez désactivée auparavant. Microsoft est conscient de ce problème et travaille sur une solution.

####<a name="main-page"></a>Page principale

La page principale de l’interface utilisateur de tempête fournit les informations suivantes :

* **Cluster résumé**: informations de base sur le cluster de tempête.

* **Résumé de la topologie**: liste des topologies en cours d’exécution. Utilisez les liens de cette section pour afficher plus d’informations sur les topologies spécifiques.

* **Résumé du superviseur**: informations sur le maître d’oeuvre tempête.

* **Configuration de Nimbus**: configuration Nimbus pour le cluster.

####<a name="topology-summary"></a>Résumé de la topologie

Sélection d’un lien dans la section **topologie résumé** affiche les informations suivantes sur la topologie :

* **Résumé de la topologie**: informations de base sur la topologie.

* **Actions de topologie**: des actions de gestion que vous pouvez effectuer pour la topologie.

    * **Activer**: traitement de curriculum vitae de topologie est désactivée.

    * **Désactiver**: interrompt une topologie en cours d’exécution.

    * **Rééquilibrer**: ajuste le parallélisme de la topologie. Vous devez rééquilibrer les topologies en cours d’exécution après avoir modifié le nombre de nœuds dans le cluster. Ainsi, la topologie à ajuster le parallélisme pour compenser l’augmenté ou diminué le nombre de nœuds dans le cluster.

        Pour plus d’informations, voir <a href="http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">comprendre le parallélisme de topologie de tempête</a>.

    * **Kill**: met fin à une topologie de tempête après le délai spécifié.

* **Statistiques de la topologie**: statistiques sur la topologie. Utilisez les liens dans la colonne de la **fenêtre** pour définir le délai pour les entrées restantes sur la page.

* **Becs verseurs amovibles**: les becs verseurs utilisés par la topologie. Utilisez les liens de cette section pour afficher plus d’informations sur becs verseurs spécifiques.

* **Boulons**: les boulons utilisés par la topologie. Utilisez les liens de cette section pour afficher plus d’informations sur les boulons spécifiques.

* **Configuration de la topologie**: la configuration de la topologie sélectionnée.

####<a name="spout-and-bolt-summary"></a>Un bec et un résumé de boulon

Sélection d’un BEC VERSEUR à partir de la section **des becs verseurs amovibles** ou **boulons** affiche les informations suivantes sur l’élément sélectionné :

* **Composant Sommaire**: informations de base sur le bec ou le boulon.

* **Statistiques de bec/du boulon**: statistiques sur le bec ou le boulon. Utilisez les liens dans la colonne de la **fenêtre** pour définir le délai pour les entrées restantes sur la page.

* **Statistiques de l’entrée** (boulon uniquement) : informations sur les flux d’entrée utilisée par le boulon.

* **Statistiques de sortie**: informations sur les flux de données émis par cet BEC VERSEUR ou de boulon.

* **Les exécuteurs**: informations sur les instances du boulon ou du bec. Sélectionnez le **Port** d’entrée d’un exécuteur spécifique afficher un journal d’informations de diagnostic produites pour cette instance.

* **Erreur**: les informations d’erreur pour cette goulotte ou de boulon.

##<a name="hdinsight-tools-for-visual-studio"></a>Outils d’HDInsight pour Visual Studio

Les outils de HDInsight peut servir à présenter les topologies C# ou hybride à votre cluster tempête. La procédure suivante utilise un exemple d’application. Pour plus d’informations sur la création de vos propres topologies en utilisant les outils de HDInsight, consultez [développement C# topologies à l’aide des outils HDInsight pour Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md).

Utilisez les étapes suivantes pour déployer un exemple à votre tempête sur cluster de HDInsight, puis d’afficher et de gérer la topologie.

1. Si vous n’avez pas déjà installé la dernière version des outils HDInsight pour Visual Studio, reportez-vous à la section <a href="../hdinsight-hadoop-visual-studio-tools-get-started/" target="_blank">mise en route à l’aide des outils HDInsight pour Visual Studio</a>.

2. Ouvrez Visual Studio, sélectionnez le **fichier** > **Nouveau** > **projet**.

3. Dans la boîte de dialogue **Nouveau projet** , développez **installé** > **modèles**, puis sélectionnez **HDInsight**. À partir de la liste des modèles, sélectionnez **Exemple de tempête**. Au bas de la boîte de dialogue, tapez un nom pour l’application.

    ![image](./media/hdinsight-storm-deploy-monitor-topology/sample.png)

1. Dans l' **Explorateur de solutions**, droit sur le projet et sélectionnez **Envoyer à tempête sur HDInsight**.

    > [AZURE.NOTE] Si vous y êtes invité, entrez les informations d’identification de connexion pour votre abonnement Azure. Si vous avez plus d’un abonnement, connectez-vous à celui qui contient votre tempête sur cluster de HDInsight.

2. Sélectionnez votre tempête sur cluster de HDInsight dans la liste déroulante de **Storm Cluster** et sélectionnez **Envoyer**. Vous pouvez contrôler si l’envoi a réussi à l’aide de la fenêtre **sortie** .

3. Lorsque la topologie a été envoyée avec succès, les **Topologies de Storm** pour le cluster doit apparaître. Sélectionnez la topologie à partir de la liste pour afficher des informations sur la topologie en cours d’exécution.

    ![Moniteur de Visual studio](./media/hdinsight-storm-deploy-monitor-topology/vsmonitor.png)

    > [AZURE.NOTE] Vous pouvez également afficher des **Topologies de tempête** à partir de **l’Explorateur de serveurs** en développant **Azure** > **HDInsight**et puis avec le bouton droit d’une tempête sur cluster de HDInsight et en sélectionnant **Vue bourrasque de Topologies**.

    Sélectionnez la forme des becs verseurs boulons pour afficher des informations sur ces composants. Une nouvelle fenêtre s’ouvre pour chaque élément sélectionné.
    
    > [AZURE.NOTE] Le nom de la topologie est le nom de classe de la topologie (dans ce cas, `HelloWord`,) avec un horodatage ajouté.

4. À partir de la vue **Résumé de la topologie** , sélectionnez **Arrêter** pour arrêter la topologie.

    > [AZURE.NOTE] Topologies de tempête poursuivre l’exécution jusqu'à ce qu’ils sont arrêtés ou le cluster est supprimé.

##<a name="rest-api"></a>API REST

L’interface utilisateur de tempête est construit sur l’API REST, pour vous pouvez d’effectuer similaires de gestion et de contrôle des fonctionnalités à l’aide de l’API REST. Vous pouvez utiliser l’API REST pour créer des outils personnalisés pour la gestion et la surveillance des topologies de tempête.

Pour plus d’informations, voir [l’API REST de l’interface utilisateur tempête](https://github.com/apache/storm/blob/0.9.3-branch/STORM-UI-REST-API.md). Les informations suivantes sont spécifiques à l’utilisation de l’API REST avec tempête Apache sur HDInsight.

###<a name="base-uri"></a>L’URI de base

L’URI de base pour l’API REST sur des clusters de HDInsight est **https://&lt;nom_cluster >.azurehdinsight.net/stormui/api/v1/**, où **clustername** est le nom de votre tempête sur cluster de HDInsight.

###<a name="authentication"></a>Authentification

Les demandes pour l’API REST doivent utiliser **l’authentification de base**, afin que vous utilisez le nom d’administrateur de cluster de HDInsight et le mot de passe.

> [AZURE.NOTE] Étant donné que l’authentification de base est envoyée à l’aide de texte en clair, vous devez **toujours** utiliser HTTPS pour sécuriser les communications avec le cluster.

###<a name="return-values"></a>Valeurs de retour

Les informations retournées par l’API REST peuvent uniquement être utilisables à partir de dans le cluster ou les ordinateurs virtuels sur le même réseau que le cluster virtuel au Azure. Par exemple, le nom de domaine complet (FQDN) retourné pour les serveurs soigneur ne sera pas accessibles à partir d’Internet.

##<a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris comment déployer et surveiller des topologies en utilisant le tableau de bord de tempête, découvrez comment :

* [Développer les topologies C# à l’aide des outils HDInsight pour Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md)

* [Développer les topologies basée sur Java à l’aide de Maven](hdinsight-storm-develop-java-topology.md)

Pour obtenir une liste de plusieurs exemples de topologies, consultez [exemples de topologies pour tempête sur HDInsight](hdinsight-storm-example-topology.md).

[hdinsight-dashboard]: ./media/hdinsight-storm-deploy-monitor-topology/dashboard-link.png
[storm-dashboard-submit]: ./media/hdinsight-storm-deploy-monitor-topology/submit.png
[storm-dashboard-ui]: ./media/hdinsight-storm-deploy-monitor-topology/storm-ui-summary.png
