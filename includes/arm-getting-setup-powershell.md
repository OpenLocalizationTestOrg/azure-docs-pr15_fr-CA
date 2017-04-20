## <a name="setting-up-powershell-for-resource-manager-templates"></a>Configuration de PowerShell pour le Gestionnaire de ressources de modèles

Avant de pouvoir utiliser PowerShell d’Azure avec le Gestionnaire de ressources, vous devez avoir le droit de Windows PowerShell et les versions de PowerShell d’Azure.

### <a name="verify-powershell-versions"></a>Vérifier les versions de PowerShell

Vérifiez que vous disposez de Windows PowerShell version 3.0 ou 4.0. Pour rechercher la version de Windows PowerShell, tapez cette commande à l’invite de commande Windows PowerShell.

    $PSVersionTable

Le type d’information suivant s’affiche :

    Name                           Value
    ----                           -----
    PSVersion                      3.0
    WSManStackVersion              3.0
    SerializationVersion           1.1.0.1
    CLRVersion                     4.0.30319.18444
    BuildVersion                   6.2.9200.16481
    PSCompatibleVersions           {1.0, 2.0, 3.0}
    PSRemotingProtocolVersion      2.2


Vérifiez que la valeur de **PSVersion** est 3.0 ou 4.0. Si ce n’est pas le cas, reportez-vous à la section [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) ou [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855).

### <a name="set-your-azure-account-and-subscription"></a>Définissez votre compte Azure et abonnement

Si vous ne disposez pas d’un abonnement Azure, vous pouvez activer vos [avantages d’abonné MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou le signe pour un [essai gratuit](https://azure.microsoft.com/pricing/free-trial/).

Ouvrez une invite de commande PowerShell de Azure et la session Azure avec cette commande.

    Login-AzureRmAccount

Si vous avez plusieurs abonnements Azure, vous pouvez répertorier vos abonnements Azure avec cette commande.

    Get-AzureRmSubscription

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

    $subscr="<SubscriptionName from the display of Get-AzureRmSubscription>"
    Select-AzureRmSubscription -SubscriptionName $subscr -Current

Pour plus d’informations sur les abonnements Azure et les comptes, reportez-vous à la section [Comment : se connecter à votre abonnement](powershell-install-configure.md#Connect).
