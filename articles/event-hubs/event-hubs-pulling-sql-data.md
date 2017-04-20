<properties
   pageTitle="Extraction des données SQL Azure des concentrateurs événement | Microsoft Azure"
   description="Vue d’ensemble des événements importer à partir de l’exemple SQL"
   services="event-hubs"
   documentationCenter="na"
   authors="spyrossak"
   manager="timlt"
   editor=""/>

<tags 
   ms.service="event-hubs"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/25/2016"
   ms.author="spyros;sethm" />

# <a name="pulling-data-from-sql-into-an-azure-event-hub"></a>Extraction des données à partir de SQL dans un concentrateur d’événements Azure

Une architecture classique d’une application de traitement de données en temps réel implique tout d’abord de le pousser à un concentrateur d’événements Azure. Il peut être un scénario IoT, telles que la surveillance du trafic sur différents étire d’une autoroute, ou d’un scénario de jeu, les actions d’un atouts du participant frenzied, de surveillance ou d’un scénario d’entreprise, telles que la surveillance d’occupation du bâtiment. Dans ces cas, les producteurs de données sont des objets généralement externes produisant des données de série de l’heure dont vous avez besoin de collecter, d’analyser, de stocker et agir et vous devrez ajuster beaucoup d’efforts dans la création de l’infrastructure de ces processus. Ce que vous faire si vous voulez mettre dans les données d’un élément, comme une base de données au lieu d’une source de données de transmission en continu et l’utiliser conjointement avec vos autres données en flux continu ? Envisagez le cas où vous souhaitez utiliser Analytique de flux d’Azure, Explorer des données à distance (RDX) ou un autre outil pour analyser et traiter de variation lente des données à partir de Microsoft Dynamics AX ou un système de gestion des installations personnalisées ? Pour résoudre ce problème, nous avons écrit et un petit nuage exemple que vous pouvez modifier et déployer qui va extraire les données d’une table SQL et l’envoyer à un concentrateur d’événements Azure à utiliser en tant qu’entrée dans vos applications d’analyse en aval source ouverte. Se rendent compte qu’il s’agit d’un scénario rare, à l’inverse de ce que vous le feriez avec un concentrateur d’événements. Toutefois, si vous trouvez vous-même dans le cas où il est ce que vous devez faire, vous pouvez trouver le code dans la galerie d’exemples d’Azure, [ici](https://azure.microsoft.com/documentation/samples/event-hubs-dotnet-import-from-sql/).  

Notez que le code de cet exemple est qu’un exemple. Il est **pas** destinée à être une application de production et aucune tentative n’a été effectuée afin d’être adapté à une utilisation dans un tel environnement, il est stricly un soi-même, destinés aux développeurs, l’exemple. Vous devez passer en revue toutes sortes de sécurité, de performances, de fonctionnalités et facteurs de coût avant de s’installer sur une architecture de bout en bout.

## <a name="application-structure"></a>Structure d’application

L’application est écrite en C#, et le fichier readme.md dans l’échantillon contient toutes les informations dont vous avez besoin de modifier, de générer et de publier l’application. Les sections suivantes fournissent une vue d’ensemble de haut niveau de l’application.

Nous allons commencer en partant du principe que vous avez accès à une table SQL Azure. Vous devez également avoir configuré à un concentrateur d’événements Azure et connaître la connexion chaîne pour y accéder.

Au démarrage de la solution SqlToEventHub, il lit un fichier de configuration (App.config) pour obtenir un certain nombre de choses, comme indiqué dans le fichier readme.md. Ceux-ci sont suffisamment explicites, telles que le nom de la table de données, etc., et il est inutile de rehash les explications ici. 

Une fois que l’application a lu le fichier de configuration, il passe dans une boucle, lecture de la table SQL et distribution des enregistrements vers le concentrateur d’événements, puis attendre un intervalle défini par l’utilisateur de veille avant d’effectuer tout nouveau. Plusieurs choses sont à noter :

1. L’application est basée sur l’hypothèse que la table SQL est mis à jour par un processus externe, et que vous souhaitez envoyer tous les et uniquement les mises à jour à un concentrateur d’événements.
2. La table SQL doit avoir un champ qui porte un numéro unique et croissant, par exemple, un numéro d’enregistrement. Cela peut être aussi simple qu’un champ nommé « Id », ou autre chose qui est incrémenté comme tout ce qui met à jour la base de données ajoute des enregistrements tels que « Creation_time » ou « Sequence_number ». L’application notes et stocke la valeur de ce champ dans chaque itération. Dans chaque étape ultérieure dans la boucle, l’application recherche essentiellement dans la table pour tous les enregistrements où la valeur de ce champ est supérieure à la valeur qu’il vu la dernière itération de la boucle. Nous allons appeler cette dernière valeur de l’offset « ».
3. L’application crée une table à « TableOffsets » lors de son démarrage, pour stocker les offsets. La table est créée avec la requête « CreateOffsetTableQuery » définie dans le fichier de configuration. 
4. Il existe plusieurs requêtes utilisées pour travailler avec la table de décalage définie dans le fichier de configuration en tant que « OffsetQuery », « UpdateOffsetQuery » et « InsertOffsetQuery ». Vous ne devez pas modifier ces.
5. Enfin, la requête « requête » de données définie dans le fichier de configuration est la requête à exécuter pour extraire les enregistrements de la table SQL. Il est actuellement limitée aux enregistrements dans chaque passage dans la boucle pour des fins d’optimisation - haut 1 000 si, par exemple, 25 000 enregistrements ont été ajoutés à la base de données depuis la dernière requête, il peut prendre un certain temps pour exécuter la requête. En limitant chaque fois que les 1 000 enregistrements de la requête, les requêtes sont beaucoup plus rapides. Sélectionnant haut 1 000 simple exécute un push de lots successifs de 1 000 enregistrements vers le concentrateur d’événements.    

## <a name="next-steps"></a>Étapes suivantes

Pour déployer la solution, cloner ou télécharger l’application SqlToEventHub, modifiez le fichier App.config, créer et finalement le publier. Une fois que vous avez publié l’application, vous pouvez voir qu’il exécute dans Azure portal classique sous Services de Cloud et surveiller les événements arrivant dans votre concentrateur d’événements. Notez que la fréquence sera déterminée par deux éléments : la fréquence des mises à jour de la table SQL et l’intervalle de veille que vous avez spécifié dans le fichier de configuration de l’application.