<properties
    pageTitle="Gestion de l’état dans le Gestionnaire de ressources de modèles | Microsoft Azure"
    description="Affiche recommandé d’approches pour l’utilisation d’objets complexes de partager des données sur l’état avec le Gestionnaire de ressources Azure modèles et modèles liés"
    services="azure-resource-manager"
    documentationCenter=""
    authors="tfitzmac"
    manager="timlt"
    editor="tysonn"/>

<tags
    ms.service="azure-resource-manager"
    ms.workload="multiple"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/26/2016"
    ms.author="tomfitz"/>

# <a name="sharing-state-in-azure-resource-manager-templates"></a>Partage d’état dans les modèles du Gestionnaire de ressources Azure

Cette rubrique présente les méthodes conseillées pour la gestion et le partage d’état dans les modèles. Les paramètres et les variables présentées dans cette rubrique sont des exemples des types d’objets que vous pouvez définir à organisez facilement vos exigences de déploiement. À partir de ces exemples, vous pouvez implémenter vos propres objets avec les valeurs de propriété qui a un sens pour votre environnement.

Cette rubrique fait partie d’un livre blanc plus grand. Pour lire l’intégralité du, téléchargez le [monde classe ressource Gestionnaire de modèles et éprouvée méthodes conseillées](http://download.microsoft.com/download/8/E/1/8E1DBEFA-CECE-4DC9-A813-93520A5D7CFE/World Class ARM Templates - Considerations and Proven Practices.pdf).


## <a name="provide-standard-configuration-settings"></a>Fournir des paramètres de configuration standard

Au lieu de proposer un modèle qui fournit une flexibilité totale et nombreuses variations, un modèle commun est de fournir une sélection de configurations connues. En effet, les utilisateurs peuvent sélectionner des tailles standard t-shirt sandbox, petite, moyenne et grande. D’autres exemples de tailles de t-shirt sont des offres de produits, tels que la Communauté edition ou enterprise edition. Dans d’autres cas, il peut être spécifique à la charge des configurations de technologie – comme mappage de réduire ou pas sql.

Avec les objets complexes, vous pouvez créer des variables qui contiennent des collections de données, parfois appelées « conteneurs de propriétés » et les utiliser pour la déclaration de la ressource du lecteur dans votre modèle. Cette approche offre de bonnes configurations connues de différentes tailles qui sont préconfigurés pour les clients. Sans configurations connues, utilisateurs du modèle doivent déterminer le cluster de dimensionnement sur leurs propres, prendre en compte les contraintes de ressources de plate-forme et effectuer des opérations mathématiques pour identifier le partitionnement résultant des comptes de stockage et d’autres ressources (en raison de contraintes de taille et de la ressource de cluster). Outre une meilleure expérience pour le client, quelques configurations connues sont plus faciles à prendre en charge et peut vous aider à assurer un haut niveau de densité.

L’exemple suivant montre comment définir des variables qui contiennent des objets complexes pour la représentation des collections de données. Les collections de définissent les valeurs qui sont utilisées pour la taille de l’ordinateur virtuel, les paramètres réseau, les paramètres de système d’exploitation et les paramètres de disponibilité.

    "variables": {
      "tshirtSize": "[variables(concat('tshirtSize', parameters('tshirtSize')))]",
      "tshirtSizeSmall": {
        "vmSize": "Standard_A1",
        "diskSize": 1023,
        "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]",
        "vmCount": 2,
        "storage": {
          "name": "[parameters('storageAccountNamePrefix')]",
          "count": 1,
          "pool": "db",
          "map": [0,0],
          "jumpbox": 0
        }
      },
      "tshirtSizeMedium": {
        "vmSize": "Standard_A3",
        "diskSize": 1023,
        "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-8disk-resources.json')]",
        "vmCount": 2,
        "storage": {
          "name": "[parameters('storageAccountNamePrefix')]",
          "count": 2,
          "pool": "db",
          "map": [0,1],
          "jumpbox": 0
        }
      },
      "tshirtSizeLarge": {
        "vmSize": "Standard_A4",
        "diskSize": 1023,
        "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-16disk-resources.json')]",
        "vmCount": 3,
        "slaveCount": 2,
        "storage": {
          "name": "[parameters('storageAccountNamePrefix')]",
          "count": 2,
          "pool": "db",
          "map": [0,1,1],
          "jumpbox": 0
        }
      },
      "osSettings": {
        "scripts": [
          "[concat(variables('templateBaseUrl'), 'install_postgresql.sh')]",
          "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/shared_scripts/ubuntu/vm-disk-utils-0.1.sh"
        ],
        "imageReference": {
          "publisher": "Canonical",
          "offer": "UbuntuServer",
          "sku": "14.04.2-LTS",
          "version": "latest"
        }
      },
      "networkSettings": {
        "vnetName": "[parameters('virtualNetworkName')]",
        "addressPrefix": "10.0.0.0/16",
        "subnets": {
          "dmz": {
            "name": "dmz",
            "prefix": "10.0.0.0/24",
            "vnet": "[parameters('virtualNetworkName')]"
          },
          "data": {
            "name": "data",
            "prefix": "10.0.1.0/24",
            "vnet": "[parameters('virtualNetworkName')]"
          }
        }
      },
      "availabilitySetSettings": {
        "name": "pgsqlAvailabilitySet",
        "fdCount": 3,
        "udCount": 5
      }
    }

Notez que la variable **tshirtSize** concatène la taille de t-shirt que vous est fournie via un paramètre (**petit**, **moyen**, **grand**) pour le texte **tshirtSize**. Vous utilisez cette variable pour récupérer la variable objet complexe associé pour cette taille de t-shirt.

Vous pouvez ensuite référencer ces variables plus loin dans le modèle. La capacité de référencer des variables nommées et leurs propriétés simplifie la syntaxe du modèle et le rend facile à comprendre le contexte. L’exemple suivant définit une ressource à déployer en utilisant les objets décrite précédemment pour définir des valeurs. Par exemple, la taille de la mémoire virtuelle est définie en récupérant la valeur de `variables('tshirtSize').vmSize` tandis que la valeur de la taille du disque est extrait de `variables('tshirtSize').diskSize`. En outre, l’URI d’un modèle lié est défini avec la valeur de `variables('tshirtSize').vmTemplate`.

    "name": "master-node",
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2015-01-01",
    "dependsOn": [
        "[concat('Microsoft.Resources/deployments/', 'shared')]"
    ],
    "properties": {
        "mode": "Incremental",
        "templateLink": {
          "uri": "[variables('tshirtSize').vmTemplate]",
          "contentVersion": "1.0.0.0"
        },
        "parameters": {
          "adminPassword": {
            "value": "[parameters('adminPassword')]"
          },
          "replicatorPassword": {
            "value": "[parameters('replicatorPassword')]"
          },
          "osSettings": {
            "value": "[variables('osSettings')]"
          },
          "subnet": {
            "value": "[variables('networkSettings').subnets.data]"
          },
          "commonSettings": {
            "value": {
              "region": "[parameters('region')]",
              "adminUsername": "[parameters('adminUsername')]",
              "namespace": "ms"
            }
          },
          "storageSettings": {
            "value":"[variables('tshirtSize').storage]"
          },
          "machineSettings": {
            "value": {
              "vmSize": "[variables('tshirtSize').vmSize]",
              "diskSize": "[variables('tshirtSize').diskSize]",
              "vmCount": 1,
              "availabilitySet": "[variables('availabilitySetSettings').name]"
            }
          },
          "masterIpAddress": {
            "value": "0"
          },
          "dbType": {
            "value": "MASTER"
          }
        }
      }
    }

