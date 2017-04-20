<properties
   pageTitle="Traiter les événements de concentrateurs d’événement avec la tempête sur HDInsight à l’aide de Java | Azure"
   description="Découvrez comment traiter les données d’événement concentrateurs avec une topologie Java tempête créée avec Maven."
   services="hdinsight,notification hubs"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/11/2016"
   ms.author="larryfr"/>

# <a name="process-events-from-azure-event-hubs-with-storm-on-hdinsight-java"></a>Traiter les événements de concentrateurs d’événement Azure avec tempête sur HDInsight (Java)

Azure concentrateurs d’événements vous permet de traiter des volumes considérables de données à partir de sites Web, des applications et des périphériques. Bec de concentrateurs d’événement rend facile à utiliser Apache tempête sur HDInsight pour analyser ces données en temps réel. Vous pouvez également écrire des données à des concentrateurs d’événement à partir de la tempête à l’aide du boulon de concentrateurs de l’événement.

Dans ce didacticiel, vous allez apprendre comment utiliser l’événement concentrateurs bec et boulon pour lire et écrire des données dans une topologie basée sur Java de tempête.

## <a name="prerequisites"></a>Conditions préalables

* Une tempête d’Apache sur cluster de HDInsight. Utilisez une de la mise en route suivantes démarré articles pour créer un cluster :

    - Une [bourrasque de basé sur Linux sur HDInsight cluster](hdinsight-apache-storm-tutorial-get-started-linux.md): sélectionnez cette option si vous souhaitez utiliser le protocole SSH pour travailler avec le cluster à partir de clients Windows, OS X, Linux ou Unix

    - Une [bourrasque de basés sur Windows sur le cluster de HDInsight](hdinsight-apache-storm-tutorial-get-started.md): sélectionnez cette option si vous souhaitez utiliser PowerShell pour travailler avec le cluster à partir d’un client Windows

    > [AZURE.NOTE] Les étapes décrites dans ce document sont basés sur l’utilisation d’une tempête sur cluster HDInsight 3.3 ou 3.4. Ces clusters fournissent une tempête 0.10.0 et Hadoop 2.7, qui réduisent le nombre d’étapes requises pour obtenir ce travail d’exemple.
    >
    > Pour une version de cet exemple qui utilise tempête 0.9.3 sur HDInsight 3.2, consultez la branche [tempête v0.9.3](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub/tree/Storm_v0.9.3) du référentiel de l’exemple.

