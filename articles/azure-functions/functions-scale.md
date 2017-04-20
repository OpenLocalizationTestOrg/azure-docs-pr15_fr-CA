<properties
   pageTitle="Comment faire évoluer les fonctions Azure | Microsoft Azure"
   description="Comprendre comment les fonctions Azure évoluer pour répondre aux besoins de vos charges de travail pilotés par événements."
   services="functions"
   documentationCenter="na"
   authors="dariagrigoriu"
   manager="erikre"
   editor=""
   tags=""
   keywords="Azure fonctions, fonctions, traitement de l’événement, webhooks, calcul dynamique, architecture sans serveur"/>

<tags
   ms.service="functions"
   ms.devlang="multiple"
   ms.topic="reference"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="08/03/2016"
   ms.author="dariagrigoriu"/>

# <a name="how-to-scale-azure-functions"></a>Comment faire évoluer les fonctions Azure

## <a name="introduction"></a>Introduction

Un avantage des fonctions d’Azure, que les ressources de calcul sont consommés uniquement lorsque cela est nécessaire. Cela signifie que vous ne payez pour les machines virtuelles inactives ou à réserver de la capacité de lorsque vous en aurez besoin. Au lieu de cela, la plate-forme alloue la puissance de calcul lorsque votre code est en cours d’exécution, évolution que nécessaire pour gérer la charge, puis mise à l’échelle lorsque le code n’est pas en cours d’exécution.

Le mécanisme de cette nouvelle fonctionnalité est le plan de Service dynamique.  

Cet article fournit une vue d’ensemble du fonctionne de le plan de Service dynamique et comment la plate-forme peut évoluer à la demande pour exécuter votre code.

Si vous n’êtes pas encore familiarisé avec les fonctions d’Azure, vérifiez l’article [vue d’ensemble des fonctions d’Azure](functions-overview.md) pour mieux comprendre ses capacités.

## <a name="configure-azure-functions"></a>Configurer des fonctions Azure

Deux paramètres principaux sont liés à la mise à l’échelle :

* Plan de [service d’application Azure](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) ou Service dynamique
* Taille de la mémoire de l’environnement d’exécution

Le coût d’une fonction change en fonction du plan de service que vous sélectionnez. Un plan de Service dynamique, le coût est une fonction de temps d’exécution, la taille de la mémoire et nombre d’exécutions. Frais de provisionner uniquement lorsque votre code est en cours d’exécution.

Un plan de Service d’application héberge vos fonctions sur des machines virtuelles existantes, qui peuvent également être utilisés pour exécuter tout autre code. Une fois que vous payez chaque mois pour ces ordinateurs virtuels, est sans frais supplémentaire pour les fonctions d’exécution dessus.

## <a name="choose-a-service-plan"></a>Choisir un plan de service

Lorsque vous créez des fonctions, vous pouvez sélectionner pour les exécuter sur un plan de Service dynamique ou d’un [plan de Service de l’application](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).
Dans le plan de Service de l’application, vos fonctions seront exécutera sur un ordinateur virtuel dédié, comme travail d’applications web aujourd'hui pour Basic, Standard ou Premium SKU.
Cet ordinateur virtuel dédié est alloué aux applications et aux fonctions et est toujours disponible si code est activement exécuté ou non. Il s’agit d’une bonne option si vous disposez d’ordinateurs virtuels existants et sous-utilisées qui sont déjà exécuter un autre code ou si vous prévoyez d’exécuter des fonctions en continu ou presque en continu. Une machine virtuelle découple le coût à partir du moment de l’exécution et de mémoire de la taille. Par conséquent, vous pouvez limiter le coût de nombreuses fonctions de longue au coût des VM fonctionnant sur un ou plusieurs.

[AZURE.INCLUDE [Dynamic Service plan](../../includes/functions-dynamic-service-plan.md)]
