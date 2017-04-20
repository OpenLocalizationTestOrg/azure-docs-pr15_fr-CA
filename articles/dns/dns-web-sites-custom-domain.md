<properties
   pageTitle="Créer des enregistrements DNS personnalisées pour une application web | Microsoft Azure  "
   description="Comment créer des enregistrements DNS pour l’application web à l’aide d’Azure DNS domaine personnalisé."
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

# <a name="create-dns-records-for-a-web-app-in-a-custom-domain"></a>Créer des enregistrements DNS pour une application web dans un domaine personnalisé

Vous pouvez utiliser DNS d’Azure pour héberger un domaine personnalisé pour vos applications web. Par exemple, vous créez une application web Azure et vous souhaitez que les utilisateurs y accéder par une ou l’autre à l’aide de contoso.com, ou www.contoso.com sous la forme d’un nom de domaine complet.

Pour ce faire, vous devez créer deux enregistrements :

- Un enregistrement « A » de racine pointant vers contoso.com
- Un enregistrement de « CNAME » pour le nom de www qui pointe vers l’enregistrement A

Gardez à l’esprit que si vous créez un enregistrement A pour une application web dans Azure, l’enregistrement A doit être manuellement mis à jour si le sous-jacent adresse IP pour que les modifications d’application web.

## <a name="before-you-begin"></a>Avant de commencer

Avant de commencer, vous devez tout d’abord créer une zone DNS dans le système DNS d’Azure et déléguez la zone dans votre registraire DNS d’Azure.

1. Pour créer une zone DNS, suivez les étapes dans [créer une zone DNS](dns-getstarted-create-dnszone.md).
2. Pour déléguer votre DNS au serveur DNS d’Azure, suivez les étapes de [la délégation de domaine DNS](dns-domain-delegation.md).

Après la création d’une zone et délégation à Azure DNS, vous pouvez ensuite créer des enregistrements de votre domaine personnalisé.


## <a name="1-create-an-a-record-for-your-custom-domain"></a>1. Créez un enregistrement A pour votre domaine personnalisé

Un enregistrement A est utilisé pour mapper un nom sur son adresse IP. Dans l’exemple suivant, nous allons désigner @ sous la forme d’un enregistrement de pour une adresse IPv4 :

### <a name="step-1"></a>Étape 1

Créer un enregistrement A et l’assigner à une variable $rs

    $rs= New-AzureRMDnsRecordSet -Name "@" -RecordType "A" -ZoneName "contoso.com" -ResourceGroupName "MyAzureResourceGroup" -Ttl 600

### <a name="step-2"></a>Étape 2

Ajouter la valeur IPv4 pour le jeu d’enregistrements créé précédemment "@" à l’aide de la variable $rs affectée. La valeur de IPv4 affectée sera l’adresse IP de votre application web.

