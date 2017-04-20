<properties
    pageTitle="Identité de hybride Active Directory Azure considérations sur la conception, de déterminer les tâches de gestion d’identité hybride | Microsoft Azure"
    description="Avec contrôle d’accès conditionnel, Azure Active Directory vérifie les conditions spécifiques que vous choisissez lors de l’authentification de l’utilisateur et avant d’autoriser l’accès à l’application. Lorsque ces conditions sont réunies, l’utilisateur est authentifié et autorisé à accéder à l’application."
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

# <a name="plan-for-hybrid-identity-lifecycle"></a>Plan du cycle de vie des identités hybride 

L’identité est un des fondements de votre stratégie d’accès entreprise mobilité et d’application. Si vous vous connectez à votre appareil mobile ou d’une application de SaaS, votre identité est la clé pour l’accès à tous les éléments. À son niveau le plus élevé, une solution de gestion des identités englobe l’unification et de la synchronisation entre vos référentiels d’identité qui comprend l’automatisation et la centralisation du processus de provisionnement des ressources de. La solution d’identité doit être une identité centralisée sur le nuage et locaux et également utiliser une forme de fédération d’identité pour maintenir l’authentification centralisée et sécurisée de partager et collaborer avec les entreprises et les utilisateurs externes. Ressources comprises entre les systèmes d’exploitation et des applications à des personnes dans ou affilié à une organisation. Structure organisationnelle peut être modifié pour prendre en charge les stratégies et les procédures de mise en service.

Il est également important de disposer d’une solution d’identité destinée à aider vos utilisateurs en leur fournissant des expériences de libre-service pour les maintenir la productivité. Votre solution d’identité est plus robuste si celle-ci permet une ouverture de session unique pour les utilisateurs au sein de toutes les ressources à que dont ils ont besoin d’accéder à tous les administrateurs niveaux peuvent utiliser des procédures standard pour la gestion des informations d’identification de l’utilisateur. Certains niveaux de l’administration peut être réduite ou éliminée, en fonction de la largeur de la solution de gestion de mise en service. En outre, vous pouvez distribuer de manière sécurisée les capacités d’administration, manuellement ou automatiquement, entre diverses organisations. Par exemple, un administrateur de domaine peut servir uniquement les personnes et les ressources de ce domaine. Cet utilisateur peut effectuer des tâches d’administration et d’approvisionnement, mais il n’est pas autorisé à effectuer des tâches de configuration, comme la création de flux de travail.


## <a name="determine-hybrid-identity-management-tasks"></a>Déterminer les tâches de gestion d’identité hybride
Distribuer les tâches d’administration dans votre organisation améliore la précision et l’efficacité de l’administration et améliore l’équilibre de la charge de travail d’une organisation. Voici les données croisées dynamiques qui définissent un système de gestion d’identité fiable.

 ![](./media/hybrid-id-design-considerations/Identity_management_considerations.png)


Pour définir les tâches de gestion d’identité hybride, vous devez comprendre certaines caractéristiques essentielles de l’entreprise qui va être adopté les identités hybride. Il est important de comprendre les référentiels en cours utilisés pour les sources d’identité. En connaissant ces éléments fondamentaux, vous aurez les besoins fondamentaux et selon que vous devez poser des questions plus précises vous conduisant à une décision de conception mieux pour votre solution d’identité.  

Lors de la définition de ces exigences, garantir qu’au moins les questions suivantes sont traitées

- Options de mise en service : 
 - La solution d’identité hybride prend-il en charge une gestion de l’accès robuste et un système d’approvisionnement ?
 - Comment les utilisateurs, les groupes et les mots de passe vont être gérés ?
 - Est la gestion du cycle de vie des identités réactive ? 
      - Combien de temps faut-il suspension de compte mises à jour de mot de passe ?
      
- Gestion des licences : 
 - Est la gestion des licences poignées solution hybride identité ?
     - Si Oui, quelles sont les fonctionnalités disponibles ?
- La solution gère-t-il la gestion des licences de groupe ? 
      -Si Oui, est-il possible de lui affecter un groupe de sécurité ? 
       -Si Oui, le répertoire de nuage affecte automatiquement des licences à tous les membres du groupe ? 
        -Que se passe-t-il si un utilisateur est ensuite ajouté à ou supprimé du groupe, sera une licence automatiquement affectée ou supprimée le cas échéant ? 

- Intégration avec d’autres fournisseurs d’identité tiers :
- Cette solution hybride peut être intégrée avec les fournisseurs d’identité tiers pour mettre en œuvre une ouverture de session unique ?
- Il est possible d’unifier tous les fournisseurs d’identité différente dans un système cohérent d’identité ?
- Si Oui, comment et qui sont elles et quelles sont les fonctionnalités disponibles ?

## <a name="synchronization-management"></a>Gestion de la synchronisation
Un des objectifs d’un gestionnaire d’identités, pour pouvoir mettre tous les fournisseurs d’identité et de les maintenir synchronisés. Conserver les synchronisation des données basée sur un fournisseur d’identité de maître faisant autorité. Dans un scénario d’identité hybride, avec un modèle de gestion synchronisée, vous gérer toutes les identités d’utilisateurs et des périphériques sur un serveur local et synchroniserez les comptes et, le cas échéant, les mots de passe vers le nuage. L’utilisateur entre le même mot de passe local comme il ou elle ne le fait dans le nuage, et à la connexion, le mot de passe est vérifié par la solution d’identité. Ce modèle utilise un outil de synchronisation d’annuaire.
 
![](./media/hybrid-id-design-considerations/Directory_synchronization.png)Pour une conception appropriée, la synchronisation de votre solution d’identité hybride garantir que sont la réponse aux questions suivantes : • quelles sont les solutions de synchronisation disponibles pour la solution d’identité hybride ?
• Quelles sont les fonctionnalités ouverture de session unique ?
• Quelles sont les options de fédération d’identités entre B2B et B2C ?

## <a name="next-steps"></a>Étapes suivantes
[Détermination de la stratégie d’adoption hybride identity management](active-directory-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md)


## <a name="see-also"></a>Voir aussi
[Vue d’ensemble des considérations relatives à la conception](active-directory-hybrid-identity-design-considerations-overview.md)

