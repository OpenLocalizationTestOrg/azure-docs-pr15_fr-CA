<properties
   pageTitle="Questions fréquentes sur catalogue de données Azure | Microsoft Azure"
   description="Questions fréquemment posées sur le catalogue de données Azure, y compris les fonctionnalités de découverte de source de données, d’annotation et de gestion."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="10/04/2016"
   ms.author="maroche"/>

# <a name="azure-data-catalog-frequently-asked-questions"></a>Questions fréquentes sur catalogue de données Azure

Cet article fournit des réponses pour des questions fréquemment posées relatives au service Microsoft **Azure données catalogue** .

## <a name="q-what-is-azure-data-catalog"></a>Q : quelles est le catalogue de données Azure ?

A: catalogue de données Microsoft Azure est un service entièrement géré hébergé dans le cloud de Microsoft Azure qui sert d’un système d’enregistrement et système de détection des sources de données d’entreprise. Catalogue de données Azure fournit des fonctionnalités qui permettent à n’importe quel utilisateur – des analystes aux scientifiques des données pour les développeurs – au livre, découvrir, comprennent et utilisent les sources de données.

## <a name="q-what-customer-challenges-does-azure-data-catalog-solve"></a>Q : quel client défis est de catalogue de données Azure résoudre ?

Catalogue de données Azure relève le défi de découverte de source de données et des « données foncées » en permettant aux utilisateurs de découvrir et de comprendre les sources de données d’entreprise.

## <a name="q-who-are-the-target-audiences-for-azure-data-catalog"></a>Q : quelles sont les audiences ciblées pour le catalogue de données Azure ?

Catalogue de données Azure fournit des fonctions pour les utilisateurs techniques et non techniques, y compris :

- Les développeurs de données, BI et professionnels de l’informatique Analytique : qui sont chargées de la production des données et analytique du contenu aux autres pour consommer
-   Conseils de données : Ceux qui disposent des connaissances sur les données, ce que cela signifie et comment il est destiné à être utilisé et leur usage
- Consommateurs de données : Celles qui doivent être en mesure de découvrir facilement, comprendre et se connecter aux données nécessaires pour effectuer leur travail à l’aide de l’outil de leur choix.
- Central informatique : ceux qui ont besoin de rendre des centaines de sources de données détectable pour les utilisateurs professionnels, et qui ont besoin de maintenir la supervision sur l’utilisation de données et par qui

## <a name="q-what-is-the-azure-data-catalog-region-availability"></a>Q : quelle est la disponibilité de région de catalogue de données Azure ?

Les services de catalogue de données Azure sont actuellement disponibles dans les centres de données suivantes :

- États-Unis Ouest
- Les États-Unis
- Europe de l’ouest
- Europe du Nord
- Est de l’Australie
- Asie du Sud-est

## <a name="q-what-are-the-limits-on-the-number-of-data-assets-in-azure-data-catalog"></a>Q : quelles sont les limites sur le nombre de ressources de données dans le catalogue de données Azure ?

L’édition d’Azure données catalogue gratuit est limité à 5 000 de données enregistré.

L’Édition Standard de Azure données catalogue prend en charge jusqu'à 100 000 éléments de données enregistré.

## <a name="q-what-are-the-supported-data-source-and-asset-types"></a>Q : quels sont les types d’actifs et de la source de données pris en charge ?

Pour la liste des sources de données actuellement pris en charge, reportez-vous à [Données catalogue DSR](data-catalog-dsr.md) .

## <a name="q-how-do-i-request-support-for-another-data-source"></a>Q : comment demander de l’aide pour une autre source de données ?

Demandes de fonctionnalités et d’autres informations peuvent être soumis dans le [catalogue de données Azure forum](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409).

## <a name="q-how-do-i-get-started-with-azure-data-catalog"></a>Q : comment commencer avec le catalogue de données Azure ?

Le meilleur endroit pour démarrer est en suivant les instructions de [Mise en route avec les données de catalogue](data-catalog-get-started.md). Cet article est une présentation de bout en bout des fonctionnalités dans le service.

## <a name="q-how-do-i-register-my-data"></a>Q : comment enregistrer mes données ?

Pour enregistrer vos données dans le catalogue de données Azure, lancez l’outil d’inscription de catalogue de données Azure à partir de la zone de « Publication » du portail du catalogue de données Azure. Dans l’application de publication du catalogue de données Azure, connectez-vous en utilisant les mêmes informations d’identification que vous permet d’accéder au portail du catalogue de données Azure, puis sélectionnez la source de données et les actifs spécifiques que vous souhaitez enregistrer.

