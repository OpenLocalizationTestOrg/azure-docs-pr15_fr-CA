<properties 
   pageTitle="Déployer un ordinateur virtuel avec une adresse IP statique publique via le portail Azure dans le Gestionnaire de ressources | Microsoft Azure"
   description="Apprenez à déployer des ordinateurs virtuels avec une adresse IP statique publique à l’aide du portail de zure dans le Gestionnaire de ressources"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/04/2016"
   ms.author="jdial" />

# <a name="deploy-a-vm-with-a-static-public-ip-using-the-azure-portal"></a>Déployer un ordinateur virtuel avec une adresse IP statique publique via le portail Azure

[AZURE.INCLUDE [virtual-network-deploy-static-pip-arm-selectors-include.md](../../includes/virtual-network-deploy-static-pip-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]modèle de déploiement classique.

[AZURE.INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

## <a name="create-a-vm-with-a-static-public-ip"></a>Créer un ordinateur virtuel avec un IP publique statique 

Pour créer un ordinateur virtuel avec une adresse IP publique statique dans Azure portal, suivez les étapes ci-dessous.

1. À partir d’un navigateur, accédez au [portail Azure](https://portal.azure.com) et, si nécessaire, connectez-vous à votre compte Azure.
2. Dans le coin supérieur gauche du portail, cliquez sur **Nouveau**>>**Calculer**>**Windows Server 2012 R2 Datacenter**.
3. Dans la liste **Sélectionnez un modèle de déploiement** , sélectionnez **Le Gestionnaire de ressources** , puis cliquez sur **créer**.
4. De la lame de **Notions de base** , entrez les informations de la machine virtuelle, comme illustré ci-dessous et puis cliquez sur **OK**.

    ![Azure portal - notions de base](./media/virtual-network-deploy-static-pip-arm-portal/figure1.png)

5. Dans la lame de **Choisir une taille** , cliquez sur **Standard A1** comme indiqué ci-dessous, puis cliquez sur **Sélectionner**.

    ![Azure portal - choisir une taille](./media/virtual-network-deploy-static-pip-arm-portal/figure2.png)

6. De la lame de **paramètres** , cliquez sur **adresse IP publique**, puis dans la lame de **créer une adresse IP publique** , sous **affectation**, **statique** comme indiqué ci-dessous. Puis cliquez sur **OK**.

    ![Azure portal - créer l’adresse IP publique](./media/virtual-network-deploy-static-pip-arm-portal/figure3.png)

7. De la lame de **paramètres** , cliquez sur **OK**.
8. Passer en revue la lame de **Résumé** , comme indiqué ci-dessous, puis cliquez sur **OK**.

    ![Azure portal - créer l’adresse IP publique](./media/virtual-network-deploy-static-pip-arm-portal/figure4.png)

9. Notez la nouvelle mosaïque de votre tableau de bord.

    ![Azure portal - créer l’adresse IP publique](./media/virtual-network-deploy-static-pip-arm-portal/figure5.png)

10. Une fois que la machine virtuelle est créée, la lame de **paramètres** s’affiche comme indiqué ci-dessous

    ![Azure portal - créer l’adresse IP publique](./media/virtual-network-deploy-static-pip-arm-portal/figure6.png)