<properties
    pageTitle="Authentification de Service à Service Azure AD à l’aide de OAuth2.0 | Microsoft Azure"
    description="Cet article décrit comment utiliser les messages HTTP pour implémenter l’authentification de service à service l’aide du flux de licence de d’informations d’identification client OAuth2.0."
    services="active-directory"
    documentationCenter=".net"
    authors="priyamohanram"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="priyamo"/>

# <a name="service-to-service-calls-using-client-credentials"></a>Service pour les appels de service à l’aide des informations d’identification du client

Le OAuth 2.0 Client informations d’identification de subvention de flux permet à un service web (un *client confidentielles*) à utiliser ses propres informations d’identification pour l’authentification lors de l’appel d’un autre service web, au lieu de l’emprunt d’identité d’un utilisateur. Dans ce scénario, le client est généralement un service web de couche intermédiaire, un service démon ou site web.

## <a name="client-credentials-grant-flow-diagram"></a>Informations d’identification client accordent le diagramme de flux

Le diagramme suivant explique comment les informations d’identification client accordent à works de flux dans Azure Active Directory (AD Azure).

![Flux accorder des informations d’identification des clients OAuth2.0](media/active-directory-protocols-oauth-service-to-service/active-directory-protocols-oauth-client-credentials-grant-flow.jpg)

1. L’application cliente s’authentifie sur le point de terminaison d’émission du jeton AD Azure et demande un jeton d’accès.
2. Le point de terminaison d’émission du jeton AD Azure émet le jeton d’accès.
3. Le jeton d’accès sert à authentifier les ressources sécurisées.
4. Les données à partir de la ressource sécurisée sont retournées à l’application web.

## <a name="register-the-services-in-azure-ad"></a>Inscrire les Services dans Active Directory Azure

Enregistrer le service d’appel et le service de réception dans Azure Active Directory (AD Azure). Pour obtenir des instructions détaillées, reportez-vous à la section [Ajout, mise à jour et suppression d’une application](active-directory-integrating-applications.md#BKMK_Native)

## <a name="request-an-access-token"></a>Demande un jeton d’accès

Pour demander un jeton d’accès, utilisez une requête HTTP POST au spécifique au locataire point de terminaison AD Azure.

```
https://login.microsoftonline.com/<tenant id>/oauth2/token
```

## <a name="service-to-service-access-token-request"></a>Demande de jeton d’accès de service-service

Une demande de jeton d’accès de service-service contient les paramètres suivants.

| Paramètre | | Description |
|-----------|------|------------|
| response_type | Obligatoire | Spécifie le type de réponse demandé. Dans un flux d’accorder des informations d’identification de Client, la valeur doit être **client_credentials**.|
| identifiant_client | Obligatoire | Spécifie l’id de client AD Azure du service web appelant. Pour trouver l’ID de client de l’application appelante, dans le portail de gestion Azure, cliquez sur **Active Directory**, cliquez sur le répertoire, cliquez sur l’application, puis cliquez sur **configurer**.|
| client_secret | Obligatoire |  Permet d’entrer une clé enregistrée pour le service web d’appel dans Azure AD. Pour créer une clé dans le portail de gestion Azure, cliquez sur **Active Directory**, cliquez sur le répertoire, cliquez sur l’application, puis cliquez sur **configurer**. |
| ressources | Obligatoire | Entrez l’URI d’ID d’application du service web réception. Pour trouver l’URI ID App, dans le portail de gestion Azure, cliquez sur **Active Directory**et cliquez sur le répertoire, cliquez sur l’application, puis cliquez sur **configurer**. |

## <a name="example"></a>Exemple

Un jeton d’accès pour le service web de https://service.contoso.com/ les demandes HTTP POST suivant. Le `client_id` identifie le service web qui demande le jeton d’accès.

```
POST contoso.com/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&client_id=625bc9f6-3bf6-4b6d-94ba-e97cf07a22de&client_secret=qkDwDJlDfig2IpeuUZYKH1Wb8q1V0ju6sILxQQqhJ+s=&resource=https%3A%2F%2Fservice.contoso.com%2F
```

## <a name="service-to-service-access-token-response"></a>Réponse jeton d’accès de service-Service

Une réponse de succès contient une réponse JSON OAuth 2.0 avec les paramètres suivants.

| Paramètre   | Description |
|-------------|-------------|
|access_token |Le jeton d’accès demandé. Le service web d’appel peut utiliser ce jeton pour s’authentifier sur le service web de réception. |
|access_type  | Indique la valeur du type de jeton. Le seul type qui prend en charge les annonces Azure est **PORTEUR**. Pour plus d’informations sur les jetons de support, consultez la [Framework d’autorisation OAuth 2.0 : utilisation de jeton de support (RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt).
|expires_in   | La durée pendant laquelle le jeton d’accès est valide (en secondes).|
|expires_on   |L’heure d’expiration du jeton d’accès. Cette date est représentée en tant que le nombre de secondes depuis 1970-01-01T0:0:0Z UTC jusqu'à ce que le délai d’expiration. Cette valeur est utilisée pour déterminer la durée de vie des jetons mis en cache. |
|ressources     | L’URI d’ID d’application du service web réception. |

## <a name="example"></a>Exemple

L’exemple suivant montre une réponse de succès à une demande d’un jeton d’accès à un service web.

```
{
"access_token":"eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0ODI2NywibmJmIjoxMzg4NDQ4MjY3LCJleHAiOjEzODg0NTIxNjcsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6ImE5OTE5MTYyLTkyMTctNDlkYS1hZTIyLWYxMTM3YzI1Y2RlYSIsInN1YiI6ImE5OTE5MTYyLTkyMTctNDlkYS1hZTIyLWYxMTM3YzI1Y2RlYSIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZS8iLCJhcHBpZCI6ImQxN2QxNWJjLWM1NzYtNDFlNS05MjdmLWRiNWYzMGRkNThmMSIsImFwcGlkYWNyIjoiMSJ9.aqtfJ7G37CpKV901Vm9sGiQhde0WMg6luYJR4wuNR2ffaQsVPPpKirM5rbc6o5CmW1OtmaAIdwDcL6i9ZT9ooIIicSRrjCYMYWHX08ip-tj-uWUihGztI02xKdWiycItpWiHxapQm0a8Ti1CWRjJghORC1B1-fah_yWx6Cjuf4QE8xJcu-ZHX0pVZNPX22PHYV5Km-vPTq2HtIqdboKyZy3Y4y3geOrRIFElZYoqjqSv5q9Jgtj5ERsNQIjefpyxW3EwPtFqMcDm4ebiAEpoEWRN4QYOMxnC9OUBeG9oLA0lTfmhgHLAtvJogJcYFzwngTsVo6HznsvPWy7UP3MINA",
"token_type":"Bearer",
"expires_in":"3599",
"expires_on":"1388452167",
"resource":"https://service.contoso.com/"
}
```

## <a name="see-also"></a>Voir aussi

* [OAuth 2.0 dans Azure AD](active-directory-protocols-oauth-code.md)
