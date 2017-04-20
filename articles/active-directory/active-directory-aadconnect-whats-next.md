<properties
    pageTitle="Azure AD Connect : Étapes et gérer Azure Connect d’annonce | Microsoft Azure"
    description="Apprenez à étendre la configuration par défaut et les tâches opérationnelles pour Azure Connect d’Active Directory."
    services="active-directory"
    documentationCenter=""
    authors="billmath"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/08/2016"
    ms.author="billmath"/>

# <a name="next-steps-and-how-to-manage-azure-ad-connect"></a>Étapes suivantes et comment gérer Azure Connect de publicité
Les éléments suivants sont des rubriques opérationnelles qui vous permettent de personnaliser Azure Connect de Active Directory pour répondre aux besoins et exigences de votre organisation.  

## <a name="add-additional-sync-administrators"></a>Ajouter des administrateurs de synchronisation supplémentaires
Par défaut, seul l’utilisateur qui a fait l’installation et les administrateurs locaux seront en mesure de gérer le moteur de synchronisation installé. Pour les autres personnes puissent y accéder et de gérer le moteur de synchronisation, localisez le groupe nommé ADSyncAdmins sur le serveur local et les ajouter à ce groupe.

## <a name="assigning-licenses-to-azure-ad-premium-and-enterprise-mobility-users"></a>Attribution des licences aux utilisateurs Azure AD Premium et mobilité d’entreprise

Maintenant que vos utilisateurs ont été synchronisées vers le nuage, vous devez leur attribuer une licence afin qu’ils peuvent commencer avec des applications de cloud telles qu’Office 365.

### <a name="to-assign-an-azure-ad-premium-or-enterprise-mobility-suite-license"></a>Pour affecter un Azure AD Premium ou la licence de la Suite Enterprise mobilité
--------------------------------------------------------------------------------
1. Ouvrez une session dans le portail Azure en tant qu’administrateur.
2. Sur la gauche, sélectionnez **Active Directory**.
3. Sur la page Active Directory, double-cliquez sur le répertoire qui contient les utilisateurs que vous souhaitez activer.
4. En haut de la page de l’annuaire, sélectionner des **licences**.
5. Sur la page licences, sélectionnez Active Directory prime ou Suite de mobilité d’entreprise, puis cliquez sur **attribuer**.
6. Dans la boîte de dialogue Sélectionnez les utilisateurs que vous souhaitez attribuer des licences à, puis cliquez sur l’icône de coche pour enregistrer les modifications.


## <a name="verifying-the-scheduled-synchronization-task"></a>Vérification de la tâche de synchronisation planifiée
Si vous souhaitez vérifier l’état d’une synchronisation que cela en vérifiant dans le portail Azure.

### <a name="to-verify-the-scheduled-synchronization-task"></a>Pour vérifier la tâche de synchronisation planifiée
--------------------------------------------------------------------------------
1. Ouvrez une session dans le portail Azure en tant qu’administrateur.
2. Sur la gauche, sélectionnez **Active Directory**.
3. Sur la page Active Directory, double-cliquez sur le répertoire qui contient les utilisateurs que vous souhaitez activer.
4. En haut de la page de l’annuaire, sélectionnez **Intégration d’annuaire**.
5. Sous Intégration avec la note de l’annuaire active directory local de la dernière synchronisation.

<center>![Nuage](./media/active-directory-aadconnect-whats-next/verify.png)</center>

## <a name="starting-a-scheduled-synchronization-task"></a>Démarrage d’une tâche de synchronisation planifiée
Si vous avez besoin exécuter une tâche de synchronisation peut cela en exécutant de nouveau l’Assistant Azure Connect d’AD.  Vous devez fournir vos informations d’identification Active Directory Azure.  Dans l’Assistant, sélectionnez la tâche de **Personnaliser les options de synchronisation** , puis cliquez sur suivant dans l’Assistant. À la fin, assurez-vous que la case **Démarrer le processus de synchronisation, dès la fin de la configuration initiale** est activée.

<center>![Nuage](./media/active-directory-aadconnect-whats-next/startsynch.png)</center>

Pour plus d’informations sur la synchronisation Azure AD Connect : Planificateur, voir [Planificateur de connexion AD Azure](active-directory-aadconnectsync-feature-scheduler.md)


## <a name="additional-tasks-available-in-azure-ad-connect"></a>Tâches supplémentaires disponibles dans Azure Connect de publicité
Après l’installation initiale d’Azure Connect d’Active Directory, vous pouvez toujours démarrer l’Assistant à nouveau à partir du raccourci de page ou le bureau de début Azure Connect d’Active Directory.  Vous remarquerez que passer par l’Assistant à nouveau fournit de nouvelles options dans l’écran des tâches supplémentaires.  

Le tableau suivant fournit un récapitulatif de ces tâches et une brève description de chacun d’eux.

![Règle de jointure](./media/active-directory-aadconnect-whats-next/addtasks.png)


Tâches supplémentaires | Description
------------- | ------------- |
Permet d’afficher le scénario sélectionné  |Vous permet d’afficher votre solution Azure Connect d’annonces en cours.  Cela inclut les paramètres généraux, les annuaires synchronisés, les paramètres de synchronisation, etc..
Personnaliser les options de synchronisation | Permet de modifier la configuration en cours, y compris l’ajout d’autres forêts Active Directory à la configuration ou les options de synchronisation avec l’activation par exemple, utilisateur, groupe, périphérique ou mot de passe en écriture différée.
Activer le Mode de mise en attente |  Cela vous permet d’accéder aux informations qui seront synchronisées plus loin, mais rien n’est exportée vers Azure AD ou Active Directory.  Cela vous permet d’afficher un aperçu de la synchronisation avant qu’ils surviennent.

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur [l’intégration des identités avec Azure Active Directory local](active-directory-aadconnect.md).
