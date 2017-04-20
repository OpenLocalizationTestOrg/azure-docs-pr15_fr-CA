<properties
    pageTitle="Créer des batteries de serveurs SharePoint | Microsoft Azure"
    description="Créer rapidement une nouvelle batterie de serveurs SharePoint 2013 ou SharePoint 2016 dans Azure."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="JoeDavies-MSFT"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/30/2016"
    ms.author="josephd"/>

# <a name="create-sharepoint-server-farms"></a>Créer des batteries de serveurs SharePoint

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modèle classique.

## <a name="sharepoint-2013-farms"></a>Batteries de serveurs SharePoint 2013

Avec le site de portail Microsoft Azure, vous pouvez rapidement créer des batteries de serveurs SharePoint Server 2013 préconfigurées. Cela peut vous faire gagner beaucoup de temps lorsque vous avez besoin d’une batterie SharePoint de base ou de haute disponibilité pour un environnement de développement/test ou si vous évaluez SharePoint Server 2013 sous la forme d’une solution de collaboration pour votre entreprise.

> [AZURE.NOTE] L’élément de **Batterie de serveurs SharePoint** dans la Azure Marketplace du portail Azure a été supprimé. Il a été remplacé par les éléments de **SharePoint 2013 non-HA batterie** et la **Batterie de serveurs SharePoint 2013 HA** .

La batterie SharePoint de base se compose de trois ordinateurs virtuels dans cette configuration.

![sharepointfarm](./media/virtual-machines-windows-sharepoint-farm/Non-HAFarm.png)

Vous pouvez utiliser cette configuration de batterie de serveurs pour une installation simplifiée pour le développement d’applications SharePoint ou votre évaluation première de SharePoint 2013.

Pour créer la batterie de serveurs SharePoint (trois serveurs) base :

1. Cliquez sur [ici](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-nonha/).
2. Cliquez sur **déployer**.
3. Dans le volet de **SharePoint 2013 non-HA batterie de serveurs** , cliquez sur **créer**.
4. Spécifiez des paramètres sur les étapes du volet **Création SharePoint 2013 non-HA batterie de serveurs** , puis cliquez sur **créer**.

La batterie de serveurs haute disponibilité se compose de neuf ordinateurs virtuels dans cette configuration.

![sharepointfarm](./media/virtual-machines-windows-sharepoint-farm/HAFarm.png)

Vous pouvez utiliser cette configuration de batterie de serveurs pour tester des charges client plus élevées, une haute disponibilité du site SharePoint externe et groupes de disponibilité AlwaysOn de SQL Server pour une batterie de serveurs SharePoint. Vous pouvez également utiliser cette configuration pour le développement des applications SharePoint dans un environnement à haute disponibilité.

Pour créer la batterie de serveurs haute disponibilité (neuf serveurs) SharePoint :

1. Cliquez sur [ici](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-ha/).
2. Cliquez sur **déployer**.
3. Dans le volet de la **Batterie de serveurs SharePoint 2013 haute disponibilité** , cliquez sur **créer**.
4. Spécifiez des paramètres sur les sept étapes du volet **Création 2013 HA batterie de serveurs SharePoint** , puis cliquez sur **créer**.

> [AZURE.NOTE] Vous ne pouvez pas créer le **SharePoint 2013 non-HA batterie** ou une **Batterie de serveurs SharePoint 2013 HA** avec une version d’évaluation gratuite d’Azure.

Le portail Azure crée deux de ces batteries de serveurs dans un réseau virtuel nuage uniquement avec une présence sur le web via Internet. Il n’y a aucune connexion de site à site VPN ou ExpressRoute sur le réseau de votre organisation.

> [AZURE.NOTE] Lorsque vous créez le basic ou batteries de serveurs de SharePoint de haute disponibilité via le portail d’Azure, vous ne pouvez pas spécifier un groupe de ressources existant. Pour contourner cette limitation, créez ces exploitations avec PowerShell d’Azure. Pour plus d’informations, consultez [créer SharePoint 2013 batteries de développement/test avec PowerShell d’Azure](https://technet.microsoft.com/library/mt743093.aspx#powershell).

## <a name="sharepoint-2016-farms"></a>Batteries de serveurs SharePoint 2016

Consultez [cet article](https://technet.microsoft.com/library/mt723354.aspx) pour les instructions créer la batterie de serveurs SharePoint Server 2016 suivant du serveur unique.

![sharepointfarm](./media/virtual-machines-windows-sharepoint-farm/SP2016Farm.png)

## <a name="managing-the-sharepoint-farms"></a>Gérer les batteries de serveurs SharePoint

Vous pouvez administrer les serveurs de ces batteries de serveurs par le biais de connexions Bureau à distance. Pour plus d’informations, consultez [Ouvrir une session sur l’ordinateur virtuel](virtual-machines-windows-hero-tutorial.md#log-on-to-the-virtual-machine).

À partir du site Central Administration SharePoint, vous pouvez configurer mes sites, les applications SharePoint et autres fonctionnalités. Pour plus d’informations, consultez [Configurer SharePoint](http://technet.microsoft.com/library/ee836142.aspx).

## <a name="next-steps"></a>Étapes suivantes

- Découvrez les autres [configurations de SharePoint](https://technet.microsoft.com/library/dn635309.aspx) dans les services d’infrastructure Azure.
