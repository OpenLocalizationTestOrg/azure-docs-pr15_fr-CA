<properties
   pageTitle="Créer une zone DNS à l’aide de la CLI | Microsoft Azure"
   description="Apprenez à créer des zones DNS pour Azure DNS étape par étape pour héberger votre domaine DNS à l’aide de la CLI"
   services="dns"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/16/2016"
   ms.author="sewhee"/>

# <a name="create-an-azure-dns-zone-using-cli"></a>Créer une zone DNS d’Azure à l’aide de la CLI


> [AZURE.SELECTOR]
- [Azure Portal](dns-getstarted-create-dnszone-portal.md)
- [PowerShell](dns-getstarted-create-dnszone.md)
- [CLI Azure](dns-getstarted-create-dnszone-cli.md)


Cet article vous guidera tout au long de la procédure de création d’une zone DNS en utilisant l’infrastructure du langage commun. Vous pouvez également créer une zone DNS à l’aide de PowerShell ou l’Azure portal.

[AZURE.INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]


## <a name="before-you-begin"></a>Avant de commencer

Ces instructions utilisent Microsoft Azure CLI. Veillez à mettre à jour vers la dernière CLI Azure (0.9.8 ou version ultérieure) pour utiliser les commandes de DNS d’Azure. Type de `azure -v` pour vérifier la version de l’infrastructure du langage commun Azure est actuellement installée sur votre ordinateur.

## <a name="step-1---set-up-azure-cli"></a>Étape 1 : configurer Azure CLI

### <a name="1-install-azure-cli"></a>1. Installez l’interface CLI Azure

Vous pouvez installer Azure CLI pour Windows, Linux ou Mac. Les étapes suivantes doivent être effectuées avant de pouvoir gérer DNS Azure à l’aide de la CLI d’Azure. Informations supplémentaires sont disponibles à [installer l’interface CLI d’Azure](../xplat-cli-install.md). Les commandes DNS nécessitent Azure CLI version 0.9.8 ou une version ultérieure.

Toutes les commandes de fournisseur de réseau sur l’infrastructure du langage commun peuvent être trouvé à l’aide de la commande suivante :

    azure network

### <a name="2-switch-cli-mode"></a>2. mode d’ILC de commutateur

DNS Azure utilise le Gestionnaire de ressources Azure. Vérifiez que vous changez de mode d’infrastructure du langage commun pour utiliser les commandes ARM.

    azure config mode arm

### <a name="3-sign-in-to-your-azure-account"></a>3. Connectez-vous à votre compte Azure

Vous devez authentifier vos informations d’identification. Gardez à l’esprit que vous ne pouvez utiliser que des comptes ORGID.

    azure login -u "username"

### <a name="4-select-the-subscription"></a>4. Sélectionnez l’abonnement

Choisir vos abonnements Azure à utiliser.

    azure account set "subscription name"

### <a name="5-create-a-resource-group"></a>5. créer un groupe de ressources

Le Gestionnaire de ressources Azure requiert que tous les groupes de ressources spécifient un emplacement. Il est utilisé comme emplacement par défaut pour les ressources dans ce groupe de ressources. Toutefois, étant donné que toutes les ressources DNS sont globales et régionales pas, le choix de l’emplacement de groupe de ressources n’a aucun impact sur Azure DNS.

Vous pouvez ignorer cette étape si vous utilisez un groupe de ressources existant.

    azure group create -n myresourcegroup --location "West US"


### <a name="6-register"></a>6. le Registre

Le service DNS d’Azure est géré par le fournisseur de ressources Microsoft.Network. Votre abonnement Azure doit être enregistré pour utiliser ce fournisseur de ressources avant de pouvoir utiliser DNS d’Azure. Il s’agit d’une opération unique pour chaque abonnement.

    azure provider register --namespace Microsoft.Network


## <a name="step-2---create-a-dns-zone"></a>Étape 2 : créer une zone DNS

Une zone DNS est créée à l’aide de la `azure network dns zone create` commande. Vous pouvez également créer une zone DNS avec balises. Les balises sont une liste de paires nom-valeur et servent par le Gestionnaire de ressources Azure aux ressources de l’étiquette à des fins de facturation ou de regroupement. Pour plus d’informations sur les balises, voir [en utilisant des balises pour organiser vos ressources Azure](../resource-group-using-tags.md).

Dans Azure, DNS, les noms de zones doivent être spécifiés sans un arrêt **'.'**. Par exemple, en tant que «**contoso.com**» plutôt que «**contoso.com.**'.


### <a name="to-create-a-dns-zone"></a>Pour créer une zone DNS

L’exemple ci-dessous crée une zone DNS nommée *contoso.com* dans le groupe de ressources appelé *MyResourceGroup*.

L’exemple permet de créer votre zone DNS, en substituant les valeurs de votre propre.

    azure network dns zone create myresourcegroup contoso.com

### <a name="to-create-a-dns-zone-and-tags"></a>Pour créer une zone DNS et des balises.

Azure DNS CLI prend en charge les balises des zones DNS spécifiés à l’aide de l’option *-balise* paramètre. L’exemple suivant montre comment créer une zone DNS avec les deux balises, projet = demo et env = test.

