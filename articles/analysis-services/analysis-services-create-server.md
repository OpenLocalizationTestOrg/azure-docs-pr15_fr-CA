<properties
   pageTitle="Créer un serveur Analysis Services dans Azure | Microsoft Azure"
   description="Apprenez à créer une instance de serveur Analysis Services dans Azure."
   services="analysis-services"
   documentationCenter=""
   authors="minewiskan"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="analysis-services"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="owend"/>

# <a name="create-an-analysis-services-server"></a>Créer un serveur Analysis Services
Cet article vous guide dans le processus de création d’une nouvelle ressource de serveur Analysis Services dans votre abonnement Azure.

## <a name="before-you-begin"></a>Avant de commencer
Pour commencer, vous devez :

- **Abonnement Azure**: visitez [D’essai Azure](https://azure.microsoft.com/offers/ms-azr-0044p/) pour créer un compte.
- **Groupe de ressources**: utiliser un groupe de ressources que vous avez déjà, ou [créer un nouveau](../azure-resource-manager/resource-group-overview.md).

> [AZURE.NOTE] Création d’un serveur Analysis Services peut entraîner un nouveau service facturable. Pour plus d’informations, consultez Tarification d’Analysis Services.

## <a name="create-an-analysis-services-server"></a>Créer un serveur Analysis Services

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Cliquez sur **+ Nouveau** > **Intelligence + analytique** > **Analysis Services**.

3. De la lame de **Analysis Services** , renseignez les champs requis et appuyez sur **créer**.

    ![Créer le serveur](./media/analysis-services-create-server/aas-create-server-blade.png)

    - **Nom du serveur**: entrez un nom unique utilisé pour référencer le serveur.

    - **Abonnement**: sélectionnez l’abonnement échéances de ce serveur pour.

    - **Groupe de ressources**: il s’agit de conteneurs conçus pour vous aider à gérer un ensemble de ressources Azure. Pour plus d’informations, consultez [groupes de ressources](../resource-group-overview.md).

    - **Emplacement**: emplacement du centre de données Azure ce héberge le serveur. Choisissez un emplacement le plus proche de votre base d’utilisateurs plus important.

    - **Niveau de tarification**: sélectionnez un niveau de tarification. Tabulaire modélise jusqu'à 100 Go sont pris en charge. Vous pouvez toujours modifier votre niveau de tarification ultérieurement.

4. Cliquez sur **créer**.

Créer prend généralement moins d’une minute ; souvent que quelques secondes. Si vous avez sélectionné **Ajouter au portail**, accédez à votre portail pour voir votre nouveau serveur. Ou, d’accéder à **davantage de services** > **Analysis Services** pour voir si votre serveur est prêt. Si elle n’apparaît pas actualiser la liste.

 ![Tableau de bord](./media/analysis-services-create-server/aas-create-server-dashboard.png)


## <a name="next-steps"></a>Étapes suivantes
Une fois que vous avez créé votre serveur, vous pouvez [déployer un modèle](analysis-services-deploy.md) lui à l’aide de SSDT ou SSMS.

Si un modèle que vous déployez sur votre serveur se connecte aux sources de données sur site, vous devez installer une [passerelle de données locales](analysis-services-gateway.md) sur un ordinateur de votre réseau.