## <a name="pass-state-to-a-template"></a>Passer l’état à un modèle

Vous partagez l’état dans un modèle via les paramètres que vous fournissez directement pendant le déploiement.

Le tableau suivant répertorie les paramètres couramment utilisés dans les modèles.

Nom | Valeur | Description
---- | ----- | -----------
emplacement    | Chaîne à partir d’une liste limitée de régions Azure   | L’emplacement dans lequel les ressources sont déployés.
storageAccountNamePrefix    | Chaîne    | Nom DNS unique pour le compte de stockage où sont placés les disques de l’ordinateur
nom_domaine  | Chaîne    | Nom de domaine de la jumpbox accessible publiquement VM dans le format : **{nom du domaine}. { emplacement}.cloudapp.com** par exemple : **mydomainname.westus.cloudapp.azure.com**
adminUsername   | Chaîne    | Nom d’utilisateur pour les ordinateurs virtuels
adminPassword   | Chaîne    | Mot de passe pour les ordinateurs virtuels
tshirtSize  | Chaîne à partir d’une liste limitée d’offert les tailles de t-shirt   | La taille d’unité d’échelle nommés à la disposition. Par exemple, « Petite », « moyenne », « Large »
virtualNetworkName  | Chaîne    | Nom du réseau virtuel que le consommateur souhaite utiliser.
enableJumpbox   | Chaîne à partir d’une liste de contrainte (activé/désactivé) | Paramètre qui identifie s’il faut activer un jumpbox pour l’environnement. Valeurs : « activé », « désactivé »

