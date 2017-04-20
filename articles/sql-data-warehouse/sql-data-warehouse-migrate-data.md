<properties
   pageTitle="Migrer vos données vers SQL Data Warehouse | Microsoft Azure"
   description="Conseils pour la migration de vos données de l’entrepôt de données SQL Azure pour le développement de solutions."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/25/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# <a name="migrate-your-data"></a>Migration de vos données
Données peuvent être déplacées à partir de différentes sources dans votre magasin de données SQL avec un outils divers.  Copie du chargeur automatique de documents et SSIS, bcp peuvent être utilisés pour atteindre cet objectif. Toutefois, comme la quantité de données augmente, vous devez réfléchir sur la décomposition du processus de migration de données en étapes. Cela vous offre l’opportunité d’optimiser chaque étape à la fois pour des performances et de résilience assurer une migration sans heurts de données.

Cet article traite d’abord les scénarios de migration simple de copie du chargeur automatique de documents, SSIS et bcp. Il recherche puis un peu plus loin dans la façon dont la migration peut être optimisée.

## <a name="azure-data-factory-adf-copy"></a>Copie de données en usine (CAD) Azure
[Chargeur automatique de documents copie][] fait partie [d’Azure Data Factory][]. Copie du chargeur automatique de documents vous permet d’exporter vos données vers des fichiers plats résidant sur le stockage local, les fichiers plats à distance détenus dans le stockage blob Azure ou directement dans le magasin de données SQL.

Si le démarrent de vos données dans les fichiers plats, puis vous devez d’abord transférer vers le blob de stockage Azure avant de lancer une charge dans l’entrepôt de données SQL. Une fois que les données sont transférées dans le stockage blob Azure vous pouvez choisir d’utiliser les [Copie du chargeur automatique de documents][] à nouveau pour envoyer les données dans l’entrepôt de données SQL.

PolyBase propose également une option de haute performance pour charger les données. Toutefois, cela ne veut pas dire à l’aide de deux outils au lieu d’un. Si vous devez les meilleures performances, utilisez PolyBase. Si vous souhaitez une expérience unique outil (et les données ne sont pas importantes), chargeur automatique de documents est votre réponse.

> [AZURE.NOTE] PolyBase requiert vos fichiers de données en UTF-8. Il s’agit par défaut de la copie du chargeur automatique de documents en codage donc rien à modifier. C’est juste un rappel de ne pas modifier le comportement par défaut de la copie du chargeur automatique de documents.

Tête sur l’article suivant pour certains des [exemples du chargeur automatique de documents][].

## <a name="integration-services"></a>Services d’intégration ##
Integration Services (SSIS) est un outil de transformation d’extraire et de chargement (ETL) puissant et flexible qui prend en charge les flux de travail complexes, la transformation de données et plusieurs options de chargement de données. SSIS permet de transférer des données simplement vers Azure ou dans le cadre d’une migration plus large.

> [AZURE.NOTE] SSIS peuvent exporter au format UTF-8 sans la marque d’ordre d’octet dans le fichier. Pour configurer cela vous devez tout d’abord utiliser le composant de colonne dérivée pour convertir les données caractères dans le flux de données pour utiliser la page de code UTF-8 65001. Une fois que les colonnes ont été convertis, écrire les données dans l’adaptateur de destination de fichier plat garantissant que 65001 a également été sélectionné en tant que la page de codes pour le fichier.

SSIS se connecte au magasin de données SQL comme il devrait se connecter à un déploiement de SQL Server. Toutefois, vos connexions devez utiliser un gestionnaire de connexions ADO.NET. Vous devez également veiller à configurer le « utilisation bulk insert lorsque disponible » paramètre afin de maximiser le débit. Veuillez vous reporter à l’article de [l’adaptateur de destination ADO.NET][] pour en savoir plus sur cette propriété

> [AZURE.NOTE] Connexion au magasin de données SQL Azure en utilisant OLEDB n’est pas pris en charge.

