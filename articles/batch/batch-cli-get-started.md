<properties
   pageTitle="Mise en route de la CLI de lot Azure | Microsoft Azure"
   description="Obtenir une brève introduction aux commandes de traitement par lots dans Azure CLI pour la gestion des ressources de service de lot d’Azure"
   services="batch"
   documentationCenter=""
   authors="mmacy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="batch"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="multiple"
   ms.workload="big-compute"
   ms.date="09/30/2016"
   ms.author="marsma"/>

# <a name="get-started-with-azure-batch-cli"></a>Mise en route de la CLI de lot Azure

L’Interface de ligne de commande Azure multiplates-formes (Azure CLI) permet de gérer vos comptes de lot et des ressources telles que les pools, les travaux et les tâches dans le shell de commande Linux, Mac et Windows. Avec la CLI d’Azure, vous pouvez effectuer et pour la plupart, les mêmes tâches à qu'effectuer avec les API de traitement par lots, Azure portal et applets de commande PowerShell de lot de script.

Cet article est basé sur Azure CLI version 0.10.5.

## <a name="prerequisites"></a>Conditions préalables

* [Installez l’interface CLI Azure](../xplat-cli-install.md)

* [La CLI Azure de se connecter à votre abonnement Azure](../xplat-cli-connect.md)

* Passer en **mode de gestionnaire de ressources**:`azure config mode arm`

>[AZURE.TIP] Il est recommandé que vous mettez à jour votre installation d’Azure CLI fréquemment afin de tirer parti des améliorations et des mises à jour de service.

## <a name="command-help"></a>Aide de la commande

Vous pouvez afficher le texte d’aide pour chaque commande dans Azure CLI en ajoutant `-h` comme une seule option après la commande. Par exemple :

* Pour obtenir de l’aide pour le `azure` de commande, entrez :`azure -h`
* Pour obtenir une liste de toutes les commandes de traitement par lots dans l’interface CLI, utilisez :`azure batch -h`
* Pour obtenir de l’aide sur la création d’un compte de traitement par lots, entrez :`azure batch account create -h`

En cas de doute, utilisez la `-h` une option de ligne de commande pour obtenir de l’aide sur n’importe quelle commande CLI d’Azure.

## <a name="create-a-batch-account"></a>Créer un compte de traitement par lots

Utilisation :

    azure batch account create [options] <name>

Exemple :

    azure batch account create --location "West US"  --resource-group "resgroup001" "batchaccount001"

Crée un nouveau compte de lot avec les paramètres spécifiés. Vous devez spécifier au moins un emplacement, un groupe de ressources et un nom de compte. Si vous ne disposez pas d’un groupe de ressources, créez-en un en exécutant `azure group create`et de spécifier l’une des régions Azure (par exemple, « ouest nous ») pour le `--location` option. Par exemple :

    azure group create --name "resgroup001" --location "West US"

> [AZURE.NOTE] Le nom de compte doit être unique au sein de la région Azure, que le compte est créé. Il peut contenir uniquement des caractères alphanumériques en minuscules et doit être 3 et 24 caractères. Vous ne pouvez pas utiliser de caractères spéciaux tels que `-` ou `_` dans les noms de compte de traitement par lots.

### <a name="linked-storage-account-autostorage"></a>Compte de stockage (autostorage)

Vous pouvez (facultativement) lier un compte de stockage **d’usage général** à votre compte de lot lors de sa création. La fonctionnalité [packages d’applications](batch-application-packages.md) de traitement par lots utilise le stockage blob dans un but général lié compte de stockage, comme le fait la bibliothèque [.NET de Conventions de fichier de traitement par lots](batch-task-output.md) . Fonctionnalités facultatives vous aideront à déployer les applications à que l’exécution de vos tâches de traitement par lots et conserver les données qu’elles produisent.

Pour lier un compte de stockage Azure existant vers un nouveau compte de lot lors de sa création, spécifiez la `--autostorage-account-id` option. Cette option requiert l’ID de ressource complet du compte de stockage.

Tout d’abord, afficher les détails de votre compte de stockage :

    azure storage account show --resource-group "resgroup001" "storageaccount001"

Puis utilisez la valeur de **l’Url** pour le `--autostorage-account-id` option. La valeur de l’Url commence par « abonnements / » et contient votre abonnement ID et la ressource chemin d’accès du compte de stockage :

    azure batch account create --location "West US"  --resource-group "resgroup001" --autostorage-account-id "/subscriptions/8ffffff8-4444-4444-bfbf-8ffffff84444/resourceGroups/resgroup001/providers/Microsoft.Storage/storageAccounts/storageaccount001" "batchaccount001"

