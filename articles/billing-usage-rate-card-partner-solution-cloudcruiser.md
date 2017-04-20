<properties
   pageTitle="Cruiser et facturation d’intégration de l’API de Microsoft Azure en nuage | Microsoft Azure"
   description="Fournit une perspective unique de facturation de Microsoft Azure partenaire Cruiser de nuage, sur leurs expériences, intégration de l’API de facturation Azure dans leurs produits.  Ceci est particulièrement utile pour les clients d’Azure et le nuage Cruiser qui sont intéressés à l’aide de/lors de la Cruiser de nuage de Microsoft Azure Pack."
   services=""
   documentationCenter=""
   authors="BryanLa"
   manager="mbaldwin"
   editor=""
   tags="billing"
   />

<tags
   ms.service="billing"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="billing"
   ms.date="09/08/2016"
   ms.author="mobandyo;sirishap;bryanla"/>

# <a name="cloud-cruiser-and-microsoft-azure-billing-api-integration"></a>Le cloud Microsoft Azure, intégration de l’API de facturation et Cruiser

Cet article décrit comment les informations collectées à partir de la nouvelle API de facturation Microsoft Azure peuvent servir dans le nuage Cruiser pour analyse et simulation de coût de flux de travail.

## <a name="azure-ratecard-api"></a>API d’Azure RateCard
L’API RateCard fournit des informations de taux d’Azure. Après l’authentification avec les informations d’identification appropriées, vous pouvez interroger l’API pour collecter les métadonnées sur les services disponibles sur Azure, ainsi que le taux associé à votre offre.

L’exemple suivant est un exemple de réponse à partir de l’API indiquant les prix pour le A0 instance (Windows) :

    {
        "MeterId": "0e59ad56-03e5-4c3d-90d4-6670874d7e29",
        "MeterName": "Compute Hours",
        "MeterCategory": "Virtual Machines",
        "MeterSubCategory": "A0 VM (Windows)",
        "Unit": "Hours",
        "MeterRates":
        {
            "0": 0.029
        },
        "EffectiveDate": "2014-08-01T00:00:00Z",
        "IncludedQuantity": 0.0,
        "MeterStatus": "Active"
    },

### <a name="cloud-cruisers-interface-to-azure-ratecard-api"></a>Interface de Cruiser à RateCard Azure API du nuage
Cruiser de nuage peut exploiter les informations de l’API de RateCard de différentes manières. Pour cet article, nous expliquerons comment elle peut être utilisée pour rendre IaaS coût de simulation et analyse de la charge de travail.

Pour illustrer ce cas d’usage, imaginez une charge de travail de plusieurs instances en cours d’exécution sur Microsoft Azure Pack (WAP). L’objectif est de simuler cette même charge de travail sur Azure et estimer les coûts liés à ce type de migration. Pour créer cette simulation, il existe deux tâches principales à effectuer :

1. **Importer et traiter les informations de service collectées à partir de l’API RateCard.** Cette tâche est également effectuée sur les classeurs, où l’extrait de l’API RateCard est transformé et publié vers un nouveau plan de taux. Le nouveau plan de taux permettra sur les simulations d’estimer les prix Azure.

2. **Normaliser les services WAP et Azure pour IaaS.** Par défaut, les services WAP sont basés sur des ressources (processeur, taille de la mémoire, taille du disque, etc.) tout en Azure services sont basés sur la taille d’instance (A0, A1, A2, etc.). Cette première tâche peuvent être exécutées par le moteur ETL de nuage Cruiser, appelée classeurs, où ces ressources sont disponibles sur la taille d’instance, analogue aux services de l’instance d’Azure.

### <a name="import-data-from-the-ratecard-api"></a>Importer des données à partir de l’API RateCard

Nuage Cruiser classeurs permettent de collecter et de traiter les informations à partir de l’API RateCard.  Les classeurs ETL (extract-transform-load) vous permettent de configurer la collection, la transformation et la publication de données dans la base de données Cruiser de nuage.

Chaque classeur peut avoir une ou plusieurs collections, ce qui vous permet de mettre en corrélation les informations provenant de plusieurs sources afin de compléter ou d’enrichir les données d’utilisation. Les deux captures d’écran suivantes montrent comment créer une *collection* dans un classeur existant et importation d’informations dans la *collection* à partir de l’API RateCard :

