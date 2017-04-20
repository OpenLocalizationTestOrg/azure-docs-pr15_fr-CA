<properties
   pageTitle="Gérer des jeux d’enregistrements DNS et enregistrements DNS Azure à l’aide de la CLI d’Azure | Microsoft Azure"
   description="Gestion des jeux d’enregistrements DNS et enregistrements sur Azure DNS lors de l’hébergement de votre domaine DNS d’Azure. Toutes les commandes CLI pour les opérations sur les jeux d’enregistrements et d’enregistrements."
   services="dns"
   documentationCenter="na"
   authors="jtuliani"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/22/2016"
   ms.author="jtuliani"/>

# <a name="manage-dns-records-and-record-sets-by-using-cli"></a>Gérer les enregistrements DNS et les jeux d’enregistrements à l’aide de CLI


> [AZURE.SELECTOR]
- [Azure Portal](dns-operations-recordsets-portal.md)
- [CLI Azure](dns-operations-recordsets-cli.md)
- [PowerShell](dns-operations-recordsets.md)


Cet article vous montre comment gérer des jeux d’enregistrements et d’enregistrements pour votre zone DNS à l’aide de l’interface de ligne de commande de Azure multiplates-formes (CLI).

Il est important de comprendre la différence entre les jeux d’enregistrements DNS et des enregistrements DNS. Un jeu d’enregistrements est un ensemble d’enregistrements dans une zone qui portent le même nom et qui sont du même type. Pour plus d’informations, voir [enregistrements et jeux d’enregistrements](dns-getstarted-create-recordset-cli.md).


## <a name="configure-the-cross-platform-azure-cli"></a>Configurer la CLI d’Azure multiplates-formes

Azure DNS est un service de gestionnaire de ressources uniquement Azure. Il ne dispose pas d’une API de gestion de Service Azure. Assurez-vous que l’infrastructure du langage commun Azure est configuré pour utiliser le mode de gestionnaire de ressources, à l’aide de la `azure config mode arm` commande.

Si vous voyez **erreur : « dns » n’est pas une commande azure**, il est plus probable, car vous utilisez Azure CLI en mode de gestion des services Azure, pas en mode de gestionnaire de ressources.

## <a name="create-a-new-record-set-and-record"></a>Créer un nouveau jeu d’enregistrements et l’enregistrement

Pour créer un nouvel enregistrement à définir dans le portail Azure, consultez [créer un jeu d’enregistrements et des enregistrements](dns-getstarted-create-recordset-cli.md).


## <a name="retrieve-a-record-set"></a>Récupérer un jeu d’enregistrements

Pour récupérer un jeu d’enregistrements existant, utilisez `azure network dns record-set show`. Spécifier le groupe de ressources, le nom de zone, jeu d’enregistrements nom relatif et le type d’enregistrement. Utilisez l’exemple ci-dessous, en remplaçant les valeurs avec vos propres.

    azure network dns record-set show myresourcegroup contoso.com www A


## <a name="list-record-sets"></a>Liste de jeux d’enregistrements

Vous pouvez répertorier tous les enregistrements dans une zone DNS à l’aide de la `azure network dns record-set list` commande. Vous devez spécifier le nom du groupe de ressources et le nom de la zone.

### <a name="to-list-all-record-sets"></a>Pour répertorier tous les jeux d’enregistrements

Cet exemple renvoie tous les jeux d’enregistrements, quel que soit le nom ou le type d’enregistrement :

    azure network dns record-set list myresourcegroup contoso.com

### <a name="to-list-record-sets-of-a-given-type"></a>Pour les jeux d’enregistrements liste d’un type donné

Cet exemple renvoie tous les jeux d’enregistrements qui correspondent au type d’enregistrement donné (dans ce cas, les enregistrements « A ») :

    azure network dns record-set list myresourcegroup contoso.com A


## <a name="add-a-record-to-a-record-set"></a>Ajouter un enregistrement à un jeu d’enregistrements

Ajouter des enregistrements à des jeux d’enregistrements à l’aide de la `azure network dns record-set add-record`commande. Les paramètres pour ajouter des enregistrements à un jeu d’enregistrements varient en fonction du type de l’enregistrement qui est en cours de définition. Par exemple, lorsque vous utilisez un jeu d’enregistrements de type « A », vous ne pouvez spécifier les enregistrements avec le paramètre `-a <IPv4 address>`.

Pour créer un jeu d’enregistrements, utilisez les `azure network dns record-set create`commande. Spécifier le groupe de ressources, le nom de zone, jeu d’enregistrements nom relatif, type d’enregistrement et durée de vie (TTL). Si le `--ttl` paramètre n’est pas définie, la valeur (en secondes) par défaut à quatre.

    azure network dns record-set create myresourcegroup  contoso.com "test-a"  A --ttl 300


Après avoir créé le jeu d’enregistrements « A », ajoutez l’adresse IPv4 à l’aide de la `azure network dns record-set add-record`commande.

    azure network dns record-set add-record myresourcegroup contoso.com "test-a" A -a 192.168.1.1


Les exemples suivants montrent comment créer un jeu d’enregistrements de chaque type d’enregistrement. Chaque jeu d’enregistrements contient un seul enregistrement.

[AZURE.INCLUDE [dns-add-record-cli-include](../../includes/dns-add-record-cli-include.md)]


## <a name="update-a-record-in-a-record-set"></a>Mettre à jour un enregistrement dans un jeu d’enregistrements