Le paramètre **tshirtSize** utilisé dans la section précédente est défini comme :

    "parameters": {
      "tshirtSize": {
        "type": "string",
        "defaultValue": "Small",
        "allowedValues": [
          "Small",
          "Medium",
          "Large"
        ],
        "metadata": {
          "Description": "T-shirt size of the MongoDB deployment"
        }
      }
    }


## <a name="pass-state-to-linked-templates"></a>Passer l’état aux modèles liés

Lors de la connexion à des modèles liés, vous utilisez une combinaison de static et généré des variables.

### <a name="static-variables"></a>Variables statiques

Les variables statiques sont souvent utilisés pour fournir les valeurs de base, telles que les URL, qui sont utilisés dans un modèle.

Dans l’extrait de modèle suivant, `templateBaseUrl` Spécifie l’emplacement de la racine du modèle dans GitHub. La ligne suivante génère une nouvelle variable `sharedTemplateUrl` qui concatène l’URL de base avec le nom connu du modèle de ressources partagées. Au-dessous de cette ligne, une variable d’objet complexe est utilisée pour stocker une taille de t-shirt, où l’URL de base est concaténé à l’emplacement du modèle configuration connue et stocké dans le `vmTemplate` propriété.

L’avantage de cette approche est que si l’emplacement du modèle change, vous devez seulement modifier la variable statique dans un seul endroit, ce qui se passe dans tous les modèles liés.

    "variables": {
      "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
      "sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
      "tshirtSizeSmall": {
        "vmSize": "Standard_A1",
        "diskSize": 1023,
        "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]",
        "vmCount": 2,
        "slaveCount": 1,
        "storage": {
          "name": "[parameters('storageAccountNamePrefix')]",
          "count": 1,
          "pool": "db",
          "map": [0,0],
          "jumpbox": 0
        }
      }
    }

### <a name="generated-variables"></a>Variables générés

En plus des variables statiques, plusieurs variables sont générés de manière dynamique. Cette section présente certains des types courants de variables générés.

#### <a name="tshirtsize"></a>tshirtSize

Vous êtes familiarisé avec cette variable générée à partir de l’exemple ci-dessus.

#### <a name="networksettings"></a>networkSettings

Dans une capacité, une capacité ou modèle de solution de portée de bout en bout, les modèles liés créent généralement des ressources qui existent sur un réseau. Un simple consiste à utiliser un objet complexe pour stocker des paramètres de réseau et de les transmettre aux modèles liés.

Un exemple de paramètres de réseau de communication apparaissent ci-dessous.

    "networkSettings": {
      "vnetName": "[parameters('virtualNetworkName')]",
      "addressPrefix": "10.0.0.0/16",
      "subnets": {
        "dmz": {
          "name": "dmz",
          "prefix": "10.0.0.0/24",
          "vnet": "[parameters('virtualNetworkName')]"
        },
        "data": {
          "name": "data",
          "prefix": "10.0.1.0/24",
          "vnet": "[parameters('virtualNetworkName')]"
        }
      }
    }

#### <a name="availabilitysettings"></a>availabilitySettings

