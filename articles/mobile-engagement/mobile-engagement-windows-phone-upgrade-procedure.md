<properties 
    pageTitle="Windows Phone SDK Silverlight les procédures de mise à niveau" 
    description="Windows Phone SDK Silverlight de procédures de mise à niveau pour Azure Engagement Mobile"                  
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="dwrede"
    editor="" />

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-windows-phone" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

#<a name="windows-phone-silverlight-sdk-upgrade-procedures"></a>Windows Phone SDK Silverlight les procédures de mise à niveau

Si vous avez déjà intégré une ancienne version de notre kit de développement logiciel dans votre application, vous devez considérer les points suivants lors de la mise à niveau de la SDK.

Il se peut que vous deviez suivre plusieurs procédures si vous avez manqué plusieurs versions du Kit de développement. Par exemple, si vous migrez à partir de 0.10.1 vers 0.11.0, que vous devez tout d’abord suivre la procédure « à partir de 0.9.0 à 0.10.1 » puis la procédure « à partir de 0.10.1 à 0.11.0 ».

##<a name="from-200-to-330"></a>À partir de 2.0.0 à 3.3.0

### <a name="test-logs"></a>Journaux des tests

Journaux produits par le Kit de développement peuvent désormais être activé/désactivé/filtrées. Pour le personnaliser, mettre à jour de la propriété `EngagementAgent.Instance.TestLogEnabled` à l’une de disponible à partir de la valeur de la `EngagementTestLogLevel` énumération, par exemple :

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

##<a name="from-111-to-200"></a>À partir de 1.1.1 à 2.0.0

Voici comment migrer une intégration SDK à partir du service de Capptain offert par Capptain SAS dans une application grâce à Azure Mobile Engagement. 

> [Azure.IMPORTANT] Capptain et l’Engagement de Mobile ne sont pas les mêmes services et la procédure décrite ci-dessous ne souligne que la migration de l’application cliente. Le Kit de développement de l’application de la migration ne migre pas vos données à partir des serveurs Capptain vers les serveurs Mobile Engagement

Si vous migrez à partir d’une version antérieure, veuillez consulter le site web Capptain pour effectuer une migration vers 1.1.1 tout d’abord, puis appliquer la procédure suivante

### <a name="nuget-package"></a>Package NuGet

Remplacez **Capptain.WindowsPhone** par package Nuget de **MicrosoftAzure.MobileEngagement** .

### <a name="applying-mobile-engagement"></a>Application d’Engagement Mobile

Le Kit de développement logiciel utilise le terme `Engagement`. Vous devez mettre à jour votre projet afin de correspondre à cette modification.

Vous devez désinstaller votre package nuget de Capptain en cours. Considérez que toutes vos modifications dans le dossier Capptain ressources seront supprimées. Si vous souhaitez conserver ces fichiers puis de faire une copie.

Ensuite, installez le package nuget Microsoft Azure Engagement sur votre projet. Vous pouvez trouver directement sur [Nuget](http://www.nuget.org/packages/MicrosoftAzure.MobileEngagement). Cette action remplace tous les fichiers de ressources utilisés par l’Engagement et ajoute la nouvelle DLL d’Engagement pour les références de votre projet.

Vous devez nettoyer vos références de projet en supprimant les références Capptain DLL. Si vous ne faites pas cela, la version de Capptain est en conflit et produiront des erreurs.

Si vous avez personnalisé des ressources de Capptain, copiez le contenu de fichiers anciens et collez-les dans les nouveaux fichiers de projet. Notez que les fichiers xaml et cs doivent être mis à jour.

Une fois ces étapes terminées, il vous suffit de remplacer les anciennes références de Capptain par les nouvelles références de l’Engagement.

1. Tous les espaces de noms Capptain doivent être mis à jour.

    Avant la migration :
    
        using Capptain.Agent;
        using Capptain.Reach;
    
    Après la migration :
    
        using Microsoft.Azure.Engagement;

2. Toutes les classes de Capptain qui contiennent « Capptain » doivent contenir « Engagement ».

    Avant la migration :
    
        public sealed partial class MainPage : CapptainPage
        {
          protected override string GetCapptainPageName()
          {
            return "Capptain Demo";
          }
          ...
        }
    
    Après la migration :
    
        public sealed partial class MainPage : EngagementPage
        {
          protected override string GetEngagementPageName()
          {
            return "Engagement Demo";
          }
          ...
        }

3. Pour les fichiers xaml attributs et espace de noms Capptain sont également modifiés.

    Avant la migration :
    
        <capptain:CapptainPage
        ...
        xmlns:capptain="clr-namespace:Capptain.Agent;assembly=Capptain.Agent.WP"
        ...
        </capptain:CapptainPage>
    
    Après la migration :
    
        <engagement:EngagementPage
        ...
        xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"
        ...
        </engagement:EngagementPage>

4. Pour les autres ressources telles que des images de Capptain, notez qu’elles aussi ont été renommés pour utiliser « Engagement ».

### <a name="application-id--sdk-key"></a>ID de l’application / clé du Kit de développement logiciel

Engagement utilise une chaîne de connexion. Vous n’êtes pas obligé de spécifier un ID d’application et d’une clé du Kit de développement logiciel avec l’Engagement de Mobile, vous ne devrez spécifier une chaîne de connexion. Vous pouvez configurer sur votre fichier EngagementConfiguration.

La configuration de l’Engagement peut être définie votre `Resources\EngagementConfiguration.xml` fichier de votre projet.

Modifier ce fichier pour spécifier :

-   Votre chaîne de connexion d’application entre les balises `<connectionString>` et `<\connectionString>`.

Si vous voulez le spécifier lors de l’exécution au lieu de cela, vous pouvez appeler la méthode suivante avant l’initialisation de l’agent d’Engagement :

        /* Engagement configuration. */
        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
        
        /* Initialize Engagement angent with above configuration. */
        EngagementAgent.Instance.Init(engagementConfiguration);

La chaîne de connexion de votre application s’affiche dans le portail classique d’Azure.

### <a name="items-name-change"></a>Changement de nom des éléments

Tous les éléments nommés *capptain* ont été nommées *engagement*. De la même façon pour les *Capptain* de *l’Engagement*.

Exemples d’éléments de Capptain couramment utilisés :

-   CapptainConfiguration nommé maintenant EngagementConfiguration
-   CapptainAgent nommé maintenant EngagementAgent
-   CapptainReach nommé maintenant EngagementReach
-   CapptainHttpConfig nommé maintenant EngagementHttpConfig
-   GetCapptainPageName nommé maintenant GetEngagementPageName

Notez que ce changement de nom affecte également les méthodes substituées.



 
