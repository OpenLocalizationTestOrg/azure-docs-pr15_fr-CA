<properties
   pageTitle="Publier des applications à des utilisateurs individuels d’une collection de RemoteApp d’Azure (aperçu) | Microsoft Azure"
   description="Découvrez comment publier des applications à des utilisateurs individuels, et non en fonction des groupes, dans Azure RemoteApp."
   services="remoteapp-preview"
   documentationCenter=""
   authors="piotrci"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="08/15/2016"
   ms.author="piotrci"/>

# <a name="publish-applications-to-individual-users-in-an-azure-remoteapp-collection-preview"></a>Publier des applications à des utilisateurs individuels d’une collection de RemoteApp d’Azure (aperçu)

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lecture de l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus de détails.

Cet article explique comment publier des applications à des utilisateurs individuels dans une collection Azure RemoteApp. Ceci est une nouvelle fonctionnalité dans Azure RemoteApp, actuellement dans « Aperçu privée » et disponible uniquement pour sélectionner les premiers utilisateurs à des fins d’évaluation.

À l’origine Azure RemoteApp n'activé qu’une seule manière de « publication » des applications : l’administrateur serait publier des applications à partir de l’image et ils ne seraient pas visibles à tous les utilisateurs dans la collection.

Un scénario courant consiste à inclure un grand nombre d’applications dans une seule image et de déployer une collection afin de réduire les coûts de gestion. Souvent pas toutes les applications soient appliquent à tous les utilisateurs, administrateurs préfèrent publier des applications à des utilisateurs individuels afin qu’ils ne voyez pas les applications inutiles dans leur application d’alimentation.

Cela est désormais possible de RemoteApp d’Azure – actuellement sous la forme d’une fonction d’aperçu limité. Voici un bref résumé des nouvelles fonctionnalités :

1. Une collection peut être définie dans un des deux modes :
 
  - l’original du « mode de collection », où une collection de tous les utilisateurs peuvent voir toutes les applications publiées. C’est le mode par défaut.
  - la nouvelle « application mode », où les utilisateurs ne voient que des applications qui ont été explicitement affectées

2. Au moment où le mode d’application ne peut être activé à l’aide des applets de commande PowerShell de RemoteApp Azure.

  - Lorsque vous définissez le mode d’application, affectation d’utilisateur dans la collection ne peut être gérée via le portail Azure. Affectation de l’utilisateur doit être géré via les applets de commande PowerShell.

3. Les utilisateurs voient uniquement les applications publiées directement. Toutefois, il peut être encore possible pour un utilisateur lancer d’autres applications disponibles sur l’image en y accédant directement dans le système d’exploitation.
  - Cette fonction ne fournit pas un verrouillage sécurisé d’applications ; Il limite uniquement la visibilité dans l’application de flux.
  - Si vous avez besoin isoler les utilisateurs à partir d’applications, vous devez utiliser des collections séparées pour que.

## <a name="how-to-get-azure-remoteapp-powershell-cmdlets"></a>Comment obtenir des applets de commande PowerShell de RemoteApp Azure

Pour tester la nouvelle fonctionnalité d’aperçu, vous devrez utiliser les applets de commande PowerShell d’Azure. Actuellement, il n’est pas possible d’utiliser le portail de gestion d’Azure pour activer le nouveau mode de publication d’application.

Tout d’abord, assurez-vous que vous avez installé le [module PowerShell de Azure](../powershell-install-configure.md) .

Lancer la console PowerShell en mode administrateur, puis exécutez l’applet de commande suivante :

        Add-AzureAccount

Il vous demandera votre nom d’utilisateur d’Azure et d’un mot de passe. Une fois connecté, vous serez en mesure d’exécuter des applets de commande Azure RemoteApp contre vos abonnements Azure.

## <a name="how-to-check-which-mode-a-collection-is-in"></a>Comment faire pour vérifier le mode une collection est dans

Exécutez l’applet de commande suivante :

        Get-AzureRemoteAppCollection <collectionName>

![Vérifier le mode de collection](./media/remoteapp-perapp/araacllelvel.png)

La propriété AclLevel peut avoir les valeurs suivantes :

- Collection : de la mode publication d’origine. Tous les utilisateurs voient que tous publiés des applications.
- Application : le nouveau mode de publication. Les utilisateurs voient uniquement les applications publiées directement.

## <a name="how-to-switch-to-application-publishing-mode"></a>Comment passer en mode de publication d’application

Exécutez l’applet de commande suivante :

        Set-AzureRemoteAppCollection -CollectionName -AclLevel Application

État de publication application est conservé : initialement tous les utilisateurs verront à toutes les applications publiées d’origine.

## <a name="how-to-list-users-who-can-see-a-specific-application"></a>La liste des utilisateurs qui peuvent voir une application spécifique

Exécutez l’applet de commande suivante :

        Get-AzureRemoteAppUser -CollectionName <collectionName> -Alias <appAlias>

Répertorie tous les utilisateurs qui peuvent voir l’application.

Remarque : Vous pouvez visualiser les alias de l’application (appelés le « alias app » dans la syntaxe ci-dessus) en exécutant Get-AzureRemoteAppProgram - CollectionName <collectionName>.

## <a name="how-to-assign-an-application-to-a-user"></a>Comment attribuer une application à un utilisateur

Exécutez l’applet de commande suivante :

        Add-AzureRemoteAppUser -CollectionName <collectionName> -UserUpn <user@domain.com> -Type <OrgId|MicrosoftAccount> -Alias <appAlias>

L’utilisateur voit à présent l’application dans le client Azure RemoteApp et sera en mesure de s’y connecter.

## <a name="how-to-remove-an-application-from-a-user"></a>Comment faire pour supprimer une application d’un utilisateur

Exécutez l’applet de commande suivante :

        Remove-AzureRemoteAppUser -CollectionName <collectionName> -UserUpn <user@domain.com> -Type <OrgId|MicrosoftAccount> -Alias <appAlias>

## <a name="providing-feedback"></a>Envoi de commentaires
Nous apprécions vos commentaires et suggestions concernant cette fonctionnalité d’aperçu. Veuillez remplir l' [enquête](http://www.instant.ly/s/FDdrb) pour nous dire ce que vous en pensez.

## <a name="havent-had-a-chance-to-try-the-preview-feature"></a>N’ont pas eu l’occasion d’essayer la fonction d’aperçu ?
Si vous n’avez pas encore pratiqués dans l’aperçu, utilisez cette [enquête](http://www.instant.ly/s/AY83p) pour demander l’accès.
