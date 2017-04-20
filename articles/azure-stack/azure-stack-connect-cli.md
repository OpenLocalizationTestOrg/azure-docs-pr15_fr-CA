<properties
    pageTitle="Se connecter à la pile Azure avec CLI | Microsoft Azure"
    description="Apprenez à utiliser l’interface de ligne de commande (CLI) multiplates-formes pour gérer et déployer des ressources dans la pile d’Azure"
    services="azure-stack"
    documentationCenter=""
    authors="HeathL17"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="helaw"/>

# <a name="install-and-configure-azure-stack-cli"></a>Installer et configurer la CLI de pile Azure

Dans ce document, nous vous guide dans le processus d’utilisation d’Azure Interface de ligne de commande (CLI) pour gérer les ressources de pile d’Azure sur Linux et Mac client.  

## <a name="install-azure-stack-cli"></a>Installation de pile Azure CLI

Si vous êtes sous Mac ou Linux, vous pouvez obtenir l’interface CLI à l’aide de la commande suivante :
  
    `npm install -g azure-cli@0.10.4`.


## <a name="connect-to-azure-stack"></a>Se connecter à la pile Azure
Dans les étapes suivantes, vous configurez CLI Azure pour se connecter à la pile d’Azure. Puis vous connectez et récupérer les informations d’abonnement.

1.  Récupérer la valeur d’active-directory-resource-id en exécutant cette PowerShell :
        
          (Invoke-RestMethod -Uri https://api.azurestack.local/metadata/endpoints?api-version=1.0 -Method Get).authentication.audiences[0]

2.  La commande CLI suivante permet d’ajouter de l’environnement de pile d’Azure, en veillant à mettre à jour *--active-directory-resource-id* avec les données de l’URL est extraite à l’étape précédente :

           azure account env add AzureStack --resource-manager-endpoint-url "https://api.azurestack.local" --management-endpoint-url "https://api.azurestack.local" --active-directory-endpoint-url  "https://login.windows.net" --portal-url "https://portal.azurestack.local" --gallery-endpoint-url "https://portal.azurestack.local" --active-directory-resource-id "https://azurestack.local-api/" --active-directory-graph-resource-id "https://graph.windows.net/"

3.  Connectez-vous à l’aide de la commande suivante (remplacez *nom d’utilisateur* avec votre nom d’utilisateur) :

        azure login -e AzureStack -u “<username>”

    >[AZURE.NOTE]Si vous obtenez les problèmes de validation de certificat, désactiver la validation de certificat en exécutant la commande `set        NODE_TLS_REJECT_UNAUTHORIZED=0`.

4.  Définir le mode de configuration Azure pour le Gestionnaire de ressources Azure à l’aide de la commande suivante :

        azure config mode arm

5.  Extraire la liste des abonnements.

        azure account list     

## <a name="next-steps"></a>Étapes suivantes

[Déployer des modèles avec Azure CLI](azure-stack-deploy-template-command-line.md)

[Se connecter avec PowerShell](azure-stack-connect-powershell.md)

[Gérer les autorisations utilisateur](azure-stack-manage-permissions.md)
