<properties
   pageTitle="Appliquer le cryptage de disque dans le centre de sécurité Azure | Microsoft Azure"
   description="Ce document vous montre la mise en œuvre de la recommandation du centre de sécurité Azure **Appliquer le chiffrement des disques**."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/29/2016"
   ms.author="terrylan"/>

# <a name="apply-disk-encryption-in-azure-security-center"></a>Appliquer le cryptage de disque dans le centre de sécurité Azure

Centre de sécurité Azure recommande que vous appliquez le cryptage de disque si vous avez des disques de Windows ou Linux VM qui ne sont pas cryptés à l’aide du cryptage de disque Azure. Chiffrement de disque vous permet de crypter vos disques Windows et Linux IaaS VM.  Le cryptage est recommandé pour les volumes à la fois le système d’exploitation et les données sur votre ordinateur virtuel.


Chiffrement de disque tire parti de la fonctionnalité de [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) de secteur standard de Windows et de la fonction [Crypt-DM](https://en.wikipedia.org/wiki/Dm-crypt) de Linux pour fournir le cryptage des données et de système d’exploitation pour protéger et protéger vos données et respecter vos engagements de sécurité et de conformité d’organisation. Cryptage du disque dur est intégré avec [Azure clé de coffre-fort](https://azure.microsoft.com/documentation/services/key-vault/) pour vous aider à contrôler et de gérer les clés de cryptage de disque et les secrets de votre abonnement en chambre forte de la clé, tout en s’assurant que toutes les données sur les disques de l’ordinateur virtuel sont cryptées au reste de votre [Stockage Azure](https://azure.microsoft.com/documentation/services/storage/).

> [AZURE.NOTE] Cryptage de disque Azure est pris en charge sur les systèmes Windows suivants serveur d’exploitation - Windows Server 2008 R2, Windows Server 2012 et Windows Server 2012 R2. Cryptage du disque est pris en charge sur les systèmes Linux suivants serveur d’exploitation - CentOS, SUSE, Ubuntu et SUSE Linux Enterprise Server (SLES).

## <a name="implement-the-recommendation"></a>Mettre en oeuvre la recommandation

1. Dans la lame de **recommandations** , sélectionnez **Appliquer le chiffrement des disques**.
2. De la lame **d’Appliquer le chiffrement des disques** , vous verrez une liste d’ordinateurs virtuels pour lesquels le cryptage du disque dur est recommandé.
3. Suivez les instructions pour appliquer le cryptage à ces ordinateurs virtuels.

![][1]

Pour crypter des Machines virtuelles Azure qui ont été identifiés par le centre de sécurité comme ayant besoin de cryptage, nous vous recommandons des étapes suivantes :

- Installez et configurez Azure PowerShell. Cela vous permettra d’exécuter les commandes PowerShell requis pour installer les composants requis pour crypter les ordinateurs virtuels Azure.
- Obtenir et exécuter le script PowerShell de Azure Azure disque cryptage requis.
- Crypter vos ordinateurs virtuels.

[Crypter un ordinateur virtuel de Azure](security-center-disk-encryption.md) va vous guider tout au long de ces étapes.  Cette rubrique suppose que vous utilisez Windows 10 en tant que l’ordinateur client à partir de laquelle vous allez configurer le cryptage du disque dur.

Il existe de nombreuses approches qui peuvent être utilisés pour installer les conditions préalables et configuration du cryptage pour Azure Virtual Machines. Si vous êtes déjà expérimentés dans Azure PowerShell ou CLI d’Azure, puis vous pouvez utiliser les autres approches. Pour en savoir plus sur ces autres approches voir [cryptage de disque Azure](../security/azure-security-disk-encryption.md).



## <a name="see-also"></a>Voir aussi

Ce document vous a montré comment implémentent la recommandation du centre de sécurité « Chiffrement de disque appliquer ». Pour en savoir plus sur le cryptage de disque, consultez les rubriques suivantes :

- [Cryptage et gestion des clés avec Azure clé coffre-fort](https://azure.microsoft.com/documentation/videos/azurecon-2015-encryption-and-key-management-with-azure-key-vault/) (vidéo, 36 min 39 sec) : Apprenez à utiliser la gestion de chiffrement de disque pour IaaS VMs et Azure clé Vault pour protéger et protéger vos données.
- [Crypter une Machine virtuelle Azure](security-center-disk-encryption.md) (document)--Découvrez comment chiffrer des ordinateurs virtuels Azure.
- [Chiffrement de disque Azure](../security/azure-security-disk-encryption.md) (document) : Apprenez à activer le chiffrement de disque pour Windows et les ordinateurs virtuels de Linux.

Pour en savoir plus sur le centre de sécurité, consultez les rubriques suivantes :

- [Définition des stratégies de sécurité dans le centre de sécurité Azure](security-center-policies.md) : Apprenez à configurer des stratégies de sécurité.
- [Surveillance de l’intégrité de la sécurité dans le centre de sécurité Azure](security-center-monitoring.md) --Découvrez comment surveiller la santé de vos ressources d’Azure.
- [Gérer et répondre aux alertes de sécurité dans le centre de sécurité Azure](security-center-managing-and-responding-alerts.md) , découvrez comment gérer et répondre aux alertes de sécurité.
- [Gestion des recommandations de sécurité dans le centre de sécurité Azure](security-center-recommendations.md) , découvrez comment les recommandations vous aident à protéger vos ressources d’Azure.
- [Forum aux questions sur Centre de sécurité azure](security-center-faq.md) --rechercher fréquemment posées à propos de l’utilisation du service.
- Billets de [blog sur la sécurité d’azure](http://blogs.msdn.com/b/azuresecurity/) --blog de recherche sur la conformité et la sécurité Azure.



<!--Image references-->
[1]: ./media/security-center-apply-disk-encryption/apply-disk-encryption.png
