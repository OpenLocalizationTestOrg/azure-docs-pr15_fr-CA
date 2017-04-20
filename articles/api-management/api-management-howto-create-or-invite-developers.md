<properties 
    pageTitle="Comment gérer les comptes d’utilisateurs dans Gestion de l’API Azure | Microsoft Azure" 
    description="Apprenez à créer ou invitez des utilisateurs dans Gestion de l’API Azure" 
    services="api-management" 
    documentationCenter="" 
    authors="steved0x" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="sdanie"/>

# <a name="how-to-manage-user-accounts-in-azure-api-management"></a>Comment faire pour gérer des comptes d’utilisateurs dans Gestion de l’API Azure

Dans Gestion de l’API, les développeurs sont les utilisateurs de l’API qui vous exposez à l’aide de la gestion de l’API. Ce guide explique comment créer et inviter les développeurs à utiliser les API et les produits que vous apportez à leur disposition à votre instance de gestion de l’API. Pour plus d’informations sur la gestion des comptes d’utilisateur par programme, consultez la documentation de [l’entité utilisateur](https://msdn.microsoft.com/library/azure/dn776330.aspx) dans la référence de [l’API de gestion reste](https://msdn.microsoft.com/library/azure/dn776326.aspx) .

## <a name="create-developer"> </a>Créer un nouveau développeur

Pour créer un nouveau développeur, cliquez sur **Gérer** dans le portail classique d’Azure pour votre service de gestion de l’API. Cela vous amène sur le portail d’éditeur de gestion de l’API. Si vous n’avez pas encore créé une instance de service de gestion de l’API, voir [Création d’une instance de service de gestion de l’API][] dans le didacticiel [mise en route de la gestion des API Azure][] .

![Portail de Publisher][api-management-management-console]

Cliquez sur **utilisateurs** dans le menu de **Gestion de l’API** sur la gauche, puis cliquez sur **Ajouter un utilisateur**.

![Créer le développeur][api-management-create-developer]

Entrez **l’E-mail**, le **mot de passe**et le **nom** du nouveau développeur et cliquez sur **Enregistrer**.

![Créer le développeur][api-management-add-new-user]

Par défaut, les comptes de développeur nouvellement créée sont **actives**et associé au groupe de **développeurs** .

![Nouveau développeur][api-management-new-developer]

Comptes de développeur qui sont dans un état **actif** peuvent être utilisés pour accéder à toutes les API pour lesquelles ils ont des abonnements. Pour associer le développeur qui vient d’être créé avec des groupes supplémentaires, voir [comment associer des groupes avec les développeurs][].

## <a name="invite-developer"> </a>Inviter un développeur

Pour inviter un développeur, cliquez sur **utilisateurs** dans le menu de **Gestion de l’API** sur la gauche, puis cliquez sur **Inviter l’utilisateur**.

![Inviter les développeurs][api-management-invite-developer]

Entrez l’adresse e-mail et le nom du développeur, cliquez sur **inviter**.

![Inviter les développeurs][api-management-invite-developer-window]

Un message de confirmation s’affiche, mais le développeur qui vient d’être invité n’apparaît pas dans la liste jusqu'à ce qu’une fois qu’ils acceptent l’invitation. 

![Invitation confirmation][api-management-invite-developer-confirmation]

Lorsqu’un développeur est invité, un e-mail est envoyé au développeur. Cet e-mail est généré à l’aide d’un modèle et il est personnalisable. Pour plus d’informations, consultez [configurer les modèles d’e-mail][].

Une fois l’invitation acceptée, le compte devient actif.

## <a name="block-developer"></a> Désactiver ou réactiver un compte de développeur

Par défaut, les comptes de développeur nouvellement créé ou invités sont **actifs**. Pour désactiver un compte de développeur, cliquez sur **Bloquer**. Pour réactiver un compte de développeur bloqués, cliquez sur **Activer**. Un compte de développeur bloqué ne peut pas accéder au portail développeur ou appeler des API. Pour supprimer un compte d’utilisateur, cliquez sur **Supprimer**.

![Développeur de bloc][api-management-new-developer]

## <a name="reset-a-user-password"></a>Réinitialiser un mot de passe de l’utilisateur

Pour réinitialiser le mot de passe pour un compte d’utilisateur, cliquez sur le nom du compte.

![Réinitialiser le mot de passe][api-management-view-developer]

Cliquez sur **Réinitialiser le mot de passe** pour envoyer un lien vers l’utilisateur de réinitialiser leur mot de passe.

![Réinitialiser le mot de passe][api-management-reset-password]

Pour travailler par programmation avec les comptes d’utilisateurs, consultez la documentation de [l’entité utilisateur](https://msdn.microsoft.com/library/azure/dn776330.aspx) dans la référence de [l’API de gestion reste](https://msdn.microsoft.com/library/azure/dn776326.aspx) . Pour réinitialiser un mot de passe de compte d’utilisateur à une valeur spécifique, vous pouvez utiliser l’opération de [mise à jour d’un utilisateur](https://msdn.microsoft.com/library/azure/dn776330.aspx#UpdateUser) et spécifier le mot de passe.

## <a name="pending-verification"></a>Vérification en attente

![Vérification en attente][api-management-pending-verification]

## <a name="next-steps"> </a>Étapes suivantes

Après la création d’un compte de développeur, vous pouvez associer à des rôles et l’abonner à des produits et des API. Pour plus d’informations, consultez [comment créer et utiliser des groupes][].


[api-management-management-console]: ./media/api-management-howto-create-or-invite-developers/api-management-management-console.png
[api-management-add-new-user]: ./media/api-management-howto-create-or-invite-developers/api-management-add-new-user.png
[api-management-create-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-create-developer.png
[api-management-invite-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer.png
[api-management-new-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-new-developer.png
[api-management-invite-developer-window]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-window.png
[api-management-invite-developer-confirmation]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-confirmation.png
[api-management-pending-verification]: ./media/api-management-howto-create-or-invite-developers/api-management-pending-verification.png
[api-management-view-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-view-developer.png
[api-management-reset-password]: ./media/api-management-howto-create-or-invite-developers/api-management-reset-password.png
[]: ./media/api-management-howto-create-or-invite-developers/.png



[Create a new developer]: #create-developer
[Invite a developer]: #invite-developer
[Deactivate or reactivate a developer account]: #block-developer
[Next steps]: #next-steps
[Comment créer et utiliser des groupes]: api-management-howto-create-groups.md
[Comment faire pour associer les groupes aux développeurs]: api-management-howto-create-groups.md#associate-group-developer

[Mise en route de la gestion des API Azure]: api-management-get-started.md
[Créez une instance de service de gestion de l’API]: api-management-get-started.md#create-service-instance
[Configurer des modèles de courriel]: api-management-howto-configure-notifications.md#email-templates