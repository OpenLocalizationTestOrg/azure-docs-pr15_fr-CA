<properties 
    pageTitle="Vue d’ensemble du modèle de sécurité et d’authentification de concentrateurs d’événement | Microsoft Azure"
    description="Événement concentrateurs d’authentification et de sécurité modèle vue d’ensemble."
    services="event-hubs"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="event-hubs"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="08/16/2016"
    ms.author="sethm;clemensv" />

# <a name="event-hubs-authentication-and-security-model-overview"></a>Présentation modèle événement concentrateurs d’authentification et de sécurité

Le modèle de sécurité de concentrateurs d’événements remplit les conditions suivantes :

- Seuls les périphériques qui présentent des informations d’identification valides peuvent envoyer des données à un concentrateur d’événements.
- Un périphérique ne peut pas emprunter l’identité d’un autre périphérique.
- Un périphérique non fiables peut être bloqué d’envoyer des données à un concentrateur d’événements.

## <a name="device-authentication"></a>Authentification de dispositif

Le modèle de sécurité de concentrateurs de l’événement est basé sur une combinaison de jetons de [Signature de l’accès partagé (SAS)](../service-bus-messaging/service-bus-shared-access-signature-authentication.md) et *éditeurs d’événements*. Un éditeur d’événements définit un point de terminaison virtuel pour un concentrateur d’événements. L’éditeur peut uniquement être utilisé pour envoyer des messages à un concentrateur d’événements. Il n’est pas possible de recevoir des messages provenant d’un éditeur.

En général, un concentrateur d’événements utilise un seul éditeur par périphérique. Tous les messages qui sont envoyés à aucun des éditeurs d’un concentrateur d’événements sont placé dans ce concentrateur d’événements. Éditeurs activer le contrôle d’accès précis et la limitation.

Un jeton unique, qui est téléchargé vers le périphérique est affecté à chaque périphérique. Les jetons sont produits tels que chaque jeton unique accorde l’accès à un autre éditeur unique. Un périphérique qui possède un jeton ne peut envoyer à un éditeur, mais aucun autre éditeur. Si plusieurs périphériques partagent le même jeton, chacun de ces périphériques partage puis un éditeur.

Bien que non recommandé, il est possible d’équiper des périphériques avec des jetons qui accordent l’accès direct à un concentrateur d’événements. N’importe quel périphérique qui conserve ce jeton peut envoyer des messages directement sur ce concentrateur de l’événement. Un tel dispositif ne sera pas soumis à la limitation. En outre, le périphérique ne peut pas être sur liste noire d’envoyer à ce concentrateur d’événements.

Tous les jetons sont signés avec une clé SAS. En règle générale, tous les jetons sont signés avec la même clé. Les périphériques ne sont pas conscients de la clé ; Cela empêche les périphériques à partir de la fabrication des jetons.

### <a name="create-the-sas-key"></a>Créer la clé SAS

Lorsque vous créez un espace de noms d’événements concentrateurs, concentrateurs d’événement Azure génère une clé SAS de 256 bits nommée **RootManageSharedAccessKey**. Cette clé subventions envoyer, écoutent et gérer les droits pour l’espace de noms. Vous pouvez créer des clés supplémentaires. Il est recommandé que vous produisez une clé que subventions envoyer des autorisations pour le concentrateur d’événements spécifiques. Pour le reste de cette rubrique, il est supposé que vous avez nommé cette clé `EventHubSendKey`.

L’exemple suivant crée une clé d’envoi uniquement lors de la création du concentrateur de l’événement :

```
// Create namespace manager.
string serviceNamespace = "YOUR_NAMESPACE";
string namespaceManageKeyName = "RootManageSharedAccessKey";
string namespaceManageKey = "YOUR_ROOT_MANAGE_SHARED_ACCESS_KEY";
Uri uri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, string.Empty);
TokenProvider td = TokenProvider.CreateSharedAccessSignatureTokenProvider(namespaceManageKeyName, namespaceManageKey);
NamespaceManager nm = new NamespaceManager(namespaceUri, namespaceManageTokenProvider);

// Create Event Hub with a SAS rule that enables sending to that Event Hub
EventHubDescription ed = new EventHubDescription("MY_EVENT_HUB") { PartitionCount = 32 };
string eventHubSendKeyName = "EventHubSendKey";
string eventHubSendKey = SharedAccessAuthorizationRule.GenerateRandomKey();
SharedAccessAuthorizationRule eventHubSendRule = new SharedAccessAuthorizationRule(eventHubSendKeyName, eventHubSendKey, new[] { AccessRights.Send });
ed.Authorization.Add(eventHubSendRule); 
nm.CreateEventHub(ed);
```

