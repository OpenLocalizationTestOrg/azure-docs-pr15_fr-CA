<properties
   pageTitle="Configurer PowerShell pour créer un ordinateur virtuel pour le marché | Microsoft Azure"
   description="Instructions pour la configuration Azure PowerShell et l’utiliser comme un processus facultatif de flux pour créer des images de machine virtuelle pour déployer sur et vendent sur le marché d’Azure"
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager="hascipio"
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/04/2016"
   ms.author="hascipio"/>

# <a name="set-up-azure-powershell-to-create-an-offer-for-the-azure-marketplace"></a>Configurer Azure PowerShell pour créer une offre à Azure Marketplace
Pour obtenir des informations détaillées sur la procédure de paramétrage de PowerShell dans Azure, voir [Comment faire pour installer et configurer Azure PowerShell](../powershell-install-configure.md). Une approche simple consiste à utiliser la méthode de certificat, qui télécharge et importe un certificat nécessaire pour l’authentification. Pour obtenir le certificat requis, utilisez l’applet de commande **Get-AzurePublishSettingsFile** . Enregistrez le fichier lorsque vous y êtes invité. Pour importer le certificat dans une session PowerShell, utilisez la cmdlet **Import-AzurePublishSettingsFile** .

Pour configurer et stocker les paramètres d’abonnement Microsoft Azure courantes pour la session de PowerShell, utiliser les applets de commande **Set-AzureSubscription** et **Sélectionnez-AzureSubscription** :

        Set-AzureSubscription -SubscriptionName “mySubName” -CurrentStorageAccountName “mystorageaccount”
        Select-AzureSubscription -SubscriptionName "mySubName" –Current

La première commande associe un compte de stockage par défaut à l’abonnement (nécessaire pour certaines opérations de configuration de machine virtuelle).  La seconde permet à l’abonnement l’actuel (reconnu par les autres applets de commande).

## <a name="see-also"></a>Voir aussi
- [Mise en route : comment publier une offre sur le marché d’Azure](marketplace-publishing-getting-started.md)
- [Création d’une image de machine virtuelle pour le marché](marketplace-publishing-vm-image-creation.md)