## <a name="q-what-properties-are-extracted-for-data-assets-that-are-registered"></a>Q : quelles propriétés sont extraites pour les ressources de données sont enregistrés ?

Les propriétés spécifiques diffèrent à partir de la source de données à la source de données, mais en général, le service de publication du catalogue de données Azure extraira les informations suivantes :

- Nom de la ressource
- Type d’immobilisation
- Description de la ressource
- Noms de colonne attribut / / /
- Types de données de colonne attribut / / /
- Description de la colonne attribut / / /

> [AZURE.IMPORTANT] Enregistrement des données avec Azure données catalogue ne pas déplacer ou copier les données dans le nuage. Enregistrement des actifs à partir d’une source de données copie les métadonnées des actifs d’Azure, mais les données restent dans l’emplacement de source de données existante. La seule exception à cette règle est si un utilisateur choisit de télécharger les enregistrements de l’aperçu ou un profil de données lors de l’enregistrement de ressources. Lorsque y compris un aperçu, jusqu'à 20 enregistrements seront copiés à partir de chaque ressource et sont stockées sous la forme d’une capture instantanée dans le catalogue de données Azure. Lorsque vous incluez un profil des données, des informations globales (par exemple, la taille des tables, les valeurs null en pourcentage par colonne et les valeurs minimales, maximales et moyennes de colonnes) seront calculées et incluses dans les métadonnées stockées dans le catalogue.

<br/>