![Figure 1 : création d’une nouvelle collection][1]

![Figure 2 : importer des données à partir de la nouvelle collection][2]

Après avoir importé les données dans le classeur, il est possible de créer de multiples étapes et des processus de transformation, de modifier et de modéliser les données. Pour cet exemple, étant donné que nous sommes uniquement intéressé dans l’infrastructure-as-a-Service (IaaS), nous pouvons utiliser les étapes de la transformation pour supprimer les lignes inutiles, ou enregistrements, relatives aux services que IaaS.

La capture d’écran suivante montre les étapes de transformation permettant de traiter les données collectées à partir des API de RateCard :

![Figure 3 : étapes de Transformation pour traiter les données collectées à partir des API de RateCard][3]

### <a name="defining-new-services-and-rate-plans"></a>Définition du taux et nouveaux Services de Plans

Il existe différentes façons de définir des services dans le nuage Cruiser. Une des options consiste à importer les services à partir des données d’utilisation. Cette méthode est couramment utilisée lorsque vous travaillez avec des nuages publics, où les services sont déjà définies par le fournisseur.

Un Plan de taux est un ensemble de taux ou le prix qui peuvent être appliqués à différents services, basés sur les dates d’effet ou un groupe de clients, parmi d’autres options. Plans de taux permet également de Cruiser du nuage pour créer la simulation ou scénarios « What-if », afin de comprendre comment les modifications des services peuvent affecter le coût total d’une charge de travail.

Dans cet exemple, nous utiliserons les informations de service à partir de l’API RateCard pour définir de nouveaux services dans le nuage Cruiser. De la même façon, nous pouvons utiliser les taux associés aux services pour créer un nouveau Plan de taux sur Cruiser de nuage.

À la fin du processus de transformation, il est possible de créer une nouvelle étape et publier les données de l’API de RateCard en tant que les taux et les nouveaux services.

![Figure 4 : les données de l’API RateCard de publication en tant que de nouveaux Services et taux][4]

### <a name="verify-azure-services-and-rates"></a>Vérifiez les taux et les Services Azure

Après avoir publié les taux et les services, vous pouvez vérifier la liste des services importés dans l’onglet des *Services* de nuage Cruiser :

![Figure 5 : vérification de nouveaux Services][5]

Sous l’onglet *Plans de taux* , vous pouvez vérifier le nouveau plan de taux appelé « AzureSimulation » avec les taux importée à partir de l’API RateCard.

![Figure 6 : vérification du nouveau Plan de taux et le taux associés][6]

### <a name="normalize-wap-and-azure-services"></a>Normaliser les WAP et Services Azure

Par défaut, WAP fournit des informations d’utilisation en fonction de l’utilisation du calcul, de mémoire et de ressources réseau. Dans le nuage Cruiser, vous pouvez définir vos services basés directement sur l’attribution ou le compteur d’utilisation de ces ressources. Par exemple, vous pouvez définir un taux de base pour chaque heure d’utilisation du processeur, ou facturer les Go de mémoire allouée à une instance.

Pour cet exemple, pour comparer les coûts entre les WAP et Azure, nous devons l’utilisation des ressources sur le WAP en lots, qui peuvent alors être appliqués aux services Azure d’agrégation. Cette transformation peut être facilement implémentée dans les classeurs :

![Figure 7 : transformation des données WAP pour normaliser les services][7]

La dernière étape au niveau du classeur est de publier les données dans la base de données Cruiser de nuage. Au cours de cette étape, les données d’utilisation sont désormais regroupées dans des services (qui correspondent aux Services Azure) et liées à un taux par défaut pour créer les frais.

Après avoir terminé le classeur, vous pouvez automatiser le traitement des données, par l’ajout d’une tâche dans le planificateur et spécification de la fréquence et l’heure pour le classeur à exécuter.

![Figure 8 : planification du classeur][8]

### <a name="create-reports-for-workload-cost-simulation-analysis"></a>Créer des rapports pour l’analyse des coûts de Simulation de la charge de travail

Une fois l’utilisation est collectée et frais sont chargées dans la base de données Cruiser de nuage, nous pouvons exploiter le module nuage Cruiser conseils pour créer des coûts de simulation qui nous souhaitent la charge de travail.