Les ressources créées dans les modèles liés sont souvent placés dans un jeu de disponibilité. Dans l’exemple suivant, le nom de jeu de disponibilité est spécifié, et également le domaine d’erreur et le domaine de la mise à jour de comptent pour utiliser.

    "availabilitySetSettings": {
      "name": "pgsqlAvailabilitySet",
      "fdCount": 3,
      "udCount": 5
    }

Si vous avez besoin de plusieurs ensembles de disponibilité (par exemple, une pour les nœuds maîtres) et une autre pour les nœuds de données, vous pouvez utiliser un nom sous la forme d’un préfixe, spécifiez plusieurs jeux de disponibilité ou suivre le modèle illustré plus haut pour la création d’une variable pour une taille de t-shirt spécifique.

#### <a name="storagesettings"></a>storageSettings

Détails de stockage sont souvent partagées avec les modèles liés. Dans l’exemple ci-dessous, un objet *storageSettings* fournit plus d’informations sur les noms de compte et de conteneur de stockage.

    "storageSettings": {
        "vhdStorageAccountName": "[parameters('storageAccountName')]",
        "vhdContainerName": "[variables('vmStorageAccountContainerName')]",
        "destinationVhdsContainer": "[concat('https://', parameters('storageAccountName'), variables('vmStorageAccountDomain'), '/', variables('vmStorageAccountContainerName'), '/')]"
    }

#### <a name="ossettings"></a>osSettings

Avec les modèles liés, vous devrez passer des paramètres de système d’exploitation à différents types de nœuds parmi les différents types de l’autre configuration connue. Un objet complexe est un moyen facile de stocker et de partager des informations de système d’exploitation et le rend plus facile à prendre en charge plusieurs choix de système d’exploitation pour le déploiement.

L’exemple suivant affiche un objet pour *osSettings*:

    "osSettings": {
      "imageReference": {
        "publisher": "Canonical",
        "offer": "UbuntuServer",
        "sku": "14.04.2-LTS",
        "version": "latest"
      }
    }

#### <a name="machinesettings"></a>machineSettings

Une variable générée, *machineSettings* est un objet complexe contenant des variables de base pour la création d’une machine virtuelle. Les variables comprennent le nom d’utilisateur administrateur et mot de passe, un préfixe pour les noms de machine virtuelle et une référence d’image de système d’exploitation.

    "machineSettings": {
        "adminUsername": "[parameters('adminUsername')]",
        "adminPassword": "[parameters('adminPassword')]",
        "machineNamePrefix": "mongodb-",
        "osImageReference": {
            "publisher": "[variables('osFamilySpec').imagePublisher]",
            "offer": "[variables('osFamilySpec').imageOffer]",
            "sku": "[variables('osFamilySpec').imageSKU]",
            "version": "latest"
        }
    },

Notez *osImageReference* récupère les valeurs de la variable *osSettings* définie dans le modèle principal. Cela signifie que vous pouvez facilement modifier le système d’exploitation d’un ordinateur virtuel, entièrement ou en fonction de la préférence d’un consommateur de modèle.

#### <a name="vmscripts"></a>vmScripts

L’objet *vmScripts* contient des détails sur les scripts de téléchargement et l’exécution sur une instance virtuelle, y compris à l’extérieur et à l’intérieur des références. En dehors des références incluent l’infrastructure. Intérieur références incluent le logiciel installé et la configuration.

La propriété *scriptsToDownload* vous permet de répertorier les scripts de téléchargement de la machine virtuelle. Cet objet contient également des références à des arguments de ligne de commande pour différents types d’actions. Ces actions incluent l’exécution de l’installation par défaut pour chaque nœud, une installation qui s’exécute après que tous les nœuds sont déployées et autres scripts qui peuvent être spécifiques à un modèle donné.

Cet exemple est un modèle utilisé pour déployer MongoDB, ce qui nécessite un arbitre garantir une haute disponibilité. *arbiterNodeInstallCommand* a été ajouté à *vmScripts* pour installer le serveur arbitre.

