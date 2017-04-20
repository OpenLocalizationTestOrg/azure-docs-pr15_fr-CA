<properties
    pageTitle="Azure Active Directory Domain Services : Administrer le DNS sur les domaines gérés | Microsoft Azure"
    description="Gérer DNS sur les domaines gérés Azure des Services de domaine Active Directory"
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
    ms.date="10/03/2016"
    ms.author="maheshu"/>

# <a name="administer-dns-on-an-azure-ad-domain-services-managed-domain"></a>Gérer DNS sur un domaine géré de Services de domaine Active Directory de Azure
Services de domaine Active Directory Azure inclut un serveur DNS (résolution de nom de domaine) qui offre une résolution DNS pour le domaine géré. Parfois, vous devrez peut-être configurer un serveur DNS dans le domaine géré. Vous devrez peut-être créer des enregistrements DNS pour les ordinateurs qui ne sont pas joints au domaine, de configurer des adresses IP virtuelles pour les programmes d’équilibrage de charge ou de configurer les redirecteurs DNS externes. Pour cette raison, les utilisateurs qui appartiennent au groupe « Administrateurs du contrôleur de domaine DAS » bénéficient des privilèges d’administration DNS sur le domaine géré.


## <a name="before-you-begin"></a>Avant de commencer
Pour effectuer les tâches répertoriées dans cet article, vous devez :

1. Un **abonnement Azure**valide.

2. Un **répertoire d’Azure AD** - soit synchronisé avec un répertoire local ou un répertoire de nuage uniquement.

3. **Les Services de domaine Active Directory de Azure** doit être activée pour le répertoire Active Directory Azure. Si vous n’avez pas fait, suivez toutes les tâches décrites dans le [guide Mise en route](./active-directory-ds-getting-started.md).

4. **À un domaine un ordinateur virtuel** à partir duquel vous administrez le domaine géré les Services de domaine Active Directory de Azure. Si vous n’avez pas une telle machine virtuelle, suivez toutes les tâches décrites dans l’article intitulé [rejoindre une machine virtuelle Windows un domaine géré](./active-directory-ds-admin-guide-join-windows-vm.md).

5. Vous devez les informations d’identification d’un **compte d’utilisateur appartenant au groupe « Administrateurs du contrôleur de domaine DAS »** dans le répertoire, pour administrer le DNS pour votre domaine géré.

<br>

## <a name="task-1---provision-a-domain-joined-virtual-machine-to-remotely-administer-dns-for-the-managed-domain"></a>Tâche 1 : disposition un ordinateur virtuel à un domaine à administrer à distance le DNS pour le domaine géré
Azure domaines gérés de Services de domaine Active Directory peuvent être gérés à distance à l’aide des outils d’administration Active Directory familiers tels que Active Directory Administrative Center (ADAC) ou Active Directory PowerShell. De même, DNS pour le domaine géré peut être administré à distance en utilisant les outils d’administration de serveur DNS.

Les administrateurs de votre annuaire AD Azure n’ont pas les privilèges pour se connecter à des contrôleurs de domaine sur le domaine géré via Bureau à distance. Les membres du groupe « Administrateurs de contrôleur de domaine DAS » peuvent administrer le DNS pour les domaines gérés à distance à l’aide des outils du serveur DNS à partir d’un ordinateur client Windows Server qui est joint au domaine géré. Outils du serveur DNS peuvent être installés dans le cadre de la fonctionnalité en option Outils d’Administration de serveur distant (RSAT) sur Windows Server et les ordinateurs clients joints au domaine géré.

La première tâche consiste à configurer un ordinateur virtuel Windows Server qui est joint au domaine géré. Pour obtenir des instructions, reportez-vous à l’article intitulé [rejoindre une machine virtuelle Windows Server un domaine des Services de domaine Active Directory de Azure gérés](active-directory-ds-admin-guide-join-windows-vm.md).