Pour illustrer ce scénario, nous avons créé le rapport suivant :

![Comparaison des coûts][9]

Le graphique du haut indique une comparaison du coût par les services, en comparant le prix de l’exécution de la charge de travail pour chaque service spécifique entre WAP (bleu foncé) et Azure (bleu clair).

Le graphique du bas montre les mêmes données mais ventilés par département. Affiche les coûts pour chaque service exécuter leur charge de travail sur le WAP et Azure, ainsi que la différence entre eux dans la barre d’épargne (vert).

## <a name="azure-usage-api"></a>API d’utilisation Azure


### <a name="introduction"></a>Introduction

Microsoft a récemment introduit l’API de l’utilisation d’Azure, permettant aux abonnés de l’extraire par programme des données d’utilisation pour obtenir des informations sur leur consommation. Il s’agit d’une excellente nouvelle pour les clients Cruiser de nuage qui peuvent tirer parti du jeu de données plus riche disponible par le biais de cette API.

Cruiser de nuage peut tirer parti de l’intégration avec l’API de l’utilisation de plusieurs façons. La granularité (informations horaires d’utilisation) et fournissent des informations de métadonnées de ressources disponibles par le biais de l’API du groupe de données nécessaire pour prendre en charge les modèles flexibles de Showback ou de refacturation. 

Dans ce didacticiel, nous présenterons un exemple de comment les Cruiser de nuage peut bénéficier à partir des informations d’utilisation des API. Plus spécifiquement, nous créer un groupe de ressources sur Azure, associer des balises de la structure de compte, puis décrivez le processus d’extraction et traitement des informations de balise dans le nuage Cruiser.
 
L’objectif final est de pouvoir créer des rapports comme la suivante et être en mesure d’analyser les coûts et la consommation basée sur la structure de compte remplie par les balises.

![Figure 10 : rapport avec des ventilations à l’aide de balises][10]

### <a name="microsoft-azure-tags"></a>Balises de Microsoft Azure

Les données disponibles par le biais de l’API de l’utilisation d’Azure incluent non seulement les informations de consommation, mais également les métadonnées des ressources y compris les balises qui lui est associés. Balises fournissent un moyen simple d’organiser vos ressources, mais pour être efficace, vous devez vous assurer que :

- Les balises sont correctement appliqués aux ressources au moment de la fourniture
- Les balises sont correctement utilisés sur le processus Showback/refacturation pour lier l’utilisation de la structure de compte de l’organisation.

Ces deux exigences est peuvent difficiles, surtout quand un processus manuel sur la fourniture ou le côté de la charge. Balises de faute de frappe, incorrectes ou manquantes même sont des clients se plaignent lorsqu’à l’aide de balises et ces erreurs peut compliquer la vie sur le côté de tarification très.

Avec la nouvelle API d’utilisation Azure, Cruiser de nuage peut extraire des informations de marquage de ressource et via un outil ETL perfectionné appelé classeurs, résoudre les erreurs de balisage courants. Par transformation à l’aide d’expressions régulières et corrélation des données, Cruiser de nuage peut identifier les ressources correctement balisés et appliquer les balises appropriées, garantissant ainsi l’association correcte des ressources avec le consommateur.

Dans la partie en cours de chargement, nuage Cruiser automatise le processus de Showback/refacturation et peuvent exploiter les informations de balise pour lier l’utilisation au consommateur approprié (département, Division, projet, etc.). Cette automatisation fournit une amélioration énorme et garantir un processus de charge cohérent et contrôlable.
 

### <a name="creating-a-resource-group-with-tags-on-microsoft-azure"></a>Création d’un groupe de ressources avec des balises sur Microsoft Azure
La première étape de ce didacticiel est de créer un groupe de ressources dans le portail Azure, puis créer les nouvelles balises à associer aux ressources. Pour cet exemple, nous allons créer les balises suivantes : département, environnement, propriétaire, projet.

La capture d’écran ci-dessous montre un exemple de groupe de ressources avec les balises associées.

![Figure 11 : groupe de ressources avec des balises associées sur Azure portal][11]

