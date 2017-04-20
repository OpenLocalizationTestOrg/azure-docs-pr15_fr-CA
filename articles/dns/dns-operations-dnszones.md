<properties
   pageTitle="Gérer les zones DNS à l’aide de PowerShell | Microsoft Azure"
   description="Vous pouvez gérer des zones DNS à l’aide de Powershell d’Azure. Comment faire pour mettre à jour, de supprimer et de créer des zones DNS sur Azure DNS"
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

# <a name="how-to-manage-dns-zones-using-powershell"></a>Comment gérer les Zones DNS à l’aide de PowerShell

> [AZURE.SELECTOR]
- [CLI Azure](dns-operations-dnszones-cli.md)
- [PowerShell](dns-operations-dnszones.md)



Cet article vous explique comment gérer votre zone DNS à l’aide de PowerShell. Pour l’utiliser, vous devez installer la dernière version de cmdlets PowerShell de gestionnaire de ressources Azure (1.0 ou ultérieurs). Pour plus d’informations sur l’installation des applets de commande PowerShell, voir [Comment faire pour installer et configurer Azure PowerShell](../powershell-install-configure.md) .


## <a name="create-a-new-dns-zone"></a>Créez une zone DNS

Pour créer une zone DNS, voir [Création d’une zone DNS à l’aide de PowerShell](dns-getstarted-create-dnszone.md).

## <a name="get-a-dns-zone"></a>Obtenir une zone DNS

Pour récupérer une zone DNS, utilisez la `Get-AzureRmDnsZone` applet de commande. Cette opération retourne un objet de zone DNS correspondant à une zone existante dans le système DNS d’Azure. L’objet contient des données sur la zone (par exemple, le nombre de jeux d’enregistrements), mais ne contient-elle pas les jeux d’enregistrement eux-mêmes.

    $zone = Get-AzureRmDnsZone -Name contoso.com –ResourceGroupName MyAzureResourceGroup

## <a name="list-dns-zones"></a>Zones DNS de liste

En omettant le nom de zone à partir de `Get-AzureRmDnsZone`, vous pouvez énumérer toutes les zones dans un groupe de ressources. Cette opération retourne un tableau d’objets de la zone.

    $zoneList = Get-AzureRmDnsZone -ResourceGroupName MyAzureResourceGroup

## <a name="update-a-dns-zone"></a>Mettre à jour une zone DNS

Une ressource de la zone DNS peuvent être modifiées à l’aide de `Set-AzureRmDnsZone`. Cela ne met pas à jour un des jeux d’enregistrements DNS dans la zone (voir [comment les enregistrements DNS de gérer](dns-operations-recordsets.md)). Il est utilisé uniquement pour mettre à jour les propriétés de la ressource de la zone elle-même. Il est actuellement limitée au Gestionnaire des ressources Azure 'balises' pour la ressource de la zone. Pour plus d’informations, reportez-vous à la section [Etags et des balises](dns-getstarted-create-dnszone.md#Etags-and-tags) .

Utilisez une des opérations suivantes deux façons de zone DNS de mise à jour :

### <a name="specify-the-zone-using-the-zone-name-and-resource-group"></a>Spécifier la zone en utilisant le groupe de ressources et le nom de zone

    Set-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup [-Tag $tags]

### <a name="specify-the-zone-using-a-zone-object"></a>Spécifier la zone à l’aide d’un objet $zone

Spécifier la zone à l’aide d’un objet $zone à partir de `Get-AzureRmDnsZone`. Lorsque vous utilisez `Set-AzureRmDnsZone` avec un objet $zone, Etag chèques seront utilisés pour vérifier les modifications simultanées ne sont pas remplacées. Vous pouvez utiliser l’option *-Remplacer* commutateur pour supprimer ces contrôles. Pour plus d’informations, reportez-vous à la section [Etags et des balises](dns-getstarted-create-dnszone.md#Etags-and-tags) .


    $zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
    <..modify $zone.Tags here...>
    Set-AzureRmDnsZone -Zone $zone [-Overwrite]


## <a name="delete-a-dns-zone"></a>Supprimer une Zone DNS

Les zones DNS peuvent être supprimés à l’aide de l’applet de commande Remove-AzureRmDnsZone.

Avant de supprimer une zone DNS dans le système DNS d’Azure, vous devez supprimer tous les jeux d’enregistrements, à l’exception des enregistrements NS et SOA à la racine de la zone qui ont été créés automatiquement lors de la création de la zone.

Utilisez une des manières suivantes pour supprimer une zone DNS :

### <a name="specify-the-zone-using-the-zone-name-and-resource-group-name"></a>Spécifier la zone à l’aide du nom et le nom du groupe de ressources

Cette opération a facultatif *-Force* commutateur qui supprime l’invite de confirmation Supprimer la zone DNS.

    Remove-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup [-Force]

### <a name="specify-the-zone-using-a-zone-object"></a>Spécifier la zone à l’aide d’un objet $zone

Spécifier la zone à l’aide d’un objet $zone à partir de `Get-AzureRmDnsZone`. Cette opération a facultatif *-Force* commutateur qui supprime l’invite de confirmation Supprimer la zone DNS. Comme avec `Set-AzureRmDnsZone`, qui spécifie la zone à l’aide d’un objet $zone permet l’Etag vérifie les modifications simultanées ne sont pas supprimées. <BR>
L’option *-Remplacer* indicateur supprime ces contrôles. Pour plus d’informations, reportez-vous à la section [Etags et des balises](dns-getstarted-create-dnszone.md#Etags-and-tags) .

    $zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
    Remove-AzureRmDnsZone -Zone $zone [-Force] [-Overwrite]



L’objet de zone peut également être rattachée au lieu du passé comme un paramètre :

    Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsZone [-Force] [-Overwrite]

## <a name="next-steps"></a>Étapes suivantes

Après avoir créé une zone DNS, créer des [ensembles d’enregistrements et d’enregistrements](dns-getstarted-create-recordset.md) pour démarrer la résolution des noms de votre domaine Internet.