L’exemple ci-dessous permet de créer une zone DNS et balises, en substituant les valeurs de votre propre.

    azure network dns zone create myresourcegroup contoso.com -t "project=demo";"env=test"

## <a name="view-records"></a>Afficher les enregistrements

Création d’une zone DNS crée également les enregistrements DNS suivants :

- L’enregistrement « De SOA » (). Il est présent à la racine de chaque zone DNS.

- Les enregistrements de serveur (NS) de noms faisant autorité. Ces affichent les serveurs de noms hébergent la zone. DNS Azure utilise un pool de serveurs de noms, et par conséquent, différents serveurs de nom peuvent être affectés à différentes zones dans Azure DNS. Pour plus d’informations, reportez-vous à la section [délégué un domaine DNS d’Azure](dns-domain-delegation.md) .

Pour afficher ces enregistrements, utilisez `azure network dns-record-set show`.<BR>
*Utilisation : show de réseau dns le jeu d’enregistrements <-groupe de ressources >< nom de la zone dns > <name><type>*


Dans l’exemple ci-dessous, si vous exécutez la commande avec le groupe de ressources *myresourcegroup*, jeu d’enregistrements nom *"@"* (pour un enregistrement de racine) et tapez *SOA*, il donnera le résultat suivant :


    azure network dns record-set show myresourcegroup "contoso.com" "@" SOA
    info:    Executing command network dns-record-set show
    + Looking up the DNS record set "@"
    data:    Id                              : /subscriptions/#######################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/SOA/@
    data:    Name                            : @
    data:    Type                            : Microsoft.Network/dnszones/SOA
    data:    Location                        : global
    data:    TTL                             : 3600
    data:    SOA record:
    data:      Email                         : msnhst.microsoft.com
    data:      Expire time                   : 604800
    data:      Host                          : edge1.azuredns-cloud.net
    data:      Minimum TTL                   : 300
    data:      Refresh time                  : 900
    data:      Retry time                    : 300
    data:                                    :
<BR>
Pour afficher les enregistrements NS créés à l’aide de la zone, utilisez la commande suivante :

    azure network dns record-set show myresourcegroup "contoso.com" "@" NS
    info:    Executing command network dns-record-set show
    + Looking up the DNS record set "@"
    data:    Id                              : /subscriptions/#######################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/NS/@
    data:    Name                            : @
    data:    Type                            : Microsoft.Network/dnszones/NS
    data:    Location                        : global
    data:    TTL                             : 3600
    data:    NS records
    data:        Name server domain name     : ns1-05.azure-dns.com
    data:        Name server domain name     : ns2-05.azure-dns.net
    data:        Name server domain name     : ns3-05.azure-dns.org
    data:        Name server domain name     : ns4-05.azure-dns.info
    data:
    info:    network dns-record-set show command OK

>[AZURE.NOTE] Enregistrement définit au niveau de la racine (ou le *sommet*) d’une utilisation de la Zone DNS **@** comme nom de l’enregistrement.

## <a name="test"></a>Test

Vous pouvez tester votre zone DNS à l’aide des outils DNS, tels que nslookup, DIG, ou de la `Resolve-DnsName` applet de commande PowerShell.

Si vous n’avez pas encore reçu votre domaine pour utiliser la nouvelle zone dans Azure DNS, vous devez diriger la requête DNS directement à l’un des serveurs de noms de votre zone. Les serveurs de noms de votre zone figurent dans les enregistrements NS, comme indiqué par « show du jeu d’enregistrements dns réseau azure » ci-dessus. Assurez-vous que le remplaçant les valeurs correctes pour votre zone dans la commande ci-dessous.

L’exemple suivant utilise DIG pour interroger le domaine contoso.com avec les serveurs de noms attribués pour la zone DNS. La requête doit pointer vers un serveur de noms pour lequel nous avons utilisé * @ * et le nom de zone à l’aide de col.

     <<>> DiG 9.10.2-P2 <<>> @ns1-05.azure-dns.com contoso.com
    (1 server found)
    global options: +cmd
    Got answer:
    ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 60963
    flags: qr aa rd; QUERY: 1, ANSWER: 0, AUTHORITY: 1, ADDITIONAL: 1
    WARNING: recursion requested but not available

    OPT PSEUDOSECTION:
    EDNS: version: 0, flags:; udp: 4000
    QUESTION SECTION:
    contoso.com.                        IN      A

    AUTHORITY SECTION:
    contoso.com.         300     IN      SOA     edge1.azuredns-cloud.net.
    msnhst.microsoft.com. 6 900 300 604800 300

    Query time: 93 msec
    SERVER: 208.76.47.5#53(208.76.47.5)
    WHEN: Tue Jul 21 16:04:51 Pacific Daylight Time 2015
    MSG SIZE  rcvd: 120

## <a name="next-steps"></a>Étapes suivantes

Après avoir créé une zone DNS, créer des [ensembles d’enregistrements et d’enregistrements](dns-getstarted-create-recordset-cli.md) pour démarrer la résolution des noms de votre domaine Internet.
