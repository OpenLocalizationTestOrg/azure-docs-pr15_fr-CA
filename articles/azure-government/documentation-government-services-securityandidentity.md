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
    ms.date="10/12/2016"
    ms.author="ryansoc"/>


#  <a name="azure-government-security-and-identity"></a>Identité et sécurité des administrations azure

##  <a name="key-vault"></a>Chambre forte de clé
Pour plus d’informations sur ce service et son utilisation, consultez la <a href="https://azure.microsoft.com/documentation/services/key-vault">documentation publique d’Azure clé coffre-fort.</a>

### <a name="data-considerations"></a>Considérations sur les données
Les informations suivantes identifient la frontière Azure gouvernement pour Azure clé coffre-fort :

| Données réglementé/contrôlé autorisé | Données réglementé/contrôlé non autorisé |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Toutes les données cryptées avec une clé de coffre-fort de clé Azure peuvent contenir des données/réglementés. | Les métadonnées de stockage en chambre forte de clé Azure ne sont pas autorisée à contenir des données d’exportation contrôlée. Ces métadonnées incluent toutes les données de configuration saisies lors de la création et la gestion de votre stockage en chambre forte de clé.  N’entrez pas réglementés/contrôle des données dans les champs suivants : noms de groupes de ressources, les noms de clé coffre-fort, nom de l’abonnement |

Clé de stockage en chambre forte est généralement disponible dans Azure gouvernement. Comme dans le public, est sans extension, coffre-fort de clé est uniquement disponible par le biais de PowerShell et CLI.

## <a name="next-steps"></a>Étapes suivantes

Pour des informations supplémentaires et des mises à jour, abonnez-vous à la <a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure gouvernement Blog.</a>