En outre, il existe toujours la possibilité qu’un package échouer en raison de problèmes de réseau ou de la limitation. Conception de packages afin qu’ils peuvent être reprises au point de défaillance, sans répétition de travail terminé avant la panne.

Pour plus d’informations, consultez la [documentation de SSIS][].

## <a name="bcp"></a>bcp
bcp est un utilitaire de ligne de commande qui est conçu pour l’exportation et d’importation des données de fichier plat. Une transformation peut avoir lieu lors de l’exportation de données. Pour effectuer des transformations de simples utilisent une requête pour sélectionner et transformer les données. Une fois exporté, les fichiers plats peuvent alors être chargées directement dans la cible de la base de données SQL Data Warehouse.

> [AZURE.NOTE] Il est souvent judicieux d’encapsuler les transformations utilisées lors de l’exportation de données dans un affichage sur le système source. Cela garantit que la logique est conservée et le processus reproductible.

Avantages de l’utilitaire bcp sont :

- Plus de simplicité. commandes de l’utilitaire bcp sont faciles à générer et exécuter
- Processus de charge redémarrés. Une fois exporté, la charge peut être exécuté autant de fois

Limitations de l’utilitaire bcp sont les suivantes :

- bcp fonctionne avec les fichiers plats tabulation. Il ne fonctionne pas avec les fichiers xml ou JSON
- bcp ne gère pas l’exportation au format UTF-8. Cela peut empêcher de données bcp exporté à l’aide de PolyBase
- Fonctions de transformation de données sont limitées aux seuls le stade de l’exportation et sont de nature simples
- bcp n’a pas été adapté pour être robuste lors du chargement des données via internet. D’instabilité du réseau peut provoquer une erreur de chargement.
- bcp s’appuie sur le schéma qui est présent dans la base de données cible avant le chargement

Pour plus d’informations, voir [bcp utiliser pour charger des données dans Data Warehouse de SQL][].

## <a name="optimizing-data-migration"></a>Optimisation de la migration des données
Un processus de migration des données SQLDW pouvez efficacement ventilé en trois étapes distinctes :

1. Exportation de données de la source
2. Transfert de données vers Azure
3. Charger dans la base de SQLDW

Chaque étape peut être optimisée individuellement pour créer un processus de migration solide, redémarrés et fiable qui optimise les performances à chaque étape.

## <a name="optimizing-data-load"></a>Optimisation de chargement de données
En regardant dans l’ordre inverse pour un moment ; Il est le moyen le plus rapide pour charger les données via PolyBase. Optimisation pour un processus de chargement de PolyBase place les conditions préalables dans les étapes précédentes il est donc préférable de comprendre ce initial. Ils sont les suivantes :

1. Codage de fichiers de données
2. Format des fichiers de données
3. Emplacement des fichiers de données

### <a name="encoding"></a>Codage
PolyBase requiert des fichiers de données d’être codé en UTF-8. Cela signifie que lorsque vous exportez vos données, il doit satisfaire à cette prescription. Si vos données contiennent uniquement des caractères ASCII base (caractères ASCII non étendus) puis correspondance de ces directement à la norme UTF-8 et que vous ne devrez vous inquiétez pas trop sur le codage. Toutefois, si vos données contiennent des caractères spéciaux tels que trémas, des accents ou des symboles ou des vos données prend en charge les langues non latins puis vous devrez vous assurer que vos fichiers d’exportation sont correctement encodé UTF-8.

> [AZURE.NOTE] bcp ne gère pas l’exportation de données au format UTF-8. Par conséquent la meilleure option est d’utiliser les Services d’intégration ou copie du chargeur automatique de documents pour l’exportation de données. Il est important de noter que la marque d’ordre d’octet (BOM) UTF-8 n’est pas nécessaire dans le fichier de données.

Les fichiers codés à l’aide de UTF-16 devez être ré-écrite ***préalable*** pour le transfert de données.

### <a name="format-of-data-files"></a>Format des fichiers de données
PolyBase de mandater un terminateur de ligne fixe de \n ou saut de ligne. Vos fichiers de données doivent être conformes à la présente norme. Il n’existe pas de restrictions concernant les indicateurs de fin de chaîne ou de la colonne.

