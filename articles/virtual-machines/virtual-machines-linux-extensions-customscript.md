<properties
   pageTitle="Les scripts personnalisés sur des ordinateurs virtuels de Linux | Microsoft Azure"
   description="Automatiser les tâches de configuration Linux VM à l’aide de l’Extension de Script personnalisé"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="neilpeterson"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="09/22/2016"
   ms.author="nepeters"/>

# <a name="using-the-azure-custom-script-extension-with-linux-virtual-machines"></a>À l’aide de l’Extension Azure Script personnalisé avec des Machines virtuelles de Linux

L’Extension de Script personnalisé télécharge et exécute les scripts sur les machines virtuelles Azure. Cette extension est utile pour la configuration de déploiement la publication, l’installation du logiciel ou toute autre configuration / tâche de gestion. Scripts pouvant être téléchargés depuis le stockage Azure ou autre emplacement accessible sur internet, ou à l’extension de l’exécution. L’extension de Script personnalisé s’intègre aux modèles du Gestionnaire de ressources Azure et peut également être exécutée à l’aide de la CLI d’Azure, PowerShell, Azure portal ou l’API REST de Machine virtuelle Azure.

Ce document explique en détail comment utiliser l’Extension de Script personnalisé à partir de la CLI d’Azure et un modèle de gestionnaire de ressources Azure et décrit également les étapes de dépannage sur les systèmes Linux.

## <a name="extension-configuration"></a>Configuration de l’extension

La configuration de l’Extension de Script personnalisée spécifie des éléments tels que l’emplacement du script et la commande à exécuter. Cette configuration peut être stockée dans les fichiers de configuration, spécifié sur la ligne de commande ou dans un modèle de gestionnaire de ressources Azure. Les données sensibles peuvent être stockées dans une configuration protégée, qui est cryptée et déchiffrée uniquement à l’intérieur de l’ordinateur virtuel. La configuration protégée est utile lorsque la commande d’exécution comprend des secrets comme un mot de passe.

### <a name="public-configuration"></a>Configuration de publics

Schéma :

- **commandToExecute**: (au besoin, de la chaîne) le script de point d’entrée à exécuter
- **fileUris**: (tableau de chaînes facultatif,) les URL des fichiers à télécharger.
- **horodatage** (facultatif, integer) Utilisez ce champ uniquement pour déclencher la ré-exécution du script en modifiant la valeur de ce champ.

```none
{
  "fileUris": ["<url>"],
  "commandToExecute": "<command-to-execute>"
}
```

### <a name="protected-configuration"></a>Configuration protégée

Schéma :

- **commandToExecute**: (facultatif, chaîne) le script de point d’entrée à exécuter. Utilisez à la place de ce champ si votre commande contient des données confidentielles telles que des mots de passe.
- **storageAccountName**: (facultatif, chaîne) le nom du compte de stockage. Si vous spécifiez des informations d’identification de stockage, tous les fileUris doivent être des URL pour les BLOB Azure.
- **storageAccountKey**: (facultatif, chaîne) du compte de stockage de la clé d’accès.


```json
{
  "commandToExecute": "<command-to-execute>",
  "storageAccountName": "<storage-account-name>",
  "storageAccountKey": "<storage-account-key>"
}
```

## <a name="azure-cli"></a>CLI Azure

Lors de l’utilisation de l’infrastructure du langage commun Azure sera exécutée l’Extension de Script personnalisé, créez un fichier de configuration ou les fichiers contenant au minimum l’uri du fichier et la commande d’exécution de script.

```none
azure vm extension set <resource-group> <vm-name> CustomScript Microsoft.Azure.Extensions 2.0 --auto-upgrade-minor-version --public-config-path /scirpt-config.json
```

Le cas échéant, la commande peut être exécutée à l’aide de la `--public-config` et `--private-config` option, qui permet la configuration d’être spécifié lors de l’exécution et sans fichier de configuration distinct.

```none
azure vm extension set <resource-group> <vm-name> CustomScript Microsoft.Azure.Extensions 2.0 --auto-upgrade-minor-version --public-config '{"fileUris": ["https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"],"commandToExecute": "./hello.sh"}'
```

