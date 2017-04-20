<properties
    pageTitle="Annonce Azure et les Applications : affectation de groupes à une Application | Microsoft Azure"
    description="Comment implémenter l’affectation de groupe pour les applications Azure."
    services="active-directory"
    documentationCenter=""
    authors="IHenkel"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/03/2015"
    ms.author="inhenk"/>

# <a name="azure-ad-and-applications-assigning-groups-to-an-application"></a>Annonce Azure et les Applications : affectation de groupes à une Application
Avant d’affecter des utilisateurs et des groupes à une application, vous devez demander l’affectation de l’utilisateur. Pour savoir comment nécessitent l’affectation de l’utilisateur, consultez l’article [Nécessitant des affectations utilisateur](active-directory-applications-guiding-developers-requiring-user-assignment.md) .

Cet article suppose que vous avez déjà créé des groupes dans active directory que vous utilisez pour cette application.

## <a name="assigning-groups-to-an-application"></a>Affectation de groupes à une Application
1. Connectez-vous au portail Azure avec un compte d’administrateur.
2. Cliquez sur l’élément de **Tous les éléments** dans le menu principal.
3. Sélectionnez le répertoire que vous utilisez pour l’application.
4. Cliquez sur l’onglet **APPLICATIONS** .
5. Sélectionnez l’application à partir de la liste des applications associées à ce répertoire.
6. Cliquez sur l’onglet **Utilisateurs et groupes** .
7. Filtrer la liste des groupes dans active directory à l’aide de la liste de **groupes** .
8. Sélectionnez le groupe.
9. Cliquez sur **attribuer**.
10. Cliquez sur **Oui** lorsque vous y êtes invité.

## <a name="next-steps"></a>Étapes suivantes
[AZURE.INCLUDE [active-directory-applications-guiding-developers-for-lob-applications-toc.md](../../includes/active-directory-applications-guiding-developers-for-lob-applications-toc.md)]
