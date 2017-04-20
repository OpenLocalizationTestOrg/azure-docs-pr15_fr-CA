<properties
    pageTitle="Dédié de groupes dans Active Directory de Azure | Microsoft Azure"
    description="Vue d’ensemble des groupes dédiés comment travailler dans Azure Active Directory et comment ils sont créés."
    services="active-directory"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""
    />

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="curtand"/>

# <a name="dedicated-groups-in-azure-active-directory"></a>Groupes dédiés dans Azure Active Directory

Dans Azure Active Directory (AD Azure), la fonctionnalité des groupes dédiés crée automatiquement et remplit l’appartenance aux groupes d’annonces Azure prédéfinis. Les membres des groupes dédiés ne peuvent pas être ajoutés ou supprimés à l’aide de l’Azure portal classique, les applets de commande Windows PowerShell, ou par programme.

>[AZURE.NOTE] Groupes dédiés nécessitent qu’une licence de Azure AD prime est attribuée à
>- l’administrateur qui gère la règle sur un groupe
>- tous les utilisateurs qui sont sélectionnés par la règle à être membre du groupe

**Pour activer les groupes dédiés**

1. Dans [Azure portal classique](https://manage.windowsazure.com), sélectionnez **Active Directory**et ouvrez l’annuaire de votre organisation.

2. Sélectionnez l’onglet **groupes** et ouvrez le groupe que vous souhaitez modifier.

3. Sélectionnez l’onglet **configurer** et puis définissez **Activer des groupes dédiés** à **Oui**.

Une fois que le commutateur d’activation des groupes dédiés est défini sur **Oui**, vous pouvez plus activer le répertoire créer automatiquement le groupe dédié de tous les utilisateurs en définissant le **Activer « All Users » groupe** passer à **Oui**. Vous pouvez ensuite également modifier le nom de ce groupe dédié en le tapant dans la **nom d’affichage pour « Tous les utilisateurs » groupe** champ.

Le groupe tous les utilisateurs peut être utilisé pour affecter les mêmes autorisations à tous les utilisateurs dans votre répertoire. Par exemple, vous pouvez accorder tous les utilisateurs de votre accès à l’annuaire pour une application SaaS par affectation pour le groupe dédié de tous les utilisateurs l’accès à cette application.

Le groupe tous les utilisateurs inclut tous les utilisateurs dans l’annuaire, y compris les invités et les utilisateurs externes. Si vous avez besoin d’un groupe qui exclut les utilisateurs externes, puis vous pouvez accomplir ceci en créant un groupe avec une règle dynamique basée sur l’attribut telles que les suivantes :

                (user.userPrincipalName -notContains "#EXT#@")

Pour un groupe qui exclut tous les invités, utilisez une règle semblable à la suivante :

                (user.userType -ne "Guest")

Pour savoir comment créer des règles *avancées* (règles qui peuvent contenir plusieurs comparaisons) pour l’appartenance au groupe dynamique, reportez-vous [à l’aide des attributs pour créer des règles avancées](active-directory-accessmanagement-groups-with-advanced-rules.md).


Ces articles fournissent des informations supplémentaires sur Azure Active Directory.

* [Gestion de l’accès aux ressources avec groupes Azure Active Directory](active-directory-manage-groups.md)
* [Index des articles pour la gestion d’Application dans Azure Active Directory](active-directory-apps-index.md)
* [Nouveautés d’Azure Active Directory ?](active-directory-whatis.md)
* [Intégration des identités sur site à Azure Active Directory](active-directory-aadconnect.md)
