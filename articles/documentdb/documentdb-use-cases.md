<properties 
    pageTitle="Cas d’usage commun DocumentDB | Microsoft Azure" 
    description="Obtenir des informations sur la partie supérieure cinq exemples d’utilisation pour DocumentDB : utilisateur généré le contenu, l’enregistrement des événements, des données de catalogue, données de préférences utilisateur et Internet des objets (IoT)." 
    services="documentdb" 
    authors="h0n" 
    manager="jhubbard" 
    editor="monicar" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/28/2016" 
    ms.author="hawong"/>

# <a name="common-documentdb-use-cases"></a>Scénarios d’utilisation courants DocumentDB
Cet article fournit une vue d’ensemble de plusieurs cas d’usage courant pour DocumentDB.  Les recommandations de cet article servent de point de départ lorsque vous développez votre application avec DocumentDB.   

Après avoir lu cet article, vous serez en mesure de répondre aux questions suivantes : 
 
- Quels sont les scénarios d’utilisation courants pour DocumentDB ?
- Quels sont les avantages de l’utilisation de DocumentDB en tant qu’utilisateur généré magasin de contenu ?
- Quels sont les avantages de l’utilisation de DocumentDB en tant que données de catalogue stockées ?
- Quels sont les avantages de l’utilisation de DocumentDB sous la forme d’un journal des événements de stockage ?
- Quels sont les avantages de l’utilisation de DocumentDB comme un utilisateur préférences de données stocker ?
- Quels sont les avantages de l’utilisation de DocumentDB comme un magasin de données pour les systèmes de l’Internet des objets (IoT) ?

## <a name="common-use-cases-for-documentdb"></a>Scénarios d’utilisation courants pour DocumentDB
DocumentDB Azure est une base de données NoSQL généraliste qui est utilisé dans un large éventail d’applications et de cas d’usage. Il s’agit d’un bon choix pour toute application qui a besoin de temps de réponse de commande de millisecondes faible et doit pouvoir évoluer rapidement. Voici certains attributs de DocumentDB qui la rendent parfaitement adaptée aux applications de hautes performances.

- DocumentDB des partitions en mode natif les données de disponibilité et d’évolutivité.
- DocumentDB a stockage SSD sauvegardé avec un temps de réponse d’ordre-de-milliseconde de faible latence.
- Prise en charge de DocumentDB pour des niveaux de cohérence comme éventuelle, de session et d’obsolescence délimitée permet de faible coût-à-rapport performances. 
- DocumentDB est un modèle de tarification conviviaux de données flexible mètres indépendamment de stockage et débit.
- Modèle de débit réservé du DocumentDB vous permet de penser en termes de nombre de lectures/écritures au lieu de la mémoire/CPU/IOPs du matériel sous-jacent.
- Conception vous permet de DocumentDB de qu'augmenter de volumes considérables de demande dans l’ordre des milliards de demandes par jour.

Ces attributs sont particulièrement utiles lorsqu’il s’agit de web, mobile, jeux et applications IoT qui ont besoin de temps de réponse faible et avez besoin de gérer des volumes considérables de lectures et d’écritures. 

## <a name="user-generated-content"></a>Contenus générés par l’utilisateur
Un cas d’usage courant pour DocumentDB consiste à stocker et généré l’utilisateur de la requête contenu (UGC) pour les applications web et mobiles, les applications de médias sociaux en particulier.  Voici quelques exemples de UGC sont des sessions de conversation, Tweet, des billets de blog, des évaluations et des commentaires.  Souvent, le UGC dans les applications de support social est un mélange de texte de forme libre, les propriétés, les balises et les relations qui ne sont pas limitées par la structure rigide.   

Contenu tels que les salles de conversation, les commentaires et les publications peuvent être stockées dans DocumentDB sans transformations ou des couches de mappage relationnel objet complexe.  Propriétés de données peuvent être ajoutées ou modifiées facilement pour répondre aux besoins que les développeurs itérer au sein du code de l’application, donc de promouvoir un développement rapide.  

