<properties
    pageTitle="Impossible de RDP à une machine virtuelle Azure | Microsoft Azure"
    description="Résoudre les problèmes lorsque vous ne pouvez pas vous connecter à votre machine virtuelle de Windows dans Azure à l’aide du Bureau à distance"
    keywords="Erreur du Bureau à distance, erreur de connexion Bureau à distance, ne peut pas se connecter à la machine virtuelle, dépannage de bureau à distance"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="top-support-issue,azure-service-management,azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="support-article"
    ms.date="10/26/2016"
    ms.author="iainfou"/>

# <a name="troubleshoot-remote-desktop-connections-to-an-azure-virtual-machine"></a>Résoudre les problèmes de connexions Bureau à distance à un ordinateur virtuel Azure

La connexion du protocole RDP (Remote Desktop) à votre Windows Azure machine virtuelle (VM) peut échouer pour diverses raisons, vous laissant Impossible d’accéder à votre machine virtuelle. Le service Bureau à distance sur l’ordinateur virtuel, la connexion réseau ou du client Bureau à distance sur votre ordinateur hôte peut être le problème. Cet article décrit quelques-unes des méthodes plus courantes pour résoudre les problèmes de connexion RDP. 

Si vous avez besoin d’obtenir de l’aide à tout moment dans cet article, vous pouvez contacter des experts dans [les forums de débordement de pile MSDN Azure](https://azure.microsoft.com/support/forums/)Azure. Sinon, vous pouvez classer un incident de support Azure. Atteindre le [site de support technique Azure](https://azure.microsoft.com/support/options/) et sélectionnez **Obtenir la prise en charge**.

<a id="quickfixrdp"></a>

## <a name="quick-troubleshooting-steps"></a>Étapes de dépannage rapides
Après chaque étape de résolution des problèmes, essayez de vous reconnecter à la machine virtuelle :

1. Réinitialiser la configuration du Bureau à distance.
2. Vérifier le groupe de sécurité du réseau de règles / Cloud Services des points de terminaison.
3. Examinez les journaux de console de machine virtuelle.
4. Vérifier l’état de la ressource virtuelle.
5. Réinitialiser votre mot de passe d’ordinateur virtuel.
6. Redémarrez votre machine virtuelle.
7. Permet de redéployer votre machine virtuelle.

Continuer la lecture si vous avez besoin des procédures plus détaillées et des explications.

> [AZURE.TIP] Si le bouton de **connexion** pour votre machine virtuelle est grisé dans le portail et vous n’êtes pas connecté via une connexion [VPN de Site à Site](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) ou à [Express itinéraire](../expressroute/expressroute-introduction.md) vers Azure, vous devez créer et attribuer à votre machine virtuelle une adresse IP publique avant que vous pouvez utiliser le protocole RDP. Vous pouvez en savoir plus sur [les adresses IP publiques dans Azure](../virtual-network/virtual-network-ip-addresses-overview-arm.md).


## <a name="ways-to-troubleshoot-rdp-issues"></a>Comment résoudre les problèmes RDP
Vous pouvez résoudre les ordinateurs virtuels créés à l’aide du modèle de déploiement du Gestionnaire de ressources à l’aide d’une des méthodes suivantes :

- [Azure portal](#using-the-azure-portal) - idéal si vous devez rapidement réinitialiser les informations d’identification utilisateur ou configuration de RDP et que vous n’avez pas installé les outils d’Azure.
- [Azure PowerShell](#using-azure-powershell) - si vous êtes à l’aise avec une invite de commandes PowerShell, rétablir rapidement les RDP configuration ou utilisateur d’informations d’identification à l’aide des applets de commande PowerShell d’Azure.

Vous pouvez également rechercher les étapes sur le dépannage des ordinateurs virtuels créés à l’aide du [modèle de déploiement standard](#troubleshoot-vms-created-using-the-classic-deployment-model).


<a id="fix-common-remote-desktop-errors"></a>
## <a name="troubleshoot-using-the-azure-portal"></a>Résoudre les problèmes liés à l’utilisation du portail Azure
Après chaque étape de résolution des problèmes, essayez à nouveau de vous connecter à votre machine virtuelle. Si vous ne pouvez toujours pas vous connecter, essayez l’étape suivante.

1. **Réinitialiser votre connexion RDP**. Cette étape de dépannage réinitialise la configuration de RDP lors de connexions à distance sont désactivées ou de règles de pare-feu Windows bloquent RDP, par exemple.

    Sélectionnez votre machine virtuelle dans le portail Azure. Faites défiler le volet Paramètres de la section **Support + de résolution des problèmes** dans le bas de la liste. Cliquez sur le bouton de **réinitialisation de mot de passe** . Définir le **Mode** de **Réinitialiser la configuration uniquement** et puis cliquez sur le bouton de **mise à jour** :

    ![Réinitialiser la configuration de RDP sur le portail Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/reset-rdp.png)

2. **Règles de vérifier le groupe de sécurité du réseau**. Cette étape de dépannage vérifie que vous disposez d’une règle dans votre groupe de sécurité de réseau pour autoriser le trafic RDP. Le port par défaut pour le protocole RDP est le port TCP 3389. Une règle pour autoriser le trafic RDP peut ne pas être créée automatiquement lorsque vous créez votre machine virtuelle.

    Sélectionnez votre machine virtuelle dans le portail Azure. Cliquez sur les **interfaces réseau** dans le volet Paramètres.

    ![Afficher les interfaces de réseau d’un ordinateur virtuel dans Azure portal](./media/virtual-machines-windows-troubleshoot-rdp-connection/select-network-interfaces.png)

    Sélectionnez votre interface de réseau à partir de la liste (il y a généralement qu’un seul) :

    ![Sélectionnez l’interface réseau dans le portail Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/select-interface.png)

    Sélectionnez le **groupe de sécurité de réseau** pour afficher le groupe de sécurité de réseau associé à votre interface de réseau :

    ![Sélectionnez le groupe de sécurité réseau dans le portail Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/select-nsg.png)

    Vérifiez qu’il existe une règle de trafic entrant autorisant le trafic RDP sur le port TCP 3389. L’exemple suivant montre une règle de sécurité qui autorise le trafic RDP. Vous pouvez voir `Service` et `Action` sont configurés correctement :

    ![Vérifier la règle RDP NSG dans le portail Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/verify-nsg-rules.png)

    Si vous ne disposez pas d’une règle qui permet le trafic RDP, [créez une règle de groupe de sécurité réseau](virtual-machines-windows-nsg-quickstart-portal.md). Autoriser le port TCP 3389.

3. **Diagnostics de démarrage VM de révision**. Cette étape de dépannage passe en revue les journaux de console de machine virtuelle pour déterminer si la machine virtuelle est signalé un problème. Pas de tous les ordinateurs virtuels ont les diagnostics de démarrage activées, cette procédure de dépannage peut être facultative.
    
    Étapes de dépannage spécifiques dépassent le cadre de cet article, mais peuvent indiquer un problème plus large qui affecte les connexion RDP. Pour plus d’informations sur l’examen des journaux de console et capture d’écran de l’ordinateur virtuel, consultez [Démarrage des Diagnostics pour les machines virtuelles](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/).

4. **Vérifier l’état de la ressource virtuelle**. Cette étape de dépannage vérifie il n’y a pas de problèmes connus avec la plateforme Azure peut avoir un impact sur la connectivité de la machine virtuelle.

    Sélectionnez votre machine virtuelle dans le portail Azure. Faites défiler le volet Paramètres de la section **Support + de résolution des problèmes** dans le bas de la liste. Cliquez sur le bouton **état de la ressource** . Une machine virtuelle en bon état signale comme étant **disponibles**:

    ![Vérifier l’intégrité du ressource virtuelle dans le portail Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/check-resource-health.png)

5. **Réinitialiser les informations d’identification de l’utilisateur**. Cette étape de dépannage réinitialise le mot de passe sur un compte d’administrateur local lorsque vous ne connaissez pas ou avez oublié les informations d’identification.

    Sélectionnez votre machine virtuelle dans le portail Azure. Faites défiler le volet Paramètres de la section **Support + de résolution des problèmes** dans le bas de la liste. Cliquez sur le bouton de **réinitialisation de mot de passe** . Assurez-vous que le **Mode** est défini à **Réinitialiser le mot de passe** , puis entrez votre nom d’utilisateur et un mot de passe. Enfin, cliquez sur le bouton de **mise à jour** :

    ![Réinitialiser les informations d’identification de l’utilisateur dans le portail Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/reset-password.png)

6. **Redémarrez votre machine virtuelle**. Cette étape de dépannage peut corriger tous les problèmes sous-jacents que rencontre la machine virtuelle elle-même.

    Sélectionnez votre machine virtuelle dans Azure portal, puis cliquez sur l’onglet **vue d’ensemble** . Cliquez sur le bouton **redémarrer** :

    ![Redémarrez la machine virtuelle dans le portail Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/restart-vm.png)

7. **Redéployer votre machine virtuelle**. Cette étape de dépannage redéploie votre ordinateur virtuel à un autre hôte dans Azure pour corriger les problèmes de réseau ou de n’importe quelle plate-forme sous-jacente.

    Sélectionnez votre machine virtuelle dans le portail Azure. Faites défiler le volet Paramètres de la section **Support + de résolution des problèmes** dans le bas de la liste. Cliquez sur le bouton **redéployer** , puis **redéployer**:

    ![Redéploiement de la machine virtuelle dans le portail Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/redeploy-vm.png)

    Une fois cette opération terminée, les données de disque éphémère seront perdues et les adresses IP dynamiques qui sont associés à la machine virtuelle sont mises à jour.

Si vous rencontrez toujours des problèmes RDP, vous pouvez [Ouvrir une demande de support](https://azure.microsoft.com/support/options/) ou de lire des [concepts de dépannage plus détaillées RDP et les étapes](virtual-machines-windows-detailed-troubleshoot-rdp.md).


## <a name="troubleshoot-using-azure-powershell"></a>Dépannage à l’aide de PowerShell d’Azure
Si vous n’avez pas déjà fait, [installez et configurez la dernière PowerShell Azure](../powershell-install-configure.md).

Les exemples suivants utilisent des variables telles que `myResourceGroup`, `myVM`, et `myVMAccessExtension`. Remplacez ces noms de variable et les emplacements de vos propres valeurs.

> [AZURE.NOTE] Vous réinitialisez les informations d’identification de l’utilisateur et la configuration de RDP à l’aide de l’applet de commande PowerShell [Set-AzureRmVMAccessExtension](https://msdn.microsoft.com/library/mt619447.aspx) . Dans les exemples suivants, `myVMAccessExtension` est un nom que vous spécifiez en tant que partie du processus. Si vous avez déjà travaillé avec la VMAccessAgent, vous pouvez obtenir le nom de l’extension existante à l’aide de `Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"` pour vérifier les propriétés de la machine virtuelle. Pour afficher le nom, regardez sous la section « Extensions » de la sortie.

Après chaque étape de résolution des problèmes, essayez à nouveau de vous connecter à votre machine virtuelle. Si vous ne pouvez toujours pas vous connecter, essayez l’étape suivante.

1. **Réinitialiser votre connexion RDP**. Cette étape de dépannage réinitialise la configuration de RDP lors de connexions à distance sont désactivées ou de règles de pare-feu Windows bloquent RDP, par exemple.

    L’exemple suivant réinitialise la connexion RDP sur un ordinateur virtuel nommé `myVM` dans les `WestUS` emplacement et dans le groupe de ressource appelé `myResourceGroup`:

    ```powershell
    Set-AzureRmVMAccessExtension -ResourceGroupName "myResourceGroup" `
        -VMName "myVM" -Location Westus -Name "myVMAccessExtension"
    ```

2. **Règles de vérifier le groupe de sécurité du réseau**. Cette étape de dépannage vérifie que vous disposez d’une règle dans votre groupe de sécurité de réseau pour autoriser le trafic RDP. Le port par défaut pour le protocole RDP est le port TCP 3389. Une règle pour autoriser le trafic RDP peut ne pas être créée automatiquement lorsque vous créez votre machine virtuelle.

    Tout d’abord affecter toutes les données de configuration pour votre groupe de sécurité réseau pour le `$rules` variable. L’exemple suivant obtienne des informations sur le groupe de sécurité réseau nommé `myNetworkSecurityGroup` dans le groupe de ressources nommé `myResourceGroup`:

    ```powershell
    $rules = Get-AzureRmNetworkSecurityGroup -ResourceGroupName "myResourceGroup" `
        -Name "myNetworkSecurityGroup"
    ```

    Maintenant, permet d’afficher les règles qui sont configurées pour ce groupe de sécurité du réseau. Vérifiez qu’il existe une règle pour autoriser le port TCP 3389 pour les connexions entrantes comme suit :

    ```powershell
    $rules.SecurityRules
    ```

    L’exemple suivant montre une règle de sécurité qui autorise le trafic RDP. Vous pouvez voir `Protocol`, `DestinationPortRange`, `Access`, et `Direction` sont configurés correctement :

    ```powershell
    Name                     : default-allow-rdp
    Id                       : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/securityRules/default-allow-rdp
    Etag                     : 
    ProvisioningState        : Succeeded
    Description              : 
    Protocol                 : TCP
    SourcePortRange          : *
    DestinationPortRange     : 3389
    SourceAddressPrefix      : *
    DestinationAddressPrefix : *
    Access                   : Allow
    Priority                 : 1000
    Direction                : Inbound
    ```

    Si vous ne disposez pas d’une règle qui permet le trafic RDP, [créez une règle de groupe de sécurité réseau](virtual-machines-windows-nsg-quickstart-powershell.md). Autoriser le port TCP 3389.

3. **Réinitialiser les informations d’identification de l’utilisateur**. Cette étape de dépannage réinitialise le mot de passe du compte administrateur local que vous spécifiez lorsque vous ne connaissez pas ou avez oublié, les informations d’identification.

    Tout d’abord, indiquez le nom d’utilisateur et un mot de passe en affectant des informations d’identification pour le `$cred` variable comme suit :

    ```powershell
    $cred=Get-Credential
    ```

    Maintenant, mettre à jour les informations d’identification sur votre machine virtuelle. L’exemple suivant met à jour les informations d’identification sur un ordinateur virtuel nommé `myVM` dans les `WestUS` emplacement et dans le groupe de ressource appelé `myResourceGroup`:

    ```powershell
    Set-AzureRmVMAccessExtension -ResourceGroupName "myResourceGroup" `
        -VMName "myVM" -Location WestUS -Name "myVMAccessExtension" `
        -UserName $cred.GetNetworkCredential().Username `
        -Password $cred.GetNetworkCredential().Password
    ```

4. **Redémarrez votre machine virtuelle**. Cette étape de dépannage peut corriger tous les problèmes sous-jacents que rencontre la machine virtuelle elle-même.

    L’exemple suivant redémarre la machine virtuelle nommée `myVM` dans le groupe de ressources nommé `myResourceGroup`:

    ```powershell
    Restart-AzureRmVM -ResourceGroup "myResourceGroup" -Name "myVM"
    ```

5. **Redéployer votre machine virtuelle**. Cette étape de dépannage redéploie votre ordinateur virtuel à un autre hôte dans Azure pour corriger les problèmes de réseau ou de n’importe quelle plate-forme sous-jacente.

    L’exemple suivant redéploie l’ordinateur virtuel nommé `myVM` dans les `WestUS` emplacement et dans le groupe de ressource appelé `myResourceGroup`:

    ```powershell
    Set-AzureRmVM -Redeploy -ResourceGroupName "myResourceGroup" -Name "myVM"
    ```

Si vous rencontrez toujours des problèmes RDP, vous pouvez [Ouvrir une demande de support](https://azure.microsoft.com/support/options/) ou de lire des [concepts de dépannage plus détaillées RDP et les étapes](virtual-machines-windows-detailed-troubleshoot-rdp.md).


## <a name="troubleshoot-vms-created-using-the-classic-deployment-model"></a>Résoudre les problèmes des ordinateurs virtuels créés avec le modèle de déploiement standard

Après chaque étape de résolution des problèmes, essayez de vous reconnecter à la machine virtuelle.

1. **Réinitialiser votre connexion RDP**. Cette étape de dépannage réinitialise la configuration de RDP lors de connexions à distance sont désactivées ou de règles de pare-feu Windows bloquent RDP, par exemple.

    Sélectionnez votre machine virtuelle dans le portail Azure. Cliquez sur le **... Plus** , puis cliquez sur **Réinitialiser l’accès distant**:

    ![Réinitialiser la configuration de RDP sur le portail Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/classic-reset-rdp.png)

2.  **Vérifiez les Services en nuage de points de terminaison**. Cette étape de dépannage vérifie que vous disposez des points de terminaison de vos Services en nuage pour autoriser le trafic RDP. Le port par défaut pour le protocole RDP est le port TCP 3389. Une règle pour autoriser le trafic RDP peut ne pas être créée automatiquement lorsque vous créez votre machine virtuelle.

    Sélectionnez votre machine virtuelle dans le portail Azure. Cliquez sur le bouton des **points de terminaison** pour afficher les points de terminaison configurés pour votre ordinateur virtuel. Vérifiez que les points de terminaison existent qui permettent au trafic RDP sur le port TCP 3389.
    
    L’exemple suivant montre des points de terminaison valides qui autorisent le trafic RDP :

    ![Vérifiez les points de terminaison de Services en nuage dans le portail Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/classic-verify-cloud-services-endpoints.png)

    Si vous ne disposez pas d’un point de terminaison qui permet le trafic RDP, [créer un point de terminaison de Services en nuage](virtual-machines-windows-classic-setup-endpoints.md). Autoriser TCP sur le port 3389 privé.

3. **Diagnostics de démarrage VM de révision**. Cette étape de dépannage passe en revue les journaux de console de machine virtuelle pour déterminer si la machine virtuelle est signalé un problème. Pas de tous les ordinateurs virtuels ont les diagnostics de démarrage activées, cette procédure de dépannage peut être facultative.
    
    Étapes de dépannage spécifiques dépassent le cadre de cet article, mais peuvent indiquer un problème plus large qui affecte les connexion RDP. Pour plus d’informations sur l’examen des journaux de console et capture d’écran de l’ordinateur virtuel, consultez [Démarrage des Diagnostics pour les machines virtuelles](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/).

4. **Vérifier l’état de la ressource virtuelle**. Cette étape de dépannage vérifie il n’y a pas de problèmes connus avec la plateforme Azure peut avoir un impact sur la connectivité de la machine virtuelle.

    Sélectionnez votre machine virtuelle dans le portail Azure. Faites défiler le volet Paramètres de la section **Support + de résolution des problèmes** dans le bas de la liste. Cliquez sur le bouton **État de la ressource** . Une machine virtuelle en bon état signale comme étant **disponibles**:

    ![Vérifier l’intégrité du ressource virtuelle dans le portail Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/classic-check-resource-health.png)

5. **Réinitialiser les informations d’identification de l’utilisateur**. Cette étape de dépannage réinitialise le mot de passe du compte administrateur local que vous spécifiez lorsque vous ne connaissez pas ou avez oublié les informations d’identification.

    Sélectionnez votre machine virtuelle dans le portail Azure. Faites défiler le volet Paramètres de la section **Support + de résolution des problèmes** dans le bas de la liste. Cliquez sur le bouton de **réinitialisation de mot de passe** . Entrez votre nom d’utilisateur et un mot de passe. Enfin, cliquez sur le bouton **Enregistrer** :

    ![Réinitialiser les informations d’identification de l’utilisateur dans le portail Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/classic-reset-password.png)

6. **Redémarrez votre machine virtuelle**. Cette étape de dépannage peut corriger tous les problèmes sous-jacents que rencontre la machine virtuelle elle-même.

    Sélectionnez votre machine virtuelle dans Azure portal, puis cliquez sur l’onglet **vue d’ensemble** . Cliquez sur le bouton **redémarrer** :

    ![Redémarrez la machine virtuelle dans le portail Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/classic-restart-vm.png)
    
Si vous rencontrez toujours des problèmes RDP, vous pouvez [Ouvrir une demande de support](https://azure.microsoft.com/support/options/) ou de lire des [concepts de dépannage plus détaillées RDP et les étapes](virtual-machines-windows-detailed-troubleshoot-rdp.md).


## <a name="troubleshoot-specific-rdp-errors"></a>Dépannage d’erreurs spécifiques de RDP
Vous pouvez rencontrer un message d’erreur lorsque vous essayez de vous connecter à votre machine virtuelle via le protocole RDP. Messages d’erreur les plus courants sont les suivants :

- [La session distante a été déconnectée car aucun serveur de licences bureau à distance fournir une licence](virtual-machines-windows-troubleshoot-specific-rdp-errors.md#rdplicense).
- [Bureau à distance ne peut pas trouver l’ordinateur « nom »](virtual-machines-windows-troubleshoot-specific-rdp-errors.md#rdpname).
- Erreur de [d’authentification s’est produite. L’autorité de sécurité locale ne peut pas être contactée](virtual-machines-windows-troubleshoot-specific-rdp-errors.md#rdpauth).
- [Erreur de sécurité de Windows : vos informations d’identification n’a pas fonctionné](virtual-machines-windows-troubleshoot-specific-rdp-errors.md#wincred).
- [Cet ordinateur ne peut pas se connecter à l’ordinateur distant](virtual-machines-windows-troubleshoot-specific-rdp-errors.md#rdpconnect).


## <a name="additional-resources"></a>Ressources supplémentaires
Si aucune de ces erreurs est survenue, et vous ne pouvez toujours pas vous connecter à la machine virtuelle via le Bureau à distance, lisez le détaillées du [guide de dépannage pour le Bureau à distance](virtual-machines-windows-detailed-troubleshoot-rdp.md).

- [Package de diagnostic Azure IaaS (Windows)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)
- Pour le dépannage de l’accès à des applications qui s’exécutent sur une machine virtuelle, consultez [Dépannage des accès à une application en cours d’exécution sur un ordinateur virtuel d’Azure](virtual-machines-linux-troubleshoot-app-connection.md).
- Si vous rencontrez des problèmes à l’aide de SSH (Secure Shell) pour se connecter à une VM Linux dans Azure, consultez [résoudre les problèmes de connexions SSH à une VM Linux dans Azure](virtual-machines-linux-troubleshoot-ssh-connection.md).
