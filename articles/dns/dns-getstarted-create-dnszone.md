<properties
   pageTitle="Mise en route de DNS d’Azure | Microsoft Azure"
   description="Apprenez à créer des zones DNS pour Azure DNS. Il s’agit d’une étape par étape pour obtenir votre première zone DNS créé pour héberger votre domaine DNS à l’aide de PowerShell."
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

# <a name="create-a-dns-zone-using-powershell"></a>Créer une zone DNS à l’aide de Powershell

> [AZURE.SELECTOR]
- [Azure Portal](dns-getstarted-create-dnszone-portal.md)
- [PowerShell](dns-getstarted-create-dnszone.md)
- [CLI Azure](dns-getstarted-create-dnszone-cli.md)

Cet article vous guidera tout au long de la procédure de création d’une zone DNS à l’aide de PowerShell. Vous pouvez également créer une zone DNS à l’aide de la CLI ou le portail Azure.

[AZURE.INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="tagetag"></a>À propos des balises et des Etags

### <a name="etags"></a>ETags

Supposons que deux personnes ou deux processus essayez de modifier un enregistrement DNS en même temps. Lequel gagne ? Et le gagnant sait-il qu’ils avez simplement remplacé les modifications créées par quelqu'un d’autre ?

DNS Azure utilise des Etags pour gérer en toute sécurité les modifications simultanées à la même ressource. Chaque ressource DNS (zone ou jeu d’enregistrements) dispose d’un Etag qui lui est associé. Chaque fois qu’une ressource est extraite, son Etag est également récupérée. Lors de la mise à jour d’une ressource, vous avez la possibilité de repasser l’Etag qui Azure DNS permet de vérifier que l’Etag sur les correspondances de serveur. Étant donné que chaque mise à jour d’une ressource entraîne l’Etag en cours de régénération, une incompatibilité Etag indique une modification simultanée s’est produite. ETags sont également utilisés lors de la création d’une nouvelle ressource pour vous assurer que la ressource n’existe pas déjà.

Par défaut, Azure DNS PowerShell utilise les Etags pour bloquer les modifications simultanées apportées aux zones et jeux d’enregistrements. L’option *-Remplacer* commutateur peut être utilisé pour supprimer les contrôles Etag, auquel cas toutes les modifications simultanées apportées seront remplacées.

Au niveau de l’API REST de DNS Azure, les Etags sont spécifiés à l’aide d’en-têtes HTTP.  Leur comportement est donnée dans le tableau suivant :

|En-tête|Comportement|
|------|--------|
|Aucun|PUT réussit toujours (aucune vérification Etag)|
|If-match.<etag>|PUT ne réussit que si la ressource existe et Etag correspond à|
|If-match *     | PUT ne réussit que si la ressource existe|
|If-none-match * |  PUT ne réussit que si la ressource n’existe pas|

### <a name="tags"></a>Balises

Les balises sont différents des Etags. Les balises sont une liste de paires nom-valeur et servent par le Gestionnaire de ressources Azure aux ressources de l’étiquette à des fins de facturation ou de regroupement. Pour plus d’informations sur les balises, voir [en utilisant des balises pour organiser vos ressources Azure](../resource-group-using-tags.md).

Azure DNS PowerShell prend en charge les balises sur les zones et les jeux d’enregistrements spécifiés à l’aide des options `-Tag` paramètre.


## <a name="before-you-begin"></a>Avant de commencer

Vérifiez que vous disposez des éléments suivants avant de commencer la configuration.

- Un abonnement Azure. Si vous ne disposez pas d’un abonnement Azure, vous pouvez activer vos [avantages d’abonné MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou le signe d’un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/).

- Vous devez installer la dernière version de cmdlets PowerShell de gestionnaire de ressources Azure (1.0 ou ultérieurs). Pour plus d’informations sur l’installation des applets de commande PowerShell, voir [Comment faire pour installer et configurer Azure PowerShell](../powershell-install-configure.md) .

## <a name="step-1---sign-in"></a>Étape 1 : ouverture de session

Ouvrez la console PowerShell et vous connecter à votre compte. Pour plus d’informations, reportez-vous [à l’aide de Windows PowerShell avec le Gestionnaire de ressources](../powershell-azure-resource-manager.md).

Pour vous aider à vous connecter, utilisez l’exemple suivant :

    Login-AzureRmAccount

Vérifiez les abonnements pour le compte.

    Get-AzureRmSubscription

Spécifier l’abonnement que vous souhaitez utiliser.

    Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

## <a name="step-2---create-a-resource-group"></a>Étape 2 - créer un groupe de ressources

Le Gestionnaire de ressources Azure requiert que tous les groupes de ressources spécifient un emplacement. Il est utilisé comme emplacement par défaut pour les ressources dans ce groupe de ressources. Toutefois, étant donné que toutes les ressources DNS sont globales et régionales pas, le choix de l’emplacement de groupe de ressources n’a aucun impact sur Azure DNS.

Vous pouvez ignorer cette étape si vous utilisez un groupe de ressources existant.

    New-AzureRmResourceGroup -Name MyAzureResourceGroup -location "West US"


## <a name="step-3---register"></a>Étape 3 - Registre

Le service DNS d’Azure est géré par le fournisseur de ressources Microsoft.Network. Votre abonnement Azure doit être enregistré pour utiliser ce fournisseur de ressources avant de pouvoir utiliser DNS d’Azure. Il s’agit d’une opération unique pour chaque abonnement.

    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network


## <a name="step-4----create-a-dns-zone"></a>Étape 4 - créer une zone DNS

Une zone DNS est créée à l’aide de la `New-AzureRmDnsZone` applet de commande. Il existe des exemples ci-dessous pour créer une zone DNS avec ou sans balises. Pour plus d’informations sur les balises, consultez la section sur les [balises](#tags) dans cet article.

>[AZURE.NOTE] Dans Azure, DNS, les noms de zones doivent être spécifiés sans un arrêt **'.'**. Par exemple, en tant que «**contoso.com**» plutôt que «**contoso.com.**'.

### <a name="to-create-a-dns-zone"></a>Pour créer une zone DNS

L’exemple ci-dessous crée une zone DNS nommée *contoso.com* dans le groupe de ressources appelé *MyResourceGroup*. L’exemple permet de créer une zone DNS, en substituant les valeurs de votre propre.

    New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup

### <a name="to-create-a-dns-zone-with-tags"></a>Pour créer une zone DNS avec des balises

L’exemple suivant montre comment créer une zone DNS avec les deux balises, *projet = demo* et *env = test*. L’exemple permet de créer une zone DNS, en substituant les valeurs de votre propre.

    New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup -Tag @( @{ Name="project"; Value="demo" }, @{ Name="env"; Value="test" } )

## <a name="view-records"></a>Afficher les enregistrements

Création d’une zone DNS crée également les enregistrements DNS suivants :

- L’enregistrement *Début d’autorité* (principale SOA). Il est présent à la racine de chaque zone DNS.

- Les enregistrements de serveur (NS) de noms faisant autorité. Ces affichent les serveurs de noms hébergent la zone. DNS Azure utilise un pool de serveurs de noms, et par conséquent, différents serveurs de nom peuvent être affectés à différentes zones dans Azure DNS. Voir [déléguer un domaine DNS d’Azure](dns-domain-delegation.md) pour plus d’informations.

Pour afficher ces enregistrements, utilisez `Get-AzureRmDnsRecordSet`:

    Get-AzureRmDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup

    Name              : @
    ZoneName          : contoso.com
    ResourceGroupName : MyResourceGroup
    Ttl               : 3600
    Etag              : 2b855de1-5c7e-4038-bfff-3a9e55b49caf
    RecordType        : SOA
    Records           : {[ns1-01.azure-dns.com,msnhst.microsoft.com,900,300,604800,300]}
    Tags              : {}

    Name              : @
    ZoneName          : contoso.com
    ResourceGroupName : MyResourceGroup
    Ttl               : 3600
    Etag              : 5fe92e48-cc76-4912-a78c-7652d362ca18
    RecordType        : NS
    Records           : {ns1-01.azure-dns.com, ns2-01.azure-dns.net, ns3-01.azure-dns.org,
                  ns4-01.azure-dns.info}
    Tags              : {}


Enregistrement définit au niveau de la racine (ou le *sommet*) d’une utilisation de la Zone DNS **@** comme nom de l’enregistrement.


## <a name="test"></a>Test

Vous pouvez tester votre zone DNS à l’aide d’outils DNS comme nslookup, dig ou l' [applet de commande Resolve-NomDNS PowerShell](https://technet.microsoft.com/library/jj590781.aspx).

Si vous n’avez pas encore reçu votre domaine pour utiliser la nouvelle zone dans Azure DNS, vous devez diriger la requête DNS directement à l’un des serveurs de noms de votre zone. Les serveurs de noms de votre zone figurent dans les enregistrements NS, par `Get-AzureRmDnsRecordSet` ci-dessus. Assurez-vous que le remplaçant les valeurs correctes pour votre zone dans la commande ci-dessous.

    nslookup
    > set type=SOA
    > server ns1-01.azure-dns.com
    > contoso.com

    Server: ns1-01.azure-dns.com
    Address:  208.76.47.1

    contoso.com
            primary name server = ns1-01.azure-dns.com
            responsible mail addr = msnhst.microsoft.com
            serial  = 1
            refresh = 900 (15 mins)
            retry   = 300 (5 mins)
            expire  = 604800 (7 days)
            default TTL = 300 (5 mins)


## <a name="next-steps"></a>Étapes suivantes

Après avoir créé une zone DNS, créer des [ensembles d’enregistrements et d’enregistrements](dns-getstarted-create-recordset.md) pour démarrer la résolution des noms de votre domaine Internet.

