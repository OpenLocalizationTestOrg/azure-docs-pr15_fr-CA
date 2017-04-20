
<properties
    pageTitle="Azure Active Directory hybride identité considérations de conception - déterminer les besoins d’incidents rResponse | Configuration requise de Microsoft Azure "
    description="Déterminer les capacités de surveillance et de reporting de la solution d’identité hybride qui peuvent être exploitées par informatique pour effectuer des actions à identifier et à atténuer des menaces potentielles"
    documentationCenter=""
    services="active-directory"
    authors="billmath"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
    ms.date="08/08/2016"
    ms.author="billmath"/>

# <a name="determine-incident-response-requirements-for-your-hybrid-identity-solution"></a>Déterminer les exigences en matière de réponse aux incidents de votre solution d’identité hybride

Les organisations de taille moyennes ou grandes probablement aura une [réponse aux incidents de sécurité](https://technet.microsoft.com/library/cc700825.aspx) en place pour IT prendre en conséquence des actions au niveau de l’incident. Le système de gestion des identités est un composant important dans le processus de réponse aux incidents, car elle peut être utilisée pour aider à identifier qui a effectué une action spécifique par rapport à la cible. La solution hybride d’identité doit être en mesure de fournir des fonctionnalités de surveillance et de reporting qui peuvent être exploitées par informatique pour effectuer des actions à identifier et à atténuer une menace potentielle. Dans un plan de réponse aux incidents, vous devez les phases suivantes dans le cadre du plan :

1.  Évaluation initiale.
2.  Communication de l’incident.
3.  Limitation des dommages et réduction des risques.
4.  Identification de ce qu’il a été compromis et la gravité.
5.  Conservation des preuves.
6.  Notification aux parties intéressées.
7.  Récupération du système.
8.  Documentation.
9.  Évaluation des dommages et des coûts.
10. Révision du plan et des processus.

Lors de l’identification de ce qu’elle a été compromis et gravité-phase, il est nécessaire d’identifier les systèmes qui ont été compromis, les fichiers qui ont accédé et déterminent la sensibilité de ces fichiers. Votre système d’identité hybride doit être en mesure de répondre à ces impératifs pour vous aider à identifier l’utilisateur qui a effectué ces modifications. 

## <a name="monitoring-and-reporting"></a>Surveillance et reporting
Le système d’identité autant de fois peut également aider dans la phase d’évaluation initiale, surtout si le système a généré dans l’audit et de génération de rapports. Au cours de l’évaluation initiale, IT Admin doit être en mesure d’identifier une activité suspecte ou le système doit être en mesure de déclencheur qu'il automatiquement basé sur une tâche préconfigurée. De nombreuses activités peuvent indiquer une attaque de possible, mais dans d’autres cas, un système mal configuré peut entraîner un nombre de faux positifs dans un système de détection des intrusions. 

Le système de gestion d’identité doit aider les administrateurs informatiques pour identifier et signaler les activités suspectes. Généralement, ces prescriptions techniques peuvent être satisfaites par tous les systèmes de surveillance et ayant une fonction de reporting qui peut mettre en évidence les menaces potentielles. Utilisez les questions ci-dessous pour vous aider à concevoir votre solution d’identité hybride en tenant compte d’exigences réponse aux incidents :

- Votre entreprise a d’une réponse aux incidents de sécurité en place ?
 - Si Oui, le système de gestion d’identité en cours est utilisé dans le cadre du processus ?
- Votre société a besoin pour identifier les tentatives de connexion suspectes d’utilisateurs sur différents périphériques ?
- Votre société a besoin pour détecter d’éventuels compromis de l’utilisateur ?
- Votre entreprise a-t-elle besoin d’auditer les accès et les actions de l’utilisateur ?
- Votre société a besoin de savoir quand un utilisateur réinitialiser son mot de passe ?

## <a name="policy-enforcement"></a>Application des stratégies

Lors de la limitation des dommages et réduction de risques en phase, il est important de réduire rapidement les effets réels et potentiels d’une attaque. Cette action, vous jouerez à ce stade peut faire la différence entre un mineur et un majeur. La réponse exacte dépend de votre organisation et la nature de l’attaque subie. Si l’évaluation initiale de conclure qu’un compte a été compromis, vous devrez appliquer une stratégie pour bloquer ce compte. C’est juste un exemple dans lequel le système de gestion d’identité est exploité. Utilisez les questions ci-dessous pour vous aider à concevoir votre solution d’identité hybride tout en prenant en considération le mode d’application des stratégies pour réagir à un incident en cours :

- Votre société possède-t-elle les stratégies en place pour empêcher les utilisateurs d’accès réseau si nécessaire ?
 - Si Oui, la solution en cours s’intègre avec le système de gestion d’identité hybride que vous allez adopter ?
- Votre société a besoin d’appliquer un accès conditionnel pour les utilisateurs qui sont dans la mise en quarantaine ? 
 
>[AZURE.NOTE]
Veillez à prendre des notes de chaque réponse et de comprendre le raisonnement derrière la réponse. [Définir une stratégie de protection données](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md) risque de dépasser les options disponibles et les avantages et les inconvénients de chaque option.  En ayant répondu à ces questions que vous sélectionnez option les adaptée à votre entreprise a besoin.

## <a name="next-steps"></a>Étapes suivantes
[Définir la stratégie de protection des données](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md)

## <a name="see-also"></a>Voir aussi
[Vue d’ensemble des considérations relatives à la conception](active-directory-hybrid-identity-design-considerations-overview.md)
