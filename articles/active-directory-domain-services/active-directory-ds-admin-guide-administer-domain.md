<properties
    pageTitle="Azure Active Directory Domain Services : Administrer un domaine géré | Microsoft Azure"
    description="Administrer les domaines gérés Azure des Services de domaine Active Directory"
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

# <a name="administer-an-azure-active-directory-domain-services-managed-domain"></a>Administrer un domaine géré Azure des Services de domaine Active Directory
Cet article vous explique comment administrer un domaine géré Azure Active Directory (AD) les Services de domaine.


## <a name="before-you-begin"></a>Avant de commencer
Pour effectuer les tâches répertoriées dans cet article, vous devez :

1. Un **abonnement Azure**valide.

2. Un **répertoire d’Azure AD** - soit synchronisé avec un répertoire local ou un répertoire de nuage uniquement.

3. **Les Services de domaine Active Directory de Azure** doit être activée pour le répertoire Active Directory Azure. Si vous n’avez pas fait, suivez toutes les tâches décrites dans le [guide Mise en route](./active-directory-ds-getting-started.md).

4. **À un domaine un ordinateur virtuel** à partir duquel vous administrez le domaine géré les Services de domaine Active Directory de Azure. Si vous n’avez pas une telle machine virtuelle, suivez toutes les tâches décrites dans l’article intitulé [rejoindre une machine virtuelle Windows un domaine géré](./active-directory-ds-admin-guide-join-windows-vm.md).

5. Vous devez les informations d’identification d’un **compte d’utilisateur appartenant au groupe « Administrateurs du contrôleur de domaine DAS »** dans le répertoire, pour administrer votre domaine géré.

<br>


## <a name="administrative-tasks-you-can-perform-on-a-managed-domain"></a>Tâches administratives que vous pouvez effectuer sur un domaine géré
Les membres du groupe « Administrateurs de contrôleur de domaine DAS » sont des privilèges sur le domaine géré qui leur permettent d’effectuer des tâches telles que :

- Joindre des ordinateurs au domaine géré.

- Configurer l’objet GPO intégré pour les conteneurs 'Ordinateurs AADDC' et 'AADDC ' utilisateurs dans le domaine géré.

- L’administration DNS dans le domaine géré.

- Créer et gérer des unités d’organisation (UO) personnalisées sur le domaine géré.

- Avoir un accès d’administration sur les ordinateurs joints au domaine géré.


## <a name="administrative-privileges-you-do-not-have-on-a-managed-domain"></a>Privilèges d’administrateur, que vous ne disposez pas sur un domaine géré
Le domaine est géré par Microsoft, y compris les activités de correctifs, de surveillance et des sauvegardes. Par conséquent, le domaine est verrouillé, et vous n’avez pas de privilèges pour effectuer certaines tâches d’administration sur le domaine. Ci-dessous sont des exemples de tâches que vous ne pouvez pas effectuer.

- Des privilèges d’administrateur de domaine ou administrateur d’entreprise pour le domaine géré n’est pas accordé.

- Vous ne pouvez pas étendre le schéma du domaine géré.

- Impossible de se connecter aux contrôleurs de domaine pour le domaine géré à l’aide du Bureau à distance.

- Impossible d’ajouter des contrôleurs de domaine pour le domaine géré.


## <a name="task-1---provision-a-domain-joined-windows-server-virtual-machine-to-remotely-administer-the-managed-domain"></a>Tâche 1 : disposition un ordinateur à un domaine de virtuel Windows Server pour administrer à distance le domaine géré
Azure domaines gérés de Services de domaine Active Directory peuvent être gérés à l’aide des outils d’administration Active Directory familiers tels que Active Directory Administrative Center (ADAC) ou Active Directory PowerShell. Les administrateurs de clients n’ont pas de privilèges pour se connecter aux contrôleurs de domaine dans le domaine géré via Bureau à distance. Par conséquent, les membres du groupe « Administrateurs de contrôleur de domaine DAS » peuvent administrer des domaines gérés à distance à l’aide des outils d’administration Active Directory à partir d’un ordinateur client Windows Server qui est joint au domaine géré. Outils d’administration Active Directory peuvent être installés dans le cadre de la fonctionnalité en option Outils d’Administration de serveur distant (RSAT) sur Windows Server et les ordinateurs clients joints au domaine géré.

La première étape consiste à configurer un ordinateur virtuel Windows Server qui est joint au domaine géré. Pour obtenir des instructions, reportez-vous à l’article intitulé [rejoindre une machine virtuelle Windows Server un domaine des Services de domaine Active Directory de Azure gérés](active-directory-ds-admin-guide-join-windows-vm.md).

### <a name="remotely-administer-the-managed-domain-from-a-client-computer-for-example-windows-10"></a>Administrer à distance le domaine géré à partir d’un ordinateur client (par exemple, Windows 10)
Les instructions de cet article utilisent un ordinateur virtuel de Windows Server pour administrer le DAS-DS gérés de domaine. Toutefois, vous pouvez également choisir d’utiliser une machine virtuelle du client (par exemple, Windows 10) Windows au pour faire.

