<properties
   pageTitle="Créer un Internet face à l’équilibreur de charge dans le Gestionnaire de ressources à l’aide d’un modèle | Microsoft Azure"
   description="Apprenez à créer un équilibreur de charge dans le Gestionnaire de ressources à l’aide d’un modèle d’ouvert sur Internet"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/24/2016"
   ms.author="sewhee" />

# <a name="creating-an-internet-facing-load-balancer-using-a-template"></a>Création d’un équilibreur de charge à l’aide d’un modèle d’ouvert sur Internet

[AZURE.INCLUDE [load-balancer-get-started-internet-arm-selectors-include.md](../../includes/load-balancer-get-started-internet-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Cet article décrit le modèle de déploiement du Gestionnaire de ressources. Vous pouvez également [apprendre à créer un équilibreur de charge à l’aide du modèle de déploiement classique d’ouvert sur Internet](load-balancer-get-started-internet-classic-portal.md)


[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="deploy-the-template-by-using-click-to-deploy"></a>Déployer le modèle à l’aide de cliquez sur déployer

L’exemple de modèle disponible dans le référentiel public utilise un fichier de paramètres contenant les valeurs par défaut utilisées pour générer le scénario décrit ci-dessus. Pour déployer ce modèle à l’aide de clic à déployer, suivez [ce lien](http://go.microsoft.com/fwlink/?LinkId=544801), cliquez sur **déployer vers Azure**, remplacez les valeurs de paramètre par défaut si nécessaire et suivez les instructions dans le portail.

## <a name="deploy-the-template-by-using-powershell"></a>Déployer le modèle à l’aide de PowerShell

Pour déployer le modèle que vous avez téléchargé à l’aide de PowerShell, suivez les étapes ci-dessous.

1. Si vous n’avez jamais utilisé Azure PowerShell, voir [Comment faire pour installer et configurer le PowerShell Azure](../../articles/powershell-install-configure.md) et suivez les instructions jusqu'à la fin pour signer dans Azure et sélectionnez votre abonnement.

2. Exécutez l’applet de commande **New-AzureRmResourceGroupDeployment** pour créer un groupe de ressources à l’aide du modèle.

        New-AzureRmResourceGroupDeployment -Name TestRG -Location uswest `
            -TemplateFile 'https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json' `
            -TemplateParameterFile 'https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.parameters.json'

## <a name="deploy-the-template-by-using-the-azure-cli"></a>Déployer le modèle à l’aide de la CLI d’Azure

Pour déployer le modèle à l’aide de la CLI d’Azure, suivez les étapes ci-dessous.

1. Si vous n’avez jamais utilisé CLI d’Azure, consultez [installation et configuration de l’infrastructure du langage commun Azure](../../articles/xplat-cli-install.md) et suivez les instructions jusqu'à l’endroit où vous sélectionnez votre compte Azure et abonnement.
2. Exécutez la commande **mode config azure** pour passer en mode de gestionnaire de ressources, comme indiqué ci-dessous.

        azure config mode arm

    Voici la sortie attendue de la commande ci-dessus :

        info:    New mode is arm

3. À partir de votre navigateur, accédez au [Modèle de démarrage rapide](https://github.com/Azure/azure-quickstart-templates/tree/master/201-2-vms-loadbalancer-lbrules), copiez le contenu du fichier json et coller dans un nouveau fichier sur votre ordinateur. Dans ce scénario, vous souhaitez copie les valeurs ci-dessous dans un fichier nommé **c:\lb\azuredeploy.parameters.json**.
4. Exécutez l’applet de commande **groupe azure déploiement crée** pour déployer le nouveau équilibreur de charge en utilisant les fichiers de modèle et de paramètres vous avez téléchargé et modifier les propriétés. La liste est indiquée après que la sortie explique les paramètres utilisés.

        azure group create -n TestRG -l westus -f 'https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json' -e 'c:\lb\azuredeploy.parameters.json'

## <a name="next-steps"></a>Étapes suivantes

[Démarrer la configuration d’un équilibreur de charge interne](load-balancer-get-started-ilb-arm-ps.md)

[Configurer un mode de distribution d’équilibrage de la charge](load-balancer-distribution-mode.md)

[Configurer les paramètres de délai d’attente TCP inactifs pour votre équilibreur de charge](load-balancer-tcp-idle-timeout.md)
