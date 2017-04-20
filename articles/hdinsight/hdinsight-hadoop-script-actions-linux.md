<properties
    pageTitle="Script de développement action avec basé sur Linux de HDInsight | Microsoft Azure"
    description="Comment personnaliser les clusters basés sur Linux de HDInsight avec une Action de Script. Actions de script sont un moyen de personnaliser Azure HDInsight clusters en spécifiant des paramètres de configuration de cluster ou en installant des outils, des services supplémentaires ou autres logiciels sur le cluster. "
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/05/2016"
    ms.author="larryfr"/>

# <a name="script-action-development-with-hdinsight"></a>Développement d’action de script avec HDInsight

Actions de script sont un moyen de personnaliser Azure HDInsight clusters en spécifiant des paramètres de configuration de cluster ou en installant des outils, des services supplémentaires ou autres logiciels sur le cluster. Vous pouvez utiliser les actions de script lors de la création du cluster ou sur un cluster en cours d’exécution.

> [AZURE.NOTE] Les informations contenues dans ce document sont spécifiques aux clusters de HDInsight de fonctionnant sous Linux. Pour plus d’informations sur l’utilisation des actions de script avec les clusters basés sur Windows, consultez [développement d’action de Script avec HDInsight (Windows)](hdinsight-hadoop-script-actions.md).

## <a name="what-are-script-actions"></a>Quelles sont les actions de script ?

Actions de script sont des scripts Bash Azure s’exécute sur les nœuds de cluster à apporter des modifications de configuration ou d’installation du logiciel. Une action de script est exécutée en tant que root et fournit des droits d’accès complets aux nœuds du cluster.

Actions de script peuvent être appliquées via les méthodes suivantes :

| Utilisez cette option pour appliquer un script... | Lors de la création du cluster... | Sur un cluster en cours d’exécution... |
| ----- |:-----:|:-----:|
| Azure Portal | ✓ | ✓ |
| PowerShell Azure | ✓ | ✓ |
| CLI Azure | &nbsp; | ✓ |
| Kit de développement .NET HDInsight | ✓ | ✓ |
| Modèle de gestionnaire de ressources Azure | ✓ | &nbsp; |

