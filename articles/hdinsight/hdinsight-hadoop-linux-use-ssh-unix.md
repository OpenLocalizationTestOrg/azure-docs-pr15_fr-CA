<properties
   pageTitle="Utiliser des clés SSH avec basé sur Linux d’Hadoop à partir d’OS X, Unix ou Linux | Microsoft Azure"
   description=" Vous pouvez accéder à HDInsight basé sur Linux à l’aide de SSH (Secure Shell). Ce document fournit des informations sur l’utilisation du protocole SSH avec HDInsight à partir de clients OS X, Unix ou Linux."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"
    tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/13/2016"
   ms.author="larryfr"/>

#<a name="use-ssh-with-linux-based-hadoop-on-hdinsight-from-linux-unix-or-os-x"></a>Utiliser le protocole SSH avec basé sur Linux d’Hadoop sur HDInsight à partir d’OS X, Unix ou Linux

> [AZURE.SELECTOR]
- [Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
- [Linux, Unix, OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

[SSH (Secure Shell)](https://en.wikipedia.org/wiki/Secure_Shell) permet d’effectuer des opérations sur vos clusters basés sur Linux de HDInsight à l’aide d’une interface de ligne de commande à distance. Ce document fournit des informations sur l’utilisation du protocole SSH avec HDInsight à partir de clients OS X, Unix ou Linux.

> [AZURE.NOTE] Les étapes décrites dans cet article supposent que vous utilisez un client OS X, Unix ou Linux. Ces étapes peuvent être exécutées sur un client fonctionnant sous Windows, si vous avez installé un package qui fournit `ssh` et `ssh-keygen`, par exemple [Bash sur Ubuntu sous Windows](https://msdn.microsoft.com/commandline/wsl/about).
>
> Si vous ne disposez pas d’un SSH installé sur votre ordinateur client Windows, suivez les étapes dans [Utiliser SSH avec basé sur Linux HDInsight (Hadoop) à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md) pour plus d’informations sur l’installation et à l’aide de PuTTY.

##<a name="prerequisites"></a>Conditions préalables

* **SSH keygen** et **ssh** pour les clients Linux, Unix et OS X. Cette utilitaires sont généralement fournis avec votre système d’exploitation, ou disponibles sur le système de gestion de package.

* Un navigateur web moderne qui prend en charge d’HTML5.

OU

* [CLI azure](../xplat-cli-install.md).

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)] 

##<a name="what-is-ssh"></a>Quel est le SSH ?

SSH est un utilitaire de connexion et exécuter à distance les commandes sur un serveur distant. HDInsight basé sur Linux, SSH établit une connexion cryptée pour la headnode de cluster et fournit une ligne de commande qui vous permet de taper des commandes. Commandes sont ensuite exécutées directement sur le serveur.

###<a name="ssh-user-name"></a>Nom d’utilisateur SSH

Un nom d’utilisateur SSH est le nom que vous utilisez pour s’authentifier sur le cluster de HDInsight. Lorsque vous spécifiez un nom d’utilisateur SSH lors de la création du cluster, cet utilisateur est créé sur tous les nœuds du cluster. Une fois que le cluster est créé, vous pouvez utiliser ce nom d’utilisateur pour se connecter à la headnodes de cluster HDInsight. À partir de la headnodes, vous pouvez ensuite connecter aux nœuds du travailleur.

###<a name="ssh-password-or-public-key"></a>Mot de passe SSH ou clé publique

Un utilisateur SSH peut utiliser soit un mot de passe ou la clé publique pour l’authentification. Un mot de passe est simplement une chaîne de texte que vous apportez, alors qu’une clé publique est partie d’une paire de clés cryptographiques générée pour vous identifier de manière unique.

Une clé est plus sûre que le mot de passe, mais il nécessite des étapes supplémentaires pour générer la clé et vous devez conserver les fichiers contenant la clé dans un emplacement sécurisé. Si tout le monde accède aux fichiers de clés, ils ont accès à votre compte. Ou, si vous perdez les fichiers de clés, vous ne serez pas en mesure de se connecter à votre compte.

Une paire de clés est constituée d’une clé publique (qui est envoyée au serveur HDInsight) et une clé privée (qui est conservée sur votre ordinateur client.) Lorsque vous vous connectez au serveur HDInsight à l’aide de SSH, le client SSH utilise la clé privée sur votre ordinateur pour s’authentifier auprès du serveur.

##<a name="create-an-ssh-key"></a>Créer un code SSH

Utilisez les informations suivantes si vous prévoyez d’utiliser des clés SSH avec votre cluster. Si vous envisagez l’utilisation d’un mot de passe, vous pouvez ignorer cette section.

1. Ouvrez une session Terminal Server et utilisez la commande suivante pour voir si vous avez des clés SSH existants :

        ls -al ~/.ssh

    Recherchez les fichiers suivants dans la liste du répertoire. Il s’agit des noms communs pour les clés publiques SSH.

    * ID\_dsa.pub
    * ID\_ecdsa.pub
    * ID\_ed25519.pub
    * ID\_rsa.pub

2. Si vous ne souhaitez pas utiliser un fichier existant ou si vous n’avez aucune clé SSH existant, utilisez la suivante pour générer un nouveau fichier :

        ssh-keygen -t rsa

    Vous demandera les informations suivantes :

    * L’emplacement du fichier - l’emplacement par défaut ~/.ssh/id\_rsa.
    * Un mot de passe - vous serez invité à entrer ce.

        > [AZURE.NOTE] Nous vous recommandons fortement d’utiliser un mot de passe sécurisé pour la clé. Toutefois, si vous oubliez le mot de passe, il n’existe aucun moyen de le récupérer.

    Une fois la commande terminée, vous aurez deux nouveaux fichiers, la clé privée (par exemple, **id\_rsa**) et la clé publique (par exemple, **id\_rsa.pub**).

##<a name="create-a-linux-based-hdinsight-cluster"></a>Création d’un cluster basé sur Linux de HDInsight

Lors de la création d’un cluster basé sur Linux de HDInsight, vous devez fournir la clé publique créée précédemment. À partir de clients OS X, Unix ou Linux, il existe deux façons de créer un cluster de HDInsight :

* **Azure Portal** - utilise un portail web pour créer le cluster.

* **Infrastructure du langage commun pour Mac, Linux et Windows azure** - utilise des commandes de ligne de commande pour créer le cluster.

Chacune de ces méthodes nécessite un mot de passe ou une clé publique. Pour plus d’informations sur la création d’un cluster basé sur Linux de HDInsight, consultez [les clusters basés sur Linux de fourniture de HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

###<a name="azure-portal"></a>Azure Portal

Lorsque vous utilisez le [Portail Azure] [ preview-portal] pour créer un cluster basé sur Linux de HDInsight, vous devez entrer un **Nom d’utilisateur SSH**et sélectionnez cette option pour entrer un **mot de passe** ou la **Clé publique SSH**.

Si vous sélectionnez la **Clé publique SSH**, vous pouvez coller la clé publique (contenue dans le fichier avec l’extension **.pub** ) dans le champ __PublicKey SSH__ , ou activez l’option __Sélectionner un fichier__ pour parcourir et sélectionner le fichier de clé publique.

![Image du formulaire de demande de clé publique](./media/hdinsight-hadoop-linux-use-ssh-unix/ssh-key.png)

> [AZURE.NOTE] Le fichier de clé est simplement un fichier texte. Le contenu doit ressembler à ce qui suit :
> ```
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCelfkjrpYHYiks4TM+r1LVsTYQ4jAXXGeOAF9Vv/KGz90pgMk3VRJk4PEUSELfXKxP3NtsVwLVPN1l09utI/tKHQ6WL3qy89WVVVLiwzL7tfJ2B08Gmcw8mC/YoieT/YG+4I4oAgPEmim+6/F9S0lU2I2CuFBX9JzauX8n1Y9kWzTARST+ERx2hysyA5ObLv97Xe4C2CQvGE01LGAXkw2ffP9vI+emUM+VeYrf0q3w/b1o/COKbFVZ2IpEcJ8G2SLlNsHWXofWhOKQRi64TMxT7LLoohD61q2aWNKdaE4oQdiuo8TGnt4zWLEPjzjIYIEIZGk00HiQD+KCB5pxoVtp user@system
> ```

Cette opération crée une connexion d’accès pour l’utilisateur spécifié, en utilisant le mot de passe ou la clé publique que vous fournissez.

###<a name="azure-command-line-interface-for-mac-linux-and-windows"></a>Interface de ligne de commande Azure pour Mac, Linux et Windows

Vous pouvez utiliser l' [Infrastructure du langage commun pour Mac, Linux et Windows Azure](../xplat-cli-install.md) pour créer un nouveau cluster à l’aide de la `azure hdinsight cluster create` commande.

Pour plus d’informations sur l’utilisation de cette commande, voir [clusters de fourniture Hadoop Linux dans HDInsight à l’aide des options personnalisées](hdinsight-hadoop-provision-linux-clusters.md).

##<a name="connect-to-a-linux-based-hdinsight-cluster"></a>Se connecter à un cluster basé sur Linux de HDInsight

À partir d’une session Terminal Server, utilisez la commande SSH pour se connecter à la headnode de cluster en fournissant l’adresse et nom d’utilisateur :

* **Adresse SSH** - il y a deux adresses qui peuvent être utilisés pour se connecter à un cluster à l’aide de SSH :

    * **Se connecter à la headnode**: le nom du cluster, suivi de **-ssh.azurehdinsight.net**. Par exemple, **mon_cluster-ssh.azurehdinsight.net**.
    
    * **Se connecter au nœud de bord**: Si votre cluster est serveur R sur HDInsight, le cluster contient également un nœud qui est accessible à l’aide de **RServer.CLUSTERNAME.ssh.azurehdinsight.net**, dans laquelle __CLUSTERNAME__ est le nom du cluster.

* **Nom d’utilisateur** - SSH le nom d’utilisateur fourni lors de la création du cluster.

L’exemple suivant se connectera à la headnode principale de **mon_cluster** utilisateur **me**:

    ssh me@mycluster-ssh.azurehdinsight.net

Si vous avez utilisé un mot de passe pour le compte d’utilisateur, vous êtes invité à entrer le mot de passe.

Si vous avez utilisé un code SSH sécurisé avec un mot de passe, vous êtes invité à entrer le mot de passe. Dans le cas contraire, SSH va tenter d’authentifier automatiquement à l’aide d’une des clés privées locales sur votre client.

> [AZURE.NOTE] Si SSH n’authentifie pas automatiquement avec la clé privée correcte, utilisez le paramètre **-i** et spécifiez le chemin d’accès à la clé privée. L’exemple suivant charge la clé privée à partir de `~/.ssh/id_rsa`:
>
> `ssh -i ~/.ssh/id_rsa me@mycluster-ssh.azurehdinsight.net`

Si vous vous connectez à l’aide de l’adresse pour le headnode, et aucun port n’est spécifié, SSH utilise par défaut le port 22, qui se connectera à l’headnode primaire sur le cluster de HDInsight. Si vous utilisez le port 23, vous connectera sur le site secondaire. Pour plus d’informations sur le headnodes, voir la [disponibilité et la fiabilité des clusters Hadoop dans HDInsight](hdinsight-high-availability-linux.md).

###<a name="connect-to-worker-nodes"></a>Se connecter aux nœuds de travailleur

Les nœuds de travail ne sont pas directement accessibles depuis l’extérieur du centre de données Azure, mais ils sont accessibles à partir de la headnode de cluster via le protocole SSH.

Si vous utilisez un code SSH pour authentifier votre compte d’utilisateur, vous devez effectuer les étapes suivantes sur votre client :

1. À l’aide d’un éditeur de texte, ouvrez `~/.ssh/config`. Si ce fichier n’existe pas, vous pouvez le créer en entrant `touch ~/.ssh/config` dans le terminal.

2. Ajoutez le code suivant du fichier. Remplacez *NOM_CLUSTER* par le nom de votre cluster HDInsight.

        Host CLUSTERNAME-ssh.azurehdinsight.net
          ForwardAgent yes

    Cela configure agent SSH pour votre cluster HDInsight de transfert.

3. Agent SSH de transfert à l’aide de la commande suivante à partir du terminal de test :

        echo "$SSH_AUTH_SOCK"

    Il doit retourner d’informations semblable au suivant :

        /tmp/ssh-rfSUL1ldCldQ/agent.1792

    Si rien n’est renvoyé, cela indique **ssh-agent** n’est pas en cours d’exécution. Documentation de votre système d’exploitation pour des opérations spécifiques sur l’installation et la configuration **ssh-agent**, ou consultez [l’aide de ssh-agent avec ssh](http://mah.everybody.org/docs/ssh).

4. Une fois que vous avez vérifié **ssh-agent** est en cours d’exécution, utilisez la suivante pour ajouter votre clé privée SSH à l’agent :

        ssh-add ~/.ssh/id_rsa

    Si votre clé privée est stockée dans un fichier différent, remplacez `~/.ssh/id_rsa` avec le chemin d’accès au fichier.

Utilisez les étapes suivantes pour vous connecter aux nœuds de votre cluster de travailleur.

> [AZURE.IMPORTANT] Si vous utilisez un code SSH pour authentifier votre compte, vous devez effectuer les étapes précédentes pour vérifier que l’agent de transfert fonctionne.

1. Connectez-vous au cluster HDInsight à l’aide de SSH comme décrit précédemment.

2. Une fois que vous êtes connecté, utilisez les éléments suivants pour récupérer une liste des nœuds de votre cluster. Remplacez *ADMINPASSWORD* avec le mot de passe pour votre compte d’administrateur de cluster. Remplacez *NOM_CLUSTER* par le nom de votre cluster.

        curl --user admin:ADMINPASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/hosts

    Ceci renverra des informations au format JSON pour les nœuds dans le cluster, y compris les `host_name`, qui contient le nom de domaine pleinement qualifié (FQDN) pour chaque nœud. Voici un exemple d’une `host_name` entrée renvoyée par la commande de **coin** :

        "host_name" : "workernode0.workernode-0-e2f35e63355b4f15a31c460b6d4e1230.j1.internal.cloudapp.net"

3. Une fois que vous avez une liste des nœuds du travailleur que vous souhaitez vous connecter, utilisez la commande suivante à partir de la session SSH sur le serveur pour ouvrir une connexion à un nœud de travail :

        ssh USERNAME@FQDN

    Remplacez le *nom d’utilisateur* avec votre nom d’utilisateur SSH et le *nom de domaine complet* avec le nom de domaine complet du nœud du travailleur. Par exemple, `workernode0.workernode-0-e2f35e63355b4f15a31c460b6d4e1230.j1.internal.cloudapp.net`.

    > [AZURE.NOTE] Si vous utilisez un mot de passe pour l’authentification de la session SSH, vous serez invité à entrer le mot de passe à nouveau. Si vous utilisez un code SSH, la connexion doit terminer sans invite.

4. Une fois la session établie, l’invite de Terminal Server pourra `username@hn#-clustername` de `username@wk#-clustername` pour indiquer que vous êtes connecté sur le nœud du travailleur. Les commandes que vous exécutez à ce stade seront exécute sur le nœud du travailleur.

4. Une fois que vous avez terminé d’exécuter des actions sur le noeud de travail, utiliser la `exit` la commande Fermer la session sur le nœud du travailleur. Cela vous renverra à la `username@hn#-clustername` invite.

## <a name="connect-to-a-domain-joined-hdinsight-cluster"></a>Se connecter à un cluster à un domaine de HDInsight

[HDInsight de joints au domaine](hdinsight-domain-joined-introduction.md) intègre Kerberos Hadoop dans HDInsight. Car l’utilisateur SSH n’est pas un utilisateur du domaine Active Directory Active, ce compte d’utilisateur ne peut pas exécuter les commandes Hadoop à partir de SSH shell sur un cluster à un domaine directement. Vous devez d’abord exécuter *kinit* . 

**Pour exécuter la ruche des requêtes sur un cluster à un domaine de HDInsight à l’aide de SSH**

1. Se connecter à un cluster à un domaine de HDInsight à l’aide de SSH.  Pour instrocutions, voir [se connecter à un cluster basé sur Linux de HDInsight](#connect-to-a-linux-based-hdinsight-cluster).
2. Exécutez kinit. Il vous demandera un nom d’utilisateur et le mot de passe de domaine utilisateur. Pour plus d’informations sur Configurer des utilisateurs du domaine pour les clusters de HDInsight à un domaine, voir [clusters de HDInisight de configuration à un domaine](hdinsight-domain-joined-configure.md).

    ![Kinit Hadoop à un domaine de HDInsight](./media/hdinsight-hadoop-linux-use-ssh-unix/hdinsight-domain-joined-hadoop-kinit.png)
3. Ouvrir la console de la ruche par entrer :

        hive

    Vous pouvez ensuite exécuter des commandes de la ruche.

##<a name="add-more-accounts"></a>Ajouter d’autres comptes

1. Générer une nouvelle clé publique et une clé privée pour le nouveau compte d’utilisateur, comme décrit dans la section [Création d’un code SSH](#create-an-ssh-key-optional) .

    > [AZURE.NOTE] La clé privée doit être générée sur un client pour que les utilisateurs utiliseront pour se connecter au cluster, ou en toute sécurité transférée à un tel client après sa création.

1. À partir d’une session SSH pour le cluster, ajouter le nouvel utilisateur avec la commande suivante :

        sudo adduser --disabled-password <username>

    Cela créera un nouveau compte d’utilisateur, mais vous permet de désactiver l’authentification du mot de passe.

2. Créer le répertoire et les fichiers pour contenir la clé en utilisant les commandes suivantes :

        sudo mkdir -p /home/<username>/.ssh
        sudo touch /home/<username>/.ssh/authorized_keys
        sudo nano /home/<username>/.ssh/authorized_keys

3. Lorsque l’éditeur nano s’ouvre, copiez et collez le contenu de la clé publique pour le nouveau compte d’utilisateur. Enfin, utilisez **Ctrl-X** pour enregistrer le fichier et quittez l’éditeur.

    ![image de nano-éditeur avec l’exemple de clé](./media/hdinsight-hadoop-linux-use-ssh-unix/nano.png)

4. Pour changer le propriétaire du dossier du .ssh et du contenu pour le nouveau compte d’utilisateur, utilisez la commande suivante :

        sudo chown -hR <username>:<username> /home/<username>/.ssh

5. Vous devez maintenant être en mesure de s’authentifier sur le serveur avec la clé privée et un nouveau compte d’utilisateur.

##<a id="tunnel"></a>Tunneling de SSH

SSH peut servir pour les demandes locales, telles que des demandes web, au cluster HDInsight du tunnel. La demande est ensuite dirigée vers la ressource demandée comme s’il avait provenant de la headnode de cluster HDInsight.

> [AZURE.IMPORTANT] Un tunnel SSH est obligatoire pour accéder à l’interface utilisateur web pour certains services Hadoop. Par exemple, l’interface utilisateur de l’historique de travail ou de l’interface utilisateur du Gestionnaire de ressources seulement sont accessibles à l’aide d’un tunnel SSH.

Pour plus d’informations sur la création et à l’aide d’un tunnel SSH, consultez [Utiliser SSH Tunneling pour accéder à l’interface utilisateur web de Ambari, ResourceManager, JobHistory, NameNode, Oozie et autre web, l’interface utilisateur](hdinsight-linux-ambari-ssh-tunnel.md).

##<a name="next-steps"></a>Étapes suivantes

Maintenant que vous comprenez comment authentifier à l’aide d’un code SSH, découvrez comment utiliser MapReduce avec Hadoop sur HDInsight.

* [Utilisez la ruche avec HDInsight](hdinsight-use-hive.md)

* [Utilisez des porcs avec HDInsight](hdinsight-use-pig.md)

* [Utiliser des travaux de MapReduce avec HDInsight](hdinsight-use-mapreduce.md)

[preview-portal]: https://portal.azure.com/
