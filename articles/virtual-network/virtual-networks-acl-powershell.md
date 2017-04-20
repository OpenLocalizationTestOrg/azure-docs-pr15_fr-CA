<properties
   pageTitle="Comment faire pour gérer les listes de contrôle d’accès (ACL) pour les points de terminaison à l’aide de PowerShell"
   description="Découvrez comment gérer les ACL avec PowerShell"
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

# <a name="how-to-manage-access-control-lists-acls-for-endpoints-by-using-powershell"></a>Comment faire pour gérer les listes de contrôle d’accès (ACL) pour les points de terminaison à l’aide de PowerShell

Vous pouvez créer et gérer Network Access Control Lists (ACL) pour les points de terminaison à l’aide de PowerShell d’Azure, ou dans le portail de gestion. Dans cette rubrique, vous trouverez les procédures pour les tâches courantes d’ACL que vous pouvez réaliser à l’aide de PowerShell. Pour une liste des applets de commande PowerShell de Azure consultez [Les Cmdlets de gestion Azure](http://go.microsoft.com/fwlink/?LinkId=317721). Pour plus d’informations sur les ACL, consultez [ce qu’est une liste de contrôle des accès (ACL) réseau ?](virtual-networks-acl.md). Si vous souhaitez gérer vos ACL à l’aide du portail de gestion, consultez [comment définir les points de terminaison à une Machine virtuelle](../virtual-machines/virtual-machines-windows-classic-setup-endpoints.md).

## <a name="manage-network-acls-by-using-azure-powershell"></a>Gérer les listes ACL du réseau à l’aide de PowerShell d’Azure

Vous pouvez utiliser des applets de commande PowerShell de Azure pour créer, supprimer et configurer (jeu) Network Access Control Lists (ACLs). Nous avons inclus quelques exemples de quelques-unes des méthodes que vous pouvez configurer une liste ACL à l’aide de PowerShell.

Pour obtenir une liste complète des applets de commande PowerShell de l’ACL, vous pouvez utiliser une des opérations suivantes :

    Get-Help *AzureACL*
    Get-Command -Noun AzureACLConfig

### <a name="create-a-network-acl-with-rules-that-permit-access-from-a-remote-subnet"></a>Créer une ACL réseau avec des règles qui autorisent l’accès à partir d’un sous-réseau à distance

L’exemple suivant illustre une manière de créer une nouvelle liste ACL qui contient des règles. Cette liste ACL est ensuite appliquée à un point de terminaison de machine virtuelle. Les règles de la liste ACL dans l’exemple ci-dessous autorise l’accès à partir d’un sous-réseau distant. Pour créer une nouvelle liste ACL de réseau avec des règles d’autorisation pour un sous-réseau distant, ouvrez un Azure PowerShell ISE. Copiez et collez le script ci-après, configuration du script par vos propres valeurs et puis exécuter le script.

1. Créer l’objet ACL réseau.

        $acl1 = New-AzureAclConfig

1. Définir une règle qui autorise l’accès à partir d’un sous-réseau distant. Dans l’exemple ci-dessous, vous définissez la règle *100* (qui a la priorité sur la règle de 200 et supérieur) pour permettre l’accès de *10.0.0.0/8* sous-réseau à distance à l’extrémité de la machine virtuelle. Remplacer les valeurs de votre propre configuration requise. Le nom « SharePoint ACL config » doit remplacé par le nom convivial que vous souhaitez appeler cette règle.

        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 100 `
            –Action permit –RemoteSubnet "10.0.0.0/8" `
            –Description "SharePoint ACL config"

1. Pour les règles supplémentaires, répétez l’applet de commande, en remplaçant les valeurs de votre propre configuration requise. N’oubliez pas de modifier la règle de numéro de commande pour tenir compte de l’ordre dans lequel vous souhaitez que les règles à appliquer. Le nombre inférieur de la règle est prioritaire sur un nombre élevé.

        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 200 `
            –Action permit –RemoteSubnet "157.0.0.0/8" `
            –Description "web frontend ACL config"

1. Ensuite, vous pouvez créer un nouveau point de terminaison (Ajouter) ou définir l’ACL d’un point de terminaison existant (jeu). Dans cet exemple, nous ajouter un nouveau point de terminaison ordinateur virtuel nommé « web » et mettre à jour le point de terminaison de machine virtuelle avec les paramètres ACL.

        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Add-AzureEndpoint –Name "web" –Protocol tcp –Localport 80 - PublicPort 80 –ACL $acl1 `
        | Update-AzureVM

1. Ensuite, combinez les applets de commande et exécutez le script. Pour cet exemple, les applets de commande combinée ressemblerait à ceci :

        $acl1 = New-AzureAclConfig
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 100 `
            –Action permit –RemoteSubnet "10.0.0.0/8" `
            –Description "Sharepoint ACL config"
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 200 `
            –Action permit –RemoteSubnet "157.0.0.0/8" `
            –Description "web frontend ACL config"
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        |Add-AzureEndpoint –Name "web" –Protocol tcp –Localport 80 - PublicPort 80 –ACL $acl1 `
        |Update-AzureVM

### <a name="remove-a-network-acl-rule-that-permits-access-from-a-remote-subnet"></a>Supprimer une règle de réseau ACL qui autorise l’accès à partir d’un sous-réseau à distance

L’exemple suivant illustre un moyen de supprimer une règle d’ACL de réseau.  Pour supprimer une règle de réseau ACL avec les règles d’autorisation pour un sous-réseau distant, ouvrez un Azure PowerShell ISE. Copiez et collez le script ci-après, configuration du script par vos propres valeurs et puis exécuter le script.

1. Première étape consiste à obtenir l’objet de l’ACL de réseau pour le point de terminaison de machine virtuelle. Vous allez ensuite supprimer la règle ACL. Dans ce cas, nous supprimons il par ID de règle. Ceci supprimera uniquement l’ID 0 de la règle de la liste ACL. Elle ne supprime pas l’objet de l’ACL à partir de la machine virtuelle.

        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Get-AzureAclConfig –EndpointName "web" `
        | Set-AzureAclConfig –RemoveRule –ID 0 –ACL $acl1

1. Ensuite, vous devez appliquer l’objet de la liste ACL de réseau pour le point de terminaison de machine virtuelle et mettre à jour la machine virtuelle.

        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Set-AzureEndpoint –ACL $acl1 –Name "web" `
        | Update-AzureVM

### <a name="remove-a-network-acl-from-a-virtual-machine-endpoint"></a>Supprimer une ACL du réseau à partir d’un point de terminaison de machine virtuelle

Dans certains scénarios, il pourriez que vous souhaitez supprimer un objet de la liste ACL du réseau à partir d’un point de terminaison de machine virtuelle. Pour ce faire, ouvrez un Azure PowerShell ISE. Copiez et collez le script ci-après, configuration du script par vos propres valeurs et puis exécuter le script.

        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Remove-AzureAclConfig –EndpointName "web" `
        | Update-AzureVM

## <a name="next-steps"></a>Étapes suivantes

[Ce qui est une liste de contrôle d’accès réseau (ACL) ?](virtual-networks-acl.md)
