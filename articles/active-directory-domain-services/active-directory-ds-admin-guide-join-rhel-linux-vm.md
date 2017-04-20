<properties
    pageTitle="Azure Active Directory Domain Services : Joindre un domaine géré une VM RHEL | Microsoft Azure"
    description="Joindre un ordinateur virtuel de Red Hat Enterprise Linux pour les Services de domaine Active Directory de Azure"
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
    ms.topic="article"
    ms.date="10/02/2016"
    ms.author="maheshu"/>

# <a name="join-a-red-hat-enterprise-linux-7-virtual-machine-to-a-managed-domain"></a>Joindre un ordinateur virtuel de Red Hat Enterprise Linux 7 à un domaine géré
Cet article vous montre comment joindre un ordinateur virtuel de Red Hat Enterprise Linux (RHEL) 7 à un domaine géré de Services de domaine Active Directory de Azure.

## <a name="provision-a-red-hat-enterprise-linux-virtual-machine"></a>Mettre en service un ordinateur virtuel de Red Hat Enterprise Linux
Procédez comme suit pour configurer un ordinateur virtuel de RHEL 7 via le portail Azure.

1. Connectez-vous au [portail Azure](https://portal.azure.com).

    ![Tableau de bord de portail Azure](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-dashboard.png)

2. Cliquez sur **Nouveau** dans le volet de gauche et tapez **Red Hat** dans la barre de recherche, comme illustré dans la capture d’écran suivante. Entrées de Red Hat Enterprise Linux apparaissent dans les résultats de recherche. Cliquez sur **Red Hat Enterprise Linux 7.2**.

    ![Sélectionnez RHEL dans les résultats](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-find-rhel-image.png)

3. Les résultats de la recherche dans le volet **tout** doivent répertorier l’image de Red Hat Enterprise Linux 7.2. Cliquez sur **Red Hat Enterprise Linux 7.2** pour afficher plus d’informations sur l’image de machine virtuelle.

    ![Sélectionnez RHEL dans les résultats](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-select-rhel-image.png)

4. Dans le volet de **Red Hat Enterprise Linux 7.2** , vous devriez voir plus d’informations sur l’image de machine virtuelle. Dans la liste déroulante **Sélectionner un modèle de déploiement** , sélectionnez **classique**. Cliquez ensuite sur le bouton **créer** .

    ![Afficher les détails des images](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-clicked.png)

5. Dans le volet **Créer un ordinateur virtuel** , entrez le **Nom d’hôte** pour le nouvel ordinateur virtuel. Également spécifier un nom d’utilisateur administrateur local dans le champ **nom d’utilisateur** et un **mot de passe**. Vous pouvez également choisir d’utiliser un code SSH pour authentifier l’utilisateur de l’administrateur local. Sélectionnez également un **Niveau de prix** pour la machine virtuelle.

    ![Créer l’ordinateur virtuel - Détails de base](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-basic-details.png)

6. Cliquez sur **Configuration facultative**. Dans le volet de **configuration facultative** , cliquez sur **réseau**.

    ![Créer des VM - configuration réseau virtuel](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-configure-vnet.png)

7. Un volet intitulé **réseau**s’affiche. Dans le volet **réseau** , cliquez sur le **Réseau virtuel** pour sélectionner le réseau virtuel auquel la VM Linux doit être déployée. Le volet **Réseau virtuel** s’ouvre. Dans le volet **Réseau virtuel** , choisissez l’option **utiliser un réseau virtuel** . Sélectionnez le réseau virtuel dans lequel les Services de domaine Active Directory Azure est disponible. Dans cet exemple, nous choisir le réseau virtuel 'MyPreviewVNet'.

    ![Créer la machine virtuelle, sélectionnez le réseau virtuel](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-select-vnet.png)

8. Dans le volet de **configuration facultative** , cliquez sur le bouton **OK** .

    ![Créer des VM - réseau virtuel sélectionné](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-vnet-selected.png)

9. Vous êtes maintenant prêt à créer l’ordinateur virtuel. Dans le volet **Créer un ordinateur virtuel** , cliquez sur le bouton **créer** .

    ![Créer des VM - prêt](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm.png)

10. Déploiement de la nouvelle machine virtuelle en fonction de l’image de RHEL 7.2 doit commencer.

  ![Créer des VM - déploiement démarré](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-deployment-started.png)

11. Après quelques minutes, l’ordinateur virtuel doit être déployé avec succès et prêt pour utilisation.

  ![Créer des VM - déployé](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-deployed.png)



## <a name="connect-remotely-to-the-newly-provisioned-linux-virtual-machine"></a>Se connecter à distance à la machine virtuelle Linux qui vient d’être mis en service
La machine virtuelle RHEL 7.2 a été mis en service dans Azure. La tâche suivante consiste à se connecter à distance à l’ordinateur virtuel.

**Se connecter à la machine virtuelle RHEL 7.2** Suivez les instructions dans l’article [comment ouvrir une session sur un ordinateur virtuel exécutant Linux](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md).

Le reste des étapes supposent que vous utilisez le client PuTTY SSH pour se connecter à la machine virtuelle RHEL. Pour plus d’informations, consultez la [page de téléchargement de PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

1. Ouvrez le programme mastic.

2. Entrez le **Nom d’hôte** de la machine virtuelle RHEL nouvellement créée. Dans cet exemple, notre machine virtuelle a le nom d’hôte « contoso-rhel.cloudapp .net ». Si vous n’êtes pas sûr du nom d’hôte de votre machine virtuelle, consultez le tableau de bord de machine virtuelle sur le portail Azure.

    ![PuTTY se connecter](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-connect.png)

3. Ouvrez une session sur l’ordinateur virtuel avec les informations d’identification administrateur local que vous avez spécifié lors de la création de la machine virtuelle. Dans cet exemple, nous avons utilisé le compte administrateur local « mahesh ».

    ![Connexion de mastic](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-login.png)


## <a name="install-required-packages-on-the-linux-virtual-machine"></a>Installer les packages requis sur la machine virtuelle Linux
Une fois connecté à l’ordinateur virtuel, la tâche suivante consiste à installer les packages requis pour la jonction de domaine sur l’ordinateur virtuel. Effectuez les opérations suivantes :

1. **Installation realmd :** Le package realmd est utilisé pour la jointure de domaines. Dans votre terminal mastic, tapez la commande suivante :

    sudo yum installation realmd

    ![Installation de realmd](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-install-realmd.png)

    Après quelques minutes, le package realmd doit obtenir installé sur l’ordinateur virtuel.

    ![realmd installé](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-realmd-installed.png)

3. **Installation sssd :** Le package realmd dépend de sssd pour effectuer des opérations de jointure de domaine. Dans votre terminal mastic, tapez la commande suivante :

    sudo yum installation sssd

    ![Installation de sssd](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-install-sssd.png)

    Après quelques minutes, le package sssd doit obtenir installé sur l’ordinateur virtuel.

    ![realmd installé](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-sssd-installed.png)

4. **Installation de kerberos :** Dans votre terminal mastic, tapez la commande suivante :

    installation d’yum sudo krb5-station de travail krb5-libs

    ![Installation de kerberos](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-install-kerberos.png)

    Après quelques minutes, le package realmd doit obtenir installé sur l’ordinateur virtuel.

    ![Installé de Kerberos](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-kerberos-installed.png)


## <a name="join-the-linux-virtual-machine-to-the-managed-domain"></a>Joindre le domaine géré la machine virtuelle Linux
Maintenant que les packages requis sont installés sur la machine virtuelle Linux, la tâche suivante consiste à joindre l’ordinateur virtuel au domaine géré.

1. Découvrir le domaine géré DAS les Services de domaine. Dans votre terminal mastic, tapez la commande suivante :

    sudo domaine découvrir CONTOSO100.COM

    ![Découverte de domaine](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-realmd-discover.png)

    Si la **découverte de domaine** ne peut pas trouver votre domaine géré, assurez-vous que le domaine est accessible à partir de la machine virtuelle (try ping). Assurez-vous également que l’ordinateur virtuel a en effet été déployé sur le même réseau virtuel dans lequel le domaine géré est disponible.

2. Initialiser kerberos. Dans votre terminal mastic, tapez la commande suivante. Veillez à spécifier un utilisateur qui appartient au groupe « Administrateurs du contrôleur de domaine DAS ». Seulement ces utilisateurs peuvent joindre des ordinateurs au domaine géré.

    kinitbob@CONTOSO100.COM

    ![Kinit](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-kinit.png)

    Assurez-vous que vous spécifiez le nom de domaine en lettres capitales, kinit autre échoue.

3. Joindre l’ordinateur au domaine. Dans votre terminal mastic, tapez la commande suivante. Spécifiez le même utilisateur que vous avez spécifié à l’étape précédente ('kinit »).

    jointure de domaine sudo--commentaires CONTOSO100.COM -U'bob@CONTOSO100.COM'

    ![Jointure de domaine](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-realmd-join.png)

Vous devez obtenir un message (« est correctement inscrit machine dans le domaine ») lorsque l’ordinateur est correctement inscrit pour le domaine géré.


## <a name="verify-domain-join"></a>Vérifiez la jonction de domaine
Vous pouvez vérifier rapidement si l’ordinateur a été joint correctement au domaine géré. Se connecter au nouveau domaine VM RHEL à l’aide de SSH et d’un compte d’utilisateur de domaine et puis vérifiez pour voir si le compte d’utilisateur a été correctement résolu.

1. Dans votre terminal mastic, tapez la commande suivante pour vous connecter à la nouvellement domaine rejoint la machine virtuelle RHEL à l’aide de SSH. Utilisez un compte de domaine auquel appartient le domaine géré (par exemple, 'bob@CONTOSO100.COM' dans ce cas.)

    SSH -l bob@CONTOSO100.COM rhel.cloudapp.net-contoso

2. Dans votre terminal mastic, tapez la commande suivante pour voir si le répertoire de base a été initialisé correctement.

    mot de passe

3. Dans votre terminal mastic, tapez la commande suivante pour voir si les appartenances aux groupes sont résolus correctement.

    ID

Un exemple de sortie de ces commandes suit :

![Vérifiez la jonction de domaine](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-verify-domain-join.png)


## <a name="troubleshooting-domain-join"></a>Résolution des problèmes de jonction de domaine
Reportez-vous à l’article de [résolution des problèmes la jointure de domaines](active-directory-ds-admin-guide-join-windows-vm.md#troubleshooting-domain-join) .


## <a name="related-content"></a>Contenu associé
- [Services de domaine Active Directory Azure - guide de mise en route](./active-directory-ds-getting-started.md)

- [Joindre un ordinateur virtuel de Windows Server pour un domaine géré de Services de domaine Active Directory de Azure](active-directory-ds-admin-guide-join-windows-vm.md)

- [Comment faire pour ouvrir une session sur un ordinateur virtuel exécutant Linux](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md).

- [L’installation de Kerberos](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Managing_Smart_Cards/installing-kerberos.html)

- [Red Hat Enterprise Linux 7 - Guide d’intégration de Windows](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Windows_Integration_Guide/index.html)
