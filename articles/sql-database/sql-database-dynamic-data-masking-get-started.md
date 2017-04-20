<properties
   pageTitle="Mise en route de SQL de base de données données masque dynamique (Azure Portal)"
   description="La mise en route avec SQL de base de données dynamique données masquage dans le portail Azure"
   services="sql-database"
   documentationCenter=""
   authors="ronitr"
   manager="jhubbard"
   editor="v-romcal"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="07/10/2016"
   ms.author="ronitr; ronmat; v-romcal; sstein"/>


# <a name="get-started-with-sql-database-dynamic-data-masking-azure-portal"></a>Mise en route de SQL de base de données données masque dynamique (Azure Portal)

> [AZURE.SELECTOR]
- [Masquage de données dynamiques - Azure Portal classique](sql-database-dynamic-data-masking-get-started-portal.md)

## <a name="overview"></a>Vue d’ensemble

Masquage données dynamiques SQL de base de données limite l’exposition des données sensibles en le masquant à des utilisateurs non privilégiés. Masquage de données dynamique est pris en charge pour la version V12 de base de données de SQL Azure.

Masquage de données dynamique contribue à empêcher l’accès non autorisé aux données sensibles en permettant aux clients d’indiquer la quantité des données sensibles pour révéler avec un impact minimal sur la couche application. Il s’agit d’une fonctionnalité de sécurité basée sur les stratégies qui masque les données sensibles dans le jeu de résultats d’une requête sur les champs de la base de données désignée, alors que les données de la base de données ne sont pas modifiées.

Par exemple, un représentant du service à un centre d’appels peut identifier des appelants par plusieurs chiffres de leur numéro de sécurité sociale ou de numéro de carte de crédit, mais ces éléments de données ne doivent pas être exposées entièrement au représentant du service. Une règle de masquage peut être définie pour que tous les masques, mais les quatre derniers chiffres de votre numéro de sécurité sociale ou numéro de carte de crédit dans le résultat de la valeur de toutes les requêtes. Comme autre exemple, un masque de données approprié peut être défini pour la protection des données d’informations d’identification personnelle (PII), afin qu’un développeur peut interroger des environnements de production aux fins de dépannage sans violer les réglementations de conformité.

## <a name="sql-database-dynamic-data-masking-basics"></a>Principes de base de masquage données dynamiques SQL de base de données

Permet de paramétrer un masquage de stratégie dans le portail Azure en sélectionnant l’opération de masquage de données dynamiques dans votre lame de configuration de la base de données SQL ou d’une lame de paramètres de données dynamiques.


### <a name="dynamic-data-masking-permissions"></a>Autorisations de masquage de données dynamiques

Masquage de données dynamiques peut être configuré par l’administrateur de base de données Azure, administration du serveur ou des rôles de responsable de la sécurité.

### <a name="dynamic-data-masking-policy"></a>Stratégie de masquage des données dynamiques

* **Les utilisateurs SQL exclus de masquage** - un ensemble d’utilisateurs SQL ou identités DAS obtiennent des données masquées dans les résultats de la requête SQL. Notez que les utilisateurs disposant de privilèges d’administrateur seront toujours exclus de masquage et affichera les données d’origine sans aucun masque.

* **Masquage des règles** - un ensemble de règles qui définissent les champs désignés à masquer et la fonction de masquage qui sera utilisée. Les champs désignés peuvent être définis à l’aide d’un nom de schéma de base de données, le nom de la table et le nom de la colonne.

* **Masquage des fonctions** - un ensemble de méthodes qui contrôlent l’exposition des données pour des scénarios différents.

| Fonction de masquage | Masquage logique |
|----------|---------------|
| **Par défaut**  |**Masquage complet en fonction des types de données des champs désignés**<br/><br/>• Utilisez XXXX ou moins Xs si la taille du champ est inférieur à 4 caractères pour les types de données chaîne (nchar, ntext, nvarchar).<br/>• Utilisez la valeur zéro pour les types de données numériques (bigint, bit, decimal, int, money, numeric, smallint, smallmoney, tinyint, float, réel).<br/>• Utilisez 1900-01-01 pour les types de données date/heure (date, datetime2, datetime, datetimeoffset, smalldatetime, heure).<br/>• Pour la variante SQL, la valeur par défaut du type en cours est utilisé.<br/>• Pour XML du document <masked/> est utilisé.<br/>• Utiliser une valeur vide pour les types de données spéciaux (table de timestamp, hierarchyid, GUID, binary, image, varbinary les types spatiale).
| **Carte de crédit** |**Masquage de méthode qui expose les quatre derniers chiffres des champs désignés** et ajoute une chaîne constante comme préfixe sous la forme d’une carte de crédit.<br/><br/>XXXX-XXXX-XXXX-1234|
| **Numéro de sécurité sociale** |**Masquage de méthode qui expose les quatre derniers chiffres des champs désignés** et ajoute une chaîne constante comme préfixe sous la forme d’un numéro de sécurité sociale américain.<br/><br/>XXX-XX-1234 |
| **Messagerie** | **Masquage de méthode qui expose la première lettre et remplace le domaine avec XXX.com** à l’aide d’un préfixe de chaîne constante sous la forme d’une adresse de messagerie.<br/><br/>aXX@XXXX.com |
| **Nombre aléatoire** | **Méthode qui génère un nombre aléatoire de masquage** selon les frontières sélectionnées et les types de données réelles. Si les limites désignés sont égales, la fonction de masquage sera un nombre constant.<br/><br/>![Volet de navigation](./media/sql-database-dynamic-data-masking-get-started/1_DDM_Random_number.png) |
| **Texte personnalisé** | **Méthode qui expose les premier et dernier caractères de masquage** et ajoute une chaîne de remplissage personnalisé au milieu. Si la chaîne d’origine est plus courte que le préfixe exposé et le suffixe, servira uniquement la chaîne de remplissage. <br/>suffixe de préfixe [remplissage]<br/><br/>![Volet de navigation](./media/sql-database-dynamic-data-masking-get-started/2_DDM_Custom_text.png) |


