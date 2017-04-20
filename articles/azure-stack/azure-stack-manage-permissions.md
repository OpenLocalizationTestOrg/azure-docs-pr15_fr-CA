<properties
    pageTitle="Gérer les autorisations sur les ressources par utilisateur dans la pile d’Azure (administrateur du service et clients) | Microsoft Azure"
    description="Comme un administrateur du service ou le locataire, découvrez comment gérer les autorisations sur les ressources par utilisateur."
    services="azure-stack"
    documentationCenter=""
    authors="ErikjeMS"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="erikje"/>

# <a name="manage-user-permissions"></a>Gérer les autorisations utilisateur

Un utilisateur dans la pile d’Azure peut être un lecteur, un propriétaire ou un collaborateur pour chaque instance d’un abonnement, le groupe de ressources ou le service. Par exemple, l’utilisateur A peut disposer des autorisations de lecture pour 1 d’abonnement, mais ont des autorisations de propriétaire sur un ordinateur virtuel 7.

-   Lecteur : Utilisateur peut afficher tous les éléments, mais ne pouvez pas apporter des modifications.

-   Contributeur : Utilisateur peut gérer tout à l’exception de l’accès aux ressources.

-   Propriétaire : Utilisateur peut gérer tout, y compris l’accès aux ressources.


## <a name="set-access-permissions-for-a-user"></a>Définir les autorisations d’accès pour un utilisateur

1.  Connectez-vous avec un compte qui dispose des autorisations de propriétaire de la ressource que vous souhaitez gérer.

2.  De la lame pour la ressource, cliquez sur l’icône **d’accès** ![](media/azure-stack-manage-permissions/image1.png).

3.  De la lame **d’utilisateurs** , cliquez sur **rôles**.

4.  De la lame de **rôles** , cliquez sur **Ajouter** pour ajouter des autorisations pour l’utilisateur.

## <a name="next-steps"></a>Étapes suivantes

[Ajouter un locataire Azure pile](azure-stack-add-new-user-aad.md)
