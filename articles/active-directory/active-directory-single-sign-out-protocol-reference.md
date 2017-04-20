<properties
    pageTitle="Connexion unique Azure SAML protocole | Microsoft Azure"
    description="Cet article décrit le protocole SAML Sign-Out unique dans Azure Active Directory"
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


# <a name="single-sign-out-saml-protocol"></a>Protocole SAML de déconnexion unique

Prend en charge Active Directory (AD Azure) Azure le SAML 2.0 web à profil de fermeture de session unique de navigateur. Pour simple fermeture de session pour fonctionner correctement, Azure AD doit enregistrer des son URL de métadonnées pendant l’inscription de l’application. Annonce Azure Obtient l’URL de la fermeture de session et la clé de signature du service nuage à partir des métadonnées. Annonce Azure utilise la clé de signature pour vérifier la signature sur le LogoutRequest entrant, et utilise le LogoutURL pour rediriger les utilisateurs une fois qu’ils sont signés.

Si le service en nuage ne gère pas un point de terminaison de métadonnées, une fois que l’application est enregistrée, le développeur doit contacter le support technique de Microsoft pour fournir l’URL de la fermeture de session et de la clé de signature.

Ce diagramme montre le flux de travail du processus de déconnexion unique AD Azure.

![Ouverture de session unique des flux de travail](media/active-directory-single-sign-out-protocol-reference/active-directory-saml-single-sign-out-workflow.png)

## <a name="logoutrequest"></a>LogoutRequest

L’envoie de service cloud un `LogoutRequest` message annonce Azure pour indiquer qu’une session a été interrompue. L’extrait suivant montre un exemple de `LogoutRequest` élément.

```
<samlp:LogoutRequest xmlns="urn:oasis:names:tc:SAML:2.0:metadata" ID="idaa6ebe6839094fe4abc4ebd5281ec780" Version="2.0" IssueInstant="2013-03-28T07:10:49.6004822Z" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.workaad.com</Issuer>
  <NameID xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
</samlp:LogoutRequest>
```

### <a name="logoutrequest"></a>LogoutRequest

Le `LogoutRequest` élément envoyées à Active Directory Azure requiert les attributs suivants :

- `ID`: Identifie la demande de déconnexion. La valeur de `ID` ne doit pas commencer par un chiffre. La classique consiste à ajouter le **code** pour la représentation sous forme de chaîne d’un GUID.

- `Version`: La valeur de cet élément vers la **version 2.0**. Cette valeur est requise.

- `IssueInstant`: Il s’agit d’un `DateTime` de chaîne avec une valeur d’heure universelle coordonnée (UTC) et de [format aller-retour (« o »)](https://msdn.microsoft.com/library/az4se3k1.aspx). Annonce Azure attend une valeur de ce type, mais ne l’impose pas.

- Le `Consent`, `Destination`, `NotOnOrAfter` et `Reason` attributs sont ignorés s’ils sont inclus dans un `LogoutRequest` élément.

### <a name="issuer"></a>Émetteur

Le `Issuer` élément dans une `LogoutRequest` doit correspondre exactement à un de l' **ServicePrincipalNames** dans le service cloud dans Azure AD. En général, ceci est définie à l' **URI d’application de code** qui est spécifié lors de l’inscription de l’application.

### <a name="nameid"></a>NameID

La valeur de la `NameID` élément doit correspondre exactement à la `NameID` de l’utilisateur qui est en cours de déconnexion.
## <a name="logoutresponse"></a>LogoutResponse

Azure AD envoie une `LogoutResponse` en réponse à une `LogoutRequest` élément. L’extrait suivant montre un exemple de `LogoutResponse`.

```
<samlp:LogoutResponse ID="_f0961a83-d071-4be5-a18c-9ae7b22987a4" Version="2.0" IssueInstant="2013-03-18T08:49:24.405Z" InResponseTo="iddce91f96e56747b5ace6d2e2aa9d4f8c" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
  </samlp:Status>
</samlp:LogoutResponse>
```

### <a name="logoutresponse"></a>LogoutResponse

Azure AD définit les `ID`, `Version` et `IssueInstant` les valeurs dans le `LogoutResponse` élément. Il définit également la `InResponseTo` élément la valeur de la `ID` attribut de la `LogoutRequest` qui provoquée à la réponse.

### <a name="issuer"></a>Émetteur

Annonce Azure définit cette valeur à `https://login.microsoftonline.com/<TenantIdGUID>/` où <TenantIdGUID> est l’ID de client du locataire AD Azure.

Pour évaluer la valeur de la `Issuer` élément, utilisez la valeur de l' **URI d’ID d’application** fournie au cours de l’inscription de l’application.

### <a name="status"></a>État

Azure AD utilise le `StatusCode` élément dans le `Status` élément pour indiquer la réussite ou l’échec de la déconnexion. En cas d’échec de la tentative de déconnexion, les `StatusCode` élément peut également contenir des messages d’erreur personnalisés.
