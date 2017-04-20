<properties
    pageTitle="À l’aide de la redirection dans Azure RemoteApp | Microsoft Azure"
    description="Découvrez comment configurer et utiliser la redirection de RemoteApp"
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />

# <a name="using-redirection-in-azure-remoteapp"></a>À l’aide de la redirection dans Azure RemoteApp

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lecture de l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus de détails.

La redirection de périphérique permet à vos utilisateurs interagissent avec les applications distantes à l’aide de périphériques connectés à leur ordinateur local, un téléphone ou un Tablet PC. Par exemple, si vous avez fourni Skype par le biais de RemoteApp d’Azure, l’utilisateur doit la caméra installée sur leur ordinateur pour travailler avec Skype. Cela est également vrai pour des imprimantes, des haut-parleurs, des moniteurs et un périphériques connectés à la plage de USB.

RemoteApp utilise le protocole RDP (Remote Desktop) et RemoteFX pour fournir la redirection.

## <a name="what-redirection-is-enabled-by-default"></a>Ce que la redirection est activée par défaut ?
Lorsque vous utilisez RemoteApp, les redirections suivantes sont activées par défaut. Les informations contenues dans les parenthèses indiquent le paramètre RDP.

- Lire les sons sur l’ordinateur local (**lire sur cet ordinateur**). (audiomode:i:0)
- Capturer les données audio à partir de l’ordinateur local et l’envoyer à l’ordinateur distant (**enregistrement à partir de cet ordinateur**). (audiocapturemode:i:1)
- Imprimer sur des imprimantes locales (redirectprinters:i:1)
- Ports COM (redirectcomports:i:1)
- Périphérique de carte à puce (redirectsmartcards:i:1)
- Presse-papiers (capacité de copier et coller) (redirectclipboard:i:1)
- Désactivez le lissage des polices de type (autoriser le lissage des polices : i:1)
- Rediriger des périphériques Plug-and-Play toutes prises en charge. (devicestoredirect:s : *)

## <a name="what-other-redirection-is-available"></a>L’autre la redirection est disponible ?
Deux options de redirection sont désactivées par défaut :

- La redirection de lecteur (mappage du lecteur) : lecteurs de l’ordinateur local sont des lecteurs mappés dans la session à distance. Cela vous permet de vous enregistrer ou ouvrir des fichiers à partir de vos disques durs locaux pendant que vous travaillez dans la session à distance.
- La redirection de USB : vous pouvez utiliser les périphériques USB connectés à votre ordinateur local dans la session à distance.

## <a name="change-your-redirection-settings-in-remoteapp"></a>Modifier vos paramètres de redirection dans RemoteApp
Vous pouvez modifier les paramètres de redirection de périphérique pour une collection à l’aide de Microsoft Azure PowerShell avec le Kit de développement logiciel. Après avoir installé le nouveau PowerShell et le Kit de développement logiciel, configurez tout d’abord pour gérer votre inscription, comme décrit dans la [procédure pour installer et configurer Azure PowerShell](../powershell-install-configure.md).

Puis utilisez une commande semblable à la suivante pour définir les propriétés RDP personnalisées :

    Set-AzureRemoteAppCollection -CollectionName <collection name>  -CustomRdpProperty "drivestoredirect:s:*`nusbdevicestoredirect:s:*"

(Notez que *n* est utilisée comme délimiteur entre des propriétés individuelles).

Pour obtenir une liste des propriétés RDP personnalisées sont configurées, exécutez l’applet de commande suivant. Notez que les propriétés personnalisées seulement sont représentées en tant que des résultats de sortie et pas les propriétés par défaut :  

    Get-AzureRemoteAppCollection -CollectionName <collection name>

Lorsque vous définissez des propriétés personnalisées vous devez spécifier toutes les propriétés personnalisées de chaque fois ; Sinon, le paramètre rétablit sur désactivé.   

### <a name="common-examples"></a>Exemples courants
Utilisez l’applet de commande suivante pour activer la redirection de lecteur :  

    Set-AzureRemoteAppCollection -CollectionName <collection name>  -CustomRdpProperty "drivestoredirect:s:*”

