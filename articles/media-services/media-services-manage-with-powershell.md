<properties 
    pageTitle="Gérer les comptes de Services Azure Media avec PowerShell" 
    description="Apprenez à gérer les comptes d’Azure Media Services avec les applets de commande PowerShell." 
    authors="Juliako" 
    manager="erikre" 
    editor="" 
    services="media-services" 
    documentationCenter=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/03/2016"
    ms.author="juliako"/>


#<a name="manage-azure-media-services-accounts-with-powershell"></a>Gérer les comptes de Services Azure Media avec PowerShell

> [AZURE.SELECTOR]
- [Portail](media-services-portal-create-account.md)
- [PowerShell](media-services-manage-with-powershell.md)
- [RESTE](http://msdn.microsoft.com/library/azure/dn194267.aspx)

> [AZURE.NOTE] Pour être en mesure de créer un compte Azure Media Services, vous devez avoir un compte Azure. Si vous n’avez pas un compte, vous pouvez créer un compte d’essai gratuit dans quelques minutes. Pour plus d’informations, reportez-vous à la section <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A8A8397B5" target="_blank">D’essai Azure</a>.

##<a name="overview"></a>Vue d’ensemble 

Cet article répertorie les applets de commande PowerShell de Azure pour Azure Media Services (AMS) dans le cadre du Gestionnaire de ressources Azure. Les applets de commande existent dans l’espace de noms **Microsoft.Azure.Commands.Media** .

## <a name="versions"></a>Versions

**ApiVersion**: « 01-10/2015 »
               

## <a name="new-azurermmediaservice"></a>Nouvelle-AzureRmMediaService

Crée un service de support.

### <a name="syntax"></a>Syntaxe

Paramètre la valeur : StorageAccountIdParamSet

    New-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string> [-Location] <string> [-StorageAccountId] <string> [-Tags <hashtable>]  [<CommonParameters>]

Paramètre la valeur : StorageAccountsParamSet

    New-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string> [-Location] <string> [-StorageAccounts] <PSStorageAccount[]> [-Tags <hashtable>]  [<CommonParameters>]

### <a name="parameters"></a>Paramètres

**ResourceGroupName - &lt;chaîne&gt;**

Spécifie le nom du groupe de ressources auquel appartient ce service de support.

Alias | Aucun
---|---
Obligatoire ?   |  valeur True
Position ?   |  0
Valeur par défaut |Aucun
Accepter les entrées de pipeline ? |True(ByPropertyName)
Accepte les caractères génériques ?  |False

**Le nom de compte - &lt;chaîne&gt;**

Spécifie le nom du service de support.

Alias |Nom
---|---
Obligatoire ? |valeur True
Position ? |1
Valeur par défaut |Aucun
Accepter les entrées de pipeline ? |False
Accepte les caractères génériques ? |False

**-Emplacement &lt;chaîne&gt;**

Spécifie l’emplacement de la ressource du service de support.

Alias |Aucun
---|---
Obligatoire ? |valeur True
Position ? |2
Valeur par défaut  |Aucun
Accepter les entrées de pipeline ? |True(ByPropertyName)
Accepte les caractères génériques ? |False

**StorageAccountId - &lt;chaîne&gt;**

Spécifie un compte de stockage principal associé au service de support.

- Nouveau compte de stockage (créée avec l’API du Gestionnaire de ressources) pris en charge uniquement.

- Le compte de stockage doit exister et a le même emplacement et avec le service de support.

Alias |Aucun
---|---
Obligatoire ? |valeur True
Position ? |3
Valeur par défaut  |Aucun
Accepter les entrées de pipeline ? |True(ByPropertyName)
Nom du paramètre de jeu |StorageAccountIdParamSet
Accepte les caractères génériques ?|False

**-StorageAccounts &lt;PSStorageAccount\[\]&gt;**

Spécifie les comptes de stockage associé au service de support.

- Nouveau compte de stockage (créée avec l’API du Gestionnaire de ressources) pris en charge uniquement.

- Le compte de stockage doit exister et a le même emplacement et avec le service de support.

- Un seul compte de stockage peut être spécifié en tant que principal.

Alias |Aucun
---|---
Obligatoire ?  |valeur True
Position ?  |3
Valeur par défaut |Aucun
Accepter les entrées de pipeline ? |True(ByPropertyName)
Nom du paramètre de jeu |StorageAccountsParamSet
Accepte les caractères génériques ? |False

**-Balises &lt;table de hachage&gt;**

Spécifie une table de hachage des balises qui sont associés avec le service de support.

- Exemple :@{"tag1"="value1";"tag2"=:value2"}

Alias |Aucun
---|---
Obligatoire ?  |False
Position ?  |nommé
Valeur par défaut |Aucun
Accepter les entrées de pipeline ? |False
Accepte les caractères génériques ? |False

**&lt;Paramètres_commande&gt;**

Cette applet de commande prend en charge les paramètres courants :-Debug, - ErrorAction, - ErrorVariable, - InformationAction, - InformationVariable, - OutVariable,-OutBuffer, - PipelineVariable, - Verbose, - WarningAction et - WarningVariable.

### <a name="inputs"></a>Entrées

Le type d’entrée est le type des objets que vous pouvez insérer dans l’applet de commande.

### <a name="outputs"></a>Sorties

Le type de sortie est le type des objets que la cdmlet émet.

## <a name="set-azurermmediaservice"></a>Ensemble-AzureRmMediaService

Met à jour un service de support.

### <a name="syntax"></a>Syntaxe

    Set-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string> [-Tags <hashtable>] [-StorageAccounts <PSStorageAccount[]>]  [<CommonParameters>]

### <a name="parameters"></a>Paramètres

**ResourceGroupName - &lt;chaîne&gt;**

Spécifie le nom du groupe de ressources auquel appartient ce service de support.

Alias |Aucun
---|---
Obligatoire ?  |valeur True
Position ?  |0
Valeur par défaut |Aucun
Accepter les entrées de Pipeline ? |True(ByPropertyName)
Accepte les caractères génériques ? |False

**Le nom de compte - &lt;chaîne&gt;**

Spécifie le nom du service de support.

Alias |Nom
---|---
Obligatoire ? |True
Position ? |1
Valeur par défaut |Aucun
Accepter les entrées de pipeline ? |True(ByPropertyName)
Accepte les caractères génériques ? |False

**-StorageAccounts &lt;PSStorageAccount\[\]&gt;**

Spécifie les comptes de stockage associé au service de support.

- Nouveau compte de stockage (créée avec l’API du Gestionnaire de ressources) pris en charge uniquement.

- Le compte de stockage doit exister et a le même emplacement et avec le service de support.

- Un seul compte de stockage peut être spécifié en tant que principal.

Alias |Aucun
---|---
Obligatoire ? |False
Position ? |Nommé
Valeur par défaut |Aucun
Accepter les entrées de pipeline ? |True(ByPropertyName)
Nom du paramètre de jeu |StorageAccountsParamSet
Accepte les caractères génériques ? |False

**-Balises &lt;table de hachage&gt;**

Spécifie une table de hachage des balises qui sont associés à ce service de support.

- Les balises qui sont associés avec le service de média sont remplacées par la valeur spécifiée par le client.

Alias |Aucun
---|---
Obligatoire ? |False
Position ?  |Nommé
Valeur par défaut |Aucun
Accepter les entrées de pipeline ? |True(ByPropertyName)
Accepte les caractères génériques ? |False

**&lt;Paramètres_commande&gt;**

Cette applet de commande prend en charge les paramètres courants :-Debug, - ErrorAction, - ErrorVariable, - InformationAction, - InformationVariable, - OutVariable,-OutBuffer, - PipelineVariable, - Verbose, - WarningAction et - WarningVariable.

### <a name="inputs"></a>Entrées

Le type d’entrée est le type des objets que vous pouvez insérer dans l’applet de commande.

### <a name="outputs"></a>Sorties

Le type de sortie est le type des objets que la cdmlet émet.

## <a name="remove-azurermmediaservice"></a>Supprimer-AzureRmMediaService

Supprime un service de support.

### <a name="syntax"></a>Syntaxe

    Remove-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string>  [<CommonParameters>]

### <a name="parameters"></a>Paramètres

**ResourceGroupName - &lt;chaîne&gt;**

Spécifie le nom du groupe de ressources auquel appartient ce service de support.

Alias |Aucun
---|---
Obligatoire ? |valeur True
Position ? |0
Valeur par défaut |Aucun
Accepter les entrées de pipeline ? |True(ByPropertyName)
Accepte les caractères génériques ? |False

**Le nom de compte - &lt;chaîne&gt;**

Spécifie le nom du service de support.

Alias |Aucun
---|---
Obligatoire ? |valeur True
Position ? |2
Valeur par défaut |Aucun
Accepter les entrées de pipeline ?  |True(ByPropertyName)
Accepte les caractères génériques ? |False

**&lt;Paramètres_commande&gt;**

Cette applet de commande prend en charge les paramètres courants :-Debug, - ErrorAction, - ErrorVariable, - InformationAction, - InformationVariable, - OutVariable,-OutBuffer, - PipelineVariable, - Verbose, - WarningAction et - WarningVariable.

### <a name="inputs"></a>Entrées

Le type d’entrée est le type des objets que vous pouvez insérer dans l’applet de commande.

### <a name="outputs"></a>Sorties

Le type de sortie est le type des objets que la cdmlet émet.

## <a name="get-azurermmediaservice"></a>Get-AzureRmMediaService

Obtient tous les services de support dans un groupe de ressources ou d’un service de support avec un nom donné.

### <a name="syntax"></a>Syntaxe

ParameterSet : ResourceGroupParameterSet

    Get-AzureRmMediaService [-ResourceGroupName] <string>  [<CommonParameters>] 

ParameterSet : AccountNameParameterSet

    Get-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string>  [<CommonParameters>]

### <a name="parameters"></a>Paramètres

**ResourceGroupName - &lt;chaîne&gt;**

Spécifie le nom du groupe de ressources auquel appartient ce service de support.

Alias |Aucun
---|---
Obligatoire ? |valeur True
Position ?  |0
Valeur par défaut |Aucun
Accepter les entrées de pipeline ? |True(ByPropertyName)
Nom du paramètre de jeu |ResourceGroupParameterSet, AccountNameParameterSet
Accepte les caractères génériques ?   False

**Le nom de compte - &lt;chaîne&gt;**

Spécifie le nom du service de support.

Alias |Aucun
---|---
Obligatoire ? |valeur True
Position ?  |1
Valeur par défaut |Aucun
Accepter les entrées de pipeline ? |True(ByPropertyName)
Nom du paramètre de jeu  |AccountNameParameterSet
Accepte les caractères génériques ? |False

**&lt;Paramètres_commande&gt;**

Cette applet de commande prend en charge les paramètres courants :-Debug, - ErrorAction, - ErrorVariable, - InformationAction, - InformationVariable, - OutVariable,-OutBuffer, - PipelineVariable, - Verbose, - WarningAction et - WarningVariable.

### <a name="inputs"></a>Entrées

Le type d’entrée est le type des objets que vous pouvez insérer dans l’applet de commande.

### <a name="outputs"></a>Sorties

Le type de sortie est le type des objets que la cdmlet émet.

## <a name="get-azurermmediaservicekeys"></a>Get-AzureRmMediaServiceKeys

Obtient les clés d’un service de support.

### <a name="syntax"></a>Syntaxe

    Get-AzureRmMediaServiceKeys [-ResourceGroupName] <string> [-AccountName] <string>  [<CommonParameters>]

### <a name="parameters"></a>Paramètres

**ResourceGroupName - &lt;chaîne&gt;**

Spécifie le nom du groupe de ressources auquel appartient ce service de support.

Alias |Aucun
---|---
Obligatoire ? |valeur True
Position ?  |0
Valeur par défaut |Aucun
Accepter les entrées de pipeline ? |True(ByPropertyName)
Accepte les caractères génériques ? |False

**Le nom de compte - &lt;chaîne&gt;**

Spécifie le nom du service de support.

Alias |Aucun
---|---
Obligatoire ? |valeur True
Position ? |1
Valeur par défaut |Aucun
Accepter les entrées de pipeline ? |True(ByPropertyName)
Accepte les caractères génériques ? |False

**&lt;Paramètres_commande&gt;**

Cette applet de commande prend en charge les paramètres courants :-Debug, - ErrorAction, - ErrorVariable, - InformationAction, - InformationVariable, - OutVariable,-OutBuffer, - PipelineVariable, - Verbose, - WarningAction et - WarningVariable.

### <a name="inputs"></a>Entrées

Le type d’entrée est le type des objets que vous pouvez insérer dans l’applet de commande.

### <a name="outputs"></a>Sorties

Le type de sortie est le type des objets que la cdmlet émet.

## <a name="set-azurermmediaservicekey"></a>Ensemble-AzureRmMediaServiceKey

Régénère une clé primaire ou secondaire d’un service de support.

### <a name="syntax"></a>Syntaxe

    Set-AzureRmMediaServiceKey [-ResourceGroupName] <string> [-AccountName] <string> [-KeyType] <KeyType> {Primary | Secondary}  [<CommonParameters>]

### <a name="parameters"></a>Paramètres

**ResourceGroupName - &lt;chaîne&gt;**

Spécifie le nom du groupe de ressources auquel appartient ce service de support.

Alias |Aucun
---|---
Obligatoire ?  |valeur True
Position ?  |0
Valeur par défaut |Aucun
Accepter les entrées de pipeline ?  |True(ByPropertyName)
Accepte les caractères génériques ? |False

**Le nom de compte - &lt;chaîne&gt;**

Spécifie le nom du service de support.

Alias |Aucun
---|---
Obligatoire ? |valeur True
Position ?  |1
Valeur par défaut |Aucun
Accepter les entrées de pipeline ?   |True(ByPropertyName)
Accepte les caractères génériques ? |False

**KeyType - &lt;KeyType&gt;**

Spécifie le type de clé du service de support.

- Principal ou secondaire

Alias |Aucun
---|---
Obligatoire ?  |valeur True
Position ?  |2
Valeur par défaut |Aucun
Accepter les entrées de pipeline ? |False
Accepte les caractères génériques ? |False

**&lt;Paramètres_commande&gt;**

Cette applet de commande prend en charge les paramètres courants :-Debug, - ErrorAction, - ErrorVariable, - InformationAction, - InformationVariable, - OutVariable,-OutBuffer, - PipelineVariable, - Verbose, - WarningAction et - WarningVariable.

### <a name="inputs"></a>Entrées

Le type d’entrée est le type des objets que vous pouvez insérer dans l’applet de commande.

### <a name="outputs"></a>Sorties

Le type de sortie est le type des objets que la cdmlet émet.

## <a name="sync-azurermmediaservicestoragekeys"></a>AzureRmMediaServiceStorageKeys de la synchronisation

Synchronise les clés de compte de stockage pour un compte de stockage associé au service de support.

### <a name="syntax"></a>Syntaxe

    Sync-AzureRmMediaServiceStorageKeys [-ResourceGroupName] <string> [-MediaServiceAccountName] <string>    [-StorageAccountId] <string>  [<CommonParameters>]

### <a name="parameters"></a>Paramètres

**ResourceGroupName - &lt;chaîne&gt;**

Spécifie le nom du groupe de ressources auquel appartient ce service de support.

Alias |Aucun
---|---
Obligatoire ? |valeur True
Position ? |0
Valeur par défaut |Aucun
Accepter les entrées de pipeline ? |True(ByPropertyName)
Accepte les caractères génériques ? |False

**Le nom de compte - &lt;chaîne&gt;**

Spécifie le nom du service de support.

Alias |Aucun
---|---
Obligatoire ? |valeur True
Position ? |1
Valeur par défaut |Aucun
Accepter les entrées de pipeline ? |True(ByPropertyName)
Accepte les caractères génériques ? |False

**StorageAccountId - &lt;chaîne&gt;**

Spécifie le compte de stockage associé au service de support.

Alias |ID
---|---
Obligatoire ? |valeur True
Position ?  |2
Valeur par défaut |Aucun
Accepter les entrées de pipeline ? |      True(ByPropertyName)
Accepte les caractères génériques ? |False

**&lt;Paramètres_commande&gt;**

Cette applet de commande prend en charge les paramètres courants :-Debug, - ErrorAction, - ErrorVariable, - InformationAction, - InformationVariable, - OutVariable,-OutBuffer, - PipelineVariable, - Verbose, - WarningAction et - WarningVariable.

### <a name="inputs"></a>Entrées

Le type d’entrée est le type des objets que vous pouvez insérer dans l’applet de commande.

### <a name="outputs"></a>Sorties

Le type de sortie est le type des objets que la cdmlet émet.

## <a name="next-step"></a>Étape suivante 

Extraction de Media Services, voies de formation.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

 
