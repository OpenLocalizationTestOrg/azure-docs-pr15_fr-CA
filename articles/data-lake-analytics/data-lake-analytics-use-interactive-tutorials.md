<properties 
   pageTitle="Découvrez les données lac Analytique et U-SQL à l’aide de didacticiels Azure Portal Interactive | Azure" 
   description="Démarrage rapide d’apprentissage des données lac Analytique et U-SQL. " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="05/16/2016"
   ms.author="edmaca"/>


# <a name="use-azure-data-lake-analytics-interactive-tutorials"></a>Utiliser les didacticiels interactifs Azure données lac Analytique

Le portail Azure propose un didacticiel interactif pour vous familiariser avec les données lac Analytique. Cet article vous montre comment parcourir le didacticiel pour l’analyse des journaux de site Web.


>[AZURE.NOTE]Si vous souhaitez parcourir le didacticiel même à l’aide de Visual Studio, reportez-vous à la section [analyse les journaux de site Web à l’aide de données lac Analytique](data-lake-analytics-analyze-weblogs.md).
>Plus des didacticiels interactifs à ajouter sur le portail.


Pour les autres didacticiels, voir :

- [Mise en route d’Analytique de LAC de données à l’aide du portail Azure](data-lake-analytics-get-started-portal.md)
- [Mise en route de données Analytique de LAC à l’aide de PowerShell d’Azure](data-lake-analytics-get-started-powershell.md)
- [Mise en route de données Analytique de LAC à l’aide du Kit de développement .NET](data-lake-analytics-get-started-net-sdk.md)
- [Développer des scripts SQL-U à l’aide des outils de données LAC pour Visual Studio](data-lake-analytics-data-lake-tools-get-started.md) 

**Conditions préalables**

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Analytique de LAC de données un compte**.  Reportez-vous à la section [Mise en route d’Analytique de LAC de données Azure à l’aide du portail Azure](data-lake-analytics-get-started-portal.md).

##<a name="create-data-lake-analytics-account"></a>Créer le compte de données lac Analytique 

Vous devez avoir un compte de données lac Analytique avant de pouvoir exécuter toutes les tâches.

Chaque compte Analytique lac de données possède une dépendance de compte [Azure Data Store de LAC](../data-lake-store/data-lake-store-overview.md) .  Ce compte est désigné comme le compte de la banque de données lac par défaut.  Vous pouvez créer le compte de banque de données lac au préalable ou lorsque vous créez votre compte Analytique lac de données. Dans ce didacticiel, vous allez créer le compte de la banque de données LAC avec le compte Analytique

**Pour créer un compte Analytique lac de données**

1. Ouvrez une session sur le [portail Azure](https://portal.azure.com/signin/index/?Microsoft_Azure_Kona=true&Microsoft_Azure_DataLake=true&hubsExtension_ItemHideKey=AzureDataLake_BigStorage%2cAzureKona_BigCompute).
2. Cliquez sur **Microsoft Azure** dans le coin supérieur gauche pour ouvrir le StartBoard.
3. Cliquez sur la mosaïque du **marché** .  
3. Dans la zone de recherche sur la lame de **tous les éléments** , puis appuyez sur **entrée**, tapez **Azure données lac Analytique** . Vous veillent **Analytique de LAC de données Azure** dans la liste.
4. Dans la liste, cliquez sur **Azure données lac Analytique** .
5. Cliquez sur **créer** en bas de la lame.
6. Tapez ou sélectionnez le des options suivantes :

    ![Lame de portail Analytique lac de données Azure](./media/data-lake-analytics-get-started-portal/data-lake-analytics-portal-create-adla.png)

    - **Nom**: nom du compte Analytique.
    - **Magasin de données lac**: compte de chaque Analytique de LAC de données possède un compte de banque de données lac dépendant. Le compte Analytique lac de données et le compte de banque de données lac dépendant doivent se trouver dans le même centre de données Azure. Suivez les instructions pour créer un nouveau compte de banque de données lac ou sélectionnez-en une existante.
    - **Abonnement**: choisissez l’abonnement Azure utilisé pour le compte Analytique.
    - **Groupe de ressources**. Sélectionnez un groupe de ressources Azure existant ou créer un nouveau. Les applications sont généralement composées de nombreux composants, par exemple une application web, de base de données, serveur de base de données, stockage et services de tiers 3e. Le Gestionnaire de ressources Azure (ARM) vous permet de travailler avec les ressources de votre application en tant que groupe, visé un groupe de ressources Azure. Vous pouvez déployer, mettre à jour, surveiller ou supprimer toutes les ressources de votre application dans une opération unique et coordonnée. Vous utilisez un modèle de déploiement, et ce modèle peut fonctionner dans différents environnements comme test, intermédiaire et production. Vous pouvez clarifier la facturation pour votre organisation en affichant les coûts reportées pour le groupe entier. Pour plus d’informations, consultez [Vue d’ensemble du Gestionnaire de ressources Azure](azure-resource-manager/resource-group-overview.md). 
    - **Emplacement**. Permet de sélectionner un centre de données Azure pour le compte de données lac Analytique. 
7. Sélectionnez le **code Pin à Startboard**. Ceci est nécessaire pour la suite de ce didacticiel.
8. Cliquez sur **créer**. Il vous permet du portail StartBoard. Une nouvelle mosaïque est ajoutée à la page d’accueil avec l’étiquette affiche « Déploiement d’Azure données lac Analytique ». Il prend un certain temps pour créer un compte Analytique lac de données. Lorsque le compte est créé, le portail ouvre le compte sur une nouvelle lame.

    ![Lame de portail Analytique lac de données Azure](./media/data-lake-analytics-get-started-portal/data-lake-analytics-portal-blade.png)

##<a name="run-website-log-analysis-interactive-tutorial"></a>Exécuter le didacticiel interactif de l’analyse des journaux site Web

**Pour ouvrir le didacticiel interactif Analytique de journal de site Web**

1. À partir du portail, cliquez sur **Microsoft Azure** dans le menu de gauche pour ouvrir le StartBoard.
2. Cliquez sur la mosaïque qui est liée à votre compte de données lac Analytique.
3. Cliquez sur **Explorer les didacticiels interactifs** à partir de la barre de **l’éducation** .

    ![Didacticiels interactifs de données lac Analytique](./media/data-lake-analytics-use-interactive-tutorials/data-lake-analytics-explore-interactive-tutorials.png)

4. Si vous voyez un message d’avertissement orange « exemples n’est pas configurée, cliquez sur... », cliquez sur **Exemple de données de copie** pour copier les exemples de données pour le compte de la banque de données lac par défaut. Le didacticiel interactif a besoin de données à exécuter.
5. À partir de la blade de **Didacticiels interactifs** , cliquez sur **Site Web journal Analytique**. Le portail ouvre le didacticiel dans une nouvelle lame de portail.
5. Cliquez sur **1 Introduction** et suivez les instructions

##<a name="see-also"></a>Voir aussi

- [Vue d’ensemble de Microsoft Azure données lac Analytique](data-lake-analytics-overview.md)
- [Mise en route d’Analytique de LAC de données à l’aide du portail Azure](data-lake-analytics-get-started-portal.md)
- [Mise en route de données Analytique de LAC à l’aide de PowerShell d’Azure](data-lake-analytics-get-started-powershell.md)
- [Développer des scripts SQL-U à l’aide des outils de données LAC pour Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
- [Analyser les journaux du site Web à l’aide d’Azure données lac Analytique](data-lake-analytics-analyze-weblogs.md)
