<properties
   pageTitle="Analyser et processus JSON documents avec la ruche HDInsight | Microsoft Azure"
   description="Apprenez à utiliser des documents JSON et à les analyser à l’aide de la ruche dans HDInsight."
   services="hdinsight"
   documentationCenter=""
   authors="rashimg"
   manager="mwinkle"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="06/22/2015"
   ms.author="rashimg"/>


# <a name="process-and-analyze-json-documents-using-hive-in-hdinsight"></a>Traiter et analyser des documents JSON à l’aide de la ruche dans HDInsight

Apprenez à traiter et analyser les fichiers JSON à l’aide de la ruche dans HDInsight. Le document JSON suivant sera utilisé dans le didacticiel

    {
        "StudentId": "trgfg-5454-fdfdg-4346",
        "Grade": 7,
        "StudentDetails": [
            {
                "FirstName": "Peggy",
                "LastName": "Williams",
                "YearJoined": 2012
            }
        ],
        "StudentClassCollection": [
            {
                "ClassId": "89084343",
                "ClassParticipation": "Satisfied",
                "ClassParticipationRank": "High",
                "Score": 93,
                "PerformedActivity": false
            },
            {
                "ClassId": "78547522",
                "ClassParticipation": "NotSatisfied",
                "ClassParticipationRank": "None",
                "Score": 74,
                "PerformedActivity": false
            },
            {
                "ClassId": "78675563",
                "ClassParticipation": "Satisfied",
                "ClassParticipationRank": "Low",
                "Score": 83,
                "PerformedActivity": true
            }
        ]
    }

Vous trouverez le fichier à wasbs://processjson@hditutorialdata.blob.core.windows.net/. Pour plus d’informations sur l’utilisation de stockage des objets Blob Azure avec HDInsight, reportez-vous à la section [stockage Blob d’Azure utilisation très compatibles avec Hadoop dans HDInsight](hdinsight-hadoop-use-blob-storage.md). Si vous le souhaitez, vous pouvez copier le fichier vers le conteneur par défaut de votre cluster.

Dans ce didacticiel, vous allez utiliser la console de la ruche.  Pour obtenir des instructions de l’ouverture de la console de la ruche, voir [Utilisation de la ruche avec Hadoop sur HDInsight avec Bureau à distance](hdinsight-hadoop-use-hive-remote-desktop.md).

##<a name="flatten-json-documents"></a>Aplatir les documents JSON

Les méthodes répertoriées dans la section suivante requièrent le document JSON dans une seule ligne. Par conséquent, vous devez aplatir document en une chaîne JSON. Si votre document JSON est déjà aplatie, vous pouvez ignorer cette étape et aller directement à la section suivante sur les données JSON d’analyse.

    DROP TABLE IF EXISTS StudentsRaw;
    CREATE EXTERNAL TABLE StudentsRaw (textcol string) STORED AS TEXTFILE LOCATION "wasb://processjson@hditutorialdata.blob.core.windows.net/";

    DROP TABLE IF EXISTS StudentsOneLine;
    CREATE EXTERNAL TABLE StudentsOneLine
    (
      json_body string
    )
    STORED AS TEXTFILE LOCATION '/json/students';

    INSERT OVERWRITE TABLE StudentsOneLine
    SELECT CONCAT_WS(' ',COLLECT_LIST(textcol)) AS singlelineJSON
          FROM (SELECT INPUT__FILE__NAME,BLOCK__OFFSET__INSIDE__FILE, textcol FROM StudentsRaw DISTRIBUTE BY INPUT__FILE__NAME SORT BY BLOCK__OFFSET__INSIDE__FILE) x
          GROUP BY INPUT__FILE__NAME;

    SELECT * FROM StudentsOneLine

Le fichier JSON brut se trouve dans **wasbs://processjson@hditutorialdata.blob.core.windows.net/**. La table *StudentsRaw* ruche pointe vers le document JSON de non aplati brut.

