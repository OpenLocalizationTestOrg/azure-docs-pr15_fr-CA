<properties
    pageTitle="Azure Active Directory Domain Services : Joindre une machine virtuelle de Windows Server pour un domaine géré | Microsoft Azure"
    description="Joindre un ordinateur virtuel de Windows Server pour les Services de domaine Active Directory de Azure"
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

# <a name="join-a-windows-server-virtual-machine-to-a-managed-domain"></a>Joindre un ordinateur virtuel de Windows Server pour un domaine géré

> [AZURE.SELECTOR]
- [Azure portal classique - Windows](active-directory-ds-admin-guide-join-windows-vm.md)
- [PowerShell - Windows](active-directory-ds-admin-guide-join-windows-vm-classic-powershell.md)

<br>

Cet article vous montre comment joindre un ordinateur virtuel exécutant Windows Server 2012 R2 pour les Services de domaine Active Directory de Azure géré à un domaine, le portail classique Azure.


## <a name="step-1-create-the-windows-server-virtual-machine"></a>Étape 1 : Créer la machine virtuelle Windows Server
Suivez les instructions décrites dans le didacticiel [créer un ordinateur virtuel exécutant Windows Azure portail classique](../virtual-machines/virtual-machines-windows-classic-tutorial.md) . Il est important de s’assurer que ce nouveau ordinateur virtuel est joint au même réseau virtuel dans lequel vous avez activé les Services de domaine Active Directory de Azure. L’option « Création rapide » ne vous autorise pas à joindre l’ordinateur virtuel à un réseau virtuel. Par conséquent, vous devez utiliser l’option « À partir de la galerie » pour créer la machine virtuelle.

Effectuez les étapes suivantes pour créer un ordinateur virtuel de Windows connecté au réseau virtuel dans lequel vous avez activé les Services de domaine Active Directory de Azure.

1. Dans le portail Azure classique, sur la barre en bas de la fenêtre, cliquez sur **Nouveau**.

2. Sous **Calculer**, cliquez sur **ordinateur virtuel**, puis cliquez sur **à partir de la galerie**.

3. Le premier écran vous permet de **Choisir une Image** pour votre machine virtuelle à partir de la liste des images disponibles. Choisir l’image appropriée.

    ![Choisissez l’image](./media/active-directory-domain-services-admin-guide/create-windows-vm-select-image.png)

4. Le deuxième écran vous permet de choisir un nom d’ordinateur, taille, nom d’utilisateur d’administration et des mot de passe. Utiliser la couche et la taille nécessaire à l’exécution de votre application ou une charge de travail. Le nom d’utilisateur que vous choisissez ici est un utilisateur de l’administrateur local sur l’ordinateur. N’entrez pas d’informations d’identification d’un compte utilisateur de domaine ici.

    ![Configuration de machine virtuelle](./media/active-directory-domain-services-admin-guide/create-windows-vm-config.png)

5. Le troisième écran vous permet de configurer les ressources pour la mise en réseau, de stockage et de disponibilité. Veillez à que sélectionner le réseau virtuel dans lequel vous avez activé les Services de domaine Active Directory de Azure dans la liste déroulante **Région/affinité/virtuels du groupe réseau** . Spécifiez le **Nom DNS du Service Cloud** en fonction de la machine virtuelle.

    ![Sélectionnez un réseau virtuel pour la machine virtuelle](./media/active-directory-domain-services-admin-guide/create-windows-vm-select-vnet.png)

    > [AZURE.WARNING]
    Assurez-vous que vous participez à la machine virtuelle au même réseau virtuel dans lequel vous avez activé les Services de domaine Active Directory de Azure. Par conséquent, la machine virtuelle peut « voir » le domaine et effectuer des tâches telles que la jonction de domaine. Si vous choisissez de créer l’ordinateur virtuel dans un autre réseau virtuel, se connecter à ce réseau virtuel au réseau virtuel dans lequel vous avez activé les Services de domaine Active Directory de Azure.

6. Le quatrième écran vous permet d’installer l’Agent de l’ordinateur virtuel et de configurer certaines des extensions disponibles.

    ![Terminé](./media/active-directory-domain-services-admin-guide/create-windows-vm-done.png)