## <a name="delete-a-batch-account"></a>Supprimer un compte de traitement par lots

Utilisation :

    azure batch account delete [options] <name>

Exemple :

    azure batch account delete --resource-group "resgroup001" "batchaccount001"

Supprime le compte de lot spécifié. Lorsque vous y êtes invité, confirmer la suppression du compte (la suppression de compte peut prendre du temps).

## <a name="manage-account-access-keys"></a>La gestion des clés d’accès de compte

Vous avez besoin d’une touche d’accès rapide pour [créer et modifier les ressources](#create-and-modify-batch-resources) dans votre compte de traitement par lots.

### <a name="list-access-keys"></a>Liste des touches d’accès

Utilisation :

    azure batch account keys list [options] <name>

Exemple :

    azure batch account keys list --resource-group "resgroup001" "batchaccount001"

Répertorie les touches de compte pour le compte de lot donné.

### <a name="generate-a-new-access-key"></a>Générer une nouvelle clé d’accès

Utilisation :

    azure batch account keys renew [options] --<primary|secondary> <name>

Exemple :

    azure batch account keys renew --resource-group "resgroup001" --primary "batchaccount001"

Régénère la clé du compte spécifié pour le compte de lot donné.

## <a name="create-and-modify-batch-resources"></a>Créer et modifier des ressources de traitement par lots

Vous pouvez utiliser la CLI Azure pour créer, lire, mettre à jour, supprimer (CRUD) lot des ressources comme les pools et calculer des nœuds, des opérations et des tâches. Ces opérations nécessitent votre nom de compte de traitement par lots, la touche d’accès rapide et le point de terminaison. Vous pouvez spécifier ces avec le `-a`, `-k`, et `-u` options, ou jeu de [variables d’environnement](#credential-environment-variables) qui l’infrastructure du langage commun utilise automatiquement (si rempli).

### <a name="credential-environment-variables"></a>Variables d’environnement d’informations d’identification

Vous pouvez définir `AZURE_BATCH_ACCOUNT`, `AZURE_BATCH_ACCESS_KEY`, et `AZURE_BATCH_ENDPOINT` variables d’environnement au lieu de spécifier `-a`, `-k`, et `-u` les options de la ligne de commande pour chaque commande que vous exécutez. Les commandes CLI utilise ces variables (si défini) afin que vous pouvez omettre le `-a`, `-k`, et `-u` options. Le reste de cet article suppose l’utilisation de ces variables d’environnement.

>[AZURE.TIP] Liste de vos clés avec `azure batch account keys list`et afficher le point de terminaison du compte avec `azure batch account show`.

### <a name="json-files"></a>Fichiers JSON

Lorsque vous créez des ressources de traitement par lots comme les pools et les tâches, vous pouvez spécifier un fichier JSON contenant la configuration de la nouvelle ressource au lieu de passer des options de la ligne de commande comme ses paramètres. Par exemple :

`azure batch pool create my_batch_pool.json`

Si vous pouvez effectuer de nombreuses opérations de création de ressources à l’aide des options de ligne de commande uniquement, certaines fonctionnalités requièrent un fichier au format JSON qui contient des informations sur les ressources. Par exemple, vous devez utiliser un fichier JSON si vous souhaitez spécifier des fichiers de ressource pour une tâche de début.

Pour trouver le JSON nécessaire à la création d’une ressource, reportez-vous à la [référence de l’API REST de lot] [ rest_api] documentation sur MSDN. Chaque rubrique « Ajouter le *type de ressource*» contient l’exemple JSON pour la création de la ressource, vous pouvez utiliser comme modèles pour vos fichiers JSON. Par exemple, vous pouvez trouver les JSON pour la création d’un pool dans [Ajouter un pool à un compte][rest_add_pool].

>[AZURE.NOTE] Si vous spécifiez un fichier JSON lorsque vous créez une ressource, tous les autres paramètres que vous spécifiez sur la ligne de commande pour cette ressource sont ignorés.

## <a name="create-a-pool"></a>Créer un pool

Utilisation :

    azure batch pool create [options] [json-file]

Exemple (Configuration de la Machine virtuelle) :

    azure batch pool create --id "pool001" --target-dedicated 1 --vm-size "STANDARD_A1" --image-publisher "Canonical" --image-offer "UbuntuServer" --image-sku "14.04.2-LTS" --node-agent-id "batch.node.ubuntu 14.04"

Exemple (Configuration des Services Cloud) :

    azure batch pool create --id "pool002" --target-dedicated 1 --vm-size "small" --os-family "4"

Crée un ensemble de nœuds de calcul dans le service de traitement par lots.

Comme mentionné dans la [Présentation des fonctionnalités de traitement par lots](batch-api-basics.md#pool), vous avez deux options lorsque vous sélectionnez un système d’exploitation pour les nœuds dans le pool : **Configuration de Machine virtuelle** et de la **Configuration des Services Cloud**. Utilisez le `--image-*` options pour créer des regroupements de Configuration de Machine virtuelle, et `--os-family` de créer des pools de Configuration des Services Cloud. Vous ne pouvez pas spécifier à la fois `--os-family` et `--image-*` options.

Vous pouvez spécifier le pool [des packages d’application](batch-application-packages.md) et la ligne de commande pour [Démarrer la tâche](batch-api-basics.md#start-task). Cependant, pour spécifier les fichiers de ressources pour cette tâche, vous devez utiliser à la place un [fichier JSON](#json-files).

Supprimer un pool avec :

    azure batch pool delete [pool-id]

>[AZURE.TIP] Vérifiez la [liste des images de machine virtuelle](batch-linux-nodes.md#list-of-virtual-machine-images) appropriée des valeurs pour le `--image-*` options.

## <a name="create-a-job"></a>Créer une tâche

Utilisation :

    azure batch job create [options] [json-file]

Exemple :

    azure batch job create --id "job001" --pool-id "pool001"

Ajoute une tâche pour le compte de traitement par lots et spécifie le pool sur lequel exécutent ses tâches.

Suppression d’un travail avec :

    azure batch job delete [job-id]

## <a name="list-pools-jobs-tasks-and-other-resources"></a>Liste les pools, les tâches, les tâches et les autres ressources

Chaque type de ressource de traitement par lots prend en charge une `list` commande qui interroge le compte de votre lot et répertorie les ressources de ce type. Par exemple, vous pouvez répertorier les pools dans votre compte et les tâches d’un projet :

    azure batch pool list
    azure batch task list --job-id "job001"

### <a name="listing-resources-efficiently"></a>Liste les ressources efficacement

Pour une recherche plus rapide, vous pouvez spécifier des options de clause de **Sélectionner**, de **filtre**et **développer** pour `list` opérations. Utilisez ces options pour limiter la quantité de données retournées par le service de traitement par lots. Puisque tout le filtrage s’effectue côté serveur, uniquement les données qui que vous intéressez traversent le réseau. Ces clauses permet d’économiser la bande passante (et par conséquent du temps) lorsque vous effectuez des opérations de la liste.

Par exemple, cela renvoie uniquement les pools dont les ID commencent par « renderTask » :

    azure batch task list --job-id "job001" --filter-clause "startswith(id, 'renderTask')"

Les commandes CLI prend en charge tous les trois clauses prises en charge par le service de traitement par lots :

* `--select-clause [select-clause]`Retourner un sous-ensemble de propriétés pour chaque entité.
* `--filter-clause [filter-clause]`Retourner uniquement les entités qui correspondent à l’expression OData spécifiée
* `--expand-clause [expand-clause]`Obtenir les informations de l’entité dans un seul appel reste sous-jacent. La clause de développement prend uniquement en charge les `stats` propriété à ce moment.

Pour plus d’informations sur les requêtes de liste performantes avec les trois clauses, reportez-vous à la section [efficace d’interroger le service de traitement par lots d’Azure](batch-efficient-list-queries.md).

## <a name="application-package-management"></a>Gestion de package d’application

Packages d’applications fournissent un moyen simple de déployer des applications sur les nœuds de calcul dans vos pools. Avec la CLI d’Azure, vous pouvez télécharger les packages d’applications, gérer les versions de lot et supprimer des packages.

Pour créer une nouvelle application et ajouter une version de package :

**Créer** une application :

    azure batch application create "resgroup001" "batchaccount001" "MyTaskApplication"

**Ajouter** un package d’application :

    azure batch application package create "resgroup001" "batchaccount001" "MyTaskApplication" "1.10-beta3" package001.zip

**Activer** le package :

    azure batch application package activate "resgroup001" "batchaccount001" "MyTaskApplication" "1.10-beta3" zip

Définir la **version par défaut** de l’application :

    azure batch application set "resgroup001" "batchaccount001" "MyTaskApplication" --default-version "1.10-beta3"

### <a name="deploy-an-application-package"></a>Déployer un package d’application

Vous pouvez spécifier un ou plusieurs packages d’application pour le déploiement lorsque vous créez un nouveau pool. Lorsque vous spécifiez un package au moment de la création de pool, il est déployé pour chaque nœud que le pool de jointures de nœud. Les packages sont également déployés lorsqu’un nœud est redémarré ou reconfiguration.

Spécifier la `--app-package-ref` option lors de la création d’un pool pour déployer un package d’application pour les nœuds de la grappe dès qu’ils rejoignent le pool. Le `--app-package-ref` option accepte une liste délimitée par des points-virgules d’ID d’application à déployer sur les nœuds de calcul.

    azure batch pool create --pool-id "pool001" --target-dedicated 1 --vm-size "small" --os-family "4" --app-package-ref "MyTaskApplication"

Lorsque vous créez un pool à l’aide des options de ligne de commande, vous ne pouvez actuellement spécifier *quelle* version du lot application à déployer sur les nœuds de calcul, par exemple « 1.10-version bêta 3 ». Par conséquent, vous devez d’abord spécifier une version par défaut de l’application avec `azure batch application set [options] --default-version <version-id>` avant de créer le pool (voir section précédente). Vous pouvez toutefois, spécifier une version du package pour le pool si vous utilisez un [fichier JSON](#json-files) au lieu des options de ligne de commande lors de la création du pool.

Vous trouverez plus d’informations sur les packages d’applications dans un [déploiement d’applications avec des packages d’application Azure lot](batch-application-packages.md).

>[AZURE.IMPORTANT] Vous devez [lier un compte de stockage Azure](#linked-storage-account-autostorage) à votre compte de traitement par lots à utiliser des packages d’application.

### <a name="update-a-pools-application-packages"></a>Mettre à jour les packages d’applications d’un pool

Pour mettre à jour les applications attribuées à un pool existant, vous devez émettre la `azure batch pool set` avec la `--app-package-ref` option :

    azure batch pool set --pool-id "pool001" --app-package-ref "MyTaskApplication2"

Pour déployer le nouveau package d’application pour les nœuds de calcul déjà dans un pool existant, vous devez redémarrer ou recréez ces nœuds :

    azure batch node reboot --pool-id "pool001" --node-id "tvm-3105992504_1-20160930t164509z"

>[AZURE.TIP] Vous pouvez obtenir une liste des nœuds dans un pool, ainsi que leur ID de noeud, avec `azure batch node list`.

Gardez à l’esprit que vous devez déjà avoir configuré l’application avec une version par défaut avant le déploiement (`azure batch application set [options] --default-version <version-id>`).

## <a name="troubleshooting-tips"></a>Conseils de dépannage

Cette section est destinée à vous fournir les ressources à utiliser lors de la résolution des problèmes de l’infrastructure du langage commun Azure. Il ne résoudra pas nécessairement tous les problèmes, mais cela peut vous aider à cerner la cause et vous indiquons des ressources d’aide.

* Utilisez `-h` pour obtenir le **texte d’aide** pour n’importe quelle commande CLI

* Utilisez `-v` et `-vv` pour afficher la sortie de commande **détaillée** ; `-vv` est documenté « extra » et affiche les autres requêtes et les réponses réelles. Ces commutateurs sont pratiques pour l’affichage de la sortie d’erreur complet.

* Vous pouvez afficher la **sortie de la commande en tant que JSON** avec le `--json` option. Par exemple, `azure batch pool show "pool001" --json` affiche les propriétés du pool001 au format JSON. Vous pouvez ensuite copier et modifier cette sortie à utiliser dans un `--json-file` (voir les [fichiers JSON](#json-files) plus haut dans cet article).

* Le [forum du lot sur le site MSDN] [ batch_forum] est une ressource très utile et étroitement contrôlé par les membres de l’équipe par lots. Assurez-vous de publier vos questions il si vous rencontrez des problèmes ou que vous souhaitez de l’aide sur une opération spécifique.

* Pas de chaque opération de ressources de traitement par lots est actuellement pris en charge par l’infrastructure du langage commun Azure. Par exemple, vous ne pouvez pas spécifier actuellement d’un package d’application *version* pour un pool, seul l’ID de package. Dans ce cas, vous devrez peut-être fournir un `--json-file` pour votre commande au lieu d’utiliser les options de ligne de commande. Veillez à rester à jour avec la dernière version de l’infrastructure du langage commun pour prélever des améliorations futures.

## <a name="next-steps"></a>Étapes suivantes

*  Consultez [déploiement d’applications avec des packages d’application Azure lot](batch-application-packages.md) pour savoir comment utiliser cette fonction pour gérer et déployer les applications que vous exécutez sur des nœuds de calcul de lot.

* Consultez [efficace d’interroger le service de traitement par lots](batch-efficient-list-queries.md) pour plus réduire le nombre d’éléments et le type d’informations retournées pour les requêtes de traitement par lots.

[batch_forum]: https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=azurebatch
[github_readme]: https://github.com/Azure/azure-xplat-cli/blob/dev/README.md
[rest_api]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx