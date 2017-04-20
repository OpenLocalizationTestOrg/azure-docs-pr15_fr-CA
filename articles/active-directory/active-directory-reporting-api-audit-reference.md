<properties
    pageTitle="Audit d’Active Directory Azure référence sur l’API | Microsoft Azure"
    description="La mise en route avec l’API de vérification Azure Active Directory"
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
    ms.date="10/24/2016"
    ms.author="dhanyahk;markvi"/>

# <a name="azure-active-directory-audit-api-reference"></a>Audit d’Active Directory Azure référence sur l’API

Cette rubrique fait partie d’un ensemble de rubriques sur Azure Active Directory API de création de rapports.  
Azure reporting d’Active Directory vous fournit une API qui vous permet d’accéder à des données d’audit à l’aide de code ou les outils associés.
La portée de cette rubrique est de vous fournir des informations de référence sur l' **API de l’audit**.

Voir :

- [Les journaux d’audit](active-directory-reporting-azure-portal.md#audit-logs) pour plus d’informations
- Pour plus d’informations sur l’API de création de rapports, [mise en route avec l’API de création de rapports de répertoire Azure Active](active-directory-reporting-api-getting-started.md) .

Vous avez des questions, des problèmes ou des commentaires, veuillez contacter [DAS Reporting aide](mailto:aadreportinghelp@microsoft.com).


## <a name="who-can-access-the-data"></a>Qui peut accéder aux données ?

- Utilisateurs du rôle administrateur de la sécurité ou de lecture de sécurité

- Administrateurs globaux

- N’importe quelle application qui a l’autorisation d’accéder à l’API (autorisation de l’application possible de configurer uniquement en fonction d’autorisation de l’administrateur Global)



## <a name="prerequisites"></a>Conditions préalables

Pour accéder à ce rapport par le biais de l’API de création de rapports, vous devez disposer :

- Un [Azure Active Directory libres ou édition mieux](active-directory-editions.md)

- Terminer les [conditions requises pour accéder à l’annonce Azure reporting API](active-directory-reporting-api-prerequisites.md). 
 

##<a name="accessing-the-api"></a>Accès à l’API

Vous pouvez soit accéder à cette API par le biais de l' [Explorateur de graphique](https://graphexplorer2.cloudapp.net) , ou par programme, par exemple, utilisation de PowerShell. Pour interpréter correctement la syntaxe de filtre OData utilisée dans les appels de DAS graphique reste PowerShell, vous devez utiliser le backtick (alias : accent grave) caractère permet de « désactiver » le caractère $. Le caractère backtick sert de [caractère d’échappement de PowerShell](https://technet.microsoft.com/library/hh847755.aspx), permettant de PowerShell effectuer une interprétation littérale du caractère $ et éviter la confusion entre elle sous la forme d’un nom de variable PowerShell (ie : $filter).

L’objectif de cette rubrique est sur le graphique de l’Explorateur. Pour obtenir un exemple de PowerShell, consultez ce [script PowerShell](active-directory-reporting-api-audit-samples.md#powershell-script).

 
 

## <a name="api-endpoint"></a>API de point de terminaison


Vous pouvez accéder à cette interface API à l’aide de l’URI suivant :  

    https://graph.windows.net/contoso.com/activities/audit?api-version=beta

Il n’y a aucune limite sur le nombre d’enregistrements renvoyés par l’API d’audit Azure AD (à l’aide de la pagination d’OData).
Pour les limites de conservation de données de rapports, consultez [Rapports de rétention](active-directory-reporting-retention.md).

Cet appel renvoie les données dans des lots. Chaque lot comporte un maximum de 1000 enregistrements.  
Pour obtenir un lot d’enregistrements suivant, cliquez sur le lien suivant. Obtenir les informations de skiptoken provenant du premier jeu d’enregistrements renvoyés. Le jeton d’ignorer est à la fin du résultat défini.  

    https://graph.windows.net/contoso.com/activities/audit?api-version=beta&%24skiptoken=-1339686058




## <a name="supported-filters"></a>Filtres pris en charge

Vous pouvez réduire le nombre d’enregistrements renvoyés par une API appeler sous forme d’un filtre.  
Pour l’API de connexion à des données, les filtres suivants sont pris en charge :

- **$top =\<nombre d’enregistrements à renvoyer\> ** - pour limiter le nombre d’enregistrements renvoyés. Il s’agit d’une opération coûteuse. Vous ne devez pas utiliser ce filtre si vous souhaitez renvoyer des milliers d’objets.     
- **$filter =\<votre instruction de filtrage\> ** - permet de spécifier le type d’enregistrements qui vous intéressent, sur la base des champs de filtre pris en charge,



## <a name="supported-filter-fields-and-operators"></a>Les opérateurs et les champs de filtre pris en charge

Pour spécifier le type d’enregistrements qui que vous intéressent, vous pouvez créer une instruction de filtrage qui peut contenir une ou une combinaison des champs de filtre suivants :

- [activityDate](#activitydate) - définit une date ou une plage de dates
- [activityType](#activitytype) - définit le type d’une activité
- [activité](#activity) - définit l’activité en tant que chaîne  
- [nom de l’acteur /](#actorname) - définit l’acteur sous forme de nom de l’acteur
- [acteur/objectid](#actorobjectid) - définit l’acteur sous forme d’ID de l’acteur   
- [acteur/upn](#actorupn) - définit l’acteur dans le formulaire principe de l’acteur nom d’utilisateur (UPN) 
- [nom de la cible](#targetname) - définit la cible sous forme de nom de l’acteur
- [cible/objectid](#targetobjectid) - définit la cible dans le formulaire de l’ID cible  
- [cible/upn](#targetupn) - définit l’acteur dans le formulaire principe de l’acteur nom d’utilisateur (UPN)   




----------

### <a name="activitydate"></a>activityDate

**Les opérateurs pris en charge**: eq, ge, le, gt, lt

**Exemple**:

    $filter=tdomain + 'activities/audit?api-version=beta&`$filter=eventTime gt ' + $7daysago    

**Notes**:

DateTime doit être au format UTC

----------

### <a name="activitytype"></a>activityType

**Les opérateurs pris en charge**: eq

**Exemple**:

    $filter=activityType eq 'User'  

**Notes**:

respect de la casse

----------

### <a name="activity"></a>activité

**Les opérateurs pris en charge**: eq, contient, startsWith

**Exemple**:

    $filter=activity eq 'Add application' or contains(activity, 'Application') or startsWith(activity, 'Add')   

**Notes**:

respect de la casse

----------

### <a name="actorname"></a>/ nom de l’acteur

**Les opérateurs pris en charge**: eq, contient, startsWith

**Exemple**:

    $filter=actor/name eq 'test' or contains(actor/name, 'test') or startswith(actor/name, 'test')  

**Notes**:

non-respect de la casse

    

----------
### <a name="actorobjectid"></a>acteur/objectId.

**Les opérateurs pris en charge**: eq

**Exemple**:

    $filter=actor/objectId eq 'e8096343-86a2-4384-b43a-ebfdb17600ba'    

----------
### <a name="targetname"></a>nom de la cible

**Les opérateurs pris en charge**: eq, contient, startsWith

**Exemple**:

    $filter=targets/any(t: t/name eq 'some name')   

**Notes**:

Non-respect de la casse

----------

### <a name="targetupn"></a>cible/upn

**Les opérateurs pris en charge**: eq, startsWith

**Exemple**:

    $filter=targets/any(t: startswith(t/Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.TargetResourceUserEntity/userPrincipalName,'abc')) 

**Notes**:

- Non-respect de la casse
- Vous devez ajouter l’espace de noms complet lors de l’interrogation de Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.TargetResourceUserEntity

----------

### <a name="targetobjectid"></a>cible/objectId.

**Les opérateurs pris en charge**: eq

**Exemple**:

    $filter=targets/any(t: t/objectId eq 'e8096343-86a2-4384-b43a-ebfdb17600ba')    

----------

### <a name="actorupn"></a>acteur/upn

**Les opérateurs pris en charge**: eq, startsWith

**Exemple**:

    $filter=startswith(actor/Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.ActorUserEntity/userPrincipalName,'abc')  

**Notes**:

- Non-respect de la casse 
- Vous devez ajouter l’espace de noms complet lors de l’interrogation de Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.ActorUserEntity

----------




## <a name="next-steps"></a>Étapes suivantes

- Vous voulez voir des exemples d’activités système filtré ? Consultez les [exemples d’API l’audit Azure Active Directory](active-directory-reporting-api-audit-samples.md).

- Vous souhaitez en savoir plus sur la publicité Azure reporting API ? Reportez-vous à la section [mise en route avec l’API de création de rapports de répertoire Azure Active](active-directory-reporting-api-getting-started.md).