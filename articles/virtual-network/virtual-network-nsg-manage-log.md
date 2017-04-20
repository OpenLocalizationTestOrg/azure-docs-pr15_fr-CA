<properties
   pageTitle="Surveiller les opérations, les événements et les compteurs pour NSGs | Microsoft Azure"
   description="Découvrez comment activer la journalisation opérationnelle pour NSGs, les événements et les compteurs"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/14/2016"
   ms.author="jdial" />

#<a name="log-analytics-for-network-security-groups-nsgs"></a>Analytique de journal pour les groupes de sécurité réseau (NSGs)

Vous pouvez utiliser différents types de journaux dans Azure pour gérer et dépanner les NSGs. Certains de ces fichiers journaux sont accessibles via le portail, et tous les journaux peuvent être extraits à partir d’un stockage blob Azure et affichés dans les différents outils, tels que le [Journal Analytique](../log-analytics/log-analytics-azure-networking-analytics.md), Excel et PowerBI. Pour plus d’informations sur les différents types de journaux à partir de la liste ci-dessous.

- **Des journaux d’audit :** Vous pouvez utiliser [Les journaux d’Audit Azure](../monitoring-and-diagnostics/insights-debugging-with-events.md) (anciennement appelé les journaux des opérations) permet d’afficher toutes les opérations à votre abonnement Azure et de leur statut. Les journaux d’audit sont activés par défaut et peuvent être affichés dans le portail Azure aperçu.
- **Les journaux d’événements :** Vous pouvez utiliser ce journal pour afficher les règles NSG sont appliquées à des ordinateurs virtuels et les rôles d’instance en fonction de l’adresse MAC. Le statut de ces règles est collecté toutes les 60 secondes.
- **Journaux de compteur :** Vous pouvez utiliser ce journal pour afficher le nombre de fois où chaque règle NSG a été appliqué pour refuser ou autoriser le trafic.

>[AZURE.WARNING] Les journaux sont disponibles uniquement pour les ressources déployées dans le modèle de déploiement du Gestionnaire de ressources. Vous ne pouvez pas utiliser des journaux pour les ressources dans le modèle de déploiement classique. Pour mieux comprendre les deux modèles, faire référence à l’article [déploiement de présentation du Gestionnaire de ressources et de déploiement classique](../resource-manager-deployment-model.md) .

##<a name="enable-logging"></a>Activer la journalisation
L’enregistrement d’audit est activé automatiquement à toutes les heures pour chaque ressource de gestionnaire de ressources. Vous devez activer les événements et le journal pour commencer à collecter les données disponibles par le biais des journaux de compteur. Pour activer l’enregistrement, suivez les étapes ci-dessous.

