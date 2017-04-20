<properties
   pageTitle="Modèle de gestionnaire de ressource de Configuration d’état vous le souhaitez | Microsoft Azure"
   description="Définition du modèle de gestionnaire de ressources pour la Configuration de l’état souhaité dans Azure, exemples et dépannage"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="zjalexander"
   manager="timlt"
   editor=""
   tags="azure-service-management,azure-resource-manager"
   keywords=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="na"
   ms.date="09/15/2016"
   ms.author="zachal"/>

# <a name="windows-vmss-and-desired-state-configuration-with-azure-resource-manager-templates"></a>Windows VMSS et Configuration de l’état souhaité avec les modèles du Gestionnaire de ressources Azure
Cet article décrit le modèle de gestionnaire de ressources du [Gestionnaire d’extension de Configuration de l’état souhaité](virtual-machines-windows-extensions-dsc-overview.md). 

## <a name="template-example-for-a-windows-vm"></a>Exemple de modèle d’un ordinateur virtuel de Windows

L’extrait suivant est placé dans la section de ressources du modèle.

```json
            "name": "Microsoft.Powershell.DSC",
            "type": "extensions",
             "location": "[resourceGroup().location]",
             "apiVersion": "2015-06-15",
             "dependsOn": [
                  "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
              ],
              "properties": {
                  "publisher": "Microsoft.Powershell",
                  "type": "DSC",
                  "typeHandlerVersion": "2.20",
                  "autoUpgradeMinorVersion": true,
                  "forceUpdateTag": "[parameters('dscExtensionUpdateTagVersion')]",
                  "settings": {
                      "configuration": {
                          "url": "[concat(parameters('_artifactsLocation'), '/', variables('dscExtensionArchiveFolder'), '/', variables('dscExtensionArchiveFileName'))]",
                          "script": "dscExtension.ps1",
                          "function": "Main"
                      },
                      "configurationArguments": {
                          "nodeName": "[variables('vmName')]"
                      }
                  },
                  "protectedSettings": {
                      "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                  }

```

## <a name="template-example-for-windows-vmss"></a>Exemple de modèle pour Windows VMSS

Un nœud VMSS a une section « Propriétés » avec le « VirtualMachineProfile », « extensionProfile » attribut. DSC est ajoutée sous « extensions ». 

```json
"extensionProfile": {
            "extensions": [
                {
                    "name": "Microsoft.Powershell.DSC",
                    "properties": {
                        "publisher": "Microsoft.Powershell",
                        "type": "DSC",
                        "typeHandlerVersion": "2.20",
                        "autoUpgradeMinorVersion": true,
                        "forceUpdateTag": "[parameters('DscExtensionUpdateTagVersion')]",
                        "settings": {
                            "configuration": {
                                "url": "[concat(parameters('_artifactsLocation'), '/', variables('DscExtensionArchiveFolder'), '/', variables('DscExtensionArchiveFileName'))]",
                                "script": "DscExtension.ps1",
                                "function": "Main"
                            },
                            "configurationArguments": {
                                "nodeName": "localhost"
                            }
                        },
                        "protectedSettings": {
                            "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                        }
                    }
                }
            ]
```

## <a name="detailed-settings-information"></a>Informations de paramètres détaillés

Le schéma suivant est pour la partie paramètres de l’extension DSC d’Azure dans un modèle de gestionnaire de ressources Azure.

```json

"settings": {
  "wmfVersion": "latest",
  "configuration": {
    "url": "http://validURLToConfigLocation",
    "script": "ConfigurationScript.ps1",
    "function": "ConfigurationFunction"
  },
  "configurationArguments": {
    "argument1": "Value1",
    "argument2": "Value2"
  },
  "configurationData": {
    "url": "https://foo.psd1"
  },
  "privacy": {
    "dataCollection": "enable"
  },
  "advancedOptions": {
    "downloadMappings": {
      "customWmfLocation": "http://myWMFlocation"
    }
  } 
},
"protectedSettings": {
  "configurationArguments": {
    "parameterOfTypePSCredential1": {
      "userName": "UsernameValue1",
      "password": "PasswordValue1"
    },
    "parameterOfTypePSCredential2": {
      "userName": "UsernameValue2",
      "password": "PasswordValue2"
    }
  },
  "configurationUrlSasToken": "?g!bber1sht0k3n",
  "configurationDataUrlSasToken": "?dataAcC355T0k3N"
}

```

