<properties
   pageTitle="Excel de se connecter à Hadoop avec le pilote ODBC de ruche | Microsoft Azure"
   description="Découvrez comment configurer et utiliser le pilote ODBC de la ruche de Microsoft Excel pour interroger les données dans un cluster HDInsight."
   services="hdinsight"
   documentationCenter=""
   authors="mumian"
   manager="jhubbard"
   tags="azure-portal"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/19/2016"
   ms.author="jgao"/>

#<a name="connect-excel-to-hadoop-with-the-microsoft-hive-odbc-driver"></a>Utiliser Excel Hadoop avec le pilote ODBC de la ruche de Microsoft

[AZURE.INCLUDE [ODBC-JDBC-selector](../../includes/hdinsight-selector-odbc-jdbc.md)]

Solution de données de Microsoft intègre des composants de Microsoft Business Intelligence (BI) Apache Hadoop clusters qui ont été déployés par l’HDInsight d’Azure. Un exemple de cette intégration est la capacité à Excel de se connecter à l’entrepôt de données de ruche d’un cluster d’Hadoop dans HDInsight en utilisant le pilote Microsoft la ruche ODBC Open Database Connectivity ().

Il est également possible de connecter les données associées à un cluster d’HDInsight et d’autres sources de données, y compris les autres clusters Hadoop (non-HDInsight), à partir de Excel à l’aide de la macro complémentaire Microsoft Query de puissance pour Excel. Pour plus d’informations sur l’installation et à l’aide de la requête d’alimentation, reportez-vous à [Excel de se connecter à HDInsight avec alimentation requête][hdinsight-power-query].

> [AZURE.NOTE] Si les étapes décrites dans cet article peuvent être utilisés avec un cluster d’un HDInsight de fonctionnant sous Windows ou Linux, Windows est requise pour la station de travail cliente.

**Conditions préalables**:

Avant de commencer cet article, vous devez disposer des éléments suivants :

- **HDInsight d’un cluster**. Pour en créer une, reportez-vous à la section [mise en route avec Azure HDInsight][hdinsight-get-started].
- **Une station de travail** avec Office 2013 Professionnel Plus, Office 365 Pro Plus, Excel 2013 autonome ou Office 2010 Professionnel Plus.


##<a name="install-microsoft-hive-odbc-driver"></a>Installer le pilote ODBC de la ruche de Microsoft

Téléchargez et installez le pilote ODBC de la ruche Microsoft à partir du [Centre de téléchargement][hive-odbc-driver-download].

Ce pilote peut être installé sur les versions 32 bits ou 64 bits de Windows 7, Windows 8, Windows 10, Windows Server 2008 R2 et Windows Server 2012 et permettra de connexion Azure HDInsight (version 1.6 ou ultérieure) et Azure HDInsight émulateur (v.1.0.0.0 et versions ultérieures). Vous devez installer la version qui correspond à la version de l’application dans laquelle vous utilisez le pilote ODBC. Pour ce didacticiel, le pilote sera utilisé à partir d’Office Excel.

##<a name="create-hive-odbc-data-source"></a>Créer la source de données ODBC de la ruche

Les étapes suivantes vous montrent comment créer une Source de données ODBC de la ruche.

1. À partir de Windows 8 ou 10 de Windows, appuyez sur la touche Windows pour ouvrir l’écran de démarrage et tapez **des sources de données**.
2. Cliquez sur **configurer des données ODBC sources (32 bits)** ou **définir des Sources de données ODBC (64 bits)** , en fonction de votre version d’Office. Si vous utilisez Windows 7, cliquez sur **Sources de données ODBC (32 bits)** ou des **Sources de données ODBC (64 bits)** à partir des **Outils d’administration**. Cette action lance la boîte de dialogue **Administrateur de sources de données ODBC** .

    ![Administrateur de source de données ODBC][img-hdi-simbahiveodbc-datasource-admin]

3. À partir du DNS de l’utilisateur, cliquez sur **Ajouter** pour ouvrir l’Assistant **Créer une nouvelle Source de données** .
4. Sélectionnez **Microsoft ODBC Driver de la ruche**, puis cliquez sur **Terminer**. Cette action lance la boîte de dialogue **Microsoft la ruche DNS configuration du pilote ODBC** .

5. Tapez ou sélectionnez les valeurs suivantes :

    Propriété|Description
    ---|---
    Nom de Source de données|Donner un nom à votre source de données
    Hôte|Entrez &lt;HDInsightClusterName >. azurehdinsight.net. Par exemple, myHDICluster.azurehdinsight.net
    Port|Utilisez <strong>443</strong>. (Ce port a été modifié à partir de 563 à 443.)
    Base de données|Utiliser <strong>par défaut</strong>.
    Type de serveur de la ruche|Sélectionnez <strong>la ruche serveur 2</strong>
    Mécanisme|Sélectionnez <strong>Azure Service de HDInsight</strong>
    Chemin d’accès HTTP|Laissez le champ vide.
    Nom d’utilisateur|Entrez le nom d’utilisateur de cluster HDInsight. C’est le nom d’utilisateur créé pendant le processus de fourniture de cluster. Si vous avez utilisé l’option de création rapide, le nom d’utilisateur par défaut est <strong>admin</strong>.
    Mot de passe|Entrez le mot de passe utilisateur de cluster HDInsight.
    </table>

    Il existe certains paramètres importants à connaître lorsque vous cliquez sur **Options avancées**:

    Paramètre|Description
    ---|---
    Utilisez la requête Native|Lorsqu’elle est sélectionnée, le pilote ODBC ne tente pas de convertir TSQL dans HiveQL. Vous doit l’utiliser uniquement si vous êtes sûr que vous soumettez des instructions HiveQL pures à 100 %. Lors de la connexion à SQL Server ou SQL Azure, vous devez laisser désactivée.
    Lignes lues par bloc|Lors de la lecture d’une grande quantité d’enregistrements, réglage de ce paramètre peut être requis pour garantir des performances optimales.
    Longueur de colonne de chaîne par défaut, la longueur de colonne binaire, échelle d’une colonne Decimal|Le type de données longueurs et précisions peuvent affecter la façon dont les données sont renvoyées. Provoquent des informations incorrectes à retourner en raison d’une perte de précision ou de troncature.


    ![Options avancées][img-HiveOdbc-DataSource-AdvancedOptions]

