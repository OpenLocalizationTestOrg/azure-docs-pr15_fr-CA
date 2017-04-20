<properties
    pageTitle="Services de domaine d’Active Directory Azure : Activer Azure domaine d’Active Directory Services | Microsoft Azure"
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
    ms.date="10/19/2016"
    ms.author="maheshu"/>

# <a name="enable-azure-ad-domain-services"></a>Activer les Services de domaine d’Active Directory Azure

## <a name="task-3-enable-azure-ad-domain-services"></a>Tâche 3 : Activer les Services de domaine d’Active Directory Azure
Dans cette tâche, vous activez les Services de domaine Active Directory de Azure pour votre répertoire. Effectuez les étapes de configuration suivantes pour activer les Services de domaine Active Directory de Azure pour votre répertoire.

1. Accédez au **portail de classique Azure** ([https://manage.windowsazure.com](https://manage.windowsazure.com)).

2. Sélectionnez le nœud **Active Directory** dans le volet gauche.

3. Sélectionnez le locataire Azure d’Active Directory (répertoire) pour lequel vous souhaitez activer les Services de domaine Active Directory de Azure.

    ![Sélectionnez répertoire de publicité Azure](./media/active-directory-domain-services-getting-started/select-aad-directory.png)

4. Cliquez sur l’onglet **configurer** .

    ![Configurer l’onglet du répertoire](./media/active-directory-domain-services-getting-started/configure-tab.png)

5. Faites défiler jusqu'à la section intitulée des **services de domaine**.

    ![Section de configuration des Services de domaine](./media/active-directory-domain-services-getting-started/domain-services-configuration.png)

6. Activer/désactiver l’option intitulée **Activer les services de domaine de ce répertoire** sur **Oui**. Vous remarquerez quelques options de configuration supplémentaires pour les services de domaine Active Directory de Azure apparaissent sur la page.

    ![Activer les Services de domaine](./media/active-directory-domain-services-getting-started/enable-domain-services.png)

    > [AZURE.NOTE] Lorsque vous activez les Services de domaine Active Directory de Azure pour vos clients, AD Azure génère et stocke les hachages d’informations d’identification Kerberos et NTLM qui sont requis pour l’authentification des utilisateurs.

7. Spécifiez le **nom de domaine DNS des services de domaine**.

   - Le nom de domaine par défaut du répertoire (c'est-à-dire, se terminant par le **. onmicrosoft.com** suffixe de domaine) est sélectionnée par défaut.

   - La liste contient tous les domaines qui ont été configurés pour votre annuaire AD Azure – y compris vérifiée ainsi que des domaines non vérifiés que vous configurez dans l’onglet 'Domaines'.

   - En outre, vous pouvez également taper un nom de domaine personnalisé. Dans cet exemple, nous avons tapé un nom de domaine personnalisé 'contoso100.com'.

     > [AZURE.WARNING] Assurez-vous que le préfixe de domaine du nom du domaine que vous spécifiez (par exemple, 'contoso100' dans le nom de domaine 'contoso100.com') est de moins de 15 caractères. Vous ne pouvez pas créer un domaine des Services de domaine Active Directory de Azure avec un préfixe de domaine plu de 15 caractères.

8. Vérifiez que le nom de domaine DNS que vous avez choisi pour le domaine géré n’existe pas déjà dans le réseau virtuel. En particulier, vérifiez si :

   - vous disposez déjà d’un domaine avec le même nom de domaine DNS sur le réseau virtuel.

   - le réseau virtuel que vous avez sélectionné possède une connexion VPN avec votre réseau local et que vous avez un domaine avec le même nom de domaine DNS de votre réseau local.

   - vous avez un service cloud existant portant le même nom sur le réseau virtuel.

9. L’étape suivante consiste à sélectionner un réseau virtuel dans lequel vous souhaitez que les Services de domaine Active Directory de Azure soit disponible. Sélectionnez le réseau virtuel et le sous-réseau dédié, que vous avez créé dans la liste déroulante intitulée des **services de domaine de se connecter à ce réseau virtuel**.

   - Assurez-vous que le réseau virtuel que vous avez indiqué appartient à une région Azure pris en charge par les Services de domaine Active Directory de Azure. Reportez-vous à la page [des services Azure par région](https://azure.microsoft.com/regions/#services/) pour connaître les régions Azure dans lequel les Services de domaine Active Directory Azure est disponible.

   - Appartenant à une zone où les Services de domaine Active Directory de Azure n’est pas prise en charge des réseaux virtuels n’apparaissent pas dans la liste déroulante.
   
   - Utiliser un sous-réseau dédié au sein du réseau virtuel pour les Services de domaine Active Directory de Azure. Assurez-vous que vous ne sélectionnez pas le sous-réseau de passerelle. Reportez-vous à la section [Considérations relatives à la mise en réseau](active-directory-ds-networking.md). 

   - De même, les réseaux virtuels qui ont été créés à l’aide du Gestionnaire de ressources Azure n’apparaissent pas dans la liste déroulante. Les réseaux virtuels basés sur un gestionnaire de ressources ne sont pas actuellement pris en charge par les Services de domaine Active Directory de Azure.

10. Pour activer les Services de domaine Active Directory de Azure, cliquez sur **Enregistrer** dans le volet Office au bas de la page.

11. La page affiche un ' en cours... » état, tandis que les Services de domaine Active Directory Azure est activée pour le répertoire.

    ![Activer les Services de domaine - état d’attente](./media/active-directory-domain-services-getting-started/enable-domain-services-pendingstate.png)

    > [AZURE.NOTE] Les Services de domaine Active Directory Azure fournit une haute disponibilité pour votre domaine géré. Une fois que les Services de domaine Active Directory de Azure, notez les adresses IP des Services de domaine sont disponibles sur le réseau virtuel s’affichent un par un. La deuxième adresse IP s’affiche peu de temps, comme dès le service permet une haute disponibilité pour votre domaine. Lors de la haute disponibilité est configuré et actif pour votre domaine, vous devez voir deux adresses IP dans la section **services du domaine** de l’onglet **configurer** .

12. Après environ 20 à 30 minutes, vous voyez la première adresse IP qui est disponible sur votre réseau virtuel dans le champ **d’adresse IP** sur la page **configurer** les Services de domaine.

    ![Mise en service du premier IP activés - des Services de domaine](./media/active-directory-domain-services-getting-started/domain-services-enabled-firstdc-available.png)

13. La haute disponibilité est opérationnelle pour votre domaine, vous voyez deux adresses IP sont affichées sur la page. Votre domaine géré est disponible sur votre réseau virtuel sélectionné à ces deux adresses IP. Notez les adresses IP vous pouvez mettre à jour les paramètres DNS pour votre réseau virtuel. Cette étape permet de machines virtuelles sur le réseau virtuel pour vous connecter au domaine pour les opérations telles que la jonction de domaine.

    ![Les Services de domaine activé - les deux adresses IP mis en service](./media/active-directory-domain-services-getting-started/domain-services-enabled-bothdcs-available.png)

> [AZURE.NOTE] Selon la taille de vos clients AD Azure (nombre d’utilisateurs, des groupes etc.), votre domaine géré la synchronisation prend un certain temps. Ce processus de synchronisation se produit en arrière-plan. Pour les locataires volumineux avec des dizaines de milliers d’objets, il peut prendre un jour ou deux pour tous les utilisateurs, les appartenances de groupe et les informations d’identification à synchroniser.

<br>

## <a name="task-4---update-dns-settings-for-the-azure-virtual-network"></a>Tâche 4 - mise à jour des paramètres de DNS pour le réseau virtuel Azure
La tâche de configuration suivante est à [jour les paramètres DNS pour le réseau virtuel Azure](active-directory-ds-getting-started-dns.md).