## <a name="details"></a>Détails
| Nom de la propriété | Type de | Description |
| --- | --- | --- |
| settings.wmfVersion | chaîne | Spécifie la version de la de Windows Management Framework doit être installé sur votre machine virtuelle. La définition de cette propriété à 'dernier' installe la version la plus à jour de WMF. Les valeurs possibles uniquement en cours pour cette propriété sont **'4.0', '5.0', ' 5.0PP' et 'plus tard'**. Les valeurs possibles sont soumis à des mises à jour. La valeur par défaut est « plus récente ».|
| Settings.Configuration.URL | chaîne | Spécifie l’emplacement de l’URL à partir duquel télécharger le fichier zip de configuration DSC. Si l’URL fournie requiert un jeton d’associations de sécurité pour l’accès, vous devez définir la propriété protectedSettings.configurationUrlSasToken à la valeur de votre jeton SAS. Cette propriété est requise si settings.configuration.script ou settings.configuration.function sont définis. |
| Settings.Configuration.script | chaîne | Spécifie le nom du fichier de script qui contient la définition de votre configuration de DSC. Ce script doit être dans le dossier racine du fichier zip téléchargé à partir de l’URL spécifiée par la propriété configuration.url. Cette propriété est requise si settings.configuration.url ou settings.configuration.script sont définis. |
| Settings.Configuration.Function | chaîne | Spécifie le nom de votre configuration de DSC. La nom de la configuration doit être contenue dans le script défini par configuration.script. Cette propriété est requise si settings.configuration.url ou settings.configuration.function sont définis. |
| settings.configurationArguments | Collection | Définit les paramètres que vous souhaitez passer votre configuration DSC. Cette propriété n’est pas chiffrée. |
| settings.configurationData.url | chaîne | Spécifie l’URL à partir duquel télécharger votre fichier de configuration de données (.pds1) à utiliser comme entrée pour votre configuration de DSC. Si l’URL fournie requiert un jeton d’associations de sécurité pour l’accès, vous devez définir la propriété protectedSettings.configurationDataUrlSasToken à la valeur de votre jeton SAS.|
| settings.privacy.dataEnabled | chaîne | Active ou désactive la collecte de télémétrie. Les seules valeurs possibles pour cette propriété sont **'Activer', 'Désactiver', '', ou $null**. Laisser cette propriété vide ou null permet de télémétrie. La valeur par défaut est ". [Plus d’informations](https://blogs.msdn.microsoft.com/powershell/2016/02/02/azure-dsc-extension-data-collection-2/) |
| settings.advancedOptions.downloadMappings | Collection | Définit des emplacements alternatifs à partir duquel télécharger le WMF. [Plus d’informations](http://blogs.msdn.com/b/powershell/archive/2015/10/21/azure-dsc-extension-2-2-amp-how-to-map-downloads-of-the-extension-dependencies-to-your-own-location.aspx) |
| protectedSettings.configurationArguments | Collection | Définit les paramètres que vous souhaitez passer votre configuration DSC. Cette propriété est cryptée. |
| protectedSettings.configurationUrlSasToken | chaîne | Spécifie le jeton SAS pour accéder à l’URL définie par configuration.url. Cette propriété est cryptée. |
| protectedSettings.configurationDataUrlSasToken | chaîne | Spécifie le jeton SAS pour accéder à l’URL définie par configurationData.url. Cette propriété est cryptée. |

## <a name="settings-vs-protectedsettings"></a>Paramètres et ProtectedSettings
Tous les paramètres sont enregistrés dans un fichier texte de paramètres sur l’ordinateur virtuel.
Sous « paramètres », les propriétés sont des propriétés publiques, car ils ne sont pas cryptés dans le fichier texte de paramètres.
Propriétés sous 'protectedSettings' sont cryptées avec un certificat et ne sont pas affichées en texte brut dans ce fichier sur l’ordinateur virtuel.

Si la configuration a besoin des informations d’identification, ils peuvent être inclus dans le protectedSettings :

```json
"protectedSettings": {
    "configurationArguments": {
        "parameterOfTypePSCredential1": {
            "userName": "UsernameValue1",
            "password": "PasswordValue1"
        }
    }
}
```

## <a name="example"></a>Exemple

L’exemple suivant dérive à partir de la section « Mise en route » de la [page de vue d’ensemble du Gestionnaire d’Extension DSC](virtual-machines-windows-extensions-dsc-overview.md).
Cet exemple utilise les modèles du Gestionnaire de ressources au lieu des applets de commande pour déployer l’extension. Enregistrer la configuration de « IisInstall.ps1 », le placer dans un. ZIP et téléchargez le fichier dans une URL accessible. Cet exemple utilise le stockage blob Azure, mais il est possible de télécharger. Fichiers ZIP à partir de n’importe quel emplacement arbitraire.

Dans le modèle de gestionnaire de ressources d’Azure, le code suivant demande à la machine virtuelle pour télécharger le fichier correct et exécuter la fonction appropriée de PowerShell :

```json
"settings": {
    "configuration": {
        "url": "https://demo.blob.core.windows.net/",
        "script": "IisInstall.ps1",
        "function": "IISInstall"
    }
    } 
},
"protectedSettings": {
    "configurationUrlSasToken": "odLPL/U1p9lvcnp..."
}
```

## <a name="updating-from-the-previous-format"></a>Mise à jour à partir de l’ancien Format
Tous les paramètres dans l’ancien format (contenant les propriétés publiques, ModulesUrl, ConfigurationFunction, SasToken ou propriétés) automatiquement s’adapter au format actuel et exécutent tout comme ils le faisaient avant.

Le schéma suivant est du précédent paramètres schéma ressemblait à :

```json
"settings": {
    "WMFVersion": "latest",
    "ModulesUrl": "https://UrlToZipContainingConfigurationScript.ps1.zip",
    "SasToken": "SAS Token if ModulesUrl points to private Azure Blob Storage",
    "ConfigurationFunction": "ConfigurationScript.ps1\\ConfigurationFunction",
    "Properties":  {
        "ParameterToConfigurationFunction1":  "Value1",
        "ParameterToConfigurationFunction2":  "Value2",
        "ParameterOfTypePSCredential1": {
            "UserName": "UsernameValue1",
            "Password": "PrivateSettingsRef:Key1" 
        },
        "ParameterOfTypePSCredential2": {
            "UserName": "UsernameValue2",
            "Password": "PrivateSettingsRef:Key2"
        }
    }
},
"protectedSettings": { 
    "Items": {
        "Key1": "PasswordValue1",
        "Key2": "PasswordValue2"
    },
    "DataBlobUri": "https://UrlToConfigurationDataWithOptionalSasToken.psd1"
}
```

Voici comment le format précédent s’adapte au format actuel :

| Nom de la propriété | Équivalent de schéma précédent |
| --- | --- |
| settings.wmfVersion | Paramètres. WMFVersion |
| Settings.Configuration.URL | Paramètres. ModulesUrl |
| Settings.Configuration.script | Première partie de paramètres. ConfigurationFunction (avant '\\\\») |
| Settings.Configuration.Function | Deuxième partie des paramètres. ConfigurationFunction (après '\\\\») |
| settings.configurationArguments | Paramètres. Propriétés |
| settings.configurationData.url | protectedSettings.DataBlobUri (sans jeton SAS) |
| settings.privacy.dataEnabled | Paramètres. Privacy.DataEnabled |
| settings.advancedOptions.downloadMappings | Paramètres. AdvancedOptions.DownloadMappings |
| protectedSettings.configurationArguments | protectedSettings.Properties |
| protectedSettings.configurationUrlSasToken | Paramètres. SasToken |
| protectedSettings.configurationDataUrlSasToken | Jeton SAS à partir de protectedSettings.DataBlobUri |


## <a name="troubleshooting---error-code-1100"></a>Dépannage - Code d’erreur 1100
Code d’erreur 1100 indique qu’il existe un problème avec l’entrée d’utilisateur à l’extension de DSC.
Le texte de ces erreurs est variable et peut changer.
Voici les erreurs que vous pouvez rencontrer et comment vous pouvez les résoudre.

### <a name="invalid-values"></a>Valeurs non valides
« Le Privacy.dataCollection est '{0}'. Les seules valeurs possibles sont «, 'Activer' et 'Désactiver' » « est de WmfVersion '{0}'. Seules les valeurs possibles sont... et «' »

Problème : La valeur fournie n’est pas autorisée.

Solution : Définissez la valeur non valide à une valeur valide. Consultez le tableau dans la section Détails.

### <a name="invalid-url"></a>URL non valide
« Le ConfigurationData.url est '{0}'. Ce n’est pas une URL valide » « est de DataBlobUri '{0}'. Ce n’est pas une URL valide » « est de Configuration.url '{0}'. Ce n’est pas une URL valide »

Problème : A fourni que l’URL n’est pas valide.

Solution : Vérifiez tous vos URL fournie. Assurez-vous que toutes les URL résoudre les emplacements valides que l’extension peut accéder sur l’ordinateur distant.

### <a name="invalid-configurationargument-type"></a>Type de ConfigurationArgument non valide
« Type de configurationArguments non valides {0} »

Problème : La propriété ConfigurationArguments ne peut pas résoudre à un objet Hashtable. 

Solution : Vérifiez votre propriété ConfigurationArguments une table de hachage. Suivez le format fourni dans l’exemple précédent. Méfiez-vous des devis, des virgules et entre accolades.

### <a name="duplicate-configurationarguments"></a>ConfigurationArguments en double
« Trouvé des arguments en double '{0}' dans configurationArguments publics et protégés »

Problème : Les ConfigurationArguments dans les paramètres publics et les ConfigurationArguments dans les paramètres protégés contiennent des propriétés portant le même nom.

Solution : Supprimez l’une des propriétés en double.

### <a name="missing-properties"></a>Propriétés manquantes
« Configuration.function nécessite que configuration.url ou configuration.module est spécifié. »

« Configuration.url nécessite que configuration.script est spécifié. »

« Configuration.script nécessite que configuration.url est spécifié. »

« Configuration.url nécessite que configuration.function est spécifié. »

« ConfigurationUrlSasToken nécessite que configuration.url est spécifié. »

« ConfigurationDataUrlSasToken nécessite que configurationData.url est spécifié. »

Problème : Une propriété définie a besoin d’une autre propriété qui est manquante.

Solutions : 
- Fournissent la propriété manquante.
- Supprimez la propriété qui doit être la propriété manquante.


## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur DSC et l’échelle de l’ordinateur virtuel se définit dans [L’utilisation de Machine virtuelle échelle définit avec l’Extension de DSC Azure](../virtual-machine-scale-sets/virtual-machine-scale-sets-dsc.md)

Trouver plus d’informations sur la [Gestion des informations d’identification sécurisées de DSC](virtual-machines-windows-extensions-dsc-credentials.md). 

Pour plus d’informations sur le Gestionnaire d’extension DSC d’Azure, consultez [Introduction au gestionnaire d’extension Azure Configuration d’état souhaitée](virtual-machines-windows-extensions-dsc-overview.md). 

Pour plus d’informations sur PowerShell DSC, [visitez le centre de documentation PowerShell](https://msdn.microsoft.com/powershell/dsc/overview). 