1.  Ouvrez une session dans le [portail Azure](https://portal.azure.com). Si vous ne disposez pas d’un groupe de sécurité de réseau existant, [créez un NSG](virtual-networks-create-nsg-arm-ps.md) avant de continuer.

2.  Dans le portail de l’aperçu, cliquez sur **Parcourir** >> **groupes de sécurité réseau**.

    ![Portail d’aperçu - groupes de sécurité réseau](./media/virtual-network-nsg-manage-log/portal-enable1.png)

3. Sélectionnez un groupe de sécurité du réseau existant.

    ![Portail d’aperçu - paramètres de groupe de sécurité réseau](./media/virtual-network-nsg-manage-log/portal-enable2.png)

4. De la lame de **paramètres** , cliquez sur **Diagnostics**et puis dans le volet de **Diagnostics** , en regard de l' **état**, cliquez **sur**
5. De la lame de **paramètres** , cliquez sur le **Compte de stockage**, puis sélectionnez un système de stockage existant compte ou créez-en un nouveau.  

>[AZURE.INFORMATION] les journaux d’Audit ne nécessitent pas un compte de stockage distinct. L’utilisation du stockage pour l’événement et la consignation de règle implique des frais de service.

6. Dans la liste déroulante, sous **Compte de stockage**, sélectionnez si vous souhaitez consigner les événements, les compteurs ou les deux, puis cliquez sur **Enregistrer**.

    ![Portail d’aperçu - journaux de diagnostic](./media/virtual-network-nsg-manage-log/portal-enable3.png)

## <a name="audit-log"></a>Journal d’audit
Par défaut, ce journal (anciennement appelé le « journal opérationnel ») est généré par Azure.  Les journaux sont conservés pendant 90 jours à banque de journaux d’événements d’Azure. Plus d’informations sur ces journaux en lisant l’article [afficher des événements et des journaux d’audit](../monitoring-and-diagnostics/insights-debugging-with-events.md) .

## <a name="counter-log"></a>Journal de compteur
Ce journal est généré uniquement si vous l’avez activé sur une base par NSG comme décrit ci-dessus. Les données sont stockées dans le compte de stockage que vous avez spécifié lorsque vous avez activé la journalisation. Chaque règle appliquée à des ressources est enregistré au format JSON, comme indiqué ci-dessous.

    {
        "time": "2015-09-11T23:14:22.6940000Z",
        "systemId": "e22a0996-e5a7-4952-8e28-4357a6e8f0c5",
        "category": "NetworkSecurityGroupRuleCounter",
        "resourceId": "/SUBSCRIPTIONS/D763EE4A-9131-455F-8C5E-876035455EC4/RESOURCEGROUPS/INSIGHTOBONRP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/NSGINSIGHTOBONRP",
        "operationName": "NetworkSecurityGroupCounters",
        "properties": {
            "vnetResourceGuid":"{DD0074B1-4CB3-49FA-BF10-8719DFBA3568}",
            "subnetPrefix":"10.0.0.0/24",
            "macAddress":"001517D9C43C",
            "ruleName":"DenyAllOutBound",
            "direction":"Out",
            "type":"block",
            "matchedConnections":0
            }
    }

## <a name="event-log"></a>Journal des événements
Ce journal est généré uniquement si vous l’avez activé sur une base par NSG comme décrit ci-dessus. Les données sont stockées dans le compte de stockage que vous avez spécifié lorsque vous avez activé la journalisation. Les données suivantes sont enregistrées :

    {
        "time": "2015-09-11T23:05:22.6860000Z",
        "systemId": "e22a0996-e5a7-4952-8e28-4357a6e8f0c5",
        "category": "NetworkSecurityGroupEvent",
        "resourceId": "/SUBSCRIPTIONS/D763EE4A-9131-455F-8C5E-876035455EC4/RESOURCEGROUPS/INSIGHTOBONRP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/NSGINSIGHTOBONRP",
        "operationName": "NetworkSecurityGroupEvents",
        "properties": {
            "vnetResourceGuid":"{DD0074B1-4CB3-49FA-BF10-8719DFBA3568}",
            "subnetPrefix":"10.0.0.0/24",
            "macAddress":"001517D9C43C",
            "ruleName":"AllowVnetOutBound",
            "direction":"Out",
            "priority":65000,
            "type":"allow",
            "conditions":{
                "destinationPortRange":"0-65535",
                "sourcePortRange":"0-65535",
                "destinationIP":"10.0.0.0/8,172.16.0.0/12,169.254.0.0/16,192.168.0.0/16,168.63.129.16/32",
                "sourceIP":"10.0.0.0/8,172.16.0.0/12,169.254.0.0/16,192.168.0.0/16,168.63.129.16/32"
            }
        }
    }

## <a name="view-and-analyze-the-audit-log"></a>Permet d’afficher et d’analyser le journal d’audit
Vous pouvez afficher et analyser les données du journal d’audit à l’aide d’une des méthodes suivantes :

- **Outils azure :** Récupérer des informations à partir des journaux d’audit par le biais de PowerShell d’Azure, Azure Command Line Interface (CLI), l’API REST Azure ou le portail Azure aperçu.  Instructions détaillées pour chaque méthode sont détaillées dans l’article [opérations de vérification avec le Gestionnaire de ressources](../resource-group-audit.md) .
- **D’alimentation BI :** Si vous ne disposez pas d’un compte [d’Analyse Décisionnelle d’alimentation](https://powerbi.microsoft.com/pricing) , vous pouvez l’essayer gratuitement. En utilisant le [pack de puissance BI du contenu des journaux d’Audit Azure](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/) vous pouvez analyser vos données avec des tableaux de bord préconfigurés que vous pouvez utiliser comme-, ou personnaliser.

## <a name="view-and-analyze-the-counter-and-event-log"></a>Permet d’afficher et d’analyser le compteur, un journal des événements

Azure [Journal Analytique](../log-analytics/log-analytics-azure-networking-analytics.md) peut collecter le compteur de journal des événements des fichiers et à partir de votre compte de stockage Blob et inclut des visualisations et des fonctions de recherche puissantes pour analyser vos journaux.

Vous pouvez également vous connecter à votre compte de stockage et récupérer les entrées du journal pour les journaux des événements et des compteurs JSON. Après avoir téléchargé les fichiers JSON, vous pouvez les convertir en CSV et les afficher dans Excel, PowerBI ou tout autre outil de visualisation de données.

>[AZURE.TIP] Si vous êtes familiarisé avec les concepts de base de la modification des valeurs de constantes et variables dans C# et de Visual Studio, vous pouvez utiliser les [Outils de conversion de journal](https://github.com/Azure-Samples/networking-dotnet-log-converter) disponible à partir de Github.

## <a name="next-steps"></a>Étapes suivantes

- Visualiser le compteur et les journaux des événements [Analytique du journal](../log-analytics/log-analytics-azure-networking-analytics.md)
- Billet de blog de [visualiser vos journaux d’Audit Azure avec alimentation BI](http://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) .
- [Afficher et analyser les journaux d’Audit d’Azure dans BI de puissance et bien plus encore](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) billet de blog.
