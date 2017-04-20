<properties 
    pageTitle="Windows Phone intégration du Kit de développement logiciel Silverlight portée" 
    description="Comment intégrer la portée de l’Engagement de Mobile Azure avec les applications Silverlight de Windows Phone"                    
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

#<a name="windows-phone-silverlight-reach-sdk-integration"></a>Windows Phone intégration du Kit de développement logiciel Silverlight portée

Vous devez suivre la procédure d’intégration décrite dans l' [Intégration de kit de développement logiciel Windows Phone Silverlight Engagement](mobile-engagement-windows-phone-integrate-engagement.md) avant de suivre ce guide.

##<a name="embed-the-engagement-reach-sdk-into-your-windows-phone-silverlight-project"></a>Incorporer le SDK atteindre d’Engagement dans votre projet Silverlight de Windows Phone

Vous n’avez pas à ajouter. `EngagementReach`références et ressources sont déjà dans votre projet.

> [AZURE.TIP]  Vous pouvez personnaliser les images situé dans le `Resources` dossier de votre projet, en particulier l’icône de la marque (qui par défaut pour l’icône de l’Engagement).

##<a name="add-the-capabilities"></a>Ajouter les fonctionnalités

Le Kit de développement atteindre Engagement a besoin des fonctions supplémentaires.

Ouvrir votre `WMAppManifest.xml` de fichiers et vérifiez que les fonctionnalités suivantes sont déclarées :

-   `ID_CAP_PUSH_NOTIFICATION`
-   `ID_CAP_WEBBROWSERCOMPONENT`

La première est utilisée par le service MPNS pour permettre l’affichage de notification de toast. L’autre est utilisé pour incorporer une tâche de navigateur dans le Kit de développement logiciel.

Modifier la `WMAppManifest.xml` de fichiers et d’ajouter à l’intérieur de la `<Capabilities />` balise :

    <Capability Name="ID_CAP_PUSH_NOTIFICATION" />
    <Capability Name="ID_CAP_WEBBROWSERCOMPONENT" />

##<a name="enable-the-microsoft-push-notification-service"></a>Activer le Service de Notification Microsoft Push

Pour pouvoir utiliser le **Service de Notification Microsoft Push** (appelée MPNS) votre `WMAppManifest.xml` fichier doit avoir un `<App />` de la balise avec un `Publisher` attribut défini sur le nom de votre projet.

##<a name="initialize-the-engagement-reach-sdk"></a>Initialiser la portée de l’Engagement SDK

### <a name="engagement-configuration"></a>Configuration de l’engagement

La configuration de l’Engagement est centralisée dans la `Resources\EngagementConfiguration.xml` fichier de votre projet.

Modifier ce fichier pour spécifier la configuration de la portée :

-   *Facultatif*, indiquer si la diffusion native (MPNS) est activée ou non entre `<enableNativePush>` et `</enableNativePush>` balises (`true` par défaut).
-   *Facultatif*, indiquer le nom du canal de transmission entre `<channelName>` et `</channelName>` balises, fournissent le même que votre application peut actuellement utiliser ou laissez ce champ vide.

Si vous voulez le spécifier lors de l’exécution au lieu de cela, vous pouvez appeler la méthode suivante avant l’initialisation de l’agent d’Engagement :

    /* Engagement configuration. */
    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    
    engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
    
    engagementConfiguration.Reach.EnableNativePush = true;                  
    /* [Optional] whether the native push (MPNS) is activated or not. */
    
    engagementConfiguration.Reach.ChannelName = "YOUR_PUSH_CHANNEL_NAME";   
    /* [Optional] Provide the same channel name that your application may currently use. */
    
    /* Initialize Engagement agent with above configuration. */
    EngagementAgent.Instance.Init(engagementConfiguration);

> [AZURE.TIP] Vous pouvez spécifier le nom du canal de transmission MPNS de votre application. Par défaut, Engagement crée un nom basé sur l’appId. Vous n’avez pas besoin de spécifier le nom vous-même, sauf si vous envisagez d’utiliser le canal de transmission en dehors de l’Engagement.

### <a name="engagement-initialization"></a>Initialisation de l’engagement

Modifier la `App.xaml.cs`:

-   Ajouter à votre `using` instructions :

        using Microsoft.Azure.Engagement;

-   Insérer `EngagementReach.Instance.Init` juste après `EngagementAgent.Instance.Init` dans `Application_Launching` :

        private void Application_Launching(object sender, LaunchingEventArgs e)
        {
           EngagementAgent.Instance.Init();
           EngagementReach.Instance.Init();
        }

-   Insérer `EngagementReach.Instance.OnActivated` dans les `Application_Activated` méthode :

        private void Application_Activated(object sender, ActivatedEventArgs e)
        {
           EngagementAgent.Instance.OnActivated(e);
           EngagementReach.Instance.OnActivated(e);
        }

> [AZURE.IMPORTANT] Le `EngagementReach.Instance.Init` s’exécute dans un thread dédié. Vous n’êtes pas obligé de le faire vous-même.

