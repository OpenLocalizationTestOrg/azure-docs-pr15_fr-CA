<properties
    pageTitle="Comment obtenir un locataire AD Azure | Microsoft Azure"
    description="Comment obtenir un locataire Azure Active Directory pour l’enregistrement et la création d’applications."
    services="active-directory"
    documentationCenter=""
    authors="dstrockis"
    manager="terrylan"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="09/28/2015"
    ms.author="dastrock"/>

# <a name="how-to-get-an-azure-active-directory-tenant"></a>Comment obtenir un locataire Azure Active Directory

Dans Azure Active Directory (AD Azure), un [client](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant) est représentatif d’une organisation.  Il s’agit d’une instance dédiée du service publicité Azure qui reçoit et propriétaire lorsqu’il s’inscrit pour un service de cloud Microsoft Office 365, Microsoft Intune ou Azure d’une organisation.  Chaque client AD Azure est distinct et séparé des autres locataires AD Azure.  

Un locataire héberge les utilisateurs d’une entreprise et les informations en - leurs mots de passe, données de profil utilisateur, les autorisations et ainsi de suite.  Il contient également les groupes, les applications et les autres informations relatives à une organisation et sa sécurité.

Pour permettre aux utilisateurs d’AD Azure pour vous connecter à votre application, vous devez inscrire votre application dans un client de votre choix.  Publication d’une application dans un locataire Azure AD est **totalement gratuit**.  En fait, la plupart des développeurs crée plusieurs locataires et applications pour l’expérimentation, le développement, la zone de transit et à des fins de test.  Les organisations qui vous inscrire à et d’utilisent votre application peuvent éventuellement choisir d’acheter des licences s’ils souhaitent tirer parti des fonctionnalités avancées directory.

Par conséquent, comment faire sur l’obtention d’un locataire AD Azure ?  Ce processus peut être un autre voire vous :

- [Avoir un abonnement Office 365](#use-an-existing-office-365-subscription)
- [Avoir un abonnement Azure associé à une Account de Microsoft](#use-an-msa-azure-subscription)
- [Avoir un abonnement Azure existant associé à un compte d’organisation](#use-an-organizational-azure-subscription)
- [Aucun des éléments ci-dessus et souhaitez recommencer depuis le début](#start-from-scratch)

## <a name="use-an-existing-office-365-subscription"></a>Utiliser un abonnement Office 365
Si vous avez un abonnement Office 365, vous avez déjà un locataire AD Azure ! Vous pouvez vous connecter au [portail Azure](https://portal.azure.com) avec votre compte O365 et commencer à utiliser AD Azure.

## <a name="use-an-msa-azure-subscription"></a>Utiliser un abonnement MSA Azure
Si vous êtes déjà inscrit pour un abonnement Azure avec votre Account Microsoft, vous avez déjà un locataire !  Lors de la connexion au [Portail Azure](https://portal.azure.com), vous allez être automatiquement connecté à vos clients par défaut. Vous êtes libre d’utiliser ce client comme vous le souhaitez -, mais vous souhaiterez peut-être créer un compte d’administrateur d’organisation.

Pour ce faire, procédez comme suit.  Ou bien, vous souhaitez créer un nouveau client et créer un administrateur de ce client suivant un processus similaire.

1.  Se connecter au [Portail Azure](https://portal.azure.com) avec votre compte individuel
2.  Accédez à la section « Azure Active Directory » du portail (situé dans la barre de navigation de gauche, sous **Plus de Services**)
3.  Vous devez être connecté automatiquement à « Répertoire par défaut », sinon vous pouvez changer de répertoire en cliquant sur votre nom de compte dans le coin supérieur droit.
4.  Dans la section **Tâches rapides** , sélectionnez **Ajouter un utilisateur**.
5.  Dans le formulaire utilisateur Ajouter, fournir les informations suivantes :

    - Nom : (choisissez une valeur appropriée)
    - Nom d’utilisateur : (choisissez un nom d’utilisateur pour cet administrateur)
    - Profil : (à renseigner les valeurs appropriées pour le prénom, nom, fonction et service)
    - Rôle : Administrateur Global

6.  Lorsque vous avez rempli le formulaire utilisateur Ajouter et recevez le mot de passe temporaire pour le nouvel utilisateur d’administration, veillez à enregistrer ce mot de passe que vous devez ouvrir une session avec ce nouvel utilisateur pour modifier le mot de passe. Vous pouvez également envoyer le mot de passe directement à l’utilisateur, à l’aide d’un autre e-mail.
7.  Cliquez sur **créer** pour créer le nouvel utilisateur.
8.  Pour modifier le mot de passe temporaire, connectez-vous à [https://login.microsoftonline.com](https://login.microsoftonline.com) avec ce nouveau compte utilisateur et modifier le mot de passe lors d’une demande.


## <a name="use-an-organizational-azure-subscription"></a>Utiliser un abonnement Azure d’organisation
Si vous êtes déjà inscrit pour un abonnement Azure avec votre compte d’organisation, vous avez déjà un locataire !  Dans le [Portail Azure](https://portal.azure.com), vous devriez trouver un locataire lorsque vous naviguez vers « Plus Services » et « Azure Active Directory ».  Vous êtes libre d’utiliser ce client comme vous le souhaitez. 


## <a name="start-from-scratch"></a>Recommencer depuis le début
Si tous les éléments ci-dessus est incompréhensible pour vous, ne vous inquiétez pas.  Visitez le site [https://account.windowsazure.com/organization](https://account.windowsazure.com/organization) pour vous inscrire pour Azure avec une nouvelle organisation simplement.  Une fois le processus terminé, vous disposerez de vos propres clients AD Azure avec le nom de domaine que vous avez choisi lors de l’inscription des.  Dans le [Portail Azure](https://portal.azure.com), vous trouverez vos clients en accédant à « Azure Active Directory » dans le NAV de gauche.

Dans le cadre du processus d’inscription pour Azure, vous devrez fournir les détails de la carte de crédit.  Vous pouvez procéder en toute confiance - pas être débités pour la publication des applications dans Azure AD ou création de nouveaux locataires.
