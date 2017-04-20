<properties
   pageTitle="Gérer les enregistrements et les jeux d’enregistrements DNS à l’aide du portail Azure | Microsoft Azure"
   description="Gestion des jeux d’enregistrements DNS et enregistrements sur Azure DNS lors de l’hébergement de votre domaine DNS d’Azure. Toutes les commandes de PowerShell pour les opérations sur les jeux d’enregistrements et d’enregistrements."
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

# <a name="manage-dns-records-and-record-sets-by-using-powershell"></a>Gérer les enregistrements DNS et les jeux d’enregistrements à l’aide de PowerShell



> [AZURE.SELECTOR]
- [Azure Portal](dns-operations-recordsets-portal.md)
- [CLI Azure](dns-operations-recordsets-cli.md)
- [PowerShell](dns-operations-recordsets.md)



Cet article vous montre comment gérer des jeux d’enregistrements et d’enregistrements pour votre zone DNS à l’aide de Windows PowerShell.

Il est important de comprendre la différence entre les jeux d’enregistrements DNS et des enregistrements DNS. Un jeu d’enregistrements est l’ensemble d’enregistrements dans une zone qui portent le même nom et qui sont du même type. Pour plus d’informations, consultez [créer un DNS de jeux d’enregistrements et les enregistrements à l’aide du portail Azure](dns-getstarted-create-recordset-portal.md).

Pour gérer vos enregistrements et les jeux d’enregistrements, vous avez besoin de la version la plus récente de cmdlets PowerShell du Gestionnaire de ressources Azure. Pour plus d’informations, consultez [comment installer et configurer Azure PowerShell](../powershell-install-configure.md). Pour plus d’informations sur l’utilisation de PowerShell, reportez-vous [à l’aide du PowerShell Azure avec le Gestionnaire de ressources Azure](../powershell-azure-resource-manager.md).



## <a name="create-a-new-record-set-and-a-record"></a>Créer un nouveau jeu d’enregistrements et l’enregistrement

Pour créer un jeu à l’aide de PowerShell d’enregistrements, consultez [créer un DNS de jeux d’enregistrements et les enregistrements à l’aide de PowerShell](dns-getstarted-create-recordset.md).

## <a name="get-a-record-set"></a>Obtenir un jeu d’enregistrements

Pour récupérer un jeu d’enregistrements existant, utilisez `Get-AzureRmDnsRecordSet`. Spécifiez que l’ensemble de l’enregistrement de nom, le type d’enregistrement et la zone.

    $rs = Get-AzureRmDnsRecordSet –Name www –RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup

Comme avec `New-AzureRmDnsRecordSet`, le nom d’enregistrement doit être un nom relatif, c'est-à-dire qu’il doit exclure le nom de zone.

Vous pouvez spécifier la zone à l’aide du nom de la zone et le nom du groupe de ressources ou à l’aide d’un objet de zone :

    $zone = Get-AzureRmDnsZone -Name contoso.com -ResouceGroupName MyAzureResourceGroup
    $rs = Get-AzureRmDnsRecordSet -Name www –RecordType A -Zone $zone

`Get-AzureRmDnsRecordSet`Retourne un objet qui représente le jeu d’enregistrements qui a été créé dans Azure DNS local.

## <a name="list-record-sets"></a>Liste de jeux d’enregistrements

Vous pouvez également utiliser`Get-AzureRmDnsRecordSet` à la liste de jeux d’enregistrements si vous omettez la *– nom* et/ou *RecordType –* paramètres.

### <a name="to-list-all-record-sets"></a>Pour répertorier tous les jeux d’enregistrements

Cet exemple renvoie tous les jeux d’enregistrements, quel que soit le nom ou le type d’enregistrement :

    $list = Get-AzureRmDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup

### <a name="to-list-record-sets-of-a-given-record-type"></a>Pour les jeux d’enregistrements liste d’un type d’enregistrement donné

Cet exemple renvoie tous les jeux d’enregistrements qui correspondent au type d’enregistrement donné. Dans ce cas, l’enregistrement de la valeur qui est retournée est enregistrements « A » :

    $list = Get-AzureRmDnsRecordSet –RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup

