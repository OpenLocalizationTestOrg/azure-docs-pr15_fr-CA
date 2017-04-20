<properties
   pageTitle="Surveillance des ressources dans des opérations de sécurité Suite de gestion et de la Solution d’Audit | Microsoft Azure"
   description="Ce document vous aide à utiliser la sécurité de l’OMS et d’Audit permettant de surveiller vos ressources et identifier les problèmes de sécurité."
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

# <a name="monitoring-resources-in-operations-management-suite-security-and-audit-solution"></a>Surveillance des ressources dans des opérations de gestion Suite de Solution de sécurité et d’Audit

Ce document vous aide à utiliser OMS fonctions de sécurité et d’Audit pour surveiller vos ressources et identifier les problèmes de sécurité.

## <a name="what-is-oms"></a>Nouveautés d’OMS ?

Suite de gestion des opérations Microsoft (OMS) est nuage de Microsoft solution de gestion informatique qui vous aide à gérer et protéger vos locaux et cloud d’infrastructure. Pour plus d’informations à propos de l’OMS, consultez l’article [Opérations Management Suite](https://technet.microsoft.com/library/mt484091.aspx).

## <a name="monitoring-resources"></a>Surveillance des ressources

Chaque fois que possible, vous devez empêcher les incidents de sécurité en premier lieu. Toutefois, il est impossible d’empêcher tous les incidents de sécurité. Lorsqu’un incident de sécurité se produit-il, vous devez minimiser son impact.  Il existe trois recommandations critiques qui peuvent être utilisées pour réduire le nombre et l’impact des incidents de sécurité :

- Évaluer régulièrement les vulnérabilités de votre environnement.
- Vérifier régulièrement tous les systèmes informatiques et périphériques réseau pour s’assurer qu’ils disposent tous des derniers correctifs.
- Vérifier régulièrement tous les journaux et mécanismes de journalisation, y compris les journaux des événements de système d’exploitation, journaux spécifiques aux applications et les journaux système de détection d’intrusion.

Sécurité de l’OMS et d’Audit solution permet au département informatique d’analyser activement toutes les ressources, ce qui peuvent aider à minimiser l’impact des incidents de sécurité. D’Audit et de sécurité de l’OMS a des domaines de sécurité qui peuvent être utilisés pour la surveillance des ressources. Les domaines de la sécurité offre un accès rapide à des options, pour contrôler la sécurité les domaines suivants seront traités dans plus de détails :

- Évaluation des logiciels malveillants
- Évaluation de la mise à jour
- Identités et des accès

> [AZURE.NOTE] pour une vue d’ensemble de toutes ces options, consultez [mise en route avec les opérations de gestion Suite de sécurité et de la Solution d’Audit](oms-security-getting-started.md).

### <a name="monitoring-system-protection"></a>Protection du système de surveillance

Dans une approche défense en profondeur, chaque couche de protection est important pour l’état global de la sécurité de vos ressources. Ordinateurs avec menaces détectées et avec une protection insuffisante sont affichées dans la mosaïque de l’évaluation des logiciels malveillants dans les domaines de sécurité. En utilisant les informations sur l’évaluation de logiciel malveillant, vous pouvez identifier un plan pour appliquer la protection pour les serveurs qui en ont besoin. Pour accéder à cette option, procédez comme suit :

1. Dans le tableau de bord **Microsoft Operations Management Suite** principal, cliquez sur mosaïque **d’Audit et de sécurité** .

    ![Sécurité et Audit](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig1.png)

2. Dans le tableau de bord **d’Audit et de sécurité** , cliquez sur **Évaluation de logiciel anti-programme malveillant** sous **Domaines de sécurité**. Le tableau de bord **d’Évaluation des logiciels anti-programmes malveillants** s’affiche comme illustré ci-dessous :

![Évaluation des logiciels malveillants](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig2-ga.png)

Vous pouvez utiliser le tableau de bord **d’Évaluation de logiciels malveillants** pour identifier les problèmes de sécurité suivants :

- **Menace Active**: les ordinateurs qui ont été compromis et disposent les menaces actives dans le système.
- **Les menaces de Remediated**: les ordinateurs qui ont été compromis, mais les menaces ont été résolues.
- **Signature obsolètes**: ordinateurs disposant d’une protection contre les logiciels malveillants activée, mais la signature est obsolète.
- **Pas de protection en temps réel**: les ordinateurs qui n’ont pas installé contre les logiciels malveillants.

### <a name="monitoring-updates"></a>Surveillance des mises à jour 

Appliquer les dernières mises à jour de sécurité est des raisons de sécurité, et il doit être incorporé dans votre stratégie de gestion des mises à jour. Le service Agent de surveillance de Microsoft (HealthService.exe) lit les informations de mise à jour à partir d’ordinateurs gérés, puis envoie ces informations mises à jour au service OMS dans le nuage pour le traitement. Le service Agent de surveillance de Microsoft est configuré comme un service automatique et il doit être toujours en cours d’exécution sur l’ordinateur cible.

![surveillance des mises à jour](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig3.png)

La logique est appliquée pour les données de mise à jour et le service en nuage enregistre les données. Si les mises à jour manquantes sont trouvés, ils sont affichés sur le tableau de bord de **mises à jour** . Vous pouvez utiliser le tableau de bord de **mises à jour** pour fonctionner avec les mises à jour manquantes et de développer un plan pour les appliquer aux serveurs qui en ont besoin. Suivez les étapes ci-dessous pour accéder aux tableaux de bord le **mises à jour** :

1. Dans le tableau de bord **Microsoft Operations Management Suite** principal, cliquez sur mosaïque **d’Audit et de sécurité** .
2. Dans le tableau de bord **d’Audit et de sécurité** , cliquez sur **Évaluation de la mise à jour** dans **Les domaines de sécurité**. Le tableau de bord de mise à jour s’affiche comme illustré ci-dessous :

![évaluation de la mise à jour](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig4.png)

Dans ce tableau de bord, vous pouvez effectuer une évaluation de la mise à jour pour comprendre l’état actuel de vos ordinateurs et de contrer les menaces les plus critiques. À l’aide de la mosaïque de **critique ou mises à jour de sécurité** , les administrateurs informatiques pourront accéder à des informations détaillées sur les mises à jour manquantes, comme indiqué ci-dessous :

![résultat de la recherche](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig5.png)

Ce rapport inclut des informations critiques qui peuvent être utilisées pour identifier le type de menace, ce système est vulnérable, qui inclut les articles Microsoft KB associés à la mise à jour de sécurité et le Bulletin MS qui a le plus d’informations sur la vulnérabilité.

### <a name="monitoring-identity-and-access"></a>Surveillance des identités et des accès

Avec les utilisateurs qui travaillent à partir de n’importe où, à l’aide de différents périphériques et accéder à une grande quantité d’applications de cloud et sur site, il est impératif de que leurs informations d’identification sont protégées. Les attaques contre le vol d’informations d’identification sont celles dans lesquelles un attaquant initialement accède aux informations d’identification d’un utilisateur normal pour accéder à un système au sein du réseau. Souvent, ce type d’attaque initiale consiste uniquement à obtenir l’accès au réseau, l’objectif ultime est de découvrir des comptes à privilèges. 

Les attaquants resteront dans le réseau, à l’aide d’outillage pour extraire des informations d’identification à partir de sessions d’autres comptes de session disponible gratuitement. En fonction de la configuration du système, ces informations d’identification peuvent être extraites sous la forme de hachages, les tickets ou même des mots de passe.  

> [AZURE.NOTE] les ordinateurs qui sont directement exposés à Internet seront affiche de tentatives ayant échouées qui tentent de se connecter à l’aide de toutes sortes de noms d’utilisateur connus (par exemple, administrateur). Dans la plupart des cas, il est OK si les noms d’utilisateurs connus ne sont pas utilisées et si le mot de passe est assez fort.

Il est possible d’identifier ces intrus avant qu’ils compromettent un compte disposant de privilèges. Vous pouvez exploiter **les solutions de d’Audit et de sécurité de l’OMS** pour surveiller des identités et des accès. Suivez les étapes ci-dessous pour accéder à du tableau de bord **des identités et des accès** :

1. Dans le tableau de bord **Microsoft Operations Management Suite** principal, cliquez sur sécurité et Audit en mosaïque.
2. Dans le tableau de bord **d’Audit et de sécurité** , cliquez sur des **identités et des accès** dans **Les domaines de sécurité**. Le tableau de bord **des identités et des accès** s’affiche comme illustré ci-dessous :

![identités et des accès](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig6-ga.png)

Dans le cadre de votre stratégie de surveillance régulière, vous devez inclure le contrôle d’identité. Administrateur informatique doit se présenter s’il y a un nom d’utilisateur valide spécifique qui possède de nombreuses tentatives. Cela peut indiquer un agresseur ayant acquis le nom d’utilisateur réel et essayez de force brute ou un outil automatique utilise codée de manière irréversible mot de passe qui a expiré.

Cette activation du tableau de bord informatique pour identifier rapidement les menaces potentielles liées à des identités et des accès aux ressources de l’entreprise. Il est notamment important également identifier les tendances potentiels, par exemple dans la mosaïque les ouvertures de session dans le temps, vous pouvez voir sur la période de temps combien de fois une tentative d’ouverture de session a été effectuée. Dans ce cas, l’ordinateur **serveur de fichiers** a reçu 35 tentatives d’ouverture de session. Vous pouvez explorer plus de détails sur cet ordinateur en cliquant dessus. 

![plus de détails](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig7-new.png)

Le rapport généré pour cet ordinateur apporte des informations précieuses sur ce modèle. Remarqué que la colonne **compte** vous donne le compte d’utilisateur qui a été utilisé pour tenter d’accéder au système, la colonne **TIMEGENERATED** vous donne l’intervalle dans lequel la tentative a été effectuée et la colonne **LOGONTYPENAME** vous indique l’emplacement où cette tentative a été effectuée. Si ces tentatives étaient effectuées localement dans le système par un programme, la colonne **processus** lorsque vous souhaitez afficher le nom du processus. Dans les scénarios où la tentative d’ouverture de session est provenant d’un programme, vous avez déjà le nom du processus, et maintenant vous pouvez approfondir l’enquête dans le système cible.

## <a name="see-also"></a>Voir aussi

Dans ce document, vous avez appris comment utiliser la sécurité de l’OMS et d’Audit pour surveiller les ressources de votre solution. Pour en savoir plus sur la sécurité de l’OMS, consultez les articles suivants :

- [Vue d’ensemble de la Suite de gestion (OMS) opérations](operations-management-suite-overview.md)
- [Mise en route avec les opérations de gestion Suite de Solution de sécurité et d’Audit](oms-security-getting-started.md)
- [Surveiller et répondre aux alertes de sécurité dans les opérations Management Suite Solution de sécurité et d’Audit](oms-security-responding-alerts.md)