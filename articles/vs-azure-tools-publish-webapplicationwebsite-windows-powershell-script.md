<properties
   pageTitle="(Script Windows PowerShell) de publier-WebApplicationWebSite | Microsoft Azure"
   description="Apprenez à publier un projet web sur un site Web Azure. Ce script crée les ressources requises dans votre abonnement Azure si elles n’existent pas."
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

# <a name="publish-webapplicationwebsite-windows-powershell-script"></a>(Script Windows PowerShell) de publier-WebApplicationWebSite

##<a name="syntax"></a>Syntaxe

Publie un projet web sur un site Web d’Azure. Le script crée les ressources requises dans votre abonnement Azure si elles n’existent pas.

    Publish-WebApplicationWebSite
    –Configuration <configuration>
    -SubscriptionName <subscriptionName>
    -WebDeployPackage <packageName>
    -DatabaseServerPassword @{Name = "name"; Password = "password"}
    -SendHostMessagesToOutput
    -Verbose


## <a name="configuration"></a>Configuration de

Le chemin d’accès au fichier de configuration JSON qui décrit les détails du déploiement.

|Paramètre|Valeur par défaut|
|---|---|
|Alias|Aucun|
|Obligatoire ?|valeur True|
|Position|nommé|
|Valeur par défaut|Aucun|
|Accepter les entrées de pipeline ?|False|
|Accepte les caractères génériques ?|False|

## <a name="subscriptionname"></a>SubscriptionName

Le nom de l’abonnement Azure que vous souhaitez créer le site Web de.

|Paramètre|Valeur par défaut|
|---|---|
|Alias|Aucun|
|Obligatoire ?|False|
|Position|nommé|
|Valeur par défaut|Aucun|
|Accepter les entrées de pipeline ?|False|
|Accepte les caractères génériques ?|False|

## <a name="webdeploypackage"></a>WebDeployPackage

Le chemin d’accès au package de déploiement web pour publier sur le site Web. Vous pouvez créer ce package à l’aide de l’Assistant Publication de site Web dans Visual Studio. Pour plus d’informations, consultez [mise en route avec les Services en nuage Azure et ASP.NET](http://go.microsoft.com/fwlink/p/?LinkID=623089).

|Paramètre|Valeur par défaut|
|---|---|
|Alias|Aucun|
|Obligatoire ?|False|
|Position|nommé|
|Valeur par défaut|Aucun|
|Accepter les entrées de pipeline ?|False|
|Accepte les caractères génériques ?|False|

## <a name="databaseserverpassword"></a>DatabaseServerPassword

Le nom d’utilisateur et un mot de passe de la base de données SQL Azure.

|Paramètre|Valeur par défaut|
|---|---|
|Alias|Aucun|
|Obligatoire ?|False|
|Position|nommé|
|Valeur par défaut|Aucun|
|Accepter les entrées de pipeline ?|False|
|Accepte les caractères génériques ?|False|

## <a name="sendhostmessagestooutput"></a>SendHostMessagesToOutput

Si la valeur est true, imprime des messages à partir du script dans le flux de sortie.

|Paramètre|Valeur par défaut|
|---|---|
|Alias|Aucun|
|Obligatoire ?|False|
|Position|nommé|
|Valeur par défaut|False|
|Accepter les entrées de pipeline ?|False|
|Accepte les caractères génériques ?|False|

## <a name="remarks"></a>Remarques

Pour obtenir une explication complète de la façon d’utiliser le script pour créer des environnements de Test et développement, consultez [L’aide de Scripts Windows PowerShell à publier sur les environnements de Test et de développement](vs-azure-tools-publishing-using-powershell-scripts.md).

Le fichier de configuration de JSON spécifie les détails de ce qui doit être déployée. Il inclut les informations que vous avez spécifié lorsque vous avez créé le projet, comme le nom et le nom d’utilisateur pour le site Web. Il comprend également la base de données à la disposition, le cas échéant. Le code suivant illustre un exemple de fichier de configuration de JSON :

    {
        "environmentSettings": {
            "webSite": {
                "name": "WebApplication10554",
                "location": "West US"
            },
            "databases": [
                {
                    "connectionStringName": "DefaultConnection",
                    "databaseName": "WebApplication10554_db",
                    "serverName": "iss00brc88",
                    "user": "sqluser2",
                    "password": "",
                    "edition": "",
                    "size": "",
                    "collation": "",
                    "location": "West US"
                }
            ]
        }
    }

Vous pouvez modifier le fichier de configuration de JSON pour modifier ce qui est déployé. Une section du site Web est requise, mais la section base de données est facultative.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez [Publication-WebApplicationVM (script Windows PowerShell)](vs-azure-tools-publish-webapplicationvm.md)
