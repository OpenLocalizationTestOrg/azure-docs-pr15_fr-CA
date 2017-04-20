<properties
    pageTitle="Des Services de domaine Active Directory Azure : Création du groupe d’administrateurs du contrôleur de domaine DAS | Microsoft Azure"
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
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="maheshu"/>

# <a name="get-started-with-azure-ad-domain-services"></a>Mise en route avec les Services de domaine Active Directory de Azure

Cet article décrit les tâches de configuration requises pour activer les Services de domaine Active Directory de Azure pour vos clients AD Azure.

## <a name="task-1-create-the-aad-dc-administrators-group"></a>Tâche 1 : Créer le groupe « Administrateurs de contrôleur de domaine DAS »
La première tâche consiste à créer un groupe d’administration dans votre client Azure Active Directory. Ce groupe d’administration particulier est appelé **DAS DC administrateurs**. Les membres de ce groupe sont accordés des privilèges d’administrateur sur les ordinateurs qui sont associés à un domaine pour le domaine géré les Services de domaine Active Directory de Azure. Sur les ordinateurs à un domaine, ce groupe est ajouté au groupe « Administrateurs ». En outre, les membres de ce groupe peuvent utiliser Bureau à distance pour se connecter à distance à un domaine de machines.  

> [AZURE.NOTE] Vous n’avez pas de privilèges d’administrateur de domaine ou administrateur d’entreprise sur le domaine géré créé à l’aide des Services de domaine Active Directory de Azure. Sur les domaines gérés, ces privilèges sont réservés par le service et ne sont pas rendues accessibles aux utilisateurs dans le locataire. Toutefois, vous pouvez utiliser le groupe administrateur spécial créé dans cette tâche de configuration à effectuer des opérations privilégiées. Ces opérations comprennent la joindre des ordinateurs au domaine, appartenant au groupe des administrateurs sur les ordinateurs à un domaine, etc. de stratégie de groupe de configuration.

Dans cette tâche de configuration, vous créez le groupe d’administration et ajoutez un ou plusieurs utilisateurs dans votre répertoire pour le groupe. Effectuez les étapes suivantes pour créer le groupe d’administration pour les Services de domaine Active Directory de Azure :

1. Accédez au **portail de classique Azure** ([https://manage.windowsazure.com](https://manage.windowsazure.com))

2. Sélectionnez le nœud **Active Directory** dans le volet gauche.

3. Sélectionnez le locataire Azure d’Active Directory (répertoire) pour lequel vous souhaitez activer les Services de domaine Active Directory de Azure. Vous ne pouvez créer un domaine pour chaque répertoire AD Azure.

    ![Sélectionnez répertoire de publicité Azure](./media/active-directory-domain-services-getting-started/select-aad-directory.png)

4. Cliquez sur l’onglet **groupes** .

5. Pour ajouter un groupe à vos clients AD Azure, cliquez sur **Ajouter un groupe** à partir du volet au bas de la page.

    ![Ajouter le bouton du groupe](./media/active-directory-domain-services-getting-started/add-group-button.png)

6. Créez un groupe nommé **DAS DC administrateurs**. Définir le **TYPE de groupe de** **sécurité**.

    > [AZURE.WARNING] Pour permettre l’accès dans les Services de domaine Active Directory de Azure gérés domaine, créez un groupe portant ce nom exact.

    ![Créer le groupe de l’administrateur](./media/active-directory-domain-services-getting-started/create-admin-group.png)

7. Ajoutez une description de ce groupe afin que d’autres conscient que ce groupe est utilisé pour accorder des privilèges d’administrateur dans les Services de domaine Active Directory de Azure.

8. Une fois que le groupe a été créé, cliquez sur le nom du groupe pour afficher les propriétés de ce groupe. Pour ajouter des utilisateurs en tant que membres de ce groupe, cliquez sur le bouton **Ajouter des membres** dans le volet inférieur.

    ![Ajouter le bouton des membres de groupe](./media/active-directory-domain-services-getting-started/add-group-members-button.png)

9. Dans la boîte de dialogue **Ajouter des membres** , sélectionnez les utilisateurs qui doivent être membres de ce groupe et activez la case à cocher lorsque vous avez terminé.

    ![Ajouter des utilisateurs au groupe Administrateurs](./media/active-directory-domain-services-getting-started/add-group-members.png)

<br>

## <a name="task-2-create-or-select-an-azure-virtual-network"></a>Tâche 2 : Créer ou sélectionner un réseau virtuel Azure
La tâche suivante de la configuration consiste à [créer ou sélectionner un réseau virtuel Azure](active-directory-ds-getting-started-vnet.md).