7. Une fois que la machine virtuelle est créée, le portail classique répertorie le nouvel ordinateur virtuel sous le nœud **ordinateurs virtuels** . La machine virtuelle et un service en nuage sont automatiquement démarrés et leur état est répertorié comme **en cours d’exécution**.

    ![Ordinateur virtuel est en cours d’exécution](./media/active-directory-domain-services-admin-guide/create-windows-vm-running.png)


## <a name="step-2-connect-to-the-windows-server-virtual-machine-using-the-local-administrator-account"></a>Étape 2 : Se connecter à la machine virtuelle Windows Server en utilisant le compte administrateur local
Maintenant, nous connecter à l’ordinateur virtuel Windows Server nouvellement créé, à le rejoindre le domaine. Utilisez les informations d’identification administrateur local que vous avez spécifié lors de la création de la machine virtuelle, à s’y connecter.

Effectuez les opérations suivantes pour vous connecter à l’ordinateur virtuel.

1. Accédez au nœud des **ordinateurs virtuels** dans le portail classique. Sélectionnez l’ordinateur virtuel que vous avez créé à l’étape 1 et cliquez sur **connexion** dans la barre de commandes au bas de la fenêtre.

    ![Se connecter à la machine virtuelle Windows](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)

2. Le portail classique vous invite à ouvrir ou enregistrer un fichier avec une extension « .rdp », qui est utilisée pour se connecter à l’ordinateur virtuel. Cliquez pour ouvrir le fichier lorsqu’il a terminé le téléchargement.

3. À l’invite de connexion, entrez vos **informations d’identification de l’administrateur local**, que vous avez spécifiée lors de la création de la machine virtuelle. Par exemple, nous avons utilisé 'localhost\mahesh' dans cet exemple.

À ce stade, vous devez avoir ouvert une session l’ordinateur virtuel Windows nouvellement créé à l’aide de droits d’administrateur locales. L’étape suivante consiste à joindre l’ordinateur virtuel au domaine.


## <a name="step-3-join-the-windows-server-virtual-machine-to-the-aad-ds-managed-domain"></a>Étape 3 : Jointure la machine virtuelle Windows Server dans le domaine géré DAS-DS
Effectuez les étapes suivantes pour joindre l’ordinateur virtuel Windows Server dans le domaine géré DAS-DS.

1. Se connecter au serveur Windows, comme indiqué à l’étape 2. À partir de l’écran de démarrage, ouvrez le **Gestionnaire de serveur**.

2. Dans le volet gauche de la fenêtre Gestionnaire de serveur, cliquez sur **Serveur Local** .

    ![Lancez le Gestionnaire de serveur sur l’ordinateur virtuel](./media/active-directory-domain-services-admin-guide/join-domain-server-manager.png)

3. Dans la section **Propriétés** , cliquez sur **groupe de travail** . Dans la page de propriétés **Propriétés système** , cliquez sur **Modifier** pour joindre le domaine.

    ![Page de propriétés système](./media/active-directory-domain-services-admin-guide/join-domain-system-properties.png)

4. Spécifiez le nom de domaine de vos Services de domaine Active Directory de Azure gérés du domaine dans la zone de texte **domaine** , puis cliquez sur **OK**.

    ![Spécifier le domaine à joindre](./media/active-directory-domain-services-admin-guide/join-domain-system-properties-specify-domain.png)

5. Vous êtes invité à entrer vos informations d’identification pour joindre le domaine. Assurez-vous que vous **Spécifiez les informations d’identification d’un utilisateur appartenant à un contrôleur de domaine Administrateurs DAS** groupe. Seuls les membres de ce groupe ont des privilèges pour joindre les ordinateurs au domaine géré.

    ![Spécifiez les informations d’identification de jonction de domaine](./media/active-directory-domain-services-admin-guide/join-domain-system-properties-specify-credentials.png)

