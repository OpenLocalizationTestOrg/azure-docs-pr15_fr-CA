<properties services="virtual-machines" title="Setting up Azure CLI for service management" authors="squillace" solutions="" manager="timlt" editor="tysonn" />

<tags
   ms.service="virtual-machine"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="linux"
   ms.workload="infrastructure"
   ms.date="04/13/2015"
   ms.author="rasquill" />

## <a name="using-azure-cli"></a>À l’aide de la CLI Azure

Les étapes suivantes vous aident à utiliser les Azure CLI facilement avec la version la plus récente et l’abonnement approprié. Si vous avez besoin installer l’interface CLI d’Azure et le connecter à votre compte tout d’abord, consultez l' [Interface de ligne de commande de Azure (Azure CLI)](xplat-cli-install.md).

### <a name="step-1-update-azure-cli-version"></a>Étape 1 : Version de mise à jour Azure CLI

Pour utiliser la CLI d’Azure pour commandes impératives avec le mode de gestion de service, doit avoir une version récente si possible. Pour vérifier votre version, tapez `azure --version`. Vous devriez voir quelque chose comme :

    $ azure --version
    0.8.17 (node: 0.10.25)

Si vous souhaitez mettre à jour votre version du CLI d’Azure, consultez [Azure CLI](https://github.com/Azure/azure-xplat-cli).

### <a name="step-2-set-the-azure-account-and-subscription"></a>Étape 2 : Définir le compte Azure et abonnement

Une fois que vous avez connecté votre CLI Azure avec le compte que vous souhaitez utiliser, peut avoir plus d’un abonnement. Dans ce cas, vous devez examiner les abonnements disponibles pour votre compte en tapant `azure account list`, puis sélectionnez l’abonnement que vous voulez utiliser en tapant `azure account set <subscription id or name> true` où _nom ou id d’abonnement_ est l’id d’abonnement ou le nom d’abonnement que vous souhaitez utiliser dans la session en cours. Vous devriez voir quelque chose comme suit :

    $ azure account set "Visual Studio Ultimate with MSDN" true
    info:    Executing command account set
    info:    Setting subscription to "Visual Studio Ultimate with MSDN" with id "0e220bf6-5caa-4e9f-8383-51f16b6c109f".
    info:    Changes saved
    info:    account set command OK

> [AZURE.NOTE] Si vous ne disposez pas d’un compte Azure, mais vous êtes abonné à un abonnement MSDN, vous pouvez obtenir gratuitement Azure crédits par l’activation de vos [avantages d’abonné MSDN ici](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) --ou vous peuvent utiliser le compte gratuit. Soit fonctionnera pour accès Azure.
