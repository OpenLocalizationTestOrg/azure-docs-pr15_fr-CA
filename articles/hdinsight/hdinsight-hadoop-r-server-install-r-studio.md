<properties
    pageTitle="Installer RStudio avec serveur R sur HDInsight (aperçu) | Microsoft Azure"
    description="Comment installer RStudio avec serveur R sur HDInsight (aperçu)."
    services="hdinsight"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/16/2016"
   ms.author="jeffstok"/>


# <a name="installing-rstudio-with-r-server-on-hdinsight-preview"></a>L’installation de RStudio avec serveur R sur HDInsight (aperçu)

Plusieurs environnements de développement intégré (IDE) sont aujourd'hui disponibles pour R, notamment de Microsoft récemment annoncé des [Outils R pour Visual Studio](https://www.visualstudio.com/en-us/features/rtvs-vs.aspx) (RTV), une gamme d’outils de bureau et de serveur à partir de [RStudio](https://www.rstudio.com/products/rstudio-server/)ou basés sur Eclipse de Walware [StatET](http://www.walware.de/goto/statet). Parmi les plus populaires sur Linux est l’utilisation du [Serveur de RStudio](https://www.rstudio.com/products/rstudio-server/) qui fournit un environnement IDE basé sur navigateur pour une utilisation par des clients distants.  L’installation serveur de RStudio sur le nœud du bord d’un cluster HDInsight Premium offre une expérience d’IDE complète pour le développement et l’exécution de scripts de R R Server sur le cluster et peut être beaucoup plus productive que l’utilisation par défaut de la Console de R.