L’étape suivante consiste à extraire les informations à partir de l’API de l’utilisation dans le nuage Cruiser. L’API d’utilisation fournit actuellement des réponses au format JSON. Voici un exemple de données récupérées :


    {
      "id": "/subscriptions/bb678b04-0e48-4b44-XXXX-XXXXXXX/providers/Microsoft.Commerce/UsageAggregates/Daily_BRSDT_20150623_0000",
      "name": "Daily_BRSDT_20150623_0000",
      "type": "Microsoft.Commerce/UsageAggregate",
      "properties":
      {
        "subscriptionId": "bb678b04-0e48-4b44-XXXX-XXXXXXXXX",
        "usageStartTime": "2015-06-22T00:00:00+00:00",
        "usageEndTime": "2015-06-23T00:00:00+00:00",
        "meterName": "Compute Hours",
        "meterRegion": "",
        "meterCategory": "Virtual Machines",
        "meterSubCategory": "Standard_D1 VM (Non-Windows)",
        "unit": "Hours",
        "instanceData": "{\"Microsoft.Resources\":{\"resourceUri\":\"/subscriptions/bb678b04-0e48-4b44-XXXX-XXXXXXXX/resourceGroups/DEMOUSAGEAPI/providers/Microsoft.Compute/virtualMachines/MyDockerVM\",\"location\":\"eastus\",\"tags\":{\"Department\":\"Sales\",\"Project\":\"Demo Usage API\",\"Environment\":\"Test\",\"Owner\":\"RSE\"},\"additionalInfo\":{\"ImageType\":\"Canonical\",\"ServiceType\":\"Standard_D1\"}}}",
        "meterId": "e60caf26-9ba0-413d-a422-6141f58081d6",
        "infoFields": {},
        "quantity": 8

      },
    },


### <a name="import-data-from-the-usage-api-into-cloud-cruiser"></a>Importer des données à partir de l’API de l’utilisation dans le nuage Cruiser

Nuage Cruiser classeurs fournissent un moyen automatisé pour collecter et traiter les informations à partir de l’API de l’utilisation. Un classeur de (chargement extrait par transformation) ETL vous permet de configurer la collection, la transformation et la publication de données dans la base de données Cruiser de nuage.

Chaque classeur peut avoir une ou plusieurs collections. Cela vous permet de mettre en corrélation les informations provenant de plusieurs sources afin de compléter ou d’enrichir les données d’utilisation. Pour cet exemple, nous allons créer une nouvelle feuille dans le classeur modèle Azure (_UsageAPI)_ et définir une nouvelle _collection_ pour importer des informations à partir de l’API de l’utilisation.

![Figure 3 : données d’utilisation des API importées dans la feuille de UsageAPI][12]

Notez que ce classeur contient déjà d’autres feuilles pour importer des services Azure (_ImportServices_) et traiter les informations de consommation à partir de l’API de facturation (_PublishData_).

Ensuite nous permet de remplir la feuille de _UsageAPI_ l’utilisation des API et corréler les informations avec les données de consommation de l’API de facturation sur la feuille _PublishData_ .

### <a name="processing-the-tag-information-from-the-usage-api"></a>Traitement des informations de balise de l’API de l’utilisation

Après avoir importé les données dans le classeur, nous allons créer des étapes de transformation dans la feuille de _UsageAPI_ pour traiter les informations à partir de l’API. Première étape consiste à utiliser un processeur « Fractionner JSON » pour extraire les balises à partir d’un seul champ, puis créer des champs pour chacune d’elles (département, projet, propriétaire et environnement).

![Figure 4 : créer des champs pour les informations de balise][13]

Notez les informations de balise (jaune) ne figure pas dans le service « Réseau », mais nous pouvons vérifier qu’il est partie du même groupe de ressources en consultant le champ _ResourceGroupName_ . Puisque nous avons les balises pour les autres ressources à partir de ce groupe de ressources, nous pouvons utiliser ces informations pour appliquer les balises manquantes pour cette ressource plus loin dans le processus.

L’étape suivante consiste à créer une table de recherche associant les informations des balises à l' _ResourceGroupName_. Cette table de recherche sera utilisée à l’étape suivante pour enrichir les données de consommation par des balises.

### <a name="adding-the-tag-information-to-the-consumption-data"></a>Ajouter les informations de balise pour les données de consommation

