<properties
    pageTitle="Des Services de domaine Active Directory Azure : Les paramètres mise à jour DNS pour le réseau virtuel Azure | Microsoft Azure"
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
    ms.date="09/21/2016"
    ms.author="maheshu"/>

# <a name="azure-ad-domain-services---update-dns-settings-for-the-azure-virtual-network"></a>Services de domaine Active Directory Azure - les paramètres de mise à jour DNS pour le réseau virtuel Azure

## <a name="task-4-update-dns-settings-for-the-azure-virtual-network"></a>Tâche 4 : Mettre à jour les paramètres DNS pour le réseau virtuel Azure
Précédent les tâches de configuration, vous avez activé avec succès les Services de domaine Active Directory de Azure pour votre répertoire. La tâche suivante consiste à s’assurer que les ordinateurs sur le réseau virtuel peuvent se connecter et utilisation de ces services. Mettre à jour les paramètres du serveur DNS pour votre réseau virtuel pour pointer vers les deux adresses IP à laquelle les Services de domaine Active Directory Azure est disponible sur le réseau virtuel.

> [AZURE.NOTE] Notez les adresses IP pour les Services de domaine AD de Azure affichées sous l’onglet de **configuration** de votre répertoire, après avoir activé les Services de domaine Active Directory de Azure pour le répertoire.

Effectuez les étapes de configuration suivantes pour le paramètre de serveur DNS pour le réseau virtuel dans lequel vous avez activé les Services de domaine Active Directory de Azure de mettre à jour.

1. Accédez au **portail de classique Azure** ([https://manage.windowsazure.com](https://manage.windowsazure.com)).

2. Sélectionnez le nœud de **réseaux** dans le volet gauche.

    ![Nœud de réseaux virtuels](./media/active-directory-domain-services-getting-started/virtual-network-select.png)

3. Dans l’onglet **Réseaux virtuels** , sélectionnez le réseau virtuel dans lequel vous avez activé les Services de domaine Active Directory de Azure afficher ses propriétés.

4. Cliquez sur l’onglet **configurer** .

    ![Nœud de réseaux virtuels](./media/active-directory-domain-services-getting-started/virtual-network-configure-tab.png)

5. Dans la section **serveurs DNS** , entrez les adresses IP des Services de domaine Active Directory de Azure.

6. Assurez-vous que vous entrez les deux adresses IP qui ont été affichés dans la section **Services de domaine** sous l’onglet de **configuration** de votre répertoire.

7. Pour enregistrer les paramètres du serveur DNS pour ce réseau virtuel, cliquez sur **Enregistrer** dans le volet Office au bas de la page.

   ![Mettre à jour les paramètres du serveur DNS pour le réseau virtuel.](./media/active-directory-domain-services-getting-started/update-dns.png)

> [AZURE.NOTE] Après la mise à jour les paramètres du serveur DNS pour le réseau virtuel, il peut prendre un certain temps pour les machines virtuelles sur le réseau à la configuration DNS mis à jour. Si une machine virtuelle ne peut pas se connecter au domaine, vous pouvez vider le cache DNS (par ex. « ipconfig /flushdns ») sur l’ordinateur virtuel. Cette commande force une actualisation des paramètres DNS sur l’ordinateur virtuel.


## <a name="task-5---enable-password-synchronization-to-azure-ad-domain-services"></a>Tâche 5 - activer la synchronisation mot de passe pour les Services de domaine Active Directory de Azure
La tâche de configuration suivante est pour [Activer la synchronisation de mot de passe pour les Services de domaine Active Directory de Azure](active-directory-ds-getting-started-password-sync.md).
