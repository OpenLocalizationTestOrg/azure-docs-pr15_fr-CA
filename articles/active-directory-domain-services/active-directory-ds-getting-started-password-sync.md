<properties
    pageTitle="Services de domaine Active Directory Azure : Activer la synchronisation de mot de passe | Microsoft Azure"
    description="Mise en route avec Azure des Services de domaine Active Directory"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/20/2016"
    ms.author="maheshu"/>

# <a name="enable-password-synchronization-to-azure-ad-domain-services"></a>Activer la synchronisation de mot de passe pour les Services de domaine Active Directory de Azure
Dans les tâches précédentes, vous avez activé les Services de domaine Active Directory de Azure pour vos clients AD Azure. La tâche suivante consiste à activer les hachages d’informations d’identification requises pour l’authentification NTLM et Kerberos à synchroniser avec les Services de domaine Active Directory de Azure. Une fois la synchronisation des informations d’identification est configurée, les utilisateurs peuvent signer le domaine géré à l’aide de leurs informations d’identification d’entreprise.

Les étapes sont différentes selon que votre organisation dispose d’une annonce Azure cloud uniquement client ou est configuré pour se synchroniser avec votre répertoire local à l’aide d’Azure Connect d’Active Directory.

<br>

> [AZURE.SELECTOR]
- [Annonce d’Azure cloud uniquement client](active-directory-ds-getting-started-password-sync.md)
- [Synchroniser les clients AD Azure](active-directory-ds-getting-started-password-sync-synced-tenant.md)

<br>


## <a name="task-5-enable-password-synchronization-to-aad-domain-services-for-a-cloud-only-azure-ad-tenant"></a>Tâche 5 : Activer la synchronisation de mot de passe aux Services de domaine DAS pour un Azure cloud uniquement les clients AD
Les Services de domaine Active Directory Azure a besoin des informations d’identification hachages dans un format adapté à l’authentification NTLM et Kerberos, pour authentifier les utilisateurs sur le domaine géré. Sauf si vous activez les Services de domaine DAS pour vos clients, Azure AD ne génère ni stocker des hachages de d’informations d’identification au format requis pour l’authentification NTLM ou Kerberos. Pour des raisons de sécurité évidente, Azure annonce également ne stocke pas d’informations d’identification sous forme de texte en clair. Par conséquent, Azure AD n’a pas de moyen de générer ces hachages de d’informations d’identification NTLM ou Kerberos, selon les informations d’identification existantes des utilisateurs.

> [AZURE.NOTE] Si votre organisation possède un nuage seule locataire Azure AD, les utilisateurs qui ont besoin d’utiliser les Services de domaine Active Directory de Azure doit modifier leur mot de passe.

Ce processus de modification de mot de passe entraîne des hachages requis par les Services de domaine Active Directory de Azure pour l’authentification Kerberos et NTLM doit être généré dans Azure annonce les informations d’identification. Vous pouvez soit faire expirer les mots de passe pour tous les utilisateurs dans les clients à utiliser les Services de domaine Active Directory de Azure ou demandez à ces utilisateurs à modifier leurs mots de passe.


### <a name="enable-ntlm-and-kerberos-credential-hash-generation-for-a-cloud-only-azure-ad-tenant"></a>Activer la génération de hachage d’informations d’identification NTLM et Kerberos pour un Azure cloud uniquement les clients AD
Voici des instructions que vous devez fournir aux utilisateurs finaux, afin qu’ils peuvent modifier leurs mots de passe :

1. Accédez à la page de Azure d’accès Active Directory pour votre organisation à [http://myapps.microsoft.com](http://myapps.microsoft.com).

2. Sélectionnez l’onglet **profil** sur cette page.

3. Cliquez sur la mosaïque de **modification mot de passe** sur cette page.

    ![Créer un réseau virtuel pour les Services de domaine Active Directory de Azure.](./media/active-directory-domain-services-getting-started/user-change-password.png)

    > [AZURE.NOTE] Si vous ne voyez pas l’option de **modification mot de passe** sur la page du panneau d’accès, assurez-vous que votre organisation a configuré la [Gestion des mots de passe dans Active Directory Azure](../active-directory/active-directory-passwords-getting-started.md).

4. Dans la page **Modifier le mot de passe** , tapez votre mot de passe (ancien) puis tapez un nouveau mot de passe et confirmez-le. Cliquez sur **Envoyer**.

    ![Créer un réseau virtuel pour les Services de domaine Active Directory de Azure.](./media/active-directory-domain-services-getting-started/user-change-password2.png)

Après avoir modifié votre mot de passe, le mot de passe sera bientôt utilisable dans les Services de domaine Active Directory de Azure. Après quelques minutes (en règle générale, environ 20 minutes), vous pouvez vous connecter aux ordinateurs joints au domaine géré en utilisant le mot de passe qui vient d’être modifié.

<br>

## <a name="related-content"></a>Contenu associé

- [La mise à jour de votre mot de passe](../active-directory/active-directory-passwords-update-your-own-password.md)

- [Mise en route de la gestion des mots de passe dans Active Directory Azure](../active-directory/active-directory-passwords-getting-started.md).

- [Activer la synchronisation de mot de passe aux Services de domaine DAS pour un Azure synchronisé les clients AD](active-directory-ds-getting-started-password-sync-synced-tenant.md)

- [Administrer un domaine géré de Services de domaine Active Directory de Azure](active-directory-ds-admin-guide-administer-domain.md)

- [Participez à une machine virtuelle Windows d’un domaine géré de Services de domaine Active Directory de Azure](active-directory-ds-admin-guide-join-windows-vm.md)

- [Joindre un ordinateur virtuel de Red Hat Enterprise Linux à un domaine géré de Services de domaine Active Directory de Azure](active-directory-ds-admin-guide-join-rhel-linux-vm.md)
