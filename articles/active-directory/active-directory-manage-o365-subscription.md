<properties
   pageTitle="Gérer l’annuaire d’abonnement à Office 365 dans Azure | Microsoft Azure"
   description="Gestion d’un annuaire d’abonnement Office 365 à l’aide d’Azure Active Directory et le portail classique Azure"
   services="active-directory"
   documentationCenter=""
   authors="curtand"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/23/2016"
   ms.author="curtand"/>

# <a name="manage-the-directory-for-your-office-365-subscription-in-azure"></a>Gérer l’annuaire d’abonnement à Office 365 dans Azure

Cet article explique comment gérer un répertoire qui a été créé pour un abonnement à Office 365, via le portail classique Azure. Vous devez être l’administrateur du Service ou administrateur de collaboration d’un abonnement Azure pour vous connecter au portail Azure classique. Si vous n’avez pas encore un abonnement Azure, vous pouvez inscrire pour une [version d’essai de 30 jours gratuite](https://azure.microsoft.com/trial/get-started-active-directory/) et déployer votre première solution de cloud de moins de 5 minutes, à l’aide de ce lien. Veillez à utiliser le compte de l’école ou de travail que vous utilisez pour vous connecter à Office 365.

Après avoir terminé l’abonnement Azure, vous pouvez vous connecter au portail classique Azure et accéder aux services Azure. Cliquez sur l’extension de Active Directory pour gérer le même répertoire que celui qui authentifie les utilisateurs d’Office 365.

Si vous avez encore un abonnement Azure, le processus pour gérer un répertoire supplémentaire est également simple. Par exemple, Michael Smith peut avoir un abonnement à Office 365 pour Contoso.com. Il a également un abonnement Azure qui il souscrit à l’aide de son compte Microsoft, msmith@hotmail.com. Dans ce cas, il gère les deux annuaires.

  Abonnement |  Office 365  |  Azure
  -------------- | ------------- | -------------------------------
  Nom complet |  Contoso  |     Répertoire d’Azure Active Directory (AD Azure) par défaut
  Nom de domaine  |  Contoso.com  | msmithhotmail.onmicrosoft.com

Il souhaite gérer les identités utilisateur dans le répertoire de Contoso pendant qu’il est connecté à Azure à l’aide de son compte Microsoft, afin qu’il peut activer des fonctionnalités d’Active Directory Azure telles que l’authentification multifactorielle. Le diagramme suivant peut vous aider pour illustrer le processus.

![Diagramme de gérer deux répertoires indépendants](./media/active-directory-manage-o365-subscription/AAD_O365_03.png)

Dans ce cas, les deux répertoires sont indépendants entre eux.

## <a name="to-manage-two-independent-directories"></a>Pour gérer les deux répertoires indépendants
Dans l’ordre pour Michael Smith gérer les deux répertoires pendant qu’il est connecté à Azure en tant que msmith@hotmail.com, qu’il doit effectuer les opérations suivantes :

> [AZURE.NOTE]
> Ces étapes peuvent être effectuées uniquement lorsqu’un utilisateur est connecté avec un compte Microsoft. Si l’utilisateur est connecté à un travail ou le compte de l’établissement, l’option pour **utiliser un répertoire existant** n’est pas disponible. Un compte de travail ou de l’école peut être authentifié uniquement par son répertoire de base (c'est-à-dire, le répertoire où le compte de travail ou de l’école est stocké, et qui est propriétaire de l’entreprise ou à l’école).

1.  Connectez-vous à l' [Azure portal classique](https://manage.windowsazure.com) en tant que msmith@hotmail.com.
2.  Cliquez sur **Nouveau** > **services d’application** > **Active Directory** > **répertoire** > **Créer des personnalisées**.
3.  Cliquez sur utiliser un répertoire existant, puis sélectionnez la case à cocher **je suis prêt à présent déconnecté** .
4.  Connectez-vous au portail Azure classique en tant qu’administrateur global de Contoso.onmicrosoft.com (par exemple, msmith@contoso.com).
5.  Lorsque vous êtes invité à **utiliser le répertoire de Contoso avec Azure ?**, cliquez sur **Continuer**.
6.  Cliquez sur **Fermer la session maintenant**.
7.  Connectez-vous au portail Azure classique en tant que msmith@hotmail.com. Le répertoire de Contoso et le répertoire par défaut s’affichent dans l’extension de l’Active Directory.

Après avoir effectué ces étapes, msmith@hotmail.com est un administrateur global dans le répertoire de Contoso.

## <a name="to-administer-resources-as-the-global-admin"></a>Pour administrer les ressources en tant que l’administrateur global
Maintenant Supposons que Jane Doe doit administrer les sites Web et la base de données de ressources associés à l’abonnement Azure pour msmith@hotmail.com. Avant de lui faire, Michael Smith a besoin effectuer ces étapes supplémentaires :

1.  Connectez-vous à l' [Azure portal classique](https://manage.windowsazure.com) à l’aide du compte d’administrateur de Service pour l’abonnement Azure (dans cet exemple, msmith@hotmail.com).
2.  Transférer l’abonnement dans le répertoire de Contoso : cliquez sur **paramètres** > **abonnements** > sélectionnez l’abonnement > **Modifier le répertoire** > sélectionnez **Contoso (Contoso.com)**. Dans le cadre d’un travail, le transfert ou à l’école, les comptes qui sont des administrateurs de l’abonnement sont supprimés.
3.  Ajouter Jane Doe en tant qu’administrateur de collègues de l’abonnement : cliquez sur **paramètres** > **administrateurs** > sélectionnez l’abonnement > **Ajouter** > type **JohnDoe@Contoso.com**.

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur la relation entre les répertoires et les abonnements, voir [comment un abonnement est associé à un répertoire](active-directory-how-subscriptions-associated-directory.md).
