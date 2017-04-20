<properties 
   pageTitle="Gérer les serveurs DNS utilisés par un réseau virtuel (VNet)"
   description="Découvrez comment ajouter et supprimer des serveurs DNS dans un réseau virtuel (vnet)"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/15/2016"
   ms.author="jdial" />

# <a name="manage-dns-servers-used-by-a-virtual-network-vnet"></a>Gérer les serveurs DNS utilisés par un réseau virtuel (VNet)

Vous pouvez gérer la liste des serveurs DNS utilisés dans un VNet dans le portail de gestion, ou dans le fichier de configuration de réseau. Vous pouvez ajouter jusqu'à 12 serveurs DNS pour chaque VNet. Lorsque vous spécifiez des serveurs DNS, il est important de vérifier que vous répertoriez vos serveurs DNS dans l’ordre approprié pour votre environnement. Listes de serveurs DNS ne fonctionnent pas cyclique. Ils sont utilisés dans l’ordre où elles sont spécifiées. Si le premier serveur DNS sur la liste ne peut être atteint, le client utilise ce serveur DNS que si le serveur DNS fonctionne correctement ou non. Pour modifier l’ordre du serveur DNS pour votre réseau virtuel, supprimer les serveurs DNS de la liste et les ajouter dans l’ordre que vous souhaitez.

>[AZURE.WARNING] Une fois la liste DNS a été mis à jour, vous devez redémarrer les ordinateurs virtuels que qui se trouve dans votre réseau virtuel afin qu’ils chercher les nouveaux paramètres de serveur DNS. Ordinateurs virtuels continueront à utiliser leur configuration actuelle avant leur redémarrage.

## <a name="edit-a-dns-server-list-for-a-virtual-network-using-the-management-portal"></a>Modifier une liste de serveurs DNS pour un réseau virtuel via le portail de gestion

1. Ouvrez une session sur le **portail de gestion**.

1. Dans le volet de navigation, cliquez sur **réseaux**, puis cliquez sur le nom du réseau virtuel dans la colonne **nom** .

1. Cliquez sur **configurer**.

1. Dans les **Serveurs DNS**, vous pouvez configurer les éléments suivants :

    - **Enregistrer le serveur (Ajouter) un nouveau serveur DNS :** Dans les zones, tapez simplement le nom et l’adresse IP. Un serveur DNS est ajouté à votre liste de serveurs DNS du réseau virtuel et enregistre également le serveur DNS avec Azure.

    - **Pour ajouter un serveur DNS qui a été précédemment inscrit :** Si vous déjà inscrit un serveur DNS avec Azure, vous pouvez le sélectionner dans la liste prédéfinie.

    - **Pour supprimer un serveur DNS de votre réseau virtuel :** Cliquez sur le X en regard du serveur que vous souhaitez supprimer. Notez que cela ne supprime que le serveur à partir de cette liste de réseau virtuel. Le serveur DNS reste inscrit dans Azure pour vos autres réseaux virtuels à utiliser. Pour supprimer un serveur DNS de votre abonnement, accédez à la page **réseaux -> serveurs DNS** .

    - **Pour réorganiser les serveurs DNS :** Supprimer tous les serveurs DNS qui sont répertoriés, puis les ajouter de nouveau dans l’ordre que vous souhaitez. N’oubliez pas qu’il ne s’agit pas d’une liste de répétition alternée DNS.

    - **Pour renommer un serveur DNS :** Sélectionnez le serveur DNS dans la liste, puis tapez le nouveau nom. Cela va enregistrer un nouveau serveur DNS dans Azure, ainsi que pour l’ajouter à la liste de serveurs DNS pour votre réseau virtuel. L’ancien serveur DNS et l’adresse IP reste inscrits avec Azure. Vous pouvez le supprimer dans la page **Serveurs DNS** si vous ne l’utilisez pas pour les autres réseaux virtuels.

1. Cliquez sur **Enregistrer** en bas de la page pour enregistrer votre nouvelle configuration de serveurs DNS.

1. Redémarrez les ordinateurs virtuels situés dans le réseau virtuel pour leur permettre d’acquérir les nouveaux paramètres de DNS.

## <a name="edit-a-dns-server-list-using-a-network-configuration-file"></a>Modifier une liste de serveurs DNS à l’aide d’un fichier de configuration de réseau

Pour modifier une liste de serveurs DNS à l’aide d’un fichier de configuration de réseau, vous devez d’abord exporter vos paramètres de configuration à partir du portail de gestion. Vous allez ensuite modifier le fichier de configuration réseau et l’importer via le portail de gestion. Voici une liste de haut niveau des étapes de ce processus.

1. Exporter vos paramètres de réseau virtuel à un fichier de configuration réseau. Pour plus d’informations et des étapes à suivre pour exporter des paramètres de configuration de votre réseau, reportez-vous à la section [Exporter les paramètres du réseau virtuel vers un fichier de Configuration réseau](virtual-networks-using-network-configuration-file.md).

1. Spécifiez les informations de serveur DNS pour votre réseau virtuel. Pour plus d’informations sur la spécification d’un serveur DNS, consultez [spécification d’un serveur DNS dans un fichier de Configuration de réseau virtuel](virtual-networks-specifying-a-dns-settings-in-a-virtual-network-configuration-file.md). Pour plus d’informations sur les fichiers de configuration réseau, consultez le [Schéma de Configuration de réseau virtuel Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx) et la [configuration d’un réseau virtuel à l’aide d’un fichier de Configuration de réseau](virtual-networks-using-network-configuration-file.md).

1. Importez le fichier de configuration réseau. Pour plus d’informations et des étapes pour importer votre fichier de configuration de réseau, reportez-vous à la section [importation d’un fichier de Configuration de réseau](virtual-networks-using-network-configuration-file.md).

1. Redémarrez les ordinateurs virtuels situés dans le réseau virtuel pour leur permettre d’acquérir les nouveaux paramètres de DNS.
