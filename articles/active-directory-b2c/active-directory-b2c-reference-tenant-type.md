<properties
    pageTitle="Azure B2C de répertoire actif : Utilisateurs de Production à l’échelle par rapport à l’aperçu B2C | Microsoft Azure"
    description="Une rubrique sur les types de clients d’Azure Active Directory B2C"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="mbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/30/2016"
    ms.author="swkrish"/>

# <a name="azure-active-directory-b2c-production-scale-vs-preview-b2c-tenants"></a>Azure B2C de répertoire actif : Utilisateurs de Production à l’échelle par rapport à l’aperçu B2C

Si vous envisagez d’écrire une application de production sur Azure Active Directory (AD Azure) B2C, vous devez être certain d’avoir le locataire droit « type » mise en ligne sur. Pour voir ce que vous avez, procédez comme suit pour [accéder à la lame de fonctionnalités B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) sur le portail Azure et regardez sous **type de client**.

## <a name="summary"></a>Résumé

Azure AD B2C prend en charge les applications de production uniquement sur B2C de **Production à l’échelle** des utilisateurs en Amérique du Nord.

| Type de client | Pays/régions | Généralement disponibles ? |
| ----------- | -------------- | --------------------- |
| **Clients à l’échelle de la production** | Pays/régions Amérique du Nord | Oui |
| **Clients à l’échelle de la production** | Tous les pays à l’exception d’Amérique du Nord | N° |
| **Clients de l’aperçu** | Tous les pays/régions | N° |

> [AZURE.NOTE]
Azure locataires AD B2C (pour les consommateurs) sont actuellement indisponibles dans quelques pays ou régions où les locataires Azure AD (pour les employés) sont disponibles. Lisez les sections suivantes pour plus de détails.

## <a name="production-scale-b2c-tenant-in-north-america"></a>Clients B2C de production à l’échelle en Amérique du Nord

Si vous avez [créé vos clients B2C](active-directory-b2c-get-started.md) en Amérique du Nord, par exemple, dans un des pays ou des régions suivantes : United États, Canada, Costa Rica, République dominicaine, El Salvador, Guatemala, Mexique, Panama, Porto Rico et Trinité- et -Tobago et le **type de clients** sur votre interface utilisateur Admin de B2C indique la **Production à l’échelle**, votre client peut être utilisé pour les applications de production.

> [AZURE.NOTE]
Locataires de production à l’échelle sont capables de mise à l’échelle à 100 s des millions d’identités de consommateurs par les clients.

![Capture d’écran d’un client à l’échelle de la production](./media/active-directory-b2c-reference-tenant-type/production-scale-b2c-tenant.png)

## <a name="preview-b2c-tenant-in-any-countryregion"></a>Afficher un aperçu des clients B2C dans n’importe quel pays/région

Si vous aviez créé un locataire B2C au cours de la période d’aperçu de B2C Azure AD, il est probable que vos **clients tapez** indique à **Afficher un aperçu des clients**. Si c’est le cas, vous devez utiliser vos clients uniquement pour le développement et de test et non pour les applications de production.

> [AZURE.IMPORTANT]
Il n’y a aucun chemin d’accès de migration à partir d’un aperçu B2C clients pour un client B2C de production à l’échelle. Notez qu’il existe des connus des problèmes lorsque vous supprimez un locataire aperçu B2C et recréez un locataire B2C de production à l’échelle avec le même nom de domaine. Vous devez créer un locataire B2C de production à l’échelle avec un nom de domaine différent.

![Capture d’écran d’un client de l’aperçu](./media/active-directory-b2c-reference-tenant-type/preview-b2c-tenant.png)

## <a name="production-scale-b2c-tenant-outside-of-north-america"></a>Clients de B2C à l’échelle de la production en dehors de l’Amérique du Nord

Azure B2C d’annonce n’est actuellement pas généralement disponible en dehors de l’Amérique du Nord. Cependant vous pouvez créer et utiliser des locataires de l’échelle de la production, de développement et de test à des fins, dans un des pays ou des régions suivantes : Algérie, Autriche, Azerbaïdjan, Bahreïn, Belarus, Belgique, Bulgarie, Croatie, Chypre, République tchèque, Danemark, Égypte, Estonie, Finlande, France, Allemagne, Grèce, Hongrie, Islande, Irlande, Israël, Italie, Jordanie, Kazakhstan, Kenya, Koweït, la Lettonie, Liban, Liechtenstein, Lituania, Luxembourg, Macédoine Macédonien, Malte, Monténégro, Maroc, pays-bas, Nigeria, Norvège , Oman, Pakistan, Pologne, Portugal, Qatar, Roumanie, Russie, Arabie saoudite, Serbie, Slovaquie, Slovénie, Afrique du Sud, Espagne, Suède, Suisse, Tunisie, Turquie, Ukraine, États-Unis Émirats Arabes Unis et le Royaume-Uni.

Une fois, Azure AD B2C annonce la disponibilité générale d’au-dessus des pays ou régions, vous pouvez continuer à utiliser ces locataires de production à l’échelle et la mise en service avec vos applications de production sans perte de données.

## <a name="availability-of-b2c-tenants"></a>Disponibilité des locataires de B2C

Les locataires B2C sont actuellement indisponibles dans le pays suivants : Afghanistan, Argentine, Australie, Brésil, Chili, Colombie, Équateur, RAS de Hong Kong, Inde, Indonésie, Iraq, Japon, Corée, Malaisie, Nouvelle-Zélande, Paraguay, Pérou, Philippines, Singapour, Sri Lanka, Taïwan, Thaïlande, Uruguay et Venezuela. Nous prévoyons de les inclure dans le futur.
