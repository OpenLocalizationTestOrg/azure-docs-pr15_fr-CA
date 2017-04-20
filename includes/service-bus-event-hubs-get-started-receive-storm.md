## <a name="receive-messages-with-apache-storm"></a>Recevoir des messages avec Apache tempête

[**Apache Storm**](https://storm.incubator.apache.org) est un système distribué de calcul en temps réel qui simplifie le traitement fiable de flux illimitées de données. Cette section indique comment utiliser un bec de vagues de concentrateurs d’événements pour recevoir des événements de l’événement concentrateurs. À l’aide de Apache tempête, vous pouvez fractionner des événements entre plusieurs processus hébergés dans des nœuds différents. L’intégration de concentrateurs d’événement avec la tempête simplifie la consommation des événements par vérification de façon transparente sa progression de l’installation de soigneur de tempête, la gestion des points de contrôle permanents et parallèle reçoit de concentrateurs de l’événement.

Pour plus d’informations sur les concentrateurs d’événement réception des modèles, consultez la [vue d’ensemble des concentrateurs d’événement][].

Ce didacticiel utilise une installation [Tempête de HDInsight][] , qui est fourni avec le bec de concentrateurs d’événement déjà disponible.

1. Suivez la procédure de [Tempête HDInsight - mise en route](../articles/hdinsight/hdinsight-storm-overview.md) pour créer un nouveau cluster de HDInsight et s’y connecter via le Bureau à distance.

2. Copier le `%STORM_HOME%\examples\eventhubspout\eventhubs-storm-spout-0.9-jar-with-dependencies.jar` le fichier à votre environnement de développement local. Il contient les événements-tempête-bec.

3. Utilisez la commande suivante pour installer le package dans le magasin local Maven. Cela vous permet de l’ajouter en tant que référence dans le projet de la tempête dans une étape ultérieure.

        mvn install:install-file -Dfile=target\eventhubs-storm-spout-0.9-jar-with-dependencies.jar -DgroupId=com.microsoft.eventhubs -DartifactId=eventhubs-storm-spout -Dversion=0.9 -Dpackaging=jar

4. Dans Eclipse, créez un nouveau projet Maven (cliquez sur **fichier**, **Nouveau**, puis **projet**).

    ![][12]

5. Sélectionnez **l’emplacement d’espace de travail utilisé par défaut**, puis cliquez sur **suivant**

6. Sélectionnez l’archétype **maven-archétype-démarrage rapide** , puis cliquez sur **suivant**

7. Insérer un **GroupId** et un **ArtifactId**, puis cliquez sur **Terminer**

8. Dans **pom.xml**, ajoutez les dépendances suivantes dans le `<dependency>` nœud.

        <dependency>
            <groupId>org.apache.storm</groupId>
            <artifactId>storm-core</artifactId>
            <version>0.9.2-incubating</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>com.microsoft.eventhubs</groupId>
            <artifactId>eventhubs-storm-spout</artifactId>
            <version>0.9</version>
        </dependency>
        <dependency>
            <groupId>com.netflix.curator</groupId>
            <artifactId>curator-framework</artifactId>
            <version>1.3.3</version>
            <exclusions>
                <exclusion>
                    <groupId>log4j</groupId>
                    <artifactId>log4j</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>org.slf4j</groupId>
                    <artifactId>slf4j-log4j12</artifactId>
                </exclusion>
            </exclusions>
            <scope>provided</scope>
        </dependency>

9. Dans le dossier **src** , créez un fichier appelé **config.Properties** et copiez le contenu suivant, en remplaçant les valeurs suivantes :

        eventhubspout.username = ReceiveRule

        eventhubspout.password = {receive rule key}

        eventhubspout.namespace = ioteventhub-ns

        eventhubspout.entitypath = {event hub name}

        eventhubspout.partitions.count = 16

        # if not provided, will use storm's zookeeper settings
        # zookeeper.connectionstring=localhost:2181

        eventhubspout.checkpoint.interval = 10

        eventhub.receiver.credits = 10

    La valeur de **eventhub.receiver.credits** détermine le nombre d’événements est regroupés par lots avant de les introduire dans le pipeline de tempête. Par souci de simplicité, cet exemple définit cette valeur à 10. En production, il doit généralement être défini à des valeurs plus élevées ; par exemple, 1024.

10. Créer une nouvelle classe appelée **LoggerBolt** par le code suivant :

        import java.util.Map;
        import org.slf4j.Logger;
        import org.slf4j.LoggerFactory;
        import backtype.storm.task.OutputCollector;
        import backtype.storm.task.TopologyContext;
        import backtype.storm.topology.OutputFieldsDeclarer;
        import backtype.storm.topology.base.BaseRichBolt;
        import backtype.storm.tuple.Tuple;

        public class LoggerBolt extends BaseRichBolt {
            private OutputCollector collector;
            private static final Logger logger = LoggerFactory
                      .getLogger(LoggerBolt.class);

            @Override
            public void execute(Tuple tuple) {
                String value = tuple.getString(0);
                logger.info("Tuple value: " + value);

                collector.ack(tuple);
            }

            @Override
            public void prepare(Map map, TopologyContext context, OutputCollector collector) {
                this.collector = collector;
                this.count = 0;
            }

            @Override
            public void declareOutputFields(OutputFieldsDeclarer declarer) {
                // no output fields
            }

        }

    Cet éclair tempête enregistre le contenu des événements reçus. Ceci peut être facilement étendu pour stocker des tuples dans un service de stockage. Le [didacticiel d’analysis HDInsight capteur] utilise cette approche pour stocker des données dans HBase.

11. Créez une classe appelée **LogTopology** par le code suivant :

        import java.io.FileReader;
        import java.util.Properties;
        import backtype.storm.Config;
        import backtype.storm.LocalCluster;
        import backtype.storm.StormSubmitter;
        import backtype.storm.generated.StormTopology;
        import backtype.storm.topology.TopologyBuilder;
        import com.microsoft.eventhubs.samples.EventCount;
        import com.microsoft.eventhubs.spout.EventHubSpout;
        import com.microsoft.eventhubs.spout.EventHubSpoutConfig;

        public class LogTopology {
            protected EventHubSpoutConfig spoutConfig;
            protected int numWorkers;

            protected void readEHConfig(String[] args) throws Exception {
                Properties properties = new Properties();
                if (args.length > 1) {
                    properties.load(new FileReader(args[1]));
                } else {
                    properties.load(EventCount.class.getClassLoader()
                            .getResourceAsStream("Config.properties"));
                }

                String username = properties.getProperty("eventhubspout.username");
                String password = properties.getProperty("eventhubspout.password");
                String namespaceName = properties
                        .getProperty("eventhubspout.namespace");
                String entityPath = properties.getProperty("eventhubspout.entitypath");
                String zkEndpointAddress = properties
                        .getProperty("zookeeper.connectionstring"); // opt
                int partitionCount = Integer.parseInt(properties
                        .getProperty("eventhubspout.partitions.count"));
                int checkpointIntervalInSeconds = Integer.parseInt(properties
                        .getProperty("eventhubspout.checkpoint.interval"));
                int receiverCredits = Integer.parseInt(properties
                        .getProperty("eventhub.receiver.credits")); // prefetch count
                                                                    // (opt)
                System.out.println("Eventhub spout config: ");
                System.out.println("  partition count: " + partitionCount);
                System.out.println("  checkpoint interval: "
                        + checkpointIntervalInSeconds);
                System.out.println("  receiver credits: " + receiverCredits);

                spoutConfig = new EventHubSpoutConfig(username, password,
                        namespaceName, entityPath, partitionCount, zkEndpointAddress,
                        checkpointIntervalInSeconds, receiverCredits);

                // set the number of workers to be the same as partition number.
                // the idea is to have a spout and a logger bolt co-exist in one
                // worker to avoid shuffling messages across workers in storm cluster.
                numWorkers = spoutConfig.getPartitionCount();

                if (args.length > 0) {
                    // set topology name so that sample Trident topology can use it as
                    // stream name.
                    spoutConfig.setTopologyName(args[0]);
                }
            }

            protected StormTopology buildTopology() {
                TopologyBuilder topologyBuilder = new TopologyBuilder();

                EventHubSpout eventHubSpout = new EventHubSpout(spoutConfig);
                topologyBuilder.setSpout("EventHubsSpout", eventHubSpout,
                        spoutConfig.getPartitionCount()).setNumTasks(
                        spoutConfig.getPartitionCount());
                topologyBuilder
                        .setBolt("LoggerBolt", new LoggerBolt(),
                                spoutConfig.getPartitionCount())
                        .localOrShuffleGrouping("EventHubsSpout")
                        .setNumTasks(spoutConfig.getPartitionCount());
                return topologyBuilder.createTopology();
            }

            protected void runScenario(String[] args) throws Exception {
                boolean runLocal = true;
                readEHConfig(args);
                StormTopology topology = buildTopology();
                Config config = new Config();
                config.setDebug(false);

                if (runLocal) {
                    config.setMaxTaskParallelism(2);
                    LocalCluster localCluster = new LocalCluster();
                    localCluster.submitTopology("test", config, topology);
                    Thread.sleep(5000000);
                    localCluster.shutdown();
                } else {
                    config.setNumWorkers(numWorkers);
                    StormSubmitter.submitTopology(args[0], config, topology);
                }
            }

            public static void main(String[] args) throws Exception {
                LogTopology topology = new LogTopology();
                topology.runScenario(args);
            }
        }


    Cette classe crée un nouvel événement les concentrateurs bec, en utilisant les propriétés de la configuration du fichier à instatiate. Il est important de noter que cet exemple crée autant de tâches de becs verseurs comme le nombre de partitions dans le concentrateur d’événements, pour pouvoir utiliser le parallélisme maximal autorisé par ce concentrateur d’événements.

<!-- Links -->
[Vue d’ensemble des concentrateurs événements]: ../articles/event-hubs/event-hubs-overview.md
[Tempête de HDInsight]: ../articles/hdinsight/hdinsight-storm-overview.md
[Didacticiel d’analysis HDInsight capteur]: ../articles/hdinsight/hdinsight-storm-sensor-data-analysis.md

<!-- Images -->

[12]: ./media/service-bus-event-hubs-get-started-receive-storm/create-storm1.png