* Un [concentrateur d’événements Azure](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

* [Les Kit de développeur Oracle Java (JDK) version 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) ou équivalent, par exemple [OpenJDK](http://openjdk.java.net/)

* [Maven](https://maven.apache.org/download.cgi): Maven est un système de génération de projet pour les projets de Java

* Un éditeur de texte ou d’un environnement de développement intégré (IDE) de Java

    > [AZURE.NOTE] Votre éditeur ou IDE peut avoir des fonctionnalités spécifiques pour l’utilisation de Maven qui n’est pas traitée dans ce document. Pour plus d’informations sur les capacités de votre environnement d’édition, consultez la documentation pour le produit que vous utilisez.

 * Un client SSH. Consultez les articles suivants pour plus d’informations sur l’utilisation de SSH avec HDInsight :

    - [Utiliser le protocole SSH avec basé sur Linux d’Hadoop sur HDInsight à partir d’OS X, Unix ou Linux](hdinsight-hadoop-linux-use-ssh-unix.md)

    - [Utiliser le protocole SSH avec basé sur Linux d’Hadoop sur HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

* Un client SCP. Ceci est fourni avec tous les systèmes Linux, Unix et OS X. Pour les clients Windows, nous vous recommandons de PSCP, qui est disponible à partir de la [page de téléchargement de PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

##<a name="understanding-the-example"></a>Présentation de l’exemple

L’exemple de [hdinsight-java-tempête-eventhub](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub) contient deux topologies :

__com.microsoft.example.EventHubWriter__ écrit des données aléatoires à un concentrateur d’événements Azure. Les données sont générées par un bec et sont un ID de périphérique aléatoire et une valeur de périphérique. Il simule donc certains matériels qui émet un ID de chaîne et une valeur numérique.

__com.microsoft.example.EventHubReader__ lit les données d’événement Hub (les données écrites par EventHubWriter) et le stocke dans le répertoire /devicedata à très (WASB dans ce cas, dans la mesure où il a été écrit et testé avec Azure HDInsight).

Les données sont formatées en tant que JSON document avant d’être écrite dans le concentrateur d’événements et lorsqu’ils sont lus par le lecteur il est analysé dans des tuples et JSON. Le format JSON est la suivante :

    { "deviceId": "unique identifier", "deviceValue": some value }

La raison de l’utilisation d’un document JSON pour stocker les données sur le concentrateur d’événements est pour que nous sachions quelle est au format, plutôt que d’utiliser le mécanisme interne de mise en forme de BEC VERSEUR de concentrateur événement et boulon.

###<a name="project-configuration"></a>Configuration de projet

Le fichier **POM.xml** contient des informations de configuration pour ce projet Maven. Les éléments intéressants sont les suivantes :

####<a name="the-eventhubs-storm-spout-dependency"></a>La dépendance de BEC VERSEUR de tempête EventHubs

    <dependency>
      <groupId>org.apache.storm</groupId>
      <artifactId>storm-eventhubs</artifactId>
      <version>0.10.0</version>
    </dependency>

Cette opération ajoute une dépendance pour le package de la tempête-eventhubs, qui contient à la fois un BEC VERSEUR pour lire à partir de l’événement concentrateurs et un boulon de l’écriture dans celle-ci.

> [AZURE.NOTE] Ce package n’est disponible pour la tempête, version 0.10.0 et ultérieures. Lors de l’utilisation de tempête 0.9.3, vous devez installer manuellement le package VERSEUR fourni par Microsoft. Pour obtenir un exemple de l’utilisation de tempête 0.9.3, consultez la branche [tempête v0.9.3](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub/tree/Storm_v0.9.3) du référentiel de l’exemple.

####<a name="the-hdfsbolt-and-wasb-components"></a>Les composants HdfsBolt et WASB

Le HdfsBolt est normalement utilisée pour stocker des données à la très de système de fichier distribué Hadoop. Toutefois les clusters HDInsight utilisent stockage Azure (WASB) en tant que la banque de données par défaut, donc nous charger plusieurs composants qui permettent de comprendre le système de fichiers WASB HdfsBolt.

      <!--HdfsBolt stuff -->
        <dependency>
        <groupId>org.apache.storm</groupId>
        <artifactId>storm-hdfs</artifactId>
        <exclusions>
            <exclusion>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-client</artifactId>
            </exclusion>
            <exclusion>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-hdfs</artifactId>
            </exclusion>
        </exclusions>
        <version>0.10.0</version>
        </dependency>
    <!--So HdfsBolt knows how to talk to WASB -->
    <dependency>
        <groupId>org.apache.hadoop</groupId>
        <artifactId>hadoop-client</artifactId>
        <version>2.7.1</version>
    </dependency>
    <dependency>
        <groupId>org.apache.hadoop</groupId>
        <artifactId>hadoop-hdfs</artifactId>
        <version>2.7.1</version>
    </dependency>
    <dependency>
        <groupId>org.apache.hadoop</groupId>
        <artifactId>hadoop-azure</artifactId>
        <version>2.7.1</version>
    </dependency>
    <dependency>
        <groupId>org.apache.hadoop</groupId>
        <artifactId>hadoop-common</artifactId>
        <version>2.7.1</version>
        <exclusions>
        <exclusion>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
        </exclusion>
        </exclusions>
    </dependency>

> [AZURE.NOTE] Lorsque vous travaillez avec une version antérieure de HDInsight, par exemple, la version 3.2, vous devez enregistrer manuellement ces composants. Pour obtenir un exemple de ceci, ainsi que les bits personnalisées requise pour les clusters de HDInsight plus anciens, voir la la branche [tempête v0.9.3](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub/tree/Storm_v0.9.3) du référentiel de l’exemple.

####<a name="the-maven-compiler-plugin"></a>Le maven-du compilateur-plug-in

    <plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-compiler-plugin</artifactId>
      <version>2.3.2</version>
      <configuration>
        <source>1.7</source>
        <target>1.7</target>
      </configuration>
    </plugin>

Cette option indique à Maven que le projet doit être compilé avec compatibilité pour Java 7, qui est ce qui est utilisé par les clusters de HDInsight.

####<a name="the-maven-shade-plugin"></a>Le maven-ombre-plug-in

      <!-- build an uber jar -->
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
        <version>2.3</version>
        <configuration>
          <transformers>
            <!-- Keep us from getting a can't overwrite file error -->
            <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer"/>
            <!-- Keep us from getting errors when trying to use WASB from the storm-hdfs bolt -->
            <transformer implementation="org.apache.maven.plugins.shade.resource.ServicesResourceTransformer"/>
          </transformers>
          <!-- Keep us from getting a bad signature error -->
          <filters>
            <filter>
                <artifact>*:*</artifact>
                <excludes>
                    <exclude>META-INF/*.SF</exclude>
                    <exclude>META-INF/*.DSA</exclude>
                    <exclude>META-INF/*.RSA</exclude>
                </excludes>
            </filter>
          </filters>
        </configuration>
        <executions>
          <execution>
            <phase>package</phase>
            <goals>
              <goal>shade</goal>
            </goals>
          </execution>
        </executions>
      </plugin>

Cela est utilisé pour la solution de package dans un fichier jar colossaux qui contient à la fois le code de projet et les dépendances obligatoires. Il est également utilisé pour :

* Renommer les fichiers de licence pour les dépendances : Si ce n’est pas le cas, il peut entraîner une erreur lors de l’exécution sur les clusters basés sur Windows de HDInsight.

* Exclure les signatures de sécurité / : Si ce n’est pas le cas, il peut entraîner une erreur lors de l’exécution sur le cluster de HDInsight.

* S’assurer que plusieurs implémentations de la même interface sont fusionnées dans une écriture unique. Si ce n’est pas le cas, vous recevrez des erreurs que le boulon de tempête-très ne comprend pas comment communiquer avec le système de fichiers WASB.

####<a name="the-exec-maven-plugin"></a>L’exec-maven-plug-in

    <plugin>
      <groupId>org.codehaus.mojo</groupId>
      <artifactId>exec-maven-plugin</artifactId>
      <version>1.2.1</version>
      <executions>
        <execution>
        <goals>
          <goal>exec</goal>
        </goals>
        </execution>
      </executions>
      <configuration>
        <executable>java</executable>
        <includeProjectDependencies>true</includeProjectDependencies>
        <includePluginDependencies>false</includePluginDependencies>
        <classpathScope>compile</classpathScope>
        <mainClass>${storm.topology}</mainClass>
      </configuration>
    </plugin>

Vous pouvez ainsi exécuter localement de la topologie de votre environnement de développement à l’aide de la commande suivante :

    mvn compile exec:java -Dstorm.topology=<CLASSNAME>

Par exemple, `mvn compile exec:java -Dstorm.topology=com.microsoft.example.EventHubWriter`.

####<a name="the-resources-section"></a>La section de ressources

    <resources>
      <resource>
        <directory>${basedir}/conf</directory>
        <filtering>false</filtering>
        <includes>
          <include>EventHubs.properties</include>
          <include>core-site.xml</include>
        </includes>
      </resource>
    </resources>

Définit les ressources nécessaires au projet :

- **EventHubs.properties**: contient des informations utilisées pour se connecter à un concentrateur d’événements Azure
- **noyau-site.xml**: contient des informations sur le stockage Azure utilisé par le cluster de HDInsight.

Vous devez remplir les deux avec des informations sur votre cluster concentrateur d’événements et de HDInsight.

##<a name="configure-environment-variables"></a>Configurer les variables d’environnement

Les variables d’environnement peuvent être définies lors de l’installation de Java et du JDK sur votre station de travail de développement. Toutefois, vous devez vérifier qu’ils existent et qu’ils contiennent les valeurs correctes pour votre système.

* **JAVA_HOME** - doit pointer vers le répertoire où est installé l’environnement d’exécution Java (JRE). Par exemple, dans une distribution Unix ou Linux, il doit avoir une valeur semblable à `/usr/lib/jvm/java-7-oracle`. Dans Windows, il aurait une valeur semblable à`c:\Program Files (x86)\Java\jre1.7`

* **Chemin d’accès** : doit contenir les chemins d’accès suivants :

    * **JAVA_HOME** (ou le chemin d’accès équivalent)

    * **JAVA_HOME\bin** (ou le chemin d’accès équivalent)

    * Le répertoire d’installation de Maven

## <a name="configure-event-hub"></a>Configurer le concentrateur d’événements

Concentrateurs de l’événement est la source de données pour cet exemple. Utilisez les étapes suivantes pour créer un nouveau concentrateur d’événements.

1. À partir du [Portail classique d’Azure](https://manage.windowsazure.com), sélectionnez **Nouveau** > **Service Bus** > **Concentrateur d’événements** > **Personnalisé créer**.

2. Dans l’écran **Ajouter un nouveau concentrateur de l’événement** , entrez un **Nom de concentrateur d’événements**, sélectionnez la **région** à créer le concentrateur et créer un nouvel espace de noms ou sélectionnez-en un. Cliquez sur la **flèche** pour continuer.

    ![page de l’Assistant 1](./media/hdinsight-storm-develop-csharp-event-hub-topology/wiz1.png)

    > [AZURE.NOTE] Vous devez sélectionner le même **emplacement** que votre tempête sur HDInsight serveur pour réduire les coûts et les temps de latence.

2. Dans l’écran **Configurer un concentrateur événement** , entrez les valeurs de **compteur de Partition** et de **Rétention de Message** . Pour cet exemple, utilisez un compteur de partition de 10 et une rétention de message 1. Notez le nombre de partitions car vous devrez ultérieurement cette valeur.

    ![page de l’Assistant 2](./media/hdinsight-storm-develop-csharp-event-hub-topology/wiz2.png)

3. Après que le concentrateur d’événements a été créé, sélectionnez l’espace de noms **Concentrateurs de l’événement**et sélectionnez ensuite le concentrateur d’événements que vous avez créé précédemment.

4. Sélectionnez **configurer**, puis créer deux nouvelles règles d’accès en utilisant les informations suivantes.

    <table>
    <tr><th>Nom</th><th>Autorisations</th></tr>
    <tr><td>Writer</td><td>Envoyer</td></tr>
    <tr><td>Lecteur</td><td>Écouter</td></tr>
    </table>

    Après avoir créé les autorisations, cliquez sur l’icône **Enregistrer** au bas de la page. Cette opération crée les stratégies d’accès partagé qui seront utilisés pour envoyer (writer) et écouter ce concentrateur d’événements (lecteur).

    ![stratégies de](./media/hdinsight-storm-develop-csharp-event-hub-topology/policy.png)

5. Après avoir enregistré les stratégies, utilisez **partagé Générateur de clé d’accès** au bas de la page pour récupérer la clé pour les stratégies de **lecteur** et de **writer** . Enregistrer ces car ils seront utilisés ultérieurement.

## <a name="download-and-build-the-project"></a>Téléchargez et générer le projet

1. Télécharger le projet à partir de GitHub : [hdinsight-java-tempête-eventhub](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub). Vous pouvez télécharger le package dans une archive zip ou [git](https://git-scm.com/) permet de cloner le projet localement.

2. Pour générer et empaqueter le projet, utilisez les éléments suivants :

        mvn package

    Il téléchargera les dépendances obligatoires, build et package du projet. Le résultats est stocké dans le répertoire __/target__ comme __1.0-EventHubExample-SNAPSHOT.jar__.

## <a name="deploy-the-topologies"></a>Déployer les topologies

Le fichier jar créé par ce projet contient deux topologies ; __com.microsoft.example.EventHubWriter__ et __com.microsoft.example.EventHubReader__. La topologie de EventHubWriter doit être démarrée en premier lieu, lorsqu’il écrit des événements dans à concentrateur d’événements qui sont ensuite lus par le EventHubReader.

###<a name="if-using-a-linux-based-cluster"></a>Si vous utilisez un cluster Linux

1. SCP permet de copier le package jar à votre cluster HDInsight. Remplacez le nom d’utilisateur avec l’utilisateur SSH pour votre cluster. Remplacez NOM_CLUSTER avec le nom de votre cluster de HDInsight :

        scp ./target/EventHubExample-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.

    Si vous avez utilisé un mot de passe pour votre compte SSH, vous êtes invité à entrer le mot de passe. Si vous avez utilisé un code SSH avec le compte, vous devez utiliser le `-i` paramètre pour spécifier le chemin d’accès au fichier de clé. Par exemple, `scp -i ~/.ssh/id_rsa ./target/EventHubExample-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.`.

    > [AZURE.NOTE] Si votre client est une station de travail Windows, pas avoir une commande SCP installée. Nous vous recommandons de PSCP, qui peut être téléchargé à partir de la [page de téléchargement de PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

    Cette commande copie le fichier vers le répertoire de base de votre utilisateur SSH sur le cluster.

1. Une fois le fichier de téléchargement est terminé, utiliser le protocole SSH pour se connecter au cluster HDInsight. **Nom d’utilisateur** de remplacer le nom de votre connexion SSH. Remplacez **CLUSTERNAME** par votre nom de cluster HDInsight :

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    > [AZURE.NOTE] Si vous avez utilisé un mot de passe pour votre compte SSH, vous êtes invité à entrer le mot de passe. Si vous avez utilisé un code SSH avec le compte, vous devez utiliser le `-i` paramètre pour spécifier le chemin d’accès au fichier de clé. L’exemple suivant charge la clé privée à partir de `~/.ssh/id_rsa`:
    >
    > `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`

    Si vous utilisez PuTTY, entrez `CLUSTERNAME-ssh.azurehdinsight.net` dans __nom d’hôte (ou adresse IP)__ champ, puis cliquez sur __Ouvrir__ pour vous connecter. Vous devrez entrer votre nom de compte SSH.

    > [AZURE.NOTE] Si vous avez utilisé un mot de passe pour votre compte SSH, vous êtes invité à entrer le mot de passe. Si vous avez utilisé un code SSH avec le compte, vous devrez peut-être utiliser les étapes suivantes pour sélectionner la clé :
    >
    > 1. Dans la **catégorie**, développez la **connexion**, développez **SSH**et sélectionnez **Auth**.
    > 2. Cliquez sur **Parcourir** et sélectionnez le fichier .ppk qui contient votre clé privée.
    > 3. Cliquez sur __Ouvrir__ pour vous connecter.

2. Utilisez la commande suivante pour démarrer les topologies :

        storm jar EventHubExample-1.0-SNAPSHOT.jar com.microsoft.example.EventHubWriter writer
        storm jar EventHubExample-1.0-SNAPSHOT.jar com.microsoft.example.EventHubReader reader

    Ceci démarre les topologies et leur donner un nom convivial d’un « lecteur » et « writer ».

3. Attendez une minute ou deux pour permettre les topologies d’écrire et de lire les événements du concentrateur de l’événement, puis utilisez la commande suivante pour vérifier que la EventHubReader stocke des données de votre système de stockage HDInsight :

        hadoop fs -ls /devicedata

    Il doit retourner une liste de fichiers similaire à la suivante :

        -rw-r--r--   1 storm supergroup      10283 2015-08-11 19:35 /devicedata/wasbbolt-14-0-1439321744110.txt
        -rw-r--r--   1 storm supergroup      10277 2015-08-11 19:35 /devicedata/wasbbolt-14-1-1439321748237.txt
        -rw-r--r--   1 storm supergroup      10280 2015-08-11 19:36 /devicedata/wasbbolt-14-10-1439321760398.txt
        -rw-r--r--   1 storm supergroup      10267 2015-08-11 19:36 /devicedata/wasbbolt-14-11-1439321761090.txt
        -rw-r--r--   1 storm supergroup      10259 2015-08-11 19:36 /devicedata/wasbbolt-14-12-1439321762679.txt

    > [AZURE.NOTE] Certains fichiers peuvent présenter une taille de 0, qu’ils ont été créés par l’EventHubReader, mais les données n’ont pas été stockées leur encore.

    Vous pouvez afficher le contenu de ces fichiers à l’aide de la commande suivante :

        hadoop fs -text /devicedata/*.txt

    Ceci renverra données semblable à la suivante :

        3409e622-c85d-4d64-8622-af45e30bf774,848981614
        c3305f7e-6948-4cce-89b0-d9fbc2330c36,-1638780537
        788b9796-e2ab-49c4-91e3-bc5b6af1f07e,-1662107246
        6403df8a-6495-402f-bca0-3244be67f225,275738503
        d7c7f96c-581a-45b1-b66c-e32de6d47fce,543829859
        9a692795-e6aa-4946-98c1-2de381b37593,1857409996
        3c8d199b-0003-4a79-8d03-24e13bde7086,-1271260574

    La première colonne contient la valeur d’ID de périphérique et la deuxième colonne est la valeur de l’unité.

4. Utilisez les commandes suivantes pour arrêter les topologies :

        storm kill reader
        storm kill writer

###<a name="if-using-a-windows-based-cluster"></a>Si vous utilisez un cluster Windows

1. Ouvrez votre navigateur à https://CLUSTERNAME.azurehdinsight.net. Lorsque vous y êtes invité, entrez les informations d’identification d’administrateur pour votre cluster HDInsight. Vous vous parviendra le tableau de bord de tempête.

2. Pour parcourir et sélectionner le fichier EventHubExample-1.0-SNAPSHOT.jar à partir de votre environnement de génération, utilisez la liste déroulante du __Fichier Jar__ .

3. __Nom de classe__, entrez `com.mirosoft.example.EventHubWriter`.

4. Pour les __Autres paramètres__, entrez `writer`. Enfin, cliquez sur __Envoyer__ pour télécharger le fichier jar et démarrer la topologie EventHubWriter.

5. Une fois la topologie a démarré, utilisez le formulaire pour démarrer le EventHubReader :

    * __Fichier JAR__: sélectionnez le EventHubExample-1.0-SNAPSHOT.jar qui a été précédemment téléchargé
    * __Nom de classe__: entrez`com.microsoft.example.EventHubReader`
    * __Paramètres supplémentaires__: entrez`reader`

    Cliquez sur Envoyer pour démarrer la topologie EventHubReader.

6. Attendez quelques minutes pour permettre les topologies générer des événements puis stocker stockage Azure, puis sélectionnez l’onglet __Hadoop requête Console__ en haut de la page de __Tableau de bord de tempête__ .

7. Sur la __Console de la requête__, sélectionnez __Éditeur de la ruche__ , puis remplacez la valeur par défaut `select * from hivesampletable` avec les éléments suivants :

        create external table devicedata (deviceid string, devicevalue int) row format delimited fields terminated by ',' stored as textfile location 'wasbs:///devicedata/';
        select * from devicedata limit 10;

    Cliquez sur __Sélectionner__ pour exécuter la requête. 10 lignes sera retourné à partir de données écrites sur le stockage Azure (WASB) par le EventHubReader. Une fois la requête terminée, vous devriez voir des données similaires à ce qui suit :

        3409e622-c85d-4d64-8622-af45e30bf774,848981614
        c3305f7e-6948-4cce-89b0-d9fbc2330c36,-1638780537
        788b9796-e2ab-49c4-91e3-bc5b6af1f07e,-1662107246
        6403df8a-6495-402f-bca0-3244be67f225,275738503
        d7c7f96c-581a-45b1-b66c-e32de6d47fce,543829859
        9a692795-e6aa-4946-98c1-2de381b37593,1857409996
        3c8d199b-0003-4a79-8d03-24e13bde7086,-1271260574

8. Sélectionnez la __Tempête de tableau de bord__ , en haut de la page, puis __L’interface utilisateur de la tempête__. À partir de l' __Interface utilisateur de tempête__, cliquez sur le lien pour la topologie du __lecteur__ et puis utiliser le bouton __Arrêter__ pour arrêter la topologie. Répétez le processus pour la topologie du __writer__ .



### <a name="checkpointing"></a>Script de vérification

Le EventHubSpout les points de contrôle son état pour le nœud de soigneur, qui enregistre le décalage en cours pour les messages lire régulièrement à partir de la file d’attente. Ainsi, le composant recevoir des messages à l’offset enregistré dans les scénarios suivants :

* L’instance de composant échoue et est redémarrée.

* Vous développer ou réduisez le cluster en ajoutant ou en supprimant des nœuds.

* La topologie est mis à mort et redémarré **avec le même nom**.

####<a name="on-windows-based-hdinsight-clusters"></a>Sur les clusters basés sur Windows de HDInsight

Vous pouvez exporter et importer les points de contrôle persistants à WASB (le stockage Azure utilisé par votre cluster HDInsight). Les scripts pour cela sont situés sur la tempête sur cluster de HDInsight, à **c:\apps\dist\storm-0.9.3.2.2.1.0-2340\zkdatatool-1.0\bin**.

>[AZURE.NOTE] Le numéro de version dans le chemin d’accès peut être différent, comme la version de Storm installé sur le cluster peut changer à l’avenir.

Les scripts dans ce répertoire sont les suivants :

* **stormmeta_import.cmd**: importer toutes les métadonnées de tempête à partir du conteneur de stockage de cluster par défaut dans soigneur.

* **stormmeta_export.cmd**: exporter toutes les métadonnées de tempête de soigneur pour le conteneur de stockage de cluster par défaut.

* **stormmeta_delete.cmd**: supprimer toutes les métadonnées de tempête à partir de soigneur.

Exportation de qu'une importation vous permet de faire persister les données de point de contrôle lorsque vous devez supprimer le cluster, mais souhaitez reprendre le traitement à partir de l’offset actuel dans le concentrateur lorsque vous mettez un nouveau cluster en ligne.

> [AZURE.NOTE] Dans la mesure où les données sont persistantes pour le conteneur de stockage par défaut, le de cluster nouveau **doivent** utiliser le même compte de stockage et le conteneur que le cluster précédent.

## <a name="delete-your-cluster"></a>Supprimer de votre cluster.

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

##<a name="troubleshooting"></a>Résolution des problèmes

Si vous ne voyez pas les fichiers stockés à la l’emplacement /devicedata (à l’aide de la `hadoop fs -ls /devicedata` commande ou la ruche commandd dans la Console de la requête,) permet de rechercher les erreurs retournées par les topologies de l’interface utilisateur de tempête.

Pour plus d’informations sur l’utilisation de l’interface utilisateur de tempête, consultez les rubriques suivantes :

* Si vous utilisez une tempête __basé sur Linux__ sur cluster de HDInsight, reportez-vous à la section [déployer et gérer des topologies de tempête de Apache sur basé sur Linux de HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)

* Si vous utilisez une tempête __basés sur Windows__ sur le cluster de HDInsight, reportez-vous à la section [déployer et gérer des topologies de tempête de Apache sur HDInsight de basés sur Windows](hdinsight-storm-deploy-monitor-topology-linux.md)

##<a name="next-steps"></a>Étapes suivantes

* [Exemples de topologies pour tempête sur HDInsight](hdinsight-storm-example-topology.md)
