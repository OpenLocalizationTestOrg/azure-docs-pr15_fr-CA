<properties
pageTitle="Activer la connexion Bureau à distance pour un rôle dans les Services en nuage Azure à l’aide de PowerShell"
description="Comment faire pour configurer votre application de service de cloud azure à l’aide de PowerShell pour autoriser les connexions Bureau à distance"
services="cloud-services"
documentationCenter=""
authors="thraka"
manager="timlt"
editor=""/>
<tags
ms.service="cloud-services"
ms.workload="tbd"
ms.tgt_pltfrm="na"
ms.devlang="na"
ms.topic="article"
ms.date="08/05/2016"
ms.author="adegeo"/>

# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services-using-powershell"></a>Activer la connexion Bureau à distance pour un rôle dans les Services en nuage Azure à l’aide de PowerShell

>[AZURE.SELECTOR]
- [Azure portal classique](cloud-services-role-enable-remote-desktop.md)
- [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
- [Visual Studio](../vs-azure-tools-remote-desktop-roles.md)


Bureau à distance vous permet d’accéder au bureau d’un rôle qui exécute dans Azure. Vous pouvez utiliser une connexion Bureau à distance pour dépanner et diagnostiquer les problèmes avec votre application pendant son exécution.

Cet article décrit comment faire pour activer le Bureau à distance sur vos rôles de Service Cloud à l’aide de PowerShell. Pour la configuration requise pour cet article, reportez-vous à la section [comment installer et configurer Azure PowerShell](../powershell-install-configure.md) . PowerShell utilise l’Extension de bureau à distance afin de l’activer Bureau à distance une fois que l’application est déployée.


## <a name="configure-remote-desktop-from-powershell"></a>Configurer le Bureau à distance à partir de PowerShell

L’applet de commande [Set-AzureServiceRemoteDesktopExtension](https://msdn.microsoft.com/library/azure/dn495117.aspx) permet d’activer le Bureau à distance sur les rôles spécifiés ou tous les rôles de déploiement de votre service cloud. L’applet de commande vous permet de spécifier le nom d’utilisateur et le mot de passe de l’utilisateur de bureau distant via le paramètre *d’informations d’identification* qui accepte un objet PSCredential.

Si vous utilisez PowerShell de façon interactive, vous pouvez facilement définir l’objet PSCredential en appelant l’applet de commande [Get-informations d’identification](https://technet.microsoft.com/library/hh849815.aspx) .

```
$remoteusercredentials = Get-Credential
```

Cette commande affiche une boîte de dialogue vous permettant d’entrer le nom d’utilisateur et le mot de passe de l’utilisateur à distance de manière sécurisée.

Étant donné que les scénarios d’automatisation permet de PowerShell, vous pouvez également définir l’objet **PSCredential** d’une façon qui ne nécessite aucune intervention de l’utilisateur. Tout d’abord, vous devez définir un mot de passe sécurisé. Vous commencez avec la spécification d’un mot de passe en texte brut convertir en une chaîne sécurisée à l’aide de [ConvertTo-SecureString](https://technet.microsoft.com/library/hh849818.aspx). Vous devez ensuite convertir cette chaîne sécurisée en chaîne chiffrée standard à l’aide de [ConvertFrom-SecureString](https://technet.microsoft.com/library/hh849814.aspx). Vous pouvez désormais enregistrer cette chaîne standard cryptée dans un fichier à l’aide du [Contenu de l’ensemble](https://technet.microsoft.com/library/ee176959.aspx).

Vous pouvez également créer un fichier de mot de passe sécurisé afin que vous n’êtes pas obligé de taper le mot de passe à chaque fois. En outre, un fichier de mot de passe sécurisé est préférable à un fichier de texte brut. Utilisez PowerShell suivante pour créer un fichier de mot de passe sécurisé :

```
ConvertTo-SecureString -String "Password123" -AsPlainText -Force | ConvertFrom-SecureString | Set-Content "password.txt"
```

>[AZURE.IMPORTANT] Lorsque vous définissez le mot de passe, assurez-vous que vous remplissez les [exigences de complexité](https://technet.microsoft.com/library/cc786468.aspx).

Pour créer l’objet d’informations d’identification à partir du fichier de mot de passe sécurisé, vous devez lire le contenu du fichier et les convertir en une chaîne sécurisée à l’aide de [ConvertTo-SecureString](https://technet.microsoft.com/library/hh849818.aspx).

L’applet de commande [Set-AzureServiceRemoteDesktopExtension](https://msdn.microsoft.com/library/azure/dn495117.aspx) accepte également un paramètre *d’Expiration* , ce qui indique un **DateTime** à partir duquel le compte d’utilisateur expire. Par exemple, vous pouvez définir le compte à quelques jours à compter de la date et l’heure d’expiration.

Cet exemple montre comment PowerShell vous montre comment définir l’Extension de bureau à distance sur un service en nuage :

```
$servicename = "cloudservice"
$username = "RemoteDesktopUser"
$securepassword = Get-Content -Path "password.txt" | ConvertTo-SecureString
$expiry = $(Get-Date).AddDays(1)
$credential = New-Object System.Management.Automation.PSCredential $username,$securepassword
Set-AzureServiceRemoteDesktopExtension -ServiceName $servicename -Credential $credential -Expiration $expiry
```
Vous pouvez également spécifier l’emplacement de déploiement et les rôles que vous souhaitez activer le Bureau à distance. Si ces paramètres ne sont pas spécifiés, l’applet de commande permet de bureau à distance sur tous les rôles dans l’emplacement de déploiement de **Production** .

L’extension du Bureau à distance est associée à un déploiement. Si vous créez un nouveau déploiement pour le service, vous devez activer le Bureau à distance sur ce déploiement. Si vous souhaitez toujours avoir le Bureau à distance activée, vous devez envisager intégrant des scripts PowerShell dans votre workflow de déploiement.


## <a name="remote-desktop-into-a-role-instance"></a>Bureau à distance dans une instance de rôle
L’applet de commande [Get-AzureRemoteDesktopFile](https://msdn.microsoft.com/library/azure/dn495261.aspx) est utilisé pour le Bureau à distance dans une instance de rôle spécifique de votre service cloud. Vous pouvez utiliser le paramètre *LocalPath* pour télécharger le fichier RDP localement. Ou vous pouvez utiliser le paramètre *de lancement* pour lancer directement de la boîte de dialogue Connexion Bureau à distance pour accéder à l’instance de rôle de service cloud.

```
Get-AzureRemoteDesktopFile -ServiceName $servicename -Name "WorkerRole1_IN_0" -Launch
```


## <a name="check-if-remote-desktop-extension-is-enabled-on-a-service"></a>Si l’extension du Bureau à distance est activée sur un service
L’applet de commande [Get-AzureServiceRemoteDesktopExtension](https://msdn.microsoft.com/library/azure/dn495261.aspx) affiche que Bureau à distance est activé ou désactivé sur un déploiement de service. L’applet de commande renvoie le nom d’utilisateur pour l’utilisateur de bureau à distance et l’extension du Bureau à distance est activée pour les rôles. Par défaut, cela se produit sur l’emplacement de déploiement, et vous pouvez choisir d’utiliser à la place de l’emplacement intermédiaire.

```
Get-AzureServiceRemoteDesktopExtension -ServiceName $servicename
```

## <a name="remove-remote-desktop-extension-from-a-service"></a>Supprimer l’extension du Bureau à distance à partir d’un service
Si vous avez déjà activé de l’extension du Bureau à distance sur un déploiement et que vous devez mettre à jour les paramètres du Bureau à distance, d’abord supprimer l’extension. Et activez de nouveau avec les nouveaux paramètres. Par exemple, si vous souhaitez définir un nouveau mot de passe pour le compte d’utilisateur distant ou si le compte a expiré. Cette opération est requise sur les déploiements existants qui portent l’extension de bureau à distance activée. Pour les nouveaux déploiements, vous pouvez simplement appliquer l’extension directement.

Pour supprimer l’extension du Bureau à distance à partir du déploiement, vous pouvez utiliser l’applet de commande [Remove-AzureServiceRemoteDesktopExtension](https://msdn.microsoft.com/library/azure/dn495280.aspx) . Vous pouvez également spécifier l’emplacement de déploiement et le rôle à partir de laquelle vous souhaitez supprimer l’extension du Bureau à distance.

```
Remove-AzureServiceRemoteDesktopExtension -ServiceName $servicename -UninstallConfiguration
```

>[AZURE.NOTE] Pour supprimer complètement la configuration de l’extension, vous devez appeler l’applet de commande *Supprimer* avec le paramètre **UninstallConfiguration** .
>
>Le paramètre **UninstallConfiguration** désinstalle toute configuration de l’extension qui s’applique au service. Chaque configuration de l’extension est associée à la configuration du service. L’appel de l’applet de commande *Supprimer* sans **UninstallConfiguration** dissocie le <mark>déploiement</mark> de la configuration de l’extension, supprimant ainsi efficacement l’extension. Toutefois, la configuration de l’extension reste associée au service.



## <a name="additional-resources"></a>Ressources supplémentaires

[Comment faire pour configurer les Services en nuage](cloud-services-how-to-configure.md)
