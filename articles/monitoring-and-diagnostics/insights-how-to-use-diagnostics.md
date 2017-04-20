<properties
    pageTitle="Surveillance de l’activer et diagnostics dans Microsoft Azure | Microsoft Azure "
    description="Apprenez à configurer les tests de diagnostic pour vos ressources dans Azure."
    authors="rboucher"
    manager="carolz"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/08/2015"
    ms.author="robb"/>

# <a name="enable-monitoring-and-diagnostics"></a>Activer l’analyse et diagnostic

Dans le [Portail Azure](https://portal.azure.com), vous pouvez configurer des riches et fréquentes, de surveillance et de données de diagnostic concernant vos ressources. Vous pouvez également utiliser [l’API REST](https://msdn.microsoft.com/library/azure/dn931932.aspx) ou le [Kit de développement .NET](https://www.nuget.org/packages/Microsoft.Azure.Insights/) pour configurer par programme des tests de diagnostic.

Les données de diagnostic, de surveillance et de mesure dans Azure sont enregistrées dans un compte de stockage de votre choix. Cela vous permet d’utiliser les outils vous souhaitez lire les données, à partir d’un Explorateur de stockage, à BI de puissance pour des outils de tiers.

## <a name="when-you-create-a-resource"></a>Lorsque vous créez une ressource

La plupart des services vous permettent d’activer les diagnostics lorsque vous les créez dans le [Portail Azure](https://portal.azure.com).

1. Accédez à **Nouveau** et choisissez la ressource qui que vous intéressez.

2. Sélectionnez **configuration facultative**.
    ![Lame de Diagnostics](./media/insights-how-to-use-diagnostics/Insights_CreateTime.png)

3. Sélectionnez les **tests de diagnostic**, puis cliquez **sur**. Vous devez choisir le compte de stockage que vous souhaitez les diagnostics à enregistrer. Vous seront facturés débit normal pour le stockage et les transactions lorsque vous envoyez des diagnostics pour un compte de stockage.

4. Cliquez sur **OK** et créer la ressource.

## <a name="change-settings-for-an-existing-resource"></a>Modifier les paramètres d’une ressource existante

Si vous avez déjà créé une ressource et que vous souhaitez modifier les paramètres de diagnostic (pour modifier le niveau de collecte des données, par exemple), vous pouvez effectuer ce droit dans le portail Azure.

1. Accéder à la ressource, puis cliquez sur la commande de **paramètres** .

2. Sélectionnez les **tests de diagnostic**.

3. La lame de **diagnostic** a tous les diagnostics possibles et l’analyse de données de collection pour cette ressource. Pour certaines ressources, vous pouvez également choisir une stratégie de **rétention** pour les données, pour nettoyer à partir de votre compte de stockage.
    ![Diagnostics de stockage](./media/insights-how-to-use-diagnostics/Insights_StorageDiagnostics.png)

4. Une fois que vous avez choisi vos paramètres, cliquez sur la commande **Enregistrer** . Il peut prendre un peu tandis que pour l’analyse des données pour s’affichent si vous l’activez pour la première fois.

### <a name="categories-of-data-collection-for-virtual-machines"></a>Catégories de collecte des données pour les machines virtuelles
Pour les machines virtuelles tous les journaux et les mesures seront enregistrées à intervalles d’une minute, vous pouvez faire toujours les informations les plus récentes sur votre ordinateur.

- **Les mesures de base** : les mesures de fonctionnement sur votre machine virtuelle telles que le processeur et la mémoire
- **Réseau et web des mesures** : les mesures sur vos connexions réseau et les services web
- **Les mesures de .NET** : mesures sur les applications .NET et ASP.NET en cours d’exécution sur votre ordinateur virtuel
- **Les mesures de SQL** : Si vous exécutez Microsoft SQL Service, ses performances
- **Journaux des événements application Windows** : les événements de Windows qui sont envoyés à la couche application
- **Journaux des événements système Windows** : des événements de Windows qui sont envoyés sur le canal système. Cela inclut également tous les événements du [Logiciel anti-programme malveillant de Microsoft](http://go.microsoft.com/fwlink/?LinkID=404171&clcid=0x409).
- **Journaux des événements sécurité Windows** : des événements de Windows qui sont envoyées sur le canal de sécurité
- **Journaux de diagnostics infrastructure** : enregistrement sur l’infrastructure de collecte des diagnostics
- **Les journaux IIS** : journaux sur votre serveur IIS

Notez que certaines distributions de Linux à ce moment ne sont pas pris en charge, et l’Agent de l’invité doit être installé sur l’ordinateur virtuel.

## <a name="next-steps"></a>Étapes suivantes

* [Recevoir des notifications alertes](insights-receive-alert-notifications.md) chaque fois que se produisent les événements opérationnels ou métriques traverser un seuil.
* [Métriques de service Moniteur](insights-how-to-customize-monitoring.md) pour vous assurer que votre service est disponible et la réactivité.
* [Mettre à l’échelle nombre d’instances automatiquement](insights-how-to-scale.md) pour vous assurer que votre échelle de service basé sur la demande.
* [Performances de l’application Moniteur](../application-insights/app-insights-azure-web-apps.md) si vous souhaitez comprendre exactement comment votre code s’exécute dans le nuage.
* [Afficher les événements et les journaux d’audit](insights-debugging-with-events.md) pour découvrir tout ce qui est arrivé dans votre service.
* [État de service de suivi](insights-service-health.md) pour savoir quand Azure a rencontré des interruptions de service ou une dégradation des performances.
