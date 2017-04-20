<properties
    pageTitle="Abonnement et lignes directrices de comptes | Microsoft Azure"
    description="Obtenir des informations sur les directives de conception et d’implémentation clés pour les abonnements et les comptes dans Azure."
    documentationCenter=""
    services="virtual-machines-windows"
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/08/2016"
    ms.author="iainfou"/>

# <a name="subscription-and-accounts-guidelines"></a>Instructions d’abonnement et de comptes

[AZURE.INCLUDE [virtual-machines-windows-infrastructure-guidelines-intro](../../includes/virtual-machines-windows-infrastructure-guidelines-intro.md)] 

Cet article se concentre sur la compréhension de l’approche de gestion d’abonnement et compte comme votre environnement et augmente de base d’utilisateurs.


## <a name="implementation-guidelines-for-subscriptions-and-accounts"></a>Instructions d’implémentation pour les comptes et les abonnements

Décisions :

- Le jeu d’abonnements et de faire des comptes vous devez héberger votre charge de travail informatique ou votre infrastructure ?
- Comment faire pour décomposer la hiérarchie afin de l’adapter à votre organisation ?

Tâches :

- Définir la hiérarchie logique que vous souhaitez administrer à partir d’un niveau d’abonnement.
- Pour faire correspondre cette hiérarchie logique, définissez les comptes requis et les abonnements sous chaque compte.
- Créer l’ensemble des abonnements et des comptes à l’aide de votre convention d’affectation de noms.


## <a name="subscriptions-and-accounts"></a>Les comptes et les abonnements

Pour travailler avec Azure, vous avez besoin d’un ou plusieurs abonnements Azure. Ressources telles que des machines virtuelles (VM) ou les réseaux virtuels existent dans ces abonnements.

- Les clients d’entreprise ont généralement une inscription de l’entreprise, qui est la ressource la plus en haut dans la hiérarchie, est associé à un ou plusieurs comptes.
- Pour les particuliers et les clients sans une inscription de l’entreprise, la ressource la plus en haut est le compte.
- Les abonnements sont associés aux comptes, et il peut y avoir un ou plusieurs abonnements par compte. Enregistrements Azure facturation des informations au niveau de l’abonnement.

En raison de la limite de deux niveaux de hiérarchie de la relation de compte/abonnement, il est important d’aligner la convention d’affectation de noms de comptes et les besoins des facturation des abonnements. Par exemple, si une société internationale utilise Azure, ils peuvent choisir d’avoir un seul compte par région, et ont des abonnements gérés au niveau de la région :

![](./media/virtual-machines-common-infrastructure-service-guidelines/sub01.png)

Par exemple, vous pouvez utiliser la structure suivante :

![](./media/virtual-machines-common-infrastructure-service-guidelines/sub02.png)

Si une région décide d’avoir plus d’un abonnement associé à un groupe particulier, la convention d’affectation de noms doit incorporer permet de coder les données supplémentaires sur le compte ou le nom de l’abonnement. Cette organisation permet de massage des données de facturation pour générer de nouveaux niveaux de la hiérarchie au cours de rapports de facturation :

![](./media/virtual-machines-common-infrastructure-service-guidelines/sub03.png)

L’organisation pourrait ressembler à ceci :

![](./media/virtual-machines-common-infrastructure-service-guidelines/sub04.png)

Nous fournissons une facturation détaillée via un fichier téléchargeable pour un seul compte, ou pour tous les comptes dans un accord d’entreprise.


## <a name="next-steps"></a>Étapes suivantes

[AZURE.INCLUDE [virtual-machines-windows-infrastructure-guidelines-next-steps](../../includes/virtual-machines-windows-infrastructure-guidelines-next-steps.md)] 