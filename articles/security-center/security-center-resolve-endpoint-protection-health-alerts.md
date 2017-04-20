<properties
   pageTitle="Résoudre les alertes de fonctionnement de protection de point de terminaison dans le centre de sécurité Azure | Microsoft Azure"
   description="Ce document vous montre la mise en œuvre de la recommandation du centre de sécurité Azure **alertes de santé résoudre Endpoint Protection**."
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

# <a name="resolve-endpoint-protection-health-alerts-in-azure-security-center"></a>Résoudre les alertes de fonctionnement de protection de point de terminaison dans le centre de sécurité Azure

Centre de sécurité Azure recommande que vous résoudre les alertes de fonctionnement de protection de point de terminaison détectés.  Centre de sécurité vous permet de voir les machines virtuelles (VM) ont des défaillances de protection de point de terminaison et le nombre.

> [AZURE.NOTE] Ce document présente le service à l’aide d’un exemple de déploiement. Il ne s’agit pas d’un guide pas à pas.

## <a name="implement-the-recommendation"></a>Mettre en oeuvre la recommandation

1. Dans la **lame de recommandations**, sélectionnez **alertes de santé résoudre Endpoint Protection**.
![Résoudre les alertes de santé endpoint protection][1]

2. La lame **Échec d’Endpoint Protection** , qui répertorie les ordinateurs virtuels avec les échecs et le nombre d’échecs pour chaque machine virtuelle s’ouvre. Sélectionnez un ordinateur virtuel dans la liste.
![Échec de protection de point de terminaison][2]

3. Une lame de la **Liste des échecs** s’ouvre pour l’ordinateur virtuel sélectionné, affiche la liste des échecs. Sélectionnez un échec dans la liste pour en savoir plus. Une blade s’ouvre avec les informations sur le problème sélectionné.
![Liste des échecs de la commande][3]
  ![événement d’échec][4]

## <a name="see-also"></a>Voir aussi

Pour en savoir plus sur le centre de sécurité, consultez les rubriques suivantes :

- [Définition des stratégies de sécurité dans le centre de sécurité Azure](security-center-policies.md): Apprenez à configurer des stratégies de sécurité pour vos abonnements Azure et les groupes de ressources.
- [Gestion des recommandations de sécurité dans le centre de sécurité Azure](security-center-recommendations.md), découvrez comment les recommandations vous aident à protéger vos ressources d’Azure.
- [Sécurité contrôle d’état dans le centre de sécurité Azure](security-center-monitoring.md)--Découvrez comment surveiller la santé de vos ressources d’Azure.
- [Gérer et répondre aux alertes de sécurité dans le centre de sécurité Azure](security-center-managing-and-responding-alerts.md), découvrez comment gérer et répondre aux alertes de sécurité.
- [Solutions de partenaires de surveillance avec le centre de sécurité Azure](security-center-partner-solutions.md) , découvrez comment surveiller l’état de santé de vos solutions de partenaires.
- [Forum aux questions sur Centre de sécurité azure](security-center-faq.md)--rechercher fréquemment posées à propos de l’utilisation du service.
- [Blog sur la sécurité d’azure](http://blogs.msdn.com/b/azuresecurity/): obtenir les informations et les dernières informations de sécurité Azure.

<!--Image references-->
[1]: ./media/security-center-resolve-endpoint-protection/resolve-endpoint-protection.png
[2]: ./media/security-center-resolve-endpoint-protection/endpoint-protection-failure.png
[3]: ./media/security-center-resolve-endpoint-protection/failure-list.png
[4]: ./media/security-center-resolve-endpoint-protection/failure-event.png