La section variables est où vous trouverez les variables qui définissent le texte spécifique pour exécuter le script avec les valeurs correctes.

    "vmScripts": {
        "scriptsToDownload": [
            "[concat(variables('scriptUrl'), 'mongodb-', variables('osFamilySpec').osName, '-install.sh')]",
            "[concat(variables('sharedScriptUrl'), 'vm-disk-utils-0.1.sh')]"
        ],
        "regularNodeInstallCommand": "[variables('installCommand')]",
        "lastNodeInstallCommand": "[concat(variables('installCommand'), ' -l')]",
        "arbiterNodeInstallCommand": "[concat(variables('installCommand'), ' -a')]"
    },


## <a name="return-state-from-a-template"></a>État de retour à partir d’un modèle

Non seulement vous passer des données dans un modèle, vous pouvez également partager des données dans le modèle d’appel. Dans la section **sort** d’un modèle lié, vous pouvez fournir des paires clé/valeur qui peuvent être consommées par le modèle source.

L’exemple suivant montre comment passer de l’adresse IP privée générée dans un modèle lié.

    "outputs": {
        "masterip": {
            "value": "[reference(concat(variables('nicName'),0)).ipConfigurations[0].properties.privateIPAddress]",
            "type": "string"
         }
    }

Dans le modèle principal, vous pouvez utiliser ces données avec la syntaxe suivante :

    "[reference('master-node').outputs.masterip.value]"

Vous pouvez utiliser cette expression dans la section des sorties ou la section de ressources du modèle principal. Vous ne pouvez pas utiliser l’expression dans la section variables, car elle dépend de l’état d’exécution. Pour renvoyer cette valeur à partir du modèle principal, utilisez :

    "outputs": { 
      "masterIpAddress": {
        "value": "[reference('master-node').outputs.masterip.value]",
        "type": "string"
      }
     
Pour obtenir un exemple d’utilisation de la section des sorties d’un modèle lié à retourner les disques de données pour une machine virtuelle, consultez [Création de plusieurs disques de données pour une Machine virtuelle](resource-group-create-multiple.md#creating-multiple-data-disks-for-a-virtual-machine).

## <a name="define-authentication-settings-for-virtual-machine"></a>Définir les paramètres d’authentification de l’ordinateur virtuel

Vous pouvez utiliser le même modèle illustré précédemment pour les paramètres de configuration pour spécifier les paramètres d’authentification pour une machine virtuelle. Vous créez un paramètre de passage dans le type d’authentification.

    "parameters": {
      "authenticationType": {
        "allowedValues": [
          "password",
          "sshPublicKey"
        ],
        "defaultValue": "password",
        "metadata": {
          "description": "Authentication type"
        },
        "type": "string"
      }
    }

Vous ajoutez des variables pour les différents types d’authentification, et une variable pour stocker le type est utilisée pour ce déploiement basé sur la valeur du paramètre.

    "variables": {
      "osProfile": "[variables(concat('osProfile', parameters('authenticationType')))]",
      "osProfilepassword": {
        "adminPassword": "[parameters('adminPassword')]",
        "adminUsername": "notused",
        "computerName": "[parameters('vmName')]",
        "customData": "[base64(variables('customData'))]"
      },
      "osProfilesshPublicKey": {
        "adminUsername": "notused",
        "computerName": "[parameters('vmName')]",
        "customData": "[base64(variables('customData'))]",
        "linuxConfiguration": {
          "disablePasswordAuthentication": "true",
          "ssh": {
            "publicKeys": [
              {
                "keyData": "[parameters('sshPublicKey')]",
                "path": "/home/notused/.ssh/authorized_keys"
              }
            ]
          }
        }
      }
    }

Lors de la définition de la machine virtuelle, vous définissez **osProfile** à la variable que vous avez créé.

    {
      "type": "Microsoft.Compute/virtualMachines",
      ...
      "osProfile": "[variables('osProfile')]"
    }


## <a name="next-steps"></a>Étapes suivantes
- Pour obtenir des informations sur les sections du modèle, consultez [Création de modèles de gestionnaire de ressources Azure](resource-group-authoring-templates.md)
- Pour afficher les fonctions qui sont disponibles dans un modèle, voir [Fonctions de modèle de gestionnaire de ressources Azure](resource-group-template-functions.md)

