
<properties
    pageTitle="Modifier le locataire Azure Active Directory dans Azure RemoteApp | Microsoft Azure"
    description="Apprenez à modifier le locataire Azure Active Directory associé à Azure RemoteApp"
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />



# <a name="change-the-azure-active-directory-tenant-in-azure-remoteapp"></a>Modifier le locataire Azure Active Directory dans Azure RemoteApp

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lecture de l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus de détails.

RemoteApp Azure utilise Azure Active Directory (AD Azure) pour autoriser l’accès de l’utilisateur. Le locataire annonce uniquement Azure que vous pouvez utiliser dans Azure RemoteApp est celui associé à l’abonnement Azure. Vous pouvez afficher l’abonnement associé dans la page **paramètres** du portail. Consultez la colonne de **répertoire** dans l’onglet **abonnements** .

> [AZURE.NOTE] Pour que cette modification réussisse, supprimez d’abord tous les utilisateurs du locataire Azure Active Directory existant à partir de toutes les collections Azure RemoteApp. Pour ce faire, accédez au portail Azure, accédez à l’onglet **Azure RemoteApp** et ouvrez chaque collection Azure RemoteApp. Accédez à l’onglet **utilisateurs** et supprimer des utilisateurs qui appartiennent à vos clients d’Azure Active Directory en cours. Répétez pour toutes les collections Azure RemoteApp existantes. Sans cette opération, vous ne serez pas en mesure de créer ou de corriger des collections.

Si vous souhaitez utiliser un autre client, suivez cette procédure pour modifier l’association à votre abonnement :

1. Dans le portail, supprimer des utilisateurs d’AD Azure auquel vous avez donné accès aux collections d’Azure RemoteApp. (Voir la remarque précédente pour obtenir la procédure sur la procédure à suivre.)


2. Définir un compte Microsoft (anciennement appelé un identifiant Live ID) comme l’administrateur de Service. (Ne pas savoir si vous êtes déjà l’administrateur de service ? Vous trouverez en cliquant sur **Paramètres -> administrateurs**.) Maintenant, voici comment vous modifier ce paramètre :
    1. Cliquez sur l’utilisateur dans le coin supérieur droit, puis cliquez sur **affichage de ma facture**.
    2. Cliquez sur l’abonnement. Puis, sur la nouvelle page, faites défiler vers le bas et cliquez sur **Modifier les détails de l’abonnement** à droite. (Tri du bas au milieu à droite, si cela vous aide à trouver.)
    3. Tapez le compte Microsoft de l’utilisateur qui doit être l’administrateur du service.

3. Maintenant, déconnectez-vous du portail et puis connectez-vous précédent avec le compte Microsoft que vous avez spécifié à l’étape précédente.


4. Cliquez sur **Nouveau -> application Services -> Active Directory -> Directory -> personnalisé créer**.
5. Sous **annuaire**, cliquez sur **utiliser un répertoire existant**. Nous allons devoir maintenant vous connecter sur le portail, **que je suis prêt à être déconnecté maintenant**le souhaitent.
6. Réinscrivez-vous dans le portail comme un administrateur global du répertoire que vous souhaitez ajouter. (Si vous n’avez pas déjà un administrateur global, vous serez après une séquence de connexion et puis déconnectez-vous.)
7. Vous devez vous connecter si vous souhaitez utiliser vos clients Active Directory existant avec votre abonnement. Cliquez sur **Continuer**, puis cliquez sur **Fermer la session maintenant**.
5. Connectez-vous à nouveau et revenir à **Paramètres -> abonnements**. Activez votre abonnement, puis cliquez sur **Modifier le répertoire**. Sélectionnez le locataire Azure annonce que vous souhaitez utiliser.



Vous pouvez les clients d’utiliser la nouvelle publicité Azure maintenant pour contrôler l’accès à l’abonnement Azure et à configurer l’accès utilisateur dans Azure RemoteApp.
