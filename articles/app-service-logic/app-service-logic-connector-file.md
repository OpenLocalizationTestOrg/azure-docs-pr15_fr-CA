<properties
    pageTitle="À l’aide du connecteur de fichier dans les applications de logique | Service d’application Microsoft Azure"
    description="Comment créer et configurer le connecteur de fichier ou l’application d’API et l’utiliser dans une logique d’application dans le Service d’application Azure"
    authors="rajeshramabathiran"
    manager="erikre"
    editor=""
    services="logic-apps"
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/01/2016"
    ms.author="rajram"/>

# <a name="get-started-with-the-file-connector-and-add-it-to-your-logic-app"></a>Mise en route avec le connecteur de fichier et l’ajouter à votre application logique
>[AZURE.NOTE] Cette version de l’article s’applique à la version du schéma logique apps 2014-12-01-aperçu.

Se connecter à un système de fichiers à télécharger, téléchargement et plus encore pour vos fichiers sur un ordinateur hôte. Applications de logique peuvent déclencher basée sur une variété de sources de données et offre des connecteurs pour récupérer et traiter les données. Vous pouvez ajouter le lien du fichier à vos données de workflow et des processus métier dans le cadre de ce flux de travail au sein d’une application de logique. 

Le connecteur de fichier utilise le Gestionnaire de connexion hybride pour la connectivité hybride pour le système de fichiers hôte.

## <a name="creating-a-file-connector-for-your-logic-app"></a>Création d’un connecteur de fichier pour votre application logique ##
Pour utiliser le connecteur de fichier, vous devez d’abord créer une instance de l’application API du connecteur fichier. Pour ce faire comme suit :

1.  Ouvrez le marché Azure à l’aide de + nouvelle option sur le côté gauche du portail Azure.
2.  Recherchez « connecteur de fichier ».
3.  Résultats de la recherche, sélectionnez **Le connecteur de fichier (aperçu)** .
4.  Cliquez sur le bouton **créer**
5.  Configurez le connecteur de fichier comme suit :  
![][1]

    - **Nom** : donnez un nom pour votre connecteur de fichier
    - **Paramètres de package**
        - **Dossier racine** - spécifier le chemin d’accès du dossier racine de votre machine hôte. Par exemple. D:\FileConnectorTest
        - **Chaîne de connexion de Service Bus** - fournir une chaîne de connexion de Service Bus. Assurez-vous que l’espace de noms de bus de service est de type Standard et non pour permettre l’utilisation de relais de Bus de Service de base.  Relais de Bus de service est utilisé pour se connecter au Gestionnaire de connexion hybride.
    - **Plan de Service d’application** - sélectionner ou créer un plan de Service d’application
    - **Niveau de tarification** - choisir un niveau de tarification pour le connecteur
    - **Groupe de ressources** - sélectionner ou créer un groupe de ressources où le connecteur doit résider
    - **Abonnement** , choisissez un abonnement que vous souhaitez que ce connecteur doit être créé dans
    - **Emplacement** - choisir la zone géographique dans laquelle vous souhaitez que le connecteur à déployer

4. Cliquez sur Créer. Un nouveau connecteur de fichier sera créé.

## <a name="configure-hybrid-connection-manager"></a>Configurer le Gestionnaire de connexion hybride ##
Une fois l’instance API App créé, accédez à son tableau de bord.  Pour ce faire, cliquez sur Parcourir > API applications > sélectionnez votre connecteur de fichier API App.  Le Gestionnaire de connexion hybride doit être configuré à partir d’ici.
Pour plus d’informations sur la configuration et le Gestionnaire de connexion hybride de dépannage, consultez [l’aide du Gestionnaire de connexion hybride].

## <a name="using-the-file-connector-in-your-logic-app"></a>À l’aide du connecteur de fichier dans votre logique d’application ##
Une fois votre application API est créée, vous pouvez désormais utiliser le connecteur de fichier pour votre application logique sous forme d’action. Pour ce faire, vous devez :

1.  Créer une nouvelle application logique et choisir le même groupe de ressources qui a le connecteur de fichier. Suivez les instructions pour [créer une nouvelle application logique].

2.  Ouvrir l’application logique créée pour ouvrir les Concepteur d’applications de logique et de configurer votre flux de « Déclencheurs et Actions ».

3.  Le connecteur de fichier apparaissent dans la section « API applications dans ce groupe de ressources » dans la galerie sur le côté droit.

4.  Vous pouvez supprimer l’application API du connecteur fichier dans l’éditeur en cliquant sur le lien « fichier ». connecteur de fichier expose un déclencheur et 4 Actions :  
![][5]

6.  Chacune d'entre elles expose certaines propriétés. L’image ci-dessous répertorie les propriétés du déclencheur et obtenir le fichier Action :  
![][6]

7. Une fois que celles-ci sont configurées, le déclencheur et l’Action peuvent servir dans vos flux. De même, les autres actions peuvent être configurées ainsi.

> [AZURE.NOTE] Le déclencheur de fichier va supprimer le fichier une fois qu’il est lu correctement à partir du dossier.

## <a name="file-connector-rest-apis"></a>Connecteur reste API de fichier ##
Pour utiliser le connecteur en dehors d’une application de logique, les API reste exposée par le connecteur peuvent être exploitées. Vous pouvez afficher les définitions de cette API à l’aide de parcourir -> Api App -> connecteur de fichier. Cliquez maintenant sur l’objectif de la définition de l’API dans la Section Résumé pour afficher toutes les API exposées par ce connecteur :  
![][7]

Vous trouverez des détails des API au [connecteur de fichier définition d’API].

## <a name="do-more-with-your-connector"></a>Faire plus avec votre connecteur
Maintenant que le connecteur est créé, vous pouvez l’ajouter à un workflow d’entreprise à l’aide d’une application de logique. Consultez [Quelles sont les applications logique ?](app-service-logic-what-are-logic-apps.md).

>[AZURE.NOTE] Si vous souhaitez démarrer avec les applications Azure logique avant l’ouverture d’un compte Azure, accédez à [essayez de logique d’application](https://tryappservice.azure.com/?appservice=logic), où vous pouvez créer une application de logique de courte durée starter immédiatement dans le Service d’application. Aucune carte de crédit obligatoire ; aucun des engagements.

Permet d’afficher la référence de l’API de REST Swagger à des [connecteurs et des applications de référence sur l’API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Vous pouvez également consulter les performances statistiques et contrôle de sécurité sur le connecteur. Reportez-vous à la section [gérer et surveiller vos applications d’API et un connecteur intégré](app-service-logic-monitor-your-connectors.md).

<!-- Image reference -->
[1]: ./media/app-service-logic-connector-file/img1.PNG
[5]: ./media/app-service-logic-connector-file/img5.PNG
[6]: ./media/app-service-logic-connector-file/img6.PNG
[7]: ./media/app-service-logic-connector-file/img7.PNG

<!-- Links -->
[Créer une nouvelle application logique]: app-service-logic-create-a-logic-app.md
[Fichier de définition d’API de connecteur]: https://msdn.microsoft.com/library/dn936296.aspx
[L’aide du Gestionnaire de connexion hybride]: app-service-logic-hybrid-connection-manager.md
