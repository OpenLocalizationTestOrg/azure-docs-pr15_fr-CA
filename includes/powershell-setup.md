<properties services="virtual-machines" title="Setting up PowerShell" authors="JoeDavies-MSFT" solutions="" manager="timlt" editor="tysonn" />

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm=""
   ms.workload="infrastructure"
   ms.date="05/12/2015"
   ms.author="rasquill" />

## <a name="setting-up-powershell"></a>Configuration de PowerShell

Avant de pouvoir utiliser PowerShell d’Azure, procédez comme suit.

### <a name="verify-powershell-versions"></a>Vérifier les versions de PowerShell

Avant de pouvoir utiliser Windows PowerShell, vous devez disposer de Windows PowerShell, Version 3.0 ou 4.0. Pour rechercher la version de Windows PowerShell, tapez cette commande à l’invite de commande Windows PowerShell.

    $PSVersionTable

Vous devriez voir quelque chose comme ceci.

    Name                           Value
    ----                           -----
    PSVersion                      3.0
    WSManStackVersion              3.0
    SerializationVersion           1.1.0.1
    CLRVersion                     4.0.30319.18444
    BuildVersion                   6.2.9200.16481
    PSCompatibleVersions           {1.0, 2.0, 3.0}
    PSRemotingProtocolVersion      2.2

Vérifiez que la valeur de **PSVersion** est 3.0 ou 4.0. Pour installer une version compatible, consultez [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) ou [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855).

Vous devez également avoir Azure PowerShell version 0.8.0 ou une version ultérieure. Vous pouvez vérifier la version de PowerShell Azure que vous avez installés à l’aide de cette commande à l’invite de commande PowerShell d’Azure.

    Get-Module azure | format-table version

Vous devriez voir quelque chose comme ceci.

    Version
    -------
    0.8.16.1

Pour obtenir des instructions et un lien vers la dernière version, consultez [comment installer et configurer le PowerShell Azure](powershell-install-configure.md).


### <a name="set-your-azure-account-and-subscription"></a>Définissez votre compte Azure et abonnement

Si vous ne disposez pas d’un abonnement Azure, vous pouvez activer vos [avantages d’abonné MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou le signe pour un [essai gratuit](https://azure.microsoft.com/pricing/free-trial/).

Ouvrez une invite de commande PowerShell de Azure et la session Azure avec cette commande.

    Add-AzureAccount

Si vous avez plusieurs abonnements Azure, vous pouvez répertorier vos abonnements Azure avec cette commande.

    Get-AzureSubscription

Le type d’information suivant s’affiche :

    SubscriptionId            : fd22919d-eaca-4f2b-841a-e4ac6770g92e
    SubscriptionName          : Visual Studio Ultimate with MSDN
    Environment               : AzureCloud
    SupportedModes            : AzureServiceManagement,AzureResourceManager
    DefaultAccount            : johndoe@contoso.com
    Accounts                  : {johndoe@contoso.com}
    IsDefault                 : True
    IsCurrent                 : True
    CurrentStorageAccountName : 
    TenantId                  : 32fa88b4-86f1-419f-93ab-2d7ce016dba7

Vous pouvez définir l’abonnement Azure en cours en exécutant ces commandes à l’invite de commande PowerShell d’Azure. Remplacez tout entre guillemets, y compris la < et > caractères, avec le nom correct.

    $subscr="<SubscriptionName from the display of Get-AzureSubscription>"
    Select-AzureSubscription -SubscriptionName $subscr -Current 

Pour plus d’informations sur les abonnements Azure et les comptes, reportez-vous à la section [Comment : se connecter à votre abonnement](powershell-install-configure.md#Connect).
