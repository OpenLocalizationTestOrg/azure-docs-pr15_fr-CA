<properties
   pageTitle="Ajouter un pare-feu de génération suivant dans le centre de sécurité Azure | Microsoft Azure"
   description="Ce document vous montre comment implémenter les recommandations du centre de sécurité Azure **Ajouter un pare-feu de génération suivante** et **utilise itinéraire par la Conviction seulement**."
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

# <a name="add-a-next-generation-firewall-in-azure-security-center"></a>Ajouter un pare-feu de génération suivant dans le centre de sécurité Azure

Le centre de sécurité Azure peut recommander que vous ajoutez un pare-feu de génération suivant (Conviction) d’un partenaire Microsoft pour augmenter la protection de votre sécurité. Ce document vous guide à travers un exemple de cette procédure.

> [AZURE.NOTE] Ce document présente le service à l’aide d’un exemple de déploiement.  Il ne s’agit pas d’un guide pas à pas.

## <a name="implement-the-recommendation"></a>Mettre en oeuvre la recommandation

1. De la lame de **recommandations** , sélectionnez **Ajouter un pare-feu de génération suivante**.
![Ajouter un pare-feu de génération suivant][1]

2. Dans la lame **Ajouter un pare-feu de génération suivante** , sélectionnez un point de terminaison.
![Sélectionnez un point de terminaison][2]

3. Une deuxième lame **d’Ajouter un pare-feu de génération suivante** s’ouvre. Vous pouvez choisir d’utiliser une solution existante si elle est disponible ou vous pouvez créer un nouveau. Dans cet exemple sont des solutions existantes disponibles afin que nous allons créer une nouvelle Conviction.
![Créez le nouveau pare-feu de génération suivante][3]

4. Pour créer une nouveau Conviction, sélectionnez une solution dans la liste des partenaires intégrés. Dans cet exemple, nous allons sélectionner **Check Point**.
![Sélectionnez la solution de pare-feu de génération suivante][4]

5. La lame de **Point de vérification** s’ouvre à vous fournir des informations sur les partenaires solution. Sélectionnez **créer** dans la blade d’informations.
![Lame d’informations de pare-feu][5]

6. La lame de la **machine virtuelle de créer** s’ouvre. Vous pouvez saisir les informations requises pour faire tourner une machine virtuelle (VM) qui exécutera la Conviction. Suivez les étapes et fournir les informations de Conviction que nécessaires. Sélectionnez OK pour les appliquer.
![Créer la machine virtuelle pour exécuter Conviction][6]

## <a name="route-traffic-through-ngfw-only"></a>Acheminement du trafic via Conviction uniquement

Revenir à la lame de **recommandations** . Une nouvelle entrée a été générée après avoir ajouté une Conviction via le centre de sécurité, appelé **acheminement du trafic via la Conviction uniquement**. Cette recommandation est créée uniquement si vous avez installé votre Conviction via le centre de sécurité. Si vous avez des points de terminaison faisant face à Internet, le centre de sécurité vous recommandera que vous configurez les règles de groupe de sécurité réseau qui force le trafic entrant de votre machine virtuelle par le biais de votre Conviction.

1. Dans la **lame de recommandations**, sélectionnez **acheminement du trafic via la Conviction uniquement**.
![Acheminement du trafic via Conviction uniquement][7]

2. La blade d' **acheminer le trafic via la Conviction seulement** qui répertorie les ordinateurs virtuels qui vous pouvez d’acheminer le trafic vers s’affiche. Sélectionnez un ordinateur virtuel dans la liste.
![Sélectionnez un ordinateur virtuel][8]

3. Une lame pour la machine virtuelle sélectionnée s’ouvre, les règles de trafic entrant connexes. Une description vous fournit plus d’informations sur les étapes suivantes possibles. Sélectionnez **Modifier les règles de trafic entrant** pour procéder à la modification d’une règle de trafic entrant. L’objectif est que **Source** n’est pas définie à **n’importe quel** pour les points de terminaison faisant face à Internet liés avec la Conviction. Pour en savoir plus sur les propriétés de la règle de trafic entrant, consultez [règles NSG](../virtual-network/virtual-networks-nsg.md#nsg-rules).
![Configurer des règles pour limiter l’accès][9]
![modifier la règle entrante][10]

## <a name="see-also"></a>Voir aussi

Ce document vous a montré comment implémentent la recommandation du centre de sécurité « Ajouter un pare-feu de génération suivant. » Pour en savoir plus sur la solution partenaire de Point de vérification et de NGFWs, consultez les rubriques suivantes :

- [Pare-feu de prochaine génération](https://en.wikipedia.org/wiki/Next-Generation_Firewall)
- [VSEC de Point de vérification](https://azure.microsoft.com/marketplace/partners/checkpoint/check-point-r77-10/)

Pour en savoir plus sur le centre de sécurité, consultez les rubriques suivantes :

- [Définition des stratégies de sécurité dans le centre de sécurité Azure](security-center-policies.md) : Apprenez à configurer des stratégies de sécurité.
- [Gestion des recommandations de sécurité dans le centre de sécurité Azure](security-center-recommendations.md) , découvrez comment les recommandations vous aident à protéger vos ressources d’Azure.
- [Surveillance de l’intégrité de la sécurité dans le centre de sécurité Azure](security-center-monitoring.md) --Découvrez comment surveiller la santé de vos ressources d’Azure.
- [Gérer et répondre aux alertes de sécurité dans le centre de sécurité Azure](security-center-managing-and-responding-alerts.md) , découvrez comment gérer et répondre aux alertes de sécurité.
- [Solutions de partenaires de surveillance avec le centre de sécurité Azure](security-center-partner-solutions.md) , découvrez comment surveiller l’état de santé de vos solutions de partenaires.
- [Forum aux questions sur Centre de sécurité azure](security-center-faq.md) --rechercher fréquemment posées à propos de l’utilisation du service.
- Billets de [blog sur la sécurité d’azure](http://blogs.msdn.com/b/azuresecurity/) --blog de recherche sur la conformité et la sécurité Azure.

<!--Image references-->
[1]: ./media/security-center-add-next-gen-firewall/add-next-gen-firewall.png
[2]: ./media/security-center-add-next-gen-firewall/select-an-endpoint.png
[3]: ./media/security-center-add-next-gen-firewall/create-new-next-gen-firewall.png
[4]: ./media/security-center-add-next-gen-firewall/select-next-gen-firewall.png
[5]: ./media/security-center-add-next-gen-firewall/firewall-solution-info-blade.png
[6]: ./media/security-center-add-next-gen-firewall/create-virtual-machine.png
[7]: ./media/security-center-add-next-gen-firewall/route-traffic-through-ngfw.png
[8]: ./media/security-center-add-next-gen-firewall/select-vm.png
[9]: ./media/security-center-add-next-gen-firewall/configure-rules-to-limit-access.png
[10]: ./media/security-center-add-next-gen-firewall/edit-inbound-rule.png
