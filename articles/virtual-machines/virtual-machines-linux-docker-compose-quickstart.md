<properties
   pageTitle="Docker et composition sur une machine virtuelle | Microsoft Azure"
   description="Brève introduction à l’utilisation de la composition et de Docker sur les machines virtuelles de Linux dans Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="09/22/2016"
   ms.author="iainfou"/>

# <a name="get-started-with-docker-and-compose-to-define-and-run-a-multi-container-application-on-an-azure-virtual-machine"></a>Mise en route de Docker et composition pour définir et exécuter une application conteneur multiples sur une machine virtuelle Azure

Mise en route à l’aide de Docker et [composition](http://github.com/docker/compose) pour définir et exécuter une application complexe sur une machine virtuelle de Linux dans Azure. Avec le nouveau message, un simple fichier texte vous permet de définir une application composée de plusieurs conteneurs de Docker. Vous faire pivoter votre application dans une seule commande qui effectue toutes les opérations pour déployer votre environnement défini. 

Par exemple, cet article vous indique comment configurer rapidement un blog WordPress avec un serveur principal de la base de données MariaDB SQL sur une VM Ubuntu. Vous pouvez également utiliser composer pour configurer des applications plus complexes.


## <a name="step-1-set-up-a-linux-vm-as-a-docker-host"></a>Étape 1 : Configurer une VM Linux comme un hôte de Docker

Vous pouvez utiliser les différentes procédures d’Azure et des images disponibles ou des modèles du Gestionnaire de ressources sur le marché d’Azure pour créer une VM Linux et le configurer comme hôte Docker. Par exemple, reportez-vous [à l’aide de l’Extension de la machine virtuelle Docker pour déployer votre environnement](virtual-machines-linux-dockerextension.md) pour créer rapidement une VM Ubuntu avec l’extension Azure VM de Docker, en utilisant un [modèle de démarrage rapide](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). 

Lorsque vous utilisez l’extension Docker VM, votre machine virtuelle est automatiquement défini comme un hôte de Docker et composition est déjà installée. L’exemple de cet article vous montre comment utiliser l' [Azure interface de ligne de commande pour Mac, Linux et Windows](../xplat-cli-install.md) (l’infrastructure CLI Azure) en mode de gestionnaire de ressources pour créer la machine virtuelle.

La commande de base à partir du document précédent crée un groupe de ressources appelé `myResourceGroup` dans les `West US` emplacement et déploie une machine virtuelle avec l’extension Azure Docker VM installée :

```bash
azure group create --name myResourceGroup --location "West US" \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

## <a name="step-2-verify-that-compose-is-installed"></a>Étape 2 : Vérifier que le message est installé

Une fois le déploiement terminé, SSH vers votre nouvel hôte Docker en utilisant le DNS nom vous avez fourni pendant le déploiement. Vous pouvez utiliser `azure vm show -g myDockerResourceGroup -n myDockerVM` pour afficher les détails de votre machine virtuelle, y compris le nom DNS.

Pour vérifier que le message est installé sur l’ordinateur virtuel, exécutez la commande suivante :

```bash
docker-compose --version
```

Vous consultez une sortie similaire à `docker-compose 1.6.2, build 4d72027`.

>[AZURE.TIP] Si vous avez utilisé une autre méthode pour créer un hôte de Docker et devez installer composer vous-même, consultez la [documentation de la composition](https://github.com/docker/compose/blob/882dc673ce84b0b29cd59b6815cb93f74a6c4134/docs/install.md).


## <a name="step-3-create-a-docker-composeyml-configuration-file"></a>Étape 3 : Créer un fichier de configuration de docker-compose.yml

Vous créez ensuite un `docker-compose.yml` fichier, qui est simplement un fichier texte de configuration, de définir les conteneurs Docker à exécuter sur l’ordinateur virtuel. Le fichier Spécifie l’image à exécuter sur chaque conteneur (ou d’une build à partir d’un Dockerfile), les liens entre les conteneurs et les dépendances, les ports et les variables d’environnement nécessaires. Pour plus d’informations sur la syntaxe du fichier yml, consultez [référence de fichier de composition](http://docs.docker.com/compose/yml/).

Créer le `docker-compose.yml` de fichiers comme suit :

```bash
touch docker-compose.yml
```

Utilisez votre éditeur de texte pour ajouter des données dans le fichier. L’exemple suivant utilise le `vi` éditeur :

```bash
vi docker-compose.yml
```

Collez l’exemple suivant dans votre fichier texte. Cette configuration utilise des images à partir du [Registre de DockerHub](https://registry.hub.docker.com/_/wordpress/) pour installer WordPress (open source blogs et contenu gestion système) et un back-end lié de la base de données MariaDB SQL. Entrez vos propres `MYSQL_ROOT_PASSWORD` comme suit :

```bash
wordpress:
  image: wordpress
  links:
    - db:mysql
  ports:
    - 80:80

db:
  image: mariadb
  environment:
    MYSQL_ROOT_PASSWORD: <your password>
```

## <a name="step-4-start-the-containers-with-compose"></a>Étape 4 : Démarrez les conteneurs avec la composition

Dans le même répertoire que votre `docker-compose.yml` fichier, exécutez la commande suivante (en fonction de votre environnement, vous devrez peut-être exécuter `docker-compose` à l’aide de `sudo`.) :

```bash
docker-compose up -d

```

Cette commande démarre les conteneurs Docker spécifiés dans `docker-compose.yml`. Il accepte une ou deux minutes pour terminer cette étape. Vous consultez une sortie similaire à l’exemple suivant :

```bash
Creating wordpress_db_1...
Creating wordpress_wordpress_1...
...
```

>[AZURE.NOTE] Veillez à utiliser l’option **d** de démarrage afin que les conteneurs de s’exécuter en continu en arrière-plan.

Pour vérifier que les conteneurs sont haut, tapez `docker-compose ps`. Vous devriez voir quelque chose comme :

```bash
Name             Command             State              Ports
-------------------------------------------------------------------------
wordpress_db_1     /docker-           Up                 3306/tcp
             entrypoint.sh
             mysqld
wordpress_wordpr   /entrypoint.sh     Up                 0.0.0.0:80->80
ess_1              apache2-for ...                       /tcp
```

Vous pouvez maintenant connecter à WordPress directement sur l’ordinateur virtuel sur le port 80. Ouvrez un navigateur web et entrez le nom DNS de votre ordinateur virtuel (tel que `http://myresourcegroup.westus.cloudapp.azure.com`). Vous devez maintenant voir le WordPress écran d’accueil, où vous pouvez terminer l’installation et mise en route de l’application.

![Écran de démarrage WordPress][wordpress_start]


## <a name="next-steps"></a>Étapes suivantes

* Consultez le [guide de l’utilisateur Docker VM extension](https://github.com/Azure/azure-docker-extension/blob/master/README.md) pour plus d’options configurer les Docker et composition de votre ordinateur virtuel de Docker. Par exemple, une option est à placer le fichier yml composer (converti en JSON) directement dans la configuration de l’extension de la machine virtuelle de Docker.
* Consultez le [guide de l’utilisateur](http://docs.docker.com/compose/) pour obtenir d’autres exemples de création et de déploiement des applications de conteneur plusieurs et de [référence de ligne de commande du nouveau message](http://docs.docker.com/compose/reference/) .
* Utiliser un modèle de gestionnaire de ressources Azure, soit votre propre ou un a contribué à partir de la [Communauté](https://azure.microsoft.com/documentation/templates/), à déployer un ordinateur virtuel d’Azure avec Docker et d’une application avec le nouveau message. Par exemple, le modèle de [déploiement d’un blog WordPress avec Docker](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-wordpress-mysql) utilise Docker et composition à déployer rapidement les WordPress avec un back-end de MySQL sur une VM Ubuntu.
* Essayez d’intégration Docker composer avec un cluster [Docker par essaim](virtual-machines-linux-docker-swarm.md) . Pour les scénarios, reportez-vous [à l’aide de composer avec l’essaim](https://docs.docker.com/compose/swarm/) .

<!--Image references-->

[wordpress_start]: ./media/virtual-machines-linux-docker-compose-quickstart/WordPress.png
