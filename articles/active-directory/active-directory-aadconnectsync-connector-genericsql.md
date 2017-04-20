<properties
   pageTitle="Connecteur SQL générique | Microsoft Azure"
   description="Cet article décrit comment configurer le connecteur SQL générique de Microsoft."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="08/30/2016"
   ms.author="billmath"/>

# <a name="generic-sql-connector-technical-reference"></a>Référence technique de connecteur de SQL générique

Cet article décrit le connecteur SQL générique. L’article s’applique aux produits suivants :

- Gestionnaire d’identité Microsoft 2016 (MIM2016)
- Forefront Identity Manager 2010 R2 (FIM2010R2)
    -   Doit utiliser le correctif logiciel 4.1.3671.0 ou une version ultérieure [KB3092178](https://support.microsoft.com/kb/3092178).

Pour MIM2016 et FIM2010R2, le connecteur est disponible en téléchargement à partir du [Centre de téléchargement Microsoft](http://go.microsoft.com/fwlink/?LinkId=717495).

Pour voir ce connecteur en action, consultez l’article [Pas à pas de connecteur de SQL générique](active-directory-aadconnectsync-connector-genericsql-step-by-step.md) .

## <a name="overview-of-the-generic-sql-connector"></a>Vue d’ensemble du connecteur SQL générique

Le connecteur SQL générique vous permet d’intégrer le service de synchronisation avec un système de base de données qui offre une connectivité ODBC.  

À partir d’une perspective de haut niveau, les fonctionnalités suivantes sont prises en charge par la version actuelle du connecteur :

Fonctionnalité | Prise en charge
--- | ---
Source de données connectée | Le connecteur est pris en charge avec tous les pilotes ODBC de 64 bits. Il a été testé avec les éléments suivants : <li>Microsoft SQL Server et SQL Azure</li><li>IBM DB2 10.x</li><li>IBM DB2 9.x</li><li>Oracle 10 et 11 g</li><li>MySQL 5.x</li>
Scénarios   | <li>Gestion du cycle de vie des objets</li><li>Gestion de mot de passe</li>
Opérations | <li>Importation intégrale et Delta, importation, exportation</li><li>Pour l’exportation : Ajouter, supprimer, mettre à jour et remplacer</li><li>Définir le mot de passe, modifier le mot de passe</li>
Schéma | <li>Découverte dynamique des objets et des attributs</li>

### <a name="prerequisites"></a>Conditions préalables
Avant d’utiliser le connecteur, vérifiez que vous disposez des éléments suivants sur le serveur de synchronisation :

- 4.5.2 de Microsoft .NET Framework ou une version ultérieure
- pilotes de client ODBC 64 bits

### <a name="permissions-in-connected-data-source"></a>Autorisations dans la source de données connectée
Pour créer ou exécuter les tâches prises en charge dans le connecteur de SQL générique, vous devez disposer :

- db_datareader
- db_datawriter

### <a name="ports-and-protocols"></a>Ports et protocoles
Pour les ports requis pour le pilote ODBC à utiliser, consultez la documentation de la base de données.

## <a name="create-a-new-connector"></a>Créer un nouveau connecteur
Pour créer un connecteur de SQL générique, sélectionnez **Agent de gestion** et de **Création**de **Service de synchronisation** . Sélectionnez le connecteur **SQL générique (Microsoft)** .

![CreateConnector](./media/active-directory-aadconnectsync-connector-genericsql/createconnector.png)

### <a name="connectivity"></a>Connectivité
Le connecteur utilise un fichier DSN ODBC pour la connexion. Créez le fichier DSN à l’aide de **Sources de données ODBC** , dans le menu Démarrer, sous **Outils d’administration**. Dans l’outil d’administration, créez un **Fichier DSN** afin qu’il peut être fourni au connecteur.

![CreateConnector](./media/active-directory-aadconnectsync-connector-genericsql/connectivity.png)

L’écran de connexion est le premier lorsque vous créez un nouveau connecteur SQL générique. Vous devez d’abord fournir les informations suivantes :

- Chemin d’accès du fichier DSN
- Authentification
    - Nom d’utilisateur
    - Mot de passe

La base de données doit prendre en charge une de ces méthodes d’authentification :

- **L’authentification Windows**: l’authentification de la base de données utilise les informations d’identification Windows pour vérifier l’utilisateur. Le nom/mot de passe spécifié est utilisé pour l’authentification auprès de la base de données. Ce compte a besoin des autorisations pour la base de données.
- **L’authentification SQL**: l’authentification utilise de base de données le nom d’utilisateur/mot de passe défini l’écran de connexion pour se connecter à la base de données. Si vous stockez le nom d’utilisateur/mot de passe dans le fichier DSN, les informations d’identification fournies à l’écran de connexion ont la priorité.
- **L’authentification de base de données de SQL Azure**: pour plus d’informations, voir [se connecter à SQL de base de données à l’aide de Azure l’authentification Active Directory](..\sql-database\sql-database-aad-authentication.md).

**DN est le point d’ancrage**: Si vous sélectionnez cette option, le nom unique est également utilisé comme l’attribut d’ancrage. Il peut être utilisé pour une implémentation simple mais également présente la limite suivante :

-   Connecteur prend en charge uniquement un type d’objet. Par conséquent, les attributs de référence ne peuvent référencer le même type d’objet.

**Type d’exportation : objet remplacer**: pendant l’exportation, lorsque seuls certains attributs ont changé, l’objet entier avec tous les attributs sont exportée et remplace l’objet existant.

### <a name="schema-1-detect-object-types"></a>Schéma 1 (types d’objet de détection)
Sur cette page, vous allez configurer la manière dont le connecteur va rechercher les différents types d’objets dans la base de données.

Chaque type d’objet est présenté sous la forme d’une partition et configuré dans **Configuration des Partitions et des hiérarchies**.

![schema1a](./media/active-directory-aadconnectsync-connector-genericsql/schema1a.png)

**Procédé de détection de Type de l’objet**: le connecteur prend en charge ces méthodes de détection de type objet.

- **Valeur fixe**: vous fournir la liste des types d’objet avec une liste séparée par des virgules. Par exemple : `User,Group,Department`.  
![Schema1b](./media/active-directory-aadconnectsync-connector-genericsql/schema1b.png)
- **Table/vue/Stored Procedure**: fournir le nom de la table vue/procédure stockée /, puis sur le nom de la colonne qui fournit la liste des types d’objet. Si vous utilisez une procédure stockée, puis également fournir des paramètres pour elle dans le format **[nom] : [Direction] : [valeur]**. Fournir chaque paramètre sur une ligne séparée (utilisez Ctrl + Entrée pour obtenir une nouvelle ligne).  
![schema1c](./media/active-directory-aadconnectsync-connector-genericsql/schema1c.png)
- **Les requêtes SQL**: cette option vous permet de fournir une requête SQL qui renvoie une seule colonne avec les types d’objet, par exemple `SELECT [Column Name] FROM TABLENAME`. La colonne renvoyée doit être de type chaîne (varchar).

### <a name="schema-2-detect-attribute-types"></a>Schéma 2 (types d’attribut détecter)
Sur cette page, vous allez configurer comment les noms d’attributs et les types vont être détecté. Les options de configuration sont répertoriées pour chaque type d’objet a été détecté sur la page précédente.

![schema2a](./media/active-directory-aadconnectsync-connector-genericsql/schema2a.png)

**Procédé de détection de Type de l’attribut**: le connecteur prend en charge ces méthodes de détection de type attribut avec chaque type d’objet détecté dans l’écran de schéma 1.

- **Table/vue/Stored Procedure**: indiquez le nom de la procédure de table/vue/stockée qui doit être utilisé pour rechercher les noms d’attribut. Si vous utilisez une procédure stockée, puis également fournir des paramètres pour elle dans le format **[nom] : [Direction] : [valeur]**. Fournir chaque paramètre sur une ligne séparée (utilisez Ctrl + Entrée pour obtenir une nouvelle ligne). Pour détecter les noms d’attribut dans un attribut à valeurs multiples, fournir une liste séparée par des virgules, des Tables ou des vues. Scénarios à valeurs multiples ne sont pas pris en charge lorsque les tables parent et enfant ont les mêmes noms de colonne.
- **Les requêtes SQL**: cette option vous permet de fournir une requête SQL qui renvoie une seule colonne, avec les noms d’attributs, par exemple `SELECT [Column Name] FROM TABLENAME`. La colonne renvoyée doit être de type chaîne (varchar).

### <a name="schema-3-define-anchor-and-dn"></a>Schéma 3 (point d’ancrage de définir et DN)
Cette page vous permet de configurer le point d’ancrage et l’attribut de nom unique pour chaque type d’objet détecté. Vous pouvez sélectionner plusieurs attribut unique pour le point d’ancrage.

![schema3a](./media/active-directory-aadconnectsync-connector-genericsql/schema3a.png)

- Les attributs à valeurs multiples et booléens ne sont pas répertoriés.
- Même attribut ne peut pas utiliser de nom de domaine et d’ancrage, à moins que le **nom unique est le point d’ancrage** est sélectionné sur la page de connexion.
- Si le **nom unique est le point d’ancrage** est sélectionné sur la page de connexion, cette page nécessite uniquement l’attribut de nom unique. Cet attribut peut également être utilisé comme l’attribut d’ancrage.
![schema3b](./media/active-directory-aadconnectsync-connector-genericsql/schema3b.png)

### <a name="schema-4-define-attribute-type-reference-and-direction"></a>Schéma 4 (définir type d’attribut, référence et direction)
Cette page vous permet de configurer le type d’attribut, comme entier, binaire, ou de type Boolean et direction pour chaque attribut. Tous les attributs de page **schéma 2** figurent notamment les attributs à valeurs multiples.

![schema4a](./media/active-directory-aadconnectsync-connector-genericsql/schema4a.png)

- **Type de données**: permet de mapper le type d’attribut à ces types connus par le moteur de synchronisation. La valeur par défaut est d’utiliser le même type que détectée dans le schéma SQL, mais que DateTime et référence ne sont pas facilement détectable. Pour les, vous devez spécifier **DateTime** ou **référence**.
- **Direction**: vous pouvez définir l’orientation de l’attribut à l’importation, l’exportation ou ImportExport. ImportExport est la valeur par défaut.
![schema4b](./media/active-directory-aadconnectsync-connector-genericsql/schema4b.png)

Remarques :

- Si un type d’attribut n’est pas détectable par le connecteur, il utilise le type de données chaîne.
- **Les tables imbriquées** peut être considéré comme des tables de base de données d’une colonne. Oracle stocke les lignes d’une table imbriquée dans aucun ordre particulier. Toutefois, lorsque vous récupérez le tableau imbriqué dans une variable de PL/SQL, les lignes figurent les indices consécutives commençant à 1. Qui vous donne accès de type tableau à des lignes individuelles.
- **VARRYS** ne sont pas pris en charge dans le connecteur.

### <a name="schema-5-define-partition-for-reference-attributes"></a>Schéma 5 (partition de définir des attributs de référence)
Sur cette page, vous configurez pour tous les attributs de référence de la partition (type d’objet) un attribut fait référence à.

![Schema5](./media/active-directory-aadconnectsync-connector-genericsql/schema5.png)

Si vous utilisez le **nom unique est le point d’ancrage**, vous devez utiliser le même type d’objet que celui que vous faites référence à partir de. Vous ne pouvez pas référencer un autre type d’objet.

### <a name="global-parameters"></a>Paramètres globaux
La page Paramètres généraux est utilisée pour configurer l’importation de Delta, format Date/heure et procédé de mot de passe.

![globalparameters1](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters1.png)

Le connecteur SQL générique prend en charge les méthodes suivantes pour l’importation de Delta :

- **Déclencheur**: voir [génération de vues Delta à l’aide de déclencheurs](https://technet.microsoft.com/library/cc708665.aspx).
- **Filigrane**: une approche générique qui peut être utilisée avec une base de données. La requête de filigrane est renseignée au préalable basées sur le fournisseur de base de données. Une colonne de filigrane doit être présente sur chaque table/vue utilisée. Cette colonne doit assurer le suivi des insertions et des mises à jour les tables sous la forme et de ses dépendants (à valeurs multiples ou enfant) tables. Les horloges entre le Service de synchronisation et le serveur de base de données doivent être synchronisées. Si ce n’est pas le cas, certaines entrées dans l’importation delta peuvent être omises.  
Limitation :
    - Stratégie de filigrane ne pas les objets de prise en charge supprimé.
- **Capture instantanée**: (fonctionne uniquement avec Microsoft SQL Server), [Générer des vues Delta à l’aide de Snapshots](https://technet.microsoft.com/library/cc720640.aspx)
- **Suivi des modifications**: (fonctionne uniquement avec Microsoft SQL Server) [sur le suivi des modifications](https://msdn.microsoft.com/library/bb933875.aspx)  
Limitations :
    - Ancre et l’attribut de nom unique doivent être la partie de l’objet sélectionné dans la table de clé primaire.
    - Les requêtes SQL non pris en charge lors de l’importation et l’exportation avec suivi des modifications.

**Paramètres supplémentaires**: spécifier le fuseau horaire du serveur de base de données qui indique où se trouve votre serveur de base de données. Cette valeur est utilisée pour prendre en charge des différents formats d’attributs de date et d’heure.

Le connecteur stocke toujours les date et heure de la date au format UTC. Pour être en mesure de convertir correctement la date et les heures, le fuseau horaire du serveur de base de données et le format utilisé doit être spécifié. Le format doit être exprimé dans un format de .net.

Lors de l’exportation, chaque attribut d’heure date doit être fournie pour le connecteur dans le format de l’heure UTC.

![globalparameters2](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters2.png)

**Configuration du mot de passe**: le connecteur offre les fonctionnalités de synchronisation de mot de passe et prend en charge et de modifier le mot de passe.

Le connecteur offre deux méthodes pour prendre en charge la synchronisation de mot de passe :

- **Procédure stockée**: cette méthode nécessite deux procédures stockées pour prendre en charge le jeu de & modification de mot de passe. Tapez tous les paramètres pour ajouter et modifier l’opération de mot de passe dans **Définir les SP de mot de passe** et les paramètres de **Changer de SP de mot de passe** respectivement en tant que par Voici un exemple.
![globalparameters3](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters3.png)
- **Extension de mot de passe**: cette méthode nécessite des DLL (vous devez fournir le nom de DLL d’Extension qui implémente l’interface [IMAExtensible2Password](https://msdn.microsoft.com/library/microsoft.metadirectoryservices.imaextensible2password.aspx) ) d’extension de mot de passe. Assembly d’extension de mot de passe doit être placé dans le dossier d’extension pour que le connecteur puisse charger la DLL au moment de l’exécution.
![globalparameters4](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters4.png)

Vous devez également activer la gestion de mot de passe sur la page **Configurer l’Extension** .
![globalparameters5](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters5.png)

### <a name="configure-partitions-and-hierarchies"></a>Configurez des Partitions et des hiérarchies
Dans la page des partitions et des hiérarchies, sélectionnez tous les types d’objet. Chaque type d’objet est sa propre partition.

![partitions1](./media/active-directory-aadconnectsync-connector-genericsql/partitions1.png)

Vous pouvez également substituer des valeurs définies dans la page de **connexion** ou **Les paramètres globaux** .

![partitions2](./media/active-directory-aadconnectsync-connector-genericsql/partitions2.png)

### <a name="configure-anchors"></a>Configurer les points d’ancrage
Cette page est en lecture seule dans la mesure où le point d’ancrage a déjà été défini. L’attribut de point d’ancrage sélectionné est toujours ajouté avec le type d’objet pour s’assurer qu’il reste unique pour tous les types d’objet.

![points d’ancrage](./media/active-directory-aadconnectsync-connector-genericsql/anchors.png)

## <a name="configure-run-step-parameter"></a>Configurez le paramètre d’exécution d’étape
Ces étapes sont configurés sur les profils d’exécution sur le connecteur. Ces configurations de faire le travail réel d’importation et d’exportation de données.

### <a name="full-and-delta-import"></a>Complet et importation de Delta
Prise en charge de SQL connecteur intégral et importation de Delta à l’aide de ces méthodes :

- Table
- Affichage
- Procédure stockée
- Requêtes SQL

![runstep1](./media/active-directory-aadconnectsync-connector-genericsql/runstep1.png)

**Table/vue**  
Pour importer des attributs à valeurs multiples d’un objet, vous devez fournir le nom de table ou de vue séparée par des virgules dans les **vues de table/nom d’à valeurs multiples** et les conditions de jointure respectifs dans la **condition de jointure** avec la table parent.

Exemple : Que vous souhaitez importer l’objet Employee et tous ses attributs à valeurs multiples. Il existe deux tables, nommés employé (table principale) et service (valeur multiples).
Effectuez le des opérations suivantes :

- Type **employé** dans la **Table/vue/SP**.
- Saisissez le département dans les **vues de table/nom d’à valeurs multiples**.
- Tapez la condition de jointure entre employé et service **Condition de jointure**, par exemple `Employee.DEPTID=Department.DepartmentID`.
![runstep2](./media/active-directory-aadconnectsync-connector-genericsql/runstep2.png)

**Procédures stockées**  
![runstep3](./media/active-directory-aadconnectsync-connector-genericsql/runstep3.png)

- Si vous avez beaucoup de données, il est recommandé d’implémenter la pagination avec vos procédures stockées.
- Dans votre procédure stockée prendre en charge de la pagination, vous devez fournir l’Index de début et de fin. Voir : [échange efficacement par le biais de grandes quantités de données](https://msdn.microsoft.com/library/bb445504.aspx).
- @StartIndexet @EndIndex sont remplacés au moment de l’exécution avec valeur de taille de page correspondante configurée sur la page de **l’Étape de configuration** . Par exemple, lorsque le connecteur récupère la première et la taille de page a la valeur 500, dans une telle situation @StartIndex serait 1 et @EndIndex 500. Ces valeurs augmentent lorsque le connecteur récupère les pages suivantes et modifier la @StartIndex & @EndIndex valeur.
- Pour exécuter des procédures stockées paramétrées, fournissez les paramètres dans `[Name]:[Direction]:[Value]` format. Entrez chaque paramètre sur une ligne séparée (utiliser Ctrl + Entrée pour obtenir une nouvelle ligne).
- Le connecteur SQL générique prend également en charge l’opération d’importation à partir des serveurs liés dans Microsoft SQL Server. Si les informations doivent être extraites d’une Table de serveur lié, Table doit être fourni au format :`[ServerName].[Database].[Schema].[TableName]`
- Le connecteur SQL générique prend en charge uniquement les objets qui ont une structure similaire (à la fois les alias nom et type de données) entre exécuter des étapes de détection de schémas et d’informations. Si l’objet sélectionné à partir des informations de schéma et fournies à l’étape d’exécution est différent, puis connecteur de SQL ne prend en charge ce type de scénarios.

**Requêtes SQL**  
![runstep4](./media/active-directory-aadconnectsync-connector-genericsql/runstep4.png)

![runstep5](./media/active-directory-aadconnectsync-connector-genericsql/runstep5.png)

- Les jeux de résultats de plusieurs requêtes non pris en charge.
- Les requêtes SQL prend en charge de la pagination et fournir Démarrer Index et l’Index de fin en tant que variable pour prendre en charge de la pagination.

### <a name="delta-import"></a>Importation d’écart
![runstep6](./media/active-directory-aadconnectsync-connector-genericsql/runstep6.png)

Configuration de l’importation delta requiert une configuration plus par rapport à une importation complète.

- Si vous choisissez l’approche de déclencheur ou de capture instantanée pour effectuer le suivi des modifications delta, puis fournissez le tableau Historique ou l’instantané de base de données dans la zone **historique de Table ou nom de base de données de capture instantanée** .
- Vous devez également fournir de condition de jointure entre les tables d’historique et Parent, par exemple`Employee.ID=History.EmployeeID`
- Pour suivre la transaction sur la table parente de la table d’historique, vous devez fournir le nom de la colonne qui contient les informations de l’opération (ajout, mise à jour/suppression).
- Si vous choisissez de filigrane pour effectuer le suivi des modifications delta, puis indiquez le nom de la colonne qui contient les informations de l’opération dans la **Colonne nom de la marque de l’eau**.
- La colonne **Modifier l’attribut de Type** est requise pour le type de modification. Cette colonne est mappée à une modification qui se produit dans la table primaire ou table de valeurs multiples pour un type de modification de la vue de delta. Cette colonne peut contenir le type de modification Modify_Attribute pour changer de niveau d’attribut ou un ajouter, modifier ou supprimer modifier type pour un type de modifications au niveau de l’objet. Si elle est différente de la valeur par défaut ajouter, modifier, ou supprimer, puis vous pouvez définir ces valeurs à l’aide de cette option.

### <a name="export"></a>Exporter
![runstep7](./media/active-directory-aadconnectsync-connector-genericsql/runstep7.png)

Le connecteur SQL générique prennent en charge l’exportation à l’aide de quatre méthodes prises en charge telles que :

- Table
- Affichage
- Procédure stockée
- Requêtes SQL

**Table/vue**  
Si vous choisissez l’option de Table ou de vue, le connecteur génère les requêtes respectifs pour procéder à l’exportation.

**Procédures stockées**  
![runstep8](./media/active-directory-aadconnectsync-connector-genericsql/runstep8.png)

Si vous choisissez l’option de la procédure stockée, l’exportation nécessite trois procédures stockées différentes pour effectuer des opérations d’insertion/mise à jour/suppression.

- **Ajouter un nom de SP**: ce SP s’exécute si n’importe quel objet est fourni au connecteur pour l’insertion dans la table.
- **Mettre à jour le nom SP**: ce SP s’exécute si n’importe quel objet est fourni au connecteur de mise à jour dans la table.
- **Supprimer un nom de SP**: ce SP s’exécute si n’importe quel objet est fourni au connecteur pour suppression dans la table.
- Attribut sélectionné à partir du schéma utilisé comme valeur de paramètre pour la procédure stockée. Par exemple, `EmployeeName: INPUT: @EmployeeName` (EmployeeName est sélectionnée dans le schéma du connecteur et le connecteur remplace la valeur respective lors d’une exportation)
- Pour exécuter une procédure stockée paramétrable, fournir des paramètres de `[Name]:[Direction]:[Value]` format. Entrez chaque paramètre sur une ligne séparée (utiliser Ctrl + Entrée pour obtenir une nouvelle ligne).

**Requêtes SQL**  
![runstep9](./media/active-directory-aadconnectsync-connector-genericsql/runstep9.png)

Si vous choisissez l’option de requête SQL, exportation nécessite trois requêtes différentes pour effectuer des opérations d’insertion/mise à jour/suppression.

- **Requête Insert**: cette requête s’exécute, si n’importe quel objet est fourni au connecteur pour l’insertion dans la table.
- **Mettre à jour la requête**: cette requête s’exécute, si n’importe quel objet est fourni au connecteur de mise à jour dans la table.
- **Requête Suppression**: requête s’exécute, si n’importe quel objet est fourni au connecteur pour suppression dans la table.
- Attribut sélectionné à partir du schéma utilisé comme valeur de paramètre à la requête, par exemple`Insert into Employee (ID, Name) Values (@ID, @EmployeeName)`

## <a name="troubleshooting"></a>Résolution des problèmes

-   Pour plus d’informations sur la façon d’activer la journalisation résoudre les problèmes de connecteur, voir [comment activer le suivi ETW des connecteurs](http://go.microsoft.com/fwlink/?LinkId=335731).