> [AZURE.NOTE] Pour les sources de données comme SQL Server Analysis Services qui ont une propriété de **Description** de première classe, le catalogue de données Azure publication d’application extrait cette valeur de propriété. Pour SQL Server bases de données relationnelles, qui ne disposent pas d’une propriété de **Description** de première classe, l’application de publication du catalogue de données Azure extrait la valeur à partir de la ms_description étendue de propriété pour les objets et les colonnes. Pour plus d’informations, consultez TechNet, [à l’aide des propriétés étendues sur les objets de base de données](https://technet.microsoft.com/library/ms190243%28v=sql.105%29.aspx).

## <a name="q-how-long-should-it-take-for-newly-registered-assets-to-appear-in-azure-data-catalog"></a>Q : combien de temps doit-elle prendre pour immobilisations récemment enregistrées apparaissent dans le catalogue de données Azure ?

Après avoir enregistré des immobilisations avec catalogue de données Azure qu’il peut y avoir un délai de 5 à 10 secondes avant d’apparaître dans le portail du catalogue de données Azure.

## <a name="q-how-do-i-annotate-and-enrich-the-metadata-for-my-registered-data-assets"></a>Q : comment annoter et enrichir les métadonnées pour mes actifs de données enregistré ?

Pour fournir des métadonnées pour les actifs inscrits, le plus simple consiste à Sélectionnez l’actif dans le portail du catalogue de données Azure puis entrez les valeurs de métadonnées dans le volet Propriétés ou le volet Schéma pour l’objet sélectionné.

Vous pouvez également fournir des métadonnées, telles que les balises et d’experts au cours du processus d’inscription. Les valeurs fournies dans le service de publication du catalogue de données Azure seront appliquent à tous les actifs en cours d’inscription à ce moment. Pour afficher les objets récemment enregistrés dans le portail d’annotation supplémentaire, cliquez sur le bouton de **Portail d’affichage** sur l’écran final de l’application de publication du catalogue de données Azure.

## <a name="q-how-do-i-delete-my-registered-data-objects"></a>Q : comment pour supprimer mes objets de données enregistré ?

Vous pouvez supprimer un objet à partir du catalogue de données Azure en sélectionnant l’objet dans le portail, puis en cliquant sur le bouton **Supprimer** . Cela supprimera les métadonnées pour l’objet du catalogue de données Azure, mais n’affectera pas la source de données sous-jacente réelle.

## <a name="q-what-is-an-expert"></a>Q : qu’est un expert ?

Un expert est une personne qui a un point de vue informé sur un objet de données. Un objet peut avoir plusieurs experts. Un expert n’a pas besoin d’être des « propriétaire » d’un objet ; l’expert est quelqu'un qui sait comment les données peuvent et doivent être utilisés.

## <a name="q-how-do-i-share-information-with-the-azure-data-catalog-team-if-i-encounter-problems"></a>Q : comment partager des informations avec l’équipe de catalogue de données Azure si je rencontre des problèmes ?

Veuillez utiliser le forum du catalogue de données Azure pour signaler les problèmes, partager des informations et poser des questions. Vous trouverez le forum à http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409

##<a name="q-does-azure-data-catalog-work-with-this-other-data-source-im-interested-in"></a>Q : catalogue de données Azure fonctionne-t-il avec cette autre source de données que m’intéresse ?
Nous travaillons activement sur l’ajout de plusieurs sources de données de catalogue de données Azure. S’il existe une source de données que vous souhaitez voir pris en charge, veuillez suggère (ou votre prise en charge de la voix si elle a déjà été suggéré) dans le [catalogue de données Azure forum](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409).

## <a name="q-how-is-azure-data-catalog-related-to-the-data-catalog-in-power-bi-for-office-365"></a>Q : comment Azure données catalogue est lié au catalogue de données BI de puissance pour Office 365 ?

Vous pouvez considérer du catalogue de données Azure comme une évolution du catalogue de données. Catalogue de données Azure offre des fonctionnalités similaires pour la découverte et la publication de source de données, mais est ciblée sur des scénarios plus larges et ne dépendent pas Office 365. Peu de temps après que le catalogue de données Azure est généralement disponible les deux catalogues fusionne dans un seul service.

## <a name="q-what-permissions-does-a-user-need-to-register-assets-with-azure-data-catalog"></a>Q : quelles sont les autorisations utilisateur doit-il enregistrer des immobilisations avec catalogue de données Azure ?

L’utilisateur qui exécute l’outil d’inscription de catalogue de données Azure a besoin d’autorisations sur la source de données qui va lui permettre de lire les métadonnées à partir de la source. Si l’utilisateur sélectionne également inclure un aperçu, l’utilisateur doit également disposer des autorisations qui lui permettent de lire des données à partir des objets en cours d’enregistrement.

## <a name="q-will-azure-data-catalog-be-made-available-for-on-premises-deployment-as-well"></a>Q : catalogue de données Azure seront disponible pour le déploiement sur site ?

Catalogue de données Azure est un service en nuage qui peut fonctionner avec des sources de données à la fois locaux et cloud, offrant une solution de découverte de source de données hybride. Il n’existe actuellement aucun plan pour une version du service de catalogue de données Azure qui s’exécuteront sur site.

##<a name="q-can-we-extract-more--richer-metadata-from-the-data-sources-we-register"></a>Q : pouvons extraire les métadonnées plus / plus riche à partir de sources de données que nous inscrire ?

Nous travaillons activement pour étendre les fonctionnalités de catalogue de données Azure. S’il existe des métadonnées supplémentaires que vous souhaiteriez extraits à partir de la source de données lors de votre inscription, veuillez suggérer qu’il (ou votent dans ce sens si elle a déjà été suggéré) dans le [catalogue de données Azure forum](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409). À l’avenir nous permettra de tiers d’ajouter de nouveaux types de source de données via une API d’extensibilité.

## <a name="q-how-do-i-restrict-the-visibility-of-registered-data-assets-so-that-only-certain-people-can-discover-them"></a>Q : comment limiter la visibilité des actifs de données enregistrées, afin que seules certaines personnes puissent découvrir les ?

R : sélectionnez les éléments de données dans le catalogue de données Azure et cliquez sur le bouton « Appropriation ». Les propriétaires des ressources de données dans le catalogue de données Azure peuvent modifier les paramètres de visibilité, pour autoriser tous les utilisateurs de catalogue pour découvrir les ressources possédées, ou pour limiter la visibilité à des utilisateurs spécifiques.

## <a name="q-how-do-i-update-the-registration-for-a-data-asset-to-that-changes-in-the-data-source-are-reflected-in-the-catalog"></a>Q : comment mettre à jour l’enregistrement d’un actif de données modifiées dans la source de données sont reflétées dans le catalogue ?

R : pour mettre à jour les métadonnées pour les ressources de données qui sont déjà enregistrés dans le catalogue, réinscrivez simplement la source de données qui contient les ressources. Toutes les modifications dans la source de données, telles que les colonnes soient ajoutés ou supprimés à partir de tables ou de vues, seront mis à jour dans le catalogue, mais toutes les annotations fournies par les utilisateurs seront conservées.

## <a name="q-my-question-isnt-answered-here--what-should-i-do"></a>Q : ma question ne trouve pas réponse ici, que dois-je faire ?

Accédez au [forum de catalogue de données Azure](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409). Questions posées il trouvera plus ici.
