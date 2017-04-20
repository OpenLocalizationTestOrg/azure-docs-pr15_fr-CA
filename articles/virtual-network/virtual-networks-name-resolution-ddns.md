<properties
   pageTitle="À l’aide de Dynamic DNS pour inscrire les noms d’hôte"
   description="Cette page explique comment configurer le DNS dynamique pour enregistrer des noms d’hôtes dans vos propres serveurs DNS."
   services="dns"
   documentationCenter="na"
   authors="GarethBradshawMSFT"
   manager="carmonm"
   editor="" />
<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/31/2016"
   ms.author="sewhee" />

# <a name="using-dynamic-dns-to-register-hostnames-in-your-own-dns-server"></a>À l’aide de DNS dynamique pour enregistrer des noms d’hôtes dans votre serveur DNS

[Azure fournit la résolution de noms](virtual-networks-name-resolution-for-vms-and-role-instances.md) pour les instances de rôles et de machines virtuelles (VM). Toutefois, lors de la résolution de nom doit aller au-delà de celles fournies par Azure, vous pouvez fournir vos propres serveurs DNS. Cela vous permet de personnaliser votre solution DNS pour l’adapter à vos propres besoins. Par exemple, vous devrez peut-être accéder à des ressources sur site via votre contrôleur de domaine Active Directory.

Lorsque vos serveurs DNS personnalisées sont hébergés en tant que machines virtuelles d’Azure, vous pouvez transférer les requêtes de nom d’hôte pour la même vnet vers Azure pour résoudre les noms d’hôtes. Si vous ne souhaitez pas utiliser cet itinéraire, vous pouvez enregistrer vos noms d’hôtes d’ordinateur virtuel dans votre serveur DNS à l’aide de DNS dynamique.  Azure ne (par exemple, des références) permet de créer directement des enregistrements de vos serveurs DNS, afin que les autres dispositions sont souvent nécessaires. Voici quelques scénarios courants avec des solutions alternatives.

## <a name="windows-clients"></a>Clients Windows

Liés à un domaine non des clients Windows essaient de mises à jour de DNS dynamique (DDNS) non sécurisées lors de leur démarrage ou lors de la modification de leur adresse IP. Le nom DNS est le nom d’hôte et le suffixe DNS principal. Azure laisse le suffixe DNS principal vide, mais vous pouvez la définir dans la machine virtuelle, via [l’interface utilisateur](https://technet.microsoft.com/library/cc794784.aspx) ou [par automation](https://social.technet.microsoft.com/forums/windowsserver/3720415a-6a9a-4bca-aa2a-6df58a1a47d7/change-primary-dns-suffix).

Les clients Windows à un domaine inscrivent leurs adresses IP avec le contrôleur de domaine à l’aide de DNS dynamique sécurisée. Le processus de jonction de domaine définit le suffixe DNS principal sur le client et crée et met à jour la relation d’approbation.

## <a name="linux-clients"></a>Clients Linux

Les clients Linux généralement ne s’enregistrent eux-mêmes avec le serveur DNS au démarrage, ils supposent que le serveur DHCP s’en charge. Les serveurs DHCP d’Azure n’ont pas la possibilité ou les informations d’identification pour inscrire les enregistrements de votre serveur DNS.  Vous pouvez utiliser un outil appelé *nsupdate*, qui est inclus dans le package de liaison, envoyer des mises à jour DNS dynamiques. Dans la mesure où le protocole DNS dynamique est normalisé, vous pouvez utiliser *nsupdate* , même lorsque vous n’utilisez pas de liaison sur le serveur DNS.

Vous pouvez utiliser les crochets qui sont fournis par le client DHCP pour créer et gérer l’entrée de nom d’hôte du serveur DNS. Pendant le cycle DHCP, le client exécute les scripts dans */etc/dhcp/dhclient-exit-hooks.d/*. Cela peut servir à enregistrer la nouvelle adresse IP à l’aide de *nsupdate*. Par exemple :

        #!/bin/sh
        requireddomain=mydomain.local

        # only execute on the primary nic
        if [ "$interface" != "eth0" ]
        then
            return
        fi

        # when we have a new IP, perform nsupdate
        if [ "$reason" = BOUND ] || [ "$reason" = RENEW ] ||
           [ "$reason" = REBIND ] || [ "$reason" = REBOOT ]
        then
            host=`hostname`
            nsupdatecmds=/var/tmp/nsupdatecmds
              echo "update delete $host.$requireddomain a" > $nsupdatecmds
              echo "update add $host.$requireddomain 3600 a $new_ip_address" >> $nsupdatecmds
              echo "send" >> $nsupdatecmds

              nsupdate $nsupdatecmds
        fi

        #done
        exit 0;

Vous pouvez également utiliser la commande *nsupdate* pour effectuer des mises à jour DNS dynamiques sécurisées. Par exemple, lorsque vous utilisez un serveur DNS Bind, une paire de clés publique / privée est [générée](http://linux.yyz.us/nsupdate/).  Le serveur DNS est [configuré](http://linux.yyz.us/dns/ddns-server.html) avec la partie publique de la clé afin qu’il peut vérifier la signature de la demande. Vous devez utiliser l’option *-k* pour fournir que la paire de clés à *nsupdate* afin que le serveur DNS dynamique mettre à jour la demande de signature.

Lorsque vous utilisez un serveur DNS Windows, vous pouvez utiliser l’authentification Kerberos avec le paramètre *-g* de *nsupdate* (non disponible dans la version Windows de *nsupdate*). Pour ce faire, utiliser *kinit* pour charger les informations d’identification (par exemple à partir d’un [fichier keytab](http://www.itadmintools.com/2011/07/creating-kerberos-keytab-files.html)). Puis *nsupdate-g* collectera les informations d’identification à partir du cache.

Si nécessaire, vous pouvez ajouter un suffixe de recherche DNS pour vos ordinateurs virtuels. Le suffixe DNS est spécifié dans le fichier */etc/resolv.conf* . La plupart des Linux distros gérer automatiquement le contenu de ce fichier, donc généralement pas les modifier. Toutefois, vous pouvez remplacer le suffixe par à l’aide de la commande de *remplacement* du client DHCP. Pour ce faire, dans */etc/dhcp/dhclient.conf*, ajoutez :

        supersede domain-name <required-dns-suffix>;