Vous devez définir toutes les colonnes dans le fichier en tant que partie de la table externe dans PolyBase. Assurez-vous que toutes les colonnes exportées sont requises et que les types sont conformes aux normes requises.

Veuillez référer à la [migrer votre schéma] article pour plus d’informations sur les types de données pris en charge.

### <a name="location-of-data-files"></a>Emplacement des fichiers de données
SQL Data Warehouse utilise PolyBase pour charger des données depuis le stockage Blob Azure exclusivement. Par conséquent, les données doivent avoir été tout d’abord transférées dans le stockage blob.

## <a name="optimizing-data-transfer"></a>Optimiser le transfert de données
Une des parties plus lentes de migration des données est le transfert des données vers Azure. Non seulement la bande passante réseau possible d’un problème, mais également la fiabilité du réseau peut également entraver sérieusement progression. Par défaut, migration de données vers Azure est sur internet afin que les risques d’erreurs de transfert qui se produisent sont raisonnablement probables. Toutefois, ces erreurs peuvent nécessiter des données soient envoyées à nouveau en tout ou en partie.

Heureusement, vous avez plusieurs options pour améliorer la vitesse et la résilience de ce processus :

### <a name="expressroute"></a>[ExpressRoute][]
Vous souhaiterez peut-être envisager d’utiliser [ExpressRoute][] pour accélérer le transfert. [ExpressRoute][] vous offre une connexion privée vers Azure afin que la connexion ne sont pas acheminés via internet. Il ne s’agit pas d’une étape obligatoire. Toutefois, il améliorera le débit lors de l’envoi de données vers Azure à partir d’un local ou les installations de colocalisation.

Les avantages de l’utilisation de [ExpressRoute][] sont :

1. Fiabilité accrue
2. Vitesse du réseau
3. Faible latence de réseau
4. une plus grande sécurité du réseau

[ExpressRoute][] est utile pour un certain nombre de scénarios ; non seulement la migration.

Vous souhaitez ? Pour plus d’informations et de tarification, consultez la [documentation de ExpressRoute][].

### <a name="azure-import-and-export-service"></a>Importation d’Azure et Service d’exportation
L’importation d’Azure et exporter un Service est un processus de transfert de données conçu pour grand (Go ++) à grande échelle (To ++) transferts de données vers Azure. Il consiste à enregistrer vos données sur les disques et leur expédition à un centre de données Azure. Le contenu du disque est ensuite chargé dans Azure stockage BLOB en votre nom.

Une vue d’ensemble du processus d’importation exportation est la suivante :

1. Configurer un conteneur de stockage des objets Blob Azure pour recevoir les données
2. Exporter des données dans le stockage local
2. Copier les données sur les lecteurs de disques durs SATA II/III de 3,5 pouces à l’aide de la [outil d’importation/exportation Azure]
3. Créer une tâche d’importation à l’aide la Azure importation et exportation de Service fournissant les fichiers de journal générés par le [outil d’importation/exportation Azure]
4. Expédier les disques de votre centre de données d’Azure désigné
5. Vos données sont transférées vers votre conteneur de stockage des objets Blob Azure
6. Charger les données à l’aide de PolyBase SQLDW

### <a name="azcopy-utility"></a>Utilitaire de [AZCopy][]
L’utilitaire [AZCopy][] est un outil formidable pour obtenir vos données transférées dans le BLOB de stockage Azure. Il est conçu pour les PME (Mo ++) de très gros transferts de données (Go ++). [AZCopy] a également été conçu pour fournir le bon débit résilient lors du transfert des données vers Azure et donc est la solution idéale pour l’étape de transfert de données. Une fois transférées, vous pouvez charger les données à l’aide de PolyBase dans SQL Data Warehouse. Vous pouvez également incorporer des AZCopy dans vos packages SSIS à l’aide de la tâche « Exécuter le processus ».

