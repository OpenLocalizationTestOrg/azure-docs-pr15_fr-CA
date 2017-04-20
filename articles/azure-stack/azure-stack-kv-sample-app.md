<properties
    pageTitle="Autoriser les applications à secrets d’Azure pile clé coffre-fort revtrieve | Microsoft Azure"
    description="Utiliser un exemple d’application avec clé de chambre forte de la pile Azure"
    services="azure-stack"
    documentationCenter=""
    authors="rlfmendes"
    manager="natmack"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/26/2016"
    ms.author="ricardom"/>

# <a name="run-the-sample-application-for-key-vault"></a>Exécutez l’exemple d’application pour le stockage en chambre forte de clé 

Dans ce guide, vous allez utiliser un exemple d’application pour récupérer des secrets et des mots de passe à partir de la chambre forte de clé.

## <a name="download-the-samples-and-prepare"></a>Téléchargez les exemples et les préparer

Téléchargez les exemples de clients de Azure clé coffre-fort à partir de la [page des exemples client Azure clé coffre-fort](https://www.microsoft.com/en-us/download/details.aspx?id=45343).

Extrayez le contenu du fichier zip sur votre ordinateur local.

Lire le fichier **README.md** (il s’agit d’un fichier texte), puis suivez les instructions.

## <a name="run-sample-1--hellokeyvault"></a>Exécuter l’exemple #1--HelloKeyVault
HelloKeyVault est une application console qui vous guide à travers les principaux scénarios pris en charge par le stockage en chambre forte de clé :

  1. Créer/importation d’une clé (logiciel ou HSM)
  2. Crypter un secret à l’aide d’une clé de contenu
  3. Retour à la ligne de la clé de contenu à l’aide d’une clé de la chambre forte de clé
  4. Désencapsuler la clé de contenu
  5. Décrypter le secret
  6. Définir un secret

Cette application de console doit s’exécuter sans apporter de modifications, sauf que les paramètres de configuration appropriés dans App.Config mis à jour en suivant ces étapes :

1. Mettre à jour les paramètres de configuration d’application dans HelloKeyVault\App.config votre URL de coffre-fort, ID principal de l’application et secret. Les informations peuvent également être générées à l’aide de **scripts\GetAppConfigSettings.ps1**.
2. Mettre à jour les valeurs des variables obligatoires dans GetAppConfigSettings.ps1.
3. Lancer la fenêtre de Windows PowerShell.
4. Dans la fenêtre PowerShell, exécutez le script GetAppConfigSettings.ps1.
5. Copier les résultats du script dans le fichier HelloKeyVault\App.config.


## <a name="next-steps"></a>Étapes suivantes

[Déployer un ordinateur virtuel avec un mot de passe du coffre-fort de clé](azure-stack-kv-deploy-vm-with-secret.md)

[Déployer un ordinateur virtuel avec un certificat de clé coffre-fort](azure-stack-kv-push-secret-into-vm.md)