<properties
 pageTitle="Créer votre concentrateur IoT et enregistrer votre framboises Pi 3 | Microsoft Azure"
 description="Créer un groupe de ressources, créez un concentrateur Azure IoT et enregistrer votre Pi dans le concentrateur Azure IoT à l’aide de la CLI d’Azure."
 services="iot-hub"
 documentationCenter=""
 authors="shizn"
 manager="timlt"
 tags=""
 keywords=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="10/21/2016"
 ms.author="xshi"/>

# <a name="22-create-your-iot-hub-and-register-your-raspberry-pi-3"></a>2.2 créer votre concentrateur IoT et enregistrer votre framboises Pi 3

## <a name="221-what-you-will-do"></a>2.2.1 ce que vous ferez

- Créer un groupe de ressources.
- Créez votre concentrateur Azure IoT dans le groupe de ressources.
- Ajoutez vos framboises Pi 3 au concentrateur Azure IoT à l’aide de la CLI d’Azure.

Lorsque vous utilisez la CLI Azure pour ajouter votre Pi à votre concentrateur IoT, le service génère une clé pour votre Pi pour s’authentifier auprès du service. Si vous répondez à tous les problèmes, rechercher des solutions dans la [page Dépannage](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="222-what-you-will-learn"></a>2.2.2 vous apprendrez

- L’utilisation de l’infrastructure du langage commun Azure pour créer un concentrateur de IoT Azure.
- Procédure de création d’une identité de périphérique pour votre Pi dans Azure IoT concentrateur.

## <a name="223-what-you-need"></a>2.2.3 ce dont vous avez besoin

- Un compte Azure
- Un Mac ou un ordinateur Windows avec la CLI d’Azure installé

## <a name="224-create-your-azure-iot-hub"></a>2.2.4 créer votre concentrateur Azure IoT

Azure IoT Hub vous permet de vous connecter, de surveiller et de gérer des millions de IoT actifs. Pour créer votre concentrateur Azure IoT, procédez comme suit :

1. Connectez-vous à votre compte Azure en exécutant la commande suivante :

    ```bash
    az login
    ```

    Tous vos abonnements Azure disponibles sont répertoriés après une connexion réussie.

2. Définir la valeur par défaut Azure abonnement que vous souhaitez utiliser en exécutant la commande suivante :

    ```bash
    az account set -n {subscription id or name}
    ```

    Le nom ou l’ID d’abonnement peut être trouvé dans la sortie de `az login`.

3. Enregistrer le fournisseur en exécutant la commande suivante :

    ```bash
    az resource provider register -n "Microsoft.Devices"
    ```

    Vous devez inscrire le fournisseur avant de pouvoir déployer la ressource Azure que le fournisseur fournit.

    > [AZURE.NOTE] La plupart des fournisseurs sont enregistrées automatiquement par le portail Azure ou l’interface CLI Azure que vous utilisez, mais pas toutes. Pour plus d’informations sur le fournisseur, consultez [résolution des problèmes courants Azure erreurs de déploiement avec le Gestionnaire de ressources Azure](../resource-manager-common-deployment-errors.md)

4. Créer un groupe de ressources nommé iot-sample dans la région Ouest américaine en exécutant la commande suivante :

    ```bash
    az resource group create --name iot-sample --location westus
    ```

5. Créer un concentrateur IoT dans le groupe de ressources d’iot-exemple en exécutant la commande suivante :

    ```bash
    az iot hub create --name {my hub name} --resource-group iot-sample
    ```

    L’édition par défaut du concentrateur IoT que vous créez **F1**, est **libre**. Pour plus d’informations, consultez [tarification d’Azure IoT concentrateur](https://azure.microsoft.com/pricing/details/iot-hub/).

> [AZURE.NOTE] Le nom de votre concentrateur IoT doit être globalement unique.
>
> Vous pouvez créer qu’une seule édition **F1** d’Azure IoT concentrateur sous votre abonnement Azure.

## <a name="225-register-your-pi-in-your-iot-hub"></a>2.2.5 inscrire votre Pi dans votre concentrateur IoT

Chaque périphérique envoie/reçoit des messages vers/depuis votre concentrateur Azure IoT doit être enregistré avec un ID unique.

Enregistrer votre Pi dans votre concentrateur Azure IoT par l’exécution de la commande suivante :

```bash
az iot device create --device-id myraspberrypi --hub {my hub name} --resource-group iot-sample
```

## <a name="226-summary"></a>2.2.6 résumé

Vous avez créé un concentrateur Azure IoT et enregistré votre Pi avec une identité de périphérique dans votre concentrateur IoT d’Azure. Vous êtes prêt à passer à la leçon suivante pour apprendre à envoyer des messages à partir de votre Pi à votre concentrateur IoT.

## <a name="next-steps"></a>Étapes suivantes

Vous êtes maintenant prêt à commencer la leçon 3 qui commence avec la [Création d’une application de fonction Azure et un compte de stockage Azure pour traiter et stocker les messages de concentrateur IoT](iot-hub-raspberry-pi-kit-node-lesson3-deploy-resource-manager-template.md).