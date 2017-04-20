<properties 
    pageTitle="Configurer un réseau virtuel à l’aide d’un fichier de configuration de réseau" 
    description="Instructions pour exporter et importer un fichier de configuration de réseau pour le portail de gestion Azure pour créer ou modifier des réseaux virtuels. " 
    services="virtual-network" 
    documentationCenter="" 
    authors="jimdial" 
    manager="carmonm" 
    editor="tysonn"/>

<tags
    ms.service="virtual-network"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services" 
    ms.date="03/15/2016"
    ms.author="jdial"/>

# <a name="configure-a-virtual-network-using-a-network-configuration-file"></a>Configurer un réseau virtuel à l’aide d’un fichier de configuration de réseau

Vous pouvez configurer un réseau virtuel (VNet) à l’aide du portail de gestion d’Azure, ou à l’aide d’un fichier de configuration réseau.

## <a name="creating-and-modifying-a-network-configuration-file"></a>Création et modification d’un fichier de configuration de réseau 
La façon la plus simple pour créer un fichier de configuration du réseau est d’exporter les paramètres réseau à partir d’une configuration de réseau virtuel existante, puis modifiez le fichier pour contenir les paramètres que vous souhaitez configurer vos réseaux virtuels.

Pour modifier le fichier de configuration de réseau, vous pouvez simplement ouvrir le fichier, apportez les modifications appropriées, puis enregistrez le fichier. Vous pouvez utiliser n’importe quel éditeur *xml* pour modifier le fichier de configuration réseau. 

Vous devez suivre de près les recommandations pour les [paramètres de schéma de fichier de configuration de réseau](https://msdn.microsoft.com/library/azure/jj157100.aspx). 

Azure considère un sous-réseau possédant chose déployé comme **en cours d’utilisation**. Lorsqu’un sous-réseau n’est en cours d’utilisation, elle ne peut pas être modifiée. Avant de le modifier, déplacez tout ce que vous avez déployé sur le sous-réseau pour un sous-réseau différent qui n’est pas en cours de modification.   Voir [déplacer une machine virtuelle ou une Instance de rôle à un autre sous-réseau](virtual-networks-move-vm-role-to-subnet.md).

## <a name="export-and-import-virtual-network-settings-using-the-management-portal"></a>Exporter et importer les paramètres de réseau virtuel via le portail de gestion  
Vous pouvez importer et exporter des paramètres de configuration réseau contenues dans le fichier de configuration de votre réseau à l’aide de PowerShell ou le portail de gestion. Les instructions ci-dessous vous aidera à exporter et importer à l’aide du portail de gestion. 

### <a name="to-export-your-network-settings"></a>Pour exporter vos paramètres réseau
Lorsque vous exportez, tous les paramètres pour les réseaux virtuels dans votre abonnement seront écrit dans un fichier .xml. 

1. Connectez-vous au **portail de gestion**.
2. Dans le portail de gestion, au bas de la page **réseaux** , cliquez sur **Exporter**. 
3. Dans la fenêtre **d’exportation de la configuration du réseau** , vérifiez que vous avez sélectionné l’abonnement pour lequel vous souhaitez exporter vos paramètres réseau. Puis, cliquez sur la coche dans le coin inférieur droit. 
4. Lorsque vous y êtes invité, enregistrez le fichier *NetworkConfig.xml* à l’emplacement de votre choix.


### <a name="to-import-your-network-settings"></a>Pour importer vos paramètres réseau

1. Dans le **Portail de gestion**, dans le volet de navigation en bas à gauche, cliquez sur **Nouveau**.
2. Cliquez sur **Services réseau** -> **réseau virtuel** -> **Configuration d’importation**.
3. Sur la page **Importer le fichier de configuration du réseau** , recherchez votre fichier de configuration de réseau, puis cliquez sur la flèche **suivant** .
4. Sur la page de **Création de votre réseau** , vous verrez des informations à l’écran affichant les sections de configuration de votre réseau seront modifiées ou créées. Si les modifications vous conviennent pour vous, cliquez sur la coche pour continuer à mettre à jour ou de créer votre réseau virtuel. 