La zone peut être spécifiée à l’aide de deux paramètres *ZoneName –* et *– ResourceGroupName* (comme illustré), ou en spécifiant un objet de zone :

    $zone = Get-AzureRmDnsZone -Name contoso.com -ResouceGroupName MyAzureResourceGroup
    $list = Get-AzureRmDnsRecordSet -Zone $zone

## <a name="add-a-record-to-a-record-set"></a>Ajouter un enregistrement à un jeu d’enregistrements

Ajouter des enregistrements à des jeux d’enregistrements à l’aide de la `Add-AzureRmDnsRecordConfig` applet de commande. Il s’agit d’une opération hors connexion. Seul l’objet local qui représente le jeu d’enregistrements est modifié.

Les paramètres pour ajouter des enregistrements à un jeu d’enregistrements varient en fonction du type du jeu d’enregistrements. Par exemple, lorsque vous utilisez un jeu d’enregistrements de type « A », vous ne pouvez spécifier les enregistrements avec le paramètre *-IPv4Address*.

Des enregistrements supplémentaires peuvent être ajoutés à chaque ensemble d’enregistrements en appels supplémentaires à `Add-AzureRmDnsRecordConfig`. Vous pouvez ajouter jusqu'à 20 enregistrements à n’importe quel jeu d’enregistrements. Toutefois, les jeux d’enregistrements de type « CNAME » peuvent contenir au plus un enregistrement et un jeu d’enregistrements ne peut pas contenir deux enregistrements identiques. Jeux d’enregistrements vides (avec zéro enregistrements) peuvent être créés, mais n’apparaissent pas sur les serveurs de noms DNS d’Azure.

Une fois le jeu d’enregistrements contient la collection souhaitée d’enregistrements, vous devez valider à l’aide de la `Set-AzureRmDnsRecordSet` applet de commande. Lorsqu’un jeu d’enregistrements a été validé, il remplace l’enregistrement existant défini dans Azure DNS.

### <a name="to-create-an-a-record-set-with-a-single-record"></a>Pour créer un enregistrement de jeu avec un enregistrement unique

    $rs = New-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address "1.2.3.4"
    Set-AzureRmDnsRecordSet -RecordSet $rs

La séquence des opérations pour créer un enregistrement peut également être *transmis*, ce qui signifie que de vous passez l’objet de jeu d’enregistrements en utilisant le canal, plutôt qu’en lui passant comme paramètre. Par exemple :

    New-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup | Add-AzureRmDnsRecordConfig -Ipv4Address "1.2.3.4" | Set-AzureRmDnsRecordSet

### <a name="additional-record-type-examples"></a>Exemples du type d’enregistrement supplémentaire

[AZURE.INCLUDE [dns-add-record-ps-include](../../includes/dns-add-record-ps-include.md)]

## <a name="modify-existing-record-sets"></a>Modifier les jeux d’enregistrements existants

La procédure de modification d’un jeu d’enregistrements existant est similaire pour les étapes à que suivre lors de la création d’enregistrements. La séquence des opérations est la suivante :

1.  Récupérer l’enregistrement existant à l’aide de la valeur `Get-AzureRmDnsRecordSet`.

2.  Modifier l’ensemble des enregistrements en soit ajouter des enregistrements, supprimer des enregistrements, modifier les paramètres d’enregistrement, ou l’enregistrement de la modification de la valeur temps live (TTL). Il s’agit d’une opération hors connexion. Seul l’objet local qui représente le jeu d’enregistrements est modifié.

3.  Valider les modifications apportées à l’aide de la `Set-AzureRmDnsRecordSet` applet de commande. Cela remplace l’enregistrement existant défini dans Azure DNS.


### <a name="to-update-a-record-in-an-existing-record-set"></a>Mettre à jour un enregistrement dans un jeu d’enregistrements existant

Dans cet exemple, nous avons modifier l’adresse IP d’un enregistrement « A » :

    $rs = Get-AzureRmDnsRecordSet -name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    $rs.Records[0].Ipv4Address = "134.170.185.46"
    Set-AzureRmDnsRecordSet -RecordSet $rs

