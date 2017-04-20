<properties
    pageTitle="Référence au protocole Azure AD SAML | Microsoft Azure"
    description="Cet article fournit une vue d’ensemble des profils de l’authentification unique et unique Sign-Out SAML dans Azure Active Directory."
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
    ms.date="06/23/2016"
    ms.author="priyamo"/>


# <a name="how-azure-active-directory-uses-the-saml-protocol"></a>Comment Azure Active Directory utilise le protocole SAML

Azure protocole utilise la SAML 2.0 de Active Directory (AD Azure) pour activer les applications fournir une expérience d’ouverture de session unique pour leurs utilisateurs. Les profils de [L’ouverture de session unique](active-directory-single-sign-on-protocol-reference.md) et [simple déconnexion](active-directory-single-sign-out-protocol-reference.md) SAML de publicité Azure expliquent comment les assertions SAML, des protocoles et des liaisons sont utilisés dans le service fournisseur d’identité.

Protocole de SAML requiert que le fournisseur d’identité (Azure AD) et le fournisseur de service (l’application) pour échanger des informations à leur sujet.

Lorsqu’une application est enregistrée avec AD Azure, le développeur de l’application enregistre les informations relatives à la fédération avec Azure AD. Cela inclut **l’URI de redirection** et les **URI de métadonnées** de l’application.

Annonce Azure utilise l' **URI de métadonnées** du service cloud pour récupérer la clé de signature et de l’URI du service de cloud de déconnexion. Si l’application ne prend pas en charge une URI de métadonnées, le développeur doit contacter le support technique de Microsoft afin de fournir l’URI de la déconnexion et la clé de signature.

Azure Active Directory expose les clients spécifiques et communes (clients indépendants) unique et d’authentification unique déconnexion points de terminaison. Ces URL représente les emplacements adressables par : ils ne sont pas simplement un identificateurs--pour accéder au point de terminaison pour lire les métadonnées.

 - Le point de terminaison client spécifique se trouve dans `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`.  Le <TenantDomainName> espace réservé représente un nom de domaine enregistré ou le GUID de TenantID d’un locataire AD Azure. Par exemple, les métadonnées de fédération du locataire contoso.com sont à : https://login.microsoftonline.com/contoso.com/FederationMetadata/2007-06/FederationMetadata.xml

- Le point de terminaison client indépendant se trouve dans `https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml`. Cette adresse de point de terminaison **commun** s’affiche, au lieu d’un identificateur ou un nom de domaine de clients

Pour plus d’informations sur les documents de métadonnées de fédération publie des annonces d’Azure, consultez [Métadonnées de fédération](active-directory-federation-metadata.md).
