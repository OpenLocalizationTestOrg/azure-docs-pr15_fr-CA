<properties
   pageTitle="Installer MongoDB sur un ordinateur virtuel de Linux | Microsoft Azure"
   description="Découvrez comment installer et configurer MongoDB sur une machine virtuelle de Linux dans Azure en utilisant le modèle de déploiement du Gestionnaire de ressources."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="09/29/2016"
   ms.author="iainfou"/>

# <a name="install-and-configure-mongodb-on-a-linux-vm-in-azure"></a>Installation et configuration de MongoDB sur un ordinateur virtuel Linux Azure
[MongoDB](http://www.mongodb.org) est une base de NoSQL d’open source, de hautes performances les plus courants. Cet article vous explique comment installer et configurer une VM Linux dans Azure MongoDB en utilisant le modèle de déploiement du Gestionnaire de ressources. Des exemples sont illustrés ce détail comment à :

- [Installer et configurer une instance de MongoDB base manuellement](#manually-install-and-configure-mongodb-on-a-vm)
- [Créez une instance de MongoDB base à l’aide d’un modèle de gestionnaire de ressources](#create-basic-mongodb-instance-on-centos-using-a-template)
- [Créer un cluster sharded avec des réplicas définit à l’aide d’un modèle de gestionnaire de ressources de MongoDB complexe](#create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template)


## <a name="prerequisites"></a>Conditions préalables
Cet article requiert la configuration suivante :

- un compte Azure ([Obtenez un essai gratuit](https://azure.microsoft.com/pricing/free-trial/)).
- la [CLI d’Azure](../xplat-cli-install.md) , connecté à l’aide`azure login`
- la CLI d’Azure *doit être* en mode de Gestionnaire des ressources Azure à l’aide`azure config mode arm`


## <a name="manually-install-and-configure-mongodb-on-a-vm"></a>Installez et configurez MongoDB sur une machine virtuelle manuellement
MongoDB [fournissent des instructions d’installation](https://docs.mongodb.com/manual/administration/install-on-linux/) pour distros de Linux, notamment Red Hat / CentOS, SUSE, Ubuntu et Debian. L’exemple suivant crée un `CoreOS` machine virtuelle à l’aide d’un code SSH stocké à `.ssh/azure_id_rsa.pub`. Répondez aux invites pour le nom du compte de stockage, nom DNS et les informations d’identification de l’administrateur :

```bash
azure vm quick-create --ssh-publickey-file .ssh/azure_id_rsa.pub --image-urn CentOS
```

Ouvrez une session sur l’ordinateur virtuel à l’aide de l’adresse IP publique à la fin de l’étape de création d’ordinateur virtuel précédent :

```bash
ssh ops@40.78.23.145
```

Pour ajouter les sources d’installation de MongoDB, créer un `yum` fichier de référentiel comme suit :

```bash
sudo touch /etc/yum.repos.d/mongodb-org-3.2.repo
```

Ouvrir le fichier mis en pension MongoDB pour le modifier. Ajoutez les lignes suivantes :

```bash
[mongodb-org-3.2]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/3.2/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-3.2.asc
```

Installation à l’aide de MongoDB `yum` comme suit :

```bash
sudo yum install -y mongodb-org
```

Par défaut, SELinux est appliquée sur les images CentOS qui vous empêche d’accéder à MongoDB. Installer les outils de gestion de stratégie et de configurer des SELinux permettant de MongoDB fonctionner sur le port TCP de sa valeur par défaut 27017 comme suit. 

```bash
sudo yum install -y policycoreutils-python
sudo semanage port -a -t mongod_port_t -p tcp 27017
```

Démarrez le service MongoDB comme suit :

```bash
sudo service mongod start
```

Vérifiez l’installation de MongoDB en vous connectant à l’aide de l’ordinateur local `mongo` client :

```bash
mongo
```

Maintenant tester l’instance MongoDB en ajoutant des données, puis recherche dans :

```
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```

Si vous le souhaitez, configurez MongoDB démarre automatiquement pendant un redémarrage du système :

```bash
sudo chkconfig mongod on
```


## <a name="create-basic-mongodb-instance-on-centos-using-a-template"></a>Créer une base instance de MongoDB sur CentOS à l’aide d’un modèle
Vous pouvez créer une instance de MongoDB base sur un ordinateur virtuel CentOS unique en utilisant le modèle d’Azure quickstart suivant à partir de Github. Ce modèle utilise l’extension de Script personnalisé pour Linux pour ajouter une `yum` référentiel aux VM de CentOS nouvellement créé et puis installer MongoDB.

- [Instance de base de MongoDB sur CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-on-centos) - https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json

L’exemple suivant crée un groupe de ressources portant le nom `myResourceGroup` dans les `WestUS` la région. Entrez vos propres valeurs comme suit :

```bash
azure group create --name myResourceGroup --location WestUS \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json
```

> [AZURE.NOTE] L’infrastructure du langage commun Azure vous renvoie à l’invite de quelques secondes de création du déploiement, mais la configuration et l’installation prend quelques minutes. Vérifiez l’état du déploiement avec `azure group deployment show myResourceGroup`, entrez le nom de votre groupe de ressources en conséquence. Attendez que le `ProvisioningState` indique 'Succeeded' avant de tenter de SSH à la machine virtuelle.

Une fois le déploiement terminé, SSH à la machine virtuelle. Obtenir l’adresse IP de votre machine virtuelle à l’aide de la `azure vm show` commande comme dans l’exemple suivant :

```bash
azure vm show --resource-group myResourceGroup --name myVM
```

À la fin de la sortie, le `Public IP address` s’affiche. SSH à votre machine virtuelle avec l’adresse IP de votre machine virtuelle :

```bash
ssh ops@138.91.149.74
```

Vérifiez l’installation de MongoDB en vous connectant à l’aide de l’ordinateur local `mongo` client comme suit :

```bash
mongo
```

Maintenant tester l’instance en ajoutant des données et de la recherche comme suit :

```
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```


## <a name="create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template"></a>Créer un Cluster de Sharded de MongoDB complexes sur CentOS à l’aide d’un modèle
Vous pouvez créer un cluster de sharded MongoDB complexe en utilisant le modèle d’Azure quickstart suivant à partir de Github. Ce modèle respecte les [meilleures pratiques de MongoDB cluster sharded](https://docs.mongodb.com/manual/core/sharded-cluster-components/) pour fournir une redondance et une disponibilité élevée. Le modèle crée deux milieu des fragments, avec trois nœuds dans chaque jeu de réplicas. Une configuration serveur jeu de réplicas avec trois nœuds est également créé, plus deux `mongos` rendent cohérentes au milieu des fragments pour applications à partir des serveurs de routeur.

- [Cluster ont MongoDB CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-sharding-centos) - https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json

> [AZURE.WARNING] Déploiement de ce cluster sharded MongoDB complexe requiert plus de 20 cœurs, qui est généralement le nombre de base par défaut par région pour un abonnement. Ouvrez une demande de support Azure pour augmenter votre nombre de base.

L’exemple suivant crée un groupe de ressources portant le nom `myResourceGroup` dans les `WestUS` la région. Entrez vos propres valeurs comme suit :

```bash
azure group create --name myResourceGroup --location WestUS \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json
```

> [AZURE.NOTE] L’infrastructure du langage commun Azure vous renvoie à l’invite de quelques secondes de création du déploiement, mais l’installation et la configuration peuvent prendre plus d’une heure. Vérifiez l’état du déploiement avec `azure group deployment show myResourceGroup`, le nom de votre groupe de ressources en ajustant en conséquence. Attendez que le `ProvisioningState` indique « Réussi » avant de se connecter aux ordinateurs virtuels.


## <a name="next-steps"></a>Étapes suivantes
Dans ces exemples, vous connectez à l’instance de MongoDB localement à partir de la machine virtuelle. Si vous souhaitez vous connecter à l’instance de MongoDB à partir d’une autre machine virtuelle ou un autre réseau, assurez-vous du approprié que [les règles du groupe de sécurité réseau sont créées](virtual-machines-linux-nsg-quickstart.md).

Pour plus d’informations sur la création à l’aide de modèles, consultez la [vue d’ensemble du Gestionnaire de ressources Azure](../azure-resource-manager/resource-group-overview.md).

Les modèles de gestionnaire de ressources Azure utilisent l’Extension de Script personnalisé à télécharger et d’exécuter des scripts sur vos ordinateurs virtuels. Pour plus d’informations, reportez-vous [à l’aide de l’Extension de Script personnalisé Azure avec Linux Virtual Machines](virtual-machines-linux-extensions-customscript.md).