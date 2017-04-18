<properties
    pageTitle="Gérer et surveiller vos connecteurs et les applications d’API dans le Service d’application | Microsoft Azure"
    description="Afficher les performances de vos connecteurs et les applications d’API dans les applications de logique ; architecture de microservices"
    services="app-service\logic"
    documentationCenter=".net,nodejs,java"
    authors="MandiOhlinger"
    manager="anneta"
    editor="cgronlun"/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="mandia"/>

# <a name="manage-and-monitor-your-built-in-api-apps-and-connectors"></a>Gérer et surveiller les connecteurs et les applications d’API intégrées

>[AZURE.NOTE] Cette version de l’article s’applique à la version du schéma logique apps 2014-12-01-aperçu.

Vous avez créé une application API intégrée. Quoi encore ?

Dans Azure, chaque application API est un site web séparé hébergé sur Azure. Par conséquent, vous pouvez facilement voir le nombre de requêtes est effectué et de voir la quantité de données est utilisé par le connecteur. Vous pouvez également sauvegarder votre application API, créer des alertes, activer Tinfoil sécurité et ajouter des utilisateurs et des rôles.

Cette rubrique décrit certaines des options différentes pour gérer votre application API.

Pour voir ces fonctions intégrées, ouvrez votre application API dans [Azure portal](http://go.microsoft.com/fwlink/p/?LinkID=525040). Si l’application API est sur votre startboard, sélectionnez-la pour ouvrir les propriétés. Vous pouvez également sélectionner **Parcourir** **Applications d’API**et sélectionnez ensuite votre application API :

![][browse]

## <a name="see-the-properties-you-entered"></a>Consultez les propriétés que vous avez entré

Lorsque vous ouvrez l’application API, il existe plusieurs fonctionnalités et tâches :

![][settings]

Vous pouvez :

- **Paramètres** présentant des informations spécifiques sur l’API d’application, y compris les détails de votre abonnement et répertorie les utilisateurs qui ont accès à votre application d’API. Vous pouvez également augmenter ou diminuer le nombre d’instances de votre application d’API à l’aide de la fonctionnalité d’échelle ; Parmi les autres fonctionnalités.
- Utilisez les boutons de **démarrage** et **d’arrêt** pour contrôler l’application API.
- Lorsque les mises à jour sont apportées aux fichiers sous-jacent utilisés par votre application d’API, vous pouvez cliquer sur **mise à jour** pour obtenir les versions les plus récentes. Par exemple, s’il existe un correctif ou une mise à jour de sécurité publiées par Microsoft, en cliquant sur **mettre à jour** automatiquement met à jour votre application API afin d’inclure ce correctif.
- Sélectionnez le **Planning** à la mise à niveau ou déclasser basées sur l’utilisation de données de l’application API. Vous pouvez également utiliser cette fonctionnalité pour voir l’utilisation de vos données.
- Lorsque vous créez un connecteur qui possède des tables, comme le connecteur SQL, vous pouvez éventuellement entrer un nom de table pour vous connecter à. Un schéma basé sur la table est automatiquement créé et disponible lorsque vous cliquez sur **Télécharger les schémas**. Vous pouvez ensuite utiliser ce schéma téléchargé pour créer une transformation ou une carte.

## <a name="change-your-connector-or-api-configuration-values-you-entered"></a>Modifier votre connecteur ou valeurs de configuration API saisis

Après avoir configuré ou créé votre connecteur intégré, vous pouvez modifier les valeurs saisies. Par exemple, si vous avez configuré le connecteur SQL et que vous souhaitez modifier le nom de la table ou le nom de SQL Server, vous cela dans la lame API App de votre connecteur.

Les étapes comprennent :

1. Ouvrez votre connecteur ou une API App. Lorsque vous le faites, la lame API App s’ouvre.
2. Dans **Essentials**, cliquez sur le lien hypertexte sous la propriété de l’hôte. Le lien hypertexte est nommé quelque chose comme *slackconnector* ou *microsoftsqlconnector123*:

    ![][apiapphost]

3. Dans la lame API Application Host, sélectionnez **paramètres**. Dans la lame de paramètres, sélectionnez **Paramètres de l’Application**. Les valeurs de configuration sont répertoriées sous les **Paramètres de l’application**:

    ![][hostsettings]

4. Cliquez sur le paramètre que vous souhaitez modifier, entrez la nouvelle valeur et **Enregistrer** vos modifications.


## <a name="install-the-hybrid-connection-manager---optional"></a>Installer le Gestionnaire de connexion hybride - facultatif

![][hcsetup]

Le Gestionnaire de connexion hybride vous donne la possibilité de se connecter à un système local, tels que SQL Server ou SAP. Cette connectivité hybride utilise le Bus des services Azure se connecter et de contrôler la sécurité entre vos ressources Azure et vos ressources sur site.

Consultez [l’aide du Gestionnaire de connexion hybride dans le Service d’application Azure](app-service-logic-hybrid-connection-manager.md).

> [AZURE.NOTE] Le Gestionnaire de connexion hybride est nécessaire uniquement si vous vous connectez à une ressource locale derrière votre pare-feu. Si vous ne vous connectez pas à un système local, le Gestionnaire de connexion hybride peut ne pas figurer dans votre carte de connecteur.

## <a name="monitor-the-performance"></a>Surveiller les performances
Des mesures de performances sont des fonctionnalités intégrées et inclus avec chaque application API que vous créez. Ces mesures sont spécifiques à votre API application hébergée dans Azure. Mesures de l’échantillon :

![][monitoring]

Vous pouvez :

- Sélectionnez **erreurs et demandes** d’ajouter des mesures de performances, y compris les codes d’erreur HTTP communément connue, 200, 400, ou des codes d’état HTTP 500. Vous pouvez également voir les temps de réponse, voir combien de requêtes est apportées à l’application de l’API et est la quantité de données vont et viennent de la quantité de données. Selon les mesures de performances, vous pouvez créer des alertes de courrier électronique si une mesure dépasse un seuil de votre choix.
- **L’utilisation**, vous pouvez voir quel **processeur** est utilisé par l’application de l’API, passez en revue le **Quota d’utilisation** de actuel en Mo et consultez l’utilisation maximale des données en fonction de votre niveau de coût. **Estimation dépenses** peut vous aider à déterminer le coût potentiel de l’exécution de votre application d’API.
- Sélectionnez le **processus** pour ouvrir le processus Explorer. Affiche vos instances de web et de leurs propriétés, y compris l’utilisation des threads nombre et de la mémoire.

À l’aide de ces outils, vous pouvez déterminer si le Plan de Service d’application doivent être agrandie ou mise à l’échelle vers le bas, en fonction des besoins de votre entreprise. Ces fonctionnalités sont intégrées au portail avec aucun des outils supplémentaires requis.

## <a name="control-the-security"></a>Contrôle de la sécurité

Applications d’API utilise la sécurité basée sur les rôles. Ces rôles s’appliquent à l’expérience d’Azure entière, y compris les applications d’API et d’autres ressources Azure. Les rôles sont les suivants :

Rôle | Description
--- | ---
Propriétaire | Ont un accès complet à l’environnement de gestion et peut donner accès à d’autres utilisateurs ou groupes.
Collaborateur | Avoir un accès complet à l’environnement de gestion. Ne peut pas accéder à d’autres utilisateurs ou groupes.
Lecteur | Peut afficher toutes les ressources, à l’exception des secrets.
Administrateur d’accès utilisateur | Peut afficher toutes les ressources, de créer et gérer des rôles et créer/gérer de prise en charge des tickets.

Voir [contrôle d’accès basé sur les rôles dans le portail Microsoft Azure](../active-directory/role-based-access-control-configure.md).

Vous pouvez facilement ajouter des utilisateurs et leur attribuer des rôles spécifiques à votre application d’API. Le portail vous montre les utilisateurs qui ont accès et leur rôle :

![][access]  

- Sélectionnez **les utilisateurs** à ajouter un utilisateur attribuer un rôle et supprimer un utilisateur.
- Sélectionnez les **rôles** pour tous les utilisateurs dans un rôle spécifique, reportez-vous à la section Ajout d’un utilisateur à un rôle et supprimer un utilisateur d’un rôle.


## <a name="more-good-stuff"></a>Plusieurs choses
- Sélectionnez la **définition de l’API** pour ouvrir le fichier Swagger créé automatiquement pour votre application d’API spécifique.
- Sélectionnez **dépendances** pour afficher les fichiers requis par votre application API. Par exemple, si vous utilisez le connecteur SAP, vous installez des fichiers supplémentaires dans le Gestionnaire de connexion local hybride. Ces dépendances sont indiquées dans la blade d’application API.

>[AZURE.IMPORTANT] Lorsque vous ouvrez les propriétés de votre application d’API et regardez sous **Essentials**, il existe des liens **hôte** et la **passerelle** qui ouvrent de nouvelles blades :
>
> ![][host]
>
>Ces propriétés sont spécifiques au site Web qui héberge votre application API. Lorsque vous utilisez une API App ou un connecteur intégré, la plupart de ces propriétés ne s’applique pas vraiment et nous vous recommandons de ne pas la mettre à jour ces propriétés. Si vous avez créé votre propre API application dans Visual Studio et que vous déployé dans votre abonnement Azure, vous pouvez utiliser des pales de l’hôte et la passerelle. <br/><br/>


>[AZURE.NOTE] Pour commencer avec la logique d’applications avant l’ouverture d’un compte Azure, accédez à [Essayer une application de logique](https://tryappservice.azure.com/?appservice=logic). Vous pouvez créer une application de logique de démarrage de courte durée. Aucune carte de crédit nécessaire et sans engagement.

## <a name="read-more"></a>Pour en savoir plus

[Surveillance de vos applications de logique](app-service-logic-monitor-your-logic-apps.md)<br/>
[Connecteurs et la liste des applications API dans le Service d’application](app-service-logic-connectors-list.md)<br/>
[Contrôle d’accès basé sur les rôles dans le portail Microsoft Azure](../active-directory/role-based-access-control-configure.md)<br/>
[À l’aide du Gestionnaire de connexion hybride dans le Service d’application Azure](app-service-logic-hybrid-connection-manager.md)


<!--Image references-->
[browse]: ./media/app-service-logic-monitor-your-connectors/browse.png
[settings]: ./media/app-service-logic-monitor-your-connectors/settings.png
[hcsetup]: ./media/app-service-logic-monitor-your-connectors/hcsetup.png
[monitoring]: ./media/app-service-logic-monitor-your-connectors/monitoring.png
[access]: ./media/app-service-logic-monitor-your-connectors/access.png
[host]: ./media/app-service-logic-monitor-your-connectors/host.png
[hostsettings]: ./media/app-service-logic-monitor-your-connectors/hostsettings.png
[apiapphost]: ./media/app-service-logic-monitor-your-connectors/apiapphost.png
