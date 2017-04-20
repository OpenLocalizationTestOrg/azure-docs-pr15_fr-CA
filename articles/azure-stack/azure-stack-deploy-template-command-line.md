<properties
    pageTitle="Déployer des modèles de la ligne de commande dans la pile d’Azure | Microsoft Azure"
    description="Apprenez à utiliser l’interface de ligne de commande (CLI) entre les plates-formes pour déployer des modèles d’à l’intérieur de la ClientVM ou après l’utilisation de la connexion VPN pour se connecter à la pile d’Azure."
    services="azure-stack"
    documentationCenter=""
    authors="heathl17"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="helaw"/>

# <a name="deploy-templates-in-azure-stack-using-the-command-line"></a>Déployer des modèles de pile Azure à l’aide de la ligne de commande

Utilisez la ligne de commande pour déployer des modèles de Azure le Gestionnaire de ressources pour le contrôle du concept pile Azure. Azure modèles du Gestionnaire de ressources déploiement et configurer toutes les ressources de votre application dans une opération unique et coordonnée.

## <a name="download-template"></a>Télécharger le modèle        
Pour tester un déploiement avec l’interface CLI, téléchargez les fichiers azuredeploy.json et azuredeploy.parameters.json à partir de l' [exemple de modèle de stockage compte de créer](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-create-storage-account).

## <a name="deploy-template"></a>Déployer le modèle
Accédez au dossier où ces fichiers ont été téléchargés et exécutez la commande suivante pour déployer le modèle :

    azure group create "cliRG" "local" –f azuredeploy.json –d "testDeploy" –e azuredeploy.parameters.json

Cette commande déploie le modèle vers le groupe de ressource **cliRG** dans l’emplacement par défaut de la VT pile Azure.

## <a name="validate-template-deployment"></a>Valider le déploiement du modèle
Pour voir ce compte de groupe et de stockage des ressources, utilisez les commandes suivantes :

    azure group list

    azure storage account list

## <a name="next-steps"></a>Étapes suivantes

[Gérer les autorisations utilisateur](azure-stack-manage-permissions.md)
