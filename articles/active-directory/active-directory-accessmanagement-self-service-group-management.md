<properties
    pageTitle="Configuration d’Azure Active Directory pour la gestion des accès libre-service application | Microsoft Azure"
    description="Gestion de groupe de libre-service permet aux utilisateurs de créer et de gérer des groupes de sécurité ou Office 365 dans Azure Active Directory et offre aux utilisateurs la possibilité d’un groupe de sécurité de demande ou des appartenances de groupe Office 365"
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
    ms.topic="get-started-article"
    ms.date="08/10/2016"
    ms.author="curtand"/>

# <a name="setting-up-azure-active-directory-for-self-service-group-management"></a>Configuration d’Azure Active Directory pour la gestion de groupe de libre-service

Gestion de groupe de libre-service permet aux utilisateurs de créer et de gérer des groupes de sécurité ou Office 365 dans Azure Active Directory (AD Azure). Les utilisateurs peuvent demander également les appartenances de groupe Office 365 ou le groupe de sécurité, et puis le propriétaire du groupe peut approuver ou refuser l’appartenance. De cette façon, contrôle quotidien de l’appartenance au groupe peut être déléguée à des personnes de comprennent le contexte professionnel pour cet abonnement. Les fonctionnalités de gestion libre-service de groupe sont disponibles uniquement pour les groupes de sécurité et les groupes d’Office 365, mais pas pour les groupes de sécurité à extension messagerie ou des listes de distribution.

Gestion de groupe de libre service comprend actuellement deux scénarios essentiels : délégation de gestion de groupe et la gestion libre-service de groupe.

- **Déléguer la gestion de groupe** 
   un exemple est un administrateur qui gère l’accès à une application de SaaS à l’aide de la société. La gestion de ces droits d’accès devient fastidieux, afin que l’administrateur vous demande le propriétaire de l’entreprise pour créer un nouveau groupe. L’administrateur assigne l’accès de l’application vers le nouveau groupe et l’ajoute au groupe déjà l’accès à l’application de toutes les personnes. Le propriétaire de l’entreprise peut ensuite ajouter d’autres utilisateurs, et les utilisateurs sont automatiquement mis en service à l’application. Le propriétaire de l’entreprise n’a pas besoin d’attendre que l’administrateur de gérer l’accès des utilisateurs. Si l’administrateur accorde la même autorisation à un gestionnaire dans un autre groupe, cette personne peut également gérer les accès pour leurs propres utilisateurs. Ni le propriétaire de l’entreprise ni le gestionnaire pour afficher ou gérer des uns et des autres utilisateurs. L’administrateur peut toujours afficher tous les utilisateurs qui ont accès à l’application et les droits d’accès bloc si nécessaire.

- **Gestion libre-service de groupe** 
   un exemple de ce scénario est deux utilisateurs disposant à la fois des sites SharePoint Online configurée indépendamment. Ils souhaitent accéder d’autres équipes pour leurs sites. Pour ce faire, ils peuvent créer un groupe dans AD Azure et dans SharePoint Online, chacun d’eux sélectionne ce groupe pour fournir l’accès à leurs sites. Lorsqu’un utilisateur souhaite accéder, ils font la demande à partir du panneau d’accès, et après l’approbation ils accéder aux deux sites SharePoint en ligne automatiquement. Par la suite, un d’eux décide que toutes les personnes qui accèdent au site doivent également accéder à une application particulière de SaaS. L’administrateur de l’application de SaaS peut ajouter des droits d’accès de l’application pour le site SharePoint Online. Dès lors, toutes les demandes approuvées permet d’accéder aux deux sites SharePoint Online et également à cette application SaaS.

## <a name="making-a-group-available-for-end-user-self-service"></a>Mise à disposition un groupe pour utilisateur final libre-service

1. Dans [Azure portal classique](https://manage.windowsazure.com), ouvrez votre répertoire AD Azure.

2. Dans l’onglet **configurer** , définissez la **gestion de groupe délégué** sur activé.

3. Définissez **les utilisateurs peuvent créer des groupes de sécurité** ou **les utilisateurs peuvent créer des groupes de Office** sur activé.

Lorsque **les utilisateurs peuvent créer des groupes de sécurité** est activé, tous les utilisateurs de votre répertoire sont autorisés à créer des groupes de sécurité et d’ajouter des membres à ces groupes. Ces nouveaux groupes également apparaîtraient dans le panneau d’accès pour tous les autres utilisateurs. Si le paramètre de stratégie de groupe le permet, autres utilisateurs peuvent créer des demandes de rejoindre ces groupes. Si **les utilisateurs peuvent créer des groupes de sécurité** est désactivé, les utilisateurs ne peuvent pas créer des groupes et ne pouvez pas modifier les groupes existants dont ils sont propriétaires. Toutefois, ils peuvent toujours gérer les membres de ces groupes et approuver les demandes d’autres utilisateurs à participer à leurs groupes.

Vous pouvez également utiliser **les utilisateurs qui peuvent utiliser le libre-service pour les groupes de sécurité** pour parvenir à un contrôle d’accès plus précis sur la gestion de groupe de libre-service pour vos utilisateurs. Lorsque **les utilisateurs peuvent créer des groupes** est activé, tous les utilisateurs de votre répertoire sont autorisés à créer de nouveaux groupes et ajouter des membres à ces groupes. En définissant également les **utilisateurs qui peuvent utiliser le libre-service pour les groupes de sécurité** à certains, vous appliquez une restriction de groupe gestion à un groupe limité d’utilisateurs. Lorsque ce commutateur est défini pour certains, vous devez ajouter des utilisateurs au groupe SSGMSecurityGroupsUsers avant de pouvoir créer de nouveaux groupes et leur ajouter des membres. En affectant à tous les **utilisateurs qui peuvent utiliser le libre-service pour les groupes de sécurité** , vous activez tous les utilisateurs du répertoire pour créer de nouveaux groupes.

Vous pouvez également utiliser la zone de **groupe qui permet de libre-service pour les groupes de sécurité** pour spécifier un nom personnalisé pour un groupe dont les membres peuvent utiliser le libre-service.

## <a name="additional-information"></a>Informations supplémentaires

Ces articles fournissent des informations supplémentaires sur Azure Active Directory.

* [Gestion de l’accès aux ressources avec groupes Azure Active Directory](active-directory-manage-groups.md)

* [Azure applets de commande de Active Directory pour configurer les paramètres de groupe](active-directory-accessmanagement-groups-settings-cmdlets.md)

* [Index des articles pour la gestion d’Application dans Azure Active Directory](active-directory-apps-index.md)

* [Nouveautés d’Azure Active Directory ?](active-directory-whatis.md)

* [Intégration des identités sur site à Azure Active Directory](active-directory-aadconnect.md)