Pour utiliser AZCopy vous devez d’abord télécharger et l’installer. Une [version de production][] et une [version d’évaluation][] est disponible.

Pour télécharger un fichier à partir de votre système de fichiers, vous avez besoin une commande similaire à celle-ci :

```
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:abc.txt
```

Un résumé du processus de haut niveau peut être :

1. Configurer un conteneur blob de stockage Azure pour recevoir les données
2. Exporter des données dans le stockage local
3. AZCopy vos données dans le conteneur de stockage des objets Blob Azure
4. Charger les données dans l’entrepôt de données SQL à l’aide de PolyBase

Complète la documentation disponible : [AZCopy][].

## <a name="optimizing-data-export"></a>Optimiser l’exportation de données
Outre la garantie que l’exportation est conforme aux prescriptions en PolyBase, vous pouvez également rechercher optimiser l’exportation des données afin d’améliorer davantage le processus.

> [AZURE.NOTE] Selon les besoins de PolyBase les données pour être au format UTF-8, qu'il est peu probable que vous allez utiliser bcp pour procéder à l’exportation de données. bcp ne gère pas les fichiers de données de sortie au format UTF-8. SSIS ou copie du chargeur automatique de documents sont beaucoup mieux adaptés à l’exécution de ce type d’exportation de données.

### <a name="data-compression"></a>Compression des données
PolyBase peut lire les données gzip compressées. Si vous êtes en mesure de compresser les données pour les fichiers gzip Vous minimiserez la quantité de données que vous appuyez bien sur le réseau.

### <a name="multiple-files"></a>Plusieurs fichiers
Fragmenter les grands tableaux en plusieurs fichiers permet non seulement d’améliorer la vitesse d’exportation, mais également avec re-startability de transfert et la gestion globale des données une seule fois dans le stockage blob Azure. Une des nombreuses fonctionnalités intéressantes du PolyBase est sa capacité à lire tous les fichiers dans un dossier et traiter comme une table. Il est donc judicieux d’isoler les fichiers pour chaque table dans son propre dossier.

PolyBase prend également en charge une fonctionnalité appelée « récursive folder traversal ». Vous pouvez utiliser cette fonction pour améliorer l’organisation de vos données exportées afin d’améliorer la gestion de vos données.

Pour en savoir plus sur le chargement des données avec PolyBase, voir [Utilisation des PolyBase pour charger des données dans Data Warehouse de SQL][].


## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur la migration, consultez [migration de votre solution d’entrepôt de données de SQL][].
Pour plus de conseils de développement, consultez [vue d’ensemble du développement][].

<!--Image references-->

<!--Article references-->
[AZCopy]: ../storage/storage-use-azcopy.md
[Copie du chargeur automatique de documents]: ../data-factory/data-factory-data-movement-activities.md 
[Exemples de chargeur automatique de documents]: ../data-factory/data-factory-samples.md
[ADF Copy examples]: ../data-factory/data-factory-copy-activity-tutorial-using-visual-studio.md
[vue d’ensemble du développement]: sql-data-warehouse-overview-develop.md
[Migration de votre solution d’entrepôt de données de SQL]: sql-data-warehouse-overview-migrate.md
[SQL Data Warehouse development overview]: sql-data-warehouse-overview-develop.md
[Utiliser bcp pour charger des données dans l’entrepôt de données SQL]: sql-data-warehouse-load-with-bcp.md
[Permet de charger des données dans l’entrepôt de données SQL PolyBase]: sql-data-warehouse-get-started-load-with-polybase.md


<!--MSDN references-->

<!--Other Web references-->
[Usine de données Azure]: http://azure.microsoft.com/services/data-factory/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[Documentation de ExpressRoute]: http://azure.microsoft.com/documentation/services/expressroute/

[version de production]: http://aka.ms/downloadazcopy/
[version d’évaluation]: http://aka.ms/downloadazcopypr/
[Adaptateur de destination de ADO.NET]: https://msdn.microsoft.com/library/bb934041.aspx
[Documentation de SSIS]: https://msdn.microsoft.com/library/ms141026.aspx