Pour rechercher l’adresse IP pour une application web, suivez les étapes de [configuration d’un nom de domaine personnalisé dans le Service d’application Azure](../web-sites-custom-domain-name.md#Find-the-virtual-IP-address).

    Add-AzureRMDnsRecordConfig -RecordSet $rs -Ipv4Address <your web app IP address>

### <a name="step-3"></a>Étape 3

Valider les modifications dans le jeu d’enregistrements. Utilisez `Set-AzureRMDnsRecordSet` pour télécharger les modifications vers l’enregistrement DNS d’Azure :

    Set-AzureRMDnsRecordSet -RecordSet $rs

## <a name="2-create-a-cname-record-for-your-custom-domain"></a>2. créer un enregistrement CNAME pour votre domaine personnalisé

Si votre domaine est déjà géré par Azure DNS (voir [délégation de domaine DNS](dns-domain-delegation.md), vous pouvez utiliser ce qui suit l’exemple pour créer un enregistrement CNAME pour contoso.azurewebsites.net.

### <a name="step-1"></a>Étape 1

Ouvrir PowerShell et créer un nouveau jeu d’enregistrements CNAME et affecter à la variable $rs. Cet exemple crée un type de jeu d’enregistrements CNAME avec une « durée de vie » de 600 secondes dans la zone DNS nommée « contoso.com ».

    $rs = New-AzureRMDnsRecordSet -ZoneName contoso.com -ResourceGroupName myresourcegroup -Name "www" -RecordType "CNAME" -Ttl 600

    Name              : www
    ZoneName          : contoso.com
    ResourceGroupName : myresourcegroup
    Ttl               : 600
    Etag              : 8baceeb9-4c2c-4608-a22c-229923ee1856
    RecordType        : CNAME
    Records           : {}
    Tags              : {}


### <a name="step-2"></a>Étape 2

Une fois le jeu d’enregistrements CNAME créé, vous devez créer une valeur d’alias qui pointera vers l’application web.

À l’aide de la variable précédemment assignée « $rs » vous pouvez utiliser la commande PowerShell ci-dessous pour créer l’alias pour la contoso.azurewebsites.net d’application web.

    Add-AzureRMDnsRecordConfig -RecordSet $rs -Cname "contoso.azurewebsites.net"

    Name              : www
    ZoneName          : contoso.com
    ResourceGroupName : myresourcegroup
    Ttl               : 600
    Etag              : 8baceeb9-4c2c-4608-a22c-229923ee185
    RecordType        : CNAME
    Records           : {contoso.azurewebsites.net}
    Tags              : {}

### <a name="step-3"></a>Étape 3

Valider les modifications apportées à l’aide de la `Set-AzureRMDnsRecordSet` applet de commande :

    Set-AzureRMDnsRecordSet -RecordSet $rs

Vous pouvez valider l’enregistrement a été créé correctement en interrogeant le « www.contoso.com » à l’aide de nslookup, comme indiqué ci-dessous :

    PS C:\> nslookup
    Default Server:  Default
    Address:  192.168.0.1

    > www.contoso.com
    Server:  default server
    Address:  192.168.0.1

    Non-authoritative answer:
    Name:    <instance of web app service>.cloudapp.net
    Address:  <ip of web app service>
    Aliases:  www.contoso.com
    contoso.azurewebsites.net
    <instance of web app service>.vip.azurewebsites.windows.net

## <a name="create-an-awverify-record-for-web-apps"></a>Créer un enregistrement de « awverify » pour les applications web


Si vous décidez d’utiliser un enregistrement A pour votre application web, vous devez accéder via un processus de vérification afin de que vous êtes le propriétaire du domaine personnalisé. Cette étape de vérification est effectuée en créant un enregistrement CNAME spécial nommé « awverify ». Cette section s’applique à des enregistrements uniquement.


### <a name="step-1"></a>Étape 1

Créer l’enregistrement de « awverify ». Dans l’exemple ci-dessous, nous allons créer l’enregistrement de « aweverify » pour contoso.com vérifier la propriété pour le domaine personnalisé.

    $rs = New-AzureRMDnsRecordSet -ZoneName contoso.com -ResourceGroupName myresourcegroup -Name "awverify" -RecordType "CNAME" -Ttl 600

    Name              : awverify
    ZoneName          : contoso.com
    ResourceGroupName : myresourcegroup
    Ttl               : 600
    Etag              : 8baceeb9-4c2c-4608-a22c-229923ee1856
    RecordType        : CNAME
    Records           : {}
    Tags              : {}


### <a name="step-2"></a>Étape 2

Une fois que le jeu d’enregistrements « awverify » est créé, attribuer l’enregistrement CNAME définir les alias. Dans l’exemple ci-dessous, nous allons désigner l’enregistrement CNAMe la valeur alias awverify.contoso.azurewebsites.net.

    Add-AzureRMDnsRecordConfig -RecordSet $rs -Cname "awverify.contoso.azurewebsites.net"

    Name              : awverify
    ZoneName          : contoso.com
    ResourceGroupName : myresourcegroup
    Ttl               : 600
    Etag              : 8baceeb9-4c2c-4608-a22c-229923ee185
    RecordType        : CNAME
    Records           : {awverify.contoso.azurewebsites.net}
    Tags              : {}

### <a name="step-3"></a>Étape 3

Valider les modifications apportées à l’aide de la `Set-AzureRMDnsRecordSet cmdlet`, comme indiqué dans la commande ci-dessous.

    Set-AzureRMDnsRecordSet -RecordSet $rs



## <a name="next-steps"></a>Étapes suivantes

Suivez les étapes de la [configuration d’un nom de domaine personnalisé pour le Service d’application](../app-service-web/web-sites-custom-domain-name.md) pour configurer votre application web pour utiliser un domaine personnalisé.








