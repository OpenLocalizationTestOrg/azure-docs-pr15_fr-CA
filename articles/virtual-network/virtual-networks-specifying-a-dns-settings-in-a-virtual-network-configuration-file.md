<properties 
   pageTitle="Spécification des paramètres DNS dans un fichier de configuration de réseau virtuel | Microsoft Azure"
   description="Comment faire pour modifier les paramètres de serveur DNS dans un réseau virtuel à l’aide d’un fichier de configuration de réseau virtuel dans le modèle de déploiement classique"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" 
   tags="azure-service-management" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/23/2016"
   ms.author="jdial" /> 


# <a name="specifying-dns-settings-in-a-virtual-network-configuration-file"></a>Spécifier les paramètres DNS dans un fichier de configuration de réseau virtuel

Un fichier de configuration réseau comporte deux éléments que vous pouvez utiliser pour spécifier les paramètres du système de nom de domaine (DNS) : **DnsServers** et **DnsServerRef**. Vous pouvez ajouter une liste de serveurs DNS en spécifiant leurs adresses IP et les noms de référence à l’élément **DnsServers** . Vous pouvez ensuite utiliser un élément de **DnsServerRef** pour spécifier les entrées du serveur DNS à partir de l’élément DnsServers sont utilisées pour les sites de réseau différents au sein du réseau virtuel.

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Cet article décrit le modèle de déploiement classique.

Le fichier de configuration de réseau peut contenir les éléments suivants. Le titre de chaque élément est lié à une page qui fournit des informations supplémentaires sur les paramètres de valeur de l’élément.

>[AZURE.IMPORTANT] Pour plus d’informations sur la façon de configurer le fichier de configuration réseau, reportez-vous à la section [configuration d’un réseau virtuel à l’aide d’un fichier de Configuration réseau](virtual-networks-using-network-configuration-file.md). Pour plus d’informations sur chaque élément contenu dans le fichier de configuration de réseau, consultez le [Schéma de Configuration de réseau virtuel Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx).

[Élément de DNS](http://go.microsoft.com/fwlink/?LinkId=248093)

    <Dns>
      <DnsServers>
        <DnsServer name="ID1" IPAddress="IPAddress1" />
        <DnsServer name="ID2" IPAddress="IPAddress2" />
        <DnsServer name="ID3" IPAddress="IPAddress3" />
      </DnsServers>
    </Dns>

>[AZURE.WARNING] L’attribut **name** dans l’élément **ServeurDNS** est utilisé uniquement en tant que référence de l’élément **DnsServerRef** . Il ne représente pas le nom d’hôte du serveur DNS. Chaque valeur de l’attribut **ServeurDNS** doit être unique dans tout l’abonnement Microsoft Azure

[Élément de Sites de réseau virtuel](http://go.microsoft.com/fwlink/?LinkId=248093)

    <DnsServersRef>
      <DnsServerRef name="ID1" />
      <DnsServerRef name="ID2" />
      <DnsServerRef name="ID3" />
    </DnsServersRef>

>[AZURE.NOTE] Pour spécifier ce paramètre pour l’élément de Sites de réseau virtuel, il doit être préalablement défini dans l’élément DNS. Le *nom* de DnsServerRef dans l’élément de Sites de réseau virtuel doit faire référence à une valeur de nom spécifiée dans l’élément serveurDNS *nom*DNS.

## <a name="next-steps"></a>Étapes suivantes

- Comprendre le [schéma de Configuration Azure réseau virtuel](http://go.microsoft.com/fwlink/?LinkId=248093).
- Comprendre le [schéma de Configuration de Service Azure](https://msdn.microsoft.com/library/windowsazure/ee758710).
- [Configuration d’un réseau virtuel à l’aide de fichiers de configuration réseau](virtual-networks-using-network-configuration-file.md).