<a name="Anchor1"></a>
### <a name="recommended-fields-to-mask"></a>Champs recommandés pour masquer

Le moteur de recommandations DDM indicateurs certains champs de votre base de données en tant que champs potentiellement sensibles, qui peuvent être de bons candidats pour le masquage. De la lame de masquage des données dynamiques dans le portail, vous verrez les colonnes recommandées pour votre base de données. Tout ce que vous devez faire est cliquez sur **Ajouter un masque** pour une ou plusieurs colonnes, puis sur **Enregistrer** pour appliquer un masque pour ces champs.

## <a name="set-up-dynamic-data-masking-for-your-database-using-the-azure-portal"></a>Configurer le masquage de données dynamiques pour votre base de données à l’aide du portail Azure

1. Lancer le portail Azure à [https://portal.azure.com](https://portal.azure.com).

2. Accédez à la lame de paramètres de la base de données qui contient les données sensibles que vous souhaitez masquer.

3. Cliquez sur la mosaïque de **Masquage de données dynamiques** qui lance la lame **Masquage dynamique des données** de configuration.

    * Également, vous pouvez faire défiler jusqu'à la section **opérations** et cliquez sur **Fonction de masquage dynamique des données**.

    ![Volet de navigation](./media/sql-database-dynamic-data-masking-get-started/4_ddm_settings_tile.png)<br/><br/>


4. Dans la lame **Masquage dynamique des données** de configuration, vous pouvez voir certaines colonnes de la base de données que le moteur de recommandations a un indicateur de masquage. Pour accepter les recommandations, cliquez sur **Ajouter un masque** pour une ou plusieurs colonnes et un masque sera créé, fondé sur le type par défaut pour cette colonne. Vous pouvez modifier la fonction de masquage en cliquant sur la règle de masque et en modifiant le masquage format de champ dans un autre format de votre choix. Assurez-vous de cliquer sur **Enregistrer** pour enregistrer vos paramètres.

    ![Volet de navigation](./media/sql-database-dynamic-data-masking-get-started/5_ddm_recommendations.png)<br/><br/>


5. Pour ajouter un masque pour n’importe quelle colonne dans votre base de données, dans la partie supérieure de la lame **Masquage dynamique des données** de configuration, cliquez sur **Ajouter un masque** pour ouvrir la lame de configuration **Ajouter une règle de masque**

    ![Volet de navigation](./media/sql-database-dynamic-data-masking-get-started/6_ddm_add_mask.png)<br/><br/>

6. Sélectionnez le **schéma**, **Table** et **colonne** pour définir le champ désigné qui est caché.

7. Choisissez un **Format de champ de masquage** à partir de la liste des catégories de masquage des données sensibles.

    ![Volet de navigation](./media/sql-database-dynamic-data-masking-get-started/7_ddm_mask_field_format.png)<br/><br/>     

8. Dans les données de masquage des lames de règle pour l’ensemble de règles dans la stratégie de masquage dynamique de données de masquage de mettre à jour, cliquez sur **Enregistrer** .

9. Tapez les utilisateurs SQL ou les identités DAS qui doivent être exclues de masquage et ont accès aux données sensibles non masquées. Ce doit être une liste séparée par des points-virgules des utilisateurs. Notez que les utilisateurs disposant de privilèges d’administrateur ont toujours accès aux données d’origine non masquées.

    ![Volet de navigation](./media/sql-database-dynamic-data-masking-get-started/8_ddm_excluded_users.png)

    >[AZURE.TIP] Pour qu’il sorte de la couche application peut afficher des données sensibles pour les utilisateurs d’application privilégiée, ajoutez l’utilisateur SQL ou l’identité DAS l’application utilise pour interroger la base de données. Il est fortement recommandé que cette liste contient un nombre minimal d’utilisateurs privilégiés à minimiser l’exposition des données sensibles.

10. Dans les données de masquage des lames de configuration pour enregistrer la stratégie de masquage de nouveau ou mis à jour, cliquez sur **Enregistrer** .

## <a name="set-up-dynamic-data-masking-for-your-database-using-powershell-cmdlets"></a>Définir les données dynamiques de masquage pour votre base de données à l’aide des applets de commande Powershell

Voir les [applets de commande de base de données SQL Azure](https://msdn.microsoft.com/library/azure/mt574084.aspx).


## <a name="set-up-dynamic-data-masking-for-your-database-using-rest-api"></a>Configurer le masquage de données dynamiques pour votre base de données à l’aide des API REST

Consultez les [opérations de bases de données SQL Azure](https://msdn.microsoft.com/library/dn505719.aspx).