##<a name="app-store-submission-considerations"></a>Considérations relatives à la soumission App store

Microsoft impose certaines règles lors de l’utilisation de notifications push :

À partir de la documentation de Microsoft, [Les stratégies d’Application] , point 2.9 :

1) Vous devez demander à l’utilisateur d’accepter pour recevoir des notifications de type Pousser. Ensuite, dans vos paramètres, ajoutez un moyen de désactiver les notifications de transmission.

L’objet EngagementReach fournit deux méthodes pour gérer l’opt-in/opt-out, `EnableNativePush()` et `DisableNativePush()`. Vous pouvez, par exemple, créer une option dans les paramètres avec un bouton bascule pour désactiver ou activer la MPNS.

Vous pouvez également décider de désactiver le MPNS par le biais de la configuration de l’Engagement\<-téléphone-sdk-portée-configuration de windows\>.

> 2.9.1) l’application doit tout d’abord décrivent les notifications doivent être fournis et **obtenir l’autorisation expresse de l’utilisateur (opt-in)**et **doit offrir un mécanisme par lequel l’utilisateur peut bloquer la réception de notifications de type Pousser**. Toutes les notifications fournies avec le Service de Notification Microsoft Push doivent être conformes à la description fournie à l’utilisateur et doit être conforme aux applique toutes les [Stratégies d’Application]  [ Content Policies] et des [Exigences supplémentaires pour les Types d’applications spécifiques].

2) Vous ne devez pas utiliser trop de notifications de type Pousser. Engagement gérera les notifications pour vous.

> 2.9.2) l’application et son utilisation du Service de Notification Microsoft Push ne doivent pas excessivement utiliser la capacité du réseau ou la bande passante du Service de Notification Microsoft Push, ou sinon indûment périphérique libère un Windows Phone ou autre dispositif de Microsoft ou le service avec les notifications de transmission excessif, tel que déterminé par Microsoft dans son appréciation raisonnable et doit pas endommager ou interférer avec des réseaux Microsoft ou serveurs tout tiers serveurs ou réseaux connectés au Service de Notification Microsoft Push.

3) Ne comptez pas sur MPNS pour envoyer les informations critiques et. Engagement utilise MPNS, cette règle s’applique également pour les campagnes créées à l’intérieur de l’Engagement du front-end.

> 2.9.3) le Service de Notification Push Microsoft peut ne pas être utilisé pour envoyer des notifications, qui sont les mission critique ou autre pourraient affecter la question de vie ou de mort, y compris sans notification critique de limitation liée à un dispositif médical ou d’une condition. MICROSOFT EXCLUT EXPRESSÉMENT TOUTE GARANTIE QUE L’UTILISATION DE LA DIFFUSION DE MICROSOFT SERVICE DE NOTIFICATION OU DE LA LIVRAISON DES NOTIFICATIONS DE SERVICE DE NOTIFICATION MICROSOFT PUSH SE FERA SANS INTERRUPTION, EXEMPT D’ERREURS, SINON ASSURÉ DE SE PRODUIRE EN TEMPS RÉEL.

**Nous ne pouvons pas garantir que votre application va passer le processus de validation si vous ne respectez pas ces recommandations.**

##<a name="handle-data-push-optional"></a>Gérer la transmission de données (facultative)

Si vous souhaitez que votre application soit en mesure de recevoir les push de données de portée, vous devez implémenter deux événements de la classe EngagementReach :

    EngagementReach.Instance.DataPushStringReceived += (body) =>
    {
       Debug.WriteLine("String data push message received: " + body);
       return true;
    };
    
    EngagementReach.Instance.DataPushBase64Received += (decodedBody, encodedBody) =>
    {
       Debug.WriteLine("Base64 data push message received: " + encodedBody);
       // Do something useful with decodedBody like updating an image view
       return true;
    };

Vous pouvez voir que le rappel de chaque méthode retourne une valeur booléenne. Engagement envoie une rétroaction pour sa principale après la transmission de la transmission de données. Si le rappel renvoie la valeur false, le `exit` commentaires sera envoyé. Dans le cas contraire, il sera `action`. Si aucun rappel n’est définie pour les événements, les `drop` commentaires sera renvoyé à l’Engagement.

> [AZURE.WARNING] Engagement n’est pas en mesure de recevoir des évaluations multiples pour une transmission de données. Si vous envisagez de définir plusieurs gestionnaires d’un événement, sachez que l’évaluation correspond à la dernière un envoyé. Dans ce cas, nous vous recommandons de toujours renvoie la même valeur pour éviter la confusion des commentaires sur le serveur frontal.

##<a name="customize-ui-optional"></a>Personnaliser l’interface utilisateur (facultatif)

### <a name="first-step"></a>Première étape

Nous vous permettent de personnaliser la portée de l’interface utilisateur.

Pour ce faire, vous devez créer une sous-classe de la `EngagementReachHandler` classe.

**Exemple de Code :**

    using Microsoft.Azure.Engagement;
    
    namespace Example
    {
       internal class ExampleReachHandler : EngagementReachHandler
       {
          // Override EngagementReachHandler methods depending on your needs
       }
    }

