<properties
   pageTitle="Publication-WebApplicationVM | Microsoft Azure"
   description="Découvrez comment déployer une application web à une machine virtuelle. Ce script crée les ressources requises dans votre abonnement Azure si elles n’existent pas."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="publish-webapplicationvm-windows-powershell-script"></a>(Script Windows PowerShell) de publier-WebApplicationVM

Déploie une application web à une machine virtuelle. Le script crée les ressources requises dans votre abonnement Azure si elles n’existent pas.

```
Publish-WebApplicationVM
–Configuration <configuration>
-SubscriptionName <subscriptionName>
-WebDeployPackage <packageName>
-VMPassword @{Name = "name"; Password = "password")
-DatabaseServerPassword @{Name = "name"; Password = "password"}
-SendHostMessagesToOutput
-Verbose
```

### <a name="configuration"></a>Configuration de

Le chemin d’accès au fichier de configuration JSON qui décrit les détails du déploiement.

|Alias|Aucun|
|---|---|
|Obligatoire ?|valeur True|
|Position|nommé|
|Valeur par défaut|Aucun|
|Accepter les entrées de pipeline ?|False|
|Accepte les caractères génériques ?|False|

### <a name="subscriptionname"></a>SubscriptionName

Le nom de l’abonnement Azure dans lequel vous souhaitez créer l’ordinateur virtuel.

|Alias|Aucun|
|---|---|
|Obligatoire ?|False|
|Position|nommé|
|Valeur par défaut|Utilise le premier abonnement dans le fichier d’abonnement|
|Accepter les entrées de pipeline ?|False|
|Accepte les caractères génériques ?|False|

### <a name="webdeploypackage"></a>WebDeployPackage

Le chemin d’accès au package de déploiement web pour publier sur l’ordinateur virtuel. Vous pouvez créer ce package à l’aide de l’Assistant Publication de site Web dans Visual Studio. Consultez [Comment : créer un Package de déploiement Web dans Visual Studio](https://msdn.microsoft.com/library/dd465323.aspx).

|Alias|Aucun|
|---|---|
|Obligatoire ?|False|
|Position|nommé|
|Valeur par défaut|Aucun|
|Accepter les entrées de pipeline ?|False|
|Accepte les caractères génériques ?|False|

### <a name="allowuntrusted"></a>AllowUntrusted

Si la valeur est true, permet l’utilisation de certificats qui ne sont pas signés par une autorité racine de confiance.

|Alias|Aucun|
|---|---|
|Obligatoire ?|False|
|Position|nommé|
|Valeur par défaut|False|
|Accepter les entrées de pipeline ?|False|
|Accepte les caractères génériques ?|False|

### <a name="vmpassword"></a>VMPassword

Les informations d’identification pour le compte de l’ordinateur virtuel. Exemple : - VMPassword @{Name = « admin » ; Mot de passe = « password »}

|Alias|Aucun|
|---|---|
|Obligatoire ?|False|
|Position|nommé|
|Valeur par défaut|Aucun|
|Accepter les entrées de pipeline ?|False|
|Accepte les caractères génériques ?|False|

### <a name="databaseserverpassword"></a>DatabaseServerPassword

Les informations d’identification pour la base de données SQL Azure. Exemple : - DatabaseServerPassword @{Name = « admin » ; Mot de passe = « password »}

|Alias|Aucun|
|---|---|
|Obligatoire ?|False|
|Position|nommé|
|Valeur par défaut|Aucun|
|Accepter les entrées de pipeline ?|False|
|Accepte les caractères génériques ?|False|

### <a name="sendhostmessagestooutput"></a>SendHostMessagesToOutput

Si la valeur est true, imprime des messages à partir du script dans le flux de sortie.

|Alias|Aucun|
|---|---|
|Obligatoire ?|False|
|Position|nommé|
|Valeur par défaut|False|
|Accepter les entrées de pipeline ?|False|
|Accepte les caractères génériques ?|False|

## <a name="remarks"></a>Remarques

Pour obtenir une explication complète de la façon d’utiliser le script pour créer des environnements de Test et développement, consultez [L’aide de Scripts Windows PowerShell à publier sur les environnements de Test et de développement](vs-azure-tools-publishing-using-powershell-scripts.md).

Le fichier de configuration de JSON spécifie les détails de ce qui doit être déployée. Il inclut les informations que vous avez spécifié lorsque vous avez créé le projet, comme le nom, groupe d’affinité, image VHD et la taille de la machine virtuelle. Il inclut les points de terminaison sur l’ordinateur virtuel, les bases de données de mise en service, le cas échéant et vous les paramètres de déploiement de web. Le code suivant illustre un exemple de fichier de configuration de JSON :

```
{
    "environmentSettings": {
        "cloudService": {
            "name": "myvmname",
            "affinityGroup": "",
            "location": "West US",
            "virtualNetwork": "",
            "subnet": "",
            "availabilitySet": "",
            "virtualMachine": {
                "name": "myvmname",
                "vhdImage": "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201404.01-en.us-127GB.vhd",
                "size": "Small",
                "user": "vmuser1",
                "password": "",
                "enableWebDeployExtension": true,
                "endpoints": [
                    {
                        "name": "Http",
                        "protocol": "TCP",
                        "publicPort": "80",
                        "privatePort": "80"
                    },
                    {
                        "name": "Https",
                        "protocol": "TCP",
                        "publicPort": "443",
                        "privatePort": "443"
                    },
                    {
                        "name": "WebDeploy",
                        "protocol": "TCP",
                        "publicPort": "8172",
                        "privatePort": "8172"
                    },
                    {
                        "name": "Remote Desktop",
                        "protocol": "TCP",
                        "publicPort": "3389",
                        "privatePort": "3389"
                    },
                    {
                        "name": "Powershell",
                        "protocol": "TCP",
                        "publicPort": "5986",
                        "privatePort": "5986"
                    }
                ]
            }
        },
        "databases": [
            {
                "connectionStringName": "",
                "databaseName": "",
                "serverName": "",
                "user": "",
                "password": ""
            }
        ],
        "webDeployParameters": {
            "iisWebApplicationName": "Default Web Site"
        }
    }
}
```

Vous pouvez modifier le fichier de configuration de JSON pour modifier ce qui est mis en service. Une machine virtuelle et un service en nuage sont obligatoires, mais la section base de données est facultative.
