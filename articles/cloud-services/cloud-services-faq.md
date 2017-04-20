<properties
    pageTitle="Forum aux questions sur les Services nuage | Microsoft Azure"
    description="Forum aux questions sur les Services en nuage."
    services="cloud-services"
    documentationCenter=""
    authors="Thraka"
    manager="timlt"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/19/2016"
    ms.author="adegeo"/>

# <a name="cloud-services-faq"></a>Forum aux questions sur les Services en nuage
Cet article répond à certaines questions fréquemment posées sur les Services en nuage Microsoft Azure. Vous pouvez également visiter le [Forum aux questions de la prise en charge Azure](http://go.microsoft.com/fwlink/?LinkID=185083) pour Azure de tarification et de la prise en charge des informations général. Vous pouvez également consulter la [page de taille de mémoire virtuelle de Services Cloud](cloud-services-sizes-specs.md) pour les informations de taille.

## <a name="certificates"></a>Certificats

### <a name="where-should-i-install-my-certificate"></a>Où dois-je installer mon certificat ?

- **Mon**  
Certificat d’application avec la clé privée (\*.pfx, \*.p12).

- **AUTORITÉ DE CERTIFICATION**  
Tous les certificats intermédiaires aller dans cette banque (stratégie et autorités de certification Sub).

- **RACINE**  
Magasin de l’autorité de certification racine, afin que votre certificat d’autorité de certification racine principal doit être placé ici.

### <a name="i-cant-remove-expired-certificate"></a>Impossible de supprimer les certificats expirés

Azure vous empêche de supprimer un certificat, alors qu’il est en cours d’utilisation. Vous devez soit supprimer le déploiement qui utilise le certificat ou mettre à jour le déploiement avec un certificat différent ou renouvelé.

### <a name="delete-an-expired-certificate"></a>Supprimer un certificat expiré

Tant que le certificat n’est pas en cours d’utilisation, vous pouvez utiliser l’applet de commande [Remove-AzureCertificate](https://msdn.microsoft.com/library/azure/mt589145.aspx) PowerShell pour supprimer un certificat.

### <a name="i-have-expired-certificates-named-windows-azure-service-management-for-extensions"></a>J’ai expiré nommés gestion des services Windows Azure pour les Extensions de certificats

Ces certificats sont créés chaque fois qu’une extension est ajoutée pour le service en nuage comme l’extension du Bureau à distance. Ces certificats sont uniquement utilisés pour crypter et décrypter la configuration privée de l’extension. Il n’a pas d’importance si ces certificats expirent. La date d’expiration n’est pas vérifiée.

### <a name="certificates-i-have-deleted-keep-reappearing"></a>J’ai supprimé les certificats continuent à apparaître

Ils continuent à apparaître très probablement en raison d’un outil que vous utilisez, par exemple Visual Studio. Lorsque vous vous reconnectez avec un outil qui utilise un certificat, elle est à nouveau téléchargé vers Azure.

### <a name="my-certificates-keep-disappearing"></a>Mes certificats ne cessent de disparaître

Lorsque l’instance de la machine virtuelle est recyclé, toutes les modifications locales sont perdues. Utiliser une [tâche de démarrage](cloud-services-startup-tasks.md) pour installer des certificats sur l’ordinateur virtuel chaque fois que le rôle démarre.

### <a name="i-cannot-find-my-management-certificates-in-the-portal"></a>Je ne trouve pas mes certificats de gestion dans le portail

[Les certificats de gestion](..\azure-api-management-certs.md) sont uniquement disponibles dans le portail classique d’Azure. Le portail Azure actuel n’utilise pas les certificats de gestion. 

### <a name="how-can-i-disable-a-management-certificate"></a>Comment puis-je désactiver un certificat de gestion ?

[Les certificats de gestion](..\azure-api-management-certs.md) ne peut pas être désactivé. Vous supprimez via le portail classique Azure lorsque vous ne souhaitez pas les utiliser plus.

### <a name="how-do-i-create-an-ssl-certificate-for-a-specific-ip-address"></a>Comment pour créer un certificat SSL pour une adresse IP spécifique ?

Suivez les instructions dans la [Création d’un didacticiel de certificat](cloud-services-certs-create.md). Utiliser l’adresse IP comme nom DNS.

## <a name="security"></a>Sécurité

### <a name="disable-ssl-30"></a>Désactiver SSL 3.0

Pour désactiver SSL 3.0 et utilisent la sécurité TLS, créer une tâche de démarrage qui est décrite dans ce billet de blog : https://azure.microsoft.com/en-us/blog/how-to-disable-ssl-3-0-in-azure-websites-roles-and-virtual-machines/

## <a name="scale-a-cloud-service"></a>Mettre à l’échelle un service cloud

### <a name="i-cannot-scale-beyond-x-instances"></a>Je ne parviens pas à mettre à l’échelle au-delà de X instances

Votre abonnement Azure a une limite sur le nombre de cœurs que vous pouvez utiliser. Mise à l’échelle ne fonctionnera pas si vous avez utilisé tous les cœurs disponibles. Par exemple, si vous avez une limite de 100 cœurs, ainsi vous pouvez y 100 instances de machine virtuelle A1 taille pour votre service cloud, ou 50 A2 taille des instances de la machine virtuelle.

## <a name="troubleshooting"></a>Résolution des problèmes

### <a name="i-cant-reserve-an-ip-in-a-multi-vip-cloud-service"></a>Impossible de réserver une adresse IP dans un service en nuage multi-VIP

Tout d’abord, assurez-vous que l’instance de la machine virtuelle que vous essayez de réserver l’adresse IP est activé. Ensuite, assurez-vous que vous utilisez des adresses IP réservées pour s’ennuyer les déploiements de test et de production. **Pas** de modifier les paramètres pendant le déploiement de la mise à niveau.

