<properties
    pageTitle="Application basée sur l’attribut de mise en service avec filtres de portée | Microsoft Azure"
    description="Apprenez à utiliser des filtres de portée pour empêcher des objets dans les applications qui prennent en charge l’approvisionnement de l’utilisateur automatisé d’effectivement en cours de provision si un objet n’est pas conforme à vos besoins."
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="markusvi"/>


# <a name="attribute-based-app-provisioning-with-scoping-filters"></a>Application basée sur l’attribut de mise en service avec filtres de portée

L’objectif de cette section est d’expliquer comment utiliser les filtres étendues pour définir les règles qui déterminent les utilisateurs qui seront déployés à l’application basée sur l’attribut.





## <a name="clauses-and-scope-groups"></a>Clauses et groupes d’étendue


![Portée du filtre][1] 




Portée des filtres sont définis par un ou plusieurs **groupes d’étendue**, qui contiennent une ou plusieurs **clauses**. Pour afficher les clauses d’un groupe particulier de portée, développez-le en cliquant sur la flèche située à gauche du nom du groupe.

Une **clause** détermine quels utilisateurs sont autorisés à passer par le filtre de portée en évaluant les attributs de chaque utilisateur. Par exemple, vous pouvez avoir une clause qui nécessite que l’attribut « état » de l’utilisateur soit égal à New York, ce qui signifie que seuls vos utilisateurs à New York seront déployés dans l’application.

![Nom du groupe de portée][2] 



Chaque **groupe de portée** commence par obligatoire une **clause**, comme illustré dans la capture d’écran ci-dessus. Cette clause indique simplement que l’utilisateur doit être préalablement assigné à l’application avant d’être évalué par vos filtres de portée. Cette clause ne peut pas être supprimée ou modifiée.

Vous pouvez ajouter de nouvelles clauses ou nouveaux groupes d’étendue en appuyant sur le bouton approprié. Vous pouvez nommer chaque groupe d’étendue en modifiant sa propriété de **Nom de groupe de l’étendue** .





## <a name="how-scoping-filters-are-evaluated"></a>L’évaluation des filtres de portée

Au cours de la mise en service, nous testons chaque utilisateur affecté par rapport à votre portées filtres afin de déterminer si cet utilisateur mérite d’accéder à l’application. Vous pouvez considérer chaque clause comme étant un test qui doit être passé pour l’utilisateur afin d’éviter la mise en route filtrés. 

Si vous avez plusieurs groupes de portée définies, chaque utilisateur doit passer au moins un d'entre eux pour accéder à l’application. Au sein de chaque groupe d’étendue, toutefois, l’utilisateur doit passer chaque clause unique afin de passer de ce groupe de portée spécifique. 

En d’autres termes, vous pouvez considérer comme étant des groupes de portée ou combinaison, et vous pouvez considérer les clauses qu’ils contiennent comme étant et combinaison. Par exemple, considérez la portée de filtre ci-dessous :


![Nom du groupe de portée][2]  


En fonction de ce filtre de portée, les utilisateurs doivent satisfaire aux critères suivants, pour être mis en service :

1. Ils doivent être affectés à l’application.

2. Ils doivent travailler dans le département d’ingénierie

3. Ils doivent être travail San Francisco ou au Canada.


##<a name="related-articles"></a>Articles connexes

- [Index des articles pour la gestion d’Application dans Azure Active Directory](active-directory-apps-index.md)
- [Automatiser l’utilisateur mise en service et hors service pour les Applications SaaS](active-directory-saas-app-provisioning.md)
- [Personnalisation des mappages d’attributs pour l’approvisionnement de l’utilisateur](active-directory-saas-customizing-attribute-mappings.md)
- [Écriture d’Expressions pour les mappages d’attributs](active-directory-saas-writing-expressions-for-attribute-mappings.md)
- [Mise en service des Notifications de compte](active-directory-saas-account-provisioning-notifications.md)
- [Pour activer la mise en service automatique des utilisateurs et des groupes d’Azure Active Directory pour les applications à l’aide de SCIM](active-directory-scim-provisioning.md)
- [Liste des didacticiels sur la façon d’intégrer les applications SaaS](active-directory-saas-tutorial-list.md)

<!--Image references-->
[1]: ./media/active-directory-saas-scoping-filters/ic782811.png
[2]: ./media/active-directory-saas-scoping-filters/ic782812.png
[3]: ./active-directory-saas-scoping-filters/ic782813.png
