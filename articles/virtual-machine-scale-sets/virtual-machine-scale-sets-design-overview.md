<properties
    pageTitle="Conception d’échelle de l’ordinateur virtuel définit pour échelle | Microsoft Azure"
    description="En savoir plus sur la conception de vos jeux d’échelle de Machine virtuelle pour échelle"
    keywords="échelle de machine virtuelle de la machine virtuelle Linux définit" 
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="gatneil"
    manager="madhana"
    editor="tysonn"
    tags="azure-resource-manager" />

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="na"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/28/2016"
    ms.author="gatneil"/>

# <a name="designing-vm-scale-sets-for-scale"></a>Conception d’échelle de la machine virtuelle définit pour échelle

Cette rubrique décrit les considérations de design pour les jeux d’échelle de Machine virtuelle. Pour plus d’informations sur les jeux d’échelle de Machine virtuelle, consultez [Vue d’ensemble des jeux d’ordinateur virtuel échelle](virtual-machine-scale-sets-overview.md).


## <a name="storage"></a>Stockage

Un ensemble d’échelle utilise des comptes de stockage pour stocker les disques du système d’exploitation des ordinateurs virtuels dans le jeu. Nous vous recommandons un ratio de 20 VM par compte de stockage ou moins. Nous vous recommandons également de répartir entre l’alphabet les premiers caractères des noms de compte de stockage. Cela vous permet de répartir la charge sur différents systèmes internes. Par exemple, dans le modèle suivant, nous utilisons la fonction de gestionnaire de ressources du modèle d’uniqueString pour générer des hachages de préfixe qui sont ajoutés aux noms de compte de stockage : [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat).


## <a name="overprovisioning"></a>Overprovisioning

À partir de la version de l’API « 2016-03-30 », jeux d’échelle de machine virtuelle par défaut est « overprovisioning » les ordinateurs virtuels. Overprovisioning sous tension, l’échelle définie réellement vrille les davantage d’ordinateurs virtuels que vous avez demandé, puis supprime les ordinateurs virtuels supplémentaires qui a lancé le dernier. Overprovisioning améliore les taux de réussite de mise en service. Vous n’êtes pas facturé pour ces ordinateurs virtuels supplémentaires, et ils n’ont aucune incidence sur les limites des quotas.

Alors que les overprovisioning, les taux de réussite de mise en service ne s’améliore pas, il peut provoquer un comportement déroutant pour une application qui n’est pas conçu pour gérer des ordinateurs virtuels disparaissent sans préavis. Pour activer l’overprovisioning off, assurez-vous la chaîne suivante dans votre modèle : « overprovision » : « false ». Vous trouverez plus de détails dans la [documentation de l’API de machine virtuelle échelle définie reste](https://msdn.microsoft.com/library/azure/mt589035.aspx).

Si vous désactivez l’option overprovisioning, vous en sortir avec un taux de plus de machines virtuelles par compte de stockage, mais nous ne recommandons pas passer au-dessus de 40.


## <a name="limits"></a>Limites
Un ensemble d’échelle basé sur une image personnalisée (une par vous) devez créer tous les disques virtuels de système d’exploitation au sein d’un seul compte de stockage. Par conséquent, le nombre maximal recommandé d’ordinateurs virtuels dans un jeu d’échelle basé sur une image personnalisée est de 20. Si vous désactivez l’option overprovisioning, vous pouvez aller jusqu'à 40.

Un ensemble d’échelle basé sur une image de la plate-forme est actuellement limité à 100 VM (nous vous recommandons de 5 comptes de stockage pour cette échelle).

Pour les machines virtuelles plus que de permettent à ces limites, vous devez déployer plusieurs ensembles d’échelle, comme indiqué dans [ce modèle](https://github.com/Azure/azure-quickstart-templates/tree/master/301-custom-images-at-scale).