### <a name="generate-tokens"></a>Générer des jetons

Vous pouvez générer des jetons à l’aide de la clé des associations de sécurité. Vous devez produire un seul jeton par périphérique. Jetons peuvent ensuite être produits à l’aide de la méthode suivante. Tous les jetons sont générés à l’aide de la clé **EventHubSendKey** . Chaque jeton est affectée à un URI unique.

```
public static string SharedAccessSignatureTokenProvider.GetSharedAccessSignature(string keyName, string sharedAccessKey, string resource, TimeSpan tokenTimeToLive)
```

Lors de l’appel de cette méthode, l’URI doit être spécifié en tant que `//<NAMESPACE>.servicebus.windows.net/<EVENT_HUB_NAME>/publishers/<PUBLISHER_NAME>`. Pour tous les jetons, l’URI est identique, à l’exception de `PUBLISHER_NAME`, qui doit être différent pour chaque jeton. Dans l’idéal, `PUBLISHER_NAME` représente l’ID de périphérique qui reçoit ce jeton.

Cette méthode génère un jeton avec la structure suivante :

```
SharedAccessSignature sr={URI}&sig={HMAC_SHA256_SIGNATURE}&se={EXPIRATION_TIME}&skn={KEY_NAME}
```

L’heure d’expiration du jeton est spécifiée en secondes depuis le 1er janvier 1970. Voici un exemple d’un jeton :

```
SharedAccessSignature sr=contoso&sig=nPzdNN%2Gli0ifrfJwaK4mkK0RqAB%2byJUlt%2bGFmBHG77A%3d&se=1403130337&skn=RootManageSharedAccessKey
```

En général, les jetons ont une durée de vie qui ressemble à ou dépasse la durée de vie de l’équipement. Si le périphérique possède la fonctionnalité pour obtenir un nouveau jeton, jetons avec une durée de vie plus courte peuvent être utilisés.

### <a name="devices-sending-data"></a>Envoi de données de périphériques

Une fois que les jetons ont été créés, chaque périphérique est configuré avec son propre jeton unique.

Lorsque le périphérique envoie des données à un concentrateur d’événements, le périphérique son jeton avec la demande d’envoi des balises. Pour empêcher un intrus des écoutes clandestines et voler le jeton, la communication entre l’appareil et le concentrateur d’événements doit se produire sur un canal crypté.

### <a name="blacklisting-devices"></a>Dispositifs de mise en liste noire

Si un jeton est volé par un intrus, le pirate peut emprunter l’identité de l’équipement dont le jeton a été volé. Un dispositif de mise en liste noire rend le périphérique inutilisable jusqu'à ce que le périphérique est donné à un nouveau jeton qui utilise un éditeur différent.

## <a name="authentication-of-back-end-applications"></a>Authentification des applications du serveur principal

Pour authentifier les applications back-end qui utilisent les données générées par les périphériques, concentrateurs d’événement utilise un modèle de sécurité qui est semblable au modèle qui est utilisé pour des thèmes du Bus de Service. Un groupe de consommateurs de concentrateurs d’événement est équivalent à un abonnement à une rubrique du Bus de Service. Un client peut créer un groupe de consommateurs si la demande de création du groupe de consommateur est accompagnée d’un jeton que subventions gérer des privilèges pour le concentrateur de l’événement, ou l’espace de noms auquel appartient le concentrateur d’événements. Un client est autorisé à utiliser des données d’un groupe de consommateurs si la demande de réception est accompagnée d’un jeton qui autorise la réception sur ce groupe de consommateurs, le concentrateur d’événements ou l’espace de noms auquel appartient le concentrateur d’événements.

La version actuelle du Bus de Service ne gère pas les règles SAS pour les abonnements individuels. Il en va de même des groupes de consommateurs d’événement concentrateurs. Prise en charge des associations de sécurité est ajouté à l’avenir pour ces deux fonctionnalités.

En l’absence d’authentification d’associations de sécurité des groupes de consommateurs individuels, vous pouvez utiliser les clés des associations de sécurité pour sécuriser tous les groupes de consommateurs avec une clé commune. Cette approche permet à une application de consommer des données à partir d’un des groupes de consommateurs d’un concentrateur d’événements.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les concentrateurs d’événement, reportez-vous aux rubriques suivantes :

- [Vue d’ensemble des concentrateurs événements]
- Une [solution de messagerie de la file d’attente] à l’aide de files d’attente de Bus de Service.
- Un [exemple d’application qui utilise des concentrateurs d’événement]complète.

[Vue d’ensemble des concentrateurs événements]: event-hubs-overview.md
[exemple d’application qui utilise des concentrateurs d’événement]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[file d’attente de la solution de messagerie]: ../service-bus-messaging/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md
 