Ensuite, définissez le contenu de la `EngagementReach.Instance.Handler` champ avec votre objet personnalisé dans votre `App.xaml.cs` classe au sein de la `Application_Launching` méthode.

**Exemple de Code :**

    private void Application_Launching(object sender, LaunchingEventArgs e)
    {
       // your app initialization 
       EngagementReach.Instance.Handler = new ExampleReachHandler();
       // Engagement Agent and Reach initialization
    }

> [AZURE.NOTE] Par défaut, Engagement utilise sa propre implémentation de `EngagementReachHandler`. Vous n’êtes pas obligé de créer votre propre, et si vous procédez ainsi, vous n’êtes pas obligé de substituer toutes les méthodes. Le comportement par défaut est de sélectionner l’objet de base d’Engagement.

### <a name="layouts"></a>Mises en page

Par défaut, portée utilise les ressources incorporées de la DLL pour afficher les pages et les notifications.

Toutefois, vous pouvez décider d’utiliser vos propres ressources pour refléter votre image de marque dans ces composants.

Vous pouvez substituer `EngagementReachHandler` méthodes dans votre sous-classe pour indiquer l’Engagement de vos mises en forme :

**Exemple de Code :**

    // In your subclass of EngagementReachHandler
    
    public override string GetTextViewAnnouncementUri()
    {
       // return the path of your own xaml
    }
    
    public override string GetWebViewAnnouncementUri()
    {
       // return the path of your own xaml
    }
    
    public override string GetPollUri()
    {
       // return the path of your own xaml
    }
    
    public override EngagementNotificationView CreateNotification(EngagementNotificationViewModel viewModel)
    {
       // return a new instance of your own notification
    }

> [AZURE.TIP] Le `CreateNotification` méthode peut retourner null. La notification ne s’affichent pas et la campagne portée va être supprimée.

Pour simplifier votre mise en œuvre de la mise en page, nous fournissons également notre propre xaml qui peut servir de base pour votre code. Ils sont situés dans l’archive d’Engagement SDK (/ src/portée).

> [AZURE.WARNING] Les sources que nous proposons sont ceux mêmes exactes que nous utilisons. Par conséquent, si vous souhaitez les modifier directement, n’oubliez pas de modifier l’espace de noms et le nom.

### <a name="notification-position"></a>Position de notification

Par défaut, une notification dans l’application s’affiche dans la partie inférieure gauche de l’application. Vous pouvez modifier ce comportement en substituant la `GetNotificationPosition` méthode de le `EngagementReachHandler` objet.

    // In your subclass of EngagementReachHandler
    
    public override EngagementReachHandler.NotificationPosition GetNotificationPosition(EngagementNotificationViewModel viewModel)
    {
       // return a value of the EngagementReachHandler.NotificationPosition enum (TOP or BOTTOM)
    }

Actuellement, vous pouvez choisir entre le `BOTTOM` (par défaut) et `TOP` positions.

### <a name="launch-message"></a>Lancement de message

Lorsqu’un utilisateur clique sur une notification du système (un toast), Engagement lance l’application, chargez le contenu des messages envoyés et afficher la page de la campagne correspondante.

Il existe un délai entre le lancement de l’application et l’affichage de la page (en fonction de la vitesse de votre réseau).

Pour indiquer à l’utilisateur que quelque chose se charge, vous devez fournir une informations visuelles, comme une barre de progression ou un indicateur de progression. Engagement ne peut gérer que lui-même, mais fournit quelques gestionnaires pour vous.

Pour implémenter le rappel, effectuez :

    /* The application has launched and the content is loading.
     * You should display an indicator here.
     */
    EngagementReach.Instance.RetrieveLaunchMessageStarted += () => { [...] };
    
    /* The application has finished loading the content and the page
     * is about to be displayed.
     * You should hide the indicator here.
     */
    EngagementReach.Instance.RetrieveLaunchMessageCompleted += () => { [...] };
    
    /* The content has been loaded, but an error has occurred.
     * You can provide an information to the user.
     * You should hide the indicator here.
     */
    EngagementReach.Instance.RetrieveLaunchMessageFailed += () => { [...] };

Vous pouvez définir le rappel dans votre `Application_Launching` PROCEDE de votre `App.xaml.cs` fichier, de préférence avant le `EngagementReach.Instance.Init()` appeler.

> [AZURE.TIP] Chaque gestionnaire est appelé par le Thread de l’interface utilisateur. Vous n’êtes pas obligé de vous inquiétez pas si vous utilisez un MessageBox ou quelque chose liés à l’interface utilisateur.

[Stratégies d’application]:http://msdn.microsoft.com/library/windows/apps/hh184841(v=vs.105).aspx
[Content Policies]:http://msdn.microsoft.com/library/windows/apps/hh184842(v=vs.105).aspx
[Exigences supplémentaires pour les Types d’applications spécifiques]:http://msdn.microsoft.com/library/windows/apps/hh184838(v=vs.105).aspx
 
