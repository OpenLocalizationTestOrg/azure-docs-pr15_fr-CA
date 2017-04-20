<properties
   pageTitle="Ajouter et gérer plusieurs annuaires d’Azure Active Directory | Microsoft Azure"
   description="Instructions et méthodes conseillées pour l’ajout et la gestion de vos répertoires Azure Active Directory, en expliquant les répertoires comme un totalement indépendante des ressources"
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

# <a name="add-and-manage-multiple-azure-active-directory-directories"></a>Ajouter et gérer plusieurs annuaires d’Azure Active Directory

Dans Azure Active Directory (AD Azure), chaque répertoire est une ressource entièrement indépendante : un homologue, entièrement équipé, logiquement indépendant des autres répertoires que vous gérez. Il n’existe aucune relation parent-enfant entre les annuaires. Cette indépendance entre les annuaires inclut l’indépendance des ressources, d’autonomie de gestion et de l’indépendance de synchronisation.

##<a name="resource-independence"></a>Indépendance des ressources

Si vous créez ou que vous supprimez une ressource d’un annuaire, il n’a aucun impact sur n’importe quelle ressource dans un autre répertoire, à l’exception de partielle des utilisateurs externes, décrites ci-dessous. Si vous utilisez un domaine personnalisé « contoso.com » avec un répertoire, il ne peut pas être utilisé avec n’importe quel autre répertoire.

##<a name="administrative-independence"></a>Indépendance d’administration

Si un utilisateur non administratif du répertoire « Contoso » crée un répertoire de test 'Test' puis :
- Par défaut, l’utilisateur qui crée un répertoire est ajouté en tant qu’un utilisateur externe de ce nouveau répertoire et du rôle d’administrateur global dans ce répertoire.
- Les administrateurs du répertoire « Contoso » n’ont aucun privilège administratif direct au répertoire « Test », sauf si un administrateur de « Test » leur accorde spécifiquement ces privilèges. Les administrateurs de « Contoso » peuvent contrôler l’accès au répertoire 'Test' si elles contrôlent le compte d’utilisateur qui a créé les « Test ».
- Si vous modifiez (ajouter ou supprimer) un rôle d’administrateur d’un utilisateur dans un répertoire, la modification n’affecte pas de n’importe quel rôle de l’administrateur que l’utilisateur est peut-être dans un autre répertoire.

##<a name="synchronization-independence"></a>Indépendance de synchronisation

Vous pouvez configurer chaque annuaire AD Azure indépendamment pour obtenir des données synchronisées à partir d’une seule instance d’une ou l’autre :
  - L’outil de synchronisation d’annuaires (DirSync), pour synchroniser les données avec une seule forêt Active Directory.
  - Azure connecteur Active Directory pour Forefront Identity Manager, pour synchroniser les données avec une ou plusieurs forêts des locaux et/ou des sources de données non-Azure AD.

##<a name="add-an-azure-ad-directory"></a>Ajouter un annuaire AD Azure

Pour ajouter un répertoire AD Azure dans Azure portal classique, sélectionnez l’extension Azure Active Directory sur la gauche, puis cliquez sur **Ajouter**.

> [AZURE.NOTE]   Contrairement à d’autres ressources d’Azure, vos répertoires ne sont pas les ressources enfants d’un abonnement Azure. Si vous annulez ou autoriser votre abonnement Azure arrive à expiration, vous pouvez toujours accéder vos données de l’annuaire à l’aide de PowerShell d’Azure, l’API de graphique Azure ou d’autres interfaces, telles que le centre d’administration Office 365. Vous pouvez également associer un autre abonnement avec l’annuaire.

Pour obtenir une vue d’ensemble des problèmes de licence AD Azure et les meilleures pratiques, consultez [qu’est Azure Active Directory licence ?](active-directory-licensing-what-is.md).