### <a name="to-add-another-ip-address-1234-to-an-existing-a-record-set-www"></a>Pour ajouter une autre adresse IP (1.2.3.4) pour un enregistrement « A » existant défini (« www ») :

    azure network dns record-set add-record  myresourcegroup contoso.com  A
    -a 1.2.3.4
    info:    Executing command network dns record-set add-record
    Record set name: www
    + Looking up the dns zone "contoso.com"
    + Looking up the DNS record set "www"
    + Updating DNS record set "www"
    data:    Id                              : /subscriptions/################################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/a/www
    data:    Name                            : www
    data:    Type                            : Microsoft.Network/dnszones/a
    data:    Location                        : global
    data:    TTL                             : 4
    data:    A records:
    data:        IPv4 address                : 192.168.1.1
    data:        IPv4 address                : 1.2.3.4
    data:
    info:    network dns record-set add-record command OK

### <a name="to-remove-an-existing-value-from-a-record-set"></a>Pour supprimer une valeur existante à partir d’un jeu d’enregistrements
Utilisez `azure network dns record-set delete-record`.

    azure network dns record-set delete-record myresourcegroup contoso.com www A -a 1.2.3.4
    info:    Executing command network dns record-set delete-record
    + Looking up the DNS record set "www"
    Delete DNS record? [y/n] y
    + Updating DNS record set "www"
    data:    Id                              : /subscriptions/################################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/A/www
    data:    Name                            : www
    data:    Type                            : Microsoft.Network/dnszones/A
    data:    Location                        : global
    data:    TTL                             : 4
    data:    A records:
    data:    IPv4 address                    : 192.168.1.1
    data:
    info:    network dns record-set delete-record command OK



## <a name="remove-a-record-from-a-record-set"></a>Supprimer un enregistrement d’un jeu d’enregistrements

Enregistrements peuvent être supprimés à partir d’un enregistrement à l’aide de la valeur `azure network dns record-set delete-record`. L’enregistrement qui est supprimé doit être une correspondance exacte avec un enregistrement existant au sein de tous les paramètres.

Supprimer le dernier enregistrement d’un jeu d’enregistrements ne supprime pas le jeu d’enregistrements. Pour plus d’informations, reportez-vous à la section de cet article appelée [Supprimer un jeu d’enregistrements](#delete).

    azure network dns record-set delete-record myresourcegroup contoso.com www A -a 192.168.1.1

    azure network dns record-set delete myresourcegroup contoso.com www A

### <a name="remove-an-aaaa-record-from-a-record-set"></a>Supprimer un enregistrement AAAA à partir d’un jeu d’enregistrements

    azure network dns record-set delete-record myresourcegroup contoso.com test-aaaa  AAAA -b "2607:f8b0:4009:1803::1005"

### <a name="remove-a-cname-record-from-a-record-set"></a>Supprimer un enregistrement CNAME à partir d’un jeu d’enregistrements

    azure network dns record-set delete-record myresourcegroup contoso.com test-cname CNAME -c www.contoso.com


### <a name="remove-an-mx-record-from-a-record-set"></a>Supprimer un enregistrement MX à partir d’un jeu d’enregistrements

    azure network dns record-set delete-record myresourcegroup contoso.com "@" MX -e "mail.contoso.com" -f 5

### <a name="remove-an-ns-record-from-record-set"></a>Supprimer un enregistrement NS à partir du jeu d’enregistrements

    azure network dns record-set delete-record myresourcegroup contoso.com  "test-ns" NS -d "ns1.contoso.com"

### <a name="remove-a-ptr-record-from-a-record-set"></a>Supprimer un enregistrement PTR à partir d’un jeu d’enregistrements
Dans ce cas « mon-arpa-zone.com » représente la zone ARPA représentant votre plage d’adresses IP.  Chaque enregistrement PTR dans cette zone correspond à une adresse IP comprise dans cette plage IP.

    azure network dns record-set delete-record myresourcegroup my-arpa-zone.com "10" PTR -P "myservice.contoso.com"

### <a name="remove-an-srv-record-from-a-record-set"></a>Supprimer un enregistrement SRV à partir d’un jeu d’enregistrements

    azure network dns record-set delete-record myresourcegroup contoso.com  "_sip._tls" SRV -p 0 -w 5 -o 8080 -u "sip.contoso.com"

### <a name="remove-a-txt-record-from-a-record-set"></a>Supprimer un enregistrement TXT à partir d’un jeu d’enregistrements

    azure network dns record-set delete-record myresourcegroup contoso.com  "test-TXT" TXT -x "this is a TXT record"

## <a name="delete"></a>Supprimer un jeu d’enregistrements

Jeux d’enregistrements peuvent être supprimés à l’aide de la `Remove-AzureRmDnsRecordSet` applet de commande. Vous ne pouvez pas supprimer l’enregistrement SOA et NS enregistrement définit au sommet de la zone (nom = "@") qui ont été créées automatiquement lors de la création de la zone. Ils seront automatiquement supprimés si la zone est supprimée.

Dans l’exemple suivant, l’enregistrement « A », définissez « test-a » est supprimé à partir de la zone DNS « contoso.com » :

    azure network dns record-set delete myresourcegroup contoso.com  "test-a" A

Le commutateur *-q* peut être utilisé pour supprimer l’invite de confirmation.


## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le service DNS d’Azure, consultez [vue d’ensemble de DNS d’Azure](dns-overview.md). Pour plus d’informations sur l’automatisation de DNS, consultez [les zones DNS de création et enregistrement définit à l’aide du Kit de développement .NET](dns-sdk.md).

Si vous souhaitez travailler avec des enregistrements DNS inversées, voir [comment gérer les enregistrements DNS inverses pour vos services à l’aide de la CLI d’Azure](dns-reverse-dns-record-operations-cli.md).