### <a name="azure-cli-examples"></a>Exemples de commandes CLI Azure

**Exemple 1** : configuration de Public avec un fichier de script.

```json
{
  "fileUris": ["https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"],
  "commandToExecute": "./hello.sh"
}
```

Commandes CLI Azure :

```none
azure vm extension set <resource-group> <vm-name> CustomScript Microsoft.Azure.Extensions 2.0 --auto-upgrade-minor-version --public-config-path /public.json
```

**Exemple 2** : configuration de publics sans fichier de script.

```json
{
  "commandToExecute": "apt-get -y update && apt-get install -y apache2"
}
```

Commandes CLI Azure :

```none
azure vm extension set <resource-group> <vm-name> CustomScript Microsoft.Azure.Extensions 2.0 --auto-upgrade-minor-version --public-config-path /public.json
```

**Exemple 3** : un fichier de configuration public est utilisé pour spécifier l’URI du fichier de script et un fichier de configuration protégée est utilisé pour spécifier la commande à exécuter.

Fichier de configuration public :

```json
{
  "fileUris": ["https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"],
}
```

Fichier de configuration protégée :  

```json
{
  "commandToExecute": "./hello.sh <password>"
}
```

Commandes CLI Azure :

```none
azure vm extension set <resource-group> <vm-name> CustomScript Microsoft.Azure.Extensions 2.0 --auto-upgrade-minor-version --public-config-path ./public.json --private-config-path ./protected.json
```

## <a name="resource-manager-template"></a>Modèle de gestionnaire de ressources

L’Extension de Script Azure personnalisé peut être exécutée au moment du déploiement Machine virtuelle à l’aide d’un modèle de gestionnaire de ressources. Pour ce faire, ajoutez JSON correctement mis en forme pour le modèle de déploiement.

### <a name="resource-manager-examples"></a>Exemples du Gestionnaire de ressources

**Exemple 1** : configuration de publique.

```json
{
    "name": "scriptextensiondemo",
    "type": "extensions",
    "location": "[resourceGroup().location]",
    "apiVersion": "2015-06-15",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', parameters('scriptextensiondemoName'))]"
    ],
    "tags": {
        "displayName": "scriptextensiondemo"
    },
    "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
      "settings": {
        "fileUris": [
          "https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"
        ],
        "commandToExecute": "sh hello.sh"
      }
    }
}
```

**Exemple 2** - commande d’exécution de la configuration protégée.

```json
{
  "name": "config-app",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh
      ]              
    },
    "protectedSettings": {
      "commandToExecute": "sh hello.sh <password>"
    }
  }
}
```

Consultez le .net démo du magasin principal musique pour obtenir un exemple complet - [Démonstration de magasin de musique](https://github.com/neilpeterson/nepeters-azure-templates/tree/master/dotnet-core-music-linux-vm-sql-db).

## <a name="troubleshooting"></a>Résolution des problèmes

Lors de l’exécution de l’Extension de Script personnalisé, il est créé ou téléchargé dans un répertoire similaire à l’exemple suivant. La sortie de commande est également enregistrée dans ce répertoire dans `stdout` et `stderr` fichier.

```none
/var/lib/azure/custom-script/download/0/
```

L’Extension de Script Azure génère un journal, qui se trouve ici.

```none
/var/log/azure/customscript/handler.log
```

L’état de l’exécution de l’Extension de Script personnalisé peut également être récupéré avec la CLI d’Azure.

```none
azure vm extension get <resource-group> <vm-name>
```

La sortie se présente comme le texte suivant :

```none
info:    Executing command vm extension get
+ Looking up the VM "scripttst001"
data:    Publisher                   Name                                      Version  State
data:    --------------------------  ----------------------------------------  -------  ---------
data:    Microsoft.Azure.Extensions  CustomScript                              2.0      Succeeded
data:    Microsoft.OSTCExtensions    Microsoft.Insights.VMDiagnosticsSettings  2.3      Succeeded
info:    vm extension get command OK
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les autres Extensions de Script de machine virtuelle, consultez [vue d’ensemble de l’Extension de Script Azure pour Linux](./virtual-machines-linux-extensions-features.md).