La table *StudentsOneLine* ruche stocke les données dans le système de fichiers par défaut HDInsight sous le chemin d’accès */json/étudiants /* .

L’instruction INSERT remplir la table StudentOneLine avec les données JSON aplaties.

L’instruction SELECT renvoie uniquement 1 ligne.

Voici la sortie de l’instruction SELECT :

![Mise à plat du document JSON.][image-hdi-hivejson-flatten]

##<a name="analyze-json-documents-in-hive"></a>Analyser les documents JSON dans une ruche

Ruche fournit trois mécanismes différents pour exécuter des requêtes sur des documents JSON :

- Utilisez la commande obtenir\_JSON\_objet FDU (fonction définie par l’utilisateur)
- Utilisez le fichier UDF JSON_TUPLE
- Utilisez SerDe personnalisé
- écrire que vous-même la FDU à l’aide de Python ou autres langages. Consultez [cet article] [ hdinsight-python] sur l’exécution de votre propre code Python avec la ruche.

### <a name="use-the-getjsonobject-udf"></a>Utilisez la commande obtenir\_JSON_OBJECT UDF
Ruche fournit qu'un fichier UDF intégré appelé [obtenir l’objet json](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object) qui peut effectuer une interrogation de JSON au moment de l’exécution. Cette méthode prend deux arguments : le nom de la table et le nom de méthode qui comporte le document JSON aplati et le champ JSON qui doit être analysé. Examinons un exemple pour voir comment cette FDU fonctionne.

Obtenir le prénom et le nom de chaque étudiant.

    SELECT
      GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.FirstName'),
      GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.LastName')
    FROM StudentsOneLine;

Voici la sortie lors de l’exécution de cette requête dans la fenêtre de la console.

![get_json_object UDF][image-hdi-hivejson-getjsonobject]

Il existe quelques limitations de la FDU de get-json_object.

- Étant donné que chaque champ de la requête requiert la ré-analyse de la requête, il affecte les performances.
- OBTENIR\_JSON_OBJECT() retourne la représentation sous forme de chaîne d’un tableau. Pour convertir un tableau de ruche, vous devrez utiliser des expressions régulières pour remplacer les crochets ' [' et ']' et appelez ensuite également fractionné pour obtenir le tableau.


C’est pourquoi le wiki de ruche recommande l’utilisation de json_tuple.  

### <a name="use-the-jsontuple-udf"></a>Utilisez le fichier UDF JSON_TUPLE

Un autre UDF fourni par ruche est appelé [json_tuple](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-json_tuple) , qui est plus performante que [get_ json _object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object). Cette méthode prend un jeu de clés et d’une chaîne JSON et retourne un tuple de valeurs à l’aide d’une fonction. La requête suivante renvoie l’id de l’étudiant et la qualité du document JSON :

    SELECT q1.StudentId, q1.Grade
      FROM StudentsOneLine jt
      LATERAL VIEW JSON_TUPLE(jt.json_body, 'StudentId', 'Grade') q1
        AS StudentId, Grade;

La sortie de ce script dans la console de la ruche :

![json_tuple UDF][image-hdi-hivejson-jsontuple]

JSON\_TUPLE utilise la syntaxe de [vue latérale](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) dans la ruche qui permet à json\_tuple pour créer une table virtuelle en appliquant la fonction UDT à chaque ligne de la table d’origine.  JSONs complexes devient trop complexe en raison de l’utilisation répétée de vue latérale. En outre, JSON_TUPLE ne peut pas gérer JSONs imbriquées.


###<a name="use-custom-serde"></a>Utilisez SerDe personnalisé

SerDe est le meilleur choix pour l’analyse des documents JSON imbriqués, il vous permet de définir le schéma JSON et l’utiliser pour analyser les documents. Dans ce didacticiel, vous allez utiliser un des SerDe plus populaire qui a été développé par [rcongiu](https://github.com/rcongiu).

**Pour utiliser le SerDe personnalisé :**

1. Installer le [Kit de développement de SE de Java 7u55 JDK 1.7.0_55](http://www.oracle.com/technetwork/java/javase/downloads/java-archive-downloads-javase7-521261.html#jdk-7u55-oth-JPR). Choisissez la version de Windows X64 de JDK si vous allez utiliser le déploiement Windows de HDInsight

    >[AZURE.WARNING] JDK 1.8 ne fonctionne pas avec ce SerDe.

    Une fois l’installation terminée, ajoutez une nouvelle variable d’environnement utilisateur :

    1. À partir de l’écran de Windows, ouvrez **l’affichage des paramètres système avancés** .
    2. Cliquez sur **Variables d’environnement**.  
    3. Ajouter qu'une nouvelle variable d’environnement **JAVA_HOME** pointe vers **C:\Program Files\Java\jdk1.7.0_55** ou partout où est installé votre JDK.

    ![Paramétrage des valeurs de configuration correctes pour JDK][image-hdi-hivejson-jdk]

2. Installer [Maven 3.3.1](http://mirror.olnevhost.net/pub/apache/maven/maven-3/3.3.1/binaries/apache-maven-3.3.1-bin.zip)

    Ajouter le dossier bin à votre chemin d’accès en accédant au contrôle de panneau--> modifier les Variables système pour les variables de Environment de votre compte. La capture d’écran ci-dessous vous montre comment effectuer cette opération.

    ![Configuration de Maven][image-hdi-hivejson-maven]

3. Dupliquer le projet à partir de la [Ruche à JSON-SerDe](https://github.com/sheetaldolas/Hive-JSON-Serde/tree/master) github site. Pour cela, vous pouvez cliquer sur le bouton « Télécharger Zip », comme illustré dans la capture d’écran ci-dessous.

    ![Le projet de clonage][image-hdi-hivejson-serde]

4 : aller dans le dossier où vous avez téléchargé ce package et type « mvn ». Cette action doit créer les fichiers jar nécessaires que vous pouvez ensuite copier sur le cluster.

5 : allez dans le dossier cible sous le dossier racine dans lequel vous avez téléchargé le package. Télécharger le fichier json-serde-1.1.9.9-Hive13-jar-with-dependencies.jar à la tête de nœuds de votre cluster. Je sont généralement placées dans le dossier binaire de ruche : C:\apps\dist\hive-0.13.0.2.1.11.0-2316\bin ou quelque chose de similaire.

6 : à l’invite de la ruche, tapez « ajouter jar /path/to/json-serde-1.1.9.9-Hive13-jar-with-dependencies.jar ». Comme dans mon cas, le fichier jar est dans le dossier C:\apps\dist\hive-0.13.x\bin, puis-je ajouter directement le fichier jar avec le nom comme indiqué ci-dessous :

    add jar json-serde-1.1.9.9-Hive13-jar-with-dependencies.jar;

    ![Adding JAR to your project][image-hdi-hivejson-addjar]

Maintenant, vous êtes prêt à le SerDe permet d’exécuter des requêtes sur le document JSON.

L’expression suivante crée une table avec un schéma défini

    DROP TABLE json_table;
    CREATE EXTERNAL TABLE json_table (
      StudentId string,
      Grade int,
      StudentDetails array<struct<
          FirstName:string,
          LastName:string,
          YearJoined:int
          >
      >,
      StudentClassCollection array<struct<
          ClassId:string,
          ClassParticipation:string,
          ClassParticipationRank:string,
          Score:int,
          PerformedActivity:boolean
          >
      >
    ) ROW FORMAT SERDE 'org.openx.data.jsonserde.JsonSerDe'
    LOCATION '/json/students';

Pour répertorier le prénom et le nom de l’étudiant

    SELECT StudentDetails.FirstName, StudentDetails.LastName FROM json_table;

Voici le résultat à partir de la console de la ruche.

![Requête SerDe 1][image-hdi-hivejson-serde_query1]

Pour calculer la somme des scores du document JSON

    SELECT SUM(scores)
    FROM json_table jt
      lateral view explode(jt.StudentClassCollection.Score) collection as scores;

La requête ci-dessus utilise un [éclatement du vue latérale](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) UDF pour développer le tableau des scores afin qu’ils peuvent être additionnés.

Voici la sortie de la console de la ruche.

![Requête de SerDe 2][image-hdi-hivejson-serde_query2]

Pour trouver les sujets un étudiant donné a obtenu des points à plus de 80 sélectionnez  
      JT. StudentClassCollection.ClassId de json_table jt vue latérale éclater (JT. Collection de StudentClassCollection.Score) comme score où score > 80 ;

La requête ci-dessus retourne un tableau de ruche Contrairement à get\_json\_objet qui retourne une chaîne.

![Requête SerDe 3][image-hdi-hivejson-serde_query3]

Si vous souhaitez skil JSON mal formé, puis comme expliqué dans la [page wiki](https://github.com/sheetaldolas/Hive-JSON-Serde/tree/master) de cette SerDe vous pouvez effectuer cette opération en tapant le code suivant :  

    ALTER TABLE json_table SET SERDEPROPERTIES ( "ignore.malformed.json" = "true");




##<a name="summary"></a>Résumé
En conclusion, le type d’opérateur JSON dans la ruche que vous choisissez dépend de votre scénario. Si vous avez un simple document JSON et n’avoir qu’un seul champ à rechercher, vous pouvez choisir d’utiliser get FDU de la ruche\_json\_objet. Si vous avez plusieurs clés à chercher vous pouvez utiliser json_tuple. Si vous avez un document imbriqué, vous devez utiliser le SerDe de JSON.

Autres articles associés, reportez-vous à la section.

- [Utilisez ruche et HiveQL avec Hadoop dans HDInsight pour analyser un exemple de fichier log4j Apache](hdinsight-use-hive.md)
- [Analyser des données de retard de vol à l’aide de ruche dans HDInsight](hdinsight-analyze-flight-delay-data.md)
- [Analyser les données de Twitter à l’aide de la ruche dans HDInsight](hdinsight-analyze-twitter-data.md)
- [Exécution d’une tâche d’Hadoop à l’aide de DocumentDB et HDInsight](../documentdb/documentdb-run-hadoop-with-hdinsight.md)

[hdinsight-python]: hdinsight-python.md

[image-hdi-hivejson-flatten]: ./media/hdinsight-using-json-in-hive/flatten.png
[image-hdi-hivejson-getjsonobject]: ./media/hdinsight-using-json-in-hive/getjsonobject.png
[image-hdi-hivejson-jsontuple]: ./media/hdinsight-using-json-in-hive/jsontuple.png
[image-hdi-hivejson-jdk]: ./media/hdinsight-using-json-in-hive/jdk.png
[image-hdi-hivejson-maven]: ./media/hdinsight-using-json-in-hive/maven.png
[image-hdi-hivejson-serde]: ./media/hdinsight-using-json-in-hive/serde.png
[image-hdi-hivejson-addjar]: ./media/hdinsight-using-json-in-hive/addjar.png
[image-hdi-hivejson-serde_query1]: ./media/hdinsight-using-json-in-hive/serde_query1.png
[image-hdi-hivejson-serde_query2]: ./media/hdinsight-using-json-in-hive/serde_query2.png
[image-hdi-hivejson-serde_query3]: ./media/hdinsight-using-json-in-hive/serde_query3.png
[image-hdi-hivejson-serde_result]: ./media/hdinsight-using-json-in-hive/serde_result.png
