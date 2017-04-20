<properties 
pageTitle="Activer la connexion Bureau à distance pour un rôle dans les Services en nuage Azure" 
description="Comment faire pour configurer votre application de service de cloud azure pour autoriser des connexions Bureau à distance" 
services="cloud-services" 
documentationCenter="" 
authors="sbtron" 
manager="timlt" 
editor=""/>
<tags 
ms.service="cloud-services" 
ms.workload="tbd" 
ms.tgt_pltfrm="na" 
ms.devlang="na" 
ms.topic="article" 
ms.date="02/17/2016" 
ms.author="saurabh"/>

# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services"></a>Activer la connexion Bureau à distance pour un rôle dans les Services en nuage Azure

>[AZURE.SELECTOR]
- [Azure portal classique](cloud-services-role-enable-remote-desktop.md)
- [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
- [Visual Studio](../vs-azure-tools-remote-desktop-roles.md)


Bureau à distance vous permet d’accéder au bureau d’un rôle qui exécute dans Azure. Vous pouvez utiliser une connexion Bureau à distance pour dépanner et diagnostiquer les problèmes avec votre application pendant son exécution. 

Vous pouvez activer une connexion Bureau à distance dans votre rôle au cours du développement en incluant les modules du Bureau à distance dans la définition de service ou vous pouvez choisir d’activer le Bureau à distance par le biais de l’Extension du Bureau à distance. L’approche recommandée est d’utiliser l’extension de bureau à distance que vous pouvez activer le Bureau à distance même après le déploiement de l’application sans avoir à redéployer votre application. 


## <a name="configure-remote-desktop-from-the-azure-classic-portal"></a>Configurer le Bureau à distance à partir du portail classique Azure
Le portail classique Azure utilise l’approche de l’Extension du Bureau à distance afin de l’activer Bureau à distance même après que l’application est déployée. La page de **configuration** de votre service cloud vous permet d’activer le Bureau à distance, modifiez le compte d’administrateur local utilisé pour se connecter aux ordinateurs virtuels, le certificat utilisé dans l’authentification et définie la date d’expiration. 


1. Cliquez sur **Services dans le nuage**et cliquez sur le nom du service cloud, puis cliquez sur **configurer**.

2. Cliquez sur **à distance**.
    
    ![Services en nuage à distance](./media/cloud-services-role-enable-remote-desktop/CloudServices_Remote.png)
    
    > [AZURE.WARNING] Toutes les instances de rôle seront redémarrés lorsque vous activez le Bureau à distance et cliquez sur OK (coche) pour la première fois. Pour éviter le redémarrage, le certificat utilisé pour crypter le mot de passe doit être installé sur le rôle. Pour empêcher le redémarrage de l’ordinateur, [télécharger un certificat pour le service en nuage](cloud-services-how-to-create-deploy/#how-to-upload-a-certificate-for-a-cloud-service) , puis revenez à cette boîte de dialogue.
    

3. Dans les **rôles**, sélectionnez le rôle que vous souhaitez mettre à jour ou sélectionnez **tous** pour tous les rôles.

4. Apportez les modifications suivantes :
    
    - Pour activer le Bureau à distance, activez la case à cocher **Activer le Bureau à distance** . Pour désactiver le Bureau à distance, désactivez la case à cocher.
    
    - Créer un compte à utiliser pour les connexions Bureau à distance pour les instances de rôle.
    
    - Mettre à jour le mot de passe pour le compte existant.
    
    - Sélectionnez un certificat chargé à utiliser pour l’authentification (téléchargement du certificat à l’aide de **téléchargement** sur la page **certificats** ) ou créer un nouveau certificat. 
    
    - Modifier la date d’expiration pour la configuration du Bureau à distance.

5. Lorsque vous avez terminé vos mises à jour de la configuration, cliquez sur **OK** (coche).


## <a name="remote-into-role-instances"></a>À distance dans des instances de rôle
Une fois que le Bureau à distance est activé sur les rôles vous pouvez distant dans une instance de rôle par le biais de divers outils.

Pour vous connecter à une instance de rôle à partir du portail classique Azure :
    
  1.   Cliquez sur **Instances** pour ouvrir la page **Instances** .
  2.   Sélectionnez une instance de rôle qui a configuré de bureau à distance.
  3.   Cliquez sur **se connecter**, puis suivez les instructions pour ouvrir le bureau. 
  4.   Cliquez sur **Ouvrir** , puis sur **se connecter** pour démarrer la connexion Bureau à distance. 


### <a name="use-visual-studio-to-remote-into-a-role-instance"></a>Utilisation de Visual Studio à distant dans une instance de rôle

Dans Visual Studio, Explorateur de serveurs :

1. Développez le **Azure\\les Services en nuage\\[nom de service cloud]** nœud.
2. Développez **intermédiaire** ou **Production**.
3. Développez le rôle individuel.
4. Cliquez sur une des instances de rôle et cliquez sur **se connecter en utilisant le Bureau à distance...**, puis entrez le nom d’utilisateur et le mot de passe. 

![Bureau à distance de l’Explorateur de serveurs](./media/cloud-services-role-enable-remote-desktop/ServerExplorer_RemoteDesktop.png)


### <a name="use-powershell-to-get-the-rdp-file"></a>Utilisation de PowerShell pour obtenir le fichier RDP
Vous pouvez utiliser l’applet de commande [Get-AzureRemoteDesktopFile](https://msdn.microsoft.com/library/azure/dn495261.aspx) pour récupérer le fichier RDP. Vous pouvez ensuite utiliser le fichier RDP avec connexion Bureau à distance pour accéder au service de cloud.

### <a name="programmatically-download-the-rdp-file-through-the-service-management-rest-api"></a>Programmer le téléchargement du fichier RDP via l’API REST de gestion de Service
Vous pouvez utiliser l’opération de reste de [Télécharger le fichier RDP](https://msdn.microsoft.com/library/jj157183.aspx) pour télécharger le fichier RDP. 



## <a name="to-configure-remote-desktop-in-the-service-definition-file"></a>Pour configurer le Bureau à distance dans le fichier de définition de service

Cette méthode permet d’activer le Bureau à distance de l’application pendant le développement. Cette approche requiert des mots de passe cryptés stockés dans la configuration de votre service de fichiers et les mises à jour de la configuration du Bureau à distance nécessite un redéploiement de l’application. Si vous souhaitez éviter ces inconvénients, vous devez utiliser l’approche d’extension de bureau à distance en fonction décrite ci-dessus.  

Vous pouvez utiliser Visual Studio pour [activer une connexion Bureau à distance](../vs-azure-tools-remote-desktop-roles.md) à l’aide de l’approche de fichier de définition de service.  
Les étapes ci-dessous décrivent les modifications nécessaires pour les fichiers de modèle de service pour activer le Bureau à distance. Visual Studio s’effectue automatiquement ces modifications lors de la publication.

### <a name="set-up-the-connection-in-the-service-model"></a>Configurer la connexion dans le modèle de service 
Utilisez l’élément **d’importation** pour importer le module **d’accès à distance** et le module **RemoteForwarder** dans le fichier [ServiceDefinition.csdef](cloud-services-model-and-package.md#csdef) .

Le fichier de définition de service doit être semblable à l’exemple suivant sur le `<Imports>` élément ajouté.

```xml
<ServiceDefinition name="<name-of-cloud-service>" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2013-03.2.0">
    <WebRole name="WebRole1" vmsize="Small">
        <Sites>
            <Site name="Web">
                <Bindings>
                    <Binding name="Endpoint1" endpointName="Endpoint1" />
                </Bindings>
            </Site>
        </Sites>
        <Endpoints>
            <InputEndpoint name="Endpoint1" protocol="http" port="80" />
        </Endpoints>
        <Imports>
            <Import moduleName="Diagnostics" />
            <Import moduleName="RemoteAccess" />
            <Import moduleName="RemoteForwarder" />
        </Imports>
    </WebRole>
</ServiceDefinition>
```
Le fichier [ServiceConfiguration.cscfg](cloud-services-model-and-package.md#cscfg) doit être similaire à l’exemple suivant, notez la `<ConfigurationSettings>` et `<Certificates>` éléments. Le certificat spécifié doit être [téléchargé sur le service en nuage](../cloud-services-how-to-create-deploy.md#how-to-upload-a-certificate-for-a-cloud-service).

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceConfiguration serviceName="<name-of-cloud-service>" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="3" osVersion="*" schemaVersion="2013-03.2.0">
    <Role name="WebRole1">
        <Instances count="2" />
        <ConfigurationSettings>
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.Enabled" value="true" />
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountUsername" value="[name-of-user-account]" />
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountEncryptedPassword" value="[base-64-encrypted-user-password]" />
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountExpiration" value="[certificate-expiration]" />
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteForwarder.Enabled" value="true" />
        </ConfigurationSettings>
        <Certificates>
            <Certificate name="Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption" thumbprint="[certificate-thumbprint]" thumbprintAlgorithm="sha1" />
        </Certificates>
    </Role>
</ServiceConfiguration>
```


## <a name="additional-resources"></a>Ressources supplémentaires

[Comment faire pour configurer les Services en nuage](cloud-services-how-to-configure.md)