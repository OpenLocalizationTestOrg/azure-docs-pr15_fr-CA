<properties
   pageTitle="Azure Connect de AD sync : extensions Directory | Microsoft Azure"
   description="Cette rubrique décrit la fonctionnalité d’extensions de répertoire dans Azure Connect d’Active Directory."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/19/2016"
   ms.author="billmath"/>

# <a name="azure-ad-connect-sync-directory-extensions"></a>Azure Connect de AD sync : extensions de répertoire
Extensions de répertoire vous permet d’étendre le schéma dans Azure AD avec vos propres attributs de sur site Active Directory. Cette fonctionnalité vous permet de créer des applications métier utilisant des attributs que vous continuer à gérer sur site. Ces attributs peuvent être consommées par les [extensions du répertoire Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions) ou de [Microsoft Graph](https://graph.microsoft.io/). Vous pouvez voir les attributs disponibles à l’aide respectivement [Azure AD graphique explorer](https://graphexplorer.cloudapp.net) et [l’Explorateur de Microsoft Graph](https://graphexplorer2.azurewebsites.net/) .

À l’heure actuelle aucune charge de travail Office 365 n’utilise ces attributs.

Vous configurez les attributs supplémentaires que vous souhaitez synchroniser dans le chemin d’accès de paramètres personnalisés de l’Assistant d’installation.
![Assistant d’Extension de schéma](./media/active-directory-aadconnectsync-feature-directory-extensions/extension2.png) l’installation présente les attributs suivants, qui sont des candidats valides :

- Types d’objets utilisateur et groupe
- Attributs à valeur simple : chaîne, booléen, entier, binaire
- Les attributs à valeurs multiples : chaîne, binaire

La liste des attributs est lu à partir du cache, créé pendant l’installation d’Azure Connect d’AD. Si vous avez étendu le schéma Active Directory avec des attributs supplémentaires, [schéma doit être actualisé](active-directory-aadconnectsync-installation-wizard.md#refresh-directory-schema) avant ces nouveaux attributs sont visibles.

Un objet peut avoir jusqu'à 100 attributs d’extensions de répertoire. La longueur maximale est de 250 caractères. Si une valeur d’attribut est plus longue, il est tronqué par le moteur de synchronisation.

Lors de l’installation d’Azure AD Connect, l’application est enregistrée lorsque ces attributs sont disponibles. Vous pouvez voir cette application dans Azure portal.  
![Application d’Extension de schéma](./media/active-directory-aadconnectsync-feature-directory-extensions/extension3.png)

Ces attributs sont maintenant disponibles via le graphique :  
![Graphique](./media/active-directory-aadconnectsync-feature-directory-extensions/extension4.png)

Les attributs sont préfixés avec l’extension\_{AppClientId}\_. Le AppClientId a la même valeur pour tous les attributs dans votre répertoire AD Azure.

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur la configuration [d’Azure AD connexion de synchronisation](active-directory-aadconnectsync-whatis.md) .

Pour en savoir plus sur [l’intégration des identités avec Azure Active Directory local](active-directory-aadconnect.md).