Pour plus d’informations sur l’utilisation de ces méthodes pour appliquer des actions de script, voir [clusters de HDInsight de personnaliser à l’aide des actions de script](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="bestPracticeScripting"></a>Méthodes conseillées pour le développement de scripts

Lorsque vous développez un script personnalisé pour un cluster de HDInsight, il existe plusieurs pratiques recommandées pour la garder à l’esprit :

- [Cibler la version Hadoop](#bPS1)
- [Cibler la Version du système d’exploitation](#bps10)
- [Fournir des liens stables aux ressources de script](#bPS2)
- [Utiliser les ressources précompilées](#bPS4)
- [Assurez-vous que le script de personnalisation du cluster est idempotent](#bPS3)
- [Garantir la haute disponibilité de l’architecture de cluster](#bPS5)
- [Configurer les composants personnalisés pour utiliser le stockage des objets Blob Azure](#bPS6)
- [Écrire des informations dans STDOUT et STDERR](#bPS7)
- [Enregistrer les fichiers au format ASCII avec les fins de ligne LF](#bps8)
- [Logique des nouvelles tentatives permet de récupérer les erreurs transitoires](#bps9)

> [AZURE.IMPORTANT] Actions de script doivent se terminer dans les 60 minutes, ou ils n’expire. Au cours de la mise en service du nœud, le script est exécuté en même temps que les autres processus d’installation et de configuration. Concurrence pour les ressources de la bande passante du réseau ou de temps processeur peut provoquer le script de prendre plus de temps qu’il ne le fait dans votre environnement de développement.

### <a name="bPS1"></a>Cibler la version Hadoop

Différentes versions de HDInsight ont des versions différentes d’Hadoop services et des composants installés. Si votre script s’attend à une version spécifique d’un service ou un composant, vous devez uniquement utiliser le script avec la version de HDInsight qui contient les composants requis. Vous pouvez trouver des informations sur les versions des composants inclus dans HDInsight en utilisant le document de [contrôle de version de composant HDInsight](hdinsight-component-versioning.md) .

###<a name="bps10"></a>Cibler la version du système d’exploitation

HDInsight de basés sur Linux est basé sur la distribution Ubuntu Linux. Différentes versions de HDInsight reposent sur différentes versions d’Ubuntu, ce qui peut affecter le comportement de votre script. Par exemple, HDInsight 3.4 et les versions antérieur sont basés sur les versions d’Ubuntu qui utilisent Upstart. Version 3.5 est basée sur Ubuntu 16.04, qui utilise Systemd. Systemd et Upstart s’appuient sur les différentes commandes, afin que votre script doit être écrits pour fonctionner avec les deux.

Une autre différence importante entre les HDInsight 3.4 et 3.5 est qui `JAVA_HOME` pointe désormais vers Java 8.

Vous pouvez vérifier la version du système d’exploitation en utilisant `lsb_release`. Les extraits de code suivants à partir du script d’installation de teinte montre comment déterminer si l’exécution du script sur Ubuntu 14 ou 16 :

    OS_VERSION=$(lsb_release -sr)
    if [[ $OS_VERSION == 14* ]]; then
        echo "OS verion is $OS_VERSION. Using hue-binaries-14-04."
        HUE_TARFILE=hue-binaries-14-04.tgz
    elif [[ $OS_VERSION == 16* ]]; then
        echo "OS verion is $OS_VERSION. Using hue-binaries-16-04."
        HUE_TARFILE=hue-binaries-16-04.tgz
    fi
    ...
    if [[ $OS_VERSION == 16* ]]; then
        echo "Using systemd configuration"
        systemctl daemon-reload
        systemctl stop webwasb.service    
        systemctl start webwasb.service
    else
        echo "Using upstart configuration"
        initctl reload-configuration
        stop webwasb
        start webwasb
    fi
    ...
    if [[ $OS_VERSION == 14* ]]; then
        export JAVA_HOME=/usr/lib/jvm/java-7-openjdk-amd64
    elif [[ $OS_VERSION == 16* ]]; then
        export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
    fi

Vous trouverez le script complet qui contient ces extraits de code à https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh.

Pour la version d’Ubuntu qui est utilisé par HDInsight, consultez le document de la [version du composant HDInsight](hdinsight-component-versioning.md) .

Pour comprendre la différence entre Systemd et Upstart, reportez-vous à [Systemd pour les utilisateurs de Upstart](https://wiki.ubuntu.com/SystemdForUpstartUsers).

### <a name="bPS2"></a>Fournir des liens stables aux ressources de script

Il se peut que vous devez vous assurer que les scripts et les ressources utilisées par le script restent disponibles pendant la durée de vie du cluster, et que les versions de ces fichiers ne changent pas pendant toute la durée. Ces ressources sont requises si de nouveaux nœuds sont ajoutés au cluster au cours de la mise à l’échelle des opérations.

La meilleure solution consiste à télécharger et archiver tous les éléments dans un compte de stockage Azure sur votre abonnement.

> [AZURE.IMPORTANT] Le compte de stockage utilisé doit être le compte de stockage par défaut pour le cluster ou d’un conteneur public en lecture seule sur un autre compte de stockage.

Par exemple, les échantillons fournis par Microsoft sont stockés dans le compte de stockage [https://hdiconfigactions.blob.core.windows.net/](https://hdiconfigactions.blob.core.windows.net/) , qui est un conteneur public en lecture seule, géré par l’équipe HDInsight.

### <a name="bPS4"></a>Utiliser les ressources précompilées

Pour réduire le temps que nécessaire pour exécuter le script, éviter les opérations de compilation des ressources à partir du code source. Au lieu de cela, les ressources de la précompilation et stocker la version binaire dans le stockage des objets Blob Azure afin qu’elle peut rapidement être téléchargée sur le cluster à partir de votre script.

### <a name="bPS3"></a>Assurez-vous que le script de personnalisation du cluster est idempotent

Les scripts doivent être conçus pour être idempotents en ce sens que si le script est exécuté plusieurs fois, il doit s’assurer que le cluster est retourné à l’état chaque fois qu’il est exécuté.

Par exemple, si un script personnalisé installe une application au niveau local/bin sur sa première exécution, puis lors de chaque exécution ultérieure, le script doit vérifier si l’application existe déjà à l’emplacement local/bin avant de continuer avec d’autres étapes dans le script.

### <a name="bPS5"></a>Garantir la haute disponibilité de l’architecture de cluster

Linux HDInsight grappes de serveurs fournit deux nœuds têtes qui sont actifs dans le cluster et l’exécution de script sont des actions pour les deux nœuds. Si les composants que vous installez n'indique qu’un seul nœud de tête, vous devez créer un script qui installe uniquement le composant sur l’un des deux nœuds dans le cluster principal.

> [AZURE.IMPORTANT] Services par défaut installés dans le cadre de HDInsight sont conçus pour basculer entre les deux nœuds de tête, le cas échéant, toutefois cette fonctionnalité n’est pas étendue aux composants personnalisés installés par l’intermédiaire des ctions de script. Si vous avez besoin des composants installés par une action de script pour être hautement disponible, vous devez implémenter votre propre mécanisme de basculement qui utilise les deux nœuds de têtes disponibles.

### <a name="bPS6"></a>Configurer les composants personnalisés pour utiliser le stockage des objets Blob Azure

Les composants que vous installez sur le cluster peuvent avoir une configuration par défaut qui utilise le système de fichier distribué (très) Hadoop stockage. HDInsight utilise le stockage de Blob Azure (WASB) comme l’espace de stockage par défaut. Cela fournit un système de fichier compatible avec très qui conserve les données même si le cluster est supprimé. Vous devez configurer les composants que vous installez pour utiliser WASB plutôt que de très.

Par exemple, le texte suivant copie le fichier giraph-examples.jar à partir du système de fichiers local dans WASB :

    hadoop fs -copyFromLocal /usr/hdp/current/giraph/giraph-examples.jar /example/jars/

### <a name="bPS7"></a>Écrire des informations dans STDOUT et STDERR

Les informations écrites dans STDOUT et STDERR pendant l’exécution du script sont enregistrées et peuvent être affichées à l’aide de l’interface utilisateur de Ambari web.

> [AZURE.NOTE] Ambari sera uniquement disponible si le cluster est créé avec succès. Si vous utilisez une action de script lors de la création du cluster, et la création échoue, consultez la section Dépannage [que des clusters HDInsight de personnaliser à l’aide des actions de script](hdinsight-hadoop-customize-cluster-linux.md#troubleshooting) pour d’autres façons d’accéder aux informations enregistrées.

La plupart des utilitaires et des packages d’installation déjà écrit les informations dans STDOUT et STDERR, cependant, vous pouvez souhaiter ajouter de journalisation supplémentaires. Pour envoyer du texte à l’utilisation STDOUT `echo`. Par exemple :

        echo "Getting ready to install Foo"

Par défaut, `echo` envoie la chaîne à STDOUT. Pour orienter vers STDERR, ajoutez `>&2` avant de `echo`. Par exemple :

        >&2 echo "An error occurred installing Foo"

Ce redirige les informations envoyées à STDOUT (1, qui est par défaut donc ne pas répertorié ici,) vers STDERR (2). Pour plus d’informations sur la redirection des e/s, consultez [http://www.tldp.org/LDP/abs/html/io-redirection.html](http://www.tldp.org/LDP/abs/html/io-redirection.html).

Pour plus d’informations sur l’affichage des informations consignées par les actions de script, voir [clusters de HDInsight de personnaliser à l’aide des actions de script](hdinsight-hadoop-customize-cluster-linux.md#troubleshooting)

###<a name="bps8"></a>Enregistrer les fichiers au format ASCII avec les fins de ligne LF

Les scripts Bash doivent être stockées en tant que format ASCII, avec des lignes interrompus par un saut de ligne. Si les fichiers sont stockés en tant que UTF-8, ce qui peut inclure une marque d’ordre d’octet au début du fichier, ou avec les fins de ligne de CRLF, ce qui est courant pour les éditeurs de Windows, le script échoue avec des erreurs similaires à ce qui suit :

    $'\r': command not found
    line 1: #!/usr/bin/env: No such file or directory

###<a name="bps9"></a>Logique des nouvelles tentatives permet de récupérer les erreurs transitoires

Lorsque vous téléchargez des fichiers, l’installation des packages à l’aide d’apt-get, ou autres actions qui transmettent les données via internet, l’action peut échouer en raison d’erreurs de réseau transitoire. Par exemple, la ressource distante, avec que vous communiquez peut être en train de basculer vers un nœud de secours.

Pour rendre votre script résistant aux erreurs transitoires, vous pouvez implémenter la logique des nouvelles tentatives. Voici un exemple d’une fonction qui exécutera toute commande passée et (si la commande échoue,) réessayer jusqu'à trois fois. Il attend deux secondes entre chaque tentative.

    #retry
    MAXATTEMPTS=3

    retry() {
        local -r CMD="$@"
        local -i ATTMEPTNUM=1
        local -i RETRYINTERVAL=2

        until $CMD
        do
            if (( ATTMEPTNUM == MAXATTEMPTS ))
            then
                    echo "Attempt $ATTMEPTNUM failed. no more attempts left."
                    return 1
            else
                    echo "Attempt $ATTMEPTNUM failed! Retrying in $RETRYINTERVAL seconds..."
                    sleep $(( RETRYINTERVAL ))
                    ATTMEPTNUM=$ATTMEPTNUM+1
            fi
        done
    }

Voici des exemples d’utilisation de cette fonction.

    retry ls -ltr foo

    retry wget -O ./tmpfile.sh https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh

## <a name="helpermethods"></a>Méthodes d’assistance pour les scripts personnalisés

méthodes d’application d’assistance de script action sont des utilitaires que vous pouvez utiliser lors de l’écriture de scripts personnalisés. Ceux-ci sont définis dans [https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh)et peuvent être incluses dans vos scripts en utilisant les éléments suivants :

    # Import the helper method module.
    wget -O /tmp/HDInsightUtilities-v01.sh -q https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh && source /tmp/HDInsightUtilities-v01.sh && rm -f /tmp/HDInsightUtilities-v01.sh

Ainsi, les applications d’assistance suivantes disponibles pour une utilisation dans votre script :

| Utilisation de l’application d’assistance | Description |
| ------------ | ----------- |
| `download_file SOURCEURL DESTFILEPATH [OVERWRITE]` | Télécharge un fichier à partir de l’URL source pour le chemin d’accès du fichier spécifié. Par défaut, il n’écrase pas un fichier existant. |
| `untar_file TARFILE DESTDIR` | Extrait un fichier tar (à l’aide de `-xf`,) vers le répertoire de destination. |
| `test_is_headnode` | Si avez exécuté sur un nœud principal de cluster, renvoie la valeur 1 ; Sinon, 0. |
| `test_is_datanode` | Si le nœud actuel est un nœud de données (travail), renvoie la valeur 1 ; Sinon, 0. |
| `test_is_first_datanode` | Si le nœud actuel est le premier nœud de données (travail) (nommé workernode0), renvoie la valeur 1 ; Sinon, 0. |
| `get_headnodes` | Renvoie le nom de domaine complet de l’headnodes dans le cluster. Les noms sont séparés par des virgules. Une chaîne vide est renvoyée sur l’erreur. |
| `get_primary_headnode` | Obtient le nom de domaine complet de l’headnode primaire. Une chaîne vide est renvoyée sur l’erreur. |
| `get_secondary_headnode` | Obtient le nom de domaine complet de l’headnode secondaire. Une chaîne vide est renvoyée sur l’erreur. |
| `get_primary_headnode_number` | Obtient le suffixe numérique de la headnode principale. Une chaîne vide est renvoyée sur l’erreur. |
| `get_secondary_headnode_number` | Obtient le suffixe numérique de l’headnode secondaire. Une chaîne vide est renvoyée sur l’erreur. |

## <a name="commonusage"></a>Modes d’utilisation courants

Cette section fournit des conseils sur l’implémentation des modes d’utilisation courants que vous pourriez rencontrer lors de l’écriture de votre propre script personnalisé.

### <a name="passing-parameters-to-a-script"></a>Passage de paramètres à un script

Dans certains cas, votre script peut nécessiter des paramètres. Par exemple, vous devrez peut-être le mot de passe administrateur du cluster pour récupérer des informations à partir de l’API REST de Ambari.

Les paramètres transmis au script sont connus comme _paramètres de positionnement_et sont affectées à `$1` pour le premier paramètre, `$2` pour le deuxième et une sorte. `$0`contient le nom du script lui-même.

Les valeurs transmises au script en tant que paramètres doivent figurer en guillemets simples (') afin que la valeur passée est traitée comme un littéral, et un traitement spécial n’est pas donné aux caractères inclus comme « ! ».

### <a name="setting-environment-variables"></a>Définition des variables d’environnement

Définissant une variable d’environnement est effectuée par le texte suivant :

    VARIABLENAME=value

Où NomVariable est le nom de la variable. Pour accéder à la variable après cela, vous devez utiliser `$VARIABLENAME`. Par exemple, pour affecter une valeur fournie par un paramètre de position sous la forme d’une variable d’environnement nommée de mot de passe, vous utilisez les éléments suivants :

    PASSWORD=$1

Utilisez ensuite l’accès aux informations suivantes `$PASSWORD`.

Variables d’environnement définies dans le script existent uniquement dans la portée du script. Dans certains cas, vous devrez peut-être ajouter des variables d’environnement de grand système persistent une fois le script terminé. Cela est généralement afin que les utilisateurs qui se connectent au cluster via le protocole SSH peuvent utiliser les composants installés par votre script. Vous pouvez accomplir cela en ajoutant la variable d’environnement `/etc/environment`. Par exemple, l’exemple suivant ajoute __HADOOP\_CONF\_DIR__:

    echo "HADOOP_CONF_DIR=/etc/hadoop/conf" | sudo tee -a /etc/environment

### <a name="access-to-locations-where-the-custom-scripts-are-stored"></a>Accès aux emplacements où sont stockés les scripts personnalisés

Les scripts permettent de personnaliser un cluster doivent soit être dans le compte de stockage par défaut pour le cluster ou, si un autre compte de stockage, dans un conteneur en lecture seule public. Si votre script accède aux ressources situées ailleurs celles-ci doivent également être dans accessibles publiquement (publics au moins en lecture seule). Vous pouvez par exemple souhaiter télécharger un fichier sur le cluster à l’aide de `download_file`.

Stockez le fichier dans un compte de stockage Azure accessible par le cluster (par exemple, le compte de stockage par défaut), fournissent un accès rapide, ce système de stockage se trouve dans le réseau d’Azure.

### <a name="checking-the-operating-system-version"></a>Vérification de la version du système d’exploitation

Différentes versions de HDInsight s’appuient sur des versions spécifiques d’Ubuntu. Il peut exister des différences entre les versions de système d’exploitation que vous devez vérifier dans votre script. Par exemple, vous devrez peut-être installer un fichier binaire qui est lié à la version d’Ubuntu.

Pour vérifier la version du système d’exploitation, utilisez `lsb_release`. Par exemple, le texte suivant montre comment faire référence à un fichier tar différents selon la version du système d’exploitation :

    OS_VERSION=$(lsb_release -sr)
    if [[ $OS_VERSION == 14* ]]; then
        echo "OS verion is $OS_VERSION. Using hue-binaries-14-04."
        HUE_TARFILE=hue-binaries-14-04.tgz
    elif [[ $OS_VERSION == 16* ]]; then
        echo "OS verion is $OS_VERSION. Using hue-binaries-16-04."
        HUE_TARFILE=hue-binaries-16-04.tgz
    fi

## <a name="deployScript"></a>Liste de contrôle pour le déploiement d’une action de script

Voici les étapes que nous avons pris avant de déployer ces scripts :

- Placez les fichiers qui contiennent les scripts personnalisés dans un emplacement accessible par les nœuds de cluster au cours du déploiement. Il peut s’agir d’une des autres comptes de stockage spécifiés au moment du déploiement du cluster, ou tout autre conteneur de stockage accessible au public ou par défaut.

- Ajouter des contrôles à des scripts pour vous assurer qu’ils exécutent impotently, afin que le script peut être exécuté plusieurs fois sur le même nœud.

- Un répertoire de fichiers temporaires/tmp permet de conserver les fichiers téléchargés, utilisés par les scripts et puis les nettoyer après que les scripts ont été exécutés.

- Dans le cas où les paramètres au niveau du système d’exploitation ou les fichiers de configuration de service Hadoop ont été modifiés, vous souhaiterez redémarrez HDInsight services afin qu’ils peuvent intercepter tous les paramètres au niveau du système d’exploitation, tels que les variables d’environnement définies dans les scripts.

## <a name="runScriptAction"></a>Comment faire pour exécuter une action de script

Actions de script vous permet de personnaliser les clusters HDInsight à l’aide du portail Azure, Azure PowerShell, modèles d’Azure Resource Manager (ARM) ou le Kit de développement .NET HDInsight. Pour obtenir des instructions, voir [comment utiliser l’action du script](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="sampleScripts"></a>Exemples de scripts personnalisés

Microsoft fournit des exemples de scripts pour installer les composants sur un cluster HDInsight. Exemples de scripts et des instructions sur la façon de les utiliser sont disponibles via les liens ci-dessous :

- [Installer et utiliser la teinte sur des clusters de HDInsight](hdinsight-hadoop-hue-linux.md)
- [Installer et utiliser R sur les clusters de HDInsight Hadoop](hdinsight-hadoop-r-scripts-linux.md)
- [Installer et utiliser le mode série sur LAN.r sur des clusters de HDInsight](hdinsight-hadoop-solr-install-linux.md)
- [Installer et utiliser le Giraph sur des clusters de HDInsight](hdinsight-hadoop-giraph-install-linux.md)  

> [AZURE.NOTE] Les documents liées ci-dessus sont spécifiques aux clusters de HDInsight de fonctionnant sous Linux. Pour les scripts qui fonctionnent avec HDInsight de basés sur Windows, reportez-vous à la section [développement d’action de Script avec HDInsight (Windows)](hdinsight-hadoop-script-actions.md) ou utiliser les liens disponibles en haut de chaque article.

##<a name="troubleshooting"></a>Résolution des problèmes

Vous pouvez rencontrer lors de l’utilisation de scripts que vous avez développé des erreurs sont les suivantes :

__Error__: `$'\r': command not found`. Parfois suivi par `syntax error: unexpected end of file`.

_Cause_: cette erreur se produit lorsque les lignes dans un script se terminent par CRLF. Les systèmes UNIX attendent LF uniquement en tant que la fin de la ligne.

Ce problème se produit généralement lorsque le script est créé dans un environnement Windows, comme le CRLF est une attitude commune fin pour de nombreux éditeurs de texte sous Windows.

_Résolution_: s’il s’agit d’une option dans votre éditeur de texte, sélectionnez format Unix ou saut de ligne pour la fin de la ligne. Vous pouvez également utiliser les commandes suivantes sur un système Unix pour modifier la séquence CRLF à un saut de ligne :

> [AZURE.NOTE] Les commandes suivantes sont à peu près équivalent dans la mesure où ils doivent modifier les fins de ligne CRLF de saut de ligne. Sélectionnez une basée sur les utilitaires disponibles sur votre système.

| Commande | Notes |
| ------- | ----- |
| `unix2dos -b INFILE` | Le fichier d’origine est sauvegardé avec un. Extension BAK |
| `tr -d '\r' < INFILE > OUTFILE` | FICHIER_SORTIE contiendra une version avec uniquement les fins de saut de ligne |
| `perl -pi -e 's/\r\n/\n/g' INFILE` | Cela va modifier le fichier directement sans création d’un fichier |
| ```sed 's/$'"/`echo \\\r`/" INFILE > OUTFILE``` | FICHIER_SORTIE contiendra une version avec uniquement les fins de saut de ligne.

__Error__: `line 1: #!/usr/bin/env: No such file or directory`.

_Cause_: cette erreur se produit lorsque le script a été enregistré en tant que UTF-8 avec une marque d’ordre d’octet (BOM).

_Résolution_: enregistrer le fichier au format ASCII ou UTF-8 sans une nomenclature. Vous pouvez également utiliser la commande suivante sur un système Linux ou Unix pour créer un nouveau fichier sans la nomenclature :

    awk 'NR==1{sub(/^\xef\xbb\xbf/,"")}{print}' INFILE > OUTFILE

Pour la commande ci-dessus, remplacez le fichier contenant la nomenclature __INFILE__ . __FICHIER_SORTIE__ doit être un nouveau nom de fichier, qui contienne le script sans la nomenclature.

## <a name="seeAlso"></a>Étapes suivantes

* Découvrez comment [les clusters HDInsight de personnaliser à l’aide des actions de script](hdinsight-hadoop-customize-cluster-linux.md)

* Utiliser la [référence du Kit de développement .NET HDInsight](https://msdn.microsoft.com/library/mt271028.aspx) pour en savoir plus sur la création d’applications .NET qui gèrent des HDInsight

* Utilisez l' [API REST de HDInsight](https://msdn.microsoft.com/library/azure/mt622197.aspx) pour apprendre à utiliser le reste pour effectuer des actions de gestion sur les clusters de HDInsight.
