<properties
   pageTitle="Surveiller les opérations, les événements et les compteurs pour équilibrage de la charge | Microsoft Azure"
   description="Apprenez à activer les événements d’alerte et sonde d’enregistrement de l’état sanitaire de l’équilibreur de charge Azure"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/24/2016"
   ms.author="sewhee" />

# <a name="log-analytics-for-azure-load-balancer-preview"></a>Analytique de journal pour l’équilibreur de charge Azure (aperçu)

Vous pouvez utiliser différents types de journaux dans Azure pour gérer et dépanner les équilibreurs de charge. Certains de ces fichiers journaux sont accessibles via le portail. Tous les journaux peuvent être extraits à partir d’un stockage blob Azure et affichés dans les différents outils, tels que des Excel et PowerBI. Pour plus d’informations sur les différents types de journaux à partir de la liste ci-dessous.

- **Des journaux d’audit :** Vous pouvez utiliser [Les journaux d’Audit Azure](../../articles/monitoring-and-diagnostics/insights-debugging-with-events.md) (anciennement appelé les journaux des opérations) permet d’afficher toutes les opérations à votre abonnement Azure et de leur statut. Les journaux d’audit sont activés par défaut et peuvent être affichés dans le portail Azure.
- **Les journaux des événements d’alerte :** Vous pouvez utiliser ce journal pour afficher les alertes pour l’équilibrage de la charge sont déclenchés. L’état de l’équilibrage de charge est collectée toutes les cinq minutes. Ce journal ne sont écrites si un événement d’alerte équilibrage de la charge est déclenché.
- **Journaux de sonde de santé :** Vous pouvez utiliser ce journal pour vérifier pour sonde à cocher État, combien d’instances est en ligne dans le dorsal équilibreur de charge et le pourcentage d’ordinateurs virtuels reçoit le trafic à partir de l’équilibreur de charge. Ce journal est écrit sur la modification de l’événement état sonde.

>[AZURE.IMPORTANT] Ouvrir une session analytique actuellement ne fonctionne que pour les équilibreurs de charge de côté Internet. Les journaux sont disponibles uniquement pour les ressources déployées dans le modèle de déploiement du Gestionnaire de ressources. Vous ne pouvez pas utiliser des journaux pour les ressources dans le modèle de déploiement classique. Pour plus d’informations sur les modèles de déploiement, consultez [déploiement de présentation du Gestionnaire de ressources et de déploiement classique](../../articles/resource-manager-deployment-model.md).

## <a name="enable-logging"></a>Activer la journalisation

L’enregistrement d’audit est activée automatiquement pour chaque ressource de gestionnaire de ressources. Vous devez activer les événements et la journalisation de sonde de santé pour commencer à collecter les données disponibles par le biais de ces journaux. Utilisez les étapes suivantes pour activer la journalisation.

