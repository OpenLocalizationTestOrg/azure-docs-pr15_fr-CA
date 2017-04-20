<properties
    pageTitle="Documentation de gouvernement Azure | Microsoft Azure"
    description="Cela fournit une comparaison des fonctionnalités et des conseils sur le développement d’applications pour Azure gouvernement"
    services="Azure-Government"
    cloud="gov"
    documentationCenter=""
    authors="ryansoc"
    manager="zakramer"
    editor=""/>

<tags
    ms.service="multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="azure-government"
    ms.date="10/18/2016"
    ms.author="ryansoc"/>


#  <a name="azure-government-databases"></a>Bases de données gouvernementales Azure

##  <a name="sql-database"></a>Base de données SQL

Pour plus d’informations sur la configuration de visibilité des métadonnées et des meilleures pratiques de protection, voir le<a href="https://msdn.microsoft.com/en-us/library/bb510589.aspx"> Centre de sécurité Microsoft pour le moteur de base de données SQL</a> et de la [Documentation publique des base de données SQL Azure](https://azure.microsoft.com/documentation/services/sql-database/) .

### <a name="variations"></a>Variations

Base de données de SQL V12 sont généralement disponible dans Azure.

L’adresse des serveurs de SQL Azure dans Azure gouvernement est différente :

Type de service|Public Azure|Gouvernement Azure
---|---|---
Base de données SQL|*. database.windows.net|*. database.usgovcloudapi.net

### <a name="considerations"></a>Considérations relatives à la

Les informations suivantes identifient la frontière Azure gouvernement pour Azure SQL :

| Données réglementé/contrôlé autorisé | Données réglementé/contrôlé non autorisé |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Toutes les données stockées et traitées dans Microsoft Azure SQL peuvent contenir des données de gouvernement d’Azure réglementés. Vous devez utiliser les outils de base de données pour le transfert de données réglementé Azure gouvernement des données. | Les métadonnées SQL Azure ne sont pas autorisée à contenir des données d’exportation contrôlée. Ces métadonnées incluent toutes les données de configuration saisies lors de la création et la gestion de votre produit de stockage.  N’entrez pas réglementée/données dans les champs suivants : nom, nom d’abonnement, groupes de ressources, nom du serveur, connexion d’admin Server, noms de déploiement, les noms de ressources, les balises de ressources de la base de données

## <a name="azure-redis-cache"></a>Cache d’Azure Redis

Pour plus d’informations sur ce service et son utilisation, consultez la [documentation de public de Cache Redis d’Azure](https://azure.microsoft.com/documentation/services/redis-cache/).

### <a name="variations"></a>Variations

L’URL pour l’accès et la gestion de Cache de Redis d’Azure dans Azure gouvernement sont différentes :

Type de service|Public Azure|Gouvernement Azure
---|---|---
Point de terminaison du cache|*. redis.cache.windows.net|*. redis.cache.usgovcloudapi.net
Azure portal|https://Portal.Azure.com|https://Portal.Azure.us

>[AZURE.NOTE] Tous vos scripts et le code doit prendre en compte les environnements et les points de terminaison appropriés. Pour plus d’informations, voir [comment se connecter au nuage du gouvernement Azure](../redis-cache/cache-howto-manage-redis-cache-powershell.md#how-to-connect-to-azure-government-cloud-or-azure-china-cloud).


### <a name="considerations"></a>Considérations relatives à la

Les informations suivantes identifient la frontière Azure gouvernement pour Cache de Redis Azure :

| Données réglementé/contrôlé autorisé | Données réglementé/contrôlé non autorisé |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Toutes les données stockées et traitées dans le Cache de Redis Azure peuvent contenir des données de gouvernement d’Azure réglementés. | Azure métadonnées de Redis le Cache ne sont pas autorisée à contenir des données d’exportation contrôlée. N’entrez pas réglementée/données dans les champs suivants : nom, nom VNET, nom de l’abonnement, groupes de ressources, les balises de ressources, les propriétés Redis en Cache.  

##  <a name="next-steps"></a>Étapes suivantes

Pour des informations supplémentaires et des mises à jour, vous abonner à la <a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure gouvernement Blog.</a>
