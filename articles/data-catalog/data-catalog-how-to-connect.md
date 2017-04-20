<properties
   pageTitle="Comment se connecter à des sources de données | Microsoft Azure"
   description="Article d’apprentissage mise en surbrillance de la façon de se connecter aux sources de données découvertes avec le catalogue de données Azure."
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
   ms.date="09/15/2016"
   ms.author="maroche"/>


# <a name="how-to-connect-to-data-sources"></a>Comment se connecter à des sources de données

## <a name="introduction"></a>Introduction
**Catalogue de données de Microsoft Azure** est un service en nuage entièrement géré qui sert d’un système d’enregistrement et système de détection des sources de données d’entreprise. En d’autres termes, le **Catalogue de données Azure** consiste à aider les utilisateurs à découvrir, à comprendre et à utiliser des sources de données et aider les entreprises à tirer davantage de valeur à partir de leurs données. Un aspect clé de ce scénario à l’aide de données – une fois qu’un utilisateur découvre une source de données et comprend son objectif, l’étape suivante consiste à se connecter à la source de données pour mettre les données à utiliser.

## <a name="data-source-locations"></a>Emplacements de source de données
Lors de l’enregistrement de source de données, **Catalogue de données Azure** reçoit des métadonnées de la source de données. Ces métadonnées incluent les détails de l’emplacement de la source de données. Les détails de l’emplacement varie à partir de la source de données à la source de données, mais il contient toujours les informations nécessaires à la connexion. Par exemple, l’emplacement d’une table SQL Server inclut le nom du serveur, nom de la base de données, nom de schéma et nom de la table, alors que l’emplacement d’un rapport SQL Server Reporting Services comprend le nom du serveur et le chemin d’accès au rapport. Autres types de source de données auront des emplacements qui reflètent la structure et les fonctionnalités du système source.

## <a name="integrated-client-tools"></a>Outils client intégré
Pour vous connecter à une source de données le plus simple consiste à utiliser le « ouvert dans... » menu dans le **Catalogue de données Azure** portal. Ce menu affiche une liste d’options pour la connexion à la ressource de données sélectionnées.
Lorsque vous utilisez l’affichage en mosaïque par défaut, ce menu est disponible sur le chaque mosaïque.

 ![Ouverture d’une table SQL Server dans Excel à partir de la mosaïque d’actifs de données](./media/data-catalog-how-to-connect/data-catalog-how-to-connect1.png)

Lorsque vous utilisez l’affichage de la liste, le menu est disponible dans la barre de recherche en haut de la fenêtre du portail.

 ![Ouverture d’un rapport SQL Server Reporting Services dans le Gestionnaire de rapports à partir de la barre de recherche](./media/data-catalog-how-to-connect/data-catalog-how-to-connect2.png)

## <a name="supported-client-applications"></a>Applications Client pris en charge
Lors de l’utilisation du « ouvrir dans... » menu de sources de données dans le portail du catalogue de données Azure, l’application client approprié doit être installé sur l’ordinateur client.

| Ouvrir dans l’application | Extension de fichier / protocole | Versions d’application pris en charge |
| --- | --- | --- |
| Excel | .odc | Excel 2010 ou version ultérieure |
| Excel (Top 1000) | .odc | Excel 2010 ou version ultérieure |
| Requête de puissance | .xlsx | 2016 d’Excel ou Excel 2010 ou 2013 d’Excel avec la requête de puissance pour le complément Excel installé
| Bureau de BI de puissance | .pbix | Alimentation BI bureau juillet 2016 ou version ultérieure |
| Outils de données de SQL Server | vsweb : / / | Mise à jour de Visual Studio 2013 4 ou version ultérieure avec les outils SQL Server installé |
| Gestionnaire de rapports | http:// | Consultez la [Configuration requise du navigateur pour SQL Server Reporting Services](https://technet.microsoft.com/en-us/library/ms156511.aspx) |

## <a name="your-data-your-tools"></a>Vos données, vos outils
Les options disponibles dans le menu dépend du type de données actuellement sélectionné. Bien sûr, pas tous les outils possibles seront inclus dans le « ouvert dans... » menu, mais il est encore facile à connecter à la source de données à l’aide de n’importe quel outil de client. Lorsqu’un élément de données est sélectionné dans le **Catalogue de données Azure** portal, l’emplacement complet s’affiche dans le volet Propriétés.

 ![Informations de connexion pour une table SQL Server](./media/data-catalog-how-to-connect/data-catalog-how-to-connect3.png)

Les informations de connexion diffère du type de source de données au type de source de données, mais les informations incluses dans le portail vous fournira tout ce dont vous avez besoin pour vous connecter à la source de données dans n’importe quel outil de client. Les utilisateurs peuvent copier les détails de connexion pour les sources de données qu’ils ont découverts à l’aide du **Catalogue de données Azure**, ce qui leur permet de travailler avec les données de leur outil de choix.

## <a name="connecting-and-data-source-permissions"></a>Autorisations de source de données et connexion
Bien que **Azure données** rend les sources de données détectable, accès aux données elles-mêmes demeurent sous le contrôle de l’administrateur ou le propriétaire du code source de données. Découverte de source de données dans le **Catalogue de données Azure** ne donne pas un utilisateur toutes les autorisations pour accéder à la source de données.

Pour rendre plus facile pour les utilisateurs à découvrir une source de données, mais n’êtes pas autorisé à accéder à ses données, les utilisateurs peuvent fournir des informations dans la propriété de la demande d’accès lors de l’annotation d’une source de données. Les informations fournies ici – y compris les liens vers le processus ou le point de contact pour l’accès de source de données – sont présentées ainsi que les informations d’emplacement de source données dans le portail.

 ![Informations de connexion avec des instructions d’accès demande fourni](./media/data-catalog-how-to-connect/data-catalog-how-to-connect4.png)

##<a name="summary"></a>Résumé
Enregistrement d’une source de données avec **Azure catalogue de données** rend ces données détectable en copiant les métadonnées structurelles et descriptifs à partir de la source de données dans le service de catalogue. Une fois une source de données a été enregistrée et découverte, les utilisateurs peuvent se connecter à la source de données à partir du portail de **Catalogue de données Azure** » ouvert dans... » » menu ou à l’aide de leurs outils de données de choix.

## <a name="see-also"></a>Voir aussi
- Didacticiel de [Mise en route de catalogue de données Azure](data-catalog-get-started.md) pour plus d’informations détaillées sur la façon de se connecter aux sources de données.
