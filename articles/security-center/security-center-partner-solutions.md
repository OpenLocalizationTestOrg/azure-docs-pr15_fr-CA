<properties
   pageTitle="Gestion des solutions de partenaires dans le centre de sécurité Azure | Microsoft Azure"
   description="Ce document vous explique comment le centre de sécurité Azure vous permet de vous moniteur en un clin de œil l’état de vos solutions de partenaires intégrée avec votre abonnement Azure."
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

# <a name="monitoring-partner-solutions-with-azure-security-center"></a>Solutions de contrôle partenaire avec le centre de sécurité Azure

Ce document vous explique comment surveiller l’état de santé de vos solutions de partenaire dans le centre de sécurité Azure.

> [AZURE.NOTE] Ce document présente le service à l’aide d’un exemple de déploiement. Il ne s’agit pas d’un guide pas à pas.

## <a name="monitoring-partner-solutions"></a>Surveillance des solutions partenaires

La mosaïque de **solutions des partenaires** sur la lame du **Centre de sécurité** vous permet de vous moniteur en un clin de œil l’état de vos solutions de partenaires intégrée avec votre abonnement Azure.
![Mosaïque de solutions des partenaires][1]

La mosaïque de **solutions partenaires** affiche le nombre de solutions de partenaire et un récapitulatif de ces solutions.

Le **statut** d’une solution partenaire peut être :

- Protégé (vert) : il n’y a aucun problème d’intégrité.
- Dérèglement (rouge) - il existe un problème de santé qui exige une attention immédiate.
- Arrêté le reporting (orange) : la solution a arrêté son état de santé de création de rapports.
- Statut de protection inconnu (orange) - l’état de santé de la solution est inconnu pour l’instant en raison d’un processus d’échec de l’ajout d’une nouvelle ressource à la solution existante.
- Pas signalé (gris) - la solution n’a pas signalé quelque chose encore, le statut de la solution peut être non signalé si elle vient d’être connecté et toujours déploie.

S’il n’existe aucune solution intégrée avec votre abonnement la mosaïque indique qu’il n’y a pas de solutions. Sélectionnant la mosaïque de **solutions partenaires** vous permettra d’ouvrir la lame de **recommandations** pour déployer des solutions de sécurité des partenaires.
![Solutions partenaires][2]

Pour afficher l’état de santé de vos solutions de partenaires :

1. Sélectionnez la mosaïque de **solutions partenaires** . Une blade s’ouvre en affichant une liste de vos solutions partenaire connecté au centre de sécurité.
![Solutions partenaires][3]

2. Sélectionnez une solution partenaire. Dans cet exemple, permet de sélectionner la solution **F5-WAF2** .  Une blade s’ouvre pour afficher que l’état de la solution du partenaire et de la solution de ressources associées. Sélectionnez la **console de la Solution** pour ouvrir l’environnement de gestion de partenaire pour cette solution.
![Détail des solutions partenaires][4]

3. Revenir à la lame de **WAF2-F5** , puis sélectionnez le **lien app**. La blade **d’Applications du lien** s’ouvre. Ici vous pouvez connecter des ressources à la solution partenaire.
![Lien ressources solution partenaire][5]

## <a name="see-also"></a>Voir aussi
Dans ce document, vous ont été présentées pour la mosaïque de **Solutions partenaires** dans le centre de sécurité. Pour en savoir plus sur le centre de sécurité, consultez les rubriques suivantes :

- [Définition des stratégies de sécurité dans le centre de sécurité Azure](security-center-policies.md) , découvrez comment configurer les stratégies de sécurité pour vos abonnements Azure et les groupes de ressources.
- [Gestion des recommandations de sécurité dans le centre de sécurité Azure](security-center-recommendations.md) , découvrez comment les recommandations vous aident à protéger vos ressources d’Azure.
- [Sécurité contrôle d’état dans le centre de sécurité Azure](security-center-monitoring.md) , découvrez comment surveiller la santé de vos ressources d’Azure.
- [Gérer et répondre aux alertes de sécurité dans le centre de sécurité Azure](security-center-managing-and-responding-alerts.md) , découvrez comment gérer et répondre aux alertes de sécurité.
- [Forum aux questions sur Centre de sécurité Azure](security-center-faq.md) : Forum aux questions sur l’utilisation du service de recherche.
- [Blog sur la sécurité d’Azure](http://blogs.msdn.com/b/azuresecurity/) , obtenir les dernières informations de sécurité Azure et les informations.

<!--Image references-->
[1]: ./media/security-center-partner-solutions/partner-solutions-tile.png
[2]: ./media/security-center-partner-solutions/no-partner-solutions-to-display.png
[3]: ./media/security-center-partner-solutions/partner-solutions.png
[4]: ./media/security-center-partner-solutions/partner-solutions-detail.png
[5]: ./media/security-center-partner-solutions/link-applications.png
