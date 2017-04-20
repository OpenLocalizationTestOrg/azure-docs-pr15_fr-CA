<properties
    pageTitle="Évolution verticale Azure machine virtuelle avec l’Automation d’Azure | Microsoft Azure"
    description="Comment faire pour l’évolution verticale d’une Machine virtuelle Linux en réponse aux alertes avec Azure Automation de surveillance"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="singhkays"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="03/29/2016"
    ms.author="singhkay"/>

# <a name="vertically-scale-azure-virtual-machine-with-azure-automation"></a>Évolution verticale Azure machine virtuelle avec l’Automation d’Azure

Mise à l’échelle verticale est le processus consistant à augmenter ou diminuer les ressources d’un ordinateur en réponse à la charge de travail. Dans Azure Ceci est possible en changeant la taille de la Machine virtuelle. Cela peut vous aider dans les scénarios suivants

- Si l’ordinateur virtuel n’est pas utilisé fréquemment, vous pouvez le redimensionner à une taille plus petite afin de réduire les coûts mensuels
- Si l’ordinateur virtuel est voir un pic de charge, il peut être redimensionnée à une taille supérieure à augmenter ses capacités

Le plan de la procédure pour cela est comme ci-dessous

1. Le programme d’installation d’Automation Azure pour accéder à vos Machines virtuelles
2. Importer les procédures opérationnelles de l’échelle verticale de Azure Automation dans votre abonnement
3. Ajouter un webhook à votre procédure opérationnelle
4. Ajouter une alerte à votre Machine virtuelle

> [AZURE.NOTE] La taille de la première Machine virtuelle, la taille, il peut être mis à l’échelle, peut être limité en raison de la disponibilité des autres tailles du cluster déployé dans des machines virtuelles en cours. Dans les procédures opérationnelles d’automation publiée utilisée dans cet article nous occuper de ce cas et évoluer uniquement au sein du dessous de paires de taille de mémoire virtuelle. Cela signifie qu’un ordinateur virtuel de Standard_D1v2 ne sera pas soudainement agrandies pour Standard_G5 ou adapté à des Basic_A0.

>| Paire de mise à l’échelle des tailles de machine virtuelle |   |
|---|---|
|  Basic_A0 |  Basic_A4 |
|  Standard_A0 | Standard_A4 |
|  Standard_A5 | Standard_A7  |
|  Standard_A8 | Standard_A9  |
|  Standard_A10 |  Standard_A11 |
|  Standard_D1 |  Standard_D4 |
|  Standard_D11 | Standard_D14  |
|  Standard_DS1 |  Standard_DS4 |
|  Standard_DS11 | Standard_DS14  |
|  Standard_D1v2 |  Standard_D5v2 |
|  Standard_D11v2 |  Standard_D14v2 |
|  Standard_G1 |  Standard_G5 |
|  Standard_GS1 |  Standard_GS5 |

## <a name="setup-azure-automation-to-access-your-virtual-machines"></a>Le programme d’installation d’Automation Azure pour accéder à vos Machines virtuelles

La première chose à faire est de créer un compte Azure Automation qui hébergera les procédures opérationnelles utilisées à l’échelle les instances VM échelle définie. Récemment, le service Automation a introduit la fonctionnalité « Exécuter en tant que compte » qui rend le paramètre de l’entité de Service permettant d’exécuter les procédures opérationnelles de la part de l’utilisateur très facile. Vous trouverez d’autres informations dans l’article ci-dessous :

* [Authentifier les procédures opérationnelles avec Azure exécuter en tant que compte](../automation/automation-sec-configure-azure-runas-account.md)

## <a name="import-the-azure-automation-vertical-scale-runbooks-into-your-subscription"></a>Importer les procédures opérationnelles de l’échelle verticale de Azure Automation dans votre abonnement

Les procédures opérationnelles nécessaires à l’évolution verticale de votre Machine virtuelle sont déjà publiés dans la galerie de procédure opérationnelle Azure Automation. Vous devez les importer dans votre abonnement. Vous pouvez apprendre à importer des procédures opérationnelles en consultant l’article suivant.

* [Galeries de module et de procédure opérationnelle pour Azure Automation](../automation/automation-runbook-gallery.md)

Les procédures opérationnelles qui doivent être importées sont affichées dans l’image ci-dessous

![Importer des procédures opérationnelles](./media/virtual-machines-vertical-scaling-automation/scale-runbooks.png)

## <a name="add-a-webhook-to-your-runbook"></a>Ajouter un webhook à votre procédure opérationnelle

Une fois que vous avez importé les procédures opérationnelles que vous devrez ajouter un webhook pour la procédure opérationnelle afin qu’il peut être déclenché par une alerte à partir d’un ordinateur virtuel. Détails de la création d’un webhook pour votre procédure opérationnelle peuvent être lu ici

* [Azure Automation webhooks](../automation/automation-webhooks.md)

Assurez-vous que vous copiez le webhook avant de fermer la boîte de dialogue webhook comme vous en avez besoin dans la section suivante.

## <a name="add-an-alert-to-your-virtual-machine"></a>Ajouter une alerte à votre Machine virtuelle

1. Sélectionnez les paramètres de la Machine virtuelle
2. Sélectionnez « Règles d’alerte »
3. Sélectionnez « Ajouter une alerte »
4. Sélectionnez une mesure pour déclencher l’alerte sur
5. Sélectionnez une condition, où remplies va provoquer le déclenchement de l’alerte
6. Sélectionnez un seuil pour la condition à l’étape 5. à remplir
7. Sélectionnez une période sur laquelle le service de surveillance vérifie la condition et le seuil dans les étapes 5 et 6
8. Coller dans la webhook que vous avez copié à partir de la section précédente.

![Ajout d’alerte à un ordinateur virtuel 1](./media/virtual-machines-vertical-scaling-automation/add-alert-webhook-1.png)

![Ajout d’alerte à la Machine virtuelle 2](./media/virtual-machines-vertical-scaling-automation/add-alert-webhook-2.png)