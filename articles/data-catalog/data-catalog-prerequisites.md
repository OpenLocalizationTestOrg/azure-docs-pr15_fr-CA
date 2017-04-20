<properties
   pageTitle="Conditions préalables de catalogue de données Azure | Microsoft Azure"
   description="Azure prérequis du catalogue de données - ce qu’il faut commencer avec le catalogue de données Azure."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="09/21/2016"
   ms.author="maroche"/>

# <a name="azure-data-catalog-prerequisites"></a>Conditions préalables de catalogue de données Azure

## <a name="what-do-i-need-to-get-started-with-azure-data-catalog"></a>Quoi ai-je besoin pour commencer à utiliser de catalogue de données Azure ?

Il y a quelques petites choses à prendre en charge avant de pouvoir définir des **Catalogues de données Azure**. Ne vous inquiétez pas, ils ne prendront pas de temps !

## <a name="azure-subscription"></a>Abonnement Azure
Pour configurer le catalogue de données Azure, vous devez être le propriétaire ou le copropriétaire d’un abonnement Azure.

Abonnements Azure vous permettent d’organiser l’accès aux ressources de service de cloud telles que catalogue de données Azure. Ils aident également à contrôler la façon dont l’utilisation des ressources est signalée, facturé et payé. Chaque abonnement peut avoir un paramétrage différent de facturation et de paiement, vous pouvez avoir différents abonnements et différents plans par département, projet, bureau régional et ainsi de suite. Chaque service en nuage appartient à un abonnement, et vous devez disposer d’un abonnement avant de configurer le catalogue de données Azure. Pour plus d’informations, voir [Gérer les comptes, les abonnements et les rôles administratifs](../active-directory/active-directory-assign-admin-roles.md).

## <a name="azure-active-directory"></a>Azure Active Directory
Pour configurer le catalogue de données Azure, vous devez être connecté en utilisant un compte d’utilisateur Active Directory de Azure.

Azure Active Directory (AD Azure) offre un moyen facile pour votre entreprise à la gestion des identités et des accès, à la fois dans le nuage et sur site. Les utilisateurs peuvent utiliser un compte de l’établissement ou le travail d’ouverture de session unique sur n’importe quel nuage et sur site web application. Catalogue de données Azure utilise Azure AD pour l’authentification d’ouverture de session. Pour plus d’informations, consultez [Nouveautés Azure Active Directory](../active-directory/active-directory-whatis.md).

> [AZURE.NOTE] Le [portail Azure](http://portal.azure.com/) permet aux utilisateurs de se connecter à l’aide d’un Account personnel de Microsoft ou un travail Azure Active Directory ou à l’école compte. Pour configurer le catalogue de données Azure via le portail d’Azure, ou à l’aide du [portail de catalogue de données](http://www.azuredatacatalog.com) , vous devez être connecté en utilisant un compte Azure Active Directory, et non un compte personnel.

## <a name="active-directory-policy-configuration"></a>Configuration de la stratégie Active Directory

Dans certains cas, les utilisateurs peuvent rencontrer une situation où ils peuvent se connecter le portail du catalogue de données Azure, mais lorsqu’ils tentent de se connecter à l’outil d’enregistrement de source de données qu’ils rencontrent un message d’erreur qui empêche l’ouverture de session. Ce problème peut se produire uniquement lorsque l’utilisateur est sur le réseau de l’entreprise, ou peut se produire uniquement lorsque l’utilisateur se connecte à partir de l’extérieur du réseau d’entreprise.

L’outil de l’enregistrement de source de données utilise l’authentification par formulaire pour valider des ouvertures de session utilisateur dans Active Directory. Pour l’ouverture de session réussie, l’authentification par formulaires doit être activée dans la stratégie d’authentification globale par un administrateur Active Directory.

La stratégie d’authentification globale permet de méthodes d’authentification être activé séparément pour l’intranet et les connexions de l’extranet, comme illustré ci-dessous. Erreurs de connexion peuvent se produire si l’authentification par formulaire n’est pas activée pour le réseau à partir duquel l’utilisateur se connecte.

 ![Stratégie d’authentification Global Active Directory](./media/data-catalog-prerequisites/global-auth-policy.png)

Pour plus d’informations, consultez [Configuration des stratégies d’authentification](https://technet.microsoft.com/library/dn486781.aspx).
