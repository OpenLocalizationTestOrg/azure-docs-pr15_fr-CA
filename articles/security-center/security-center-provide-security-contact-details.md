<properties
   pageTitle="Fournir des coordonnées de la sécurité dans le centre de sécurité Azure | Microsoft Azure"
   description="Ce document vous montre comment fournir des coordonnées de la sécurité dans le centre de sécurité Azure."
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
   ms.date="10/17/2016"
   ms.author="terrylan"/>

# <a name="provide-security-contact-details-in-azure-security-center"></a>Fournir des coordonnées de la sécurité dans le centre de sécurité Azure

Le centre de sécurité Azure recommande que vous fournissez des détails de contact de sécurité pour votre abonnement Azure si vous ne l’avez pas déjà. Ces informations seront être utilisées par Microsoft pour vous contacter si le centre de réponse sécurité Microsoft (MSRC) détecte que les données de votre client a accédé par une partie illégale ou non autorisée. MSRC effectue une analyse de sécurité select de l’infrastructure et du réseau Azure et reçoit les plaintes de l’intelligence et les abus de menace par des tiers.

Une notification par e-mail est envoyée à la première occurrence quotidienne d’une alerte et uniquement pour les alertes de gravité élevée. Préférences de courrier électronique ne peuvent être configurés pour les stratégies de l’abonnement. Les groupes de ressources dans un abonnement héritera de ces paramètres.

> [AZURE.NOTE] Ce document présente le service à l’aide d’un exemple de déploiement.  Il ne s’agit pas d’un guide pas à pas.

## <a name="implement-the-recommendation"></a>Mettre en oeuvre la recommandation

1. Dans la lame de **recommandations** , sélectionnez **fournir une sécurité détails du contact**.
![Fournir des contacts de sécurité][1]

2. La blade de **fournir une sécurité détails du contact**s’ouvre. Sélectionnez l’abonnement Azure pour fournir des informations de contact sur.
![Fournir des détails de contact de sécurité][2]

3. Une deuxième lame **fournissent des détails de contact de sécurité** s’ouvre.

  - Entrez l’adresse de messagerie de sécurité ou les adresses en les séparant par des virgules. Il n’est pas une limite au nombre d’adresses de messagerie que vous entrez.
  - Entrez un numéro de téléphone international de sécurité.
  - Pour recevoir des courriers électroniques à propos des alertes de gravité élevée, activez l’option **Envoyer par e-mail sur les alertes**.
  - À l’avenir, vous devez l’option pour envoyer des notifications par courrier électronique aux propriétaires de l’abonnement. Cette option est actuellement désactivée.
  - Cliquez sur **OK** pour appliquer les informations de contact de sécurité à votre abonnement.

## <a name="see-also"></a>Voir aussi

Pour en savoir plus sur le centre de sécurité, consultez les rubriques suivantes :

- [Définition des stratégies de sécurité dans le centre de sécurité Azure](security-center-policies.md) : Apprenez à configurer des stratégies de sécurité pour vos abonnements Azure et les groupes de ressources.
- [Gestion des recommandations de sécurité dans le centre de sécurité Azure](security-center-recommendations.md) , découvrez comment les recommandations vous aident à protéger vos ressources d’Azure.
- [Surveillance de l’intégrité de la sécurité dans le centre de sécurité Azure](security-center-monitoring.md) --Découvrez comment surveiller la santé de vos ressources d’Azure.
- [Gérer et répondre aux alertes de sécurité dans le centre de sécurité Azure](security-center-managing-and-responding-alerts.md) , découvrez comment gérer et répondre aux alertes de sécurité.
- [Solutions de partenaires de surveillance avec le centre de sécurité Azure](security-center-partner-solutions.md) , découvrez comment surveiller l’état de santé de vos solutions de partenaires.
- [Forum aux questions sur Centre de sécurité azure](security-center-faq.md) --rechercher fréquemment posées à propos de l’utilisation du service.
- [Blog sur la sécurité d’azure](http://blogs.msdn.com/b/azuresecurity/) : obtenir les informations et les dernières informations de sécurité Azure.

<!--Image references-->
[1]: ./media/security-center-provide-security-contacts/provide-contacts.png
[2]:./media/security-center-provide-security-contacts/provide-contact-details.png