Ouvrez une session dans le [portail Azure](http://portal.azure.com). Si vous ne disposez pas d’un équilibreur de charge, [créer un équilibreur de charge](load-balancer-get-started-internet-arm-ps.md) avant de continuer.

1. Dans le portail, cliquez sur **Parcourir**.
2. Sélectionnez les **équilibreurs de charge**.

    ![portail - programme d’équilibrage de charge](./media/load-balancer-monitor-log/load-balancer-browse.png)

3. Sélectionnez un équilibreur de charge existant >> **Tous les paramètres**.
4. Sur le côté droit de la boîte de dialogue sous le nom de l’équilibreur de charge, accédez à **l’analyse**, cliquez sur **Diagnostics**.

    ![portail - paramètres de l’équilibrage de la charge](./media/load-balancer-monitor-log/load-balancer-settings.png)

5. Dans le volet des **Diagnostics** , sous **état**, sélectionnez **sur**.
6. Cliquez sur le **compte de stockage**.
7. Sous **journaux**, sélectionnez un compte de stockage existant ou créez-en un nouveau. Utilisez le curseur pour déterminer combien dat d’événement jours est conservés dans les journaux des événements. 8. Cliquez sur **Enregistrer**.

    ![Portail - journaux de diagnostic](./media/load-balancer-monitor-log/load-balancer-diagnostics.png)

>[AZURE.INFORMATION] les journaux d’Audit ne nécessitent pas un compte de stockage distinct. L’utilisation de stockage des événements et de la santé journalisation de sonde entraînera des frais de service.

## <a name="audit-log"></a>Journal d’audit

Le journal d’audit est généré par défaut. Les journaux sont conservés pendant 90 jours à banque de journaux d’événements d’Azure. Plus d’informations sur ces journaux en lisant l’article [afficher des événements et des journaux d’audit](../../articles/monitoring-and-diagnostics/insights-debugging-with-events.md) .

## <a name="alert-event-log"></a>Journal des événements alertes

Ce journal est généré uniquement si vous l’avez activé sur une base d’équilibrage de la charge. Les événements sont enregistrés au format JSON et stockées dans le compte de stockage que vous avez spécifié lorsque vous avez activé la journalisation. Voici un exemple d’événement.

    {
    "time": "2016-01-26T10:37:46.6024215Z",
    "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
    "category": "LoadBalancerAlertEvent",
    "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
    "operationName": "LoadBalancerProbeHealthStatus",
    "properties": {
        "eventName": "Resource Limits Hit",
        "eventDescription": "Ports exhausted",
        "eventProperties": {
            "public ip address": "40.117.227.32"
        }
    }

La sortie JSON montre la propriété *eventname* qui décrit la raison de l’équilibreur de charge créé une alerte. Dans ce cas, l’alerte générée était en raison de l’épuisement du port TCP source IP NAT dû de limites (SNAT).

## <a name="health-probe-log"></a>Journal de sonde de santé

Ce journal est généré uniquement si vous l’avez activé sur une chaque charge équilibrage comme indiqué ci-dessus. Les données sont stockées dans le compte de stockage que vous avez spécifié lorsque vous avez activé la journalisation. Un conteneur nommé 'perspectives-journaux-loadbalancerprobehealthstatus' est créé et les données suivantes sont enregistrées :

    {
        "records":
        {
            "time": "2016-01-26T10:37:46.6024215Z",
            "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
            "category": "LoadBalancerProbeHealthStatus",
            "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
            "operationName": "LoadBalancerProbeHealthStatus",
            "properties": {
                "publicIpAddress": "40.83.190.158",
                "port": "81",
                "totalDipCount": 2,
                "dipDownCount": 1,
                "healthPercentage": 50.000000
            }
        },
        {
            "time": "2016-01-26T10:37:46.6024215Z",
            "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
            "category": "LoadBalancerProbeHealthStatus",
            "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
            "operationName": "LoadBalancerProbeHealthStatus",
            "properties": {
                "publicIpAddress": "40.83.190.158",
                "port": "81",
                "totalDipCount": 2,
                "dipDownCount": 0,
                "healthPercentage": 100.000000
            }
        }
    }

La sortie JSON montre, dans le champ de propriétés, les informations de base pour l’état de santé de sonde. La propriété *dipDownCount* indique le nombre total d’instances sur le back-end qui ne reçoivent pas le trafic du réseau en raison de réponses de sonde a échoué.

## <a name="view-and-analyze-the-audit-log"></a>Permet d’afficher et d’analyser le journal d’audit

Vous pouvez afficher et analyser les données du journal d’audit à l’aide d’une des méthodes suivantes :

- **Outils azure :** Récupérer des informations à partir des journaux d’audit par le biais de PowerShell d’Azure, Azure Command Line Interface (CLI), l’API REST Azure ou le portail Azure aperçu. Instructions détaillées pour chaque méthode sont détaillées dans l’article [opérations de vérification avec le Gestionnaire de ressources](../../articles/resource-group-audit.md) .
- **D’alimentation BI :** Si vous ne disposez pas d’un compte [Courant BI](https://powerbi.microsoft.com/pricing) , vous pouvez l’essayer gratuitement. À l’aide du [pack de puissance BI du contenu des journaux d’Audit Azure](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs), vous pouvez analyser vos données avec des tableaux de bord préconfigurés, ou vous pouvez personnaliser les vues en fonction de vos besoins.

## <a name="view-and-analyze-the-health-probe-and-event-log"></a>Permet d’afficher et d’analyser la sonde de santé et le journal des événements

Vous devez vous connecter à votre compte de stockage et récupérer les entrées du journal pour les journaux des événements et de la santé sonde JSON. Après avoir téléchargé les fichiers JSON, vous pouvez les convertir en CSV et les afficher dans Excel, PowerBI ou tout autre outil de visualisation de données.

>[AZURE.TIP] Si vous êtes familiarisé avec les concepts de base de la modification des valeurs de constantes et variables dans C# et de Visual Studio, vous pouvez utiliser les [Outils de conversion de journal](https://github.com/Azure-Samples/networking-dotnet-log-converter) disponible à partir de Github.

## <a name="additional-resources"></a>Ressources supplémentaires

- Billet de blog de [visualiser vos journaux d’Audit Azure avec alimentation BI](http://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) .
- [Afficher et analyser les journaux d’Audit d’Azure dans BI de puissance et bien plus encore](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) billet de blog.

## <a name="next-steps"></a>Étapes suivantes

[Comprendre les sondes d’équilibrage de la charge](load-balancer-custom-probe-overview.md)
