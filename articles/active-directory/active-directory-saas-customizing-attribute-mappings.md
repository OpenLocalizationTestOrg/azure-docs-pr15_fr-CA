<properties
    pageTitle="Personnalisation des mappages d’attributs | Microsoft Azure"
    description="Les mappages d’attributs pour les applications SaaS dans Azure Active Directory Découvrez comment vous pouvez les modifier pour répondre à vos besoins."
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


# <a name="customizing-attribute-mappings"></a>Personnalisation des mappages d’attributs


Microsoft annonce Azure fournit un support pour l’approvisionnement de l’utilisateur pour les applications SaaS de tiers tels que force de vente, Google Apps. Si vous disposez d’approvisionnement de l’utilisateur pour une application SaaS activée tierce, le portail de gestion Azure contrôle ses valeurs d’attribut sous la forme d’une configuration appelée « mappage d’attribut ».

Il existe un jeu préconfiguré de mappages d’attributs entre les objets d’utilisateur AD Azure et de chaque application SaaS. Certaines applications gérer d’autres types d’objets, tels que des groupes ou des Contacts. <br> 
Vous pouvez personnaliser les mappages d’attributs par défaut en fonction des besoins de votre entreprise. Ainsi, vous pouvez modifier ou supprimer les mappages d’attributs existants ou créer de nouveaux mappages d’attributs.

Dans le portail AD Azure, vous pouvez accéder à cette fonctionnalité en cliquant sur les attributs dans la barre d’outils d’une application SaaS.

> [AZURE.NOTE] Le **attributs** de lien est disponible uniquement si vous avez activé pour une application SaaS approvisionnement de l’utilisateur. 


![Force de vente][1] 


Lorsque vous cliquez sur attributs dans la barre d’outils, la liste des mappages actuels qui sont configurés pour une application SaaS.

La capture d’écran suivante illustre un exemple de ceci :



![Force de vente][2]  


Dans l’exemple ci-dessus, vous pouvez voir que l’attribut **firstName** d’un objet managé dans la force de vente est rempli avec la valeur **givenName** lié objet AD Azure.

Si vous souhaitez personnaliser vos mappages d’attributs ou si vous souhaitez rétablir les paramètres de la configuration par défaut de personnalisé, vous pouvez pour cela en cliquant sur le bouton associé dans la barre d’outils dans la partie inférieure d’une application.


![Force de vente][3]  


Il existe des mappages d’attributs qui sont requis par une application SaaS pour fonctionner correctement. Dans la table d’attributs, les mappages d’attributs connexes ont **Oui** comme valeur pour l’attribut **obligatoire** . Si un mappage d’attributs est requis, vous ne pouvez pas le supprimer. Dans ce cas, **supprimez** la fonctionnalité n’est pas disponible.

Pour modifier un mappage d’attribut existant, sélectionnez le mappage, puis cliquez sur **Modifier**. Elle affiche une page de la boîte de dialogue qui vous permet de modifier le mappage d’attribut sélectionné.


![Modifier le mappage d’attributs][4]  



## <a name="understanding-attribute-mapping-types"></a>Comprendre le mappage des Types d’attribut


Des mappages d’attributs, vous pouvez contrôler comment les attributs sont complétés dans une troisième partie SaaS application. Il existe quatre types différents de mappage pris en charge :

- **Direct** – l’attribut cible est remplie avec la valeur d’un attribut de l’objet lié dans Azure AD.


- **Constante** – l’attribut cible est remplie avec une chaîne spécifique que vous avez spécifié.


- **Expression** - l’attribut cible est remplie en fonction du résultat d’une expression de type script. Pour plus d’informations, consultez [Développement d’Expressions pour les mappages d’attributs dans Azure Active Directory](active-directory-saas-writing-expressions-for-attribute-mappings.md).


- **Aucun** - l’attribut cible est non modifié de gauche. Toutefois, si l’attribut cible n’est jamais vide, il sera remplie avec la valeur par défaut que vous spécifiez.



En plus de ces quatre types de mappage de l’attribut de base, les mappages d’attributs personnalisés prennent en charge le concept d’une affectation de la valeur **par défaut** . L’affectation de la valeur par défaut permet de garantir qu’un attribut cible est rempli avec une valeur si il n’y a aucune valeur dans Azure AD, ni sur l’objet cible.

Microsoft annonce Azure fournit une implémentation très efficace d’un processus de synchronisation. Dans un environnement initialisé, seuls les objets nécessitant des mises à jour sont traités au cours d’un cycle de synchronisation. Mise à jour des mappages d’attributs d’a un impact sur les performances d’un cycle de synchronisation. C’est parce qu’une mise à jour de la configuration de mappage d’attribut nécessite tous les objets managés à réévaluer. Pour cette raison, il est recommandé de conserver le nombre de modifications consécutives à vos mappages d’attributs au minimum.


##<a name="related-articles"></a>Articles connexes

- [Index des articles pour la gestion d’Application dans Azure Active Directory](active-directory-apps-index.md)
- [Automatiser l’utilisateur mise en service/hors service pour les applications SaaS](active-directory-saas-app-provisioning.md)
- [Écriture d’Expressions pour les mappages d’attributs](active-directory-saas-writing-expressions-for-attribute-mappings.md)
- [Portée des filtres pour l’approvisionnement de l’utilisateur](active-directory-saas-scoping-filters.md)
- [Pour activer la mise en service automatique des utilisateurs et des groupes d’Azure Active Directory pour les applications à l’aide de SCIM](active-directory-scim-provisioning.md)
- [Mise en service des Notifications de compte](active-directory-saas-account-provisioning-notifications.md)
- [Liste des didacticiels sur la façon d’intégrer les applications SaaS](active-directory-saas-tutorial-list.md)


<!--Image references-->
[1]: ./media/active-directory-saas-customizing-attribute-mappings/ic765497.png
[2]: ./media/active-directory-saas-customizing-attribute-mappings/ic775419.png
[3]: ./media/active-directory-saas-customizing-attribute-mappings/ic775420.png
[4]: ./media/active-directory-saas-customizing-attribute-mappings/ic775421.png
