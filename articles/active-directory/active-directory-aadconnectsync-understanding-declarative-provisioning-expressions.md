<properties
    pageTitle="Azure Connect de AD sync : compréhension des Expressions déclaratives provisionnement | Microsoft Azure"
    description="Explique les mise en service des expressions déclaratives."
    services="active-directory"
    documentationCenter=""
    authors="andkjell"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/31/2016"
    ms.author="markusvi;andkjell"/>


# <a name="azure-ad-connect-sync-understanding-declarative-provisioning-expressions"></a>Azure Connect de AD sync : compréhension des Expressions déclaratives mise en service
Azure sync de connexion d’Active Directory s’appuie sur la mise en service déclaratif introduite dans Forefront Identity Manager 2010. Il vous permet d’implémenter votre logique intégration complète d’identité sans avoir à écrire du code compilé.

Une partie essentielle de provisionnement déclarative est le langage des expressions utilisé dans les flux d’attributs. La langue utilisée est un sous-ensemble de Microsoft® Visual Basic® pour Applications (VBA). Ce langage est utilisé dans Microsoft Office et les utilisateurs ayant une expérience de VBScript reconnaît également. Le langage des expressions déclaratives provisionnement est uniquement à l’aide de fonctions et n’est pas un langage structuré. Il n’y a aucune méthode ou les instructions. Fonctions sont imbriquées à la place pour le déroulement du programme express.

Pour plus d’informations, reportez-vous à la section [Bienvenue dans le Visual Basic pour Applications de référence du langage pour Office 2013](https://msdn.microsoft.com/library/gg264383.aspx).

Les attributs sont fortement typées. Une fonction n’accepte que les attributs du type correct. Il est également sensible à la casse. Les noms de fonctions et de noms de l’attribut doivent avoir une casse appropriée ou une erreur est levée.

## <a name="language-definitions-and-identifiers"></a>Définitions de la langue et les identificateurs

- Les fonctions ont un nom suivi des arguments entre parenthèses : nomfonction (argument 1, argument N).
- Les attributs sont identifiés par des crochets : [Nom_attribut]
- Les paramètres sont identifiés par deux signes de pourcentage : ParameterName %
- Les constantes de chaîne sont entourés de guillemets : par exemple, « Contoso » (Remarque : doit utiliser des guillemets « » et pas de guillemets « »)
- Les valeurs numériques sont exprimées sans guillemets et doit être décimale. Les valeurs hexadécimales sont précédés de & H. Par exemple, 98052, & HFF
- Les valeurs booléennes sont exprimées avec les constantes : vrai, faux.
- Littéraux et des constantes intégrées sont exprimées avec uniquement le nom : NULL, CRLF, IgnoreThisFlow

### <a name="functions"></a>Fonctions
Provisionnement déclarative d’utilise de nombreuses fonctions pour activer la possibilité de transformer des valeurs d’attribut. Ces fonctions peuvent être imbriquées afin que le résultat d’une fonction est passé à une autre fonction.

`Function1(Function2(Function3()))`

Vous trouverez la liste complète des fonctions dans la [référence de la fonction](active-directory-aadconnectsync-functions-reference.md).

### <a name="parameters"></a>Paramètres
Un paramètre est défini par un connecteur, ou par un administrateur à l’aide de PowerShell. Généralement, les paramètres contiennent des valeurs qui diffèrent d’un système, par exemple le nom du domaine de l’utilisateur se trouve dans. Ces paramètres peuvent être utilisés dans les flux d’attributs.

Le connecteur Active Directory fourni les paramètres suivants pour les règles de synchronisation entrants :

| Nom du paramètre | Commentaire |
| --- | --- |
| Domain.Netbios | Format NetBIOS du domaine en cours d’importation, par exemple FABRIKAMSALES |
| Domain.FQDN | Format de nom de domaine complet du domaine en cours d’importation, par exemple ventes.fabrikam.com |
| Domain.LDAP | Format LDAP du domaine en cours d’importation, par exemple DC = sales, DC = fabrikam, DC = com |
| Forest.Netbios | Format NetBIOS du nom de la forêt en cours d’importation, par exemple FABRIKAMCORP |
| Forest.FQDN | Format de nom de domaine complet du nom de la forêt en cours d’importation, par exemple fabrikam.com |
| Forest.LDAP | Format LDAP portant le nom de forêt est en cours d’importation, par exemple DC = fabrikam, DC = com |

Le système fournit le paramètre suivant, qui est utilisé pour obtenir l’identificateur du connecteur en cours d’exécution :  
`Connector.ID`

Voici un exemple qui remplit le domaine d’attribut du métaverse par le nom netbios du domaine où se trouve l’utilisateur :  
`domain` <- `%Domain.Netbios%`

### <a name="operators"></a>Opérateurs
Les opérateurs suivants peuvent être utilisés :

- **Comparaison**: <, < =, <>, =, >, > =
- **Mathématiques**: +, -, \*, -
- **Chaîne**: & (concaténation)
- **Logique**: & & (et), || (ou)
- **Ordre d’évaluation**:)

Les opérateurs sont évalués de gauche à droite et ont la même priorité d’évaluation. Autrement dit, les \* (multiplicateur) n’est pas évaluée avant - (soustraction). 2\*(5 + 3) n’est pas le même que 2\*5 + 3. Les crochets () sont utilisés pour modifier l’ordre d’évaluation lors de la gauche pour l’ordre d’évaluation de droite n’est pas appropriée.

## <a name="multi-valued-attributes"></a>Attributs à valeurs multiples
Les fonctions peuvent fonctionner sur les attributs à valeur unique et à valeurs multiples. Pour les attributs à valeurs multiples, la fonction opère sur toutes les valeurs et s’applique de la même fonction pour chaque valeur.

Par exemple :  
`Trim([proxyAddresses])`Effectuer un ajustement de toutes les valeurs de l’attribut proxyAddress.  
`Word([proxyAddresses],1,"@") & "@contoso.com"`Pour chaque valeur avec une @-sign, remplacer le domaine par @contoso.com.  
`IIF(InStr([proxyAddresses],"SIP:")=1,NULL,[proxyAddresses])`Recherchez l’adresse SIP et le supprimer à partir des valeurs.

## <a name="next-steps"></a>Étapes suivantes

- Apprenez-en plus sur le modèle de configuration de [Mise en service déclaratif de présentation](active-directory-aadconnectsync-understanding-declarative-provisioning.md).
- Voir comment déclarative le provisionnement est utilisé out-of-box pour [comprendre la configuration par défaut](active-directory-aadconnectsync-understanding-default-configuration.md).
- Voir comment apporter une modification pratique à l’aide de la mise en service de manière déclarative comment [apporter une modification à la configuration par défaut](active-directory-aadconnectsync-change-the-configuration.md).

**Rubriques de vue d’ensemble**

- [Azure Connect de AD sync : comprendre et personnaliser la synchronisation](active-directory-aadconnectsync-whatis.md)
- [Intégration des identités sur site à Azure Active Directory](active-directory-aadconnect.md)

**Rubriques de référence**

- [Azure Connect de AD sync : référence des fonctions](active-directory-aadconnectsync-functions-reference.md)
