<properties
   pageTitle="Gestion des incidents de sécurité dans le centre de sécurité Azure | Microsoft Azure"
   description="Ce document vous aide à utiliser les fonctionnalités du centre de sécurité Azure à gérer les incidents de sécurité."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.topic="hero-article"
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/18/2016"
   ms.author="yurid"/>

# <a name="handling-security-incident-in-azure-security-center"></a>Gestion des incidents de sécurité dans le centre de sécurité Azure 
Triage et analyse des alertes de sécurité peuvent prendre beaucoup de temps pour même les plus compétents analystes en sécurité, et pour la plupart, il est difficile de savoir où commencer. À l’aide [d’analytique](security-center-detection-capabilities.md) pour connecter les informations entre les différentes [alertes de sécurité](security-center-managing-and-responding-alerts.md), le centre de sécurité peut vous offrir une vue unique d’une campagne d’attaque et de toutes les alertes connexes – vous pouvez rapidement comprendre les actions que l’attaquant a eu et quelles ressources d’impactés.

Ce document explique comment utiliser la fonction d’alerte de sécurité dans le centre de sécurité pour vous aider à gérer les incidents de sécurité.


## <a name="what-is-a-security-incident"></a>En quoi consiste un incident de sécurité ?

Dans le centre de sécurité, un incident de sécurité est un regroupement de toutes les alertes pour une ressource qui s’alignent avec les modèles de [chaîne de kill](https://blogs.technet.microsoft.com/office365security/addressing-your-cxos-top-five-cloud-security-concerns/) . Incidents apparaissent dans les [Alertes de sécurité](security-center-managing-and-responding-alerts.md) mosaïque et lame. Un Incident permettra de connaître la liste des alertes associées, ce qui vous permet d’obtenir plus d’informations sur chaque occurrence.

## <a name="managing-security-incidents"></a>Gestion des incidents de sécurité

Vous pouvez consulter les incidents de sécurité actuel en consultant la mosaïque d’alertes de sécurité. Accéder au portail Azure et suivez les étapes ci-dessous pour plus de détails sur chaque incident de sécurité :

1. Sur le tableau de bord du centre de sécurité, vous verrez la mosaïque **d’alertes de sécurité** .

    ![Mosaïque d’alertes de sécurité dans le centre de sécurité](./media/security-center-incident/security-center-incident-fig1.png)

2.  Cliquez sur cette mosaïque pour développer et si un incident de sécurité est détecté, il apparaît sous le graphique d’alertes de sécurité comme indiqué ci-dessous :

    ![Incident de sécurité](./media/security-center-incident/security-center-incident-fig2.png)

3.  Notez que la description d’incidents de sécurité présente une icône différente par rapport aux autres alertes. Cliquez dessus pour afficher plus de détails sur cet incident.

    ![Incident de sécurité](./media/security-center-incident/security-center-incident-fig3.png)

4.  Sur l' **incident** lame vous verrez plus détails concernant cet incident de sécurité, ce qui inclut sa description complète, sa gravité (qui dans ce cas est élevée), son état actuel (dans ce cas il est toujours *actif*, qui implique que l’utilisateur n’a pas effectué une action pour *faire disparaître* , cela en cliquant avec le bouton droit sur l’incident dans la blade **d’alertes de sécurité** ) , la ressource attaquée (dans ce cas *VM1*), les étapes de la mise à jour de l’incident, et dans le volet inférieur, vous avez les alertes qui ont été inclus dans cet incident. Si vous souhaitez obtenir plus d’informations sur chaque alerte, cliquez simplement sur une autre lame et s’ouvre, comme illustré ci-dessous :

    ![Incident de sécurité](./media/security-center-incident/security-center-incident-fig4.png)

Les informations sur cette blade varient en fonction de l’alerte. Pour plus d’informations sur la gestion de ces alertes, lisez [gérer et répondre aux alertes de sécurité dans le centre de sécurité Azure](security-center-managing-and-responding-alerts.md) . Certaines considérations importantes concernant cette fonctionnalité :

- Un nouveau filtre vous permet de personnaliser votre vue d’incidents uniquement, alertes uniquement ou les deux. 
- Le même message d’alerte peut exister dans le cadre d’un Incident (le cas échéant), ainsi que pour être visible sous la forme d’une alerte autonome. 
- Faire disparaître un incident fera disparaître pas les alertes associées.

## <a name="see-also"></a>Voir aussi

Dans ce document, vous avez appris comment utiliser la fonctionnalité d’incidents de sécurité dans le centre de sécurité. Pour en savoir plus sur le centre de sécurité, consultez les rubriques suivantes :

- [Gérer et répondre aux alertes de sécurité dans le centre de sécurité Azure](security-center-managing-and-responding-alerts.md)
- [Le centre de sécurité Azure des fonctionnalités de détection](security-center-detection-capabilities.md)
- [Guide des opérations et la planification du centre de sécurité Azure](security-center-planning-and-operations-guide.md)
- [Gérer et répondre aux alertes de sécurité dans le centre de sécurité Azure](security-center-managing-and-responding-alerts.md)
- [Forum aux questions sur Centre de sécurité azure](security-center-faq.md)--rechercher fréquemment posées à propos de l’utilisation du service.
- Billets de [blog sur la sécurité d’azure](http://blogs.msdn.com/b/azuresecurity/)--blog de recherche sur la conformité et la sécurité Azure.