Dans cet article, vous allez apprendre à installer la version de (gratuit) communautaire de RStudio Server sur le nœud du bord d’un cluster à l’aide d’un script personnalisé. Si vous préférez la version Pro dans le commerce sous licence du serveur de RStudio, vous devez suivre les instructions d’installation de [RStudio Server](https://www.rstudio.com/products/rstudio/download-server/).

> [AZURE.NOTE] Les étapes de ce document requièrent un serveur R sur cluster de HDInsight et ne fonctionnera pas correctement si vous utilisez un cluster HDInsight où R a été installé à l’aide de l' [Action du Script de R installer](hdinsight-hadoop-r-scripts-linux.md).

## <a name="prerequisites"></a>Conditions préalables

* Un cluster Azure HDInsight et R Server est installé. Pour obtenir des instructions, reportez-vous à la section [mise en route du serveur R sur les clusters de HDInsight](hdinsight-hadoop-r-server-get-started.md).
* Un client SSH. Pour les distributions de Linux et Unix ou Macintosh OS X, la `ssh` commande est fourni avec le système d’exploitation. Pour Windows, nous vous recommandons de [Cygwin](http://www.redhat.com/services/custom/cygwin/) avec [l’option d’OpenSSH](https://www.youtube.com/watch?v=CwYSvvGaiWU), ou [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).  


## <a name="install-rstudio-on-the-cluster-using-a-custom-script"></a>Installation de RStudio sur le cluster à l’aide d’un script personnalisé

1. Identifier le nœud de bord du cluster. Pour un cluster HDInsight avec serveur de R, voici la convention d’affectation de noms pour le nœud de tête et de nœud de bord.

    * Nœud de tête :`CLUSTERNAME-ssh.azurehdinsight.net`
    * Nœud de bord :`R-Server.CLUSTERNAME-ssh.azurehdinsight.net` 

2. SSH dans le nœud du bord du cluster en utilisant le modèle d’affectation de noms ci-dessus. 
 
    * Si vous vous connectez à partir d’un client Linux, voir [se connecter à un cluster basé sur Linux de HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster).
    * Si vous vous connectez à partir d’un client Windows, voir [se connecter à un cluster basé sur Linux de HDInsight à l’aide de PuTTY](hdinsight-hadoop-linux-use-ssh-windows.md#connect-to-a-linux-based-hdinsight-cluster).

3. Une fois que vous êtes connecté, devenir un utilisateur racine sur le cluster. Dans la session SSH, utilisez la commande suivante.

        sudo su -

4. Télécharger le script personnalisé pour installer RStudio. Utilisez la commande suivante.

        wget http://mrsactionscripts.blob.core.windows.net/rstudio-server-community-v01/InstallRStudio.sh

5. Modifier les autorisations sur le fichier de script personnalisés et exécuter le script. Utilisez les commandes suivantes.

        chmod 755 InstallRStudio.sh
        ./InstallRStudio.sh

6. Si vous avez utilisé un mot de passe SSH lors de la création d’un cluster HDInsight avec serveur de R, vous pouvez ignorer cette étape et passez à la suivante. Si vous avez utilisé un code SSH d’à la place pour créer le cluster, vous devez définir un mot de passe pour votre utilisateur SSH. Vous en aurez besoin lors de la connexion à RStudio. Exécutez les commandes suivantes. Lorsque vous y êtes invité pour un **mot de passe Kerberos en cours**, appuyez simplement sur **entrée**.  Notez que vous devez remplacer `USERNAME` avec un utilisateur SSH pour votre cluster HDInsight.

        passwd USERNAME
        Current Kerberos password:
        New password:
        Retype new password:
        Current Kerberos password:
        
    Si votre mot de passe est correctement défini, vous devez voir un message comme suit.

        passwd: password updated successfully


    Quittez la session SSH.

7. Créer un tunnel SSH pour le cluster en mappant `localhost:8787` sur le cluster HDInsight sur l’ordinateur client. Vous devez créer un tunnel SSH avant d’ouvrir une nouvelle session de navigateur.

    * Sur un client Linux ou un client Windows [Cygwin](http://www.redhat.com/services/custom/cygwin/) puis ouvrir une session Terminal Server et utilisez la commande suivante.

            ssh -L localhost:8787:localhost:8787 USERNAME@R-Server.CLUSTERNAME-ssh.azurehdinsight.net
            
        Remplacez le **nom d’utilisateur** avec un utilisateur SSH pour votre cluster HDInsight et **NOM_CLUSTER** avec le nom de votre cluster HDInsight vous pouvez également utiliser un code SSH, et non un mot de passe en ajoutant`-i id_rsa_key`     

    * Si vous utilisez un client Windows avec PuTTY, puis

        1.  Ouvrez PuTTY et entrez vos informations de connexion. Si vous n’êtes pas familiarisé avec PuTTY, consultez [Utiliser SSH avec basé sur Linux d’Hadoop sur HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md) pour plus d’informations sur la façon de l’utiliser avec HDInsight.
        2.  Dans la section **catégories** , à gauche de la boîte de dialogue, développez la **connexion**et développez le **SSH**puis sélectionnez **Tunnels**.
        3.  Sur l’écran des **Options de contrôle de réacheminement de port SSH** , fournissez les informations suivantes :

            * **Port source** : le port sur le client que vous souhaitez transférer. Par exemple, **8787**.
            * **Destination** - la destination doit être mappée à l’ordinateur client local. Par exemple, **localhost:8787**.

            ![Créer un tunnel SSH] (./media/hdinsight-hadoop-r-server-install-r-studio/createsshtunnel.png "Créer un tunnel SSH")

        4. Cliquez sur **Ajouter** pour ajouter les paramètres, puis cliquez sur **Ouvrir** pour ouvrir une connexion SSH.
        5. Lorsque vous y êtes invité, ouvrez une session sur le serveur. Vous établissez une session SSH et activer le tunnel.

8. Ouvrez un navigateur web et entrez l’URL suivante est basée sur le port que vous avez entré pour le tunnel.

        http://localhost:8787/ 

9. Vous devrez entrer le nom d’utilisateur SSH et d’un mot de passe pour se connecter au cluster. Si vous avez utilisé un code SSH lors de la création du cluster, vous devez entrer le mot de passe que vous avez créé à l’étape 5 ci-dessus.

    ![Se connecter à R Studio] (./media/hdinsight-hadoop-r-server-install-r-studio/connecttostudio.png "Créer un tunnel SSH")

10. Pour vérifier si l’installation de RStudio a réussi, vous pouvez exécuter un test de script qui s’exécute de R en fonction des travaux MapReduce et Spark sur le cluster. Revenez à la console SSH et entrez les commandes suivantes pour télécharger le script de test à exécuter dans RStudio.

    * Si vous avez créé un cluster Hadoop avec R, utilisez cette commande.
        
            wget http://mrsactionscripts.blob.core.windows.net/rstudio-server-community-v01/testhdi.r

    * Si vous avez créé un cluster d’allumage avec R, utilisez cette commande.

            wget http://mrsactionscripts.blob.core.windows.net/rstudio-server-community-v01/testhdi_spark.r

11. Dans RStudio, vous verrez le script de test que vous avez téléchargé. Double-cliquez sur le fichier pour l’ouvrir, sélectionnez le contenu du fichier, puis cliquez sur **exécuter**. Vous devez voir la sortie dans le volet de la **Console** .
 
    ![Test de l’installation] (./media/hdinsight-hadoop-r-server-install-r-studio/test-r-script.png "Test de l’installation")

Une autre option consisterait à taper `source(testhdi.r)` ou `source(testhdi_spark.r)` pour exécuter le script.

## <a name="see-also"></a>Voir aussi

- [Calculer les options de contexte pour R Server sur des clusters de HDInsight](hdinsight-hadoop-r-server-compute-contexts.md)

- [Options de stockage Azure pour R Server sur HDInsight prime](hdinsight-hadoop-r-server-storage.md)


 