Cette applet de commande permet d’activer les ports USB et lecteur de redirection :

    Set-AzureRemoteAppCollection -CollectionName <collection name>  -CustomRdpProperty "drivestoredirect:s:*`nusbdevicestoredirect:s:*"

Cette applet de commande permet de désactiver le partage du Presse-papiers :  

    Set-AzureRemoteAppCollection -CollectionName <collection name>  -CustomRdpProperty "redirectclipboard:i:0”

> [AZURE.IMPORTANT] Veillez à déconnecter complètement de tous les utilisateurs de la collection (et pas simplement les déconnecter) avant de tester la modification. Pour garantir que les utilisateurs sont complètement déconnectés, accédez à l’onglet **Sessions** de la collection dans le portail Azure et déconnecter tous les utilisateurs qui sont déconnectés ou connectés. Parfois, il peut prendre plusieurs secondes pour les lecteurs locaux à afficher dans l’Explorateur dans la session.

## <a name="change-usb-redirection-settings-on-your-windows-client"></a>Modifier les paramètres de redirection USB sur votre client Windows

Si vous souhaitez utiliser la redirection de USB sur un ordinateur qui se connecte à RemoteApp, il y a 2 actions devant se produire. 1 - votre administrateur doit activer la redirection USB au niveau de la collection à l’aide de PowerShell d’Azure. 2 - sur chaque périphérique où vous souhaitez utiliser la redirection de USB, vous devez activer une stratégie de groupe qui permet de. Cette étape va doit être réalisé pour chaque utilisateur qui souhaite utiliser la redirection de USB.

> [AZURE.NOTE] Redirection de USB avec Azure RemoteApp est uniquement pris en charge pour les ordinateurs Windows.

### <a name="enable-usb-redirection-for-the-remoteapp-collection"></a>Activer la redirection de USB pour la collection de RemoteApp
Pour activer la redirection USB au niveau de la collection, utilisez l’applet de commande suivante :

    Set-AzureRemoteAppCollection -CollectionName <collection_name> -CustomRdpProperty "nusbdevicestoredirect:s:*"

### <a name="enable-usb-redirection-for-the-client-computer"></a>Activer la redirection de USB de l’ordinateur client

Pour configurer les paramètres de redirection USB sur votre ordinateur :

1. Ouvrez l’éditeur de stratégie de groupe (GPEDIT. (MSC). (À partir d’une invite de commande, exécutez gpedit.msc.)
2. Ouvrez **ordinateur Configuration utilisateur\Stratégies\Modèles d ' administration\Composants Windows\Services Bureau distance\Hôte connexion Bureau Client\RemoteFX USB la Redirection de périphérique**.
3. Double-cliquez sur **la redirection de RDP de permettre à d’autres périphériques USB de RemoteFX pris en charge à partir de cet ordinateur**.
4. Sélectionnez **activé**et puis sélectionnez **administrateurs et utilisateurs dans les droits d’accès de RemoteFX USB la Redirection**.
5. Ouvrez une invite de commandes avec des autorisations administratives et exécutez la commande suivante :

        gpupdate /force
6. Redémarrez l’ordinateur.

Vous pouvez également utiliser l’outil de gestion de stratégie de groupe pour créer et appliquer la stratégie de redirection USB pour tous les ordinateurs de votre domaine :

1. Ouvrez une session en tant qu’administrateur de domaine en contrôleur de domaine.
2. Ouvrez la Console Gestion de stratégie de groupe. (Cliquez sur **Démarrer > Outils d’administration > Gestion de la stratégie de groupe**.)
3. Accédez au domaine ou à une unité d’organisation pour lequel vous souhaitez créer la stratégie.
4. Cliquez droit sur la **Stratégie de domaine par défaut**, puis cliquez sur **Modifier**.
5. Ouvrez **ordinateur Configuration utilisateur\Stratégies\Modèles d ' administration\Composants Windows\Services Bureau distance\Hôte connexion Bureau Client\RemoteFX USB la Redirection de périphérique**.
6. Double-cliquez sur **la redirection de RDP de permettre à d’autres périphériques USB de RemoteFX pris en charge à partir de cet ordinateur**.
7. Sélectionnez **activé**et puis sélectionnez **administrateurs et utilisateurs dans les droits d’accès de RemoteFX USB la Redirection**.
8. Cliquez sur **OK**.  
