<properties
   pageTitle="Restreindre l’accès par le biais de points de terminaison faisant face à Internet dans le centre de sécurité Azure | Microsoft Azure"
   description="Ce document vous montre comment implémenter la recommandation du centre de sécurité Azure **restreindre l’accès par le biais de point de terminaison qui fait face à Internet**."
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
   ms.date="10/26/2016"
   ms.author="terrylan"/>

# <a name="restrict-access-through-internet-facing-endpoints-in-azure-security-center"></a>Restreindre l’accès par le biais de points de terminaison faisant face à Internet dans le centre de sécurité Azure

Le centre de sécurité Azure recommande de restreindre l’accès par le biais de points de terminaison faisant face à Internet si un de vos groupes de sécurité réseau (NSGs) a une ou plusieurs règles de trafic entrant qui permet l’accès à partir de « toute » adresse IP source. Ouvrant l’accès à « tout » peut permettent aux pirates d’accéder à vos ressources. Centre de sécurité vous recommandera que vous modifiez ces règles de trafic entrant pour restreindre l’accès à des adresses IP source qui en ont réellement besoin.

Cette recommandation est générée pour n’importe quel port non web qui a « quelconque » en tant que source.

> [AZURE.NOTE] Ce document présente le service à l’aide d’un exemple de déploiement. Il ne s’agit pas d’un guide pas à pas.

## <a name="implement-the-recommendation"></a>Mettre en oeuvre la recommandation

1. Dans la **lame de recommandations**, sélectionnez **restreindre l’accès par le biais de point de terminaison qui fait face à Internet**.
![Restreindre l’accès par le biais de Internet vers le point de terminaison][1]

2. La blade de **restreindre l’accès par le biais de point de terminaison qui fait face à Internet**s’ouvre. Cette blade répertorie les machines virtuelles (VM) avec des règles de trafic entrant qui créent un problème de sécurité potentiel. Sélectionnez un ordinateur virtuel.
![Sélectionnez un ordinateur virtuel][2]

3. La lame **NSG** affiche les informations, les règles de trafic entrant connexes et la machine virtuelle associée groupe de sécurité de réseau. Sélectionnez **Modifier les règles de trafic entrant** pour procéder à la modification d’une règle de trafic entrant.
![Lame de groupe de sécurité réseau][3]

4. Sur la lame de **règles de sécurité entrante** , sélectionnez la règle de trafic entrant pour le modifier. Dans cet exemple, sélectionnez **AllowWeb**.
![Les règles de sécurité de trafic entrant][4]

  Remarque Vous pouvez également sélectionner des **règles par défaut** pour visualiser l’ensemble de règles par défaut contenus dans tous les NSGs. Les règles par défaut ne peut pas être supprimés, mais dans la mesure où ils sont affectés d’une priorité plus faible, elles peuvent être remplacées par les règles que vous créez. Pour en savoir plus sur [les règles par défaut](../virtual-network/virtual-networks-nsg.md#default-rules).
![Règles par défaut][5]

5. Sur la lame **AllowWeb** , modifiez les propriétés de la règle de trafic entrant pour que la **Source** est une adresse IP ou un bloc d’adresses IP. Pour en savoir plus sur les propriétés de la règle de trafic entrant, consultez [règles NSG](../virtual-network/virtual-networks-nsg.md#nsg-rules).

  ![Modifier la règle de trafic entrant][6]

## <a name="see-also"></a>Voir aussi

Cet article vous a montré comment implémentent la recommandation du centre de sécurité « Restreindre l’accès par le biais de point de terminaison qui fait face à Internet ». Pour en savoir plus sur l’activation de règles et NSGs, consultez les rubriques suivantes :

- [En quoi consiste un groupe de sécurité réseau (NSG) ?](../virtual-network/virtual-networks-nsg.md)
- [Comment gérer les NSGs à l’aide du portail Azure](../virtual-network/virtual-networks-create-nsg-arm-pportal.md)

Pour en savoir plus sur le centre de sécurité, consultez les rubriques suivantes :

- [Définition des stratégies de sécurité dans le centre de sécurité Azure](security-center-policies.md): Apprenez à configurer des stratégies de sécurité pour vos abonnements Azure et les groupes de ressources.
- [Gestion des recommandations de sécurité dans le centre de sécurité Azure](security-center-recommendations.md), découvrez comment les recommandations vous aident à protéger vos ressources d’Azure.
- [Sécurité contrôle d’état dans le centre de sécurité Azure](security-center-monitoring.md)--Découvrez comment surveiller la santé de vos ressources d’Azure.
- [Gérer et répondre aux alertes de sécurité dans le centre de sécurité Azure](security-center-managing-and-responding-alerts.md), découvrez comment gérer et répondre aux alertes de sécurité.
- [Solutions de partenaires de surveillance avec le centre de sécurité Azure](security-center-partner-solutions.md) , découvrez comment surveiller l’état de santé de vos solutions de partenaires.
- [Forum aux questions sur Centre de sécurité azure](security-center-faq.md)--rechercher fréquemment posées à propos de l’utilisation du service.
- [Blog sur la sécurité d’azure](http://blogs.msdn.com/b/azuresecurity/): obtenir les informations et les dernières informations de sécurité Azure.

<!--Image references-->
[1]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/restrict-access-thru-internet-facing-endpoint.png
[2]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/select-a-vm.png
[3]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/network-security-group-blade.png
[4]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/inbound-security-rules.png
[5]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/default-rules.png
[6]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/edit-inbound-rule.png
