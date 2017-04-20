<properties
   pageTitle="Analyser des données de capteur avec Apache tempête et HBase | Microsoft Azure"
   description="Apprenez à vous connecter à Apache tempête avec un réseau virtuel. Utilisez tempête avec HBase pour traiter des données de capteur à partir d’un concentrateur d’événements et le visualiser avec D3.js."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/20/2016"
   ms.author="larryfr"/>

# <a name="analyze-sensor-data-with-apache-storm-event-hub-and-hbase-in-hdinsight-hadoop"></a>Analyser des données de capteur avec Apache tempête, concentrateur d’événements et HBase dans les HDInsight (Hadoop) 

Découvrez comment utiliser Apache tempête sur HDInsight pour traiter des données de capteur de concentrateur d’événements Azure, stockez-le dans HBase Apache sur HDInsight et le visualiser à l’aide de D3.js en cours d’exécution sous la forme d’une application Web de Azure.

Le modèle de gestionnaire de ressources Azure utilisé dans ce document montre comment créer plusieurs ressources Azure dans un groupe de ressources. Plus précisément, il crée un réseau virtuel d’Azure, les deux clusters de HDInsight (tempête et HBase) et une application Web de Azure. Une implémentation node.js du tableau de bord web en temps réel est déployée automatiquement sur l’application web.

> [AZURE.NOTE] Les informations contenues dans ce document et que l’exemple fourni, ont été testés à l’aide de 3.3 de HDInsight basé sur Linux et les 3.4 versions du cluster.

## <a name="prerequisites"></a>Conditions préalables

