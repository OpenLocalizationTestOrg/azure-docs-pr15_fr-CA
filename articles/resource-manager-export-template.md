<properties
    pageTitle="Exporter le modèle de gestionnaire de ressources Azure | Microsoft Azure"
    description="Permet de gérer des ressources d’Azure pour exporter un modèle à partir d’un groupe de ressources existant."
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
    ms.topic="get-started-article"
    ms.date="10/20/2016"
    ms.author="tomfitz"/>

# <a name="export-an-azure-resource-manager-template-from-existing-resources"></a>Exporter un modèle de gestionnaire de ressources Azure à partir des ressources existantes

Le Gestionnaire de ressources vous permet d’exporter un modèle de gestionnaire de ressources à partir de ressources existantes dans votre abonnement. Vous pouvez utiliser ce modèle généré pour en savoir plus sur la syntaxe du modèle ou pour automatiser le redéploiement de votre solution en fonction des besoins.

Il est important de noter qu’il existe deux méthodes différentes pour exporter un modèle :

- Vous pouvez exporter le modèle réel que vous avez utilisé pour le déploiement. Le modèle exporté comprend tous les paramètres et les variables exactement comme ils apparaissent dans le modèle d’origine. Cette approche est utile lorsque vous avez déployé des ressources via le portail. Maintenant, vous voulez voir comment construire le modèle pour créer ces ressources.
- Vous pouvez exporter un modèle qui représente l’état actuel du groupe de ressources. Le modèle exporté n’est pas basé sur un modèle que vous avez utilisé pour le déploiement. Au lieu de cela, il crée un modèle qui représente le groupe de ressources. Le modèle exporté a probablement pas autant de paramètres que vous définissez en général et de nombreuses valeurs codées en dur. Cette approche est utile lorsque vous avez modifié le groupe de ressources par le biais du portail ou de scripts. Maintenant, vous devez capturer le groupe de ressources en tant que modèle.

Cette rubrique illustre les deux approches.

Dans ce didacticiel, vous vous connectez au portail Azure, créer un compte de stockage et exportez le modèle de ce compte de stockage. Vous ajoutez un réseau virtuel pour modifier le groupe de ressources. Enfin, vous exportez un nouveau modèle qui représente son état actuel. Bien que cet article se concentre sur une infrastructure simplifiée, vous pouvez utiliser ces mêmes étapes pour exporter un modèle pour une solution plus complexe.

## <a name="create-a-storage-account"></a>Créer un compte de stockage

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez **Nouveau** > **stockage** > **compte de stockage**.

      ![créer le stockage](./media/resource-manager-export-template/create-storage.png)

2. Créer un compte de stockage avec le **stockage**du nom, vos initiales et la date. Le nom de compte de stockage doit être unique au sein d’Azure. Si le nom est déjà en cours d’utilisation, vous voyez un message d’erreur indiquant que le nom est en cours d’utilisation. Essayez une variante. Pour le groupe de ressources, créez un nouveau groupe de ressources et nommez-le **ExportGroup**. Vous pouvez utiliser les valeurs par défaut pour les autres propriétés. Sélectionnez **créer**.

      ![fournir des valeurs pour le stockage](./media/resource-manager-export-template/provide-storage-values.png)

Le déploiement peut prendre une minute. Une fois le déploiement terminé, votre abonnement contient le compte de stockage.

## <a name="view-a-template-from-deployment-history"></a>Afficher un modèle à partir de l’historique de déploiement

1. Accédez à la lame de groupe de ressource pour votre nouveau groupe de ressources. Remarquez que la lame indique le résultat du dernier déploiement. Cliquez sur ce lien.

      ![lame de groupe de ressources](./media/resource-manager-export-template/resource-group-blade.png)

2. Vous consultez l’historique des déploiements pour le groupe. Dans votre cas, la lame répertorie probablement un seul déploiement. Sélectionnez ce déploiement.

     ![dernier déploiement](./media/resource-manager-export-template/last-deployment.png)

3. La lame affiche un résumé du déploiement. Le résumé inclut l’état du déploiement et de ses opérations et les valeurs que vous avez fournies pour les paramètres. Pour afficher le modèle que vous avez utilisé pour le déploiement, sélectionnez le **modèle de la vue**.

     ![afficher le résumé de déploiement](./media/resource-manager-export-template/deployment-summary.png)

