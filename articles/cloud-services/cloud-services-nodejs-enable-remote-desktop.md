<properties 
    pageTitle="Activer le Bureau à distance pour les services en nuage (Node.js)" 
    description="Découvrez comment activer l’accès Bureau à distance pour les ordinateurs virtuels qui héberge votre application Azure Node.js." 
    services="cloud-services" 
    documentationCenter="nodejs" 
    authors="rmcmurray" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="cloud-services" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="nodejs" 
    ms.topic="article" 
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

# <a name="enabling-remote-desktop-in-azure"></a>L’activation du Bureau à distance dans Azure

Bureau à distance vous permet d’accéder au bureau d’une instance de rôle qui s’exécute dans Azure. Vous pouvez utiliser une connexion Bureau à distance pour configurer l’ordinateur virtuel ou résoudre des problèmes avec votre application.

> [AZURE.NOTE] Cet article s’applique aux applications Node.js hébergées comme un Service en nuage Azure.


## <a name="prerequisites"></a>Conditions préalables

- Installez et configurez [Azure Powershell](../powershell-install-configure.md).
- Déployer une application Node.js à un Service Cloud Azure. Pour plus d’informations, consultez [Générer et déployer une application Node.js à un Service de Cloud Azure](cloud-services-nodejs-develop-deploy-app.md).


## <a name="step-1-use-azure-powershell-to-configure-the-service-for-remote-desktop-access"></a>Étape 1 : Utiliser Azure PowerShell pour configurer le service pour l’accès aux services Bureau à distance

Pour utiliser le Bureau à distance, vous devez mettre à jour la configuration et la définition de service Azure avec un nom d’utilisateur, un mot de passe et un certificat. 

Effectuez les opérations suivantes à partir d’un ordinateur qui contient les fichiers sources de votre application.

1. Exécution **de Windows PowerShell** en tant qu’administrateur. (À partir du **Menu Démarrer** ou **l’Écran de démarrage**, recherchez **Windows PowerShell**.)

2.  Accédez au répertoire qui contient la définition de service (.csdef) et les fichiers de configuration (.cscfg) de service.

3. Entrez l’applet de commande PowerShell suivante :

        Enable-AzureServiceProjectRemoteDesktop

4. À l’invite, entrez un nom d’utilisateur et le mot de passe.

    ![Activer-azureserviceprojectremotedesktop][enable-rdp]

3.  Entrez l’applet de commande PowerShell suivante pour publier les modifications :

        Publish-AzureServiceProject

    ![publication-azureserviceproject][publish-project]

## <a name="step-2-connect-to-the-role-instance"></a>Étape 2 : Se connecter à l’instance de rôle

Après avoir publié la mise à jour définition de service, vous pouvez vous connecter à l’instance de rôle.

1.  Dans [Azure portal classique], sélectionnez les **Services en nuage** , puis votre service.

    ![Azure portal classique][cloud-services]

2.  Cliquez sur **les Instances**, puis cliquez sur pour afficher les instances de votre service de **Production** ou **intermédiaire** . Sélectionnez une instance et puis cliquez sur **se connecter** au bas de la page.

    ![La page instances][3]

2.  Lorsque vous cliquez sur **se connecter**, le navigateur web vous invite à enregistrer un fichier .rdp. Ouvrir ce fichier. (Par exemple, si vous utilisez Internet Explorer, cliquez sur **Ouvrir**.)

    ![inviter à ouvrir ou à enregistrer le fichier .rdp][4]

3.  Lorsque le fichier est ouvert, le message de sécurité suivant s’affiche :

    ![Invite de sécurité de Windows][5]

4.  Cliquez sur **se connecter**, et une invite de sécurité s’affiche pour la saisie des informations d’identification pour accéder à l’instance. Entrez le mot de passe que vous avez créé dans [étape 1] [étape 1 : configurer le service pour l’accès Bureau à distance à l’aide de PowerShell d’Azure], puis cliquez sur **OK**.

    ![nom d’utilisateur/mot de passe][6]

Lorsque la connexion est établie, connexion Bureau à distance affiche le bureau de l’instance dans Azure. 

![Session Bureau à distance][7]

## <a name="step-3-configure-the-service-to-disable-remote-desktop-access"></a>Étape 3 : Configurer le service pour désactiver l’accès Bureau à distance 

Lorsque vous avez besoin ne sont plus des connexions Bureau à distance pour les instances de rôles dans le nuage, désactivez l’accès Bureau à distance à l’aide de [PowerShell d’Azure].

1.  Entrez l’applet de commande PowerShell suivante :

        Disable-AzureServiceProjectRemoteDesktop

2.  Entrez l’applet de commande PowerShell suivante pour publier les modifications :

        Publish-AzureServiceProject

## <a name="additional-resources"></a>Ressources supplémentaires

- [Accéder à distance à des Instances de rôle dans Azure] 
- [À l’aide du Bureau à distance avec les rôles d’Azure]
- [Centre de développement Node.js](/develop/nodejs/)

  [PowerShell Azure]: http://go.microsoft.com/?linkid=9790229&clcid=0x409

[Azure portal classique]: http://manage.windowsazure.com
[publish-project]: ./media/cloud-services-nodejs-enable-remote-desktop/publish-rdp.png
[enable-rdp]: ./media/cloud-services-nodejs-enable-remote-desktop/enable-rdp.png
[cloud-services]: ./media/cloud-services-nodejs-enable-remote-desktop/cloud-services-remote.png
[3]: ./media/cloud-services-nodejs-enable-remote-desktop/cloud-service-instance.png
[4]: ./media/cloud-services-nodejs-enable-remote-desktop/rdp-open.png
[5]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-12.png
[6]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-13.png
[7]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-14.png
  
[Accéder à distance à des Instances de rôle dans Azure]: http://msdn.microsoft.com/library/windowsazure/hh124107.aspx
[À l’aide du Bureau à distance avec les rôles d’Azure]: http://msdn.microsoft.com/library/windowsazure/gg443832.aspx
 