<properties
    pageTitle="Mise en route de catalogue de données | Microsoft Azure"
    description="Didacticiel de bout en bout pour présenter les scénarios et les fonctionnalités de catalogue de données Azure."
    documentationCenter=""
    services="data-catalog"
    authors="steelanddata"
    manager="jhubbard"
    editor=""
    tags=""/>
<tags
    ms.service="data-catalog"
    ms.devlang="NA"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-catalog"
    ms.date="09/20/2016"
    ms.author="spelluru"/>

# <a name="get-started-with-azure-data-catalog"></a>Mise en route de catalogue de données Azure
Catalogue de données Azure est un service en nuage entièrement géré qui sert d’un système d’enregistrement et système de détection pour les actifs de données d’entreprise. Pour une présentation détaillée, reportez-vous à la section [Quel est le catalogue de données Azure](data-catalog-what-is-data-catalog.md).

Ce didacticiel vous aide à commencer avec le catalogue de données Azure. Vous effectuez les procédures suivantes dans ce didacticiel :

| Procédure | Description |
| :--- | :---------- |
| [Catalogue de fourniture de données](#provision-data-catalog) | Dans cette procédure, vous mettez en service ou configurez le catalogue de données Azure. Vous effectuez cette étape uniquement si le catalogue n’a pas été défini avant. Vous pouvez avoir qu’un seul catalogue de données par l’organisation (domaine Active Directory de Microsoft Azure) même s’il existe plusieurs abonnements associés à votre compte Azure. |
| [Enregistrer les données de l’entreprise](#register-data-assets) | Dans cette procédure, vous inscrivez des éléments de données à partir de la base de données exemple de AdventureWorks2014 avec le catalogue de données. L’inscription est le processus d’extraction des metadata structurelles clé tels que les noms, les types et les emplacements à partir de la source de données et copie les métadonnées vers le catalogue. La source de données et de vos données restent où ils sont, mais que les métadonnées sont utilisées par le catalogue afin de les rendre plus facilement détectable et compréhensibles. |
| [Découvrir les ressources de données](#discover-data-assets) | Dans cette procédure, vous utilisez le portail du catalogue de données Azure pour découvrir les ressources de données qui ont été enregistrés à l’étape précédente. Une fois une source de données a été enregistrée avec le catalogue de données Azure, ses métadonnées sont indexée par le service afin que les utilisateurs peuvent rechercher facilement les données que dont ils ont besoin. |
| [Annoter les éléments de données](#annotate-data-assets) | Dans cette procédure, vous fournissez des annotations (informations de description, balises, documentation ou experts) pour les actifs de données. Ces informations complètent les métadonnées extraites à partir de la source de données et la source de données plus compréhensibles à d’autres personnes. |
| [Se connecter à des ressources de données](#connect-to-data-assets) | Dans cette procédure, vous ouvrez les ressources de données dans les outils du client intégré (par exemple, Excel et les outils de données de SQL Server) et un outil non intégrées (SQL Server Management Studio). |
| [Gérer les ressources de données](#manage-data-assets) | Dans cette procédure, vous définissez la sécurité à vos données. Catalogue de données ne donne pas aux utilisateurs l’accès aux données elles-mêmes. Le propriétaire de la source de données contrôle l’accès aux données. <br/><br/> Avec les données de catalogue, vous pouvez découvrir les sources de données et afficher les **métadonnées** associées aux sources inscrits dans le catalogue. Il peut arriver, cependant, où les sources de données doivent être visibles uniquement à des utilisateurs spécifiques ou à des membres de groupes spécifiques. Pour ces scénarios, vous pouvez utiliser le catalogue de données à prendre possession d’actifs de données enregistrées dans le catalogue et contrôler la visibilité des actifs que vous possédez. |
| [Supprimer les données de l’entreprise](#remove-data-assets) | Dans cette procédure, vous allez apprendre à supprimer les données de l’entreprise à partir du catalogue de données. |  

## <a name="tutorial-prerequisites"></a>Conditions préalables de didacticiels

### <a name="azure-subscription"></a>Abonnement Azure
Pour configurer le catalogue de données Azure, vous devez être le propriétaire ou le copropriétaire d’un abonnement Azure.

Abonnements Azure vous permettent d’organiser l’accès aux ressources de service de cloud telles que catalogue de données Azure. Ils aident également à contrôler la façon dont l’utilisation des ressources est signalée, facturé et payé. Chaque abonnement peut avoir un paramétrage différent de facturation et de paiement, vous pouvez avoir différents abonnements et différents plans par département, projet, bureau régional et ainsi de suite. Chaque service en nuage appartient à un abonnement, et vous devez disposer d’un abonnement avant de configurer le catalogue de données Azure. Pour plus d’informations, voir [Gérer les comptes, les abonnements et les rôles administratifs](../active-directory/active-directory-how-subscriptions-associated-directory.md).

Si vous n’êtes pas abonné, vous pouvez créer un compte d’essai gratuit en quelques minutes. Pour plus d’informations, consultez [Version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/) .

### <a name="azure-active-directory"></a>Azure Active Directory
Pour configurer le catalogue de données Azure, vous devez être connecté avec un compte d’utilisateur Azure Active Directory (AD Azure). Vous devez être le propriétaire ou le copropriétaire d’un abonnement Azure.  

Annonce Azure offre un moyen facile pour votre entreprise à la gestion des identités et des accès, à la fois dans le nuage et sur site. Vous pouvez utiliser un compte de l’établissement ou le travail pour vous connecter à n’importe quelle application web nuage ou sur site. Catalogue de données Azure utilise AD Azure pour authentifier la connexion. Pour plus d’informations, consultez [Nouveautés Azure Active Directory](../active-directory/active-directory-whatis.md).

### <a name="azure-active-directory-policy-configuration"></a>Configuration de la stratégie Active Directory Azure

Vous pouvez rencontrer une situation où vous pouvez vous connecter sur le portail du catalogue de données Azure, mais lorsque vous essayez de vous connecter à l’outil de l’enregistrement de source de données, vous rencontrez un message d’erreur qui vous empêche de vous connecter. Cette erreur peut se produire lorsque vous êtes sur le réseau d’entreprise ou lorsque vous vous connectez depuis l’extérieur du réseau d’entreprise.

L’outil d’inscription utilise *l’authentification par formulaire* pour valider les connexions utilisateur dans Azure Active Directory. Pour la connexion réussie, un administrateur Active Directory de Azure doit activer l’authentification par formulaires dans la *stratégie d’authentification globale*.

Avec la stratégie globale de l’authentification, vous pouvez activer l’authentification séparément pour l’intranet et les connexions de l’extranet, comme illustré dans l’image suivante. Erreurs de connexion peuvent se produire si l’authentification par formulaire n’est pas activée pour le réseau à partir duquel vous vous connectez.

 ![Stratégie d’authentification global Active Directory Azure](./media/data-catalog-prerequisites/global-auth-policy.png)

Pour plus d’informations, voir [stratégies de configuration d’authentification](https://technet.microsoft.com/library/dn486781.aspx).

## <a name="provision-data-catalog"></a>Catalogue de fourniture de données
Vous pouvez prévoir qu’un seul catalogue de données par l’organisation (domaine Azure Active Directory). Par conséquent, si le propriétaire ou un abonnement Azure copropriétaire appartenant à ce domaine Azure Active Directory n’a déjà créé un catalogue, vous ne pourrez pas créer un catalogue à nouveau, même si vous disposez de plusieurs abonnements Azure. Pour tester si un catalogue de données a été créé par un utilisateur dans votre domaine Active Directory de Azure, accédez à la [page d’accueil de catalogue de données Azure](http://azuredatacatalog.com) et vérifiez si vous voyez le catalogue. Si un catalogue a déjà été créé pour vous, ignorez la procédure suivante et allez à la section suivante.    

1. Accédez à la [page de service de catalogue de données](https://azure.microsoft.com/services/data-catalog) , cliquez sur **mise en route**.

    ![Catalogue de données Azure : page d’accueil de marketing](media/data-catalog-get-started/data-catalog-marketing-landing-page.png)
2. Connectez-vous avec un compte d’utilisateur qui est le propriétaire ou le copropriétaire d’un abonnement Azure. Vous consultez la page suivante après l’ouverture de session.

    ![Catalogue de données Azure--catalogue de fourniture de données](media/data-catalog-get-started/data-catalog-create-azure-data-catalog.png)
3. Spécifiez un **nom** pour le catalogue de données, l' **abonnement** que vous souhaitez utiliser et l' **emplacement** du catalogue.
4. Développez la **tarification** et sélectionnez un catalogue de données Azure **edition** (libre ou Standard).
    ![Catalogue de données Azure--sélectionnez edition](media/data-catalog-get-started/data-catalog-create-catalog-select-edition.png)
5. Développez **Utilisateurs de catalogue** et cliquez sur **Ajouter** pour ajouter des utilisateurs pour le catalogue de données. Vous sont automatiquement ajoutés à ce groupe.
    ![Catalogue de données Azure--les utilisateurs](media/data-catalog-get-started/data-catalog-add-catalog-user.png)
6. Développez **Les administrateurs de catalogue** et cliquez sur **Ajouter** pour ajouter des administrateurs supplémentaires pour le catalogue de données. Vous sont automatiquement ajoutés à ce groupe.
    ![Catalogue de données Azure : administrateurs](media/data-catalog-get-started/data-catalog-add-catalog-admins.png)
7. Cliquez sur **Créer un catalogue** pour créer le catalogue de données pour votre organisation. Vous consultez la page d’accueil pour le catalogue de données après sa création.
    ![Catalogue de données Azure--créé](media/data-catalog-get-started/data-catalog-created.png)    

### <a name="find-a-data-catalog-in-the-azure-portal"></a>Trouver un catalogue de données dans le portail Azure
1. Sous un onglet distinct dans le navigateur web ou dans une fenêtre de navigateur web distincte, accédez au [portail Azure](https://portal.azure.com) et connectez-vous avec le même compte que celui que vous avez utilisé pour créer le catalogue de données à l’étape précédente.
2. Sélectionnez **Parcourir** et puis cliquez sur **Catalogue des données**.

    ![Catalogue de données Azure--Parcourir Azure](media/data-catalog-get-started/data-catalog-browse-azure-portal.png) vous voir le catalogue de données que vous avez créé.

    ![Catalogue de données Azure--afficher le catalogue dans la liste](media/data-catalog-get-started/data-catalog-azure-portal-show-catalog.png)
4.  Cliquez sur le catalogue que vous avez créé. Vous voyez la lame de **Catalogue de données** dans le portail.

    ![Catalogue de données Azure--lame dans le portail ](media/data-catalog-get-started/data-catalog-blade-azure-portal.png)
5. Vous pouvez afficher les propriétés du catalogue de données et les mettre à jour. Par exemple, cliquez sur **niveau de tarification** et modifier l’édition.

    ![Catalogue de données Azure--niveau de tarification](media/data-catalog-get-started/data-catalog-change-pricing-tier.png)

### <a name="adventure-works-sample-database"></a>Base de données exemple de Adventure Works
Dans ce didacticiel, vous inscrivez les ressources de données (tables) à partir de la base de données exemple de AdventureWorks2014 pour le moteur de base de données SQL Server, mais vous pouvez utiliser n’importe quelle source de données pris en charge si vous préférez travailler avec des données qui sont familier et pertinentes pour votre rôle. Pour obtenir une liste de sources de données prises en charge, consultez les [sources de données prises en charge](data-catalog-dsr.md).

### <a name="install-the-adventure-works-2014-oltp-database"></a>Installer la base de données Adventure Works 2014 OLTP
La base de données Adventure Works prend en charge les scénarios de traitement des transactions en ligne standard pour un fabricant de vélos fictif (Adventure Works Cycles), qui inclut des produits, de ventes et d’achats. Dans ce didacticiel, vous enregistrez les informations sur les produits dans le catalogue de données Azure.

Pour installer la base de données exemple de Adventure Works :

1. Télécharger [Adventure Works 2014 complète de base de données Backup.zip](https://msftdbprodsamples.codeplex.com/downloads/get/880661) sur CodePlex.
2. Pour restaurer la base de données sur votre ordinateur, suivez les instructions de [restauration d’une sauvegarde de base de données en utilisant SQL Server Management Studio](http://msdn.microsoft.com/library/ms177429.aspx), ou en procédant comme suit :
    1. Ouvrez SQL Server Management Studio et connectez-vous au moteur de base de données SQL Server.
    2. Droit des **bases de données** et cliquez sur **Restaurer la base de données**.
    3. Sous **Restaurer la base de données**, cliquez sur le **périphérique** de **Source** , puis cliquez sur **Parcourir**.
    4. Sous **Sélectionnez les périphériques de sauvegarde**, cliquez sur **Ajouter**.
    5. Accédez au dossier où vous avez le fichier **AdventureWorks2014.bak** , sélectionnez le fichier et cliquez sur **OK** pour fermer la boîte de dialogue **Localiser le fichier de sauvegarde** .
    6. Cliquez sur **OK** pour fermer la boîte de dialogue **Sélectionner les unités de sauvegarde** .    
    7. Cliquez sur **OK** pour fermer la boîte de dialogue **Restaurer la base de données** .

Vous pouvez maintenant enregistrer les données de l’entreprise à partir de la base de données exemple de Adventure Works à l’aide du catalogue de données Azure.

## <a name="register-data-assets"></a>Enregistrer les données de l’entreprise

Dans cet exercice, l’outil d’inscription vous permet d’enregistrer les données de l’entreprise à partir de la base de données Adventure Works avec le catalogue. L’inscription est le processus d’extraction des metadata structurelles clé tels que les noms, les types et les emplacements à partir de la source de données et les ressources qu’il contient et copie les métadonnées vers le catalogue. La source de données et de vos données restent où ils sont, mais que les métadonnées sont utilisées par le catalogue afin de les rendre plus facilement détectable et compréhensibles.

### <a name="register-a-data-source"></a>Enregistrer une source de données

1.  Accédez à la [page d’accueil de catalogue de données Azure](https://azuredatacatalog.com) , puis cliquez sur **Publier**.

    ![Catalogue de données Azure--publier les données de bouton](media/data-catalog-get-started/data-catalog-publish-data.png)

2.  Cliquez sur **Lancer l’Application** pour télécharger, installer et exécuter l’outil d’inscription sur votre ordinateur.

    ![Catalogue de données Azure : bouton de lancement](media/data-catalog-get-started/data-catalog-launch-application.png)

3. Dans la page **Bienvenue** , cliquez sur **se connecter** et entrez vos informations d’identification.    

    ![Catalogue de données Azure – page d’accueil](media/data-catalog-get-started/data-catalog-welcome-dialog.png)

4. Dans la page **Catalogue de données de Microsoft Azure** , cliquez sur **suivant**et **SQL Server** .

    ![Catalogue de données Azure : sources de données](media/data-catalog-get-started/data-catalog-data-sources.png)

5.  Entrez les propriétés de la connexion SQL Server pour **AdventureWorks2014** (voir l’exemple suivant), puis cliquez sur **se connecter**.

    ![Catalogue de données Azure : les paramètres de connexion SQL Server](media/data-catalog-get-started/data-catalog-sql-server-connection.png)

6.  Enregistrer les métadonnées de vos ressources de données. Dans cet exemple, vous enregistrez les objets de **Production et des produits** à partir de l’espace de noms de Production d’AdventureWorks :

    1. Dans l’arborescence de la **Hiérarchie du serveur** , développez **AdventureWorks2014** , puis cliquez sur la **Production**.
    2. Sélectionnez le **produit**, **ProductCategory**, **ProductDescription**et **ProductPhoto** à l’aide de Ctrl + clic.
    3. Cliquez sur la **flèche sélectionnée de déplacer** (**>**). Cette action déplace tous les objets sélectionnés dans la liste **d’objets à inscrire** .

        ![Didacticiel du catalogue de données Azure--Parcourir et sélectionner des objets](media/data-catalog-get-started/data-catalog-server-hierarchy.png)
    4. Sélectionnez **inclure un aperçu** pour inclure un aperçu de la capture instantanée des données. Le cliché comprend jusqu'à 20 enregistrements de chaque table, et il est copié dans le catalogue.
    5. Sélectionnez **Inclure les profils de données** à inclure un instantané des statistiques d’objet pour le profil de données (par exemple : les valeurs minimales, maximales et moyennes d’une colonne, le nombre de lignes).
    6. Dans le champ **Ajouter des mots clés** , entrez **la société UNTEL, cycles**. Cette action ajoute des balises de recherche pour ces données de l’entreprise. Les balises sont un excellent moyen pour aider les utilisateurs à rechercher une source de données enregistré.
    7. Spécifiez le nom d’un **expert** sur ces données (facultatives).

        ![Didacticiel de catalogue de données Azure--objets à enregistrer](media/data-catalog-get-started/data-catalog-objects-register.png)

    8. Cliquez sur **Enregistrer**. Catalogue de données Azure enregistre vos objets sélectionnés. Dans cet exercice, les objets sélectionnés à partir de la société Adventure Works sont enregistrés. L’outil d’inscription extrait des métadonnées de l’élément de données et copie les données dans le service de catalogue de données Azure. Les données restent où il se trouve, et il reste sous le contrôle des administrateurs et des stratégies du système en cours.

        ![Catalogue de données Azure--objets inscrits](media/data-catalog-get-started/data-catalog-registered-objects.png)

    9. Pour afficher vos objets de source de données enregistrées, cliquez sur **Affichage Portal**. Dans le portail du catalogue de données Azure, vérifiez que vous consultez les quatre tables et la base de données dans la grille.

        ![Objets dans le portail du catalogue de données Azure ](media/data-catalog-get-started/data-catalog-view-portal.png)


Dans cet exercice, vous objets enregistrés à partir de la base de données exemple de Adventure Works afin qu’ils peuvent être aisément découverts par les utilisateurs au sein de votre organisation. Dans l’exercice suivant, vous allez apprendre à découvrir les ressources de données enregistré.

## <a name="discover-data-assets"></a>Découvrir les ressources de données
Recherche dans le catalogue de données Azure utilise deux mécanismes principaux : recherche et le filtrage.

La recherche est conçue pour être à la fois puissantes et intuitives. Par défaut, les termes de recherche sont mis en correspondance avec n’importe quelle propriété du catalogue, y compris les annotations fourni par l’utilisateur.

Le filtrage est conçu pour compléter la recherche. Vous pouvez sélectionner des caractéristiques spécifiques telles que les experts, type de source de données, type d’objet et balises pour afficher les données par des actifs congruents et à limiter les résultats de la recherche à des actifs congruents.

À l’aide d’une combinaison de la recherche et le filtrage, vous pouvez naviguer rapidement les sources de données qui ont été enregistrés avec le catalogue de données Azure pour découvrir les ressources de données dont vous avez besoin.

Dans cet exercice, le portail du catalogue de données Azure vous permet de découvrir les ressources de données que vous inscrit dans l’exercice précédent. Pour plus d’informations sur la syntaxe de recherche, consultez [recherche de catalogue de données de référence de la syntaxe](https://msdn.microsoft.com/library/azure/mt267594.aspx) .

Voici quelques exemples pour la découverte des ressources de données dans le catalogue.  

### <a name="discover-data-assets-with-basic-search"></a>Découvrir les ressources de données avec recherche de base
Recherche de base vous permet de rechercher un catalogue à l’aide d’un ou plusieurs termes de recherche. Les résultats sont des actifs qui correspondent à n’importe quelle propriété avec un ou plusieurs des mentions visées.

1. Cliquez sur **accueil** dans le portail du catalogue de données Azure. Si vous avez fermé le navigateur web, accédez à la [page d’accueil de catalogue de données Azure](https://www.azuredatacatalog.com).
2. Dans la zone Rechercher, entrez `cycles` et appuyez sur **entrée**.

    ![Catalogue de données Azure : recherche de texte de base](media/data-catalog-get-started/data-catalog-basic-text-search.png)
3. Vérifiez que vous consultez les quatre tables et la base de données (AdventureWorks2014) dans les résultats. Vous pouvez basculer entre **la vue de grille** et **affichage de la liste** en cliquant sur les boutons de la barre d’outils, comme illustré dans l’image suivante. Notez que le mot clé de recherche est mis en surbrillance dans les résultats de recherche, car l’option **mettre en surbrillance** est **activée**. Vous pouvez également spécifier le nombre de **résultats par page** dans les résultats de la recherche.

    ![Catalogue de données Azure : résultats de la recherche de texte de base](media/data-catalog-get-started/data-catalog-basic-text-search-results.png)

    Le panneau de **recherche** se trouve sur la gauche et se trouve à droite du panneau **Propriétés** . Dans le volet de **recherche** , vous pouvez modifier les critères de recherche et filtrer les résultats. Le panneau **Propriétés** affiche les propriétés d’un objet sélectionné dans la liste ou la grille.

4. Cliquez sur **le produit** dans les résultats de recherche. Cliquez sur **l’Aperçu**, **colonnes**, **Données de profil**, les onglets de **Documentation** , ou cliquez sur la flèche pour développer le volet inférieur.  

    ![Catalogue de données Azure : volet inférieur](media/data-catalog-get-started/data-catalog-data-asset-preview.png)

    Sous l’onglet **Aperçu** , vous voyez un aperçu des données dans la table **Product** .  
5. Cliquez sur l’onglet **colonnes** pour rechercher des informations détaillées sur les colonnes (par exemple, le **nom** et le **type de données**) dans l’élément de données.
6. Cliquez sur l’onglet **Profil de données** pour voir le profilage des données (par exemple : nombre de lignes, la taille des données, ou la valeur minimale d’une colonne) dans l’élément de données.
7. Filtrer les résultats en utilisant des **filtres** sur la gauche. Par exemple, cliquez sur **la Table** pour le **Type d’objet**et afficher uniquement les quatre tables, pas la base de données.

    ![Catalogue de données Azure--filtre les résultats de recherche](media/data-catalog-get-started/data-catalog-filter-search-results.png)

### <a name="discover-data-assets-with-property-scoping"></a>Découvrir les ressources de données avec la propriété étendue
Propriété étendue vous permet de découvrir les ressources de données où le terme de recherche est mis en correspondance avec la propriété spécifiée.

1. Effacer le filtre de la **Table** sous **Type d’objet** dans les **filtres**.  
2. Dans la zone Rechercher, entrez `tags:cycles` et appuyez sur **entrée**. Pour toutes les propriétés que vous pouvez utiliser pour la recherche dans le catalogue de données, reportez-vous à la section [recherche de catalogue de données de référence de la syntaxe](https://msdn.microsoft.com/library/azure/mt267594.aspx) .
3. Vérifiez que vous consultez les quatre tables et la base de données (AdventureWorks2014) dans les résultats.  

    ![Catalogue de données--propriété étendue des résultats de la recherche](media/data-catalog-get-started/data-catalog-property-scoping-results.png)

### <a name="save-the-search"></a>Enregistrer la recherche
1. Dans le volet de **recherche** , dans la section de **Recherche en cours** , entrez un nom pour la recherche et cliquez sur **Enregistrer**.

    ![Catalogue de données Azure : enregistrer la recherche](media/data-catalog-get-started/data-catalog-save-search.png)
2. Vérifiez que la recherche enregistrée s’affiche sous les **Recherches enregistrées**.

    ![Catalogue de données Azure--les recherches enregistrée](media/data-catalog-get-started/data-catalog-saved-search.png)
3. Vous avez le choix entre les actions à qu'entreprendre sur la recherche enregistrée (**Renommer**, **Supprimer**, **Enregistrer en tant que valeur par défaut** de recherche).

    ![Catalogue de données Azure--enregistré les options de recherche](media/data-catalog-get-started/data-catalog-saved-search-options.png)

### <a name="boolean-operators"></a>Opérateurs booléens
Vous pouvez élargir ou affiner votre recherche avec des opérateurs booléens.

1. Dans la zone Rechercher, tapez `tags:cycles AND objectType:table`, puis appuyez sur **entrée**.
2. Vérifiez que vous voyez apparaître que les tables (et non la base de données) dans les résultats.  

    ![Catalogue de données Azure--opérateur booléen dans la recherche](media/data-catalog-get-started/data-catalog-search-boolean-operator.png)

### <a name="grouping-with-parentheses"></a>Regroupement entre parenthèses
En effectuant un regroupement avec des parenthèses, vous pouvez regrouper des parties de la requête pour isoler logiquement, en particulier avec les opérateurs booléens.

1. Dans la zone Rechercher, entrez `name:product AND (tags:cycles AND objectType:table)` et appuyez sur **entrée**.
2. Vérifiez que vous ne voyez que la table de **produit** dans les résultats de recherche.

    ![Catalogue de données Azure--recherche de regroupement](media/data-catalog-get-started/data-catalog-grouping-search.png)   

### <a name="comparison-operators"></a>Opérateurs de comparaison
Avec les opérateurs de comparaison, vous pouvez utiliser des comparaisons de l’égalité pour les propriétés qui ont des types de données numérique et date.

1. Dans la zone Rechercher, entrez `lastRegisteredTime:>"06/09/2016"`.
2. Effacer le filtre de la **Table** sous **Type d’objet**.
3. Appuyez sur **entrée**.
4. Vérifiez que vous consultez les tables **Product**, **ProductCategory**, **ProductDescription**et **ProductPhoto** et la base de données AdventureWorks2014 que vous avez enregistré dans les résultats de la recherche.

    ![Catalogue de données Azure : résultats de comparaison](media/data-catalog-get-started/data-catalog-comparison-operator-results.png)

Pour plus d’informations sur la découverte des actifs de données et de [recherche de catalogue de données de référence de la syntaxe](https://msdn.microsoft.com/library/azure/mt267594.aspx) pour la syntaxe de recherche, reportez-vous à la section [comment découvrir les ressources de données](data-catalog-how-to-discover.md) .

## <a name="annotate-data-assets"></a>Annoter les éléments de données
Dans cet exercice, vous utilisez le portail du catalogue de données Azure à annoter (ajouter des informations telles que des descriptions, des balises ou des experts) actifs de données que vous avez précédemment inscrit dans le catalogue. Les annotations de complément et améliorent les métadonnées structurelles extraites de la source de données lors de l’inscription et rend les ressources de données beaucoup plus facile de découvrir et de comprendre.

Dans cet exercice, vous annotez un élément de données unique (ProductPhoto). Vous ajoutez un nom convivial et une description de la ressource de données ProductPhoto.  

1.  Accédez à la [page d’accueil de catalogue de données Azure](https://www.azuredatacatalog.com) et effectuez des recherches avec `tags:cycles` pour trouver les éléments de données que vous avez enregistré.  
2. Cliquez sur **ProductPhoto** dans les résultats de la recherche.  
3. Entrez **les images de produit** pour le **Nom convivial** et des **photos produit des documents de marketing** pour la **Description**.

    ![Catalogue de données Azure--ProductPhoto description](media/data-catalog-get-started/data-catalog-productphoto-description.png)

    La **Description** d’autres personnes vous permet de découvrir et de comprendre pourquoi et comment utiliser l’élément de données sélectionné. Vous pouvez également ajouter des balises supplémentaires et d’afficher les colonnes. Maintenant vous pouvez essayer de recherche et de filtrage pour découvrir les ressources de données en utilisant les métadonnées descriptives que vous avez ajoutés au catalogue.

Vous pouvez également effectuer les opérations suivantes sur cette page :

- Ajouter des experts pour l’élément de données. Cliquez sur **Ajouter** dans la zone **Experts** .
- Ajouter des balises au niveau du groupe de données. Cliquez sur **Ajouter** dans la zone **balises** . Une balise peut être un utilisateur de balise ou un glossaire. L’Édition Standard de catalogue de données inclut un glossaire d’entreprise qui aide les administrateurs de catalogue définir une classification des affaires central. Les utilisateurs du catalogue peuvent ensuite annoter des actifs de données avec des termes du glossaire. Pour plus d’informations, consultez [comment configurer le glossaire Business pour le marquage de régie](data-catalog-how-to-business-glossary.md)
- Ajouter des balises au niveau de la colonne. Cliquez sur **Ajouter** sous les **balises** de la colonne que vous souhaitez annoter.
- Ajouter la description de la colonne. Entrez la **Description** d’une colonne. Vous pouvez également afficher les métadonnées de description extraites de la source de données.
- Ajouter des informations de **demande d’accès** qui indique aux utilisateurs comment demander l’accès à la ressource de données.

    ![Catalogue de données Azure--ajouter des balises, des descriptions](media/data-catalog-get-started/data-catalog-add-tags-experts-descriptions.png)

- Cliquez sur l’onglet **Documentation** et fournir une documentation pour l’élément de données. Documentation du catalogue de données Azure, vous pouvez utiliser votre catalogue de données sous la forme d’un référentiel de contenu pour créer une narration complète de vos données.

    ![Catalogue de données Azure--onglet Documentation](media/data-catalog-get-started/data-catalog-documentation.png)


Vous pouvez également ajouter une annotation à plusieurs éléments de données. Par exemple, vous pouvez sélectionner tous les éléments de données que vous vous êtes inscrit et spécifier un expert pour eux.

![Catalogue de données Azure--annoter plusieurs ressources de données](media/data-catalog-get-started/data-catalog-multi-select-annotate.png)

Catalogue de données Azure prend en charge une approche d’approvisionnement de foule d’annotations. N’importe quel utilisateur de catalogue de données peut ajouter des descriptions (utilisateur ou glossaire), de balises et d’autres métadonnées, afin que tout utilisateur disposant d’un point de vue sur un actif de données et son utilisation peut avoir ce point de vue capturée et disponible à d’autres utilisateurs.

Pour des informations détaillées sur l’annotation des éléments de données, reportez-vous à la section [comment annoter les éléments de données](data-catalog-how-to-annotate.md) .

## <a name="connect-to-data-assets"></a>Se connecter à des ressources de données
Dans cet exercice, vous ouvrez les éléments de données dans un outil client intégré (Excel) et un outil non intégrées (SQL Server Management Studio) à l’aide des informations de connexion.

> [AZURE.NOTE] Il est important de se rappeler que catalogue de données Azure ne vous donne accès à la source de données, il permet simplement plus facile pour vous de découvrir et de comprendre. Lorsque vous vous connectez à une source de données, l’application cliente que vous choisissez utilise vos informations d’identification Windows ou vous demande des informations d’identification si nécessaire. Si vous n'avez pas auparavant été accordé l’accès à la source de données, vous devez avoir accès pour pouvoir vous connecter.

### <a name="connect-to-a-data-asset-from-excel"></a>Se connecter à une ressource de données à partir d’Excel

1. Sélectionnez le **produit** à partir des résultats de la recherche. Cliquez sur **Ouvrir dans** la barre d’outils, puis cliquez sur **Excel**.

    ![Catalogue de données Azure : se connecter à des ressources de données](media/data-catalog-get-started/data-catalog-connect1.png)
2. Cliquez sur **Ouvrir** dans la fenêtre de téléchargement. Cette expérience peut varier selon le navigateur.

    ![Catalogue de données Azure--fichier de connexion Excel téléchargé](media/data-catalog-get-started/data-catalog-download-open.png)
3. Dans la fenêtre de **Notification de sécurité de Microsoft Excel** , cliquez sur **Activer**.

    ![Catalogue de données Azure--menu contextuel sécurité de Excel](media/data-catalog-get-started/data-catalog-excel-security-popup.png)
4. Conserver les valeurs par défaut dans la boîte de dialogue **Importer les données** , puis cliquez sur **OK**.

    ![Catalogue de données Azure : importer des données Excel](media/data-catalog-get-started/data-catalog-excel-import-data.png)
5. Permet d’afficher la source de données dans Excel.

    ![Catalogue de données Azure--table produit dans Excel](media/data-catalog-get-started/data-catalog-connect2.png)

Dans cet exercice, vous connecté aux ressources de données découvertes à l’aide du catalogue de données Azure. Avec le portail du catalogue de données Azure, vous pouvez vous connecter directement à l’aide d’applications clientes intégrées dans le menu **Ouvrir dans** . Vous pouvez également vous connecter à n’importe quelle application que vous choisissez en utilisant les informations d’emplacement de connexion incluses dans les métadonnées de l’immobilisation. Par exemple, vous pouvez utiliser SQL Server Management Studio pour vous connecter à la base de données AdventureWorks2014 pour accéder aux données dans les éléments de données enregistrés dans ce didacticiel.

1. Ouvrez **SQL Server Management Studio**.
2. Dans la boîte de dialogue **se connecter au serveur** , entrez le nom du serveur dans le volet **Propriétés** dans le portail du catalogue de données Azure.
3. Utiliser les informations d’identification et d’authentification appropriées pour accéder à la ressource de données. Si vous n’avez pas accès, utiliser les informations dans le champ de **Demande d’accès** pour l’obtenir.

    ![Catalogue de données Azure--demande d’accès](media/data-catalog-get-started/data-catalog-request-access.png)

Cliquez sur **Afficher les chaînes de connexion** pour afficher et copier des chaînes de connexion OLEDB, ODBC et ADF.NET dans le Presse-papiers pour une utilisation dans votre application.

## <a name="manage-data-assets"></a>Gérer les ressources de données
Dans cette étape, vous allez apprendre à configurer la sécurité de vos ressources de données. Catalogue de données ne donne pas aux utilisateurs l’accès aux données elles-mêmes. Le propriétaire de la source de données contrôle l’accès aux données.

Vous pouvez utiliser le catalogue de données pour découvrir les sources de données et afficher les métadonnées relatives aux sources inscrits dans le catalogue. Il peut arriver, cependant, où des sources de données ne doivent être visibles à des utilisateurs spécifiques ou à des membres de groupes spécifiques. Pour ces scénarios, vous pouvez utiliser le catalogue de données à prendre possession d’actifs de données enregistrées dans le catalogue, et puis contrôle la visibilité des actifs que vous possédez.

> [AZURE.NOTE] Les capacités de gestion décrites dans cet exercice sont disponibles uniquement dans l’Édition Standard de Azure données catalogue, et non dans l’édition gratuite.
Dans le catalogue de données Azure, vous pouvez prendre possession des ressources de données, ajouter des copropriétaires pour les données de l’entreprise et définir la visibilité des données.

### <a name="take-ownership-of-data-assets-and-restrict-visibility"></a>Prendre possession des données et de restreindre la visibilité

1. Accédez à la [page d’accueil de catalogue de données Azure](https://www.azuredatacatalog.com). Dans la zone de texte **Rechercher** , entrez `tags:cycles` et appuyez sur **entrée**.
2. Cliquez sur un élément dans la liste des résultats, puis cliquez sur **Appropriation** dans la barre d’outils.
3. Dans la section **gestion** du panneau **Propriétés** , cliquez sur **Appropriation**.

    ![Catalogue de données Azure--appropriation](media/data-catalog-get-started/data-catalog-take-ownership.png)
4. Pour restreindre la visibilité, choisissez des **propriétaires et des utilisateurs** dans la section de **visibilité** et cliquez sur **Ajouter**. Entrez les adresses e-mail des utilisateurs dans la zone de texte et appuyez sur **entrée**.

    ![Catalogue de données Azure : restreindre l’accès](media/data-catalog-get-started/data-catalog-ownership.png)

## <a name="remove-data-assets"></a>Supprimer les données de l’entreprise

Dans cet exercice, vous utilisez le portail du catalogue de données Azure pour supprimer l’aperçu des données à partir des données de l’entreprise et de supprimer des éléments de données du catalogue.

Dans le catalogue de données Azure, vous pouvez supprimer un actif individuel ou plusieurs actifs.

1. Accédez à la [page d’accueil de catalogue de données Azure](https://www.azuredatacatalog.com).
2. Dans la zone de texte **Rechercher** , entrez `tags:cycles` et appuyez sur **entrée**.
3. Sélectionnez un élément dans la liste des résultats et cliquez sur **Supprimer** dans la barre d’outils comme indiqué dans l’image suivante :

    ![Catalogue de données Azure--suppression de l’élément grille](media/data-catalog-get-started/data-catalog-delete-grid-item.png)

    Si vous utilisez l’affichage de liste, la case à cocher est à gauche de l’élément, comme illustré dans l’image suivante :

    ![Catalogue de données Azure--suppression de l’élément liste](media/data-catalog-get-started/data-catalog-delete-list-item.png)

    Vous pouvez également sélectionner plusieurs éléments de données et les supprimer, comme illustré dans l’image suivante :

    ![Catalogue de données Azure--supprimer plusieurs ressources de données](media/data-catalog-get-started/data-catalog-delete-assets.png)


> [AZURE.NOTE] Le comportement par défaut du catalogue est de permettre aux utilisateurs d’enregistrer n’importe quelle source de données et de permettre aux utilisateurs de supprimer n’importe quel élément de données qui a été enregistré. Les capacités de gestion incluses dans l’Édition Standard de catalogue Azure données fournissent des options supplémentaires pour prendre possession des ressources, restriction qui peut découvrir les ressources, et de restreindre qui peut supprimer des actifs.


## <a name="summary"></a>Résumé

Dans ce didacticiel, vous a présenté les fonctions essentielles du catalogue de données Azure, y compris l’inscription, annoter, découverte et gestion des ressources de données d’entreprise. Maintenant que vous avez terminé le didacticiel, il est temps de commencer. Vous pouvez commencer dès aujourd'hui en inscrivant votre équipe et s’appuient sur les sources de données et à inviter des collègues à utiliser le catalogue.

## <a name="references"></a>Références

- [Comment enregistrer les données de l’entreprise](data-catalog-how-to-register.md)
- [Comment découvrir les ressources de données](data-catalog-how-to-discover.md)
- [Comment annoter les éléments de données](data-catalog-how-to-annotate.md)
- [Comment documenter les données de l’entreprise](data-catalog-how-to-documentation.md)
- [Comment se connecter à des ressources de données](data-catalog-how-to-connect.md)
- [Comment gérer les ressources de données](data-catalog-how-to-manage.md)
