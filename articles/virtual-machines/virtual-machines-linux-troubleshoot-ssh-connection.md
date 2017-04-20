<properties
    pageTitle="Résoudre les problèmes de connexion SSH à une machine virtuelle | Microsoft Azure"
    description="Comment résoudre les problèmes « Échoué de la connexion SSH » ou « Connexion SSH refusée » pour un Azure VM fonctionnant sous Linux."
    keywords="SSH connexion refusée, ssh erreur, azure ssh, SSH Échec de la connexion"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="top-support-issue,azure-service-management,azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="iainfou"/>

# <a name="troubleshoot-ssh-connections-to-an-azure-linux-vm-that-fails-errors-out-or-is-refused"></a>Résoudre les problèmes de connexions SSH vers une machine virtuelle Linux de Azure qui tombe en panne, les erreurs, ou est refusée
Il existe diverses raisons que vous rencontrez des erreurs de SSH (Secure Shell), les échecs de connexion SSH ou SSH est refusé lorsque vous essayez de vous connecter à un ordinateur virtuel de Linux (VM). Cet article vous permet de rechercher et de corriger les problèmes. Vous pouvez utiliser le portail Azure, Azure CLI, ou Extension d’accès aux machines virtuelles pour Linux pour dépanner et résoudre des problèmes de connexion.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Si vous avez besoin d’obtenir de l’aide à tout moment dans cet article, vous pouvez contacter des experts dans [les forums de débordement de pile MSDN Azure](http://azure.microsoft.com/support/forums/)Azure. Sinon, vous pouvez classer un incident de support Azure. Atteindre le [site de support technique Azure](http://azure.microsoft.com/support/options/) , puis sélectionnez **obtenir de l’aide**. Pour plus d’informations sur l’utilisation de la prise en charge de Azure, lire la [prise en charge de Microsoft Azure Forum aux questions](http://azure.microsoft.com/support/faq/).


## <a name="quick-troubleshooting-steps"></a>Étapes de dépannage rapides
Après chaque étape de résolution des problèmes, essayez de vous reconnecter à la machine virtuelle.

1. Réinitialiser la configuration SSH.
2. Réinitialiser les informations d’identification de l’utilisateur.
3. Vérifiez que les règles du [Groupe de sécurité de réseau](../virtual-network/virtual-networks-nsg.md) autorisent le trafic SSH.
    - Vérifiez l’existence d’une règle de groupe de sécurité de réseau pour autoriser le trafic SSH (par défaut, le port TCP 22).
    - Vous ne pouvez pas utiliser la redirection de port / mise en correspondance sans utiliser un équilibreur de charge Azure.
4. Vérifiez la [santé des ressources machine virtuelle](../resource-health/resource-health-overview.md). 
    - Assurez-vous que la machine virtuelle signale comme étant en bon état.
    - Si vous avez des diagnostics de démarrage activé, vérifiez que la machine virtuelle ne signale pas les erreurs de démarrage dans les journaux.
5. Redémarrez la machine virtuelle.
6. Redéployer la machine virtuelle.

Continuez la lecture pour les étapes de dépannage plus détaillées et des explications.


## <a name="available-methods-to-troubleshoot-ssh-connection-issues"></a>Méthodes disponibles pour résoudre les problèmes de connexion SSH

Vous pouvez réinitialiser les informations d’identification ou de configuration SSH à l’aide d’une des méthodes suivantes :

- [Azure portal](#using-the-azure-portal) - idéal si vous devez rapidement réinitialiser la configuration SSH ou code SSH et que vous n’avez pas installé les outils d’Azure.
- [Les commandes CLI d’azure](#using-the-azure-cli) - si vous êtes déjà sur la ligne de commande rapidement réinitialiser la configuration SSH ou les informations d’identification.
- [Extension d’azure VMAccessForLinux](#using-the-vmaccess-extension) - créer et réutiliser les fichiers de définition de json pour réinitialiser les informations d’identification utilisateur ou de configuration SSH.

Après chaque étape de résolution des problèmes, essayez à nouveau de vous connecter à votre machine virtuelle. Si vous ne pouvez toujours pas vous connecter, essayez l’étape suivante.


## <a name="using-the-azure-portal"></a>L’utilisation du portail Azure
Le portail Azure offre un moyen rapide pour réinitialiser les informations d’identification utilisateur ou de configuration SSH sans installer les outils sur votre ordinateur local.

Sélectionnez votre machine virtuelle dans le portail Azure. Faites défiler jusqu'à la section **prise en charge + dépannage** et sélectionnez **Réinitialiser le mot de passe** comme dans l’exemple suivant :

![Réinitialiser la configuration SSH ou les informations d’identification dans le portail Azure](./media/virtual-machines-linux-troubleshoot-ssh-connection/reset-credentials-using-portal.png)

### <a name="reset-the-ssh-configuration"></a>Réinitialiser la configuration SSH
Dans un premier temps, sélectionnez `Reset SSH configuration only` dans le menu déroulant **Mode** , comme dans la capture d’écran précédente, puis cliquez sur le bouton **Réinitialiser** . Une fois cette opération terminée, essayez d’accéder de nouveau à votre machine virtuelle.

### <a name="reset-ssh-credentials-for-a-user"></a>Réinitialiser les informations d’identification SSH pour un utilisateur
Pour réinitialiser les informations d’identification d’un utilisateur existant, sélectionnez `Reset SSH public key` ou `Reset password` à partir du menu déroulant **Mode** comme dans la capture d’écran précédente. Spécifier le nom d’utilisateur et un code SSH ou un nouveau mot de passe, puis cliquez sur le bouton **Réinitialiser** .

Vous pouvez également créer un utilisateur avec des privilèges sudo sur la machine virtuelle à partir de ce menu. Entrez un nouveau nom d’utilisateur et un mot de passe associé ou un code SSH, puis cliquez sur le bouton **Réinitialiser** .


## <a name="using-the-azure-cli"></a>À l’aide de l’interface CLI Azure
Si vous n’avez pas déjà fait, [installez l’interface CLI d’Azure et se connecter à votre abonnement Azure](../xplat-cli-install.md). Assurez-vous que vous utilisez le mode du Gestionnaire de ressources comme suit :

```
azure config mode arm
```

Si vous avez créé et téléchargé une image de disque Linux personnalisée, assurez-vous que l' [Agent de Microsoft Azure Linux](virtual-machines-linux-agent-user-guide.md) version 2.0.5 ou version ultérieure est installé. Pour les machines virtuelles créées à l’aide d’images de la galerie, cette extension de l’accès est déjà installée et configurée pour vous.

### <a name="reset-ssh-configuration"></a>Réinitialiser la configuration SSH
La configuration SSHD lui-même peut être mal configurée ou que le service a rencontré une erreur. Vous pouvez réinitialiser SSHD pour vous assurer que la configuration SSH lui-même est valide. Réinitialisation SSHD doit être de la première étape de dépannage que vous prenez.

L’exemple suivant réinitialise SSHD sur un ordinateur virtuel nommé `myVM` dans le groupe de ressources nommé `myResourceGroup`. Utilisez vos propres noms de groupe de ressources et de la machine virtuelle comme suit :

```bash
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --reset-ssh
```

### <a name="reset-ssh-credentials-for-a-user"></a>Réinitialiser les informations d’identification SSH pour un utilisateur
Si SSHD semble fonctionner correctement, vous pouvez réinitialiser le mot de passe d’un utilisateur du donneur d’ordre. L’exemple suivant réinitialise les informations d’identification de `myUsername` à la valeur spécifiée dans `myPassword`, sur l’ordinateur virtuel nommé `myVM` dans `myResourceGroup`. Utilisez vos propres valeurs comme suit :

```bash
azure vm reset-access --resource-group myResourceGroup --name myVM \
     --username myUsername --password myPassword
```

Si vous utilisez l’authentification par clé SSH, vous pouvez réinitialiser le code SSH d’un utilisateur donné. L’exemple suivant met à jour la clé SSH stockée dans `~/.ssh/azure_id_rsa.pub` pour l’utilisateur nommé `myUsername`, sur l’ordinateur virtuel nommé `myVM` dans `myResourceGroup`. Utilisez vos propres valeurs comme suit :

```bash
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --username myUsername --ssh-key-file ~/.ssh/azure_id_rsa.pub
```


## <a name="using-the-vmaccess-extension"></a>À l’aide de l’extension VMAccess
L’Extension d’accès aux machines virtuelles pour Linux lit un fichier json qui définit les actions à effectuer. Ces actions incluent la réinitialisation SSHD, réinitialiser un code SSH ou l’ajout d’un utilisateur. Vous utilisez toujours la CLI Azure à appeler l’extension VMAccess, mais vous pouvez réutiliser les fichiers json sur plusieurs ordinateurs virtuels si vous le souhaitez. Cette approche vous permet de créer un référentiel de fichiers json qui peut ensuite être appelée pour compte tenu des scénarios.

### <a name="reset-sshd"></a>Réinitialisation SSHD
Créez un fichier nommé `PrivateConf.json` avec le contenu suivant :

```bash
{  
    "reset_ssh":"True"
}
```

À l’aide de la CLI d’Azure, puis appelez le `VMAccessForLinux` extension pour réinitialiser votre connexion SSHD en spécifiant votre fichier json. L’exemple suivant réinitialise SSHD sur l’ordinateur virtuel nommé `myVM` dans `myResourceGroup`. Utilisez vos propres valeurs comme suit :

```bash
azure vm extension set myResourceGroup myVM \
    VMAccessForLinux Microsoft.OSTCExtensions "1.2" \
    --private-config-path PrivateConf.json
```

### <a name="reset-ssh-credentials-for-a-user"></a>Réinitialiser les informations d’identification SSH pour un utilisateur
Si SSHD semble fonctionner correctement, vous pouvez réinitialiser les informations d’identification d’un utilisateur du donneur d’ordre. Pour réinitialiser le mot de passe d’un utilisateur, créez un fichier nommé `PrivateConf.json`. L’exemple suivant réinitialise les informations d’identification de `myUsername` à la valeur spécifiée dans `myPassword`. Entrez les lignes suivantes dans votre `PrivateConf.json` fichier, à l’aide de vos propres valeurs :

```bash
{
    "username":"myUsername", "password":"myPassword"
}
```

Ou pour réinitialiser le code SSH d’un utilisateur, créez un fichier nommé `PrivateConf.json`. L’exemple suivant réinitialise les informations d’identification de `myUsername` à la valeur spécifiée dans `myPassword`, sur l’ordinateur virtuel nommé `myVM` dans `myResourceGroup`. Entrez les lignes suivantes dans votre `PrivateConf.json` fichier, à l’aide de vos propres valeurs :

```bash
{
    "username":"myUsername", "ssh_key":"mySSHKey"
}
```

Après avoir créé votre fichier json, utilisez la CLI Azure pour appeler le `VMAccessForLinux` extension pour réinitialiser vos informations d’identification utilisateur SSH en spécifiant votre fichier json. L’exemple suivant réinitialise les informations d’identification sur l’ordinateur virtuel nommé `myVM` dans `myResourceGroup`. Utilisez vos propres valeurs comme suit :

```
azure vm extension set myResourceGroup myVM \
    VMAccessForLinux Microsoft.OSTCExtensions "1.2" \
    --private-config-path PrivateConf.json
```


## <a name="restart-a-vm"></a>Redémarrer une machine virtuelle
Si vous avez réinitialisé les informations d’identification utilisateur et de configuration SSH, ou a rencontré une erreur de cette manière, vous pouvez essayer de redémarrer la machine virtuelle à l’adresse sous-jacente des problèmes de calcul.

### <a name="azure-portal"></a>Azure portal
Pour redémarrer un ordinateur virtuel à l’aide de l’Azure portal, sélectionnez votre ordinateur virtuel, puis cliquez sur le ***Redémarrez** le bouton comme dans l’exemple suivant :

![Redémarrer une machine virtuelle dans le portail Azure](./media/virtual-machines-linux-troubleshoot-ssh-connection/restart-vm-using-portal.png)

### <a name="azure-cli"></a>CLI Azure
L’exemple suivant redémarre la machine virtuelle nommée `myVM` dans le groupe de ressources nommé `myResourceGroup`. Utilisez vos propres valeurs comme suit :

```bash
azure vm restart --resource-group myResourceGroup --name myVM
```


## <a name="redeploy-a-vm"></a>Redéployer une machine virtuelle
Vous pouvez redéployer un ordinateur virtuel vers un autre nœud dans Azure, ce qui peut résoudre les problèmes de réseau sous-jacents. Pour plus d’informations sur le redéploiement d’une machine virtuelle, consultez [redéployer l’ordinateur virtuel pour le nouveau nœud Azure](virtual-machines-windows-redeploy-to-new-node.md).

> [AZURE.NOTE] Une fois cette opération terminée, les données de disque éphémère seront perdues, et met à jour les adresses IP dynamiques qui sont associés à l’ordinateur virtuel.

### <a name="azure-portal"></a>Azure portal
Pour redéployer une machine virtuelle via le portail d’Azure, sélectionnez votre machine virtuelle et le faites défiler jusqu'à la section **prise en charge + de résolution des problèmes** . Cliquez sur le bouton de **redéployer** , comme dans l’exemple suivant :

![Redéployer une machine virtuelle dans le portail Azure](./media/virtual-machines-linux-troubleshoot-ssh-connection/redeploy-vm-using-portal.png)

### <a name="azure-cli"></a>CLI Azure
L’exemple suivant redéploie l’ordinateur virtuel nommé `myVM` dans le groupe de ressources nommé `myResourceGroup`. Utilisez vos propres valeurs comme suit :

```bash
azure vm redeploy --resource-group myResourceGroup --name myVM
```

## <a name="vms-created-by-using-the-classic-deployment-model"></a>Ordinateurs virtuels créés à l’aide du modèle de déploiement standard

Essayez les étapes suivantes pour résoudre les échecs de connexion SSH plus courants pour les machines virtuelles qui ont été créés à l’aide du modèle de déploiement classique. Après chaque étape, essayez de vous reconnecter à la machine virtuelle.

- Réinitialiser l’accès à distance à partir du [portail Azure](https://portal.azure.com). Sur le portail Azure, sélectionnez votre machine virtuelle et cliquez sur le bouton **De réinitialisation à distance...** .

- Redémarrez la machine virtuelle. Sur [Azure portal](https://portal.azure.com), sélectionnez votre ordinateur virtuel, puis cliquez sur le bouton **redémarrer** .

    -OU-

    Sur [Azure portal classique](https://manage.windowsazure.com), sélectionnez **machines virtuelles** > **Instances** > **redémarrage**.

- Permet de redéployer l’ordinateur virtuel vers un nouveau nœud d’Azure. Pour plus d’informations sur la façon de redéployer une machine virtuelle, consultez [redéployer l’ordinateur virtuel pour le nouveau nœud Azure](virtual-machines-windows-redeploy-to-new-node.md).

    Une fois cette opération terminée, les données de disque éphémère seront perdues, et met à jour les adresses IP dynamiques qui sont associés à l’ordinateur virtuel.

- Suivez les instructions sur la [façon de réinitialiser un mot de passe ou le SSH pour les machines virtuelles basées sur Linux](virtual-machines-linux-classic-reset-access.md) :
    - Réinitialiser le mot de passe ou un code SSH.
    - Créer un compte d’utilisateur _sudo_ .
    - Réinitialiser la configuration SSH.

- Vérifiez la santé des ressources de la machine virtuelle pour les problèmes de la plate-forme.<br>
  Sélectionnez votre machine virtuelle et le défilement vers le bas les **paramètres** > **Vérifier l’intégrité**.


## <a name="additional-resources"></a>Ressources supplémentaires

- Si vous ne parvenez toujours pas à SSH pour votre ordinateur virtuel après avoir suivi les étapes après, consultez [plus détaillée des étapes de dépannage](virtual-machines-linux-detailed-troubleshoot-ssh-connection.md) pour passer en revue les étapes supplémentaires pour résoudre votre problème.

- Pour plus d’informations sur la résolution des problèmes d’application, voir [dépanner l’accès à une application s’exécutant sur une machine virtuelle Azure](virtual-machines-linux-troubleshoot-app-connection.md)

- Pour plus d’informations sur le dépannage des ordinateurs virtuels qui ont été créés à l’aide du modèle de déploiement classique, voir [Comment faire pour réinitialiser un mot de passe ou le SSH pour les machines virtuelles basées sur Linux](virtual-machines-linux-classic-reset-access.md).
