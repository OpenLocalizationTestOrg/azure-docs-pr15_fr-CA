<properties
   pageTitle="Le centre de sécurité Azure menace rapport | Microsoft Azure"
   description="Ce document vous aide à utiliser Azure centre de menaces intelligente des rapports de sécurité au cours d’une enquête pour trouver plus d’informations sur une alerte de sécurité."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/17/2016"
   ms.author="yurid"/>

# <a name="azure-security-center-threat-intelligence-report"></a>Rapport de menace Azure le centre de sécurité
Ce document explique comment Azure centre de menaces intelligente des rapports de sécurité peut vous aider à en savoir plus sur une menace qui a généré une alerte de sécurité.

## <a name="what-is-a-threat-intelligence-report"></a>Ce qu’est un rapport de menace ?
Détection des menaces de sécurité Centre fonctionne en analysant les informations sur la sécurité de vos ressources d’Azure, le réseau et les solutions des partenaires connectés. Il analyse ces informations, souvent mise en corrélation des informations provenant de plusieurs sources, d’identifier les menaces. Ce processus fait partie des [fonctionnalités de détection](security-center-detection-capabilities.md)centre de sécurité. 

Lorsque le centre de sécurité détecte une menace, il déclenchera une [alerte de sécurité](security-center-managing-and-responding-alerts.md), qui contient des informations détaillées sur un événement particulier, y compris des suggestions pour la conversion. Pour faciliter la réponse aux incidents, équipes, analyser et remédier aux menaces, centre de sécurité comprend un rapport de menace qui contient des informations sur la menace a été détectée, y compris des informations telles que les : 

- Identité de l’intrus ou associations (si cette information n’est disponible)
- Objectifs de l’attaquant
- Campagnes d’attaques en cours et historiques (si cette information n’est disponible)
- Les tactiques des pirates, des outils et des procédures
- Indicateurs associés de compromis (IoC) telles que les URL et les hachages de fichier
- Victimology, qui est de l’industrie et la prévalence géographique pour vous aider à déterminer si vos ressources Azure sont exposés
- Réduction des risques et mise à jour des informations

>[AZURE.NOTE] La quantité d’informations dans n’importe quel rapport donné varie ; le niveau de détail est basé sur les activités et la récurrence du logiciel malveillant.

Centre de sécurité propose trois types de rapports de menace, qui peuvent varier en fonction de l’attaque. Les rapports disponibles sont les suivantes :

- **Rapport d’activité de groupe**: fournit tels qu’approfondies dans les attaquants, leurs objectifs et les tactiques.
- **Rapport de la campagne**: se concentre sur les détails des campagnes d’attaques spécifiques. 
- **Rapport de synthèse de menaces**: couvre tous les éléments dans les deux précédents rapports.

Ce type d’informations est très utile au cours du processus de [réponse aux incidents](security-center-incident-response.md) , dans le cas où il y a une enquête en cours pour comprendre la source de l’attaque, les motivations de l’attaquant et procédure à suivre pour atténuer ce problème de déplacement vers l’avant. 

## <a name="how-to-access-the-threat-intelligence-report"></a>Comment faire pour accéder au rapport d’analyse décisionnelle de menace ?

Vous pouvez consulter vos alertes actuelles en examinant la mosaïque **d’alertes de sécurité** . Ouvrez le portail Azure et suivez les étapes ci-dessous pour plus de détails sur chaque alerte :

1. Sur le tableau de bord du centre de sécurité, vous verrez la mosaïque **d’alertes de sécurité** .

2. Cliquez sur la mosaïque pour ouvrir la blade **d’alertes de sécurité** qui contient plus de détails sur les alertes, cliquez sur l’alerte de sécurité que vous souhaitez obtenir plus d’informations sur.

    ![Alertes de sécurité](./media/security-center-threat-report/security-center-threat-report-fig1.png)

3. Dans ce cas la lame de **processus suspects exécuté** affiche les détails de l’alerte comme illustré dans la figure ci-dessous :

    ![Detais alerte de sécurité](./media/security-center-threat-report/security-center-threat-report-fig2.png)

4.  La quantité d’informations disponibles pour chaque alerte de sécurité varient selon le type d’alerte. Dans le champ des **rapports** , vous avez un lien vers le rapport de menace. Cliquez dessus et une autre fenêtre de navigateur s’affiche avec le fichier PDF.

    ![Sélection du stockage](./media/security-center-threat-report/security-center-threat-report-fig3.png)

À partir de là, vous pouvez télécharger le fichier PDF ce rapport et en savoir plus sur le problème de sécurité a été détecté et agir en fonction des informations fournies.

## <a name="see-also"></a>Voir aussi

Dans ce document, vous avez appris comment Azure centre de menaces intelligente des rapports de sécurité peut aider au cours d’une enquête sur les alertes de sécurité. Pour en savoir plus sur le centre de sécurité Azure, consultez les rubriques suivantes :

- [Forum aux questions du centre de sécurité azure](security-center-faq.md). Forum aux questions sur l’utilisation du service de recherche.
- [Le centre de sécurité Azure en réponse aux incidents](security-center-incident-response.md)
- [Capacités de détection Azure le centre de sécurité](security-center-detection-capabilities.md)
- [Guident de planification du centre de sécurité azure et opérations](security-center-planning-and-operations-guide.md). Découvrez comment planifier et comprendre les considérations de conception à adopter le centre de sécurité Azure.
- [Gérer et répondre aux alertes de sécurité dans le centre de sécurité Azure](security-center-managing-and-responding-alerts.md). Apprenez à gérer et répondre aux alertes de sécurité.
- [Gestion des incidents de sécurité dans le centre de sécurité Azure](security-center-incident.md)
- [Blog sur la sécurité azure](http://blogs.msdn.com/b/azuresecurity/). Trouver des billets de blog sur la sécurité Azure et de conformité.
