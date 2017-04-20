<properties
    pageTitle="Rapport d’activité de connexion Active Directory référence sur l’API Azure | Microsoft Azure"
    description="Référence de l’API de rapport d’activité de connexion Azure Active Directory"
    services="active-directory"
    documentationCenter=""
    authors="dhanyahk"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="09/25/2016"
    ms.author="dhanyahk;markvi"/>

# <a name="azure-active-directory-sign-in-activity-report-api-reference"></a>Rapport d’activité de connexion Active Directory Azure référence sur l’API


Cette rubrique fait partie d’un ensemble de rubriques sur Azure Active Directory API de création de rapports.  
Rapports de publicité Azure vous offre une API qui vous permet d’accéder aux données de rapport d’activité de connexion à l’aide de code ou les outils associés.
La portée de cette rubrique est de vous fournir des informations de référence sur l' **API de rapport d’activité de signe**.

Voir :

- [Reconnectez-vous activités](active-directory-reporting-azure-portal.md#sign-in-activities) pour plus d’informations
- Pour plus d’informations sur l’API de création de rapports, [mise en route avec l’API de création de rapports de répertoire Azure Active](active-directory-reporting-api-getting-started.md) .

Vous avez des questions, des problèmes ou des commentaires, veuillez contacter [DAS Reporting aide](mailto:aadreportinghelp@microsoft.com).



## <a name="who-can-access-the-api-data"></a>Qui peut accéder aux données API ?

- Utilisateurs du rôle administrateur de la sécurité ou de lecture de sécurité

- Administrateurs globaux

- N’importe quelle application qui a l’autorisation d’accéder à l’API (autorisation de l’application possible de configurer uniquement en fonction d’autorisation de l’administrateur Global)



## <a name="prerequisites"></a>Conditions préalables

Pour accéder à ce rapport par le biais de l’API de création de rapports, vous devez disposer :

- Un [Azure Active Directory prime P1 ou P2 edition](active-directory-editions.md)

- Terminer les [conditions requises pour accéder à l’annonce Azure reporting API](active-directory-reporting-api-prerequisites.md). 


##<a name="accessing-the-api"></a>Accès à l’API

Vous pouvez soit accéder à cette API par le biais de l' [Explorateur de graphique](https://graphexplorer2.cloudapp.net) , ou par programme, par exemple, utilisation de PowerShell. Pour interpréter correctement la syntaxe de filtre OData utilisée dans les appels de DAS graphique reste PowerShell, vous devez utiliser le backtick (alias : accent grave) caractère permet de « désactiver » le caractère $. Le caractère backtick sert de [caractère d’échappement de PowerShell](https://technet.microsoft.com/library/hh847755.aspx), permettant de PowerShell effectuer une interprétation littérale du caractère $ et éviter la confusion entre elle sous la forme d’un nom de variable PowerShell (ie : $filter).

L’objectif de cette rubrique est sur le graphique de l’Explorateur. Pour obtenir un exemple de PowerShell, consultez ce [script PowerShell](active-directory-reporting-api-sign-in-activity-samples.md#powershell-script).


## <a name="api-endpoint"></a>API de point de terminaison

Vous pouvez accéder à cette interface API à l’aide de l’URI de base suivant :  
    
    https://graph.windows.net/contoso.com/activities/signinEvents?api-version=beta  



En raison du volume de données, cette API a une limite d’un million d’enregistrements retournés. 

Cet appel renvoie les données dans des lots. Chaque lot comporte un maximum de 1000 enregistrements.  
Pour obtenir un lot d’enregistrements suivant, cliquez sur le lien suivant. Obtenir les informations de [skiptoken](https://msdn.microsoft.com/library/dd942121.aspx) provenant du premier jeu d’enregistrements renvoyés. Le jeton d’ignorer est à la fin du résultat défini.  

    https://graph.windows.net/$tenantdomain/activities/signinEvents?api-version=beta&%24skiptoken=-1339686058


## <a name="supported-filters"></a>Filtres pris en charge

Vous pouvez réduire le nombre d’enregistrements renvoyés par une API appeler sous forme d’un filtre.  
Pour l’API de connexion à des données, les filtres suivants sont pris en charge :

- **$top =\<nombre d’enregistrements à renvoyer\> ** - pour limiter le nombre d’enregistrements renvoyés. Il s’agit d’une opération coûteuse. Vous ne devez pas utiliser ce filtre si vous souhaitez renvoyer des milliers d’objets.  
- **$filter =\<votre instruction de filtrage\> ** - permet de spécifier le type d’enregistrements qui vous intéressent, sur la base des champs de filtre pris en charge,



## <a name="supported-filter-fields-and-operators"></a>Les opérateurs et les champs de filtre pris en charge

Pour spécifier le type d’enregistrements qui que vous intéressent, vous pouvez créer une instruction de filtrage qui peut contenir une ou une combinaison des champs de filtre suivants :

- [signinDateTime](#signindatetime) - définit une date ou une plage de dates

- [userId](#userid) - définit une spécifiques à l’utilisateur en fonction des ID de. l’utilisateur

- [userPrincipalName](#userprincipalname) - définit une spécifiques à l’utilisateur en fonction utilisateur principaux (UPN) de l’utilisateur

- [appId](#appid) - définit spécifiques à une application en fonction des ID de l’application

- [appDisplayName](#appdisplayname) - définit spécifiques à une application en fonction de nom d’affichage de l’application

- [loginStatus](#loginStatus) - définit l’état de la connexion (réussite / échec)


> [AZURE.NOTE] Lorsque vous utilisez l’Explorateur de graphe, vous la nécessité d’utiliser la casse appropriée pour chaque lettre est vos champs de filtre.


Pour limiter l’étendue des données retournées, vous pouvez créer des combinaisons de la prise en charge des filtres et champs de filtre. Par exemple, l’instruction suivante renvoie les 10 premiers enregistrements des 2016 de 6 juillet 2016 de 1er juillet :

    https://graph.windows.net/contoso.com/activities/signinEvents?api-version=beta&$top=10&$filter=signinDateTime+ge+2016-07-01T17:05:21Z+and+signinDateTime+le+2016-07-07T00:00:00Z


----------

### <a name="signindatetime"></a>signinDateTime

**Les opérateurs pris en charge**: eq, ge, le, gt, lt

**Exemple**:

À l’aide d’une date spécifique

    $filter=signinDateTime+eq+2016-04-25T23:59:00Z  



À l’aide d’une plage de dates    

    $filter=signinDateTime+ge+2016-07-01T17:05:21Z+and+signinDateTime+le+2016-07-07T17:05:21Z


**Notes**:

Le paramètre datetime doit être au format UTC 


----------

### <a name="userid"></a>ID utilisateur

**Les opérateurs pris en charge**: eq

**Exemple**:

    $filter=userId+eq+’00000000-0000-0000-0000-000000000000’

**Notes**:

La valeur d’userId est une valeur de chaîne



----------

### <a name="userprincipalname"></a>userPrincipalName

**Les opérateurs pris en charge**: eq

**Exemple**:

    $filter=userPrincipalName+eq+'audrey.oliver@wingtiptoysonline.com' 


**Notes**:

La valeur d’userPrincipalName est une valeur de chaîne

----------

### <a name="appid"></a>appId

**Les opérateurs pris en charge**: eq

**Exemple**:

    $filter=appId+eq+’00000000-0000-0000-0000-000000000000’



**Notes**:

La valeur de l’identificateur est une valeur de chaîne

----------


### <a name="appdisplayname"></a>appDisplayName

**Les opérateurs pris en charge**: eq

**Exemple**:

    $filter=appDisplayName+eq+'Azure+Portal' 


**Notes**:

La valeur d’appDisplayName est une valeur de chaîne

----------

### <a name="loginstatus"></a>loginStatus

**Les opérateurs pris en charge**: eq

**Exemple**:

    $filter=loginStatus+eq+'1'  


**Notes**:

Il existe deux options pour le loginStatus : 0 - Réussite 1 - Échec

----------



## <a name="next-steps"></a>Étapes suivantes

- Vous voulez voir des exemples pour les activités de connexion filtrées ? Consultez les [exemples d’API de rapport Azure Active Directory activité de connexion](active-directory-reporting-api-sign-in-activity-samples.md).

- Vous souhaitez en savoir plus sur la publicité Azure reporting API ? Reportez-vous à la section [mise en route avec l’API de création de rapports de répertoire Azure Active](active-directory-reporting-api-getting-started.md).