Les applications qui s’intègrent à différents réseaux sociaux doivent répondre à l’évolution des schémas à partir de ces réseaux.  Comme les données sont indexées automatiquement par défaut dans DocumentDB, les données sont prêtes à être interrogé à tout moment.  Par conséquent, ces applications ont la possibilité de récupérer les prévisions en fonction de leurs besoins respectifs.       

La plupart des applications sociales exécutent à l’échelle mondiale et peuvent présenter des modèles d’utilisation imprévisibles.  Souplesse dans la mise à l’échelle de la banque de données est essentielle que la couche application s’adapte pour répondre à la demande d’utilisation.  Vous pouvez évoluer en ajoutant des partitions de données supplémentaires sous un compte DocumentDB.  En outre, vous pouvez également créer des comptes supplémentaires DocumentDB dans plusieurs régions. De disponibilité dans la région DocumentDB service, reportez-vous à la section [Régions d’Azure](https://azure.microsoft.com/regions/#services).   

## <a name="catalog-data"></a>Les données de catalogue
Scénarios d’utilisation de données de catalogue impliquent le stockage et l’interrogation d’un ensemble d’attributs d’entités telles que les personnes, les lieux et les produits.  Comptes d’utilisateurs, des catalogues de produits, les registres de périphérique pour IoT et de systèmes de matériaux sont des exemples de données de catalogue.  Les attributs de ces données peuvent varier et peuvent changer avec le temps en fonction des besoins de l’application.  

Prenons l’exemple d’un catalogue de produits d’un fournisseur de pièces détachées de voiture. Chaque partie peut avoir ses propres attributs, en plus des attributs communs qui partagent de toutes les parties.  En outre, les attributs pour une partie spécifique peuvent changer l’année suivante lors de la libération d’un nouveau modèle.  Comme une banque de documents JSON, DocumentDB prend en charge les schémas flexibles et permet de représenter des données à l’aide des propriétés imbriquées, et il est donc bien adapté pour le stockage des données de catalogue de produits.       

## <a name="logging-and-time-series-data"></a>Journalisation et des données de série chronologique
Journalisation de l’application est souvent émise dans des volumes importants et peuvent ont des attributs variables en fonction de la version de l’application déployée ou les événements d’enregistrement de composant.  Les données du journal ne sont pas limitées par les relations complexes ou des structures rigides. De plus en plus, les données du journal sont rendue persistante au format JSON comme JSON n’est légère et facile pour les êtres humains à lire.
   
Il existe généralement deux cas d’usage principaux liés à des données de journal des événements.  Le premier cas d’utilisation est d’effectuer des requêtes ad-hoc sur un sous-ensemble de données pour la résolution des problèmes.  Pendant le dépannage, un sous-ensemble de données est d’abord récupéré à partir des journaux, généralement par séries temporelles.  Ensuite, un zoom est effectué en filtrant le dataset avec les niveaux d’erreurs ou de messages d’erreur. Il s’agit dans le cas où stocker les journaux d’événements dans DocumentDB est un avantage. Les données de journal stockées dans DocumentDB sont indexées automatiquement par défaut, et par conséquent, il est prêt à être interrogé à tout moment. En outre, les données du journal peuvent être rendues persistantes entre les partitions de données sous la forme d’une série chronologique. Anciens journaux pouvant être déployés vers le stockage à froid par votre stratégie de rétention.          

Le deuxième cas d’utilisation implique de longs travaux d’analytique de données effectuées hors connexion sur un volume important de données de journal.  Analyse de disponibilité de serveur, analyse des erreurs d’application et l’analyse des données de parcours sont des exemples de ce cas d’usage.  En règle générale, les Hadoop est utilisé pour effectuer ces types d’analyses.  Avec le connecteur Hadoop pour DocumentDB, DocumentDB de bases de données fonctionnent en tant que sources de données et de récepteurs pour les travaux de porc, de ruche et de mappage/réduction. Pour plus d’informations sur le connecteur Hadoop pour DocumentDB, consultez [exécution d’une tâche d’Hadoop avec DocumentDB et HDInsight](documentdb-run-hadoop-with-hdinsight.md).      

## <a name="gaming"></a>Jeu
La couche de base de données est un composant essentiel des applications de jeu. Les jeux modernes effectuent le traitement graphique sur les clients mobiles/console, mais s’appuient sur le nuage pour fournir un contenu personnalisé et personnalisé telles que les statistiques du jeu, l’intégration de médias sociaux et ajouterons des scores élevés. Les jeux nécessitent une latence très faible pour les lectures pour fournir un engagement écrit de la partie expérience et la couche de base de données doit gérer les valeurs maximales et minimales dans le taux de demandes lors des lancements de jeu nouvelles et mises à jour de la fonctionnalité.

DocumentDB est utilisé par l’échelle massive des jeux tels que [le parcours morts : les terres de l’homme N°](https://azure.microsoft.com//blog/the-walking-dead-no-mans-land-game-soars-to-1-with-azure-documentdb/) par [Jeux suivant](http://www.nextgames.com/), et [Halo 5 : tuteurs](https://azure.microsoft.com/blog/how-halo-5-guardians-implemented-social-gameplay-using-azure-documentdb/). Dans les deux cas d’utilisation, les avantages clés de DocumentDB étaient les suivants :

- DocumentDB permet de performances à l’échelle vers le haut ou vers le bas ELASTIQUEMENT. Ainsi, les jeux gérer la mise à jour de profil et de statistiques de quelques dizaines à plusieurs millions de joueurs simultanées en effectuant un seul appel d’API.
- DocumentDB prend en charge les millisecondes lit et écrit afin d’éviter les retards pendant le jeu.
- L’indexation automatique de DocumentDB permet le filtrage sur plusieurs propriétés différentes en temps réel, par exemple, recherchez les joueurs par leur ID, le lecteur interne ou leur GameCenter, Facebook, Google ID ou requête basée sur l’appartenance du lecteur dans une guild. Cela est possible sans générer d’indexation complexe ou infrastructure d’ont.
- Fonctionnalités sociales, y compris les nouveaux messages dans le jeu, les appartenances guild lecteur, défis terminées, ajouterons des scores élevés et sociales graphiques sont plus faciles à mettre en œuvre un schéma flexible.
- DocumentDB comme un managé plate-forme-comme-a-service (PaaS) requis gestion installation minimale pour autoriser l’itération rapide de travail et réduire le temps de mise sur le marché.


## <a name="user-preferences-data"></a>Données des préférences utilisateur
Aujourd'hui, les web plus moderne et les applications mobiles sont fournis avec des expériences et des vues complexes. Ces vues et expériences sont généralement dynamiques, la cuisine et de table pour les préférences de l’utilisateur ou l’humeur et besoins de personnalisation.  Par conséquent, les applications ont besoin être en mesure de récupérer efficacement les paramètres personnalisés pour rendre rapidement des expériences et des éléments d’interface utilisateur. 

JSON est un format efficace pour représenter les données de présentation de l’interface utilisateur, tel qu’il est non seulement léger, mais aussi peut être facilement reconnu par JavaScript.  DocumentDB offre des niveaux de cohérence ACCORDABLE qui permettent des lectures rapides avec des écritures de faible latence. Par conséquent, le stockage des données de mise en page de l’interface utilisateur, y compris des paramètres personnalisés en tant que documents JSON dans DocumentDB est un moyen efficace pour obtenir ces données sur le câble.

## <a name="iot-and-device-sensor-data"></a>Données de capteur IoT et dispositif
Cas d’usage IoT partagent généralement certains modèles dans comment ils réceptionner, traitent et stockent des données.  Tout d’abord, ces systèmes permettent d’admission de données qui peut traiter des paquets de données à partir de capteurs de dispositif de différents paramètres régionaux.  Ensuite, ces systèmes de traitent et analyser des données de transmission en continu pour tirer des leçons de temps réel. Et enfin, la plupart si ce n’est pas toutes les données seront finalement débarquer dans un magasin de données d’analytique d’en mode hors connexion et de requête ad hoc.    

Microsoft Azure offre des services qui peuvent être exploitées pour IoT cas d’usage.  Azure IoT services sont un ensemble de services, y compris les concentrateurs d’événement Azure, DocumentDB d’Azure, Azure flux Analytique, Azure Notification Hub, Azure Machine Learning, Azure HDInsight et PowerBI. 

Rafales de données peuvent être ingérés par Azure événement concentrateurs qu’il propose la réception de données haut débit avec une faible latence. Données ingérées qui doivent être traité en temps réel aperçu peuvent être utilisé pour garantir que pour Azure flux Analytique pour l’analytique des temps réel. Données peuvent être chargées dans DocumentDB pour l’interrogation d’ad hoc. Une fois que les données sont chargées dans les DocumentDB, les données sont prêtes à être interrogé.  Les données dans DocumentDB peuvent être utilisées en tant que données de référence dans le cadre du temps réel analytique. En outre, données peuvent encore être raffinées et traitées par connexion des données de DocumentDB à HDInsight pour les travaux de porc, de ruche ou de mappage/réduction.  Données raffinées sont ensuite chargées à DocumentDB pour la création de rapports.   

Pour un exemple de solution IoT à l’aide de DocumentDB, EventHubs et Storm, consultez le [référentiel hdinsight-tempête-exemples sur GitHub](https://github.com/hdinsight/hdinsight-storm-examples/).

Pour plus d’informations sur les offres de IoT Azure, consultez [créer l’Internet des objets de votre](http://www.microsoft.com/en-us/server-cloud/internet-of-things.aspx).

## <a name="next-steps"></a>Étapes suivantes
 
Pour commencer avec DocumentDB, vous pouvez créer un [compte](https://azure.microsoft.com/pricing/free-trial/) et puis suivez nos [cursus](https://azure.microsoft.com/documentation/learning-paths/documentdb/) pour en savoir plus sur les DocumentDB et trouver les informations dont vous avez besoin. 

Ou, si vous souhaitez en savoir plus sur les clients à l’aide de DocumentDB les témoignages de clients suivants sont disponibles :

- [Jeux suivant](https://azure.microsoft.com//blog/the-walking-dead-no-mans-land-game-soars-to-1-with-azure-documentdb/). La mort de cannes : jeu de N° manuel de terres soars à #1 pris en charge par DocumentDB d’Azure.
- [Halo](https://azure.microsoft.com/blog/how-halo-5-guardians-implemented-social-gameplay-using-azure-documentdb/). Halo 5 fonctionnement social jeu à l’aide de DocumentDB d’Azure.
- [Galerie de Cortana Analytique](https://azure.microsoft.com/blog/cortana-analytics-gallery-a-scalable-community-site-built-on-azure-documentdb/). Cortana la galerie Analytique - un site de communauté évolutif basé sur Azure DocumentDB.
- [Clin de œil](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18602). Intégration de pointe permet d’aperçu Global des entreprises multinationales en quelques Minutes avec les Technologies de Cloud souple.
- [République de news](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18639). Ajout d’une intelligence à l’annonce pour fournir des informations de type pour les citoyens engagés. 
- [SGS International](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18653). Couleurs cohérente dans le monde entier, principales marques optez pour SGS. Et SGS active vers Azure.
- [Telenor](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18608). Leader mondial Telenor utilise le nuage pour passer à la vitesse d’une startup. 
- [XOMNI](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18667). La banque d’informations du futur s’exécute sur une recherche rapide et le simple flux de données.
