<properties
   pageTitle="À l’aide du centre de sécurité Azure une réponse aux incidents | Microsoft Azure"
   description="Ce document explique comment utiliser le centre de sécurité Azure pour un scénario de réponse aux incidents."
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
   ms.date="09/20/2016"
   ms.author="yurid"/>

# <a name="using-azure-security-center-for-an-incident-response"></a>À l’aide du centre de sécurité Azure une réponse aux incidents
De nombreuses organisations comment répondre aux incidents de sécurité qu’après avoir subi une attaque. Pour réduire les coûts et les dommages, il est important d’avoir une réponse aux incidents plan en place avant une attaque a lieu. Vous pouvez utiliser le centre de sécurité Azure dans différentes étapes de la réponse à un incident.

## <a name="incident-response-planning"></a>Planification de la réponse aux incidents

Un plan efficace dépend de trois fonctions principales : la possibilité de protéger, détecter et répondre aux menaces. Protection sur la prévention des incidents, la détection est sur l’identification précoce des menaces, et réponse sur la suppression de l’attaquant et la restauration des systèmes pour atténuer les conséquences d’une violation.

Cet article utilise les phases de réponse aux incidents de sécurité à partir de l’article de la [Réponse de sécurité Microsoft Azure dans le nuage](https://gallery.technet.microsoft.com/Azure-Security-Response-in-dd18c678) , comme illustré dans le diagramme suivant :

![Cycle de vie de réponse aux incidents](./media/security-center-incident-response/security-center-incident-response-fig1.png)

Vous pouvez utiliser le centre de sécurité au cours des phases de détecter, d’évaluer et de diagnostic. Voici des exemples de la façon dont le centre de sécurité peut s’avérer utile au cours des trois phases initiale de réponse aux incidents :

- **Détecter**: passez en revue la première indication d’une enquête de l’événement.
    - Exemple : révision la vérification initiale qu’une alerte de sécurité de haute priorité a été déclenchée dans le tableau de bord du centre de sécurité.
- **Évaluation**: l’évaluation initiale pour obtenir plus d’informations sur les activités suspectes.
    - Exemple : obtenir plus d’informations sur l’alerte de sécurité.
- **Diagnostiquer**: mener une investigation technique et d’identifier les stratégies de contenance, des risques et de solution de contournement.
    - Exemple : suivez les étapes de mise à jour décrites par le centre de sécurité dans cette alerte de sécurité particulier.

Le scénario qui suit vous montre comment tirer parti du centre de sécurité au cours des phases de détecter, d’évaluer et de diagnostiquer/répondre d’un incident de sécurité. Dans le centre de sécurité, un [incident de sécurité](security-center-incident.md) est un regroupement de toutes les alertes pour une ressource qui s’alignent avec les modèles de [chaîne de kill](https://blogs.technet.microsoft.com/office365security/addressing-your-cxos-top-five-cloud-security-concerns/) . Incidents apparaissent dans les [alertes de sécurité](security-center-managing-and-responding-alerts.md) mosaïque et lame. Un incident révèle la liste des alertes associées, ce qui vous permet d’obtenir plus d’informations sur chaque occurrence. Centre de sécurité présente également autonome des alertes de sécurité qui peuvent également être utilisés pour suivre une activité suspecte.

## <a name="scenario"></a>Scénario

Récemment certaines de leurs ressources sur site Contoso la migration vers Azure, y compris certains basé sur une machine virtuelle métier de charges de travail et les bases de données SQL. Actuellement, Core ordinateur réponse équipe (incidents Contoso de sécurité) a un problème de rechercher les problèmes de sécurité en raison de l’intelligence de sécurité n’étant ne pas intégré avec leurs outils de réponse aux incidents en cours. Ce manque d’intégration présente un problème lors de l’étape de détection (trop de faux positifs), ainsi qu’au cours des phases d’évaluation et diagnostiquer. Dans le cadre de cette migration, a décidé de participer pour le centre de sécurité afin de les aider à résoudre le problème.

La première phase de cette migration terminée après qu’elles onboarded toutes les ressources et traité toutes les recommandations de sécurité du centre de sécurité. Contoso CSIRT est le centre névralgique de traitement des incidents de sécurité informatique. L’équipe se compose d’un groupe de personnes chargé du traitement des incidents de sécurité. Les membres de l’équipe ont clairement défini les droits pour vous assurer qu’aucune zone de réponse ne reste non couverts.

Pour ce scénario, nous allons le focus sur les rôles du personnage suivants qui font partie de Contoso CSIRT :

![Cycle de vie de réponse aux incidents](./media/security-center-incident-response/security-center-incident-response-fig2.png)

Suzanne est dans les opérations de sécurité. Ses responsabilités sont les suivantes :

- Surveillance et répondre aux menaces de sécurité en continu.
- Escalade au propriétaire de la charge de travail de nuage ou analyste de la sécurité en fonction des besoins.

SAM est un analyste de la sécurité et ses responsabilités incluent :

- Source d’une attaque.
- Alertes appropriées.
- Travaillez avec les propriétaires de la charge de travail pour déterminer et appliquer des mesures d’atténuation.

Comme vous pouvez le voir, Laura et Sam ont différentes responsabilités, et ils doivent travailler ensemble pour partager des informations du centre de sécurité.

## <a name="recommended-solution"></a>Solution recommandée

Laura et Sam ayant des rôles différents, ils utiliserez différentes zones du centre de sécurité pour obtenir des informations pertinentes pour leurs activités quotidiennes. Laura utiliseront les **alertes de sécurité** dans le cadre de son analyse quotidienne.

![Alertes de sécurité](./media/security-center-incident-response/security-center-incident-response-fig3.png)

Laura utilise pendant les phases de détecter et d’évaluer les alertes de sécurité. Laura après l’évaluation initiale, il peut le transmettre à Sam si une enquête supplémentaire n’est nécessaire. À ce stade, Sam utilise les informations fournies par le centre de sécurité, parfois conjointement avec d’autres sources de données, pour passer à l’étape de diagnostic.


## <a name="how-to-implement-this-solution"></a>La mise en œuvre de cette solution

Pour voir comment utiliser le centre de sécurité Azure dans un scénario de réponse aux incidents, nous la procédure de Judy dans les phases de détecter et d’évaluer et puis afficher la fonction Sam pour diagnostiquer le problème.

### <a name="detect-and-assess-incident-response-stages"></a>Détecter et évaluer les étapes de la réponse aux incidents

Laura connecté au portail Azure et travaille dans la console du centre de sécurité. Dans le cadre de son contrôle des activités de tous les jours, elle route examinant des alertes de sécurité hautement prioritaires, effectuez les opérations suivantes :

1. Cliquez sur la mosaïque **d’alertes de sécurité** et accéder à la lame **d’alertes de sécurité** .
    ![Lame alerte de sécurité](./media/security-center-incident-response/security-center-incident-response-fig4.png)

    > [AZURE.NOTE] Pour ce scénario, Laura va effectuer une évaluation sur l’alerte activité SQL malveillant, comme illustré dans la figure précédente.
2. Cliquez sur l’alerte de **l’activité de SQL malveillantes** et consulter les ressources attaqués de la lame **d’activité de SQL malveillant** :  ![détails de l’Incident](./media/security-center-incident-response/security-center-incident-response-fig5.png)

    Dans cette blade, Laura peut prendre des notes en ce qui concerne les ressources attaqués, comment autant de fois que cette attaque s’est produite, et lorsqu’il a été détecté.
3. Cliquez sur **ressource une attaque** pour obtenir plus d’informations sur ce type d’attaque.

Après avoir lu la description, Suzanne est convaincue qu’il ne s’agit pas d’un faux positif et où elle doit escalader ce cas à Sam.

### <a name="diagnose-incident-response-stage"></a>Diagnostiquer la phase de réponse aux incidents

SAM reçoit le cas de Laura et commence à consulter la procédure de correction suggéré par le centre de sécurité.

![Cycle de vie de réponse aux incidents](./media/security-center-incident-response/security-center-incident-response-fig6.png)

### <a name="additional-resources"></a>Ressources supplémentaires

L’équipe de réponse aux incidents peut également tirer parti de la fonctionnalité de [Sécurité Centre d’alimentation BI](security-center-powerbi.md) pour voir différents types de rapports. Ces rapports peuvent aider les au cours de la nouvelle enquête pour visualiser, analyser et filtrer les recommandations et les alertes de sécurité. Pour les entreprises qui utilisent leurs informations de sécurité et de la solution de gestion (SIEM) d’événements au cours du processus d’enquête, ils peuvent également [intégrer le centre de sécurité avec leur solution](security-center-integrating-alerts-with-log-integration.md). Vous pouvez également intégrer des journaux d’audit Azure et la machine virtuelle (VM) les événements de sécurité à l’aide de l' [outil d’intégration de journal Azure](https://blogs.msdn.microsoft.com/azuresecurity/2016/07/21/microsoft-azure-log-integration-preview/). Pour analyser une attaque, vous pouvez utiliser ces informations en conjonction avec les informations fournies par le centre de sécurité.


## <a name="conclusion"></a>Conclusion

Composition d’une équipe avant la survenance d’un incident est très important de votre organisation et positivement sur la gestion des incidents. Ayant les bons outils pour surveiller les ressources peut aider à cette équipe de prendre des mesures précises pour résoudre un incident de sécurité. Centre de sécurité [des fonctionnalités de détection](security-center-detection-capabilities.md) peut aider rapidement répondre aux incidents de sécurité et de résoudre les problèmes de sécurité.
