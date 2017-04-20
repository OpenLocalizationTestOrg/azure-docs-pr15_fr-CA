<properties
    pageTitle="Migrer à partir de Services mobiles à une application Mobile Application Service"
    description="Apprenez à migrer facilement votre application de Services mobiles à une application de Service Mobile App"
    services="app-service\mobile"
    documentationCenter=""
    authors="adrianhall"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="adrianha"/>

# <a name="article-top"></a>Migrer votre Service Mobile de Azure existant à un Service d’application Azure

Avec la [disponibilité générale du Service d’application Azure], sites Azure Mobile Services peuvent être facilement migrés sur place pour tirer parti de toutes les fonctionnalités du Service application Azure.  Ce document explique ce qui se passe lors de la migration de votre site à partir d’Azure Mobile Services au Service d’application Azure.

## <a name="what-does-migration-do"></a>Que fait migration ? à votre site

Migration de votre Service Mobile d’Azure, votre Service Mobile transforme en une application de [Service d’application Azure] , sans affecter le code.  Votre Notification concentrateurs, SQL connexion de données, les paramètres d’authentification, les tâches planifiées et nom de domaine restent inchangés.  Les clients mobiles à l’aide de votre Service Mobile de Azure continuent à fonctionner normalement.  Migration redémarre votre service une fois qu’il est transféré vers le Service d’application Azure.

