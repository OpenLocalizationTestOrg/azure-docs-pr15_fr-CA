<properties
    pageTitle="Services de domaine ActiveDirectory Azure : Guide d’Administration | Microsoft Azure"
    description="Créer une unité d’organisation (UO) dans les domaines des Services de domaine Active Directory de Azure gérés"
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
    ms.date="09/21/2016"
    ms.author="maheshu"/>

# <a name="create-an-organizational-unit-ou-on-an-azure-ad-domain-services-managed-domain"></a>Créer une unité d’organisation (OU) dans un domaine géré de Services de domaine Active Directory de Azure
Domaines gérés des Services de domaine Active Directory Azure incluent deux conteneurs prédéfinis appelés respectivement des « Ordinateurs AADDC » et 'AADDC ' utilisateurs. Le conteneur « Ordinateurs AADDC » a des objets ordinateur pour tous les ordinateurs qui sont joints au domaine géré. Le conteneur 'AADDC ' utilisateurs inclut des utilisateurs et des groupes dans le locataire AD Azure. Parfois, il peut être nécessaire de créer des comptes de service sur le domaine géré pour déployer des charges de travail. Pour cela, vous pouvez créer une unité d’organisation personnalisée (OU) dans le domaine géré et créer des comptes de service dans cette unité d’organisation. Cet article vous montre comment créer une unité d’organisation dans votre domaine géré.


## <a name="install-ad-administration-tools-on-a-domain-joined-virtual-machine-for-remote-administration"></a>Installer les outils d’administration Active Directory sur un ordinateur virtuel à un domaine pour l’administration à distance
Azure domaines gérés de Services de domaine Active Directory peuvent être gérés à distance à l’aide des outils d’administration Active Directory familiers tels que Active Directory Administrative Center (ADAC) ou Active Directory PowerShell. Les administrateurs de clients n’ont pas de privilèges pour se connecter aux contrôleurs de domaine dans le domaine géré via Bureau à distance. Pour administrer le domaine géré, installer la fonction Outils d’administration Active Directory sur un ordinateur virtuel associé au domaine géré. À l’article intitulé [administrer un domaine des Services de domaine Active Directory de Azure gérés](active-directory-ds-admin-guide-administer-domain.md) pour obtenir des instructions, reportez-vous à la section.

## <a name="create-an-organizational-unit-on-the-managed-domain"></a>Créer une unité d’organisation dans le domaine géré
Maintenant que les outils d’administration Active Directory sont installés sur l’ordinateur virtuel de domaine, nous pouvons utiliser ces outils pour créer une unité d’organisation dans le domaine géré. Effectuez les opérations suivantes :

> [AZURE.NOTE] Seuls les membres du groupe « Administrateurs de contrôleur de domaine DAS » ont les privilèges requis pour créer une unité d’organisation personnalisée. Assurez-vous que vous effectuez les opérations suivantes en tant qu’utilisateur appartenant à ce groupe.

1. À partir de l’écran de démarrage, cliquez sur **Outils d’administration**. Vous devez voir les outils d’administration Active Directory installés sur l’ordinateur virtuel.

    ![Outils d’administration installées sur le serveur](./media/active-directory-domain-services-admin-guide/install-rsat-admin-tools-installed.png)

2. Cliquez sur **Centre d’administration Active Directory**.

    ![Centre d’administration Active Directory](./media/active-directory-domain-services-admin-guide/adac-overview.png)

3. Pour afficher le domaine, cliquez sur le nom de domaine dans le volet gauche (par exemple, ' contoso100.com').

    ![ADAC - domaine d’affichage](./media/active-directory-domain-services-admin-guide/create-ou-adac-overview.png)

4. Dans le volet de **tâches** de droite, cliquez sur **Nouveau** sous le nœud du nom de domaine. Dans cet exemple, nous allons cliquer sur **Nouveau** sous le noeud 'contoso100(local)' dans le volet de **tâches** de droite.

    ![ADAC - nouvelle unité d’organisation](./media/active-directory-domain-services-admin-guide/create-ou-adac-new-ou.png)

5. Vous devez voir l’option pour créer une unité d’organisation. Cliquez sur l' **Unité d’organisation** pour ouvrir la boîte de dialogue **Créer une unité d’organisation** .

6. Dans la boîte de dialogue **Créer une unité d’organisation** , spécifiez un **nom** pour la nouvelle unité d’organisation. Fournir une brève description de l’unité d’organisation. Vous pouvez également définir le champ **Géré par** pour l’unité d’organisation. Pour créer l’unité d’organisation personnalisée, cliquez sur **OK**.

    ![ADAC - créer la boîte de dialogue unité d’organisation](./media/active-directory-domain-services-admin-guide/create-ou-dialog.png)

7. L’unité d’organisation créée doit maintenant apparaître dans le centre d’administration Active Directory (ADAC).

    ![ADAC - unité d’organisation créée](./media/active-directory-domain-services-admin-guide/create-ou-done.png)


## <a name="permissionssecurity-for-newly-created-ous"></a>Permissions et de sécurité pour les unités d’organisation qui vient d’être créées.
Par défaut, l’utilisateur qui a créé l’unité d’organisation personnalisée (membre du groupe « Administrateurs de contrôleur de domaine DAS ») bénéficie des privilèges d’administration (contrôle total) sur l’unité d’organisation. L’utilisateur peut ensuite continuer et accorder des privilèges à d’autres utilisateurs ou au groupe « Administrateurs de contrôleur de domaine DAS » comme vous le souhaitez. Comme dans la capture d’écran suivante, l’utilisateur 'bob@domainservicespreview.onmicrosoft.com' qui a créé la nouvelle unité d’organisation « MyCustomOU » est accordé un contrôle total sur lui.

 ![ADAC - nouvelle sécurité d’unité d’organisation](./media/active-directory-domain-services-admin-guide/create-ou-permissions.png)


## <a name="notes-on-administering-custom-ous"></a>Notes sur l’administration des unités d’organisation personnalisées
Maintenant que vous avez créé une unité d’organisation personnalisée, vous pouvez continuer et créer des utilisateurs, des groupes, des ordinateurs et des comptes de service dans cette unité d’organisation. Impossible de déplacer les utilisateurs ou les groupes à partir de la « DAS DC' unité d’organisation utilisateurs aux unités d’organisation personnalisées.

> [AZURE.WARNING] Comptes d’utilisateurs, des groupes, des comptes de service et des objets d’ordinateur que vous créez sous les unités d’organisation personnalisées ne sont pas disponibles dans votre client AD Azure. En d’autres termes, ces objets n’apparaissent pas à l’aide de l’API Azure AD graphique ou dans l’interface utilisateur Active Directory de Azure. Ces objets ne sont plus disponibles dans votre domaine géré les Services de domaine Active Directory de Azure.


## <a name="related-content"></a>Contenu associé

- [Administrer un domaine géré de Services de domaine Active Directory de Azure](active-directory-ds-admin-guide-administer-domain.md)

- [Centre d’administration Active Directory : Démarrage](https://technet.microsoft.com/library/dd560651.aspx)

- [Guide pas à pas des comptes de service](https://technet.microsoft.com/library/dd548356.aspx)
