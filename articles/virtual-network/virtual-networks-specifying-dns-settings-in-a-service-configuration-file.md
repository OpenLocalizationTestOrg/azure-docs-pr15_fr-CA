<properties 
   pageTitle="Spécification des paramètres DNS dans un fichier de configuration de service | Microsoft Azure"
   description="spécification de paramètres personnalisés de DNS à l’aide du fichier de configuration de service de réseau virtuel"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/24/2016"
   ms.author="jdial" />

# <a name="specifying-dns-settings-in-a-service-configuration-file"></a>Spécifier les paramètres dans un fichier de Configuration du Service DNS

## <a name="dns-elements"></a>Éléments DNS

Un fichier de configuration de service peut contenir un élément DnsServers avec une liste d’adresses IPv4 pour les serveurs de nom de domaine (DNS) qui utilise le service. Paramètres dans le fichier de configuration de service ont priorité sur les paramètres dans le fichier de configuration réseau. Pour plus d’informations, consultez [Schéma de Configuration de Service Azure (.cscfg fichier)](https://msdn.microsoft.com/library/azure/ee758710.aspx).

**Élément de NetworkConfiguration**

      <DnsServers>
        <DnsServer name="ID1" IPAddress="IPAddress1" />
        <DnsServer name="ID2" IPAddress="IPAddress2" />
        <DnsServer name="ID3" IPAddress="IPAddress3" />
      </DnsServers>

>[AZURE.WARNING] L’attribut **name** dans l’élément **ServeurDNS** est utilisé uniquement comme un nom de référence. Il ne représente pas le nom d’hôte du serveur DNS. Chaque valeur de l’attribut **ServeurDNS** doit être unique dans tout l’abonnement Microsoft Azure.

## <a name="see-also"></a>Voir aussi

[Schéma de Configuration de Service Azure (.cscfg)](https://msdn.microsoft.com/library/windowsazure/ee758710)

[Schéma de Configuration Azure réseau virtuel](http://go.microsoft.com/fwlink/?LinkId=248093)

[Configurer un réseau virtuel à l’aide de fichiers de Configuration réseau](http://go.microsoft.com/fwlink/?LinkId=248094)

[À propos des paramètres de réseau virtuel du portail de gestion](http://go.microsoft.com/fwlink/?LinkId=248092)

