<properties
   pageTitle="Déployer un cluster Service de conteneur Azure | Microsoft Azure"
   description="Déployer un cluster Service de conteneur Azure à l’aide du portail Azure, Azure CLI ou PowerShell."
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Services-docker, conteneurs, Micro, Mesos, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/13/2016"
   ms.author="rogardle"/>

# <a name="deploy-an-azure-container-service-cluster"></a>Déployer un cluster Service de conteneur Azure

Azure conteneur Service fournit un déploiement rapide du conteneur d’open source les plus courants de solutions de clustering et d’orchestration. En utilisant le Service de conteneur d’Azure, vous pouvez déployer DC/système d’exploitation et les clusters Docker par essaim avec le Gestionnaire de ressources Azure modèles ou le portail Azure. Vous déployez ces clusters à l’aide de jeux d’échelle Azure Virtual Machine, et les clusters de tirer parti des solutions de réseau et de stockage Azure. Pour accéder aux services du conteneur Azure, vous avez besoin d’un abonnement Azure. Si vous n’en avez pas, puis vous pouvez vous inscrire pour un [essai gratuit](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935).

Ce document vous guide dans le processus de déploiement d’un cluster Service de conteneur Azure à l’aide du [portail Azure](#creating-a-service-using-the-azure-portal), l' [Azure interface de ligne de commande (CLI)](#creating-a-service-using-the-azure-cli)et le [module PowerShell de Azure](#creating-a-service-using-powershell).  

## <a name="create-a-service-by-using-the-azure-portal"></a>Création d’un service à l’aide du portail Azure

Connectez-vous au portail Azure, sélectionnez **Nouveau**et rechercher l’Azure Marketplace **Azure conteneur de Service**.

![Créer le déploiement 1](media/acs-portal1.png)  <br />

Sélectionnez le **Service de conteneur d’Azure**et cliquez sur **créer**.

![Créer le déploiement 2](media/acs-portal2.png)  <br />

Entrez les informations suivantes :

- **Nom d’utilisateur**: c’est le nom de l’utilisateur qui sera utilisé pour un compte sur chacun des ordinateurs virtuels et affecte à l’échelle de l’ordinateur virtuel dans le cluster Service de conteneur Azure.
- **Abonnement**: sélectionnez un abonnement Azure.
- **Groupe de ressources**: sélectionnez un groupe de ressources existant ou créez-en un nouveau.
- **Emplacement**: sélectionnez une région Azure pour le déploiement du Service de conteneur d’Azure.
- **Clé publique de SSH**: ajouter la clé publique qui sera utilisée pour l’authentification par rapport aux machines virtuelles de Service de conteneur d’Azure. Il est très important que cette clé ne contienne aucun saut de ligne, et qu’elle inclut le préfixe « ssh-rsa » et la 'username@domain' postfix. Il doit se présenter comme suit : **AAAAB3Nz de ssh rsa... <>...... UcyupgH azureuser@linuxvm **. Pour obtenir des instructions sur la création de clés de SSH (Secure Shell), consultez les articles de [Linux]( https://azure.microsoft.com/documentation/articles/virtual-machines-linux-ssh-from-linux/) et [Windows]( https://azure.microsoft.com/documentation/articles/virtual-machines-linux-ssh-from-windows/) .

Lorsque vous êtes prêt à continuer, cliquez sur **OK** .

![Créer le déploiement 3](media/acs-portal3.png)  <br />

Sélectionnez un type d’Orchestration. Les options sont les suivantes :

- **Contrôleur de domaine/OS**: déploie un cluster DC/système d’exploitation.
- **Par essaim**: déploie un cluster Docker par essaim.

Lorsque vous êtes prêt à continuer, cliquez sur **OK** .

![Créer le déploiement 4](media/acs-portal4.png)  <br />

Entrez les informations suivantes :

- **Masque de compte**: le nombre de formes de base dans le cluster.
- **Compteur de l’agent**: pour Docker par essaims particulaires, ce sera le nombre initial d’agents dans le jeu d’échelle de l’agent. Pour un contrôleur de domaine/OS, ce sera le nombre initial d’agents dans un jeu d’échelle privé. En outre, un ensemble d’échelle public est créé, qui contient un nombre prédéterminé d’agents. Le nombre d’agents dans ce jeu d’échelle public est déterminé par le nombre de formes de base ont été créés dans le cluster : un agent public pour un maître et les deux agents publics pour trois ou cinq formes de base.
- **Taille de machine virtuelle de l’agent**: la taille des ordinateurs virtuels agent.
- **Préfixe DNS**: un nom unique du monde qui sera utilisé pour préfixer les éléments clés des noms de domaine pleinement qualifié pour le service.

Lorsque vous êtes prêt à continuer, cliquez sur **OK** .

![Déploiement de 5](media/acs-portal5.png)  <br />

Après que validation du service terminée, cliquez sur **OK** .

![Créer le déploiement 6](media/acs-portal6.png)  <br />

Cliquez sur **créer** pour lancer le processus de déploiement.

![Déploiement de 7](media/acs-portal7.png)  <br />

Si vous avez choisi d’épingler le déploiement sur le portail Azure, vous pouvez voir l’état du déploiement.

![Créer le déploiement 8](media/acs-portal8.png)  <br />

Une fois le déploiement terminé, le cluster Azure conteneur Service est prêt à l’emploi.

## <a name="create-a-service-by-using-the-azure-cli"></a>Création d’un service à l’aide de la CLI d’Azure

Pour créer une instance d’Azure conteneur de Service à l’aide de la ligne de commande, vous avez besoin d’un abonnement Azure. Si vous n’en avez pas, puis vous pouvez vous inscrire pour un [essai gratuit](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935). Vous devez également avoir [installé](../xplat-cli-install.md) et [configuré](../xplat-cli-connect.md) le CLI Azure.

Pour déployer un contrôleur de domaine/système d’exploitation ou d’un cluster de Docker par essaims particulaires, sélectionnez un des modèles suivants à partir de GitHub. Notez que ces deux modèles sont identiques, à l’exception de la sélection d’orchestrator par défaut.

* [Modèle de contrôleur de domaine/OS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos)
* [Modèle de l’essaim](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)

Ensuite, assurez-vous que l’infrastructure du langage commun Azure a été connecté à un abonnement Azure. Pour cela, à l’aide de la commande suivante :

```bash
azure account show
```
Si un compte Azure n’est pas retourné, utilisez la commande suivante pour ouvrir une session CLI Azure.

```bash
azure login -u user@domain.com
```

Ensuite, configurez les outils Azure CLI pour utiliser le Gestionnaire de ressources Azure.

```bash
azure config mode arm
```

Créer un groupe de ressources Azure et le conteneur Service de cluster avec la commande suivante, où :

- **RESOURCE_GROUP** est le nom du groupe de ressources que vous souhaitez utiliser pour ce service.
- **Emplacement** est la région Azure où seront créés le groupe de ressources et d’un déploiement d’Azure conteneur Service.
- **TEMPLATE_URI** est l’emplacement du fichier de déploiement. Notez que cela doit être le fichier Raw, et non un pointeur vers le GitHub UI. Pour rechercher cette URL, sélectionnez le fichier azuredeploy.json dans GitHub, puis cliquez sur le bouton **premières** .

> [AZURE.NOTE] Lorsque vous exécutez cette commande, le shell vous demandera les valeurs de paramètre de déploiement.

```bash
azure group create -n RESOURCE_GROUP DEPLOYMENT_NAME -l LOCATION --template-uri TEMPLATE_URI
```

### <a name="provide-template-parameters"></a>Fournir des paramètres de modèle

Cette version de la commande nécessite que vous permet de définir les paramètres de manière interactive. Si vous souhaitez fournir des paramètres, comme une chaîne au format JSON, vous pouvez le faire à l’aide de la `-p` basculer. Par exemple :

 ```bash
azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI -p '{ "param1": "value1" … }'
```

Sinon, vous pouvez fournir un fichier au format JSON des paramètres à l’aide de la `-e` commutateur :

```bash
azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI -e PATH/FILE.JSON
```

Pour voir un exemple de fichier de paramètres nommé `azuredeploy.parameters.json`, recherchez-le avec les modèles de Service de conteneur Azure dans GitHub.

## <a name="create-a-service-by-using-powershell"></a>Création d’un service à l’aide de PowerShell

Vous pouvez également déployer un cluster Service de conteneur Azure avec PowerShell. Ce document est basé sur la version 1.0 du [module PowerShell de Azure](https://azure.microsoft.com/blog/azps-1-0/).

Pour déployer un contrôleur de domaine/système d’exploitation ou d’un cluster de Docker par essaims particulaires, sélectionnez un des modèles suivants. Notez que ces deux modèles sont identiques, à l’exception de la sélection d’orchestrator par défaut.

* [Modèle de contrôleur de domaine/OS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos)
* [Modèle de l’essaim](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)

Avant de créer un cluster dans votre abonnement Azure, vérifiez que votre session PowerShell a été connectée à Azure. Vous pouvez faire avec le `Get-AzureRMSubscription` commande :

```powershell
Get-AzureRmSubscription
```

Si vous devez vous connecter à Azure, utilisez le `Login-AzureRMAccount` commande :

```powershell
Login-AzureRmAccount
```

Si vous déployez sur un groupe de ressources, vous devez d’abord créer le groupe de ressources. Pour créer un nouveau groupe de ressources, utilisez les `New-AzureRmResourceGroup` de commande et spécifier une région ressource le nom et la destination du groupe :

```powershell
New-AzureRmResourceGroup -Name GROUP_NAME -Location REGION
```

Après avoir créé un groupe de ressources, vous pouvez créer votre cluster avec la commande suivante. L’URI du modèle souhaité doit être spécifié pour le `-TemplateUri` paramètre. Lorsque vous exécutez cette commande, PowerShell vous invite pour les valeurs de paramètre de déploiement.

```powershell
New-AzureRmResourceGroupDeployment -Name DEPLOYMENT_NAME -ResourceGroupName RESOURCE_GROUP_NAME -TemplateUri TEMPLATE_URI
```

### <a name="provide-template-parameters"></a>Fournir des paramètres de modèle

Si vous êtes familiarisé avec PowerShell, vous savez que vous pouvez passer en revue les paramètres disponibles pour une applet de commande en tapant un signe moins (-) et en appuyant sur la touche TAB. Cette même fonctionnalité fonctionne également avec les paramètres que vous définissez dans votre modèle. Dès que vous tapez le nom du modèle, l’applet de commande extrait le modèle, analyse les paramètres et ajoute dynamiquement les paramètres du modèle à la commande. Cela rend très facile de spécifier les valeurs de paramètre de modèle. Et bien, si vous oubliez une valeur de paramètre requis, PowerShell vous invite pour la valeur.

Vous trouverez ci-dessous la commande complète, avec des paramètres inclus. Vous pouvez fournir vos propres valeurs pour les noms des ressources.

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName RESOURCE_GROUP_NAME-TemplateURI TEMPLATE_URI -adminuser value1 -adminpassword value2 ....
```

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez un cluster opérationnel, consultez ces documents pour la connexion et les informations de gestion :

- [Se connecter à un cluster Service de conteneur Azure](container-service-connect.md)
- [Fonctionne avec le Service de conteneur Azure et DC/OS](container-service-mesos-marathon-rest.md)
- [Travailler avec le Service de conteneur Azure et Docker essaim](container-service-docker-swarm.md)
