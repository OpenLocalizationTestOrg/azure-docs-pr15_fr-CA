<properties
    pageTitle="Authentification unique Azure SAML protocole | Microsoft Azure"
    description="Cet article décrit le protocole d’authentification unique sur SAML dans Azure Active Directory"
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

# <a name="single-sign-on-saml-protocol"></a>Protocole d’authentification SAML unique

Cet article traite les demandes d’authentification de SAML 2.0 et les réponses qui prend en charge les Azure Active Directory (AD Azure) pour Single Sign-On.

Le schéma de protocole ci-dessous décrit la séquence d’ouverture de session unique. Le service cloud (le fournisseur de services) utilise une liaison de redirection HTTP pour transmettre un `AuthnRequest` élément (demande d’authentification) pour Azure AD (le fournisseur d’identité). Annonce Azure puis utilise un verbe HTTP post de liaison pour valider un `Response` élément au service cloud.

![Ouverture de session unique sur le flux de travail](media/active-directory-single-sign-on-protocol-reference/active-directory-saml-single-sign-on-workflow.png)

## <a name="authnrequest"></a>AuthnRequest

Pour demander une authentification utilisateur, les services de cloud envoyer un `AuthnRequest` élément AD Azure. Un échantillon SAML 2.0 `AuthnRequest` pourrait ressembler à ceci :

```
<samlp:AuthnRequest
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="id6c1c178c166d486687be4aaf5e482730"
Version="2.0" IssueInstant="2013-03-18T03:28:54.1839884Z"
xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
</samlp:AuthnRequest>
```


