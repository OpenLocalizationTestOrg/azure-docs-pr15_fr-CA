<properties
pageTitle="Utilisez une fonction définie par l’utilisateur de Java (UDF) avec la ruche HDInsight | Microsoft Azure"
description="Découvrez comment créer et utiliser une fonction définie par l’utilisateur de Java (UDF) à partir de la ruche dans HDInsight."
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
ms.date="09/27/2016"
ms.author="larryfr"/>

#<a name="use-a-java-udf-with-hive-in-hdinsight"></a>Utilisez un Java FDU avec ruche dans HDInsight

Ruche est idéal pour travailler avec des données dans HDInsight, mais parfois vous avez besoin d’un plus grand objectif de langage. Ruche vous permet de créer des fonctions définies par l’utilisateur (UDF) à l’aide d’une variété de langages de programmation. Dans ce document, vous apprendrez comment utiliser un fichier UDF de Java à partir de la ruche.

## <a name="requirements"></a>Configuration requise

* Un abonnement Azure

* Un cluster de HDInsight (Windows ou Linux)

    > [AZURE.NOTE] La plupart des étapes de ce document fonctionne sur les deux types de cluster ; Toutefois, les étapes à suivre pour télécharger le fichier UDF compilé au cluster et exécuter sont spécifiques aux clusters basés sur Linux. Des liens sont fournis à des informations qui peuvent être utilisées avec les clusters basés sur Windows.

* [Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/) 7 ou version ultérieure (ou un équivalent, par exemple, OpenJDK)