4. Le Gestionnaire de ressources récupère les six fichiers suivants pour vous :

   1. **Modèle** - le modèle qui définit l’infrastructure de votre solution. Lorsque vous avez créé le compte de stockage via le portail, Gestionnaire de ressources permet de déployer un modèle et enregistré ce modèle pour référence ultérieure.
   2. **Paramètres** - un fichier de paramètres que vous pouvez utiliser pour passer des valeurs au cours du déploiement. Il contient les valeurs que vous avez fournie lors du premier déploiement, mais vous pouvez modifier ces valeurs lorsque vous redéployez le modèle.
   3. **CLI** - Azure une interface de ligne de commande (CLI) fichier de script que vous pouvez utiliser pour déployer le modèle.
   4. **PowerShell** - fichier de script PowerShell d’Azure un que vous pouvez utiliser pour déployer le modèle.
   5. **.NET** - classe A .NET que vous pouvez utiliser pour déployer le modèle.
   6. **Ruby** - classe A Ruby que vous pouvez utiliser pour déployer le modèle.

     Les fichiers sont disponibles par le biais de liens sur la lame. Par défaut, le serveur lame affiche le modèle.

       ![modèle d’affichage](./media/resource-manager-export-template/view-template.png)

     Supposons une attention particulière au modèle. Votre modèle doit ressembler à :

        {« $schema » : « https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json# », « contentVersion » : « 1.0.0.0 », « paramètres » : {« nom » : {« type » : « Chaîne »}, « accountType » : {« type » : « Chaîne »}, « emplacement » : {« type » : « Chaîne »}, « encryptionEnabled » : {« defaultValue » : false, « type » : « Bool »}}, « ressources » : [{« type » : « Microsoft.Storage/storageAccounts », « référence » : {« nom » : « [parameters('accountType')] »}, « type » : « Storage », « nom » : « [parameters('name')] », « apiVersion » : « 2016-01-01 », « emplacement » : « [parameters('location')] », « propriétés » : {« chiffrement » : {« services » : {« blob » : {« activé » : « [parameters('encryptionEnabled')] »}}, « keySource » : « Microsoft.Storage »}}}]}
 
Ce modèle est le modèle réel utilisé pour créer votre compte de stockage. Notez qu’il contient des paramètres qui vous permettent de déployer différents types de comptes de stockage. Pour en savoir plus sur la structure d’un modèle, consultez [Création d’un gestionnaire de ressources Azure modèles](resource-group-authoring-templates.md). Pour obtenir la liste complète des fonctions que vous pouvez utiliser dans un modèle, voir [fonctions de modèle de gestionnaire de ressources Azure](resource-group-template-functions.md).


## <a name="add-a-virtual-network"></a>Ajouter un réseau virtuel

Le modèle que vous avez téléchargé dans la section précédente représenté l’infrastructure pour le déploiement d’origine. Toutefois, il ne sera pas en compte les modifications apportées après le déploiement.
Pour illustrer ce problème, nous allons modifier le groupe de ressources en ajoutant un réseau virtuel via le portail.

1. De la lame de groupe de ressources, sélectionnez **Ajouter**.

      ![ajouter des ressources](./media/resource-manager-export-template/add-resource.png)

2. Sélectionnez le **réseau virtuel** à partir des ressources disponibles.

      ![Sélectionnez le réseau virtuel](./media/resource-manager-export-template/select-vnet.png)

2. Nommez votre réseau virtuel **VNET**et utiliser les valeurs par défaut pour les autres propriétés. Sélectionnez **créer**.

      ![définir une alerte](./media/resource-manager-export-template/create-vnet.png)

3. Une fois le réseau virtuel a été déployé avec succès à votre groupe de ressources, examinez de nouveau l’historique de déploiement. Vous voyez maintenant les deux déploiements. Si vous ne voyez pas le deuxième déploiement, vous devrez peut-être fermer la blade de groupe ressources, le rouvrir. Sélectionnez le déploiement plus récent.

      ![historique de déploiement](./media/resource-manager-export-template/deployment-history.png)

4. Permet d’afficher le modèle pour ce déploiement. Notez qu’il définit uniquement le réseau virtuel. Il n’inclut pas le compte de stockage que vous avez déployée précédemment. Vous n’avez plus un modèle qui représente toutes les ressources dans votre groupe de ressources.

## <a name="export-the-template-from-resource-group"></a>Exporter le modèle de groupe de ressources

Pour obtenir l’état actuel de votre groupe de ressources, exporter un modèle qui affiche un instantané du groupe ressources.  

> [AZURE.NOTE] Vous ne pouvez pas exporter un modèle pour un groupe de ressources qui a plus de 200 ressources.

1. Pour afficher le modèle pour un groupe de ressources, sélectionnez **script d’Automation**.

      ![Exporter le groupe de ressources](./media/resource-manager-export-template/export-resource-group.png)

     Pas tous les types de ressources prennent en charge la fonction de modèle d’exportation. Si votre groupe de ressources contient uniquement le compte de stockage et le réseau virtuel indiqué dans cet article, vous ne voyez pas une erreur. Cependant, si vous avez créé d’autres types de ressources, vous pouvez voir une erreur indiquant qu’il existe un problème lié à l’exportation. Vous apprenez à gérer ces problèmes dans la section [problèmes d’exportation de correctif](#fix-export-issues) .

      

2. Vous consultez à nouveau les six fichiers que vous pouvez utiliser pour redéployer la solution, mais cette fois le modèle est un peu différent. Ce modèle a seulement deux paramètres : un pour le nom de compte de stockage et l’autre pour le nom du réseau virtuel.

        "parameters": {
          "virtualNetworks_VNET_name": {
            "defaultValue": "VNET",
            "type": "String"
          },
          "storageAccounts_storagetf05092016_name": {
            "defaultValue": "storagetf05092016",
            "type": "String"
          }
        },

     Le Gestionnaire de ressources n’a pas récupéré les modèles que vous avez utilisé au cours du déploiement. Au lieu de cela, il généré un nouveau modèle basé sur la configuration actuelle des ressources. Par exemple, le modèle définit la valeur d’emplacement et de la réplication du compte stockage :

        "location": "northeurope",
        "tags": {},
        "properties": {
            "accountType": "Standard_RAGRS"
        },

3. Vous disposez de deux options pour continuer à travailler avec ce modèle. Vous pouvez télécharger le modèle et travailler dessus localement avec un éditeur de JSON. Ou bien, vous pouvez enregistrer le modèle dans votre bibliothèque et de les utiliser via le portail.

     Si vous êtes à l’aise en utilisant un éditeur de JSON comme [Code de VS](resource-manager-vs-code.md) ou [Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md), vous pouvez télécharger le modèle localement et à l’aide de cet éditeur. Si vous n’êtes pas configuré avec un éditeur de JSON, vous pouvez modifier le modèle via le portail. Le reste de cette rubrique suppose que vous avez enregistré le modèle de votre bibliothèque dans le portail. Toutefois, vous apportez les mêmes modifications de syntaxe pour le modèle si le travail en local avec un éditeur de JSON ou via le portail.

     Pour travailler en local, sélectionnez **Télécharger**.

      ![Télécharger le modèle](./media/resource-manager-export-template/download-template.png)

     Pour travailler sur le portail, sélectionnez **Ajouter à la bibliothèque**.

      ![ajouter à la bibliothèque](./media/resource-manager-export-template/add-to-library.png)

     Lorsque vous ajoutez un modèle à la bibliothèque, donnez au modèle un nom et une description. Ensuite, cliquez sur **Enregistrer**.

     ![définir des valeurs de modèle](./media/resource-manager-export-template/set-template-values.png)

4. Pour afficher un modèle enregistré dans votre bibliothèque, sélectionnez **davantage de services**, tapez **modèles** pour filtrer les résultats, sélectionnez les **modèles**.

      ![Rechercher des modèles](./media/resource-manager-export-template/find-templates.png)

5. Sélectionnez le modèle avec le nom que vous avez enregistré.

      ![Sélectionnez le modèle](./media/resource-manager-export-template/select-library-template.png)

## <a name="customize-the-template"></a>Personnaliser le modèle

Le modèle exporté fonctionne correctement si vous souhaitez créer le même compte de stockage et le réseau virtuel pour chaque déploiement. Toutefois, le Gestionnaire de ressources propose des options vous pouvez déployer des modèles avec une plus grande flexibilité. Par exemple, pendant le déploiement, vous pouvez souhaiter spécifier le type de compte de stockage pour créer ou les valeurs à utiliser pour le préfixe d’adresse de réseau virtuel et un préfixe de sous-réseau.

Dans cette section, vous ajoutez des paramètres au modèle exporté afin que vous pouvez réutiliser le modèle lorsque vous déployez ces ressources à d’autres environnements. Vous ajoutez également certaines fonctionnalités à votre modèle pour réduire la probabilité de rencontrer une erreur lorsque vous déployez votre modèle. Vous n’avez plus à deviner un nom unique pour votre compte de stockage. Au lieu de cela, le modèle crée un nom unique. Vous limitez les valeurs qui peuvent être spécifiées pour le type de compte de stockage uniquement les options valides.

1. Sélectionnez **Modifier** pour personnaliser le modèle.

     ![afficher le modèle](./media/resource-manager-export-template/show-template.png)

1. Sélectionnez le modèle.

     ![modifier le modèle](./media/resource-manager-export-template/edit-template.png)

1. Pour être en mesure de passer les valeurs que vous pouvez spécifier au cours du déploiement, remplacez la section **paramètres** des définitions de paramètre. Notez les valeurs **allowedValues** pour **storageAccount_accountType**. Si vous avez fourni accidentellement une valeur non valide, cette erreur est reconnue avant le déploiement commence. Notez également que vous fournissez uniquement un préfixe pour le nom de compte de stockage, et le préfixe est limité à 11 caractères. Lorsque vous limitez le préfixe à 11 caractères, vous assurez que le nom complet ne dépasse pas le nombre maximal de caractères pour un compte de stockage. Le préfixe vous permet d’appliquer une convention d’affectation de noms pour vos comptes de stockage. Vous verrez comment créer un nom unique à l’étape suivante.

        "parameters": {
          "storageAccount_prefix": {
            "type": "string",
            "maxLength": 11
          },
          "storageAccount_accountType": {
            "defaultValue": "Standard_RAGRS",
            "type": "string",
            "allowedValues": [
              "Standard_LRS",
              "Standard_ZRS",
              "Standard_GRS",
              "Standard_RAGRS",
              "Premium_LRS"
            ]
          },
          "virtualNetwork_name": {
            "type": "string"
          },
          "addressPrefix": {
            "defaultValue": "10.0.0.0/16",
            "type": "string"
          },
          "subnetName": {
            "defaultValue": "subnet-1",
            "type": "string"
          },
          "subnetAddressPrefix": {
            "defaultValue": "10.0.0.0/24",
            "type": "string"
          }
        },

2. La section **variables** de votre modèle est actuellement vide. Dans la section **variables** , vous créez des valeurs qui simplifient la syntaxe pour le reste de votre modèle. Remplacer cette section par une nouvelle définition de variable. La variable **storageAccount_name** concatène le préfixe du paramètre à une chaîne unique qui est généré en fonction de l’identificateur du groupe de ressources. Vous n’avez plus à deviner un nom unique lors de la fourniture d’une valeur de paramètre.

        "variables": {
          "storageAccount_name": "[concat(parameters('storageAccount_prefix'), uniqueString(resourceGroup().id))]"
        },

3. Pour utiliser les paramètres et la variable dans les définitions de ressource, remplacez la section des **ressources** avec des définitions de ressource. Notez que peu a changé dans les définitions de ressource autre que la valeur qui est assignée à la propriété de la ressource. Les propriétés sont les mêmes que les propriétés à partir du modèle exporté. Vous affectez simplement des propriétés aux valeurs de paramètre au lieu des valeurs codées en dur. L’emplacement des ressources est défini pour utiliser le même emplacement que le groupe de ressources par le biais de l’expression **resourceGroup () .location** . La variable que vous avez créé pour le nom de compte de stockage est référencée par le biais de l’expression de **variables** .

        "resources": [
          {
            "type": "Microsoft.Network/virtualNetworks",
            "name": "[parameters('virtualNetwork_name')]",
            "apiVersion": "2015-06-15",
            "location": "[resourceGroup().location]",
            "properties": {
              "addressSpace": {
                "addressPrefixes": [
                  "[parameters('addressPrefix')]"
                ]
              },
              "subnets": [
                {
                  "name": "[parameters('subnetName')]",
                  "properties": {
                    "addressPrefix": "[parameters('subnetAddressPrefix')]"
                  }
                }
              ]
            },
            "dependsOn": []
          },
          {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageAccount_name')]",
            "apiVersion": "2015-06-15",
            "location": "[resourceGroup().location]",
            "tags": {},
            "properties": {
                "accountType": "[parameters('storageAccount_accountType')]"
            },
            "dependsOn": []
          }
        ]

4. Sélectionnez **OK** lorsque vous avez terminé la modification du modèle.

5. Sélectionnez **Enregistrer** pour enregistrer les modifications dans le modèle.

     ![enregistrer le modèle](./media/resource-manager-export-template/save-template.png)

6. Pour déployer le modèle mis à jour, sélectionnez **déployer**.

     ![déployer le modèle](./media/resource-manager-export-template/deploy-template.png)

7. Fournir des valeurs de paramètre et sélectionnez un nouveau groupe de ressources pour déployer les ressources.

## <a name="update-the-downloaded-parameters-file"></a>Mise à jour du fichier de paramètres téléchargé

Si vous travaillez avec les fichiers téléchargés (plutôt que la bibliothèque de portail), vous devez mettre à jour le fichier de paramètres téléchargé. Il ne correspond plus les paramètres de votre modèle. Vous n’avez pas à utiliser un fichier de paramètres, mais elle peut simplifier le processus lorsque vous redéployez un environnement. Vous utilisez les valeurs par défaut sont définis dans le modèle pour la plupart des paramètres afin que votre fichier de paramètres n’a besoin que deux valeurs.

Remplacez le contenu du fichier parameters.json :

```
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccount_prefix": {
      "value": "storage"
    },
    "virtualNetwork_name": {
      "value": "VNET"
    }
  }
}
```

Le fichier de paramètres de mise à jour fournit des valeurs uniquement pour les paramètres qui n’ont pas de valeur par défaut. Vous pouvez fournir des valeurs pour les autres paramètres lorsque vous voulez une valeur qui est différente de la valeur par défaut.

## <a name="fix-export-issues"></a>Résoudre les problèmes d’exportation

Pas tous les types de ressources prennent en charge la fonction de modèle d’exportation. En particulier, le Gestionnaire de ressources n’exporte pas certains types de ressources pour empêcher l’exposition des données sensibles. Par exemple, si vous avez une chaîne de connexion dans la configuration de votre site, probablement non souhaités qu'il affiché explicitement dans un modèle exporté. Vous pouvez contourner ce problème en ajoutant manuellement les ressources manquantes dans votre modèle.

> [AZURE.NOTE] Vous rencontrez uniquement les problèmes d’exportation lors de l’exportation à partir d’un groupe de ressources, et non à partir de l’historique de votre déploiement. Si votre dernier déploiement représente avec précision l’état actuel du groupe de ressources, vous devez exporter le modèle à partir de l’historique de déploiement et non à partir du groupe de ressources. Exporter à partir d’un groupe de ressources lorsque vous avez apporté des modifications au groupe de ressources qui ne sont pas définis dans un modèle unique.

Par exemple, si vous exportez un modèle pour un groupe de ressources qui contient une application web, de base de données SQL et chaîne de connexion dans la configuration du site, vous voyez le message suivant :

![Afficher erreur](./media/resource-manager-export-template/show-error.png)

Sélectionner le message vous indique exactement quel type de ressources n’ont pas été exportés. 
     
![Afficher erreur](./media/resource-manager-export-template/show-error-details.png)

Cette rubrique indique les correctifs courants.

### <a name="connection-string"></a>Chaîne de connexion

Dans la ressource de sites web, ajoutez une définition pour la chaîne de connexion à la base de données :

```
{
  "type": "Microsoft.Web/sites",
  ...
  "resources": [
    {
      "apiVersion": "2015-08-01",
      "type": "config",
      "name": "connectionstrings",
      "dependsOn": [
          "[concat('Microsoft.Web/Sites/', parameters('<site-name>'))]"
      ],
      "properties": {
          "DefaultConnection": {
            "value": "[concat('Data Source=tcp:', reference(concat('Microsoft.Sql/servers/', parameters('<database-server-name>'))).fullyQualifiedDomainName, ',1433;Initial Catalog=', parameters('<database-name>'), ';User Id=', parameters('<admin-login>'), '@', parameters('<database-server-name>'), ';Password=', parameters('<admin-password>'), ';')]",
              "type": "SQLServer"
          }
      }
    }
  ]
}
```    

### <a name="web-site-extension"></a>Extension du site Web

Dans la ressource de site web, ajoutez une définition pour le code d’installation :

```
{
  "type": "Microsoft.Web/sites",
  ...
  "resources": [
    {
      "name": "MSDeploy",
      "type": "extensions",
      "location": "[resourceGroup().location]",
      "apiVersion": "2015-08-01",
      "dependsOn": [
        "[concat('Microsoft.Web/sites/', parameters('<site-name>'))]"
      ],
      "properties": {
        "packageUri": "[concat(parameters('<artifacts-location>'), '/', parameters('<package-folder>'), '/', parameters('<package-file-name>'), parameters('<sas-token>'))]",
        "dbType": "None",
        "connectionString": "",
        "setParameters": {
          "IIS Web Application Name": "[parameters('<site-name>')]"
        }
      }
    }
  ]
}
```

### <a name="virtual-machine-extension"></a>Extension de la machine virtuelle

Pour obtenir des exemples d’extensions de la machine virtuelle, consultez [Exemples de Configuration Extension Azure Windows VM](./virtual-machines/virtual-machines-windows-extensions-configuration-samples.md).

### <a name="virtual-network-gateway"></a>Passerelle réseau virtuel

Ajouter un type de ressource de passerelle réseau virtuel.

```
{
  "type": "Microsoft.Network/virtualNetworkGateways",
  "name": "[parameters('<gateway-name>')]",
  "apiVersion": "2015-06-15",
  "location": "[resourceGroup().location]",
  "properties": {
    "gatewayType": "[parameters('<gateway-type>')]",
    "ipConfigurations": [
      {
        "name": "default",
        "properties": {
          "privateIPAllocationMethod": "Dynamic",
          "subnet": {
            "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets', parameters('<vnet-name>'), parameters('<new-subnet-name>'))]"
          },
          "publicIpAddress": {
            "id": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('<new-public-ip-address-Name>'))]"
          }
        }
      }
    ],
    "enableBgp": false,
    "vpnType": "[parameters('<vpn-type>')]"
  },
  "dependsOn": [
    "Microsoft.Network/virtualNetworks/codegroup4/subnets/GatewaySubnet",
    "[concat('Microsoft.Network/publicIPAddresses/', parameters('<new-public-ip-address-Name>'))]"
  ]
},
```

### <a name="local-network-gateway"></a>Passerelle du réseau local

Ajouter un type de ressource de passerelle de réseau local.

```
{
    "type": "Microsoft.Network/localNetworkGateways",
    "name": "[parameters('<local-network-gateway-name>')]",
    "apiVersion": "2015-06-15",
    "location": "[resourceGroup().location]",
    "properties": {
      "localNetworkAddressSpace": {
        "addressPrefixes": "[parameters('<address-prefixes>')]"
      }
    }
}
```

### <a name="connection"></a>Connexion

Ajouter un type de ressource de connexion.

```
{
    "apiVersion": "2015-06-15",
    "name": "[parameters('<connection-name>')]",
    "type": "Microsoft.Network/connections",
    "location": "[resourceGroup().location]",
    "properties": {
        "virtualNetworkGateway1": {
        "id": "[resourceId('Microsoft.Network/virtualNetworkGateways', parameters('<gateway-name>'))]"
      },
      "localNetworkGateway2": {
        "id": "[resourceId('Microsoft.Network/localNetworkGateways', parameters('<local-gateway-name>'))]"
      },
      "connectionType": "IPsec",
      "routingWeight": 10,
      "sharedKey": "[parameters('<shared-key>')]"
    }
},
```


## <a name="next-steps"></a>Étapes suivantes

Félicitations ! Vous avez appris comment exporter un modèle à partir des ressources que vous avez créée dans le portail.

- Vous pouvez déployer un modèle via [l’API REST](resource-group-template-deploy-rest.md), [Azure CLI](resource-group-template-deploy-cli.md)ou [PowerShell](resource-group-template-deploy.md).
- Pour savoir comment exporter un modèle via PowerShell, reportez-vous [à l’aide du PowerShell Azure avec le Gestionnaire de ressources Azure](powershell-azure-resource-manager.md).
- Pour savoir comment exporter un modèle via l’interface CLI d’Azure, voir [utilisation de la CLI Azure pour Mac, Linux et Windows avec le Gestionnaire de ressources Azure](xplat-cli-azure-resource-manager.md).
