<properties
   pageTitle="Utiliser la BI de puissance avec SQL Data Warehouse | Microsoft Azure"
   description="Conseils pour l’utilisation de puissance BI à l’entrepôt de données SQL Azure pour le développement de solutions."
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
   ms.date="05/31/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# <a name="use-power-bi-with-sql-data-warehouse"></a>Utiliser la BI de puissance avec SQL Data Warehouse
Comme la base de données SQL Azure entrepôt de données du SQL Direct Connect permet à utilisateur d’exploiter de puissante activer logique ainsi que les capacités analytiques de puissance BI.  Avec une connexion directe, les requêtes sont envoyées à votre magasin de données SQL Azure en temps réel comme vous explorez les données.  Cela, combiné avec l’échelle de l’entrepôt de données SQL, permet aux utilisateurs de créer des rapports dynamiques en quelques minutes contre plusieurs téraoctets de données.  En outre, l’introduction de l’ouvrir dans le bouton d’alimentation BI permet aux utilisateurs pour se connecter directement BI de puissance à leur SQL Data Warehouse sans la collecte d’informations provenant d’autres parties d’Azure.

Lorsque vous utilisez une connexion directe, Remarque :

+ Spécifiez le nom du serveur complet lors de la connexion (voir ci-dessous pour plus de détails)
+ Vérifiez les règles de pare-feu pour la base de données sont configurés sur « Autoriser l’accès aux services Azure ».
+ Toutes les actions telles que la sélection d’une colonne ou ajout d’un filtre interroge directement le data warehouse
+ Les mosaïques sont actualisés environ toutes les 15 minutes (actualisation n’a pas besoin d’être planifiés)
+ Q & r ne sont pas disponible pour la connexion directe des groupes de données
+ Modifications de schéma ne sont pas automatiquement prises
+ Toutes les requêtes de connexion directe doit expirer après 2 minutes

Afin d’améliorer l’expérience de ces restrictions et ces notes peuvent changer. Les étapes de connexion sont détaillées ci-dessous.  

## <a name="using-the-open-in-power-bi-button"></a>Utilisez le bouton « Ouvrir dans BI de puissance »
Le moyen le plus simple pour vous déplacer entre votre entrepôt de données de SQL et de la puissance BI est la bouton Ouvrir dans BI d’alimentation. Ce bouton vous permet de commencer en toute transparence la création de nouveaux tableaux de bord BI d’alimentation.  

1.  Pour commencer à votre instance SQL Data Warehouse dans le portail classique d’Azure.
2.  Cliquez sur le bouton « Ouvrir dans BI de puissance ».
3.  Si nous ne sommes pas en mesure de vous connecter directement, ou si vous ne disposez pas d’un compte courant BI, vous devez vous connecter.  
4.  Vous allez être redirigé vers la page de connexion SQL Data Warehouse, avec les informations de votre magasin de données SQL remplis au préalable.
5.  Après avoir entré vos informations d’identification vous sera entièrement connecté à votre magasin de données SQL.

## <a name="connecting-through-the-power-bi-portal"></a>Connexion via le portail d’alimentation BI
En plus d’utiliser la bouton Ouvrir dans BI de puissance, les utilisateurs peuvent également se connecter à leur magasin de données SQL via le portail de BI d’alimentation.

1.  Cliquez sur « Obtenir des données » au bas du volet de navigation.
2.  Sélectionnez « Bases de données ».
3.  Une fois sur la page de bases de données, sélectionnez « Entrepôt de données de SQL Azure », puis sur « Se connecter ».
4.  Entrez les informations de connexion nécessaires.  Votre nom de serveur et de base de données peuvent être trouvés dans le portail Azure.
5.  Vous sera dirigé vers la page principale de puissance BI et une fois que votre connexion est établie une nouvelle entrée sous « groupes » de données s’affiche avec le nom de votre instance.  
6.   Vous pouvez cliquer sur le nouveau groupe de données à Explorer toutes les tables et les vues dans votre base de données. Sélection d’une colonne enverra une requête à la source, la création dynamique de votre objet visuel. Ces effets visuels peuvent être enregistrés dans un nouveau rapport et épinglés revenir à votre tableau de bord.

<!--Image references-->

<!--Article references-->
[SQL Data Warehouse development overview]:  ./sql-data-warehouse-overview-develop/
[SQL Data Warehouse integration overview]:  ./sql-data-warehouse-overview-integration/

<!--MSDN references-->

<!--Other Web references-->