Vous pouvez [installer les outils d’Administration de serveur distant (RSAT)](http://social.technet.microsoft.com/wiki/contents/articles/2202.remote-server-administration-tools-rsat-for-windows-client-and-windows-server-dsforum2wiki.aspx) sur une machine virtuelle du client Windows en suivant les instructions sur le site TechNet.


## <a name="task-2---install-active-directory-administration-tools-on-the-virtual-machine"></a>Tâche 2 - Outils d’administration installer Active Directory sur l’ordinateur virtuel
Procédez comme suit pour installer les outils d’Administration Active Directory sur l’ordinateur virtuel de joint au domaine. Pour plus d' [informations sur l’installation et l’utilisation des outils d’Administration de serveur distant](https://technet.microsoft.com/library/hh831501.aspx), consultez Technet.

1. Accédez au nœud des **ordinateurs virtuels** dans le portail classique Azure. Sélectionnez l’ordinateur virtuel que vous avez créé dans la tâche 1 et cliquez sur **connexion** dans la barre de commandes au bas de la fenêtre.

    ![Se connecter à la machine virtuelle Windows](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)

2. Le portail classique vous invite à ouvrir ou enregistrer un fichier avec une extension « .rdp », qui est utilisée pour se connecter à l’ordinateur virtuel. Cliquez pour ouvrir le fichier lorsqu’il a terminé le téléchargement.

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

9. Sur la page **des fonctionnalités** , développez le nœud **Outils d’Administration de serveur distant** , puis sur pour développer le nœud **Outils d’Administration de rôles** . Sélectionnez la fonctionnalité **AD DS et AD LDS outils** à partir de la liste des outils d’administration de rôles.

    ![Page fonctionnalités](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-ad-tools.png)

10. Sur la page de **Confirmation** , cliquez sur **installer** pour installer Active Directory et de la fonctionnalité d’outils AD LDS sur l’ordinateur virtuel. Lors de l’installation de la fonction se termine avec succès, cliquez sur **Fermer** pour quitter l’Assistant **Ajout de rôles et de fonctionnalités** .

    ![Page de confirmation](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-confirmation.png)


## <a name="task-3---connect-to-and-explore-the-managed-domain"></a>Tâche 3 : se connecter à et explorez le domaine géré
Maintenant que les outils d’administration Active Directory sont installés sur l’ordinateur virtuel de domaine, nous pouvons utiliser ces outils pour Explorer et administrer le domaine géré.

> [AZURE.NOTE] Vous devez être membre du groupe « Administrateurs de contrôleur de domaine DAS », pour administrer le domaine géré.

1. À partir de l’écran de démarrage, cliquez sur **Outils d’administration**. Vous devez voir les outils d’administration Active Directory installés sur l’ordinateur virtuel.

    ![Outils d’administration installées sur le serveur](./media/active-directory-domain-services-admin-guide/install-rsat-admin-tools-installed.png)

2. Cliquez sur **Centre d’administration Active Directory**.

    ![Centre d’administration Active Directory](./media/active-directory-domain-services-admin-guide/adac-overview.png)

3. Pour Explorer le domaine, cliquez sur le nom de domaine dans le volet gauche (par exemple, ' contoso100.com'). Notez les deux conteneurs sont appelés des « Ordinateurs AADDC » et 'AADDC ' utilisateurs respectivement.

    ![ADAC - domaine d’affichage](./media/active-directory-domain-services-admin-guide/adac-domain-view.png)

4. Cliquez sur le conteneur appelé **AADDC utilisateurs** pour afficher tous les utilisateurs et les groupes qui appartiennent au domaine géré. Vous devriez voir comptes d’utilisateurs et de groupes à partir de votre annonce Azure client afficher dans ce conteneur. Notez que dans cet exemple, un compte d’utilisateur pour l’utilisateur appelé « bob » et le groupe « Administrateurs du contrôleur de domaine DAS » sont disponibles dans ce conteneur.

    ![ADAC - les utilisateurs de domaine](./media/active-directory-domain-services-admin-guide/adac-aaddc-users.png)

5. Cliquez sur le conteneur appelé **AADDC ordinateurs** pour afficher les ordinateurs joints à ce domaine géré. Vous devriez voir une entrée pour la machine virtuelle courante, qui est joint au domaine. Comptes d’ordinateur pour tous les ordinateurs qui sont joints au domaine des Services de domaine Active Directory de Azure géré sont stockées dans ce conteneur 'les ordinateurs AADDC'.

    ![ADAC - ordinateurs de domaine](./media/active-directory-domain-services-admin-guide/adac-aaddc-computers.png)

<br>

## <a name="related-content"></a>Contenu associé

- [Services de domaine Active Directory Azure - guide de mise en route](./active-directory-ds-getting-started.md)

- [Joindre un ordinateur virtuel de Windows Server pour un domaine géré de Services de domaine Active Directory de Azure](active-directory-ds-admin-guide-join-windows-vm.md)

- [Déployer les outils d’Administration de serveur distant](https://technet.microsoft.com/library/hh831501.aspx)
