<properties
   pageTitle="Déployer un nœud 3 Deis cluster | Microsoft Azure"
   description="Cet article décrit la procédure pour créer un nœud de 3 Deis cluster sur Azure à l’aide d’un modèle de gestionnaire de ressources Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="HaishiBai"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="06/24/2015"
   ms.author="hbai"/>

# <a name="deploy-a-3-node-deis-cluster"></a>Déployer un nœud 3 Deis de cluster

Cet article vous guide dans la mise en service un [Deis](http://deis.io/) cluster sur Azure. Il couvre toutes les étapes de la création des certificats nécessaires au déploiement et mise à l’échelle d’un exemple d’application **Aller** sur le cluster qui vient d’être mis en service.

Le diagramme suivant illustre l’architecture du système déployé. Un administrateur système gère le cluster à l’aide d’outils tels que **deis** et **deisctl**de Deis. Les connexions sont établies via un équilibreur de charge Azure, qui transfère les connexions à l’un des membre nœuds sur le cluster. L’accès de clients déployé des applications par l’intermédiaire de l’équilibreur de charge également. Dans ce cas, l’équilibreur de charge transmet le trafic à un Deis de maillage de routeur, lequel plus achemine le trafic vers les conteneurs correspondants Docker hébergé sur le cluster.

  ![Diagramme de l’architecture de cluster déployé de Desis](media/virtual-machines-linux-deis-cluster/architecture-overview.png)

Pour exécuter les étapes suivantes, vous aurez besoin :

 * Un abonnement Azure. Si vous n’en avez pas, vous pouvez obtenir une piste disponible sur [azure.com](https://azure.microsoft.com/).
 * Un id de travail ou de l’école à utiliser des groupes de ressources Azure. Si vous avez un compte personnel et un journal à l’aide d’un id Microsoft, vous devez [créer un id de travail de votre personnel une](virtual-machines-windows-create-aad-work-id.md).
 * Soit, en fonction de votre système d’exploitation de client-- [Azure PowerShell](../powershell-install-configure.md) ou l' [Infrastructure du langage commun pour Mac, Linux et Windows Azure](../xplat-cli-install.md).
 * [OpenSSL](https://www.openssl.org/). OpenSSL est utilisé pour générer les certificats requis.
 * Un client Git comme [Git Bash](https://git-scm.com/).
 * Pour tester l’exemple d’application, vous aurez également besoin d’un serveur DNS. Vous pouvez utiliser des serveurs DNS ou des services qui prennent en charge les génériques des enregistrements de.
 * Un ordinateur d’exécuter Deis outils clients. Vous pouvez utiliser un ordinateur local ou sur une machine virtuelle. Vous pouvez exécuter ces outils sur presque n’importe quelle distribution Linux, mais les instructions suivantes utilisent Ubuntu.

## <a name="provision-the-cluster"></a>Mettre en service le cluster

Dans cette section, vous allez utiliser un modèle [Azure le Gestionnaire de ressources](../azure-resource-manager/resource-group-overview.md) à partir des référentiels open source [azure-démarrage rapide-modèles](https://github.com/Azure/azure-quickstart-templates). Tout d’abord, vous allez copier le modèle vers le bas. Ensuite, vous allez créer une nouvelle paire de clés SSH pour l’authentification. Et ensuite, vous allez configurer un nouvel identificateur de cluster vous. Et enfin, vous utiliserez le script Shell ou le script PowerShell pour configurer le cluster.

1. Cloner le référentiel : [https://github.com/Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates).

        git clone https://github.com/Azure/azure-quickstart-templates

2. Allez dans le dossier de modèle :

        cd azure-quickstart-templates\deis-cluster-coreos

3. Créer une nouvelle paire de clés SSH à l’aide de ssh keygen :

        ssh-keygen -t rsa -b 4096 -c "[your_email@domain.com]"

4. Générer un certificat à l’aide de la clé privée :

        openssl req -x509 -days 365 -new -key [your private key file] -out [cert file to be generated]

5. Accédez à [https://discovery.etcd.io/new](https://discovery.etcd.io/new) pour générer un nouveau jeton de cluster, qui ressemble à quelque chose comme :

        https://discovery.etcd.io/6a28e078895c5ec737174db2419bb2f3
<br />
Chaque cluster CoreOS doit disposer d’un jeton unique à partir de ce service gratuit. Consultez la [documentation de CoreOS](https://coreos.com/docs/cluster-management/setup/cluster-discovery/) pour plus de détails.

6. Modifiez le fichier **config.yaml-nuage** pour remplacer le jeton de **découverte** existant avec le nouveau jeton :

        #cloud-config
        ---
        coreos:
          etcd:
            # generate a new token for each unique cluster from https://discovery.etcd.io/new
            # uncomment the following line and replace it with your discovery URL
            discovery: https://discovery.etcd.io/3973057f670770a7628f917d58c2208a
        ...

7. Modifiez le fichier **azuredeploy-parameters.json** : ouvrir le certificat que vous avez créé à l’étape 4 dans un éditeur de texte. Copier tout le texte entre `----BEGIN CERTIFICATE-----` et `-----END CERTIFICATE-----` dans le paramètre **sshKeyData** (vous devez supprimer tous les caractères de saut de ligne).

8. Modifiez le paramètre **newStorageAccountName** . Il s’agit du compte de stockage pour les disques du système d’exploitation de la machine virtuelle. Ce nom de compte doit être globalement unique.

9. Modifiez le paramètre **publicDomainName** . Celle-ci deviendra une partie du nom DNS associé à l’adresse IP publique d’équilibreur de charge. Le nom de domaine complet final aura le format de _[valeur de ce paramètre]_. _[Région]_. cloudapp.azure.com. Par exemple, si vous spécifiez le nom sous la forme deishbai32, et le groupe de ressources est déployé dans la région ouest, le nom de domaine complet finale à votre équilibreur de charge sera deishbai32.westus.cloudapp.azure.com.

10. Enregistrez le fichier de paramètres. Et puis, vous pouvez configurer le cluster à l’aide de PowerShell d’Azure :

        .\deploy-deis.ps1 -ResourceGroupName [resource group name] -ResourceGroupLocation "West US" -TemplateFile
        .\azuredeploy.json -ParametersFile .\azuredeploy-parameters.json -CloudInitFile .\cloud-config.yaml

  ou la CLI Azure :

        ./deploy-deis.sh -n "[resource group name]" -l "West US" -f ./azuredeploy.json -e ./azuredeploy-parameters.json
        -c ./cloud-config.yaml  

11. Une fois le groupe de ressources est mis en service, vous pouvez voir toutes les ressources dans le groupe sur Azure portal classique. Comme illustré dans la capture d’écran suivante, le groupe de ressources contient un réseau virtuel avec les trois ordinateurs virtuels, qui sont joints au même jeu de disponibilité. Ce groupe contient également un équilibreur de charge, ce qui a une IP publique associée.

  ![Le groupe de ressources de mise en service sur Azure portal classique](media/virtual-machines-linux-deis-cluster/resource-group.png)

## <a name="install-the-client"></a>Installer le client

Vous devez **deisctl** au contrôle de votre cluster de Deis. Bien que deisctl est automatiquement installé dans tous les nœuds du cluster, il est recommandé d’utiliser deisctl sur un ordinateur d’administration distinct. En outre, étant donné que tous les nœuds sont configurés avec uniquement les adresses IP privées, vous devez utiliser le protocole SSH tunneling par l’intermédiaire de l’équilibreur de charge, ce qui a une adresse IP publique, pour se connecter aux ordinateurs nœud. Voici les étapes de configuration de deisctl sur une autre Ubuntu physique ou une machine virtuelle.

1. Deisctl:mkdir d’installation deis

        cd deis
        curl -sSL http://deis.io/deisctl/install.sh | sh -s 1.6.1
        sudo ln -fs $PWD/deisctl /usr/local/bin/deisctl

2. Ajoutez votre clé privée ssh agent :

        eval `ssh-agent -s`
        ssh-add [path to the private key file, see step 1 in the previous section]

3. Configurer les deisctl :

        export DEISCTL_TUNNEL=[public ip of the load balancer]:2223

Le modèle définit des règles NAT entrantes qui mappent 2223 à instance 2225 à instance 3, 2224 à l’instance 2 et 1. Cela assure la redondance pour l’utilisation de l’outil deisctl. Vous pouvez examiner ces règles sur Azure portal classique :

![Règles NAT à l’équilibreur de charge](media/virtual-machines-linux-deis-cluster/nat-rules.png)

> [AZURE.NOTE] Actuellement le modèle prend uniquement en charge les clusters à 3 nœuds. C’est en raison d’une limitation dans le modèle de gestionnaire de ressources Azure définition des règles NAT, qui ne prend pas en charge la syntaxe de boucle.

## <a name="install-and-start-the-deis-platform"></a>Installez et démarrez le Deis de plate-forme

Vous pouvez désormais utiliser deisctl pour installer et démarrer la Deis la plate-forme :

    deisctl config platform set domain=[some domain]
    deisctl config platform set sshPrivateKey=[path to the private key file]
    deisctl install platform
    deisctl start platform

> [AZURE.NOTE] Démarrage de la plate-forme prend un certain temps (jusqu'à 10 minutes). En particulier, le démarrage du service du générateur peut prendre beaucoup de temps. Et parfois il faut quelques essaie de réussir : si l’opération semble se bloquer, essayez de taper `ctrl+c` pour arrêter l’exécution de la commande et réessayez.

Vous pouvez utiliser `deisctl list` pour vérifier si tous les services sont exécutés :

    deisctl list
    UNIT                            MACHINE                 LOAD    ACTIVE          SUB
    deis-builder.service            ebe3005e.../10.0.0.6    loaded  active          running
    deis-controller.service         ebe3005e.../10.0.0.6    loaded  active          running
    deis-database.service           9c79bbdd.../10.0.0.5    loaded  active          running
    deis-logger.service             9c79bbdd.../10.0.0.5    loaded  active          running
    deis-logspout.service           8d658d5a.../10.0.0.4    loaded  active          running
    deis-logspout.service           9c79bbdd.../10.0.0.5    loaded  active          running
    deis-logspout.service           ebe3005e.../10.0.0.6    loaded  active          running
    deis-publisher.service          8d658d5a.../10.0.0.4    loaded  active          running
    deis-publisher.service          9c79bbdd.../10.0.0.5    loaded  active          running
    deis-publisher.service          ebe3005e.../10.0.0.6    loaded  active          running
    deis-registry@1.service         ebe3005e.../10.0.0.6    loaded  active          running
    deis-router@1.service           ebe3005e.../10.0.0.6    loaded  active          running
    deis-router@2.service           8d658d5a.../10.0.0.4    loaded  active          running
    deis-router@3.service           9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-daemon.service       8d658d5a.../10.0.0.4    loaded  active          running
    deis-store-daemon.service       9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-daemon.service       ebe3005e.../10.0.0.6    loaded  active          running
    deis-store-gateway@1.service    9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-metadata.service     8d658d5a.../10.0.0.4    loaded  active          running
    deis-store-metadata.service     9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-metadata.service     ebe3005e.../10.0.0.6    loaded  active          running
    deis-store-monitor.service      8d658d5a.../10.0.0.4    loaded  active          running
    deis-store-monitor.service      9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-monitor.service      ebe3005e.../10.0.0.6    loaded  active          running
    deis-store-volume.service       8d658d5a.../10.0.0.4    loaded  active          running
    deis-store-volume.service       9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-volume.service       ebe3005e.../10.0.0.6    loaded  active          running

Félicitations ! Maintenant vous avez une exécution Deis clsuter sur Azure ! Ensuite, nous allons déployer un exemple application aller voir le cluster en action.

## <a name="deploy-and-scale-a-hello-world-application"></a>Déployer et mettre à l’échelle une application Hello World

Les étapes suivantes indiquent comment déployer un « Hello World » accédez d’application au cluster. Les étapes sont basées sur les [Deis la documentation](http://docs.deis.io/en/latest/using_deis/using-dockerfiles/#using-dockerfiles).

1. Pour le maillage de routage fonctionne correctement, vous devez disposer d’un enregistrement générique A pour votre domaine qui pointe vers l’adresse IP publique de l’équilibreur de charge. La capture d’écran suivante montre l’enregistrement A un exemple d’enregistrement des domaines sur GoDaddy :

    ![Enregistrement GoDaddy A](media/virtual-machines-linux-deis-cluster/go-daddy.png)
<p />
2. Deis de l’installation :

        mkdir deis
        cd deis
        curl -sSL http://deis.io/deis-cli/install.sh | sh
        ln -fs $PWD/deis /usr/local/bin/deis
        
3. Créer une nouvelle clé SSH et ajoutez la clé publique pour GitHub (bien entendu, vous pouvez également réutiliser vos clés existantes). Pour créer une nouvelle paire de clés SSH, utilisez :

        cd ~/.ssh
        ssh-keygen (press [Enter]s to use default file names and empty passcode)

4. Ajouter des id_rsa.pub, ou la clé publique de votre choix, à GitHub. Pour cela, en utilisant le bouton clé ajouter SSH dans l’écran de configuration clés SSH :

  ![Clé de Github](media/virtual-machines-linux-deis-cluster/github-key.png)
<p />
5. Inscrire un nouvel utilisateur :

        deis register http://deis.[your domain]
<p />
6. Ajoutez le code SSH :

        deis keys:add [path to your SSH public key]
  <p />      
7. Création d’une application.

        git clone https://github.com/deis/helloworld.git
        cd helloworld
        deis create
        git push deis master
<p />
8.L’installation par diffusion git déclenchera les images Docker pour être généré et déployé, qui prendront quelques minutes. D’après mon expérience, parfois, étape 10 (push image au référentiel privé) peut se bloquer. Dans ce cas, vous pouvez arrêter le processus, supprimer l’application à l’aide de ' deis applications : détruire – <application name> ` to remove the application and try again. You can use `deis apps:list' pour trouver le nom de votre application. Si tout fonctionne, vous devriez voir quelque chose comme ce qui suit à la fin de la sortie de la commande :

        -----> Launching...
               done, lambda-underdog:v2 deployed to Deis
               http://lambda-underdog.artitrack.com
               To learn more, use `deis help` or visit http://deis.io
        To ssh://git@deis.artitrack.com:2222/lambda-underdog.git
         * [new branch]      master -> master
<p />
9. Vérifiez si l’application fonctionne :

        curl -S http://[your application name].[your domain]
  Vous devez voir :

        Welcome to Deis!
        See the documentation at http://docs.deis.io/ for more information.
        (you can use geis apps:list to get the name of your application).
<p />
10. Échelle 3 instances de l’application :

        deis scale cmd=3
<p />
11. Si vous le souhaitez, vous pouvez utiliser deis info pour examiner les détails de votre application. Les sorties suivantes sont à partir de mon déploiement d’application :

        deis info
        === lambda-underdog Application
        {
          "updated": "2015-05-22T06:14:10UTC",
          "uuid": "10c74ee7-b7ff-4786-967a-7e65af7eabc3",
          "created": "2015-05-22T06:07:55UTC",
          "url": "lambda-underdog.artitrack.com",
          "owner": "haishi",
          "id": "lambda-underdog",
          "structure": {
            "cmd": 3
          }
        }

        === lambda-underdog Processes
        --- cmd:
        cmd.1 up (v2)
        cmd.2 up (v2)
        cmd.3 up (v2)

        === lambda-underdog Domains
        No domains

## <a name="next-steps"></a>Étapes suivantes

Cet article aborde toutes les étapes pour configurer un nouveau cluster sur Azure à l’aide d’un modèle de gestionnaire de ressources Azure de Deis. Le modèle prend en charge la redondance dans l’outillage de connexions, ainsi que pour les applications déployées d’équilibrage de charge. Le modèle permet également d’éviter à l’aide d’adresses IP publique sur des nœuds de membre, qui enregistre les précieuses ressources IP publics et offre un environnement plus sécurisé pour les applications hôtes. Pour plus d’informations, consultez les articles suivants :

[Vue d’ensemble de Gestionnaire des ressources Azure] [resource-group-overview]  
[L’utilisation de l’infrastructure du langage commun Azure] [azure-command-line-tools]  
[À l’aide de PowerShell Azure avec le Gestionnaire de ressources Azure] [powershell-azure-resource-manager]  

[azure-command-line-tools]: ../xplat-cli-install.md
[resource-group-overview]: ../azure-resource-manager/resource-group-overview.md
[powershell-azure-resource-manager]: ../powershell-azure-resource-manager.md