6. Vous pouvez spécifier les informations d’identification d’une des manières suivantes :

    - Format UPN : spécifiez le suffixe UPN pour le compte d’utilisateur, tel que configuré dans Azure AD. Dans cet exemple, le suffixe UPN de l’utilisateur 'bob' est 'bob@domainservicespreview.onmicrosoft.com'.

    - Format de SAMAccountName : vous pouvez spécifier le nom du compte au format SAMAccountName. Dans cet exemple, l’utilisateur 'bob' devez saisir 'CONTOSO100\bob'.

        > [AZURE.NOTE] **Nous recommandons d’utiliser le format UPN pour spécifier les informations d’identification.** SAMAccountName peut être généré automatiquement si le préfixe UPN de l’utilisateur est trop long (par exemple, 'joereallylongnameuser'). Si plusieurs utilisateurs ont le même préfixe UPN (par exemple, « bob ») dans vos clients AD Azure, leur format SAMAccountName peut être généré automatiquement par le service. Dans ces cas, le format UPN peut servir fiable pour ouvrir une session sur le domaine.

7. Une fois la jointure de domaines est réussie, vous voyez le message suivant s’affiche pour vous accueillir dans le domaine. Redémarrez l’ordinateur virtuel pour l’opération de jonction de domaine terminer.

    ![Bienvenue dans le domaine](./media/active-directory-domain-services-admin-guide/join-domain-done.png)


## <a name="troubleshooting-domain-join"></a>Résolution des problèmes de jonction de domaine
### <a name="connectivity-issues"></a>Problèmes de connectivité
Si l’ordinateur virtuel ne peut pas trouver le domaine, reportez-vous aux étapes de dépannage suivantes :

- Assurez-vous que l’ordinateur virtuel est connecté au même réseau virtuel que vous avez activé les Services de domaine. Si ce n’est pas le cas, la machine virtuelle ne peut pas se connecter au domaine et par conséquent ne peut pas joindre le domaine.

- Si l’ordinateur virtuel est connecté à un autre réseau virtuel, assurez-vous que ce réseau virtuel est connecté au réseau virtuel dans lequel vous avez activé les Services de domaine.

- Essayez de taper le domaine en utilisant le nom de domaine du domaine géré (par exemple, « ping contoso100.com »). Si vous ne parvenez pas à le faire, essayez de ping sur les adresses IP pour le domaine affiché sur la page où vous avez activé les Services de domaine Active Directory de Azure (par exemple, « ping 10.0.0.4 »). Si vous êtes en mesure d’exécuter la commande ping l’adresse IP mais pas sur le domaine, DNS peut être mal configuré. Vous ne pouvez pas avoir configuré les adresses IP du domaine comme serveurs DNS pour le réseau virtuel.

- Essayez de vider le cache de résolution DNS sur l’ordinateur virtuel (ipconfig /flushdns).

Si vous accédez à la boîte de dialogue qui vous demande des informations d’identification pour joindre le domaine, vous n’avez pas de problèmes de connectivité.


### <a name="credentials-related-issues"></a>Problèmes liés aux informations d’identification
Reportez-vous aux étapes suivantes si vous rencontrez des problèmes avec les informations d’identification et que vous ne parvenez pas à joindre le domaine.

- Essayez d’utiliser le format UPN pour spécifier les informations d’identification. SAMAccountName pour votre compte peut être généré automatiquement s’il existe plusieurs utilisateurs avec le même préfixe UPN dans vos clients, ou si votre préfixe UPN est trop long. Par conséquent, le format de SAMAccountName pour votre compte peut être différent de ce que vous pensez ou utilisez dans votre domaine local.

- Essayez d’utiliser les informations d’identification d’un compte d’utilisateur appartenant au groupe « Administrateurs du contrôleur de domaine DAS » pour joindre des ordinateurs au domaine géré.

- Assurez-vous d’avoir [activé la synchronisation de mot de passe](active-directory-ds-getting-started-password-sync.md) , conformément à la procédure décrite dans le guide Mise en route.

- Assurez-vous d’utiliser le nom UPN de l’utilisateur tel que configuré dans Azure AD (par exemple, 'bob@domainservicespreview.onmicrosoft.com') pour vous connecter.

- Vérifiez que vous avez attendu assez longtemps pour la synchronisation de mot de passe à une telle que spécifiée dans le guide Mise en route.


## <a name="related-content"></a>Contenu associé

- [Services de domaine Active Directory Azure - guide de mise en route](./active-directory-ds-getting-started.md)

- [Administrer un domaine géré de Services de domaine Active Directory de Azure](./active-directory-ds-admin-guide-administer-domain.md)
