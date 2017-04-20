<properties
    pageTitle="Explicateur d’aperçu Active Directory Azure | Microsoft Azure"
    description="Une rubrique qui explique les différences entre Azure Active Directory dans le portail classique et l’aperçu Azure Active Directory dans le portail Azure."
    services="active-directory"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/12/2016"
    ms.author="curtand"/>


# <a name="preview-of-the-azure-active-directory-management-experience-in-the-azure-portal"></a>Aperçu de l’expérience de gestion Azure Active Directory dans le portail Azure

L’expérience de gestion Azure Active Directory (AD Azure) est en mode Aperçu dans le portail Azure. Vous pouvez l’essayer out en vous connectant au [portail Azure](https://portal.azure.com) comme un administrateur global de votre répertoire. Ensuite, sélectionnez Azure Active Directory dans la liste de services s’il est visible, ou sélectionnez **plusieurs services** pour afficher la liste de tous les services. Vous n’avez pas besoin d’un abonnement Azure à utiliser l’Azure expérience en gestion d’Active Directory dans le portail Azure.


## <a name="capabilities-of-the-preview-experience"></a>Fonctionnalités de l’expérience de l’aperçu

L’expérience de l’aperçu vous permet de gérer de nombreuses ressources de répertoire telles que les utilisateurs, groupes et les applications, ainsi que les paramètres de répertoire, dans le portail Azure. Nous efforçons donc d’améliorer cette expérience pour inclure toutes les fonctions qui existent dans l’Azure expérience en gestion d’Active Directory dans [Azure portal classique](https://manage.windowsazure.com). En attendant, il y a certaines tâches que vous devez toujours réaliser sur le portail classique gestion du répertoire.

## <a name="manage-the-same-azure-ad-tenants"></a>Gérer les locataires AD Azure mêmes

L’expérience d’aperçu lit et écrit au locataire même Azure Active Directory ainsi que le portail classique, le centre d’administration d’Office 365. Les modifications apportées dans un de ces portails sont répercutées dans toutes les autres.

## <a name="use-the-same-authorization-logic"></a>Utiliser la même logique d’autorisation

L’expérience de l’aperçu utilise la même logique d’autorisation en tant que les clients Active Directory existants. Les utilisateurs sont autorisés à apporter des modifications aux ressources de répertoire en fonction de leur rôle de répertoire, par exemple l’administrateur général, administrateur d’utilisateurs, administrateur de mot de passe. Avec un rôle de ressources Azure ou un abonnement Azure n’autorise pas un utilisateur de gérer les ressources du répertoire. Pour plus d’informations, rôles de gestion AD Azure, voir [affectation les rôles d’administrateur dans Azure Active Directory](active-directory-assign-admin-roles.md). 

L’expérience de l’aperçu est optimisé pour les administrateurs globaux. Si vous utilisez l’expérience de l’aperçu lors d’une connexion en tant qu’utilisateur qui n’est pas un administrateur global, peut avoir une expérience de dégradé. Par exemple, vous pourrez peut-être sélectionner un bouton qui vous permet de commencer une tâche que vous ne pouvez pas réaliser dans le répertoire. Nous lançons dès cette expérience.
 
## <a name="tell-us-what-you-think"></a>Dites-nous ce que vous pensez

Vous pouvez fournir des commentaires sur l’expérience de l’aperçu dans la section de portail admin du [forum d’évaluations AD Azure](https://social.msdn.microsoft.com/Forums/home?forum=WindowsAzureAD&filter=alltypes&sort=lastpostdesc).
