<properties
    pageTitle="Installez IIS sur votre premier Windows VM | Microsoft Azure"
    description="Faites des essais avec votre première machine virtuelle de Windows par l’installation d’IIS et ouvrir le port 80 à l’aide du portail Azure."
    keywords=""
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>
<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="cynthn"/>

# <a name="experiment-with-installing-a-role-on-your-windows-vm"></a>Faites des essais avec l’installation d’un rôle sur votre machine virtuelle de Windows
    
Une fois que vous avez votre première machine virtuelle (VM) opérationnel, vous pouvez atteindre l’installation des logiciels et des services. Pour ce didacticiel, nous allons utiliser le Gestionnaire de serveur sur la machine virtuelle de Windows Server pour installer les services IIS. Ensuite, nous allons créer un groupe de sécurité réseau (NSG) à l’aide du portail Azure à ouvrir le port 80 pour le trafic IIS. 

Si vous ne l’avez pas déjà créé votre premier VM, vous devez revenir à [créer votre première machine virtuelle de Windows dans le portail Azure](virtual-machines-windows-hero-tutorial.md) avant de continuer avec ce didacticiel.

## <a name="make-sure-the-vm-is-running"></a>Assurez-vous que l’ordinateur virtuel est en cours d’exécution.

1. Ouvrez le [portail Azure](https://portal.azure.com).
2. Dans le menu de concentrateur, cliquez sur **ordinateurs virtuels**. Sélectionnez l’ordinateur virtuel dans la liste.
3. Si l’état est **Stopped (Deallocated)**, cliquez sur le bouton **Démarrer** sur la blade **d’éducation** de la machine virtuelle. Si le statut est **en cours d’exécution**, vous pouvez passer à l’étape suivante.

## <a name="connect-to-the-virtual-machine-and-sign-in"></a>Se connecter à l’ordinateur virtuel et ouvrez une session

1.  Dans le menu de concentrateur, cliquez sur **ordinateurs virtuels**. Sélectionnez l’ordinateur virtuel dans la liste.

3. Sur la lame pour l’ordinateur virtuel, cliquez sur **se connecter**. Crée et télécharge un Remote Desktop Protocol (fichier .rdp) est comme un raccourci pour vous connecter à votre ordinateur. Vous pouvez souhaiter enregistrer le fichier sur votre bureau pour y accéder facilement. **Ouvrez** ce fichier pour vous connecter à votre ordinateur virtuel.

    ![Capture d’écran du portail Azure montrant comment se connecter à votre machine virtuelle](./media/virtual-machines-windows-hero-tutorial/connect.png)

4. Vous obtenez un avertissement indiquant que le .rdp provient d’un éditeur inconnu. Ceci est normal. Dans la fenêtre Bureau à distance, cliquez sur **se connecter** pour continuer.

    ![Capture d’écran d’un avertissement sur un éditeur inconnu](./media/virtual-machines-windows-hero-tutorial/rdp-warn.png)

5. Dans la fenêtre sécurité de Windows, tapez le nom d’utilisateur et le mot de passe du compte local que vous avez créé lorsque vous avez créé l’ordinateur virtuel. Le nom d’utilisateur est entré comme *vmname*& #, 92 ; *nom d’utilisateur*, puis cliquez sur **OK**.

    ![Capture d’écran de saisie le nom de machine virtuelle, nom d’utilisateur et un mot de passe](./media/virtual-machines-windows-hero-tutorial/credentials.png)
    
6.  Vous obtenez un message d’avertissement que le certificat ne peut pas être vérifié. Ceci est normal. Cliquez sur **Oui** pour vérifier l’identité de l’ordinateur virtuel et terminer la session.

    ![Capture d’écran présentant un message à propos de la vérification de l’identité de la machine virtuelle](./media/virtual-machines-windows-hero-tutorial/cert-warning.png)


Si vous exécutez des problèmes lorsque vous essayez de vous connecter, consultez [résoudre les problèmes de bureau à distance, connexions à un Windows Azure Machine virtuelle](virtual-machines-windows-troubleshoot-rdp-connection.md).


## <a name="install-iis-on-your-vm"></a>Installez IIS sur votre ordinateur virtuel

Maintenant que vous êtes connecté à l’ordinateur virtuel, nous allons installer un rôle de serveur de sorte que vous pouvez faire des essais plus.

1. Si elle n’est pas déjà ouverte, ouvrez le **Gestionnaire de serveur** . Cliquez sur le menu **Démarrer** , puis cliquez sur **Gestionnaire de serveur**.
2. Dans le **Gestionnaire de serveur**, sélectionnez **Serveur Local** dans le volet de gauche. 
3. Dans le menu, sélectionnez **Gérer** > **Ajout de rôles et de fonctionnalités**.
4. Dans l’ajout de rôles et fonctionnalités Assistant, dans la page **Type d’Installation** , choisissez **installation basée sur les rôles ou basé sur la fonctionnalité**, puis cliquez sur **suivant**.

    ![Capture d’écran présentant l’onglet Ajout de rôles et de fonctionnalités Assistant pour un Type d’Installation](./media/virtual-machines-windows-hero-tutorial/role-wizard.png)

5. Sélectionnez la machine virtuelle à partir du pool du serveur et cliquez sur **suivant**.
6. Dans la page **Rôles de serveur** , sélectionnez **Serveur Web (IIS)**.

    ![Capture d’écran présentant l’onglet Ajout de rôles et de fonctionnalités Assistant pour les rôles de serveur](./media/virtual-machines-windows-hero-tutorial/add-iis.png)

7. Dans le menu contextuel sur Ajouter les fonctionnalités requises pour IIS, assurez-vous que **les outils d’administration** est activée et puis cliquez sur **Ajouter des fonctionnalités**. Lors de la fermeture de la fenêtre contextuelle, cliquez sur **suivant** dans l’Assistant.

    ![Capture d’écran présentant les fenêtres pop-up à confirmer l’ajout du rôle IIS](./media/virtual-machines-windows-hero-tutorial/confirm-add-feature.png)

8. Sur la page de fonctionnalités, cliquez sur **suivant**.
9. Sur la page du **Rôle de serveur Web (IIS)** , cliquez sur **suivant**. 
10. Dans la page **Services de rôle** , cliquez sur **suivant**. 
11. Sur la page de **Confirmation** , cliquez sur **installer**. 
12. Lorsque l’installation est terminée, cliquez sur **Fermer** dans l’Assistant.



## <a name="open-port-80"></a>Ouvrir le port 80 

Afin que votre machine virtuelle accepter le trafic entrant sur le port 80, vous devez ajouter une règle de trafic entrant pour le groupe de sécurité du réseau. 

1. Ouvrez le [portail Azure](https://portal.azure.com).
2. Dans des **machines virtuelles** , sélectionnez l’ordinateur virtuel que vous avez créé.
3. Dans les paramètres de machines virtuelles, sélectionner des **interfaces réseau** et sélectionnez l’interface réseau existante.

    ![Capture d’écran présentant le paramètre de la machine virtuelle pour les interfaces réseau](./media/virtual-machines-windows-hero-tutorial/network-interface.png)

4. Dans **Essentials** pour l’interface réseau, cliquez sur le **groupe de sécurité réseau**.

    ![Capture d’écran présentant la section Essentials pour l’interface réseau](./media/virtual-machines-windows-hero-tutorial/select-nsg.png)

5. De la lame **Essentials** pour le NSG, vous devez avoir une règle entrante par défaut existant pour **rdp autorisent par défaut** qui permet de vous connecter à la machine virtuelle. Vous allez ajouter une autre règle de trafic entrant pour autoriser le trafic IIS. Cliquez sur **la règle de sécurité entrante**.

    ![Capture d’écran présentant la section Essentials pour du NSG](./media/virtual-machines-windows-hero-tutorial/inbound.png)

6. Dans **les règles de sécurité de trafic entrant**, cliquez sur **Ajouter**.

    ![Capture d’écran présentant le bouton pour ajouter une règle de sécurité](./media/virtual-machines-windows-hero-tutorial/add-rule.png)

7. Dans **les règles de sécurité de trafic entrant**, cliquez sur **Ajouter**. Tapez **80** dans la plage de ports et assurez-vous que l’option **Qu'autoriser** est sélectionnée. Lorsque vous avez terminé, cliquez sur **OK**.

    ![Capture d’écran présentant le bouton pour ajouter une règle de sécurité](./media/virtual-machines-windows-hero-tutorial/port-80.png)
 
Pour plus d’informations sur NSGs, les règles d’entrée et de sortie, reportez-vous à la section [Autoriser l’accès externe à votre machine virtuelle via le portail Azure](virtual-machines-windows-nsg-quickstart-portal.md)
 
## <a name="connect-to-the-default-iis-website"></a>Se connecter à un site Web IIS par défaut

1. Dans le portail Azure, cliquez sur les **machines virtuelles** et sélectionnez votre machine virtuelle.
2. La lame **Essentials** , copiez votre **adresse IP publique**.

    ![Capture d’écran présentant où trouver l’adresse IP publique de votre machine virtuelle](./media/virtual-machines-windows-hero-tutorial/ipaddress.png)

2. Ouvrez un navigateur et dans la barre d’adresses, tapez votre adresse IP publique comme suit : http://<publicIPaddress> et appuyez sur **entrée** pour accéder à cette adresse.
3. Votre navigateur doit ouvrir la page web IIS par défaut. Il ressemble à ceci :

    ![Capture d’écran montrant à quoi ressemble la page IIS par défaut dans un navigateur](./media/virtual-machines-windows-hero-tutorial/iis-default.png)

    

## <a name="next-steps"></a>Étapes suivantes

- Vous pouvez également essayer [y attacher un disque de données](virtual-machines-windows-attach-disk-portal.md) à votre machine virtuelle. Les disques de données offrent plus de stockage pour votre ordinateur virtuel.
