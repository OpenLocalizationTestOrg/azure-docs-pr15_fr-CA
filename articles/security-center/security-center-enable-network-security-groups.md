<properties
   pageTitle="Activer le réseau des groupes de sécurité dans le centre de sécurité Azure | Microsoft Azure"
   description="Ce document vous montre comment implémenter la recommandation du centre de sécurité Azure **Activer des groupes de sécurité réseau**."
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

# <a name="enable-network-security-groups-in-azure-security-center"></a>Activer le réseau des groupes de sécurité dans le centre de sécurité Azure

Le centre de sécurité Azure recommandons d’activer un groupe de sécurité réseau (NSG) si une n’est pas déjà activée. NSGs contient une liste des règles de liste de contrôle d’accès (ACL) qui autorisent ou refusent le trafic réseau vers les instances de la machine virtuelle dans un réseau virtuel. NSGs peuvent être associés à des sous-réseaux ou des instances VM individuelles au sein de ce sous-réseau. Lorsqu’un NSG est associé à un sous-réseau, les règles de la liste ACL s’appliquent à toutes les instances de la machine virtuelle de ce sous-réseau. En outre, le trafic vers un ordinateur virtuel individuel peut être limité en associant un NSG directement à cette machine virtuelle. Pour en savoir plus, consultez [ce qu’est un groupe de sécurité réseau (NSG) ?](../virtual-network/virtual-networks-nsg.md)

Si vous n’avez pas activés de NSGs, le centre de sécurité présente deux recommandations pour vous : activer des groupes de sécurité de réseau sur les sous-réseaux et activer des groupes de sécurité de réseau sur les machines virtuelles. Vous choisissez le niveau, le sous-réseau ou le VM, à appliquer les NSGs.


> [AZURE.NOTE] Ce document présente le service à l’aide d’un exemple de déploiement.  Il ne s’agit pas d’un guide pas à pas.

## <a name="implement-the-recommendation"></a>Mettre en oeuvre la recommandation

1. Dans la lame de **recommandations** , sélectionnez **Activer les groupes de sécurité réseau** sur les sous-réseaux ou sur des machines virtuelles.
![Activer des groupes de sécurité réseau][1]

2. La blade de **Configurer des groupes de sécurité réseau manquant** pour les sous-réseaux ou les ordinateurs virtuels, selon les recommandations que vous avez sélectionné s’affiche. Sélectionnez un sous-réseau ou une machine virtuelle à configurer un NSG sur.

  ![Configurer NSG de sous-réseau][2]

  ![Configurer NSG de machine virtuelle][3]
3. Sur la lame du **groupe de sécurité réseau choisir** sélectionnez un NSG existant ou pour créer un nouveau NSG.

  ![Choisissez le groupe de sécurité réseau][4]

Si vous créez un nouveau NSG, suivez les étapes de [la gestion des NSGs de via le portail Azure](../virtual-network/virtual-networks-create-nsg-arm-pportal.md) créer un NSG et de définir des règles de sécurité.

## <a name="see-also"></a>Voir aussi

Cet article vous a montré comment implémentent la recommandation du centre de sécurité « Activer des groupes de sécurité de réseau » pour les sous-réseaux ou les ordinateurs virtuels. Pour en savoir plus sur l’activation de NSGs, consultez les rubriques suivantes :

- [En quoi consiste un groupe de sécurité réseau (NSG) ?](../virtual-network/virtual-networks-nsg.md)
- [Comment gérer les NSGs à l’aide du portail Azure](../virtual-network/virtual-networks-create-nsg-arm-pportal.md)

Pour en savoir plus sur le centre de sécurité, consultez les rubriques suivantes :

- [Définition des stratégies de sécurité dans le centre de sécurité Azure](security-center-policies.md) : Apprenez à configurer des stratégies de sécurité pour vos abonnements Azure et les groupes de ressources.
- [Gestion des recommandations de sécurité dans le centre de sécurité Azure](security-center-recommendations.md) , découvrez comment les recommandations vous aident à protéger vos ressources d’Azure.
- [Surveillance de l’intégrité de la sécurité dans le centre de sécurité Azure](security-center-monitoring.md) --Découvrez comment surveiller la santé de vos ressources d’Azure.
- [Gérer et répondre aux alertes de sécurité dans le centre de sécurité Azure](security-center-managing-and-responding-alerts.md) , découvrez comment gérer et répondre aux alertes de sécurité.
- [Solutions de partenaires de surveillance avec le centre de sécurité Azure](security-center-partner-solutions.md) , découvrez comment surveiller l’état de santé de vos solutions de partenaires.
- [Forum aux questions sur Centre de sécurité azure](security-center-faq.md) --rechercher fréquemment posées à propos de l’utilisation du service.
- [Blog sur la sécurité d’azure](http://blogs.msdn.com/b/azuresecurity/) : obtenir les informations et les dernières informations de sécurité Azure.

<!--Image references-->
[1]: ./media/security-center-enable-nsg/enable-nsg.png
[2]:./media/security-center-enable-nsg/configure-nsg-for-subnet.png
[3]: ./media/security-center-enable-nsg/configure-nsg-for-vm.png
[4]: ./media/security-center-enable-nsg/choose-nsg.png
