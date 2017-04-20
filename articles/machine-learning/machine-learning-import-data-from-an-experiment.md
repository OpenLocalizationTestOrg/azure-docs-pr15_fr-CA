<properties
    pageTitle="Importer des données dans un Studio de formation de Machine à partir d’une autre expérience | Microsoft Azure"
    description="Comment enregistrer les données d’apprentissage dans Azure Machine Learning Studio et l’utiliser dans une autre expérience."
    keywords="importer des données, les données, les sources de données, les données d’apprentissage"
    services="machine-learning"
    documentationCenter=""
    authors="garyericson"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="garye;bradsev" />


# <a name="import-your-data-into-azure-machine-learning-studio-from-another-experiment"></a>Importer les données dans Azure Machine Learning Studio à partir d’une autre expérience

[AZURE.INCLUDE [import-data-into-aml-studio-selector](../../includes/machine-learning-import-data-into-aml-studio.md)]


Il peut arriver, lorsque vous souhaiterez prendre un résultat intermédiaire à partir d’une expérience et de l’utiliser dans le cadre d’une autre expérience.  Pour ce faire, vous enregistrez le module sous la forme d’un groupe de données :

1. Cliquez sur la sortie du module que vous souhaitez enregistrer sous la forme d’un groupe de données.

2. Cliquez sur **Enregistrer en tant que Dataset**.

3. Lorsque vous y êtes invité, entrez un nom et une description qui vous permet de facilement identifier le groupe de données.

4. Cliquez sur la coche **OK** .

Lorsque la sauvegarde est terminée, le groupe de données sera disponible pour une utilisation dans une expérience dans votre espace de travail. Vous pouvez le trouver dans la liste **Des groupes de données enregistrées** dans la palette du module.

