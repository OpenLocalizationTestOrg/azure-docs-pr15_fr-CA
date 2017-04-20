<properties 
    pageTitle="Planifier un déploiement de service cloud (Node.js) | Microsoft Azure" 
    description="Apprenez à déployer votre application Azure dans un environnement de test, puis le déployer dans un environnement de production à l’aide de remplacement de l’adresse IP virtuelle (VIP)." 
    services="cloud-services" 
    documentationCenter="nodejs" 
    authors="rmcmurray" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="cloud-services" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="nodejs" 
    ms.topic="article" 
    ms.date="08/11/2016" 
    ms.author="robmcm"/>



# <a name="staging-an-application-in-azure"></a>Mise en attente d’une Application dans Azure

Une application peut être déployée dans l’environnement intermédiaire dans Azure à tester avant de le déplacer à l’environnement de production dans lequel l’application est accessible sur Internet. L’environnement intermédiaire est identique à l’environnement de production, sauf que vous ne pouvez accéder à l’application intermédiaire avec une URL masquée qui est générée par Azure. Après avoir vérifié que votre application fonctionne correctement, il peut être déployé dans l’environnement de production en exécutant un swap de l’adresse IP virtuelle (VIP).

> [AZURE.NOTE] Les étapes décrites dans cet article s’appliquent uniquement aux applications de nœud hébergées comme un Service en nuage Azure.

## <a name="step-1-stage-an-application"></a>Étape 1 : Préparer une Application

Cette tâche décrit comment préparer une application à l’aide de **Microsoft Azure PowerShell**.

1.  Lors de la publication d’un service, il suffit de transmettre le **-emplacement** paramètre à l’applet de commande **Publier-AzureServiceProject** .

    ```powershell
    Publish-AzureServiceProject -Slot staging
    ```

2.  Ouvrez une session sur [Azure portal classique] et sélectionnez **Services dans le nuage**. Une fois le service cloud est créé et l’état de la colonne **mise en attente** a été mis à jour en cours **d’exécution**, cliquez sur le nom du service.

    ![affichage d’un service de portail][cloud-service]

3.  Sélectionnez le **tableau de bord**et sélectionnez **mise en attente**.

    ![tableau de bord de service cloud][cloud-service-dashboard]

4. Notez la valeur de l’entrée de **l’URL du Site** vers la droite. Le nom DNS est un ID interne obscurci Azure généré.

    ![url du site][cloud-service-staging-url]

Vous pouvez maintenant vérifier que l’application fonctionne correctement dans l’environnement intermédiaire à l’aide de l’URL du site intermédiaire.

## <a name="step-2-upgrade-an-application-in-production-by-swapping-vips"></a>Étape 2 : Mettre à niveau une Application en Production par échange VIP

Après avoir vérifié la version mise à niveau d’une application dans l’environnement intermédiaire, vous pouvez rapidement en faire disponible en production en échangeant l’IPs virtuel (VIPs) des environnements de test et de production.

> [AZURE.NOTE] Cette étape suppose que vous avez déjà déployé une application de production et mis en place de la version de mise à niveau de l’application.

1.  Connecter le [Azure portal classique], cliquez sur **Services dans le nuage** et puis sélectionnez le nom du service.

2.  Du tableau de **bord**, sélectionnez **Staging**, puis cliquez sur **Permuter** au bas de la page. Cela ouvre la boîte de dialogue VIP Swap.

    ![boîte de dialogue de swap VIP][vip-swap-dialog]

3.  Passez en revue les informations, puis cliquez sur **OK**. Les deux déploiements commencent la mise à jour alors que le déploiement intermédiaire des commutateurs à la production et le déploiement en production passe intermédiaire.

Vous avez correctement un déploiement par étapes et mise à niveau d’un déploiement de production en permutant les VIP avec le déploiement de reclassement.

## <a name="additional-resources"></a>Ressources supplémentaires

- [Comment déployer une mise à niveau de Service à la Production en permutant les VIP dans Azure]

[Azure portal classique]: http://manage.windowsazure.com
[cloud-service]: ./media/cloud-services-nodejs-stage-application/staging-cloud-service-running.png
[cloud-service-dashboard]: ./media/cloud-services-nodejs-stage-application/cloud-service-dashboard-staging.png
[cloud-service-staging-url]: ./media/cloud-services-nodejs-stage-application/cloud-service-staging-url.png
[vip-swap-dialog]: ./media/cloud-services-nodejs-stage-application/vip-swap-dialog.png
[Comment déployer une mise à niveau de Service à la Production en permutant les VIP dans Azure]: cloud-services-how-to-manage.md#how-to-swap-deployments-to-promote-a-staged-deployment-to-production
