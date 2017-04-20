<properties 
   pageTitle="Surveiller les accès et les journaux et la métrique pour la passerelle d’Application | Microsoft Azure"
   description="Découvrez comment activer et gérer les journaux d’accès et de performances pour la passerelle d’Application"
   services="application-gateway"
   documentationCenter="na"
   authors="amitsriva"
   manager="rossort"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags 
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/26/2016"
   ms.author="amitsriva" />

# <a name="diagnostics-logging-and-metrics-for-application-gateway"></a>Enregistrement des diagnostics et la métrique pour la passerelle d’Application

Azure permet de surveiller les ressources, l’enregistrement et les mesures

[**Journalisation**](#enable-logging-with-powershell) - enregistrement permet de performances, les accès et les autres journaux d’être enregistrées ou consommés à partir d’une ressource pour la surveillance.

[**Mesures**](#metrics) - passerelle d’Application actuellement a une mesure. Cette mesure mesure le débit de la passerelle d’application en octets par seconde.

Vous pouvez utiliser différents types de journaux dans Azure pour gérer et dépanner les passerelles d’application. Certains de ces fichiers journaux sont accessibles via le portail, et tous les journaux peuvent être extraits à partir d’un stockage blob Azure et affichés dans les différents outils, tels que le [Journal Analytique](../log-analytics/log-analytics-azure-networking-analytics.md), Excel et PowerBI. Vous pouvez en savoir plus sur les différents types de journaux à partir de la liste suivante :

- **Des journaux d’audit :** Vous pouvez utiliser [Les journaux d’Audit Azure](../monitoring-and-diagnostics/insights-debugging-with-events.md) (anciennement appelé les journaux des opérations) permet d’afficher toutes les opérations à votre abonnement Azure et de leur statut. Les journaux d’audit sont activés par défaut et peuvent être affichés dans le portail Azure aperçu.
- **Accéder aux journaux :** Vous pouvez utiliser ce journal pour afficher le modèle de passerelle d’accès d’application et d’analyser les importantes informations y compris IP l’appelant, URL demandée, latence de la réponse, code de retour, octets d’entrée et de sortie. Journal d’accès est collectée toutes les 300 secondes. Ce journal contient un enregistrement par instance de passerelle d’application. L’instance passerelle d’application peut être identifié par propriété de « ID d’instance ».
- **Journaux de performances :** Vous pouvez utiliser ce journal pour afficher les performances des instances de passerelle d’application. Ce journal capture des données de performance sur par instance, y compris la demande totale pris en charge, débit, en octets, le nombre total de demandes traitées, nombre de demandes qui ont échoué, nombre d’instances de back-end et incorrects. Journal de performance est collectée toutes les 60 secondes.
- **Les journaux du pare-feu :** Vous pouvez utiliser ce journal pour afficher les demandes qui sont connectés via le mode de détection ou de prévention d’une passerelle d’application qui est configuré avec le pare-feu pour applications web.

>[AZURE.WARNING] Les journaux sont disponibles uniquement pour les ressources déployées dans le modèle de déploiement du Gestionnaire de ressources. Vous ne pouvez pas utiliser des journaux pour les ressources dans le modèle de déploiement classique. Pour mieux comprendre les deux modèles, faire référence à l’article [déploiement de présentation du Gestionnaire de ressources et de déploiement classique](../resource-manager-deployment-model.md) .

## <a name="enable-logging-with-powershell"></a>Activer la journalisation avec PowerShell

L’enregistrement d’audit est activée automatiquement pour chaque ressource de gestionnaire de ressources. Vous devez activer accès et enregistrement pour commencer à collecter les données disponibles par le biais des journaux de performances. Pour activer la journalisation, consultez les étapes suivantes : 

1. Notez l’ID de ressource de votre compte de stockage, où sont stockées les données de journal. Il s’agit de la forme : /subscriptions/\<subscriptionId\>/resourceGroups/\<nom de groupe de ressource\>/providers/Microsoft.Storage/storageAccounts/\<nom de compte de stockage\>. N’importe quel compte de stockage de votre abonnement peut être utilisé. Le portail d’aperçu vous permet de trouver ces informations.

    ![Afficher un aperçu du portail - Diagnostics de passerelle d’Application](./media/application-gateway-diagnostics/diagnostics1.png)

2. Remarque Les ID de ressource de la passerelle de votre application pour laquelle la journalisation doit être activé. Il s’agit de la forme : /subscriptions/\<subscriptionId\>/resourceGroups/\<nom de groupe de ressource\>/providers/Microsoft.Network/applicationGateways/\<nom de passerelle d’application\>. Le portail d’aperçu vous permet de trouver ces informations.

    ![Afficher un aperçu du portail - Diagnostics de passerelle d’Application](./media/application-gateway-diagnostics/diagnostics2.png)

3. Activer la journalisation de diagnostics à l’aide de l’applet de commande powershell suivante :

        Set-AzureRmDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name> -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> -Enabled $true  

>[AZURE.INFORMATION] les journaux d’Audit ne nécessitent pas un compte de stockage distinct. L’utilisation du stockage pour l’enregistrement des performances et de l’accès entraîne des frais de service.

## <a name="enable-logging-with-azure-portal"></a>Activer la journalisation avec Azure portal

### <a name="step-1"></a>Étape 1

Accédez à la ressource dans le portail Azure. Cliquez sur **journaux de Diagnostic**. S’il s’agit de la première configuration des diagnostics la lame ressemble à l’image suivante :

Pour la passerelle d’application, 3 journaux sont disponibles.

- Journal d’accès
- Journal de performances
- Journal du pare-feu

Cliquez sur **Activer les diagnostics** pour démarrer la collecte de données.

![lame de paramètre de Diagnostics][1]

### <a name="step-2"></a>Étape 2

Sur la lame de **paramètres de tests de diagnostic** , les paramètres sur la façon des journaux de diagnostic sont définis. Dans cet exemple, analytique de journal est utilisé pour stocker les journaux. Sous **Journal Analytique** pour configurer votre espace de travail, cliquez sur **configurer** . Concentrateurs d’événement et un compte de stockage peuvent servir à enregistrer les journaux de diagnostics ainsi.

![lame de Diagnostics][2]

### <a name="step-3"></a>Étape 3

Choisissez un espace de travail OMS existant ou créer un nouveau. Pour cet exemple, une existante est utilisée.

![espaces de travail OMS][3]

### <a name="step-4"></a>Étape 4

Lorsque vous avez terminé, confirmez les paramètres, puis cliquez sur **Enregistrer** pour enregistrer les paramètres.

![Confirmer la sélection][4]

## <a name="audit-log"></a>Journal d’audit

Par défaut, ce journal (anciennement appelé le « journal opérationnel ») est généré par Azure.  Les journaux sont conservés pendant 90 jours à banque de journaux d’événements d’Azure. Plus d’informations sur ces journaux en lisant l’article [afficher des événements et des journaux d’audit](../monitoring-and-diagnostics/insights-debugging-with-events.md) .

## <a name="access-log"></a>Journal d’accès

Ce journal est généré uniquement si vous l’avez activé sur une base de passerelle d’Application comme indiqué dans les étapes précédentes. Les données sont stockées dans le compte de stockage que vous avez spécifié lorsque vous avez activé la journalisation. Chaque accès de passerelle d’Application est enregistré au format JSON, comme illustré dans l’exemple suivant :

    {
        "resourceId": "/SUBSCRIPTIONS/<subscription id>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/<application gateway name>",
        "operationName": "ApplicationGatewayAccess",
        "time": "2016-04-11T04:24:37Z",
        "category": "ApplicationGatewayAccessLog",
        "properties": {
            "instanceId":"ApplicationGatewayRole_IN_0",
            "clientIP":"37.186.113.170",
            "clientPort":"12345",
            "httpMethod":"HEAD",
            "requestUri":"/xyz/portal",
            "requestQuery":"",
            "userAgent":"-",
            "httpStatus":"200",
            "httpVersion":"HTTP/1.0",
            "receivedBytes":"27",
            "sentBytes":"202",
            "timeTaken":"359",
            "sslEnabled":"off"
        }
    }

## <a name="performance-log"></a>Journal de performances

Ce journal est généré uniquement si vous l’avez activé sur une base de passerelle d’Application comme indiqué dans les étapes précédentes. Les données sont stockées dans le compte de stockage que vous avez spécifié lorsque vous avez activé la journalisation. Les données suivantes sont enregistrées :

    {
        "resourceId": "/SUBSCRIPTIONS/<subscription id>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/<application gateway name>",
        "operationName": "ApplicationGatewayPerformance",
        "time": "2016-04-09T00:00:00Z",
        "category": "ApplicationGatewayPerformanceLog",
        "properties": 
        {
            "instanceId":"ApplicationGatewayRole_IN_1",
            "healthyHostCount":"4",
            "unHealthyHostCount":"0",
            "requestCount":"185",
            "latency":"0",
            "failedRequestCount":"0",
            "throughput":"119427"
        }
    }


## <a name="firewall-log"></a>Journal du pare-feu

Ce journal est généré uniquement si vous l’avez activé sur une base de passerelle d’application comme indiqué dans les étapes précédentes. Ce journal nécessite également de ce pare-feu d’application web soit configuré sur une passerelle d’application. Les données sont stockées dans le compte de stockage que vous avez spécifié lorsque vous avez activé la journalisation. Les données suivantes sont enregistrées :

    {
        "resourceId": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/<applicationGatewayName>",
        "operationName": "ApplicationGatewayFirewall",
        "time": "2016-09-20T00:40:04.9138513Z",
        "category": "ApplicationGatewayFirewallLog",
        "properties":     {
            "instanceId":"ApplicationGatewayRole_IN_0",
            "clientIp":"108.41.16.164",
            "clientPort":1815,
            "requestUri":"/wavsep/active/RXSS-Detection-Evaluation-POST/",
            "ruleId":"OWASP_973336",
            "message":"XSS Filter - Category 1: Script Tag Vector",
            "action":"Logged",
            "site":"Global",
            "message":"XSS Filter - Category 1: Script Tag Vector",
            "details":{"message":" Warning. Pattern match "(?i)(<script","file":"/owasp_crs/base_rules/modsecurity_crs_41_xss_attacks.conf","line":"14"}}
    }

## <a name="view-and-analyze-the-audit-log"></a>Permet d’afficher et d’analyser le journal d’audit

Vous pouvez afficher et analyser les données du journal d’audit à l’aide d’une des méthodes suivantes :

- **Outils azure :** Récupérer des informations à partir des journaux d’audit par le biais de PowerShell d’Azure, Azure Command Line Interface (CLI), l’API REST Azure ou le portail Azure aperçu.  Instructions détaillées pour chaque méthode sont détaillées dans l’article [opérations de vérification avec le Gestionnaire de ressources](../resource-group-audit.md) .
- **D’alimentation BI :** Si vous ne disposez pas d’un compte [d’Analyse Décisionnelle d’alimentation](https://powerbi.microsoft.com/pricing) , vous pouvez l’essayer gratuitement. En utilisant le [pack de puissance BI du contenu des journaux d’Audit Azure](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/) vous pouvez analyser vos données avec des tableaux de bord préconfigurés que vous pouvez utiliser comme-, ou personnaliser.

## <a name="view-and-analyze-the-access-performance-and-firewall-log"></a>Permet d’afficher et d’analyser le journal des accès, des performances et des pare-feu

Azure [Journal Analytique](../log-analytics/log-analytics-azure-networking-analytics.md) peut collecter le compteur de journal des événements des fichiers et à partir de votre compte de stockage Blob et inclut des visualisations et des fonctions de recherche puissantes pour analyser vos journaux.

Vous pouvez également vous connecter à votre compte de stockage et récupérer les entrées de journal JSON pour l’accès et les journaux. Après avoir téléchargé les fichiers JSON, vous pouvez les convertir en CSV et les afficher dans Excel, PowerBI ou tout autre outil de visualisation de données.

>[AZURE.TIP] Si vous êtes familiarisé avec les concepts de base de la modification des valeurs de constantes et variables dans C# et de Visual Studio, vous pouvez utiliser les [Outils de conversion de journal](https://github.com/Azure-Samples/networking-dotnet-log-converter) disponible à partir de Github.

## <a name="metrics"></a>Mesures

Mesures sont une fonctionnalité de certaines ressources Azure où vous pouvez afficher les compteurs de performance dans le portail. Pour la passerelle d’Application, une mesure est disponible au moment de la rédaction de cet article. Cette métrique est le débit et peut être affichée dans le portail. Accédez à une passerelle d’application et cliquez sur **mesures**.  Dans la section **statistiques disponibles** pour afficher les valeurs, sélectionnez le débit. Dans l’image ci-dessous, vous pouvez voir un exemple avec les filtres qui peuvent être utilisés pour afficher les données dans les plages de temps différentes.

Pour afficher la liste de l’actuel prend en charge des mesures, visitez [les mesures prises en charge avec moniteur d’Azure](../monitoring-and-diagnostics/monitoring-supported-metrics.md)

![mode de mesure][5]

## <a name="alert-rules"></a>Règles d’alerte

Règles d’alerte peuvent être démarrés sur la base des mesures sur une ressource. Cela signifie que pour la passerelle d’application, une alerte peut appeler un webhook ou un administrateur de messagerie si le débit de la passerelle d’application est au-dessus ou au-dessous ou à un seuil pour une période donnée.

L’exemple suivant vous guidera dans la création d’une règle d’alerte qui envoie un message électronique à un administrateur après qu’un seuil de débit n’a pas été respecté.

### <a name="step-1"></a>Étape 1

Cliquez sur **Ajouter une alerte métrique** pour démarrer. Cette blade est également accessible à partir de la blade de mesures.

![lame de règles d’alerte][6]

### <a name="step-2"></a>Étape 2

De la lame **d’Ajouter une règle** , remplissez le nom, la condition et notifier les sections et cliquez sur **OK** lorsque vous avez terminé.

Le sélecteur de **Condition** permettant de 4 valeurs, **supérieur à**, **supérieur ou égal**, **inférieur à**ou **inférieur ou égal à**.

Le sélecteur de la **période** , permet le prélèvement d’une période de 5 minutes à 6 heures.

En sélectionnant des **lecteurs, collaborateurs et les propriétaires de messagerie** l’e-mail peut être dynamique basé sur les utilisateurs qui ont accès à cette ressource. Dans le cas contraire, une liste séparée par des virgules des utilisateurs peut être fournie dans la zone de texte **administrateur supplémentaires email(s)** .

![ajouter des lames de règle][7]

Si le seuil de violation, un courrier électronique arrive semblable à celle dans l’image suivante :

![e-mail de violation de seuil][8]

Une liste d’alertes s’affiche une fois qu’une alerte de mesure a été créée et fournit une vue d’ensemble de toutes les règles d’alerte.

![affichage de la règle d’alerte][9]

Pour en savoir plus sur les notifications d’alerte, consultez [notifications d’alerte réception](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

Pour en savoir plus sur les webhooks et comment vous pouvez les utiliser avec les alertes, consultez [configurer une webhook sur une alerte métrique Azure](../monitoring-and-diagnostics/insights-webhooks-alerts.md)

## <a name="next-steps"></a>Étapes suivantes

- Visualiser le compteur et les journaux des événements [Analytique du journal](../log-analytics/log-analytics-azure-networking-analytics.md) 
- Billet de blog de [visualiser vos journaux d’Audit Azure avec alimentation BI](http://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) .
- [Afficher et analyser les journaux d’Audit d’Azure dans BI de puissance et bien plus encore](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) billet de blog.

[1]: ./media/application-gateway-diagnostics/figure1.png
[2]: ./media/application-gateway-diagnostics/figure2.png
[3]: ./media/application-gateway-diagnostics/figure3.png
[4]: ./media/application-gateway-diagnostics/figure4.png
[5]: ./media/application-gateway-diagnostics/figure5.png
[6]: ./media/application-gateway-diagnostics/figure6.png
[7]: ./media/application-gateway-diagnostics/figure7.png
[8]: ./media/application-gateway-diagnostics/figure8.png
[9]: ./media/application-gateway-diagnostics/figure9.png