## <a name="task-2---install-dns-server-tools-on-the-virtual-machine"></a>Tâche 2 - outils de serveur DNS d’installation sur l’ordinateur virtuel
Procédez comme suit pour installer les outils d’Administration DNS sur l’ordinateur virtuel de joint au domaine. Pour plus d’informations sur [l’installation et l’utilisation des outils d’Administration de serveur distant](https://technet.microsoft.com/library/hh831501.aspx), consultez Technet.

1. Accédez au nœud des **ordinateurs virtuels** dans le portail classique Azure. Sélectionnez l’ordinateur virtuel que vous avez créé dans la tâche 1 et cliquez sur **connexion** dans la barre de commandes au bas de la fenêtre.

    ![Se connecter à la machine virtuelle Windows](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)

2. Le portail classique vous invite à ouvrir ou enregistrer un fichier avec une extension « .rdp », qui est utilisée pour se connecter à l’ordinateur virtuel. Lorsqu’il a terminé le téléchargement, cliquez sur le fichier.

3. À l’invite de connexion, utilisez les informations d’identification d’un utilisateur appartenant au groupe « Administrateurs du contrôleur de domaine DAS ». Par exemple, nous utilisons 'bob@domainservicespreview.onmicrosoft.com' dans notre cas.

4. À partir de l’écran de démarrage, ouvrez le **Gestionnaire de serveur**. Cliquez sur **Ajouter des rôles et des fonctionnalités** , dans le volet central de la fenêtre Gestionnaire de serveur.

    ![Lancez le Gestionnaire de serveur sur l’ordinateur virtuel](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager.png)

5. Sur la page **Avant de commencer** l' **Ajout de rôles et de fonctionnalités Assistant**, cliquez sur **suivant**.

    ![Avant de commencer une page](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-begin.png)

6. Dans la page **Type d’Installation** , laissez l’option **d’installation basée sur les rôles ou basé sur la fonctionnalité** sélectionnée et cliquez sur **suivant**.

    ![Page Type d’installation](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-type.png)

7. Dans la page **Sélection de serveur** , sélectionnez l’ordinateur virtuel en cours à partir du pool du serveur, puis cliquez sur **suivant**.

    ![Page de sélection du serveur](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-server.png)

8. Dans la page **Rôles de serveur** , cliquez sur **suivant**. Nous ignorer cette page car nous ne sommes pas installer tous les rôles sur le serveur.

9. Sur la page **des fonctionnalités** , développez le nœud **Outils d’Administration de serveur distant** , puis sur pour développer le nœud **Outils d’Administration de rôles** . Sélectionnez la fonctionnalité **Outils de serveur DNS** à partir de la liste des outils d’administration de rôles.

    ![Page fonctionnalités](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-dns-tools.png)

10. Sur la page de **Confirmation** , cliquez sur **installer** pour installer la fonctionnalité Outils de serveur DNS sur l’ordinateur virtuel. Lors de l’installation de la fonction se termine avec succès, cliquez sur **Fermer** pour quitter l’Assistant **Ajout de rôles et de fonctionnalités** .

    ![Page de confirmation](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-dns-confirmation.png)


## <a name="task-3---launch-the-dns-management-console-to-administer-dns"></a>Tâche 3 : lancement de la console de gestion DNS pour administrer le DNS
Maintenant que la fonctionnalité Outils de serveur DNS est installée sur l’ordinateur virtuel de domaine, nous pouvons utiliser les outils DNS d’administrer le DNS dans le domaine géré.

> [AZURE.NOTE] Vous devez être membre du groupe « Administrateurs de contrôleur de domaine DAS », d’administrer le DNS dans le domaine géré.

1. À partir de l’écran de démarrage, cliquez sur **Outils d’administration**. Vous devez voir la console **DNS** installée sur l’ordinateur virtuel.

    ![Outils d’administration – Console DNS](./media/active-directory-domain-services-admin-guide/install-rsat-dns-tools-installed.png)

2. Cliquez sur **DNS** pour lancer la console de gestion DNS.

3. Dans la boîte de dialogue **connexion au serveur DNS** , cliquez sur l’option intitulée **l’ordinateur suivant**et entrez le nom de domaine DNS du domaine géré (par exemple, ' contoso100.com').

    ![Console DNS - se connecter au domaine](./media/active-directory-domain-services-admin-guide/dns-console-connect-to-domain.png)

4. La Console DNS se connecte au domaine géré.

    ![Console DNS - administrer le domaine](./media/active-directory-domain-services-admin-guide/dns-console-managed-domain.png)

5. Vous pouvez maintenant utiliser la console DNS pour ajouter des entrées DNS pour les ordinateurs au sein du réseau virtuel dans lequel vous avez activé les Services de domaine DAS.

> [AZURE.WARNING] Soyez prudent lors de l’administration de DNS pour le domaine géré à l’aide des outils d’administration DNS. Assurez-vous que vous **ne pas de supprimer ou de modifier les enregistrements DNS intégrées qui sont utilisés par les Services de domaine dans le domaine**. Les enregistrements DNS intégrés incluent les enregistrements DNS du domaine, les enregistrements de serveur de noms et les autres enregistrements utilisés pour l’emplacement du contrôleur de domaine. Si vous modifiez ces enregistrements, les services de domaine sont interrompues sur le réseau virtuel.


Consultez l' [article d’outils DNS sur Technet](https://technet.microsoft.com/library/cc753579.aspx) pour plus d’informations sur la gestion du système DNS.


## <a name="related-content"></a>Contenu associé

- [Services de domaine Active Directory Azure - guide de mise en route](./active-directory-ds-getting-started.md)

- [Joindre un ordinateur virtuel de Windows Server pour un domaine géré de Services de domaine Active Directory de Azure](active-directory-ds-admin-guide-join-windows-vm.md)

- [Administrer un domaine géré de Services de domaine Active Directory de Azure](active-directory-ds-admin-guide-administer-domain.md)

- [Outils d’administration DNS](https://technet.microsoft.com/library/cc753579.aspx)
