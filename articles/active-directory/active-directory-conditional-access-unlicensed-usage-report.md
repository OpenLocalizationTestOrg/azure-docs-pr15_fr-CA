<properties
    pageTitle="Rapport d’utilisation non licenciée | Microsoft Azure"
    description="L’utilisation non licenciée rapport vous permet de que vous identifier les utilisateurs sans licence utilisant payé les fonctionnalités AD Azure."
    services="active-directory"
    documentationCenter=""
    authors="MarkusVi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/20/2016"
    ms.author="markvi"/>

# <a name="unlicensed-usage-report"></a>Rapport d’utilisation non licenciée

L’utilisation non licenciée rapport vous permet de que vous identifier les utilisateurs sans licence utilisant payé les fonctionnalités AD Azure. Ainsi vous faire une meilleure utilisez de licences que vous avez achetés et pour identifier vous savez que vous devrez parfois des licences supplémentaires. 

Le rapport affiche l’utilisation active des fonctionnalités payées au cours des 30 derniers jours. 

## <a name="report-structure"></a>Structure de rapport
 
| Nom de la colonne          |    Description |
| :--                  | :--         |
| Licence utilisateur      |    Nom de l’utilisateur |
| Fonctionnalité              | Le nom de la fonction. Par exemple : accès conditionnel |
| Accédé à l’application | Le nom de l’application qui accède à la fonctionnalité. Par exemple : Office 365 SharePoint Online |

 
> [AZURE.NOTE] Si un compte d’utilisateur a été supprimé la colonne « Sans licence utilisateur » sera remplie avec un ID, comme 1003000090D8B285


## <a name="conditional-access-feature"></a>Fonction d’accès conditionnel

Les utilisateurs sans licence seront signalés lorsqu’ils accèdent à un service qui possède la stratégie d’accès conditionnel s’ils ne disposent pas d’une licence d’Azure AD Premium. 

Cela s’applique aux AMF / stratégies d’emplacement ainsi que dispositif de stratégies qui utilisent Intune.
 

## <a name="see-also"></a>Voir aussi

- [À l’aide d’accès conditionnel avec Office 365 et autres Azure Active Directory connecté des applications](active-directory-conditional-access.md)
- [Mise en route avec accès conditionnel à AD Azure](active-directory-conditional-access-azuread-connected-apps.md) 


