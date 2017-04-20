<properties
    pageTitle="Déployer un ordinateur virtuel à l’aide d’un mot de passe stocké dans la clé de chambre forte de la pile Azure | Microsoft Azure"
    description="Découvrez comment déployer un ordinateur virtuel à l’aide d’un mot de passe stocké dans la clé de chambre forte de la pile Azure"
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

# <a name="deploy-a-vm-by-retrieving-the-password-stored-in-key-vault"></a>Déployer un ordinateur virtuel en récupérant le mot de passe stocké dans le coffre-fort de la clé

Lorsque vous devez passer une valeur sécurisée (par exemple, un mot de passe) en tant que paramètre au cours du déploiement, vous pouvez stocker cette valeur sous la forme d’un secret dans une chambre forte de clé Azure pile et référencer la valeur dans d’autres modèles d’Azure le Gestionnaire de ressources. Vous n'incluez qu’une référence aux données secrètes dans votre modèle, afin que le mot de passe n’est jamais exposée. Vous n’avez pas besoin d’entrer manuellement la valeur pour le mot de passe chaque fois que vous déployez les ressources. Vous spécifiez les utilisateurs ou les entités de service accès au secret.

## <a name="reference-a-secret-with-static-id"></a>Faire référence à un secret dont l’ID est statique

Vous référencez le secret à partir d’un fichier de paramètres, qui transmet les valeurs à votre modèle. Vous référencez le secret en passant l’identificateur de ressource de la chambre forte de clé et le nom de la clé secrète. Dans cet exemple, le secret de la chambre forte de clé doit déjà exister. Vous utilisez une valeur statique pour son ID de ressource.

    "parameters": {
    "adminPassword": {
    "reference": {
    "keyVault": {
    "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
    },
    "secretName": "sqlAdminPassword"


>[AZURE.NOTE]Le paramètre qui accepte le secret doit être une *chaîne securestring*.

## <a name="next-steps"></a>Étapes suivantes
[Déployer un exemple d’application avec clé de coffre-fort](azure-stack-kv-sample-app.md)

[Déployer un ordinateur virtuel avec un certificat de clé coffre-fort](azure-stack-kv-push-secret-into-vm.md)