| Paramètre | | Description |
| ----------------------- | ------------------------------- | --------------- |
| ID | Obligatoire | Annonce Azure utilise cet attribut pour remplir le `InResponseTo` l’attribut de la réponse retournée. ID ne doit pas commencer par un numéro, pour une stratégie commune consiste à ajouter une chaîne like « id » de la représentation sous forme de chaîne d’un GUID. Par exemple, `id6c1c178c166d486687be4aaf5e482730` est un identificateur valide. |
| Version | Obligatoire | Il s’agit de **2.0**.|
| IssueInstant | Obligatoire | Il s’agit d’une chaîne DateTime avec une valeur UTC et un [format aller-retour (« o »)](https://msdn.microsoft.com/library/az4se3k1.aspx). Annonce Azure attend une valeur DateTime de ce type, mais ne pas évaluer ou utilisez la valeur. |
| AssertionConsumerServiceUrl | facultatif | Si fourni, il doit correspondre à la `RedirectUri` du service cloud dans Azure AD. |
| ForceAuthn | facultatif | Si fourni, il doit être false. Toute autre valeur génère une erreur.|
| IsPassive | facultatif | Si fourni, il doit être false. Toute autre valeur génère une erreur. |  

Tous les autres `AuthnRequest` des attributs, tels que le consentement, Destination, AssertionConsumerServiceIndex, AttributeConsumerServiceIndex et ProviderName sont **ignorés**.

Annonce Azure ignore également les `Conditions` élément dans `AuthnRequest`.

### <a name="issuer"></a>Émetteur

Le `Issuer` élément dans une `AuthnRequest` doit correspondre exactement à un de l' **ServicePrincipalNames** dans le service cloud dans Azure AD. En général, ceci est définie à l' **URI d’application de code** qui est spécifié lors de l’inscription de l’application.

Un extrait d’exemple SAML contenant le `Issuer` élément ressemble à ceci :

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
```

### <a name="nameidpolicy"></a>NameIDPolicy

Cet élément demande un format de code nom particulier dans la réponse et est facultatif dans les `AuthnRequest` les éléments envoyés à AD Azure.

Un échantillon `NameIdPolicy` élément ressemble à ceci :

```
<NameIDPolicy Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent"/>
```

Si `NameIDPolicy` est fourni, vous pouvez inclure ses facultatif `Format` attribut. Le `Format` attribut peut avoir uniquement une des valeurs suivantes ; toute autre valeur génère une erreur.

-  `urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`: Azure Active Directory émet la demande NameID comme identificateur par paire.
- `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`: Azure Active Directory émet la demande NameID dans le format d’adresse électronique.
- `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`: Cette valeur autorise les Azure Active Directory pour sélectionner le format de la demande. Azure Active Directory émet le NameID comme identificateur par paire.

N’incluez pas le `SPNameQualifer` attribut. Annonce Azure ignore le `AllowCreate` attribut.

### <a name="requestauthncontext"></a>RequestAuthnContext

Le `RequestedAuthnContext` élément spécifie les méthodes d’authentification de votre choix. Elle est facultative dans les `AuthnRequest` les éléments envoyés à AD Azure. Annonce Azure prend en charge qu’un seul `AuthnContextClassRef` valeur : `urn:oasis:names:tc:SAML:2.0:ac:classes:Password`.

### <a name="scoping"></a>Limitation d’étendue

Le `Scoping` élément qui inclut une liste de fournisseurs d’identité, est facultative dans les `AuthnRequest` les éléments envoyés à AD Azure.

Si fourni, n’incluez pas le `ProxyCount` attribut, `IDPListOption` ou `RequesterID` élément, qu’ils ne sont pas pris en charge.

### <a name="signature"></a>Signature

N’incluez pas une `Signature` élément dans `AuthnRequest` éléments, car Azure AD ne supporte pas signé les demandes d’authentification.

### <a name="subject"></a>Objet

Annonce Azure ignore le `Subject` élément de `AuthnRequest` éléments.

## <a name="response"></a>Réponse

Lors d’une ouverture de session demandé se termine avec succès, AD Azure publie une réponse pour le service en nuage. Un exemple de réponse à une tentative d’ouverture de session réussie ressemble à ceci :

```
<samlp:Response ID="_a4958bfd-e107-4e67-b06d-0d85ade2e76a" Version="2.0" IssueInstant="2013-03-18T07:38:15.144Z" Destination="https://contoso.com/identity/inboundsso.aspx" InResponseTo="id758d0ef385634593a77bdf7e632984b6" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
    ...
  </ds:Signature>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
  </samlp:Status>
  <Assertion ID="_bf9c623d-cc20-407a-9a59-c2d0aee84d12" IssueInstant="2013-03-18T07:38:15.144Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">
    <Issuer>https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
    <ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
      ...
    </ds:Signature>
    <Subject>
      <NameID>Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="id758d0ef385634593a77bdf7e632984b6" NotOnOrAfter="2013-03-18T07:43:15.144Z" Recipient="https://contoso.com/identity/inboundsso.aspx" />
      </SubjectConfirmation>
    </Subject>
    <Conditions NotBefore="2013-03-18T07:38:15.128Z" NotOnOrAfter="2013-03-18T08:48:15.128Z">
      <AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
      </AudienceRestriction>
    </Conditions>
    <AttributeStatement>
      <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
        <AttributeValue>testuser@contoso.com</AttributeValue>
      </Attribute>
      <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
        <AttributeValue>3F2504E0-4F89-11D3-9A0C-0305E82C3301</AttributeValue>
      </Attribute>
      ...
    </AttributeStatement>
    <AuthnStatement AuthnInstant="2013-03-18T07:33:56.000Z" SessionIndex="_bf9c623d-cc20-407a-9a59-c2d0aee84d12">
      <AuthnContext>
        <AuthnContextClassRef> urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
      </AuthnContext>
    </AuthnStatement>
  </Assertion>
</samlp:Response>
```

### <a name="response"></a>Réponse

Le `Response` élément inclut le résultat de la demande d’autorisation. Azure AD définit les `ID`, `Version` et `IssueInstant` les valeurs dans le `Response` élément. Il définit également les attributs suivants :

- `Destination`: Lorsque la session se termine avec succès, la valeur est la `RedirectUri` du fournisseur de services (service cloud).
- `InResponseTo`: Ce paramètre est défini la `ID` l’attribut de la `AuthnRequest` élément qui a initialisé la réponse.

### <a name="issuer"></a>Émetteur

Azure AD définit les `Issuer` élément `https://login.microsoftonline.com/<TenantIDGUID>/` où <TenantIDGUID> est l’ID de client du locataire AD Azure.

Par exemple, un exemple de réponse avec élément d’émetteur pourrait ressembler à ceci :

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

### <a name="status"></a>État

Le `Status` élément indique la réussite ou l’échec de l’ouverture de session. Il inclut le `StatusCode` élément, qui contient un code ou un ensemble de codes imbriqués qui représentent l’état de la demande. Il inclut également le `StatusMessage` élément, qui contient des messages d’erreur personnalisés générés au cours du processus d’ouverture de session.

<!-- TODO: Add a authentication protocol error reference -->

Voici une réponse SAML à une tentative d’ouverture de session échoue.

```
<samlp:Response ID="_f0961a83-d071-4be5-a18c-9ae7b22987a4" Version="2.0" IssueInstant="2013-03-18T08:49:24.405Z" InResponseTo="iddce91f96e56747b5ace6d2e2aa9d4f8c" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Requester">
      <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:RequestUnsupported" />
    </samlp:StatusCode>
    <samlp:StatusMessage>AADSTS75006: An error occurred while processing a SAML2 Authentication request. AADSTS90011: The SAML authentication request property 'NameIdentifierPolicy/SPNameQualifier' is not supported.
Trace ID: 66febed4-e737-49ff-ac23-464ba090d57c
Timestamp: 2013-03-18 08:49:24Z</samlp:StatusMessage>
  </samlp:Status>
```

### <a name="assertion"></a>Assertion

En plus de la `ID`, `IssueInstant` et `Version`, AD Azure définit les éléments suivants le `Assertion` élément de la réponse.

#### <a name="issuer"></a>Émetteur

Cette option est définie `https://sts.windows.net/<TenantIDGUID>/`où <TenantIDGUID> est l’ID de client du locataire AD Azure.

```
<Issuer>https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

#### <a name="signature"></a>Signature

Annonce Azure signe l’assertion en réponse à une ouverture de session réussie. Le `Signature` élément contient une signature numérique que le service en nuage peut utiliser pour authentifier la source pour vérifier l’intégrité de l’assertion.

Pour générer cette signature numérique, AD Azure utilise la clé de signature dans le `IDPSSODescriptor` élément de son document de métadonnées.

```
<ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
      digital_signature_here
    </ds:Signature>
```

#### <a name="subject"></a>Objet

Spécifie l’entité de sécurité qui fait l’objet des instructions de l’assertion. Il contient un `NameID` élément, qui représente l’utilisateur authentifié. Le `NameID` valeur est un identificateur de cible qui est uniquement dirigé vers le fournisseur de services est le public visé pour le jeton. Il est persistant - il peut être révoqué, mais n’est jamais été réassigné. Il est également opaque, dans la mesure où elle ne révèle pas quoi que ce soit sur l’utilisateur et ne peut pas être utilisé comme identificateur pour les requêtes d’attribut.

Le `Method` l’attribut de la `SubjectConfirmation` élément est toujours définie sur `urn:oasis:names:tc:SAML:2.0:cm:bearer`.

```
<Subject>
      <NameID>Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="id758d0ef385634593a77bdf7e632984b6" NotOnOrAfter="2013-03-18T07:43:15.144Z" Recipient="https://contoso.com/identity/inboundsso.aspx" />
      </SubjectConfirmation>
</Subject>
```

#### <a name="conditions"></a>Conditions

Cet élément spécifie les conditions qui définissent l’utilisation acceptable des assertions SAML.

```
<Conditions NotBefore="2013-03-18T07:38:15.128Z" NotOnOrAfter="2013-03-18T08:48:15.128Z">
      <AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
      </AudienceRestriction>
</Conditions>
```

La `NotBefore` et `NotOnOrAfter` attributs spécifient l’intervalle pendant lequel l’assertion est valide.

- La valeur de la `NotBefore` attribut est égal à ou légèrement (moins d’une seconde) ultérieure à la valeur de `IssueInstant` l’attribut de la `Assertion` élément. Annonce Azure ne tient pas compte des différences de temps entre lui-même et le service en nuage (fournisseur de services) et n’ajoute pas d’une mémoire tampon à ce moment.
- La valeur de la `NotOnOrAfter` attribut est 70 minutes plus tard que la valeur de la `NotBefore` attribut.

#### <a name="audience"></a>Public

Contient un URI qui identifie une audience. Annonce Azure définit la valeur de cet élément la valeur de `Issuer` élément de la `AuthnRequest` qui a lancé la session. Pour évaluer la `Audience` , utilisez la valeur de la `App ID URI` qui a été spécifié lors de l’inscription de l’application.

```
<AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
</AudienceRestriction>
```

Comme le `Issuer` valeur, le `Audience` valeur doit correspondre exactement à un des noms principaux du service qui représente le service cloud dans Azure AD. Toutefois, si la valeur de la `Issuer` élément n’est pas une valeur d’URI, le `Audience` la valeur de la réponse est la `Issuer` value préfixé avec `spn:`.

#### <a name="attributestatement"></a>AttributeStatement

Ce fichier contient les revendications sur le sujet ou l’utilisateur. L’extrait suivant contient un exemple de `AttributeStatement` élément. Les points de suspension indique que l’élément peut contenir plusieurs attributs et valeurs d’attribut.

```
<AttributeStatement>
      <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
        <AttributeValue>testuser@contoso.com</AttributeValue>
      </Attribute>
      <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
        <AttributeValue>3F2504E0-4F89-11D3-9A0C-0305E82C3301</AttributeValue>
      </Attribute>
      ...
</AttributeStatement>
```     

- **Revendication de nom** : la valeur de la `Name` attribut (`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`) est le nom d’utilisateur principal de l’utilisateur authentifié, tel que `testuser@managedtenant.com`.
- **Demande de ObjectIdentifier** : la valeur de la `ObjectIdentifier` attribut (`http://schemas.microsoft.com/identity/claims/objectidentifier`) est la `ObjectId` de l’objet annuaire représentant l’utilisateur authentifié dans Active Directory Azure. `ObjectId`est immuable, unique au monde et réutilisation identificateur de sécurité de l’utilisateur authentifié.

#### <a name="authnstatement"></a>AuthnStatement

Cet élément indique que l’objet de l’assertion a été authentifié par un moyen particulier à un moment donné.

- Le `AuthnInstant` attribut spécifie l’heure à laquelle l’utilisateur s’est authentifié avec AD Azure.
- Le `AuthnContext` élément spécifie le contexte d’authentification utilisé pour authentifier l’utilisateur.

```
<AuthnStatement AuthnInstant="2013-03-18T07:33:56.000Z" SessionIndex="_bf9c623d-cc20-407a-9a59-c2d0aee84d12">
      <AuthnContext>
        <AuthnContextClassRef> urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
      </AuthnContext>
</AuthnStatement>
```
