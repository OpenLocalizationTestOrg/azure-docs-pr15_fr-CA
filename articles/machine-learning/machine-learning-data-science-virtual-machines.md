<properties
    pageTitle="Ordinateurs virtuels Science de données dans Azure | Microsoft Azure"
    description="La valeur d’une Machine virtuelle de données scientifiques"
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard" 
    editor="cgronlun"  />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/19/2016"
    ms.author="xibingao;bradsev" />

# <a name="data-science-virtual-machines-in-azure"></a>Ordinateurs virtuels Science données Azure

Des instructions sont fournies ici qui décrivent comment configurer un ordinateur virtuel d’Azure et un VM Azure avec SQL Service en tant que serveurs de IPython bloc-notes. La machine virtuelle Windows est configurée avec la prise en charge des outils comme IPython bloc-notes, Explorateur de stockage Azure et AzCopy, ainsi que les autres utilitaires qui sont utiles pour des projets de science. Explorateur de stockage Azure et AzCopy, par exemple, fournissent les moyens pratiques pour transférer des données vers le stockage Azure à partir de votre ordinateur local ou pour le télécharger sur votre ordinateur local à partir du stockage. 

Ce menu liens vers des rubriques qui décrivent comment configurer différents environnements de science de données utilisées par le [Processus de Science données Team (TDSP)](data-science-process-overview.md).

[AZURE.INCLUDE [data-science-environment-setup](../../includes/cap-setup-environments.md)]

Plusieurs types de Azure VM peuvent être mis en service et configurés pour être utilisés dans le cadre d’un environnement informatique de données basées sur le nuage. La décision sur la version de la machine virtuelle à utiliser varie selon le type et la quantité de données à modéliser avec apprentissage automatique et la destination des données dans le nuage. 

* Pour obtenir des conseils sur les questions à prendre en considération lorsque vous prenez cette décision, voir [Planifier votre Machine Learning données Science environnement Azure](machine-learning-data-science-plan-your-environment.md). 
* Pour un catalogue de certains des scénarios que vous pouvez rencontrer lors de l’exécution analytique avancée, voir [scénarios pour des processus Analytique avancée et technologie d’apprentissage d’ordinateur Azure](machine-learning-data-science-plan-sample-scenarios.md)

Deux ensembles d’instructions sont fournies :

* [Configurer une machine virtuelle Azure comme serveur de IPython portable pour avancées analytique](machine-learning-data-science-setup-virtual-machine.md) montre comment configurer un ordinateur virtuel Azure avec IPython bloc-notes et d’autres outils permettant d’effectuer la science des données pour les cas dans lesquels une forme de stockage Azure que SQL peut être utilisée pour stocker les données.

* [Configurer un ordinateur virtuel de Azure SQL Server comme serveur de IPython portable pour analytique avancée](machine-learning-data-science-setup-sql-server-virtual-machine.md) montre comment configurer un ordinateur virtuel de Azure SQL Server avec l’ordinateur portable de IPython et d’autres outils permettant d’effectuer la science des données pour les cas dans lequel une base de données SQL peut être utilisé pour stocker les données.

Une fois configuré et mis en service, ces machines virtuelles sont prêts à être utilisés en tant que serveurs IPython portable d’exploration et de traitement de données et pour les autres tâches nécessaires avec formation de Machine Azure et le processus de Science données Team (TDSP). Les étapes du processus de données scientifiques sont mappés dans le [chemin d’accès de la formation TDSP](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) et peuvent inclure des étapes de déplacement des données dans SQL Server ou de HDInsight, de traitent et d’aperçu en vue de l’apprentissage à partir des données avec Azure Machine d’apprentissage.


> [AZURE.NOTE] Les Machines virtuelles Azure sont tarifés comme **payez uniquement ce que vous utilisez**. Pour vous assurer que vous ne sont pas en cours facturés lorsque vous n’utilisez ne pas votre ordinateur virtuel, il doit se trouver dans l’état **arrêté (Deallocated)** à partir du [Portail classique d’Azure](http://manage.windowsazure.com/). Pour obtenir des instructions pas à pas ou comment libérer la machine virtuelle, voir [arrêt et libérer l’ordinateur virtuel lorsqu’elles pas en cours d’utilisation](machine-learning-data-science-setup-virtual-machine.md#shutdown)
 