Maintenant, nous pouvons passer à la feuille _PublishData_ , qui traite les informations de consommation à partir de l’API de facturation et ajouter les champs extraits des balises. Ce processus est exécuté en regardant la table de choix créée à l’étape précédente, à l’aide de la _ResourceGroupName_ comme clé pour les recherches.

![Figure 5 : remplissage de la structure de compte avec les informations de la recherche][14]

Notez que les champs de structure de compte approprié pour le service « Réseau » ont été appliquées, le problème est corrigé avec les balises manquantes. Nous également renseigné les champs de structure de compte pour les ressources autres que notre groupe de ressources cible avec « Autre », afin de les différencier des rapports.

Nous devons maintenant simplement à ajouter une étape pour publier les données d’utilisation. Au cours de cette étape, les taux appropriés pour chaque service défini sur notre Plan de taux seront appliqueront pour les informations d’utilisation, avec les frais qui en résultent chargé dans la base de données.

Le plus intéressant est que vous ne devez effectuer ce processus une seule fois. Lorsque le classeur est terminé, il vous suffit de l’ajouter au planificateur et s’exécutera toutes les heures ou tous les jours à l’heure planifiée. Il s’agit simplement de la création de nouveaux rapports ou personnaliser existants, afin d’analyser les données pour obtenir des informations significatives à partir de votre utilisation de nuage.

### <a name="next-steps"></a>Étapes suivantes

+ Pour obtenir des instructions détaillées sur la création de rapports et les classeurs Cruiser de nuage, consultez online [documentation](http://docs.cloudcruiser.com/) (connexion valide requise du nuage Cruiser).  Pour plus d’informations sur les Cruiser de nuage, veuillez contacter [info@cloudcruiser.com](mailto:info@cloudcruiser.com).
+ Reportez-vous à [intégrer votre consommation de ressources de Microsoft Azure](billing-usage-rate-card-overview.md) pour une vue d’ensemble de l’utilisation des ressources Azure et RateCard APIs.
+ Consultez la [Référence des API reste Azure facturation](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) pour plus d’informations sur les API qui font partie de l’ensemble des API fournies par le Gestionnaire de ressources Azure.
+ Si vous souhaitez plonger directement dans l’exemple de code, consultez notre Microsoft Azure facturation API Code Samples sur des [Échantillons de Code Azure](https://azure.microsoft.com/documentation/samples/?term=billing).

### <a name="learn-more"></a>Pour en savoir plus
+ Consultez l’article [Vue d’ensemble du Gestionnaire de ressources Azure](azure-resource-manager/resource-group-overview.md) pour en savoir plus sur le Gestionnaire de ressources Azure.

<!--Image references-->
 
[1]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Create-New-Workbook-Collection.png "Figure 1 : création d’une nouvelle collection"
[2]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Import-Data-From-RateCard.png "Figure 2 : importer des données à partir de la nouvelle collection"
[3]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Transformation-Steps-Process-RateCard-Data.png "Figure 3 : étapes de Transformation pour traiter les données collectées à partir des API de RateCard"
[4]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Publish-RateCard-Data-New-Services-Rates.png "Figure 4 : les données de l’API RateCard de publication en tant que de nouveaux Services et taux"
[5]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Verify-Azure-Services-And-Pricing1.png "Figure 5 : vérification de nouveaux Services"
[6]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Verify-Azure-Services-And-Pricing2.png "Figure 6 : vérification du nouveau Plan de taux et le taux associés"
[7]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Transforming-WAP-Normalize-Services.png "Figure 7 : transformation des données WAP pour normaliser les services"
[8]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Workbook-Scheduling.png "Figure 8 : planification du classeur"
[9]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Workload-Cost-Simulation-Report.png "Figure 9 : exemple de rapport pour le scénario de comparaison de coût de la charge de travail"
[10]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/1_ReportWithTags.png "Figure 10 : rapport avec des ventilations à l’aide de balises"
[11]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/2_ResourceGroupsWithTags.png "Figure 11 : groupe de ressources avec des balises associées sur Azure portal"
[12]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/3_ImportIntoUsageAPISheet.png "Figure 12 : données d’utilisation des API importées dans la feuille de UsageAPI"
[13]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/4_NewTagField.png "Figure 13 : créer des champs pour les informations de balise"
[14]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/5_PopulateAccountStructure.png "Figure 14 : remplissage de la structure de compte avec les informations de la recherche"
