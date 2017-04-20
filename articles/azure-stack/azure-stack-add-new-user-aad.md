<properties
    pageTitle="Ajouter un nouveau compte de client de pile d’Azure dans Azure Active Directory | Microsoft Azure"
    description="Après avoir déployé Microsoft Azure pile VT, vous aurez besoin créer le compte d’utilisateur au moins un locataire afin que vous pouvez explorer le portail clients."
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

# <a name="add-a-new-azure-stack-tenant-account-in-azure-active-directory"></a>Ajouter un nouveau compte de client de pile d’Azure dans Azure Active Directory

Après le [déploiement de la VT pile Azure](azure-stack-run-powershell-script.md), vous aurez besoin un compte d’utilisateur client pour vous permettre d’Explorer le portail clients et de vos offres et les plans de test. Vous pouvez créer un compte client via [le portail d’Azure](#create-an-azure-stack-tenant-account-using-the-azure-portal) , ou à [l’aide de PowerShell](#create-an-azure-stack-tenant-account-using-powershell).

## <a name="create-an-azure-stack-tenant-account-using-the-azure-portal"></a>Créer un compte de clients Azure pile via le portail Azure

Vous devez disposer d’un abonnement Azure à utiliser le portail Azure.

1. Ouvrez une session [Azure](http://manage.windowsazure.com).

2.  Dans la barre de navigation de gauche Microsoft Azure, cliquez sur **Active Directory**.

3.  Dans la liste répertoire, cliquez sur le répertoire que vous souhaitez utiliser pour pile d’Azure, ou créez-en un nouveau.

4.  Sur cette page de l’annuaire, cliquez sur **utilisateurs**.

5.  Cliquez sur **Ajouter un utilisateur**.

6.  Dans l’Assistant **Ajouter un utilisateur** , dans la liste **Type d’utilisateur** , cliquez sur **nouvel utilisateur dans votre organisation**.

7.  Dans la zone **nom d’utilisateur** , tapez un nom pour l’utilisateur.

8.  Dans le **@** zone de, sélectionnez l’entrée appropriée.

9.  Cliquez sur la flèche suivant.

10.  Dans la page de **profil utilisateur** de l’Assistant, tapez un **prénom**, un **nom**et un **nom complet**.

11. Dans la liste **rôle** , cliquez sur **utilisateur**.

12. Cliquez sur la flèche suivant.

13. Sur la page **obtenir le mot de passe temporaire** , cliquez sur **créer**.

14. Copiez le **nouveau mot de passe**.

15. Ouvrez une session avec le nouveau compte Microsoft Azure. Modifier le mot de passe lorsque vous y êtes invité.

16. Se connecter à `https://portal.azurestack.local` avec le nouveau compte pour afficher le portail du locataire.

## <a name="create-an-azure-stack-tenant-account-using-powershell"></a>Créer un compte de clients Azure pile à l’aide de PowerShell

Si vous n’avez pas un abonnement Azure, vous ne pouvez pas utiliser le portail Azure pour ajouter un compte d’utilisateur client. Dans ce cas, vous pouvez utiliser le Module Azure pour annuaire Active pour Windows PowerShell, à la place.

> [AZURE.NOTE] Si vous utilisez Microsoft Account (Live ID) au déploiement de la VT de pile Azure, vous ne pouvez pas utiliser PowerShell de DAS pour créer le compte de clients. 

1.  Installez le [Microsoft Online Services - Assistant de connexion pour les professionnels de l’informatique RTW](https://www.microsoft.com/en-us/download/details.aspx?id=41950).

2.  Installez le [Azure Active Directory Module pour Windows PowerShell (version 64 bits)](http://go.microsoft.com/fwlink/p/?linkid=236297) et l’ouvrir.

3.  Exécuter les applets de commande suivantes :




    ```powershell
    # Provide the AAD credential you use to deploy Azure Stack PoC
   
            $msolcred = get-credential
    
    # Add a tenant account "Tenant Admin <username>@<yourdomainname>" with the initial password "<password>".
    
            connect-msolservice -credential $msolcred
            $user = new-msoluser -DisplayName "Tenant Admin" -UserPrincipalName <username>@<yourdomainname> -Password <password>
            Add-MsolRoleMember -RoleName "Company Administrator" -RoleMemberType User -RoleMemberObjectId $user.ObjectId
    
    ```

4.  Connectez-vous à Microsoft Azure avec le nouveau compte. Modifier le mot de passe lorsque vous y êtes invité.

5.  Se connecter à `https://portal.azurestack.local` avec le nouveau compte pour afficher le portail du locataire.



