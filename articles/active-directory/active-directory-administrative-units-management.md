<properties
   pageTitle="Gestion des unités administratives dans Azure Active Directory"
   description="Pour plus de la délégation d’autorisations dans Azure Active Directory à l’aide d’unités administratives"
   services="active-directory"
   documentationCenter=""
   authors="curtand"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/23/2016"
   ms.author="curtand"/>

# <a name="administrative-units-management-in-azure-ad---public-preview"></a>Gestion des unités administratives dans Azure AD - version d’évaluation

Cet article décrit les unités administratives – un nouveau conteneur d’Azure Active Directory des ressources qui peuvent être utilisées pour déléguer des autorisations d’administration sur des sous-ensembles d’utilisateurs et d’application des stratégies à un sous-ensemble d’utilisateurs. Unités administratives activer Azure Active Directory, les administrateurs centraux pour déléguer des autorisations à des administrateurs régionaux ou pour définir la stratégie à un niveau granulaire.

Cela est utile dans les entreprises disposant de divisions indépendantes, par exemple, une grande université qui est composé de nombreuses écoles autonomes (Business school, école d’ingénierie, etc.) qui sont indépendants les uns des autres. Ces divisions ont leurs propres administrateurs informatiques qui contrôlent l’accès, gestion des utilisateurs et définir des stratégies pour leur division. Administrateurs centraux et souhaitez pouvoir accorder ces divisions autorisations d’administrateurs sur les utilisateurs dans leurs divisions particuliers. Plus précisément, à l’aide de cet exemple, un administrateur central peut, par exemple, créer une unité d’administration pour un établissement donné (Business school) et le remplir avec uniquement les utilisateurs de l’école professionnels. Puis un administrateur central peut ajouter la Business school personnel informatique à un rôle de portée limitée, accorder en d’autres termes, les informaticiens professionnels l’établissement d’autorisations d’administration uniquement sur la division d’administration de l’école.

> [AZURE.IMPORTANT]
> Vous pouvez créer et utiliser des unités d’administration uniquement si vous activez Azure Active Directory prime. Pour plus d’informations, consultez [mise en route avec Azure AD Premium](active-directory-get-started-premium.md).

Du point de vue de l’administrateur central, une unité d’administration est un objet d’annuaire qui peut être créé et rempli avec les ressources. **Dans cette version, ces ressources peuvent être des utilisateurs uniquement.** Une fois créé et rempli, l’unité administrative utilisable comme une portée pour restreindre l’autorisation accordée uniquement sur les ressources contenues dans l’unité administrative.

## <a name="managing-administrative-units"></a>Gestion des unités d’administration

Dans cette version d’évaluation, vous pouvez créer et gérer des unités d’administration à l’aide des applets de commande Azure Active Directory Module pour Windows PowerShell.

Pour plus d’informations sur la configuration logicielle requise et l’installation du module AD Azure et pour plus d’informations sur les applets de commande de Module de publicité Azure pour la gestion des unités administratives, y compris la syntaxe, des descriptions des paramètres et des exemples, voir la rubrique [Manage AD Azure à l’aide de Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx).


## <a name="next-steps"></a>Étapes suivantes
[Azure éditions d’Active Directory](active-directory-editions.md)