6. Cliquez sur **tester** pour tester la source de données. Lorsque la source de données est correctement configurée, il affiche les *des TESTS s’est terminée avec succès !*.
7. Cliquez sur **OK** pour fermer la boîte de dialogue de Test. La nouvelle source de données doit maintenant être affichée dans l' **Administrateur de Source de données ODBC**.
8. Cliquez sur **OK** pour quitter l’Assistant.

##<a name="import-data-into-excel-from-hdinsight"></a>Importer des données dans Excel à partir de HDInsight

Les étapes ci-dessous décrivent la manière d’importer des données à partir d’une table de la ruche dans un classeur Excel à l’aide de la source de données ODBC que vous avez créé dans les étapes ci-dessus.

1. Dans Excel, ouvrez un classeur existant ou nouveau.
2. Sous l’onglet **données** , cliquez sur **à partir d’autres Sources de données**, puis cliquez sur **Assistant de connexion de données** pour lancer l' **Assistant connexion de données**.

    ![Assistant connexion de données ouverte][img-hdi-simbahiveodbc.excel.dataconnection]

3. Sélectionnez le **DSN ODBC** comme source de données, puis cliquez sur **suivant**.
4. À partir de sources de données ODBC, sélectionnez le nom de source de données que vous avez créé à l’étape précédente, puis cliquez sur **suivant**.
5. Entrez à nouveau le mot de passe pour le cluster dans l’Assistant, puis cliquez sur **Test** pour vérifier la configuration une fois de plus, si nécessaire.
6. Cliquez sur **OK** pour fermer la boîte de dialogue de test.
7. Cliquez sur **OK**. Attendez que la boîte de dialogue **Sélectionner la base de données et la Table** à ouvrir. Cette opération peut prendre quelques secondes.
8. Sélectionnez la table que vous souhaitez importer, puis cliquez sur **suivant**. Le *hivesampletable* est un exemple de table de ruche qui est fourni avec les clusters HDInsight.  Vous pouvez choisir si vous n’avez pas créé un. Pour plus d’informations sur l’exécution des requêtes de la ruche et créer des tables de la ruche, voir [Utilisation de la ruche avec HDInsight][hdinsight-use-hive].
8. Cliquez sur **Terminer**.
9. Dans la boîte de dialogue **Importer des données** , vous pouvez modifier ou spécifier la requête. Pour ce faire, cliquez sur **Propriétés**. Cette opération peut prendre quelques secondes.
10. Cliquez sur l’onglet **définition** et puis ajouter **200 de limite** à l’instruction select de la ruche dans la zone de texte **texte de la commande** . La modification limite le jeu d’enregistrements retourné à 200.

    ![Propriétés de connexion][img-hdi-simbahiveodbc-excel-connectionproperties]

11. Cliquez sur **OK** pour fermer la boîte de dialogue Propriétés de la connexion.
12. Cliquez sur **OK** pour fermer la boîte de dialogue **Importer des données** .  
13. Entrez à nouveau le mot de passe, puis cliquez sur **OK**. Il faut quelques secondes avant Obtient les données importées dans Excel.

##<a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris comment utiliser le pilote ODBC de la ruche de Microsoft pour récupérer des données à partir du Service de HDInsight dans Excel. De même, vous pouvez récupérer des données à partir du Service de HDInsight dans la base de données de SQL. Il est également possible de charger les données dans un Service de HDInsight. Pour plus d’informations, voir :

- [Analyser des données de retard de vol avec HDInsight][hdinsight-analyze-flight-data]
- [Téléchargement des données vers HDInsight][hdinsight-upload-data]
- [Utilisez Sqoop avec HDInsight] [hdinsight-use-sqoop]


[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md
[hdinsight-get-started]: hdinsight-hadoop-tutorial-get-started-windows.md

[hive-odbc-driver-download]: http://go.microsoft.com/fwlink/?LinkID=286698

[img-hdi-simbahiveodbc-datasource-admin]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveOdbc.DataSourceAdmin1.png
[img-HiveOdbc-DataSource-AdvancedOptions]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.HiveOdbc.DataSource.AdvancedOptions1.png
[img-hdi-simbahiveodbc-excel-connectionproperties]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveODBC.Excel.ConnectionProperties1.png
[img-hdi-simbahiveodbc.excel.dataconnection]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveOdbc.Excel.DataConnection1.png