* [Apache Maven](http://maven.apache.org/)

* Un éditeur de texte ou un IDE de Java

    > [AZURE.IMPORTANT] Si vous utilisez un serveur basé sur Linux de HDInsight, mais créez les Python de fichiers sur un client Windows, vous devez utiliser un éditeur qui utilise LF comme une fin de ligne. Si vous ne savez pas si votre éditeur utilise le saut de ligne ou CRLF, consultez la section [dépannage](#troubleshooting) pour obtenir la procédure sur la suppression du caractère CR à l’aide des utilitaires sur le cluster de HDInsight.

## <a name="create-an-example-udf"></a>Créer un exemple de fichier UDF

1. À partir d’une ligne de commande, utilisez la suivante pour créer un nouveau projet Maven :

        mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=ExampleUDF -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

    > [AZURE.NOTE] Si vous utilisez PowerShell, vous devez placer entre guillemets les paramètres. Par exemple, `mvn archetype:generate "-DgroupId=com.microsoft.examples" "-DartifactId=ExampleUDF" "-DarchetypeArtifactId=maven-archetype-quickstart" "-DinteractiveMode=false"`.

    Cela créera un nouveau répertoire nommé __exampleudf__, qui contient le projet Maven.

2. Une fois que le projet a été créé, supprimez le répertoire __exampleudf/src/test__ qui a été créé dans le cadre du projet ; Elle servira pas pour cet exemple.

3. Ouvrez __exampleudf/pom.xml__et remplacer l’existant `<dependencies>` entrée avec les éléments suivants :

        <dependencies>
            <dependency>
                <groupId>org.apache.hadoop</groupId>
                <artifactId>hadoop-client</artifactId>
                <version>2.7.1</version>
                <scope>provided</scope>
            </dependency>
            <dependency>
                <groupId>org.apache.hive</groupId>
                <artifactId>hive-exec</artifactId>
                <version>1.2.1</version>
                <scope>provided</scope>
            </dependency>
        </dependencies>

    Ces entrées spécifient la version d’Hadoop et ruche inclus avec HDInsight 3.3 et 3.4 clusters. Vous pouvez trouver des informations sur les versions de Hadoop et ruche fournis avec HDInsight à partir du document de [Versioning des composants de HDInsight](hdinsight-component-versioning.md) .

    Ajouter un `<build>` section avant de la `</project>` ligne à la fin du fichier. Cette section doit contenir les éléments suivants :

        <build>
            <plugins>
                <!-- build for Java 1.7, even if you're on a later version -->
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-compiler-plugin</artifactId>
                    <version>3.3</version>
                    <configuration>
                        <source>1.7</source>
                        <target>1.7</target>
                    </configuration>
                </plugin>
                <!-- build an uber jar -->
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-shade-plugin</artifactId>
                    <version>2.3</version>
                    <configuration>
                        <!-- Keep us from getting a can't overwrite file error -->
                        <transformers>
                            <transformer
                                    implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                            </transformer>
                            <transformer implementation="org.apache.maven.plugins.shade.resource.ServicesResourceTransformer">
                            </transformer>
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
            </plugins>
        </build>
    
    Ces entrées définissent comment générer le projet. En particulier, la version de Java que le projet utilise et comment créer un uberjar pour le déploiement sur le cluster.

    Enregistrez le fichier une fois que les modifications ont été apportées.

4. Renommer le __exampleudf/src/main/java/com/microsoft/examples/App.java__ à __ExampleUDF.java__et puis ouvrez le fichier dans votre éditeur.

5. Remplacez le contenu du fichier __ExampleUDF.java__ par le texte suivant, puis enregistrez le fichier.

        package com.microsoft.examples;

        import org.apache.hadoop.hive.ql.exec.Description;
        import org.apache.hadoop.hive.ql.exec.UDF;
        import org.apache.hadoop.io.*;

        // Description of the UDF
        @Description(
            name="ExampleUDF",
            value="returns a lower case version of the input string.",
            extended="select ExampleUDF(deviceplatform) from hivesampletable limit 10;"
        )
        public class ExampleUDF extends UDF {
            // Accept a string input
            public String evaluate(String input) {
                // If the value is null, return a null
                if(input == null)
                    return null;
                // Lowercase the input string and return it
                return input.toLowerCase();
            }
        }

    Il implémente une FDU qui accepte une valeur de chaîne et retourne une version en minuscules de la chaîne.

## <a name="build-and-install-the-udf"></a>Générer et installer le fichier UDF

1. Utilisez la commande suivante pour compiler et empaqueter la FDU :

        mvn compile package

    Cela va générer, puis empaqueter la FDU dans __exampleudf/target/ExampleUDF-1.0-SNAPSHOT.jar__.

2. Utilisez le `scp` commande pour copier le fichier vers le cluster de HDInsight.

        scp ./target/ExampleUDF-1.0-SNAPSHOT.jar myuser@mycluster-ssh.azurehdinsight

    Remplacez __myuser__ avec le compte d’utilisateur SSH pour votre cluster. Remplacez __mon_cluster__ par le nom du cluster. Si vous avez utilisé un mot de passe pour le compte SSH, vous êtes invité à entrer le mot de passe. Si vous avez utilisé un certificat, vous devrez peut-être utiliser le `-i` paramètre pour spécifier le fichier de clé privée.

3. La connexion au cluster à l’aide de SSH. 

        ssh myuser@mycluster-ssh.azurehdinsight.net

    Pour plus d’informations sur l’utilisation de SSH avec HDInsight, consultez les documents suivants.

    * [Utiliser le protocole SSH avec basé sur Linux d’Hadoop sur HDInsight à partir d’OS X, Unix ou Linux](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [Utiliser le protocole SSH avec basé sur Linux d’Hadoop sur HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

4. À partir de la session SSH, copiez le fichier jar HDInsight stockage.

        hdfs dfs -put ExampleUDF-1.0-SNAPSHOT.jar /example/jars

## <a name="use-the-udf-from-hive"></a>Utilisez la FDU de la ruche

1. Utilisez ce qui suit pour démarrer le client de Beeline à partir de la session SSH.

        beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin

    Cette commande suppose que vous avez utilisé la valeur par défaut de __l’administrateur__ pour le compte de connexion pour votre cluster.

2. Une fois sur le `jdbc:hive2://localhost:10001/>` invite, entrez la commande suivante pour ajouter la FDU à la ruche et l’exposer en tant que fonction.

        ADD JAR wasbs:///example/jars/ExampleUDF-1.0-SNAPSHOT.jar;
        CREATE TEMPORARY FUNCTION tolower as 'com.microsoft.examples.ExampleUDF';

3. Utilisez le fichier UDF pour convertir des valeurs extraites d’une table pour les chaînes en minuscules.

        SELECT tolower(deviceplatform) FROM hivesampletable LIMIT 10;

    Cela va sélectionner la plate-forme périphérique (Android, Windows, iOS, etc.) à partir de la table, convertir la chaîne en minuscules et puis les afficher. Le résultat sera semblable à la suivante.

        +----------+--+
        |   _c0    |
        +----------+--+
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        +----------+--+

## <a name="next-steps"></a>Étapes suivantes

Pour d’autres façons de travailler avec la ruche, voir [Utilisation de la ruche avec HDInsight](hdinsight-use-hive.md).

Pour plus d’informations sur les fonctions de Hive User-Defined, consultez la section [opérateurs de la ruche et les fonctions définies par l’utilisateur](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF) de la ruche wiki sur le site apache.org.
