<properties
    pageTitle="Les métadonnées de fédération AD Azure | Microsoft Azure"
    description="Cet article décrit le document de métadonnées de fédération Azure Active Directory publie des services qui acceptent des jetons d’Azure Active Directory."
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


# <a name="federation-metadata"></a>Métadonnées de fédération

Azure Active Directory (AD Azure) publie un document de métadonnées de fédération des services configurés pour accepter les jetons de sécurité qui émet des annonces Azure. Le format de document des métadonnées de fédération est décrit dans le [Web Services Federation Language (WS-Federation) Version 1.2](http://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html), qui étend les [métadonnées pour la version 2.0 d’OASIS Markup langage SAML (Security Assertion)](http://docs.oasis-open.org/security/saml/v2.0/saml-metadata-2.0-os.pdf).

## <a name="tenant-specific-and-tenant-independent-metadata-endpoints"></a>Clients spécifiques et les points de terminaison de métadonnées indépendantes du locataire

Annonce Azure publie des points de terminaison clients spécifiques et des clients indépendants.

Les points de terminaison clients spécifiques sont conçus pour un client particulier. Les métadonnées de fédération de clients spécifiques incluent des informations sur les clients, y compris les informations de l’émetteur et le point de terminaison de clients spécifiques. Les applications qui limitent l’accès à un seul client utilisent des points de terminaison clients spécifiques.

Les points de terminaison clients indépendants fournissent des informations qui est communes à AD Azure tous les locataires. Ces informations s’applique aux locataires hébergés à *login.microsoftonline.com* et sont partagées par les locataires. Les points de terminaison clients indépendants sont recommandés pour plusieurs utilisateurs, applications, dans la mesure où ils ne sont pas associés à des clients particuliers.

## <a name="federation-metadata-endpoints"></a>Points de terminaison de métadonnées fédération

Annonce Azure publie des métadonnées de fédération à `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`.

Pour **les points de terminaison clients spécifiques**, le `TenantDomainName` peut être un des types suivants :

- Un nom de domaine enregistré d’une annonce Azure client, telle que : `contoso.onmicrosoft.com`.
- Les données immuables client ID du domaine, tel que `72f988bf-86f1-41af-91ab-2d7cd011db45`.

Pour **les points de terminaison clients indépendants**, le `TenantDomainName` est `common`. Ce document répertorie uniquement les éléments de métadonnées de fédération qui sont communes à tous les locataires Azure AD qui sont hébergés à login.microsoftonline.com.

Par exemple, un point de terminaison client spécifique peut être `https:// login.microsoftonline.com/contoso.onmicrosoft.com/FederationMetadata/2007-06/FederationMetadata.xml`. Le point de terminaison client indépendant est [https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml](https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml). Vous pouvez afficher le document de métadonnées de fédération en tapant cet URL dans un navigateur.

## <a name="contents-of-federation-metadata"></a>Contenu des métadonnées de fédération

La section suivante fournit des informations requises par les services qui utilisent les jetons émis par AD Azure.

### <a name="entity-id"></a>ID de l’entité

Le `EntityDescriptor` élément contient un `EntityID` attribut. La valeur de la `EntityID` attribut représente l’émetteur, c'est-à-dire le service jeton de sécurité (STS) qui a émis le jeton. Il est important de valider l’émetteur lorsque vous recevez un jeton.

Les métadonnées suivantes présente un exemple de client spécifique `EntityDescriptor` élément avec un `EntityID` élément.

```
<EntityDescriptor
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="_b827a749-cfcb-46b3-ab8b-9f6d14a1294b"
entityID="https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db45/">
```
Vous pouvez remplacer l’ID de client dans le point de terminaison client indépendant avec votre ID client pour créer un spécifique de clients `EntityID` valeur. La valeur résultante sera le même que l’émetteur de jeton. La stratégie permet à une application de plusieurs utilisateurs valider l’émetteur pour un client donné.

Les métadonnées suivantes présente un exemple de client indépendant `EntityID` élément. Veuillez noter que les `{tenant}` est un littéral, pas un espace réservé.

```
<EntityDescriptor
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="="_0e5bd9d0-49ef-4258-bc15-21ce143b61bd"
entityID="https://sts.windows.net/{tenant}/">
```

### <a name="token-signing-certificates"></a>Certificats de signature de jeton

Lorsqu’un service reçoit un jeton qui est émis par un client AD Azure, la signature du jeton doit être validée avec une clé de signature qui est publiée dans le document de métadonnées de fédération. Les métadonnées de fédération contient la partie publique des certificats utilisés par les locataires pour la signature des jetons. Les octets bruts du certificat s’affichent dans le `KeyDescriptor` élément. Le certificat de signature de jeton est valide pour la signature uniquement lorsque la valeur de la `use` attribut est `signing`.

Un document de métadonnées de fédération publié par AD Azure peut avoir plusieurs clés de signature, par exemple lorsque AD Azure se prépare à jour le certificat de signature. Lorsqu’un document de métadonnées de fédération comprend plus d’un certificat, un service qui valide les jetons doit prendre en charge tous les certificats dans le document.

Les métadonnées suivantes présente un exemple de `KeyDescriptor` élément avec une clé de signature.

```
<KeyDescriptor use="signing">
<KeyInfo xmlns="http://www.w3.org/2000/09/xmldsig#">
<X509Data>
<X509Certificate>
MIIDPjCCAiqgAwIBAgIQVWmXY/+9RqFA/OG9kFulHDAJBgUrDgMCHQUAMC0xKzApBgNVBAMTImFjY291bnRzLmFjY2Vzc2NvbnRyb2wud2luZG93cy5uZXQwHhcNMTIwNjA3MDcwMDAwWhcNMTQwNjA3MDcwMDAwWjAtMSswKQYDVQQDEyJhY2NvdW50cy5hY2Nlc3Njb250cm9sLndpbmRvd3MubmV0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEArCz8Sn3GGXmikH2MdTeGY1D711EORX/lVXpr+ecGgqfUWF8MPB07XkYuJ54DAuYT318+2XrzMjOtqkT94VkXmxv6dFGhG8YZ8vNMPd4tdj9c0lpvWQdqXtL1TlFRpD/P6UMEigfN0c9oWDg9U7Ilymgei0UXtf1gtcQbc5sSQU0S4vr9YJp2gLFIGK11Iqg4XSGdcI0QWLLkkC6cBukhVnd6BCYbLjTYy3fNs4DzNdemJlxGl8sLexFytBF6YApvSdus3nFXaMCtBGx16HzkK9ne3lobAwL2o79bP4imEGqg+ibvyNmbrwFGnQrBc1jTF9LyQX9q+louxVfHs6ZiVwIDAQABo2IwYDBeBgNVHQEEVzBVgBCxDDsLd8xkfOLKm4Q/SzjtoS8wLTErMCkGA1UEAxMiYWNjb3VudHMuYWNjZXNzY29udHJvbC53aW5kb3dzLm5ldIIQVWmXY/+9RqFA/OG9kFulHDAJBgUrDgMCHQUAA4IBAQAkJtxxm/ErgySlNk69+1odTMP8Oy6L0H17z7XGG3w4TqvTUSWaxD4hSFJ0e7mHLQLQD7oV/erACXwSZn2pMoZ89MBDjOMQA+e6QzGB7jmSzPTNmQgMLA8fWCfqPrz6zgH+1F1gNp8hJY57kfeVPBiyjuBmlTEBsBlzolY9dd/55qqfQk6cgSeCbHCy/RU/iep0+UsRMlSgPNNmqhj5gmN2AFVCN96zF694LwuPae5CeR2ZcVknexOWHYjFM0MgUSw0ubnGl0h9AJgGyhvNGcjQqu9vd1xkupFgaN+f7P3p3EVN5csBg5H94jEcQZT7EKeTiZ6bTrpDAnrr8tDCy8ng
</X509Certificate>
</X509Data>
</KeyInfo>
</KeyDescriptor>
  ```

Le `KeyDescriptor` élément apparaît à deux endroits dans le document de métadonnées de fédération ; dans la section spécifique WS-Federation et la section SAML spécifique. Les certificats publiés dans les deux sections sera le même.

Dans la section spécifique WS-Federation, un lecteur de métadonnées WS-Federation lit les certificats à partir d’un `RoleDescriptor` élément avec le `SecurityTokenServiceType` type.

Les métadonnées suivantes présente un exemple de `RoleDescriptor` élément.

```
<RoleDescriptor xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:fed="http://docs.oasis-open.org/wsfed/federation/200706" xsi:type="fed:SecurityTokenServiceType"protocolSupportEnumeration="http://docs.oasis-open.org/wsfed/federation/200706">
```

Dans la section SAML spécifique, un lecteur de métadonnées WS-Federation lit les certificats à partir d’un `IDPSSODescriptor` élément.

Les métadonnées suivantes présente un exemple de `IDPSSODescriptor` élément.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
```
Il n’y a aucune différence entre le format des certificats clients spécifiques et des clients indépendants.

### <a name="ws-federation-endpoint-url"></a>URL du point de terminaison WS-Federation

Les métadonnées de fédération incluent l’URL utilise AD Azure pour la connexion unique et simple de déconnexion dans le protocole WS-Federation. Ce point de terminaison s’affiche dans le `PassiveRequestorEndpoint` élément.

Les métadonnées suivantes présente un exemple de `PassiveRequestorEndpoint` élément pour un point de terminaison de client spécifiques.

```
<fed:PassiveRequestorEndpoint>
<EndpointReference xmlns="http://www.w3.org/2005/08/addressing">
<Address>
https://login.microsoftonline.com/72f988bf-86f1-41af-91ab-2d7cd011db45/wsfed
</Address>
</EndpointReference>
</fed:PassiveRequestorEndpoint>
```
Pour le point de terminaison client indépendant, l’URL de WS-Federation apparaît dans le point de terminaison WS-Federation, comme illustré dans l’exemple suivant.

```
<fed:PassiveRequestorEndpoint>
<EndpointReference xmlns="http://www.w3.org/2005/08/addressing">
<Address>
https://login.microsoftonline.com/common/wsfed
</Address>
</EndpointReference>
</fed:PassiveRequestorEndpoint>
```

### <a name="saml-protocol-endpoint-url"></a>URL de point de terminaison de protocole SAML

Les métadonnées de fédération incluent l’URL AD Azure utilise pour la connexion unique et simple fermeture de session de protocole SAML 2.0. Ces points de terminaison qui s’affichent dans le `IDPSSODescriptor` élément.

L’URL de connexion et de déconnexion s’affichent dans le `SingleSignOnService` et `SingleLogoutService` éléments.

Les métadonnées suivantes présente un exemple de `PassiveResistorEndpoint` pour un point de terminaison de client spécifiques.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
…
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/contoso.onmicrosoft.com/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/contoso.onmicrosoft.com /saml2" />
  </IDPSSODescriptor>
```

De la même façon les points de terminaison pour les points de terminaison de protocole SAML 2.0 communes sont publiés dans les métadonnées de fédération de clients indépendants, comme illustré dans l’exemple suivant.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
…
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/common/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/common/saml2" />
  </IDPSSODescriptor>
```
