<properties
   pageTitle="Créer un jeu d’enregistrements et d’enregistrements pour une zone DNS à l’aide de PowerShell | Microsoft Azure"
   description="Comment créer des enregistrements d’hôte pour DNS d’Azure. Configuration d’enregistrement définit et enregistre l’utilisation de PowerShell"
   services="dns"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/16/2016"
   ms.author="sewhee"/>



# <a name="create-dns-record-sets-and-records-by-using-powershell"></a>Créer des enregistrements et jeux d’enregistrements DNS à l’aide de PowerShell


> [AZURE.SELECTOR]
- [Azure Portal](dns-getstarted-create-recordset-portal.md)
- [PowerShell](dns-getstarted-create-recordset.md)
- [CLI Azure](dns-getstarted-create-recordset-cli.md)

Cet article vous guide tout au long du processus de création d’enregistrements et jeux d’enregistrements à l’aide de Windows PowerShell. Après avoir créé votre zone DNS, vous ajoutez les enregistrements DNS pour votre domaine. Pour ce faire, vous devez d’abord comprendre les enregistrements DNS et les jeux d’enregistrements.

[AZURE.INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

## <a name="verify-that-you-have-the-latest-version-of-powershell"></a>Vérifiez que vous disposez de la version la plus récente de PowerShell

Vérifiez que vous avez installé la dernière version de cmdlets PowerShell du Gestionnaire de ressources Azure. Pour plus d’informations sur l’installation des applets de commande PowerShell, voir [Comment faire pour installer et configurer Azure PowerShell](../powershell-install-configure.md) .

## <a name="create-a-record-set-and-record"></a>Créer un jeu d’enregistrements et l’enregistrement

Cette section décrit comment créer un jeu d’enregistrements et l’enregistrement.


### <a name="1-connect-to-your-subscription"></a>1. Connectez-vous à votre abonnement

Ouvrez la console PowerShell et vous connecter à votre compte. Pour vous aider à vous connecter, utilisez l’exemple suivant :

    Login-AzureRmAccount

Vérifiez les abonnements pour le compte.

    Get-AzureRmSubscription

Spécifier l’abonnement que vous souhaitez utiliser.

    Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

Pour plus d’informations sur l’utilisation de PowerShell, reportez-vous [à l’aide de Windows PowerShell avec le Gestionnaire de ressources](../powershell-azure-resource-manager.md).


### <a name="2-create-a-record-set"></a>2. créer un jeu d’enregistrements

Vous créez des jeux d’enregistrements à l’aide de la `New-AzureRmDnsRecordSet` applet de commande. Lorsque vous créez un jeu d’enregistrements, vous devez spécifier que l’enregistrement de définie le nom, la zone, la durée de vie et le type d’enregistrement.

Pour créer un enregistrement figurant au sommet de la zone (dans ce cas, « contoso.com »), utilisez le nom d’enregistrement "@", y compris les guillemets. Il s’agit d’une convention DNS courante.

L’exemple suivant crée un enregistrement défini avec le nom « www » dans la Zone DNS « contoso.com ». Le nom complet du jeu d’enregistrements est « www.contoso.com ». Le type d’enregistrement est « A », et la durée de vie est de 60 secondes. À l’issue de cette étape, vous avez un jeu d’enregistrements vide « www » qui est affecté à la variable *$rs*.

    $rs = New-AzureRmDnsRecordSet -Name "www" -RecordType "A" -ZoneName "contoso.com" -ResourceGroupName "MyAzureResourceGroup" -Ttl 60

#### <a name="if-a-record-set-already-exists"></a>Si un jeu d’enregistrements déjà existe

Si il existe un enregistrement déjà défini, la commande échoue, à moins que le *-Remplacer* est utilisé. Le *-Remplacer* option déclenche un message de confirmation, qui peut être supprimé à l’aide de la *-Force* basculer.


    $rs = New-AzureRmDnsRecordSet -Name www -RecordType A -Ttl 300 -ZoneName contoso.com -ResouceGroupName MyAzureResouceGroup [-Tag $tags] [-Overwrite] [-Force]


Dans cet exemple, vous spécifiez la zone à l’aide du nom de zone et le nom du groupe de ressources. Vous pouvez également spécifier un objet de la zone, tel que retourné par `Get-AzureRmDnsZone` ou `New-AzureRmDnsZone`.

    $zone = Get-AzureRmDnsZone -Name contoso.com –ResourceGroupName MyAzureResourceGroup
    $rs = New-AzureRmDnsRecordSet -Name www -RecordType A -Ttl 300 –Zone $zone [-Tag $tags] [-Overwrite] [-Force]

`New-AzureRmDnsRecordSet`Retourne un objet qui représente le jeu d’enregistrements qui a été créé dans Azure DNS local.

### <a name="3-add-a-record"></a>3. ajouter un enregistrement

Pour utiliser le jeu d’enregistrements qui vient d’être créé « www », vous devez ajouter des enregistrements. Vous pouvez ajouter IPv4 *A* des enregistrements à l’enregistrement de « www » définie à l’aide de l’exemple suivant. Cet exemple repose sur la variable *$rs* que vous avez défini à l’étape précédente.

Ajout d’enregistrements à un enregistrement défini à l’aide de `Add-AzureRmDnsRecordConfig` est une opération hors connexion. Uniquement le local variable *$rs* est mis à jour.


    Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address 134.170.185.46
    Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address 134.170.188.221

### <a name="4-commit-the-changes"></a>4. valider les modifications

Valider les modifications dans le jeu d’enregistrements. Utilisez `Set-AzureRmDnsRecordSet` pour télécharger les modifications vers l’enregistrement DNS d’Azure.

    Set-AzureRmDnsRecordSet -RecordSet $rs

### <a name="5-retrieve-the-record-set"></a>5. récupérer le jeu d’enregistrements

Vous pouvez récupérer l’ensemble d’Azure DNS à l’aide des enregistrements `Get-AzureRmDnsRecordSet` comme indiqué dans l’exemple suivant.


    Get-AzureRmDnsRecordSet –Name www –RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup


    Name              : www
    ZoneName          : contoso.com
    ResourceGroupName : MyAzureResourceGroup
    Ttl               : 3600
    Etag              : 68e78da2-4d74-413e-8c3d-331ca48246d9
    RecordType        : A
    Records           : {134.170.185.46, 134.170.188.221}
    Tags              : {}


Vous pouvez également utiliser l’outil nslookup ou autres outils DNS pour interroger le nouveau jeu d’enregistrements.

Si vous n’avez pas encore délégué le domaine pour les serveurs de noms DNS d’Azure, vous devez spécifier explicitement le nom, le serveur et l’adresse pour votre zone.


    nslookup www.contoso.com ns1-01.azure-dns.com

    Server: ns1-01.azure-dns.com
    Address:  208.76.47.1

    Name:    www.contoso.com
    Addresses:  134.170.185.46
                134.170.188.221

## <a name="create-a-record-set-of-each-type-with-a-single-record"></a>Créer un jeu d’enregistrements de chaque type avec un enregistrement unique


Les exemples suivants montrent comment créer un jeu d’enregistrements de chaque type d’enregistrement. Chaque jeu d’enregistrements contient un seul enregistrement.

[AZURE.INCLUDE [dns-add-record-ps-include](../../includes/dns-add-record-ps-include.md)]


## <a name="next-steps"></a>Étapes suivantes

[Comment gérer les zones DNS à l’aide de PowerShell](dns-operations-dnszones.md)

[Gérer les enregistrements DNS et les jeux d’enregistrements à l’aide de PowerShell](dns-operations-recordsets.md)

[Automatiser les opérations Azure avec le Kit de développement .NET](dns-sdk.md)