[AZURE.INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

## <a name="why-migrate"></a>Pourquoi vous devez migrer votre site

Microsoft recommande que vous migrez votre Service Mobile de Azure pour tirer parti des fonctionnalités de Service d’application Azure, y compris :

  *  Nouvelles fonctionnalités hôte, y compris les [WebJobs] et [les noms de domaine personnalisé].
  *  Connectivité à vos ressources sur site à l’aide de [VNet] en plus [Les connexions hybride].
  *  Surveillance et dépannage avec Relic nouvelle ou les [Perspectives de l’Application].
  *  Des outils DevOps intégré, y compris les [emplacements de la zone de transit], retour en arrière et dans la production de test.
  *  [Ajuster automatiquement]l’équilibrage de charge et [l’analyse des performances].

Pour plus d’informations sur les avantages du Service d’application Azure, consultez la rubrique [Services Mobile et Service de l’application] .

## <a name="before-you-begin"></a>Avant de commencer

Avant de commencer les travaux importants sur votre site, vous devez [sauvegarder votre Service Mobile] de scripts et de la base de données SQL.

## <a name="migrating-site"></a>Migration de vos sites.

Le processus de migration fait migrer tous les sites au sein d’une seule région d’Azure.

Pour faire migrer votre site :

  1.  Connectez-vous à l' [Azure Portal classique].
  2.  Sélectionnez un Service Mobile dans la région que vous souhaitez migrer.
  3.  Cliquez sur le bouton de **migrer vers le Service de l’application** .

    ![Le bouton de la migration][0]

  4.  Lire le migrer vers la boîte de dialogue Service de l’application.
  5.  Entrez le nom de votre Service Mobile dans la zone prévue à cet effet.  Par exemple, si votre nom de domaine est contoso.azure-mobile.net, puis entrez _contoso_ dans la zone prévue à cet effet.
  6.  Cliquez sur le bouton de coche.

Surveiller l’état de la migration du moniteur d’activité. Votre site est répertorié en tant que la *migration* dans le portail classique d’Azure.

  ![Moniteur d’activité de migration][1]

Chaque migration peut prendre de 3 à 15 minutes par service mobile en cours de migration.  Votre site reste disponible pendant la migration.
Votre site est redémarré à la fin du processus de migration.  Le site n’est pas disponible pendant le processus de redémarrage, ce qui peut durer quelques secondes.

## <a name="finalizing-migration"></a>Finalisation de la Migration

Vous souhaitez tester votre site à partir d’un client mobile à l’issue du processus de migration.  Vérifiez que vous pouvez effectuer toutes les actions de clients communs sans les modifications sur le client mobile.  

### <a name="update-app-service-tier"></a>Sélectionnez un Service d’application appropriées niveau de tarification

Vous disposez d’une plus grande souplesse dans la tarification après avoir migré vers le Service d’application Azure.

  1.  Connectez-vous au [portail Azure].
  2.  Sélectionnez **toutes les ressources** ou les **Services d’application** , puis cliquez sur le nom de votre Service Mobile ayant migré.
  3.  La lame de paramètres s’ouvre par défaut.
  4.  Dans le menu Paramètres, cliquez sur **Plan de Service d’application** .
  5.  Cliquez sur la mosaïque du **Niveau de prix** .
  6.  Cliquez sur la mosaïque adaptée à vos besoins, puis cliquez sur **Sélectionner**.  Vous devrez peut-être cliquez sur **Afficher tout** pour afficher les tarifs disponibles niveaux.

Comme point de départ, nous vous recommandons des niveaux suivants :

| Niveau de prix de Service mobile | Niveau de prix de Service de l’application |
| :-------------------------- | :----------------------- |
| Gratuit                        | F1 libre                  |
| Base                       | B1 Basic                 |
| Standard                    | S1 Standard              |

Il existe une flexibilité considérable dans choisir le bon prix de couche pour votre application.  Pour plus d’informations sur la tarification de votre nouveau Service d’application, consultez [Tarification des Service d’application] .

> [AZURE.TIP]La couche application Service Standard contient des accès à de nombreuses fonctionnalités que vous souhaitez utiliser, y compris les [emplacements de la zone de transit], les sauvegardes automatiques et la mise à l’échelle.  Découvrez les nouvelles fonctionnalités lorsque vous y !

### <a name="review-migration-scheduler-jobs"></a>Passez en revue les tâches migrées

Travaux du planificateur ne sera pas visibles jusqu'à environ 30 minutes après la migration.  Tâches planifiées continuent à s’exécuter en arrière-plan.
Pour afficher vos tâches planifiées une fois qu’ils sont visibles :

  1.  Connectez-vous au [portail Azure].
  2.  Sélectionnez **Parcourir >**, saisissez la **grille** dans la zone de _filtre_ , puis sélectionnez **Les Collections du planificateur**.

Il existe un nombre limité de planificateur libre travaux disponibles après la migration.  Passez en revue votre activité et les [Plans de planificateur Azure].

### <a name="configure-cors"></a>Configurer les CORS si nécessaire

Partage des ressources d’origine croisée est une technique pour autoriser un site Web pour accéder à une API Web sur un autre domaine.  Si vous utilisez les Services de Mobile Azure avec un site Web associé, puis vous devez configurer les CORS dans le cadre de la migration.  Si vous accédez à Services mobiles de Azure exclusivement à partir de périphériques mobiles, CORS n’a pas besoin d’être configurés sauf dans de rares cas.

Vos paramètres de CORS migrés sont disponibles en tant que le paramètre d’application **MS_CrossDomainWhitelist** .  Pour migrer votre site vers la fonction App Service CORS :

  1.  Connectez-vous au [portail Azure].
  2.  Sélectionnez **toutes les ressources** ou les **Services d’application** , puis cliquez sur le nom de votre Service Mobile ayant migré.
  3.  La lame de paramètres s’ouvre par défaut.
  4.  Cliquez sur **CORS** dans le menu de l’API.
  5.  Entrez les origines autorisés dans la zone de, en appuyant sur ENTRÉE après chacune d’elles.
  6.  Une fois votre liste des origines d’autorisé est correcte, cliquez sur le bouton Enregistrer.

> [AZURE.TIP]Un des avantages de l’utilisation d’un Service d’application Azure est que vous pouvez exécuter votre site web et le service mobile sur le même site.  Pour plus d’informations, consultez la section [étapes suivantes](#next-steps) .

### <a name="download-publish-profile"></a>Télécharger un nouveau profil de publication

Le profil de publication de votre site est modifié lors de la migration vers le Service d’application Azure.  Si vous avez l’intention de publier votre site à partir de Visual Studio, vous avez besoin d’un nouveau profil de publication.  Pour télécharger le nouveau profil de publication :

  1.  Connectez-vous au [portail Azure].
  2.  Sélectionnez **toutes les ressources** ou les **Services d’application** , puis cliquez sur le nom de votre Service Mobile ayant migré.
  3.  Cliquez sur **Get profil de la publication**.

Le fichier PublishSettings est téléchargé sur votre ordinateur.  Elle est normalement appelée _sitename_. PublishSettings.  Importer les paramètres de publication dans votre projet :

  1.  Ouvrez Visual Studio et votre projet Azure le Service Mobile.
  2.  Droit sur votre projet dans l' **Explorateur de solutions** et sélectionnez **publier...**
  3.  Cliquez sur **Importer**
  4.  Cliquez sur **Parcourir** et sélectionnez votre téléchargé publier le fichier de paramètres.  Cliquez sur **OK**
  5.  Cliquez sur **Valider la connexion** pour vous assurer du travail de paramètres de publication.
  6.  Cliquez sur **Publier** pour publier votre site.


## <a name="working-with-your-site"></a>Utilisation de votre migration postérieur au site

Commencer à utiliser votre nouveau Service d’application dans la [portail Azure] après la migration.  Voici quelques remarques sur les opérations spécifiques vous permettant d’effectuer sur le [Portail classique d’Azure], avec leur équivalent du Service de l’application.

### <a name="publishing-your-site"></a>Téléchargement et publication de votre site migré

Votre site est disponible via git ou ftp et peut être publiées à nouveau avec différents mécanismes différents, y compris WebDeploy TFS, mercurienne, GitHub et FTP.  Les informations d’identification de déploiement sont migrées avec le reste de votre site.  Si vous n’avez pas défini vos informations d’identification de déploiement ou que vous ne vous souvenez pas les, vous pouvez réinitialiser les :

  1. Connectez-vous au [portail Azure].
  2. Sélectionnez **toutes les ressources** ou les **Services d’application** , puis cliquez sur le nom de votre Service Mobile ayant migré.
  3. La lame de paramètres s’ouvre par défaut.
  4. Cliquez sur les **informations d’identification de déploiement** dans la publication menu.
  5. Entrez les nouvelles informations d’identification de déploiement dans les zones correspondantes, puis cliquez sur le bouton Enregistrer.

Vous pouvez utiliser ces informations d’identification pour dupliquer le site avec git ou configurer des déploiements automatisés à partir de GitHub, TFS ou mercurienne.  Pour plus d’informations, consultez la [documentation de déploiement de Service d’application Azure].

### <a name="appsettings"></a>Paramètres de l’application

La plupart des paramètres pour un service mobile migré sont disponibles via les paramètres de l’application.  Vous pouvez obtenir une liste des paramètres d’application à partir du [portail Azure].
Pour afficher ou modifier vos paramètres d’application :

  1. Connectez-vous au [portail Azure].
  2. Sélectionnez **toutes les ressources** ou les **Services d’application** , puis cliquez sur le nom de votre Service Mobile ayant migré.
  3. La lame de paramètres s’ouvre par défaut.
  4. Dans le menu général, cliquez sur **paramètres de l’Application** .
  5. Faites défiler jusqu'à la section Paramètres de l’application et rechercher les paramètres de l’application.
  6. Cliquez sur la valeur du paramètre d’application pour modifier la valeur.  Cliquez sur **Enregistrer** pour enregistrer la valeur.

Vous pouvez mettre à jour plusieurs paramètres d’application en même temps.

> [AZURE.TIP]Il existe deux paramètres de l’Application avec la même valeur.  Par exemple, vous pouvez voir _ApplicationKey_ et _MS\_ApplicationKey_.  Mettre à jour les paramètres d’application en même temps.

### <a name="authentication"></a>Authentification

Tous les paramètres d’authentification sont disponibles en tant que paramètres de l’application dans votre site migré.  Pour mettre à jour vos paramètres d’authentification, vous devez modifier les paramètres d’application appropriées.  Le tableau suivant montre les paramètres d’application approprié pour votre fournisseur d’authentification :

| Fournisseur          | ID de client                 | Secret des clients                | Autres paramètres             |
| :---------------- | :------------------------ | :--------------------------- | :------------------------- |
| Compte Microsoft | **MS\_MicrosoftClientID**  | **MS\_MicrosoftClientSecret** | **MS\_MicrosoftPackageSID** |
| Facebook          | **MS\_FacebookAppID**      | **MS\_FacebookAppSecret**     |                            |
| Twitter           | **MS\_TwitterConsumerKey** | **MS\_TwitterConsumerSecret** |                            |
| Google            | **MS\_GoogleClientID**     | **MS\_GoogleClientSecret**    |                            |
| Annonce Azure          | **MS\_AadClientID**        |                              | **MS\_AadTenants**          |

Remarque : **MS\_AadTenants** est stocké sous forme de liste séparée par des virgules des domaines de clients (les champs « Locataires autorisé » dans le portail de Services mobiles).

> [AZURE.WARNING] **N’utilisez pas les mécanismes d’authentification dans le menu Paramètres**
>
> Service d’application Azure fournit un système de l’authentification et l’autorisation de « sans code » distinct sous la _l’authentification / autorisation_ menu Paramètres et l’option _D’authentification de Mobile_ (déconseillée) sous le menu Paramètres.  Ces options sont incompatibles avec un Service de Mobile Azure migrés.  Vous pouvez [mettre à niveau votre site](app-service-mobile-net-upgrading-from-mobile-services.md) pour tirer parti de l’authentification du Service d’application Azure.

### <a name="easytables"></a>Données

L’onglet _données_ , dans les Services de Mobile a été remplacée par les _Tables facile_ au sein du portail Azure.  Pour accéder à des Tables simples :

  1. Connectez-vous au [portail Azure].
  2. Sélectionnez **toutes les ressources** ou les **Services d’application** , puis cliquez sur le nom de votre Service Mobile ayant migré.
  3. La lame de paramètres s’ouvre par défaut.
  4. Dans le menu MOBILE, cliquez sur **tables faciles** .

Vous pouvez ajouter une table en cliquant sur le bouton **Ajouter** ou accéder à des tables existantes en cliquant sur un nom de table.  Il existe diverses opérations à partir de cette lame, y compris :

* Modification des autorisations de table
* Modification des scripts d’exploitation
* Gestion du schéma de table
* Suppression de la table
* Effacement du contenu de la table
* Suppression des lignes de la table

### <a name="easyapis"></a>API

L’onglet de _l’API_ de Services mobiles a été remplacée par les _API facile_ au sein du portail Azure.  Pour accéder aux API facile :

  1. Connectez-vous au [portail Azure].
  2. Sélectionnez **toutes les ressources** ou les **Services d’application** , puis cliquez sur le nom de votre Service Mobile ayant migré.
  3. La lame de paramètres s’ouvre par défaut.
  4. Cliquez sur **Une API simple** dans le menu MOBILE.

Votre API migrés est déjà répertoriés dans la lame.  Vous pouvez également ajouter une API à partir de cette carte.  Pour gérer une API spécifique, cliquez sur l’API.
À partir de la nouvelle blade, vous pouvez ajuster les autorisations et modifier les scripts de l’API.

### <a name="on-demand-jobs"></a>Travaux du planificateur

Toutes les tâches sont disponibles par l’intermédiaire de la section Collections de tâche du planificateur.  Pour accéder à vos tâches du planificateur :

  1. Connectez-vous au [portail Azure].
  2. Sélectionnez **Parcourir >**, saisissez la **grille** dans la zone de _filtre_ , puis sélectionnez **Les Collections du planificateur**.
  3. Sélectionnez l’ensemble de travail pour votre site.  Il est nommé _sitename_-travaux.
  4. Cliquez sur **paramètres**.
  5. Sous gestion, cliquez sur **tâches du planificateur** .

Les tâches planifiées sont répertoriées avec la fréquence que vous avez spécifié avant la migration.  Les travaux à la demande sont désactivées.  Pour exécuter une tâche à la demande :

  1. Sélectionnez la tâche que vous souhaitez exécuter.
  2. Si nécessaire, cliquez sur **Activer** pour activer la tâche.
  3. Cliquez sur **paramètres**, puis sur **planification**.
  4. Sélectionnez une périodicité de **qu’une seule fois**, puis cliquez sur **Enregistrer**

Les travaux à la demande se trouvent dans `App_Data/config/scripts/scheduler post-migration`.  Nous vous conseillons de convertir toutes les tâches à la demande de [WebJobs] ou de [fonctions].  Écrire de nouveaux travaux du planificateur en tant que [WebJobs] ou des [fonctions].

### <a name="notification-hubs"></a>Concentrateurs de notification

Services mobiles utilise des concentrateurs de Notification pour les notifications de transmission.  Les paramètres d’application sont utilisées pour lier le concentrateur de Notification de votre Service Mobile après la migration :

| Paramètre d’application                    | Description                              |
| :------------------------------------- | :--------------------------------------- |
| **MS\_PushEntityNamespace**             | Le Namespace de concentrateur de Notification           |
| **MS\_NotificationHubName**             | Le nom de concentrateur de Notification                |
| **MS\_NotificationHubConnectionString** | La chaîne de connexion de concentrateur de Notification   |
| **MS\_NomEspaceNoms**                   | Un alias de MS_PushEntityNamespace      |

Votre concentrateur de Notification est géré via le [portail Azure].  Notez le nom de concentrateur de Notification (vous le trouverez en utilisant les paramètres d’application) :

  1. Connectez-vous au [portail Azure].
  2. Sélectionnez **Parcourir**>, puis sélectionnez **Notification de concentrateurs**
  3. Cliquez sur le nom de concentrateur de Notification associé au service mobile.

> [AZURE.NOTE]Si votre concentrateur de Notification est un type de « Mixte », il n’est pas visible.  « Mixte » tapez concentrateurs utilisent à la fois des concentrateurs de Notification et des fonctionnalités héritées de Bus des services de notification.  [Convertir vos espaces de noms mixte] avant de continuer.  Une fois la conversion terminée, votre concentrateur de notification s’affiche dans le [portail Azure].

Pour plus d’informations, consultez la documentation de [Concentrateurs de Notification] .

> [AZURE.TIP]Fonctionnalités de gestion de concentrateurs de notification dans le [portail Azure] sont toujours dans l’aperçu.  Le [Portail classique d’Azure] reste disponible pour la gestion de tous vos concentrateurs de Notification.

### <a name="legacy-push"></a>Paramètres hérités de Push

Si vous avez configuré la transmission de votre service mobile avant l’introduction de concentrateurs de Notification, vous utilisez _push héritée_.  Si vous utilisez Push et que vous ne voyez pas un concentrateur de Notification répertoriés dans votre configuration, il est probablement à l’aide de _push héritée_.  Cette fonctionnalité est migrée avec toutes les autres fonctionnalités.  Toutefois, nous vous conseillons de changer à des concentrateurs de Notification dès que la migration est terminée.

Dans l’intervalle, tous les paramètres hérités push (à l’exception notable du certificat APNS) sont disponibles dans les paramètres de l’application.  Mettre à jour le certificat APNS en remplaçant le fichier approprié dans le système de fichiers.

### <a name="app-settings"></a>Autres paramètres de l’application

Les paramètres d’application supplémentaires suivants sont migrés à partir de votre Service Mobile et disponible sous *paramètres* > *Paramètres de l’application*:

| Paramètre d’application              | Description                             |
| :------------------------------- | :-------------------------------------- |
| **MS\_MobileServiceName**         | Le nom de votre application.                    |
| **MS\_MobileServiceDomainSuffix** | Le préfixe du domaine. ex : Azure-mobile.net |
| **MS\_ApplicationKey**            | Votre clé d’application                    |
| **MS\_clé principale.**                 | Clé principale de votre application                     |

La clé de l’application et la clé principale sont identiques pour les clés de l’Application à partir de votre Service Mobile d’origine.  En particulier, la clé d’Application envoyée par les clients mobiles pour valider leur utilisation de l’API mobile.

### <a name="cliequivalents"></a>Équivalents de ligne de commande

Vous pouvez plus utiliser la commande _azure mobile_ pour gérer votre site Azure Mobile Services.  Au lieu de cela, les nombreuses fonctions ont été remplacées par la commande _site azure_ .  Utilisez le tableau suivant pour connaître les équivalents des commandes les plus courantes :

| _Azure Mobile_ Commande                     | Commande _d’Azure Site_ équivalente            |
| :----------------------------------------- | :----------------------------------------- |
| emplacements mobiles                           | liste des emplacements de site                         |
| liste mobile                                | liste des sites                                  |
| Mobile afficher _nom_                         | site afficher _nom_                           |
| redémarrage mobile _nom_                      | redémarrage du site _nom_                        |
| _nom_ du redéploiement mobile                     | _nom_ de le _commitId_ de site déploiement redéploiement |
| _nom_ de _type de_ _valeur_ la valeur clé mobile       | _nom_ du site appsetting supprimer _clé_ <br/> site appsetting ajouter _clé_= _nom_ de la_valeur_ |
| _nom_ de la liste configuration mobile                  | _nom_ de la liste appsetting site                |
| configuration mobile obtenir une _clé_ de _nom_             | _nom_ du site appsetting afficher _clé_          |
| _nom de_ _clé_ du jeu de configuration mobile             | _nom_ du site appsetting supprimer _clé_ <br/> site appsetting ajouter _clé_= _nom_ de la_valeur_ |
| _nom_ de la liste domaine mobile                  | _nom_ de la liste domaine site                    |
| Ajouter un domaine mobile de _nom de_ _domaine_          | Ajouter un domaine de site du _nom de_ _domaine_            |
| supprimer _nom_ du domaine mobile                | suppression du domaine de site, de _domaine_ _nom_         |
| une échelle mobile afficher _nom_                   | site afficher _nom_                           |
| changer le _nom_ d’une échelle mobile                 | _nom_ du site échelle mode _mode_ <br /> site échelle instances _instances_ _nom_ |
| _nom_ de la liste appsetting mobile              | _nom_ de la liste appsetting site                |
| Mobile appsetting ajouter _nom_ _clé_ _valeur_ | site appsetting ajouter _clé_= _nom_ de la_valeur_   |
| _nom_ _clé_ de Mobile appsetting SUPPR      | _nom_ du site appsetting supprimer _clé_        |
| Mobile appsetting afficher _nom_ _clé_        | _nom_ du site appsetting supprimer _clé_        |

Mise à jour d’authentification ou les paramètres de notification envoyés par le paramètre d’Application appropriées de mise à jour.
Modifier des fichiers et publier votre site via ftp ou git.

### <a name="diagnostics"></a>Tests de diagnostic et de journalisation

L’enregistrement des diagnostics est normalement désactivée dans un Service d’application Azure.  Pour activer l’enregistrement des diagnostics :

  1. Connectez-vous au [portail Azure].
  2. Sélectionnez **toutes les ressources** ou les **Services d’application** , puis cliquez sur le nom de votre Service Mobile ayant migré.
  3. La lame de paramètres s’ouvre par défaut.
  4. Dans le menu Fonctions, sélectionnez **Les journaux de Diagnostic** .
  5. Cliquez **sur** pour les fichiers journaux suivants : **Ouverture de l’Application (système de fichiers)**, **messages d’erreur détaillés**et **le traçage des demandes a échoué**
  6. Cliquez sur **Système de fichiers** pour l’enregistrement de serveur Web
  7. Cliquez sur **Enregistrer**

Pour afficher les journaux :

  1. Connectez-vous au [portail Azure].
  2. Sélectionnez **toutes les ressources** ou les **Services d’application** , puis cliquez sur le nom de votre Service Mobile ayant migré.
  3. Cliquez sur le bouton **Outils**
  4. Sélectionnez le **flux de journaux** sous le menu observer.

Les journaux sont affichés dans la fenêtre comme ils sont générés.  Vous pouvez également télécharger les journaux pour les analyser ultérieurement à l’aide de vos informations d’identification de déploiement. Pour plus d’informations, consultez la documentation de [l’enregistrement] .

## <a name="known-issues"></a>Problèmes connus

### <a name="deleting-a-migrated-mobile-app-clone-causes-a-site-outage"></a>Suppression d’un Clone d’application Mobile migrés provoque un arrêt du site

Si vous clonez votre service mobile ayant migré à l’aide de PowerShell d’Azure, puis supprimez le clone, l’entrée DNS de votre service de production est supprimée.  Votre site est plus accessibles à partir d’Internet.  

Résolution : Si vous souhaitez dupliquer votre site, le faire via le portail.

### <a name="changing-webconfig-does-not-work"></a>Modification de Web.config ne fonctionne pas

Si vous avez un site ASP.NET, passe à la `Web.config` fichier ne sont pas appliquées.  Le Service d’application Azure génère un approprié `Web.config` fichier lors du démarrage de la prise en charge de l’exécution de Services mobiles.  Vous pouvez substituer certains paramètres (tels que les en-têtes personnalisés) à l’aide d’un fichier de transformation XML.  Créer un fichier appelé dans `applicationHost.xdt` -ce fichier doit se retrouvent dans le `D:\home\site` répertoire du service Azure.  Télécharger le `applicationHost.xdt` de fichiers via un script de déploiement personnalisé ou directement à l’aide de Kudu.  Vous trouverez ci-dessous un exemple de document :

```
<?xml version="1.0" encoding="utf-8"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <system.webServer>
    <httpProtocol>
      <customHeaders>
        <add name="X-Frame-Options" value="DENY" xdt:Transform="Replace" />
        <remove name="X-Powered-By" xdt:Transform="Insert" />
      </customHeaders>
    </httpProtocol>
    <security>
      <requestFiltering removeServerHeader="true" xdt:Transform="SetAttributes(removeServerHeader)" />
    </security>
  </system.webServer>
</configuration>
```

Pour plus d’informations, consultez la documentation des [Exemples de transformation XDT] sur GitHub.

### <a name="migrated-mobile-services-cannot-be-added-to-traffic-manager"></a>Les Services mobiles migrés Impossible d’ajouter le trafic vers le Gestionnaire de périphériques

Lorsque vous créez un profil de Traffic Manager, vous ne peut pas choisir un service mobile ayant migré vers le profil.  Utilisez un « point de terminaison externe. »  Le point de terminaison externe ne peut pas être ajouté par le biais de PowerShell.  Pour plus d’informations, consultez le [didacticiel de Traffic Manager](https://azure.microsoft.com/blog/azure-traffic-manager-external-endpoints-and-weighted-round-robin-via-powershell/).

## <a name="next-steps"></a>Étapes suivantes

Maintenant que votre application est migrée vers le Service d’application, il existe encore plus de fonctionnalités que vous pouvez utiliser :

  * Déploiement [d’emplacements de la zone de transit] permettent d’effectuer des modifications sur votre site et effectuer un tests de B.
  * [WebJobs] fournit un remplaçant pour les travaux à la demande planifiée.
  * Vous pouvez [déployer en permanence] votre site en liant votre site à GitHub, TFS ou mercurienne.
  * [Idées d’Application] vous permet de surveiller votre site.
  * Servir un site Web et une API Mobile à partir du même code.

### <a name="upgrading-your-site"></a>Mise à niveau de votre site de Services mobiles vers Azure Mobile applications SDK

  * Pour les projets serveur Node.js, le nouveau [SDK de Node.js Apps Mobile] fournit plusieurs nouvelles fonctionnalités. Par exemple, vous pouvez maintenant faire de développement local et de débogage, utiliser n’importe quelle version de Node.js au-dessus de 0,10 et personnaliser avec n’importe quel middleware de Express.js.

  * Pour. Projets de serveur basé sur le NET, les nouveaux [packages NuGet de kit de développement d’applications Mobile](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) ont une plus grande souplesse sur les dépendances de NuGet.  Ces solutions prennent en charge de la nouvelle authentification de Service de l’application et composent avec n’importe quel projet ASP.NET. Pour plus d’informations sur la mise à niveau, consultez [mise à niveau de votre Service Mobile de .NET existant au Service de l’application](app-service-mobile-net-upgrading-from-mobile-services.md).

<!-- Images -->
[0]: ./media/app-service-mobile-migrating-from-mobile-services/migrate-to-app-service-button.PNG
[1]: ./media/app-service-mobile-migrating-from-mobile-services/migration-activity-monitor.png
[2]: ./media/app-service-mobile-migrating-from-mobile-services/triggering-job-with-postman.png

<!-- Links -->
[Service d’application de tarification]: https://azure.microsoft.com/en-us/pricing/details/app-service/
[Aperçu de l’application]: ../application-insights/app-insights-overview.md
[Échelle automatique]: ../app-service-web/web-sites-scale.md
[Service d’application Azure]: ../app-service/app-service-value-prop-what-is.md
[Documentation de déploiement de Service d’application Azure]: ../app-service-web/web-sites-deploy.md
[Azure Portal classique]: https://manage.windowsazure.com
[Azure portal]: https://portal.azure.com
[Azure Region]: https://azure.microsoft.com/en-us/regions/
[Plans de planificateur Azure]: ../scheduler/scheduler-plans-billing.md
[déployer en continu]: ../app-service-web/app-service-continuous-deployment.md
[Convertir vos espaces de noms mixtes]: https://azure.microsoft.com/en-us/blog/updates-from-notification-hubs-independent-nuget-installation-model-pmt-and-more/
[curl]: http://curl.haxx.se/
[noms de domaine personnalisé]: ../app-service-web/web-sites-custom-domain-name.md
[Fiddler]: http://www.telerik.com/fiddler
[disponibilité générale du Service d’application Azure]: https://azure.microsoft.com/blog/announcing-general-availability-of-app-service-mobile-apps/
[Connexions hybride]: ../app-service-web/web-sites-hybrid-connection-get-started.md
[Enregistrement dans le journal]: ../app-service-web/web-sites-enable-diagnostic-log.md
[Les applications mobiles Node.js SDK]: https://github.com/azure/azure-mobile-apps-node
[Services et application de Service mobile]: app-service-mobile-value-prop-migration-from-mobile-services.md
[Concentrateurs de notification]: ../notification-hubs/notification-hubs-push-notification-overview.md
[analyse des performances]: ../app-service-web/web-sites-monitor.md
[Postman]: http://www.getpostman.com/
[Sauvegarder votre Service Mobile]: ../mobile-services/mobile-services-disaster-recovery.md
[emplacements de la zone de transit]: ../app-service-web/web-sites-staged-publishing.md
[VNet]: ../app-service-web/web-sites-integrate-with-vnet.md
[WebJobs]: ../app-service-web/websites-webjobs-resources.md
[Exemples de transformation XDT]: https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples
[Fonctions]: ../azure-functions/functions-overview.md