Le `Set-AzureRmDnsRecordSet` applet de commande utilise l’etag vérifie pour s’assurer que les modifications simultanées ne sont pas remplacées. Utilisez le *-Remplacer* indicateur pour supprimer ces contrôles. Pour plus d’informations, voir [a propos des balises et des etags](dns-getstarted-create-dnszone.md#tagetag).

### <a name="to-modify-an-soa-record"></a>Pour modifier un enregistrement SOA

Vous ne pouvez pas ajouter ou supprimer des enregistrements à partir de l’enregistrement SOA créé automatiquement est défini au sommet de la zone (nom = "@"). Toutefois, vous pouvez modifier les paramètres dans l’enregistrement SOA (à l’exception de « hôte ») et l’enregistrement de définie la durée de vie.

L’exemple suivant montre comment modifier la propriété *Email* de l’enregistrement SOA :

    $rs = Get-AzureRmDnsRecordSet -Name "@" -RecordType SOA -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    $rs.Records[0].Email = "admin.contoso.com"
    Set-AzureRmDnsRecordSet -RecordSet $rs

### <a name="to-modify-ns-records-at-the-zone-apex"></a>Pour modifier les enregistrements NS au sommet de la zone

Vous ne pouvez pas ajouter, pour supprimer ou modifier les enregistrements créés automatiquement NS jeu d’enregistrements au sommet de la zone (nom = "@"). La seule modification autorisée est de modifier le jeu d’enregistrements de durée de vie.

L’exemple suivant montre comment modifier la propriété durée de vie du jeu d’enregistrements NS :

    $rs = Get-AzureRmDnsRecordSet -Name "@" -RecordType NS -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    $rs.Ttl = 300
    Set-AzureRmDnsRecordSet -RecordSet $rs

### <a name="to-add-records-to-an-existing-record-set"></a>Pour ajouter des enregistrements à un jeu d’enregistrements existant

Dans cet exemple, nous ajoutons deux enregistrements MX supplémentaires pour le jeu d’enregistrements existant :

    $rs = Get-AzureRmDnsRecordSet -name "test-mx" -RecordType MX -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Add-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail2.contoso.com" -Preference 10
    Add-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail3.contoso.com" -Preference 20
    Set-AzureRmDnsRecordSet -RecordSet $rs

## <a name="remove-a-record-from-an-existing-record-set"></a>Supprimer un enregistrement d’un jeu d’enregistrements existant

Enregistrements peuvent être supprimés à partir d’un enregistrement à l’aide de la valeur `Remove-AzureRmDnsRecordConfig`. L’enregistrement qui est supprimé doit être une correspondance exacte avec un enregistrement existant au sein de tous les paramètres. Modifications doivent être validées à l’aide de `Set-AzureRmDnsRecordSet`.

Supprimer le dernier enregistrement d’un jeu d’enregistrements ne supprime pas le jeu d’enregistrements. Pour plus d’informations, voir [suppression d’un jeu d’enregistrement](#delete-a-record-set) ci-dessous.


    $rs = Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Remove-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address "1.2.3.4"
    Set-AzureRmDnsRecordSet -RecordSet $rs

La séquence des opérations pour supprimer un enregistrement d’un jeu d’enregistrements peut également être transmise, c'est-à-dire que vous passez l’objet de jeu d’enregistrements en utilisant le canal, plutôt qu’en lui passant comme paramètre. Par exemple :

    Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsRecordConfig -Ipv4Address "1.2.3.4" | Set-AzureRmDnsRecordSet

### <a name="remove-an-aaaa-record-from-a-record-set"></a>Supprimer un enregistrement AAAA à partir d’un jeu d’enregistrements

    $rs = Get-AzureRmDnsRecordSet -Name "test-aaaa" -RecordType AAAA -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Remove-AzureRmDnsRecordConfig -RecordSet $rs -Ipv6Address "2607:f8b0:4009:1803::1005"
    Set-AzureRmDnsRecordSet -RecordSet $rs

### <a name="remove-a-cname-record-from-a-record-set"></a>Supprimer un enregistrement CNAME à partir d’un jeu d’enregistrements

Dans la mesure où un jeu d’enregistrements CNAME peut contenir au plus un enregistrement, la suppression de cet enregistrement laisse un jeu d’enregistrements vide.

    $rs =  Get-AzureRmDnsRecordSet -name "test-cname" -RecordType CNAME -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Remove-AzureRmDnsRecordConfig -RecordSet $rs -Cname "www.contoso.com"
    Set-AzureRmDnsRecordSet -RecordSet $rs

### <a name="remove-an-mx-record-from-a-record-set"></a>Supprimer un enregistrement MX à partir d’un jeu d’enregistrements

    $rs = Get-AzureRmDnsRecordSet -name "test-mx" -RecordType MX -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Remove-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail.contoso.com" -Preference 5
    Set-AzureRmDnsRecordSet -RecordSet $rs

### <a name="remove-an-ns-record-from-record-set"></a>Supprimer un enregistrement NS à partir du jeu d’enregistrements

    $rs = Get-AzureRmDnsRecordSet -Name "test-ns" -RecordType NS -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Remove-AzureRmDnsRecordConfig -RecordSet $rs -Nsdname "ns1.contoso.com"
    Set-AzureRmDnsRecordSet -RecordSet $rs

### <a name="remove-an-srv-record-from-a-record-set"></a>Supprimer un enregistrement SRV à partir d’un jeu d’enregistrements

    $rs = Get-AzureRmDnsRecordSet -Name "_sip._tls" -RecordType SRV -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Remove-AzureRmDnsRecordConfig -RecordSet $rs –Priority 0 –Weight 5 –Port 8080 –Target "sip.contoso.com"
    Set-AzureRmDnsRecordSet -RecordSet $rs

### <a name="remove-a-txt-record-from-a-record-set"></a>Supprimer un enregistrement TXT à partir d’un jeu d’enregistrements

    $rs = Get-AzureRmDnsRecordSet -Name "test-txt" -RecordType TXT -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Remove-AzureRmDnsRecordConfig -RecordSet $rs -Value "This is a TXT record"
    Set-AzureRmDnsRecordSet -RecordSet $rs

## <a name="delete-a-record-set"></a>Supprimer un jeu d’enregistrements

Jeux d’enregistrements peuvent être supprimés à l’aide de la `Remove-AzureRmDnsRecordSet` applet de commande. Vous ne pouvez pas supprimer l’enregistrement SOA et NS enregistrement définit au sommet de la zone (nom = "@") qui ont été créées automatiquement lors de la création de la zone. Ils seront automatiquement supprimés si la zone est supprimée.

Pour supprimer un jeu d’enregistrements, utilisez une des trois méthodes suivantes :

### <a name="specify-all-the-parameters-by-name"></a>Spécifiez tous les paramètres par nom

L’option *-Force* commutateur peut être utilisé pour supprimer l’invite de confirmation.

    Remove-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup [-Force]


### <a name="specify-the-record-set-by-name-and-type-and-specify-the-zone-by-object"></a>Spécifier le jeu d’enregistrements par nom et type et spécifier le fuseau par objet

    $zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
    Remove-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Zone $zone [-Force]

### <a name="specify-the-record-set-by-object"></a>Spécifier l’ensemble des enregistrements en objet

    $rs = Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Remove-AzureRmDnsRecordSet –RecordSet $rs [-Overwrite] [-Force]

Lorsque vous spécifiez l’enregistrement à définir à l’aide d’un objet, il permet d’etag vérifie que les modifications simultanées ne sont pas supprimées. L’option *-Remplacer* indicateur supprime ces contrôles. Pour plus d’informations, reportez-vous à la section [Etags et des balises](dns-getstarted-create-dnszone.md#tagetag) .

L’objet de jeu d’enregistrements peut également être rattachée au lieu du passé comme un paramètre :

    Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsRecordSet [-Overwrite] [-Force]

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le service DNS d’Azure, consultez [vue d’ensemble de DNS d’Azure](dns-overview.md). Pour plus d’informations sur l’automatisation de DNS, consultez [les zones DNS de création et enregistrement définit à l’aide du Kit de développement .NET](dns-sdk.md).

Pour plus d’informations sur les enregistrements DNS inversées, voir [comment gérer les enregistrements DNS inverses pour vos services à l’aide de PowerShell](dns-reverse-dns-record-operations-ps.md).
