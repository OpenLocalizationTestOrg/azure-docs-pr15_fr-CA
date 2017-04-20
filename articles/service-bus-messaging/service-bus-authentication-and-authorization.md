<properties 
    pageTitle="Service d’authentification de Bus et d’autorisation | Microsoft Azure"
    description="Vue d’ensemble de l’authentification de Signature de l’accès partagé (SAS)."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="10/03/2016"
    ms.author="sethm" />

# <a name="service-bus-authentication-and-authorization"></a>Bus de Service authentification et autorisation

Les applications peuvent s’authentifier au Bus des services Azure en utilisant soit l’authentification de Signature de l’accès partagé (SAS) ou via Azure Active Directory contrôle d’accès (également appelé Service de contrôle d’accès ou ACS). Partagé des accès Signature d’authentification permet aux applications à s’authentifier sur le Bus de Service à l’aide d’une touche d’accès configurée sur l’espace de noms, ou sur l’entité auxquels sont associés des droits spécifiques. Vous pouvez ensuite utiliser cette clé pour générer un jeton de Signature de l’accès partagé que les clients peuvent utiliser pour s’authentifier sur le Bus de Service.

>AZURE. SAS IMPORTANT est recommandée sur ACS, car elle fournit un schéma d’authentification simple, flexible et facile à utiliser pour le Bus de Service. Les applications peuvent utiliser des associations de sécurité dans les scénarios dans lesquels ils n’avez pas besoin de gérer la notion d’un autorisés « utilisateur ».

## <a name="shared-access-signature-authentication"></a>Authentification de Signature de l’accès partagée

[L’authentification d’associations de sécurité](service-bus-sas-overview.md) vous permet d’accorder un accès utilisateur aux ressources de Bus de Service avec des droits spécifiques. Authentification de SAS dans le Bus de Service implique la configuration d’une clé cryptographique avec les droits associés sur une ressource de Service Bus. Les clients peuvent alors accéder à cette ressource en présentant un jeton SAS qui se compose de l’URI en cours d’accès et d’expiration signé avec la clé de configuration.

Vous pouvez configurer les clés pour les associations de sécurité sur le Bus de Service d’un espace de noms. La clé s’applique à toutes les entités de messagerie dans cet espace de noms. Vous pouvez également configurer des clés sur des sujets et des files d’attente de Bus de Service. SAS est également pris en charge sur des Bus de Service relais.

Pour utiliser des associations de sécurité, vous pouvez configurer un objet [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx) sur un espace de noms, la file d’attente ou la rubrique qui comprend les éléments suivants :

- *Nom* qui identifie la règle.

- *PrimaryKey* est une clé de chiffrement utilisée pour valider/signe les jetons SAS.

- *SecondaryKey* est une clé de chiffrement utilisée pour valider/signe les jetons SAS.

- *Droits* qui représente la collection d’écoute, d’envoyer ou de gérer des droits accordés.

Règles d’autorisation configurées au niveau de l’espace de noms peuvent accorder l’accès à toutes les entités dans un espace de noms pour les clients avec des jetons signés à l’aide de la clé correspondante. Jusqu'à 12 d’autorisation des règles peuvent être configurées sur un espace de noms, une file d’attente ou une rubrique Bus de Service. Par défaut, un [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx) avec tous les droits est configuré pour chaque espace de noms lorsqu’il est tout d’abord mis en service.

Pour accéder à une entité, le client requiert un jeton SAS généré à l’aide d’un [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx)de spécifique. Le jeton SAS est généré en utilisant le code HMAC-SHA256 d’une chaîne de ressources qui se compose de l’URI de la ressource à laquelle l’accès est demandé et d’expiration avec une clé de chiffrement associée à la règle d’autorisation.

Prise en charge de l’authentification SAS pour le Bus de Service est inclus dans les versions d’Azure .NET SDK 2.0 et versions ultérieures. SAS prend en charge une [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx). Toutes les API qui acceptent comme paramètre une chaîne de connexion prennent en charge les chaînes de connexion de SAS.

## <a name="acs-authentication"></a>Authentification ACS

Authentification de service Bus via ACS est gérée via un Compagnon «-sb » espace de noms ACS. Si vous souhaitez un espace de noms ACS associé doivent être créés pour un espace de noms du Bus des services, vous ne pouvez pas créer votre espace de noms du Bus des services à l’aide du portail Azure classique ; Vous devez créer l’espace de noms à l’aide de l’applet de commande PowerShell [New-AzureSBNamespace](https://msdn.microsoft.com/library/azure/dn495165.aspx) . Par exemple :

```
New-AzureSBNamespace <namespaceName> "<Region>” -CreateACSNamespace $true
```

Pour éviter de créer un espace de noms ACS, exécutez la commande suivante :

```
New-AzureSBNamespace <namespaceName> "<Region>” -CreateACSNamespace $false
```

Par exemple, si vous créez un espace de noms du Bus de Service appelé **contoso.servicebus.windows.net**, un espace de noms ACS Compagnon appelée **contoso-sb.accesscontrol.windows.net** est configuré automatiquement. Pour tous les espaces de noms qui ont été créés avant août 2014, un espace de noms ACS qui l’accompagne a été également créé.

Une identité de service par défaut « propriétaire », avec tous les droits, est configurée par défaut dans cet espace de noms ACS le Guide. Vous pouvez obtenir un contrôle affiné à toute entité de Bus de Service via ACS en configurant les relations d’approbation appropriée. Vous pouvez configurer les identités de service supplémentaires pour la gestion de l’accès aux entités de Bus de Service.

Pour accéder à une entité, le client demande un jeton SWT ACS avec les déclarations appropriées en présentant ses informations d’identification. Le jeton SWT doit être mise dans le cadre de la demande au Service de Bus pour permettre l’autorisation du client pour l’accès à l’entité.

Prise en charge de l’authentification ACS pour le Bus de Service est inclus dans les versions d’Azure .NET SDK 2.0 et versions ultérieures. Cette authentification prend en charge une [SharedSecretTokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.sharedsecrettokenprovider.aspx). Toutes les API qui acceptent comme paramètre une chaîne de connexion prennent en charge les chaînes de connexion ACS.

## <a name="next-steps"></a>Étapes suivantes

Poursuivre la lecture de [l’authentification de Signature de l’accès partagé avec le Bus de Service](service-bus-shared-access-signature-authentication.md) pour plus d’informations sur les associations de sécurité.

Pour obtenir une vue d’ensemble d’associations dans le Bus des services de sécurité, consultez [Signatures de l’accès partagé](service-bus-sas-overview.md).

Vous trouverez plus d’informations sur les jetons ACS dans [Comment : demander un jeton ACS via le protocole de retour à la ligne OAuth](https://msdn.microsoft.com/library/hh674475.aspx).



