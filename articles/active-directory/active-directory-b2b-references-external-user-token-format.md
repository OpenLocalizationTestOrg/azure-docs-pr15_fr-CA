<properties
   pageTitle="Format du jeton utilisateur externe pour l’aperçu de collaboration Azure Active Directory B2B | Microsoft Azure"
   description="Azure B2B Directory Active prend en charge vos relations intersociétés en activant les partenaires commerciaux de manière sélective accéder à vos applications d’entreprise"
   services="active-directory"
   documentationCenter=""
   authors="viv-liu"
   manager="cliffdi"
   editor=""
   tags=""/>

<tags
   ms.service="active-directory"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="na"
   ms.date="05/09/2016"
   ms.author="viviali"/>

# <a name="azure-ad-b2b-collaboration-preview-external-user-token-format"></a>Aperçu de collaboration B2B de publicité Azure : format du jeton utilisateur externe

Les demandes pour une publicité Azure standard jeton sont décrites dans l’article de la [prise en charge de jeton et les Types de revendications](active-directory-token-and-claims.md) sur azure.microsoft.com.

Les revendications sont différentes pour un utilisateur externe authentifié de collaboration B2B sont les suivantes :<br/>
- **OID :** l’ID d’objet à partir de clients de la ressource<br/>
- **TID**: client ID à partir de clients de la ressource<br/>
- **Émetteur**: c’est le client de la ressource<br/>
- **IDP**: il s’agit de la base clients de l’utilisateur<br/>
- **AltSecId**: il s’agit de l’ID de sécurité de remplacement, qui sont opaque pour vous<br/>

## <a name="related-articles"></a>Articles connexes
Parcourir nos autres articles sur Azure AD B2B collaboration :

- [Quelle est la collaboration d’Azure AD B2B ?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Mode de fonctionnement](active-directory-b2b-how-it-works.md)
- [Procédure pas à pas détaillé](active-directory-b2b-detailed-walkthrough.md)
- [Référence de format de fichier CSV](active-directory-b2b-references-csv-file-format.md)
- [Modifications de l’attribut objet utilisateur externe](active-directory-b2b-references-external-user-object-attribute-changes.md)
- [Limites d’aperçu en cours](active-directory-b2b-current-preview-limitations.md)
- [Index des articles pour la gestion d’Application dans Azure Active Directory](active-directory-apps-index.md)