* Un abonnement Azure. Consultez [Azure d’obtenir la version d’évaluation gratuite](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

    > [AZURE.IMPORTANT] Vous n’avez pas besoin d’un cluster existant de HDInsight ; les étapes de ce document va créer les ressources suivantes :
    >
    > * Un réseau virtuel Azure
    > * Une bourrasque sur cluster de HDInsight (basé sur Linux, 2 nœuds de travail)
    > * Un HBase sur HDInsight cluster (basé sur Linux, 2 nœuds de travail)
    > * Une application Web Azure qui héberge le tableau de bord web

* [Node.js](http://nodejs.org/): il est utilisé pour afficher l’aperçu du tableau de bord web localement, sur votre environnement de développement.

* [Java et du JDK 1.7](http://www.oracle.com/technetwork/java/javase/downloads/index.html): permet de développer la topologie tempête.

* [Maven](http://maven.apache.org/what-is-maven.html): permet de générer et de compiler le projet.

* [GIT](http://git-scm.com/): permet de télécharger le projet à partir de GitHub.

* Un client __SSH__ : utilisé pour se connecter aux clusters basés sur Linux de HDInsight. Pour plus d’informations sur l’utilisation de SSH avec HDInsight, consultez les documents suivants.

    * [Utiliser le protocole SSH avec HDInsight à partir d’un client Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

    * [Utiliser le protocole SSH avec HDInsight à partir d’un client Mac, Unix ou Linux](hdinsight-hadoop-linux-use-ssh-unix.md)

    > [AZURE.NOTE] Doit également avoir accès à la `scp` commande, qui est utilisé pour copier des fichiers entre votre environnement de développement local et le cluster de HDInsight à l’aide de SSH.

## <a name="architecture"></a>Architecture

![diagramme d’architecture](./media/hdinsight-storm-sensor-data-analysis/devicesarchitecture.png)

Cet exemple est constitué des composants suivants :

* **Concentrateurs d’événement Azure**: contient des données qui sont collectées à partir des capteurs. Pour cet exemple, une application est la condition qui génère les données.

* **Tempête sur HDInsight**: fournit un traitement en temps réel des données du concentrateur de l’événement.

* **HBase sur HDInsight**: fournit un magasin de données NoSQL permanent des données une fois qu’elle a été traitée par une tempête.

* **Service de réseau virtuel d’Azure**: permet de sécuriser les communications entre la tempête sur HDInsight et HBase sur des clusters de HDInsight.

    > [AZURE.NOTE] Un réseau virtuel est nécessaire pour utiliser l’API cliente Java HBase, tel qu’il n’est pas exposé via la passerelle publique pour les clusters de HBase. L’installation de clusters HBase et Storm au même réseau virtuel permet du cluster Storm (ou tout autre système sur le réseau virtuel,) accéder directement à HBase à l’aide des API du client.

* **Site Web de tableau de bord**: un tableau de bord exemple que les graphiques des données en temps réel.

    * Le site Web est implémenté dans Node.js, afin de l’exécuter sur n’importe quel système d’exploitation de client pour le test, ou il peut être déployé sur des sites Web d’Azure.

    * [Socket.IO](http://socket.io/) est utilisé pour la communication en temps réel entre la topologie de la tempête et le site Web.

        > [AZURE.NOTE] Il s’agit d’un détail d’implémentation. Vous pouvez utiliser n’importe quelle infrastructure de communications, par exemple WebSocket brutes ou SignalR.

    * [D3.js](http://d3js.org/) est utilisé pour représenter les données qui sont envoyées sur le site Web.

> [AZURE.IMPORTANT] Deux clusters sont nécessaires, car il n’existe aucune méthode prise en charge pour créer un cluster d’HDInsight de HBase et Storm.

La topologie lit les données à partir du concentrateur d’événements à l’aide de la classe [org.apache.storm.eventhubs.spout.EventHubSpout](http://storm.apache.org/releases/0.10.1/javadocs/org/apache/storm/eventhubs/spout/class-use/EventHubSpout.html) et écrit les données en HBase à l’aide de la classe [org.apache.storm.hbase.bolt.HBaseBolt](https://storm.apache.org/javadoc/apidocs/org/apache/storm/hbase/bolt/class-use/HBaseBolt.html) . Communication avec le site Web s’effectue à l’aide de [socket.io-client.java](https://github.com/nkzawa/socket.io-client.java).

Voici un diagramme de la topologie.

![diagramme de la topologie](./media/hdinsight-storm-sensor-data-analysis/sensoranalysis.png)

> [AZURE.NOTE] Il s’agit d’un affichage très simplifié de la topologie. Au moment de l’exécution, une instance de chaque composant est créée pour chaque partition pour le concentrateur d’événements en cours de lecture. Ces instances sont réparties entre les nœuds du cluster, et les données sont acheminées entre eux comme suit :
>
> * Données de bec de l’analyseur sont équilibré.
> * Données de l’analyseur, le HBase et le tableau de bord sont regroupées par ID de périphérique, afin que les messages à partir du même périphérique flux toujours au même composant.

### <a name="topology-components"></a>Composants de la topologie

* **Bec de EventHub**: bec est fourni dans le cadre de la version d’Apache tempête 0.10.0 et ultérieures.

    > [AZURE.NOTE] Bec de concentrateurs d’événements utilisé dans cet exemple nécessite une tempête sur la version de cluster HDInsight 3.3 ou 3.4. Pour plus d’informations sur l’utilisation de concentrateurs d’événement avec une ancienne version de Storm sur HDInsight, consultez [événements de processus de concentrateurs d’événement Azure avec tempête sur HDInsight](hdinsight-storm-develop-java-event-hub-topology.md).

* **ParserBolt.java**: les données qui sont émises par un bec JSON brut, et parfois plus d’un événement est émis à la fois. Cet éclair montre comment lire les données émises par bec et émettre à un flux de données sous un tuple qui contient plusieurs champs.

* **DashboardBolt.java**: cette procédure montre comment utiliser la bibliothèque cliente Socket.io pour Java pour envoyer des données temps réel au tableau de bord web.

Cet exemple utilise l’infrastructure de [Flux](https://storm.apache.org/releases/0.10.0/flux.html) , pour la définition de la topologie est contenue dans les fichiers YAML. Il existe deux :

* __no-hbase.yaml__ - utilisation de ce fichier lors du test de la topologie de votre environnement de développement. Il n’utilise pas les composants HBase, dans la mesure où vous ne pouvez pas accéder à l’API de Java HBase à partir de l’extérieur du réseau virtuel dans que du cluster se trouve.

* __avec hbase.yaml__ - utilisation de ce fichier lors du déploiement de la topologie pour le cluster de tempête. Il utilise des composants de HBase dans la mesure où il s’exécute sur le même réseau virtuel que le cluster HBase.

## <a name="prepare-your-environment"></a>Préparation de votre environnement.

Avant d’utiliser cet exemple, vous devez créer un concentrateur d’événements Azure, qui lit la topologie tempête.

### <a name="configure-event-hub"></a>Configurer le concentrateur d’événements

Concentrateur d’événements est la source de données pour cet exemple. Utilisez les étapes suivantes pour créer un nouveau concentrateur d’événements.

1. À partir du [portail Azure](https://portal.azure.com), sélectionnez **+ Nouveau** -> __Internet des objets__ -> __Événement concentrateurs__.

2. Sur la lame de __Créer le Namespace__ , effectuez les tâches suivantes :

    1. Entrez un __nom__ pour l’espace de noms.
    2. Sélectionnez un niveau de tarification. __Base__ est suffisante pour cet exemple.
    3. Sélectionnez __l’abonnement__ d' Azure à utiliser.
    4. Sélectionnez un groupe de ressources existant ou créez-en un nouveau.
    5. Sélectionnez l' __emplacement__ pour le concentrateur de l’événement.
    6. Sélectionnez __Ajouter au tableau de bord__, puis cliquez sur __créer__.

3. Lorsque le processus de création est terminée, la lame de concentrateurs de l’événement pour votre espace de noms s’affiche. À partir de là, sélectionnez __+ Ajouter événement concentrateur__. Sur la lame de __Concentrateur d’événement créer__ , entrez un nom de __sensordata__ et sélectionnez __créer__. Laissez les autres champs les valeurs par défaut.

4. À partir de la blade de concentrateurs de l’événement pour votre espace de noms, sélectionnez __Événement concentrateurs__. Sélectionnez l’entrée __sensordata__ .

5. À partir de la lame pour la sensordata concentrateur d’événements, sélectionnez __stratégies d’accès partagé__. Cliquez sur le lien __+ Ajouter__ pour ajouter les stratégies suivantes :


  	| Nom de la stratégie | Créances |
  	| ----- | ----- |
  	| périphériques | Envoyer |
  	| orage | Écouter |

5. Sélectionnez les deux stratégies et prenez note de la valeur de __Clé primaire__ . Vous aurez besoin de la valeur pour les deux stratégies dans les étapes suivantes.

## <a name="download-and-configure-the-project"></a>Télécharger et configurer le projet

Utilisez ce qui suit pour télécharger le projet à partir de GitHub.

    git clone https://github.com/Blackmist/hdinsight-eventhub-example

Une fois la commande terminée, vous aurez la structure de répertoire suivante :

    hdinsight-eventhub-example/
        TemperatureMonitor/ - this contains the topology
            resources/
                log4j2.xml - set logging to minimal
                no-hbase.yaml - topology definition for local testing
                with-hbase.yaml - topology definition that uses HBase in a virutal network
            src/ - the Java bolts
            dev.properties - contains configuration values for your environment
        dashboard/nodejs/ - this is the node.js web dashboard
        SendEvents/ - utilities to send fake sensor data

> [AZURE.NOTE] Ce document ne va pas tous les détails du code inclus dans cet exemple ; Toutefois, le code est entièrement mis en commentaire.

Ouvrez le fichier **hdinsight-eventhub-example/TemperatureMonitor/dev.properties** et ajoutez vos informations sur le Hub d’événement pour les lignes suivantes :

    eventhub.read.policy.name: storm
    eventhub.read.policy.key: KeyForTheStormPolicy
    eventhub.namespace: YourNamespace
    eventhub.name: sensordata

> [AZURE.NOTE] Cet exemple suppose que vous avez utilisé un __orage__ comme nom de la stratégie qui a une demande __d’écoute__ , et que votre concentrateur d’événements est nommé __sensordata__.

 Enregistrez le fichier une fois que vous ajoutez ces informations.

## <a name="compile-and-test-locally"></a>Compiler et tester localement

Avant l’essai, vous devez démarrer le tableau de bord pour afficher la sortie de la topologie et de générer des données à stocker dans le concentrateur d’événements.

> [AZURE.IMPORTANT] Le composant HBase de cette topologie n’est pas actif lorsque le test localement, comme l’API Java pour le cluster HBase ne sont pas accessibles à partir de l’extérieur du réseau virtuel Azure qui contient les clusters.

### <a name="start-the-web-application"></a>Démarrez l’application web

1. Ouvrez une nouvelle invite de commande ou le terminal, changez les répertoires pour le **hdinsight-eventhub-exemple/tableau de bord**, puis utilisez la commande suivante pour installer les dépendances requises par l’application web :

        npm install

2. Pour démarrer l’application web, utilisez la commande suivante :

        node server.js

    Vous devriez voir un message semblable au suivant :

        Server listening at port 3000

2. Ouvrez un navigateur web et entrez **http://localhost:3000 /** comme adresse. Vous devriez voir une page semblable à la suivante :

    ![tableau de bord Web](./media/hdinsight-storm-sensor-data-analysis/emptydashboard.png)

    Laissez cette invite de commande ou d’un Terminal Server ouvert. Après le test, utilisez Ctrl + C pour arrêter le serveur web.

### <a name="start-generating-data"></a>Démarrer la génération de données

> [AZURE.NOTE] Les étapes de cette section utilisent des Node.js afin qu’ils peuvent être utilisés sur n’importe quelle plate-forme. Pour d’autres exemples de langage, consultez le répertoire **SendEvents** .

1. Ouvrez une nouvelle invite de commande, le shell ou le terminal, changez les répertoires pour **hdinsight-eventhub-exemple/SendEvents/nodejs**, puis utilisez la commande suivante pour installer les dépendances requises par l’application :

        npm install

2. Ouvrez le fichier **app.js** dans un éditeur de texte et ajoutez les informations de concentrateur d’événements que vous avez obtenu précédemment :

        // ServiceBus Namespace
        var namespace = 'YourNamespace';
        // Event Hub Name
        var hubname ='sensordata';
        // Shared access Policy name and key (from Event Hub configuration)
        var my_key_name = 'devices';
        var my_key = 'YourKey';
    
    > [AZURE.NOTE] Cet exemple suppose que vous avez utilisé __sensordata__ comme nom de votre événement concentrateur, __périphériques__ et que le nom de la stratégie qui a une demande __d’envoi__ .

2. Pour insérer de nouvelles entrées dans le concentrateur d’événements, utilisez la commande suivante :

        node app.js

    Vous devriez voir plusieurs lignes de sortie qui contiennent les données envoyées au concentrateur d’événements. Elles apparaissent comme suit :

        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"0","Temperature":7}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"1","Temperature":39}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"2","Temperature":86}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"3","Temperature":29}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"4","Temperature":30}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"5","Temperature":5}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"6","Temperature":24}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"7","Temperature":40}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"8","Temperature":43}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"9","Temperature":84}

### <a name="start-the-topology"></a>Démarrer la topologie

2. Ouvrez une nouvelle invite de commande, coque ou terminal et modification des répertoires pour __Hdinsight-eventhub-exemple de/TemperatureMonitor__, puis utilisez la commande suivante pour démarrer la topologie :

        mvn compile exec:java -Dexec.args="--local -R /no-hbase.yaml --filter dev.properties"
    
    Si vous utilisez PowerShell, utilisez plutôt la suivante :

        mvn compile exec:java "-Dexec.args=--local -R /no-hbase.yaml --filter dev.properties"

    > [AZURE.NOTE] Si vous êtes sur un système Linux/Unix/OS X et disposez de [tempête dans votre environnement de développement](http://storm.apache.org/releases/0.10.0/Setting-up-development-environment.html), vous pouvez utiliser les commandes suivantes à la place :
    >
    > `mvn compile package`
    > `storm jar target/WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local -R /no-hbase.yaml`

    Démarre la topologie définie dans le fichier __n°-hbase.yaml__ en mode local. Les valeurs contenues dans le fichier __dev.properties__ fournissent les informations de connexion pour les concentrateurs d’événement. Une fois démarré, la topologie lit les entrées à partir du concentrateur de l’événement et les envoie au tableau de bord en cours d’exécution sur votre ordinateur local. Vous devriez voir des lignes s’affichent dans le tableau de bord web, semblable à la suivante :

    ![tableau de bord avec des données](./media/hdinsight-storm-sensor-data-analysis/datadashboard.png)

3. Alors que le tableau de bord est en cours d’exécution, utilisez la `node app.js` commande les étapes précédentes pour envoyer de nouvelles données à des concentrateurs de l’événement. Étant donné que les valeurs de température sont générés de façon aléatoire, le graphique doit mettre à jour pour indiquer les changements importants de température.

    > [AZURE.NOTE] Vous devez être dans le répertoire __Hdinsight-eventhub-exemple/SendEvents/Nodejs__ lorsque vous utilisez la `node app.js` commande.

3. Après avoir vérifié que cela fonctionne, arrêtez la topologie à l’aide de Ctrl + C. Vous pouvez utiliser Ctrl + C pour arrêter le serveur web local également.

## <a name="create-a-storm-and-hbase-cluster"></a>Créer un cluster tempête et HBase

Pour exécuter la topologie sur HDInsight et le boulon de HBase, vous devez créer un nouveau cluster tempête et le cluster de HBase. Les étapes de cette section utilisent un [modèle de gestionnaire de ressources Azure](../resource-group-template-deploy.md) pour créer un cluster de réseau virtuel d’Azure et d’une tempête et HBase sur le réseau virtuel. Le modèle crée une application Web de Azure également et déploie une copie du tableau de bord dans celui-ci.

> [AZURE.NOTE] Un réseau virtuel est utilisé afin que la topologie en cours d’exécution sur le cluster de tempête peut communiquer directement avec le cluster de HBase à l’aide de l’API Java HBase.

Le modèle de gestionnaire de ressources utilisé dans ce document se trouve dans un conteneur de blob publique à __https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-hbase-storm-cluster-in-vnet.json__.

1. Cliquez sur le bouton suivant pour vous connecter à Azure et ouvrez le modèle de gestionnaire de ressources dans le portail Azure.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hbase-storm-cluster-in-vnet.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. À partir de la blade de **paramètres** , entrez les informations suivantes :

    ![Paramètres de HDInsight](./media/hdinsight-storm-sensor-data-analysis/parameters.png)
    
    * **BASECLUSTERNAME**: cette valeur sera utilisée comme nom de base de la tempête et des clusters HBase. Par exemple, une saisie __hdi__ créera un tempête nommé __hdi-tempête__ et le cluster un HBase nommé __hbase-hdi__.
    * __CLUSTERLOGINUSERNAME__: le nom d’utilisateur admin pour les clusters de tempête et HBase.
    * __CLUSTERLOGINPASSWORD__: le mot de passe administrateur pour les clusters de tempête et HBase.
    * __SSHUSERNAME__: le SSH utilisateur de créer pour les clusters de tempête et HBase.
    * __SSHPASSWORD__: le mot de passe pour l’utilisateur SSH pour les clusters de tempête et HBase.
    * __Emplacement__: la zone qui seront créées dans les clusters.
    
    Cliquez sur __OK__ pour enregistrer les paramètres.
    
3. La section de __groupe de ressources__ permet de créer un nouveau groupe de ressources ou sélectionnez-en une existante.

4. Dans le menu déroulant __emplacement de groupe de ressources__ , sélectionnez le même emplacement que vous avez sélectionné pour le paramètre __d’emplacement__ .

5. Permet de sélectionner __les conditions juridiques__et sélectionnez __créer__.

6. Enfin, vérifiez le __code confidentiel pour le tableau de bord__ et sélectionnez __créer__. Il prendra environ 20 minutes pour créer les clusters.

Une fois que les ressources ont été créés, vous serez redirigé vers une lame pour le groupe de ressources contenant les clusters et le tableau de bord web.

![Lame de groupe de ressources de la vnet et des clusters](./media/hdinsight-storm-sensor-data-analysis/groupblade.png)

> [AZURE.IMPORTANT] Notez que les noms des clusters HDInsight __BASENAME tempête__ et __hbase-BASENAME__, où NOMDEBASE est le nom que vous avez fourni pour le modèle. Lors de la connexion sur les clusters, vous allez utiliser ces noms dans les étapes ultérieures. Notez également que le nom du site du tableau de bord est __basename-tableau de bord__. Vous utiliserez ce ultérieurement lors de l’affichage du tableau de bord.

## <a name="configure-the-dashboard-bolt"></a>Configurer le boulon de tableau de bord

Pour envoyer des données au tableau de bord déployé sous la forme d’une application web, vous devez modifier la ligne suivante dans le fichier __dev.properties__ :

    dashboard.uri: http://localhost:3000

Modification de `http://localhost:3000` à `http://BASENAME-dashboard.azurewebsites.net` et enregistrez le fichier. Remplacez le __nom de base__ avec le nom de base que vous avez fourni à l’étape précédente. Vous pouvez également utiliser le groupe de ressources créé précédemment pour sélectionner le tableau de bord et affichage de l’URL.

## <a name="create-the-hbase-table"></a>Créer la table HBase

Pour stocker des données dans HBase, nous devons d’abord créer une table. Vous souhaitez généralement créer préalablement les ressources tempête doit écrire dans, comme la création de ressources à l’intérieur d’une topologie de tempête peuvent entraîner plusieurs copies distribuées du code essayant de créer la même ressource. Créer les ressources à l’extérieur de la topologie et simplement utiliser tempête de lecture/écriture et analytique.

1. Utiliser le protocole SSH pour se connecter au cluster HBase à l’aide de l’utilisateur SSH et le mot de passe fourni dans le modèle lors de la création du cluster. Par exemple, si la connexion à l’aide de la `ssh` de commande, vous utiliseriez la syntaxe suivante :

        ssh USERNAME@hbase-BASENAME-ssh.azurehdinsight.net
    
    Dans cette commande, remplacez le __nom d’utilisateur__ avec le nom d’utilisateur SSH fourni lors de la création du cluster et __nom de base__ avec le nom de base que vous avez fournies. Lorsque vous y êtes invité, entrez le mot de passe pour l’utilisateur SSH.

2. À partir de la session SSH, démarrez le shell HBase.

        hbase shell
    
    Une fois que le shell a chargé, vous verrez une `hbase(main):001:0>` invite.

3. À partir du shell HBase, entrez la commande suivante pour créer une table pour stocker les données du capteur :

        create 'SensorData', 'cf'

4. Vérifiez que la table a été créée à l’aide de la commande suivante :

        scan 'SensorData'
        
    Ceci renvoie des informations semblables à l’exemple suivant, qui indique qu’il y a des 0 lignes dans la table.
    
        ROW                   COLUMN+CELL                                       0 row(s) in 0.1900 seconds

5. Pour quitter l’interpréteur de commandes HBase, saisissez :

        exit

## <a name="configure-the-hbase-bolt"></a>Configurer le boulon de HBase

Pour écrire à HBase à partir du cluster de la tempête, vous devez fournir le boulon de HBase avec les détails de la configuration de votre cluster de HBase. Le moyen le plus simple de procéder consiste à les télécharger le __hbase-site.xml__ du cluster et l’inclure dans votre projet. Vous devez également retirer le commentaire plusieurs dépendances dans le fichier __pom.xml__ , qui chargement le composant de la tempête-hbase et les dépendances obligatoires.

> [AZURE.IMPORTANT] Vous devez également télécharger le fichier hbase.jar-tempête fourni sur votre tempête sur HDInsight 3.3 ou 3.4 cluster ; Cette version est compilée pour travailler avec HBase 1.1.x, qui est utilisé pour HBase sur HDInsight 3.3 et 3.4 clusters. Si vous utilisez un composant tempête-hbase à partir d’un autre emplacement, il peut être compilé avec une version plus ancienne de HBase.

### <a name="download-the-hbase-sitexml"></a>Téléchargez le hbase-site.xml

À partir d’une invite de commandes, utilisez SCP pour télécharger le fichier __hbase-site.xml__ du cluster. Dans l’exemple suivant, remplacez le __nom d’utilisateur__ avec l’utilisateur SSH que vous avez fourni lors de la création du cluster et __nom de base__ avec le nom de base que vous avez spécifié précédemment. Lorsque vous y êtes invité, entrez le mot de passe pour l’utilisateur SSH. Remplacer le `/path/to/TemperatureMonitor/resources/hbase-site.xml` avec le chemin d’accès à ce fichier dans le projet TemperatureMonitor.

    scp USERNAME@hbase-BASENAME-ssh.azurehdinsight.net:/etc/hbase/conf/hbase-site.xml /path/to/TemperatureMonitor/resources/hbase-site.xml

Le __hbase-site.xml__ est alors téléchargée vers le chemin d’accès spécifié.

### <a name="download-and-install-the-storm-hbase-component"></a>Téléchargez et installez le composant de tempête-hbase

1. À partir d’une invite de commandes, utilisez SCP pour télécharger le fichier __hbase.jar-tempête__ du cluster tempête. Dans l’exemple suivant, remplacez le __nom d’utilisateur__ avec l’utilisateur SSH que vous avez fourni lors de la création du cluster et __nom de base__ avec le nom de base que vous avez spécifié précédemment. Lorsque vous y êtes invité, entrez le mot de passe pour l’utilisateur SSH.

        scp USERNAME@storm-BASENAME-ssh.azurehdinsight.net:/usr/hdp/current/storm-client/contrib/storm-hbase/storm-hbase*.jar .

    Il télécharge un fichier nommé `storm-hbase-####.jar`, où ### est le numéro de version de Storm pour ce cluster. Prenez note de ce numéro, tel qu’il est utilisé ultérieurement.

2. Utilisez la commande suivante pour installer ce composant dans le référentiel Maven local sur votre environnement de développement. Cela permet de Maven trouver le package lors de la compilation du projet. Remplacer __####__ avec le numéro de version inclus dans le nom de fichier.

        mvn install:install-file -Dfile=storm-hbase-####.jar -DgroupId=org.apache.storm -DartifactId=storm-hbase -Dversion=#### -Dpackaging=jar
    
    Si vous utilisez PowerShell, utilisez la commande suivante :

        mvn install:install-file "-Dfile=storm-hbase-####.jar" "-DgroupId=org.apache.storm" "-DartifactId=storm-hbase" "-Dversion=####" "-Dpackaging=jar"

### <a name="enable-the-storm-hbase-component-in-the-project"></a>Activer le composant de tempête-hbase dans le projet

1. Ouvrez le fichier __TemperatureMonitor/pom.xml__ et supprimez les lignes suivantes :

        <!-- uncomment this section to enable the hbase-bolt
        end comment for hbase-bolt section -->
    
    > [AZURE.IMPORTANT] Ne supprimer ces deux lignes ; ne supprimez pas les lignes entre eux.
    
    Cela permet à plusieurs composants qui sont nécessaires lors de la communication avec HBase à l’aide de boulon hbase.

2. Recherchez les lignes suivantes et remplacer __####__ avec le numéro de version du fichier hbase-tempête téléchargé précédemment.

        <dependency>
            <groupId>org.apache.storm</groupId>
            <artifactId>storm-hbase</artifactId>
            <version>####</version>
        </dependency>

    > [AZURE.IMPORTANT] Le numéro de version doit correspondre à la version que vous avez utilisé lors de l’installation du composant dans le référentiel Maven local, comme Maven utilise ces informations pour charger le composant lors de la génération du projet.

2. Enregistrez le fichier __pom.xml__ .

## <a name="build-package-and-deploy-the-solution-to-hdinsight"></a>Package, générer et déployer la solution vers HDInsight

Dans votre environnement de développement, procédez comme suit pour déployer la topologie tempête au cluster tempête.

1. À partir du répertoire __TemperatureMonitor__ , utilisez la commande suivante pour effectuer une nouvelle build et créer un package de fichier JAR à partir de votre projet :

        mvn clean compile package

    Cela créera un fichier nommé **TemperatureMonitor-1.0-SNAPSHOT.jar** dans le répertoire **cible** de votre projet.

2. Scp permet de télécharger le fichier __TemperatureMonitor-1.0-SNAPSHOT.jar__ à votre cluster tempête. Dans l’exemple suivant, remplacez le __nom d’utilisateur__ avec l’utilisateur SSH que vous avez fourni lors de la création du cluster et __nom de base__ avec le nom de base que vous avez spécifié précédemment. Lorsque vous y êtes invité, entrez le mot de passe pour l’utilisateur SSH.

        scp target\TemperatureMonitor-1.0-SNAPSHOT.jar USERNAME@storm-BASENAME-ssh.azurehdinsight.net:TemperatureMonitor-1.0-SNAPSHOT.jar
    
    > [AZURE.NOTE] Elle peut prendre plusieurs minutes pour télécharger le fichier, car il sera de plusieurs méga-octets.

    Scp permet de télécharger le fichier __dev.properties__ , car il contient les informations utilisées pour se connecter à des concentrateurs d’événement et du tableau de bord.

        scp dev.properties USERNAME@storm-BASENAME-ssh.azurehdinsight.net:dev.properties

3. Une fois que les fichiers ont été téléchargés, connectez-vous au cluster à l’aide de SSH.

        ssh USERNAME@storm-BASENAME-ssh.azurehdinsight.net

4. À partir de la session SSH, utilisez la commande suivante pour démarrer la topologie.

        storm jar TemperatureMonitor-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /with-hbase.yaml --filter dev.properties
    
    Démarre la topologie à l’aide de la définition de topologie dans le fichier __avec hbase.yaml__ et les valeurs de configuration dans le fichier __dev.properties__ .

3. Après le démarrage de la topologie, ouvrez un navigateur sur le site Web que vous avez publié sur Azure, puis utiliser le `node app.js` commande à envoyer des données au concentrateur d’événements. Vous devez voir le tableau de bord web mise à jour pour afficher les informations.

    ![tableau de bord](./media/hdinsight-storm-sensor-data-analysis/datadashboard.png)

## <a name="view-hbase-data"></a>Afficher les données de HBase

Après l’envoi de données à la topologie à l’aide de `node app.js`, procédez comme suit pour vous connecter à HBase et vérifiez que les données ont été écrites dans la table que vous avez créé précédemment.

1. Utiliser le protocole SSH pour se connecter au cluster HBase.

        ssh USERNAME@hbase-BASENAME-ssh.azurehdinsight.net

2. À partir de la session SSH, démarrez le shell HBase.

        hbase shell
    
    Une fois que le shell a chargé, vous verrez une `hbase(main):001:0>` invite.

2. Afficher les lignes de la table :

        scan 'SensorData'
        
    Cela doit retourner des informations similaires à la suivante, qui indique qu’il y a des 0 lignes dans la table.
    
        hbase(main):002:0> scan 'SensorData'
        ROW                             COLUMN+CELL
        \x00\x00\x00\x00               column=cf:temperature, timestamp=1467290788277, value=\x00\x00\x00\x04
        \x00\x00\x00\x00               column=cf:timestamp, timestamp=1467290788277, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x01               column=cf:temperature, timestamp=1467290788348, value=\x00\x00\x00M
        \x00\x00\x00\x01               column=cf:timestamp, timestamp=1467290788348, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x02               column=cf:temperature, timestamp=1467290788268, value=\x00\x00\x00R
        \x00\x00\x00\x02               column=cf:timestamp, timestamp=1467290788268, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x03               column=cf:temperature, timestamp=1467290788269, value=\x00\x00\x00#
        \x00\x00\x00\x03               column=cf:timestamp, timestamp=1467290788269, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x04               column=cf:temperature, timestamp=1467290788356, value=\x00\x00\x00>
        \x00\x00\x00\x04               column=cf:timestamp, timestamp=1467290788356, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x05               column=cf:temperature, timestamp=1467290788326, value=\x00\x00\x00\x0D
        \x00\x00\x00\x05               column=cf:timestamp, timestamp=1467290788326, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x06               column=cf:temperature, timestamp=1467290788253, value=\x00\x00\x009
        \x00\x00\x00\x06               column=cf:timestamp, timestamp=1467290788253, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x07               column=cf:temperature, timestamp=1467290788229, value=\x00\x00\x00\x12
        \x00\x00\x00\x07               column=cf:timestamp, timestamp=1467290788229, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x08               column=cf:temperature, timestamp=1467290788336, value=\x00\x00\x00\x16
        \x00\x00\x00\x08               column=cf:timestamp, timestamp=1467290788336, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x09               column=cf:temperature, timestamp=1467290788246, value=\x00\x00\x001
        \x00\x00\x00\x09               column=cf:timestamp, timestamp=1467290788246, value=2015-02-10T14:43.05.00320Z
        10 row(s) in 0.1800 seconds

    > [AZURE.NOTE] Cette opération d’analyse retourne uniquement un maximum de 10 lignes de la table.

## <a name="delete-your-clusters"></a>Supprimer vos clusters

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Pour supprimer les clusters, le stockage et l’application web à un moment donné, supprimer le groupe de ressources qui les contient.

## <a name="next-steps"></a>Étapes suivantes

Vous avez désormais appris à Storm permet de lire les données d’événement concentrateurs, stockez-le dans HBase et visualiser les informations sur un tableau de bord externe à l’aide de Socket.io et D3.js.

* Pour plus d’exemples de topologies de tempête avec HDInsight, voir :

    * [Exemples de topologies pour tempête sur HDInsight](hdinsight-storm-example-topology.md)

* Pour plus d’informations sur Apache tempête, consultez le site de [Tempête d’Apache](https://storm.incubator.apache.org/) .

* Pour plus d’informations sur HBase sur HDInsight, reportez-vous à la [HBase vue d’ensemble de HDInsight](hdinsight-hbase-overview.md).

* Pour plus d’informations sur Socket.io, consultez le site [socket.io](http://socket.io/) .

* Pour plus d’informations sur D3.js, reportez-vous à la section [D3.js - Documents de piloté par des données](http://d3js.org/).

* Pour plus d’informations sur la création de topologies de Java, reportez-vous à la section [Java de développer des topologies de tempête Apache sur HDInsight](hdinsight-storm-develop-java-topology.md).

* Pour plus d’informations sur la création de topologies dans .NET, reportez-vous à la section [développement C# topologies de tempête Apache sur HDInsight à l’aide de Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md).

[azure-portal]: https://portal.azure.com
