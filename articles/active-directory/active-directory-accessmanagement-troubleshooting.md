
<properties
    pageTitle="Résolution des problèmes d’appartenance dynamique des groupes | Microsoft Azure"
    description="Conseils de dépannage pour des membres dynamiques pour les groupes dans AD Azure."
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


# <a name="troubleshooting-dynamic-memberships-for-groups"></a>Résolution des problèmes d’appartenance dynamique pour les groupes

**J’ai configuré une règle sur un groupe, mais aucune appartenance mis à jour dans le groupe**<br/>Vérifiez que le paramètre **Activer déléguer la gestion de groupe** est défini sur **Oui** dans l’onglet **configurer** . Vous verrez ce paramètre uniquement si vous êtes connecté en tant qu’utilisateur auquel est affectée une licence Azure Active Directory prime. Vérifiez les valeurs des attributs d’utilisateur de la règle : existe-t-il des utilisateurs qui satisfont la règle ?

**J’ai configuré une règle, mais maintenant les membres existants de la règle sont supprimés**<br/>Ce comportement est attendu. Les membres existants du groupe sont supprimés lors de l’activation ou de la modification d’une règle. Les utilisateurs retournés à partir de l’évaluation de la règle sont ajoutés en tant que membres au groupe.     

**Je ne vois pas l’appartenance change instantanément lorsque ajouter ou modifier une règle, pourquoi pas ?**<br/>Évaluation d’adhésions dédié est effectuée régulièrement dans un processus d’arrière-plan asynchrone. La durée de la procédure est déterminée par le nombre d’utilisateurs dans votre répertoire et de la taille du groupe créé à la suite de la règle. En général, les répertoires avec un petit nombre d’utilisateurs seront affiche les modifications de l’appartenance au groupe dans moins de quelques minutes. Répertoires avec un grand nombre d’utilisateurs peuvent prendre 30 minutes ou plus à remplir.

Ces articles fournissent des informations supplémentaires sur Azure Active Directory.

* [Gestion de l’accès aux ressources avec groupes Azure Active Directory](active-directory-manage-groups.md)
* [Index des articles pour la gestion d’Application dans Azure Active Directory](active-directory-apps-index.md)
* [Nouveautés d’Azure Active Directory ?](active-directory-whatis.md)
* [Intégration des identités sur site à Azure Active Directory](active-directory-aadconnect.md)
