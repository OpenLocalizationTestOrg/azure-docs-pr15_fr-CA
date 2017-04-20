<properties
   pageTitle="Surveiller et répondre aux alertes de sécurité dans les opérations de sécurité Suite de gestion et de la Solution d’Audit | Microsoft Azure"
   description="Ce document vous aide à utiliser l’option d’intelligence de menace disponible dans la sécurité de l’OMS et l’Audit pour surveiller et répondre aux alertes de sécurité."
   services="operations-management-suite"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="operations-management-suite"
   ms.topic="article" 
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/18/2016"
   ms.author="yurid"/>

# <a name="monitoring-and-responding-to-security-alerts-in-operations-management-suite-security-and-audit-solution"></a>Surveiller et répondre aux alertes de sécurité dans les opérations de gestion Suite de Solution de sécurité et d’Audit

Ce document vous aide à utiliser l’option d’intelligence de menace disponible dans la sécurité de l’OMS et l’Audit pour surveiller et répondre aux alertes de sécurité.

## <a name="what-is-oms"></a>Nouveautés d’OMS ?

Suite de gestion des opérations Microsoft (OMS) est nuage de Microsoft solution de gestion informatique qui vous aide à gérer et protéger vos locaux et cloud d’infrastructure. Pour plus d’informations à propos de l’OMS, consultez l’article [Opérations Management Suite](https://technet.microsoft.com/library/mt484091.aspx).

## <a name="threat-intelligence"></a>Informations sur les menaces

Dans un environnement d’entreprise où les utilisateurs ont accès au réseau large et une gamme de périphériques permet de se connecter aux données d’entreprise, il est impératif que vous pouvez surveiller activement vos ressources et répondre rapidement aux incidents de sécurité. Cela est particulièrement important du point de vue sécurité du cycle de vie car certains cyber-sécurité menaces ne peuvent pas déclencher les alertes ou les activités qui peuvent être identifiées par des contrôles techniques de sécurité traditionnelles. 

À l’aide de l’option **Informations sur les menaces** disponible dans la sécurité de l’OMS et l’Audit, les administrateurs informatiques peuvent identifier les menaces contre l’environnement, par exemple, identifier si un ordinateur particulier fait partie d’un [réseau de robots](https://www.microsoft.com/security/sir/story/default.aspx#!botnetsection). Ordinateurs peuvent devenir des nœuds d’un botnet lorsque illégalement, les attaquants installer des logiciels malveillants qui secrètement se connecte à cet ordinateur à la commande et de contrôle. Il peut également identifier les menaces potentielles en provenance des canaux de communication souterrains, par exemple [darknet](https://www.microsoft.com/security/sir/story/default.aspx#!botnetsection_honeypots_darkents). 

Pour générer cette intelligence de menace, d’Audit et de sécurité de l’OMS utilisent des données provenant de plusieurs sources au sein de Microsoft. D’Audit et de sécurité de l’OMS exploitera ces données pour identifier les menaces potentielles contre votre environnement.

Le volet d’informations sur les menaces est composé par trois options principales :
- Serveurs avec le trafic malveillant
- Types de menaces détectées
- Carte d’intelligence des menaces

> [AZURE.NOTE] pour une vue d’ensemble de toutes ces options, consultez [mise en route avec les opérations de gestion Suite de sécurité et de la Solution d’Audit](oms-security-getting-started.md).

### <a name="responding-to-security-alerts"></a>Réponse aux alertes de sécurité

L’une des étapes d’un processus de [réponse aux incidents de sécurité](https://technet.microsoft.com/library/cc512623.aspx) est d’identifier la gravité du ou des systèmes compromis. Dans cette phase, vous devez effectuer les tâches suivantes :

- Déterminer la nature de l’attaque
- Déterminer le point d’origine de l’attaque
- Le but de l’attaque. Était-elle spécifiquement dirigée contre votre entreprise pour obtenir certaines informations ou était-elle aléatoire ?
- Identifier les systèmes qui ont été compromis.
- Identifier les fichiers qui ont accédé et déterminent la sensibilité de ces fichiers

Vous pouvez exploiter les informations **Sur les menaces** de l’OMS solution de sécurité et d’Audit pour aider à effectuer ces tâches. Suivez les étapes ci-dessous pour accéder à ces options **Sur les menaces** :

1. Dans le tableau de bord **Microsoft Operations Management Suite** principal, cliquez sur mosaïque **d’Audit et de sécurité** .

    ![Sécurité et Audit](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig1.png)

2. Dans le tableau de bord **d’Audit et de sécurité** , vous verrez les options **Sur les menaces** de droite, comme indiqué ci-dessous :

    ![Intel de la menace](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig2-ga.png)

Ces trois mosaïques vous donnera une vue d’ensemble des menaces en cours. Le **serveur avec le trafic malveillant** , que vous serez en mesure d’identifier si il est n’importe quel ordinateur que vous analysez (dans ou en dehors de votre réseau) qui envoie le trafic malveillant à Internet. 

La mosaïque de **types de menaces détectées** , affiche un résumé des menaces qui sont en cours « dans la nature », si vous cliquez sur ce carré s’affiche plus de détails sur ces menaces comme indiqué ci-dessous :

![Types de menaces détectés](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig3.png)

Vous pouvez extraire le plus d’informations sur chaque menace en cliquant dessus. L’exemple ci-dessous montre plus de détails sur le réseau de robots :

![plus d’informations sur une menace](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig4.png)

Comme indiqué au début de cette section, ces informations peuvent être très utiles au cours d’un incident de réponse aux incidents. Il peut également être important pendant une investigation informatique, où vous devez rechercher la source de l’attaque, le système a été compromis et de la barre de planning. Dans ce rapport, vous pouvez facilement identifier certains détails de la clé sur l’attaque, telles que : la source de l’attaque, l’adresse IP locale qui a été compromis et l’état de session actuel de la connexion. 

La **carte d’intelligence des menaces** vous aidera à identifier l’emplacement en cours dans le monde qui ont le trafic malveillant. Il y a orange (entrant) et flèches (sortantes) rouges dans ce mappage qui identifient la direction du trafic, si vous cliquez sur l’une de ces flèches, il affiche le type de menace et de la direction du trafic, comme indiqué ci-dessous :

![carte intel des menaces](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig5.png)

> [AZURE.NOTE] Vous pouvez voir une démonstration sur l’utilisation de cette fonctionnalité pendant un incident en regardant la présentation [atténuer les menaces de sécurité datacenter avec enquête interactive à l’aide de la Suite de gestion des opérations](https://myignite.microsoft.com/videos/5000) , les processus de réponse envoyées à Microsoft Ignite.

## <a name="see-also"></a>Voir aussi

Dans ce document, vous avez appris comment utiliser l’option **Informations sur les menaces** dans la solution d’Audit et de sécurité de l’OMS pour répondre aux alertes de sécurité. Pour en savoir plus sur la sécurité de l’OMS, consultez les articles suivants :

- [Vue d’ensemble de la Suite de gestion (OMS) opérations](operations-management-suite-overview.md)
- [Mise en route avec les opérations de gestion Suite de Solution de sécurité et d’Audit](oms-security-getting-started.md)
- [Surveillance des ressources dans des opérations de sécurité Suite de gestion et de la Solution d’Audit](oms-security-monitoring-resources.md)
