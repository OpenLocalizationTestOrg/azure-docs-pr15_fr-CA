<properties
   pageTitle="Durée de vie des jetons configurable dans Azure Active Directory | Microsoft Azure"
   description="Cette fonctionnalité est utilisée par les administrateurs et les développeurs pour spécifier les durées de vie des jetons émis par AD Azure."
   services="active-directory"
   documentationCenter=""
   authors="billmath"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"  
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="10/06/2016"
   ms.author="billmath"/>


# <a name="configurable-token-lifetimes-in-azure-active-directory-public-preview"></a>Durée de vie des jetons configurable dans Azure Active Directory (version d’évaluation)

>[AZURE.NOTE]
>Cette fonctionnalité est en cours d’évaluation.  Soyez prêt à rétablir ou supprimer toutes les modifications.  Nous mettons cette fonctionnalité pour tous les utilisateurs d’essayer au cours de la présentation publique, cependant, certains aspects peuvent nécessiter un [abonnement Azure AD Premium](active-directory-get-started-premium.md) une fois généralement disponible.


## <a name="introduction"></a>Introduction
Cette fonctionnalité est utilisée par les administrateurs et les développeurs pour spécifier les durées de vie des jetons émis par AD Azure. Durée de vie de jeton peut être configurée pour toutes les applications dans un client, pour une application de plusieurs utilisateurs ou pour un Principal de Service spécifique dans un client.

Dans AD Azure, un objet de stratégie représente un ensemble de règles appliquées à des applications individuelles ou toutes les applications d’un client.  Chaque type de stratégie a une structure unique avec un ensemble de propriétés qui sont ensuite appliquées aux objets auxquels ils sont affectés.

Une stratégie peut être désignée comme la valeur par défaut pour un client. Cette stratégie prend ensuite l’effet sur n’importe quelle application qui réside à l’intérieur, client tant qu’il n’est pas substituée par une stratégie avec une priorité plus élevée. Les stratégies peuvent également être attribuées à des applications spécifiques. L’ordre de priorité varie en fonction du type de stratégie.

Stratégies de jeton de durée de vie peuvent être configurés pour actualiser les jetons, les jetons d’accès, les jetons de session et jetons d’ID.


### <a name="access-tokens"></a>Jetons d’accès
Un jeton d’accès est utilisé par un client pour accéder à une ressource protégée. Un jeton d’accès peut uniquement être utilisé pour une combinaison spécifique de l’utilisateur, le client et ressources. Les jetons d’accès ne peut pas être révoqués et sont valables jusqu'à leur expiration. Un acteur malveillant qui a obtenu un jeton d’accès peut l’utiliser pour la mesure de sa durée de vie.  Ajustement vie de jeton d’accès est un compromis entre l’amélioration des performances du système et d’augmenter la quantité de temps que le client conserve l’accès une fois le compte d’utilisateur est désactivé.  Améliorer les performances système sont obtenue en réduisant le nombre de fois qu’un client a besoin d’acquérir un jeton d’accès fraîches. 

### <a name="refresh-tokens"></a>Actualiser les jetons
Lorsqu’un client obtient un jeton d’accès pour accéder à une ressource protégée, il recevra un jeton d’actualisation et un jeton d’accès. Le jeton d’actualisation est utilisé pour obtenir des paires de jetons accès nouvelle et d’actualisation lors de l’expiration du jeton d’accès actuel. Actualiser les jetons sont liés à des combinaisons de l’utilisateur et le client. Ils peuvent être révoqués et leur validité est vérifiée chaque fois qu’ils sont utilisés.

Il est important de faire une distinction entre les clients confidentielles et publics. Clients confidentielles sont des applications qui sont en mesure de stocker en toute sécurité d’un mot de passe du client, ce qui leur permet de prouver que les demandes provenant de l’application cliente et pas un acteur malveillant. Comme ces flux est plus sécurisés, les durées de vie par défaut d’actualisation les jetons émis à ces flux sont plus élevés et ne peut pas être modifiés à l’aide de la stratégie.

En raison des limitations de l’environnement que les applications s’exécutent dans, les clients publics ne peuvent pas stocker en toute sécurité un mot de passe du client. Stratégies peuvent être définies sur les ressources pour empêcher l’obtention d’une nouvelle paire de jeton d’accès/refresh (Actualiser jeton inactif temps Max) de jetons d’actualisation à partir de clients publics antérieurs à une période spécifiée.  En outre, stratégies peuvent servir à définir une période au-delà de laquelle l’actualisation jetons ne sont plus acceptés (Actualiser jeton Max Age).  Ajustement vie de jeton d’actualisation permet de contrôler quand et à quelle fréquence l’utilisateur est nécessaire pour entrer de nouveau les informations d’identification au lieu d’être en mode silencieux de nouveau authentifié lors de l’utilisation d’une application client public.


### <a name="id-tokens"></a>Jetons d’ID
Jetons d’ID sont transmises aux sites web et les clients natifs et contiennent des informations sur un utilisateur. Un jeton d’ID est lié à une combinaison spécifique de l’utilisateur et le client. Jetons d’ID sont considérés comme valides jusqu'à l’expiration.  En règle générale, une application web correspond à un utilisateur de durée de vie de l’application pour la durée de vie du jeton d’ID de session émis pour l’utilisateur.  Vie de jeton ID ajustement permet vous permet de contrôler la fréquence à laquelle l’application web expiration de la session de l’application et demander à l’utilisateur de s’authentifier à nouveau à Azure annonce (soit en mode silencieux ou de manière interactive).

### <a name="single-sign-on-session-token"></a>Jeton de session d’ouverture de session unique
Lorsqu’un utilisateur s’authentifie avec AD Azure, une seule session d’ouverture de session est établie avec le navigateur de l’utilisateur et Azure AD.  La seule session Session jeton, sous la forme d’un cookie, représente cette session. Il est important de noter que le jeton de session d’authentification unique n’est pas lié à une application de ressources/client spécifique. Les jetons de session de l’authentification unique peuvent être révoqués et leur validité est vérifiée chaque fois qu’ils sont utilisés.

Il existe deux types de jetons de session de l’authentification unique. Les jetons de session persistant sont stockés par le navigateur et les jetons de session non persistante, les cookies persistants sont stockés sous forme de cookies de session (ils sont détruits lorsque le navigateur est fermé).

Jetons de session non persistants ont une durée de vie de 24 heures que jetons permanents ont une durée de vie de 180 jours. Chaque fois que le jeton de session d’authentification unique est utilisé dans sa période de validité, un autre de 24 heures ou de 180 jours, la période de validité est étendue. Si la session de l’authentification unique jeton n’est pas utilisé pendant sa période de validité, il est considéré comme expiré et sont ne sont plus acceptés. 

Stratégies peuvent servir à définir une période de temps après que la première session jeton a été délivré, au-delà de laquelle les jetons de session ne sont plus acceptés (Session jeton Max Age).  Ajustement vie de jeton de session vous permet de contrôler le moment et la fréquence à laquelle l’utilisateur doit entrer à nouveau les informations d’identification au lieu d’être authentifié en mode silencieux lors de l’utilisation d’une application web.

### <a name="token-lifetime-policy-properties"></a>Propriétés de stratégie de jeton de durée de vie
Une stratégie de jeton de durée de vie est un type d’objet de stratégie qui contient les règles du jeton de durée de vie.  Les propriétés de la stratégie sont utilisées pour contrôler la durée de vie de jeton spécifiée.  Si aucune stratégie n’est définie, le système applique la valeur de durée de vie par défaut.


### <a name="configurable-token-lifetime-properties"></a>Propriétés de durée de vie de jeton configurable
Propriété|Chaîne de la propriété stratégie|Affecte|Par défaut|Minimum|Maximum|
----- | ----- | ----- |----- | ----- | ----- |
Durée de vie de jeton accès|  AccessTokenLifetime|Les jetons d’accès, les jetons d’ID, les jetons de SAML2|1 heure|10 minutes|1 jour|
Actualisation Max jeton inactif|    MaxInactiveTime |Actualiser les jetons |14 jours|10 minutes|    90 jours|
Actualisation du seul facteur jeton Max Age|    MaxAgeSingleFactor  |Actualiser les jetons (pour tous les utilisateurs) |90 jours|10 minutes |Jusqu'à révoqué *|
Plusieurs facteur actualisation jeton Max Age| MaxAgeMultiFactor|  Actualiser les jetons (pour tous les utilisateurs)| 90 jours|10 minutes|Jusqu'à révoqué *|
Âge Max jeton de Session du seul facteur |MaxAgeSessionSingleFactor **    |Jetons de session (persistants et non persistants)| Jusqu'à révoqué   |10 minutes |Jusqu'à révoqué *|
Âge Max jeton de Session de plusieurs facteur| MaxAgeSessionMultiFactor ***|    Jetons de session (persistants et non persistants)| Jusqu'à révoqué|  10 minutes| Jusqu'à révoqué *



- * 365 jours est la longueur maximale explicite qui peut être définie pour ces attributs.
- ** Si MaxAgeSessionSingleFactor n’est pas définie, cette valeur prend la valeur MaxAgeSingleFactor. Si aucun paramètre n’est défini, la propriété prend la valeur par défaut (jusqu'à-révoqué).
- Si MaxAgeSessionMultiFactor n’est pas définie, cette valeur prend la valeur MaxAgeMultiFactor. Si aucun paramètre n’est défini, la propriété prend la valeur par défaut (jusqu'à-révoqué).

### <a name="exceptions"></a>Exceptions
Propriété|Affecte|Par défaut|
----- | ----- | ----- |
Actualisation jeton Max inactif (les utilisateurs fédérés avec les informations de révocation insuffisante)|Actualiser les jetons (émis pour les utilisateurs fédérés avec les informations de révocation insuffisante)|12 heures|
Actualisation Max jeton inactif (Clients confidentielles)| Actualiser les jetons (émis pour les Clients confidentielles)|90 jours|
Actualiser le jeton Max Age (émis pour les Clients confidentielles) |   Actualiser les jetons (émis pour les Clients confidentielles) |Jusqu'à révoqué

### <a name="priority-and-evaluation-of-policies"></a>Priorité et l’évaluation des stratégies

Jetons stratégies de durée de vie peuvent être créés et assignés à des applications spécifiques, les locataires et les entités de service. Cela signifie qu’il est possible que plusieurs stratégies à appliquer à une application spécifique. La stratégie de durée de vie de jeton qui prend effet suit ces règles :


- Si une stratégie est explicitement affectée à l’entité du service, il sera appliqué. 
- Si aucune stratégie n’est explicitement affectée à l’entité du service, une stratégie attribuée explicitement au locataire parent du service principal sera appliquée. 
- Si aucune stratégie n’est explicitement affecté au service principal ou le locataire, la stratégie attribuée à la candidature sera appliquée. 
- Si aucune stratégie n’a été assigné à l’objet application, le locataire ou l’entité du service, les valeurs par défaut seront appliquées (voir tableau ci-dessus).

Pour plus d’informations sur la relation entre les objets d’application et les objets principal de service dans Active Directory Azure, reportez-vous à la section [Application et objets principal du service dans Azure Active Directory](active-directory-application-objects.md).

Validité d’un jeton est évaluée au moment où qu'il est utilisé. La stratégie avec la priorité la plus élevée sur l’application qui est accédée prend effet.


>[AZURE.NOTE]
>Exemple
>
>Un utilisateur souhaite accéder aux applications web 2, A et B. 
>
>
>- Les deux applications sont dans le même client parent. 
>- Stratégie de jeton de durée de vie 1 avec un jeton Session Max Age de 8 heures est définie comme valeur par défaut du locataire parent.
>- L’application Web A est une application web d’usage régulier et n’est pas liée à toutes les stratégies. 
>- L’application Web B est utilisée pour les processus hautement sensibles et son principal de service est lié à la stratégie de durée de vie de jeton 2 avec un jeton de Session Max Age de 30 minutes.
>
>À 12:00, l’utilisateur ouvre une nouvelle session de navigateur et tente d’accéder aux applications web A. l’utilisateur est redirigé vers Azure AD et est invité à vous connecter. Il dépose un cookie avec un jeton de session dans le navigateur. L’utilisateur est redirigé précédent pour le web d’une application avec un jeton d’identification qui leur permet d’accéder à l’application.
>
>À 12 h 15, l’utilisateur tente ensuite d’accéder à l’application web B. Le navigateur redirige vers Azure AD qui détecte le cookie de session. Principaux de service Web application B est lié à une stratégie de 1, mais il fait également partie du locataire parent avec le stratégie par défaut 2. Stratégie 2 prend effet dans la mesure où les stratégies liées aux entités de service ont une priorité plus élevée que les stratégies par défaut de clients. Le jeton de session a été émis dans les 30 dernières minutes et il est considéré comme valide. L’utilisateur est redirigé vers l’application web B avec un jeton ID leur accorder l’accès.
>
>À 13 h 00 l’utilisateur tente d’accéder à web application a L’utilisateur est redirigé vers Azure AD. L’application Web A n’est pas liée à des stratégies, mais dans la mesure où il est un client avec la stratégie de la valeur par défaut 1, cette stratégie prend effet. La session de cookie est détecté a été émise dans les 8 dernières heures et l’utilisateur est en mode silencieux redirigée vers application web A avec un nouveau jeton ID sans devoir s’authentifier.
>
>L’utilisateur essaie immédiatement pour accéder à l’application web B. L’utilisateur est redirigé vers Azure AD. Comme avant, 2 de stratégie prend effet. Le jeton a été délivré plus de 30 minutes auparavant, l’utilisateur est invité à entrer de nouveau leurs informations d’identification et une nouvelle session et le jeton d’ID sont émis. L’utilisateur peut ensuite accéder web application B.

## <a name="configurable-policy-properties-in-depth"></a>Propriétés de stratégie configurables : profondeur

### <a name="access-token-lifetime"></a>Durée de vie de jeton accès

**Chaîne :** AccessTokenLifetime

**Affecte :** Jetons d’accès, les jetons d’ID

**Résumé :** Cette stratégie détermine la durée pendant laquelle l’accès et jetons d’ID pour cette ressource sont considérés comme valides. Réduction de la durée de vie de jeton accès réduit le risque d’un accès ou d’un jeton d’identification utilisé par un acteur malveillant pendant une longue période de temps (comme ils ne peuvent pas être révoqués) mais également nuit aux performances comme les jetons doivent être remplacées plus souvent.

### <a name="refresh-token-max-inactive-time"></a>Actualisation jeton max inactif

**Chaîne :** MaxInactiveTime

**Affecte :** Actualiser les jetons

**Résumé :** Cette stratégie détermine l’âge un jeton d’actualisation peut être un client pour pouvoir l’utiliser n’est plus à récupérer une nouvelle paire de jeton d’accès et d’actualisation lors de l’accès à cette ressource. Dans la mesure où un nouveau jeton d’actualisation est généralement retourné à qu'un jeton d’actualisation est utilisé, le client ne doit pas parvenus toute ressource à l’aide du jeton d’actualisation en cours pour la période de temps avant que cette stratégie empêche l’accès. 

Cette stratégie force les utilisateurs qui n’ont pas été actives sur leur client pour s’authentifier à nouveau afin de récupérer un nouveau jeton d’actualisation. 

Il est important de noter que l’actualisation jeton inactif temps Max doit être définie à une valeur inférieure au seul facteur jeton Max Age et plusieurs facteurs actualiser jeton Max Age.

### <a name="single-factor-refresh-token-max-age"></a>Âge max jeton de rafraîchissement du seul facteur

**Chaîne :** MaxAgeSingleFactor

**Affecte :** Actualiser les jetons

**Résumé :** Cette stratégie contrôle la durée pendant laquelle un utilisateur peut continuer utiliser les jetons de l’actualisation pour obtenir accès/actualisation nouveau jetons paires après la dernière fois qu’il est authentifiés avec succès avec uniquement un facteur unique. Une fois l’utilisateur authentifié et reçoit un nouveau jeton d’actualisation, ils seront en mesure d’utiliser le flux de jeton d’actualisation (tant que le jeton d’actualisation en cours n’est pas révoqué et qu’il ne reste pas inutilisé depuis plus longtemps que la période inactif) pour la période spécifiée. À ce stade, les utilisateurs seront forcés à s’authentifier de nouveau pour recevoir un nouveau jeton d’actualisation. 

Réduction de la durée de vie maximale forcera les utilisateurs s’authentifient le plus souvent. Dans la mesure où l’authentification unifactorielle est considérée comme moins sûre que d’une authentification à facteurs multiples, il est recommandé que cette stratégie est définie à une valeur inférieure ou égale à plusieurs facteurs actualiser jeton Max âge stratégie.

### <a name="multi-factor-refresh-token-max-age"></a>Âge max jeton de plusieurs facteurs actualisation

**Chaîne :** MaxAgeMultiFactor

**Affecte :** Actualiser les jetons

**Résumé :** Cette stratégie contrôle la durée pendant laquelle un utilisateur peut continuer utiliser les jetons de l’actualisation pour obtenir accès/actualisation nouveau jetons paires après la dernière fois qu’il est authentifiés avec succès avec plusieurs facteurs. Une fois l’utilisateur authentifié et reçoit un nouveau jeton d’actualisation, ils seront en mesure d’utiliser le flux de jeton d’actualisation (tant que le jeton d’actualisation en cours n’est pas révoqué et qu’il ne reste pas inutilisé depuis plus longtemps que la période inactif) pour la période spécifiée. À ce stade, les utilisateurs seront forcés à s’authentifier de nouveau pour recevoir un nouveau jeton d’actualisation. 

Réduction de la durée de vie maximale forcera les utilisateurs s’authentifient le plus souvent. Dans la mesure où l’authentification unifactorielle est considérée comme moins sûre que d’une authentification à facteurs multiples, il est recommandé que cette stratégie est définie à une valeur supérieure ou égale que le seul facteur stratégie d’actualisation de jeton Max âge.

### <a name="single-factor-session-token-max-age"></a>Âge max jeton de session du seul facteur

**Chaîne :** MaxAgeSessionSingleFactor

**Affecte :** Jetons de session (persistants et non persistants)

**Résumé :** Cette stratégie contrôle la durée pendant laquelle un utilisateur peut continuer à utiliser les jetons de session pour obtenir de nouveaux jetons de session et ID après la dernière fois qu’ils authentifiés avec succès avec uniquement un facteur unique. Une fois l’utilisateur authentifié et reçoit un jeton de session nouvelle, ils seront en mesure d’utiliser le flux de jeton de session (tant que le jeton de session en cours n’est pas révoqué ou expiré), pour la période spécifiée. À ce stade, les utilisateurs seront forcés à s’authentifier de nouveau pour recevoir un jeton de la nouvelle session. 

Réduction de la durée de vie maximale forcera les utilisateurs s’authentifient le plus souvent. Dans la mesure où l’authentification unifactorielle est considérée comme moins sûre que d’une authentification à facteurs multiples, il est recommandé que cette stratégie est définie à une valeur inférieure ou égale à la stratégie de durée de vie de Max jeton multi-facteurs Session.

### <a name="multi-factor-session-token-max-age"></a>Âge max jeton de session de plusieurs facteurs

**Chaîne :** MaxAgeSessionMultiFactor

**Affecte :** Jetons de session (persistants et non persistants)

**Résumé :** Cette stratégie contrôle la durée pendant laquelle un utilisateur peut continuer à utiliser les jetons de session pour obtenir de nouveaux jetons de session et ID après la dernière fois qu’ils authentifiés avec succès par plusieurs facteurs. Une fois l’utilisateur authentifié et reçoit un jeton de session nouvelle, ils seront en mesure d’utiliser le flux de jeton de session (tant que le jeton de session en cours n’est pas révoqué ou expiré), pour la période spécifiée. À ce stade, les utilisateurs seront forcés à s’authentifier de nouveau pour recevoir un jeton de la nouvelle session. 

Réduction de la durée de vie maximale forcera les utilisateurs s’authentifient le plus souvent. Dans la mesure où l’authentification unifactorielle est considérée comme moins sûre que d’une authentification à facteurs multiples, il est recommandé que cette stratégie est définie à une valeur supérieure ou égale à la stratégie de durée de vie de Max jeton seul facteur Session.

## <a name="sample-token-lifetime-policies"></a>Exemples de stratégies de jeton de durée de vie

Possibilité de créer et de gérer les durées de vie jetons pour des applications, des entités de service et vos clients de général expose toutes sortes de nouveaux scénarios possibles dans Azure AD.  Nous allons vous guider dans quelques scénarios courants stratégie qui vous aidera à imposer de nouvelles règles pour :


- Durée de vie des jetons
- Temps inactif de jeton Max
- Jeton de durée de vie maximale

Nous allons un peu de scénarios, y compris :


- Gestion de la stratégie de valeur par défaut d’un client
- Création d’une stratégie pour connecter à Web
- Création d’une stratégie pour les applications natives en appelant une API Web
- Gestion d’une stratégie de type Avancé 

### <a name="prerequisites"></a>Conditions préalables
Dans les scénarios d’exemple nous allons être création, mise à jour, la liaison et suppression de stratégies sur les applications, les entités de service et vos clients de général.  Si vous êtes nouveau sur Azure AD, extraction [cet article](active-directory-howto-tenant.md) pour vous aider à commencer avant de procéder à ces exemples.  


1. Pour commencer, téléchargez la toute dernière [Aperçu d’applet de commande PowerShell de publicité Azure](https://www.powershellgallery.com/packages/AzureADPreview). 
2.  Une fois que vous avez les applets de commande PowerShell de publicité Azure, exécuter la commande Connect pour vous connecter à votre compte d’administrateur AD Azure. Vous devrez effectuer cette opération chaque fois que vous démarrez une nouvelle session.
        
        Connect-AzureAD -Confirm

3.  Exécutez la commande suivante pour afficher toutes les stratégies qui ont été créés dans vos clients.  Cette commande doit être utilisée après la plupart des opérations dans les scénarios suivants.  Il vous aidera également obtenir l' **ID de l’objet** de vos stratégies. 
        
        Get-AzureADPolicy

### <a name="sample-managing-a-tenants-default-policy"></a>Exemple : Gestion des stratégie de par défaut d’un client

Dans cet exemple, nous allons créer une stratégie qui permet à vos utilisateurs pour vous connecter à vos clients toute moins fréquemment. 

Pour ce faire, nous créons une stratégie du jeton de durée de vie pour les jetons d’actualiser seul facteur qui est appliquée à vos clients. Cette stratégie s’appliquera à toutes les applications de vos clients et chaque service principal qui n’a pas déjà une stratégie définie pour elle. 

1.  **Créer une stratégie de jeton de durée de vie.** 

La valeur du jeton actualiser seul facteur « jusqu'à-révoqué » de sens qu’il n’expire jusqu'à ce que l’accès est révoqué.  La définition de la stratégie ci-dessous est ce que nous allons créer :
        
        @("{
          `"TokenLifetimePolicy`":
              {
                 `"Version`":1, 
                 `"MaxAgeSingleFactor`":`"until-revoked`"
              }
        }")

Puis exécutez la commande suivante pour créer cette stratégie. 

        
    New-AzureADPolicy -Definition @("{`"TokenLifetimePolicy`":{`"Version`":1, `"MaxAgeSingleFactor`":`"until-revoked`"}}") -DisplayName TenantDefaultPolicyScenario -IsTenantDefault $true -Type TokenLifetimePolicy
        
Pour voir votre nouvelle stratégie et obtenir son ObjectID, exécutez la commande suivante.

    Get-AzureADPolicy
&nbsp;&nbsp;2. **mise à jour de la stratégie**

Vous avez décidé que la première stratégie n’est pas tout à fait aussi stricte que votre service requiert et avez décidé de jetons d’actualiser votre seul facteur expire dans les 2 jours. Exécutez la commande suivante. 
        
    Set-AzureADPolicy -ObjectId <ObjectID FROM GET COMMAND> -DisplayName TenantDefaultPolicyUpdatedScenario -Definition @("{`"TokenLifetimePolicy`":{`"Version`":1,`"MaxAgeSingleFactor`":`"2.00:00:00`"}}")
        
&nbsp;&nbsp;3. **vous avez terminé !** 

### <a name="sample-creating-a-policy-for-web-sign-in"></a>Exemple : Création d’une stratégie pour connecter à web

Dans cet exemple, nous allons créer une stratégie nécessitant l’authentifier vos utilisateurs plus fréquemment dans votre application Web. Cette stratégie définit la durée de vie des jetons de l’Id d’accès et l’âge maximal d’un jeton de Session selon plusieurs facteurs sur l’entité de service de votre application web.

1.  **Créer une stratégie de jeton de durée de vie.**

Cette stratégie de connexion à Web définit la durée de vie du jeton Id d’accès et de l’âge de jeton Session Max seul facteur à 2 heures.

    New-AzureADPolicy -Definition @("{`"TokenLifetimePolicy`":{`"Version`":1,`"AccessTokenLifetime`":`"02:00:00`",`"MaxAgeSessionSingleFactor`":`"02:00:00`"}}") -DisplayName WebPolicyScenario -IsTenantDefault $false -Type TokenLifetimePolicy

Pour voir votre nouvelle stratégie et obtenir son ObjectID, exécutez la commande suivante.

    Get-AzureADPolicy
&nbsp;&nbsp;2. **affecter la stratégie à votre principal service.**

Nous allons associer cette nouvelle stratégie à une entité de sécurité de service.  Vous aurez également besoin d’un moyen d’accéder aux **ObjectId** de votre service principal. Vous pouvez interroger le [Microsoft Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity) ou accédez à notre [Outil de graphe Explorer](https://graphexplorer.cloudapp.net/) et connectez-vous à votre compte de publicité Azure pour afficher les entités de service de tous vos clients. 

Une fois que vous avez **ObjectId**, exécutez la commande suivante.
        
    Add-AzureADServicePrincipalPolicy -ObjectId <ObjectID of the Service Principal> -RefObjectId <ObjectId of the Policy>
&nbsp;&nbsp;3. **vous avez terminé !** 

 

### <a name="sample-creating-a-policy-for-native-apps-calling-a-web-api"></a>Exemple : Création d’une stratégie pour les applications natives en appelant une API Web

>[AZURE.NOTE]
>Liaison des stratégies pour applications est actuellement désactivée.  Nous travaillons sur l’activation de ce peu de temps.  Cette page sera mis à jour dès que la fonctionnalité n’est disponible.

Dans cet exemple, nous va créer une stratégie qui impose aux utilisateurs de s’authentifier inférieure et s’allonger la durée pendant laquelle qu'ils peuvent être inactives sans devoir s’authentifier à nouveau. La stratégie s’appliquera à l’API Web, de cette façon, lorsque l’application Native demande une ressource que cette stratégie sera appliquée.

1.  **Créer une stratégie de jeton de durée de vie.** 

Cette commande crée une stratégie stricte d’une API Web. 
        
    New-AzureADPolicy -Definition @("{`"TokenLifetimePolicy`":{`"Version`":1,`"MaxInactiveTime`":`"30.00:00:00`",`"MaxAgeMultiFactor`":`"until-revoked`",`"MaxAgeSingleFactor`":`"180.00:00:00`"}}") -DisplayName WebApiDefaultPolicyScenario -IsTenantDefault $false -Type TokenLifetimePolicy
         
Pour voir votre nouvelle stratégie et obtenir son ObjectID, exécutez la commande suivante.

    Get-AzureADPolicy

&nbsp;&nbsp;2. **affectez la stratégie à votre API Web**.

Nous allons associer cette nouvelle stratégie avec une application.  Vous aurez également besoin d’un moyen d’accéder aux **ObjectId** de votre application. La meilleure façon de votre application **ObjectId** est à utiliser le [Portail Azure](https://portal.azure.com/). 

Une fois que vous avez **ObjectId**, exécutez la commande suivante.

    Add-AzureADApplicationPolicy -ObjectId <ObjectID of the App> -RefObjectId <ObjectId of the Policy>

&nbsp;&nbsp;3. **vous avez terminé !** 

### <a name="sample-managing-an-advanced-policy"></a>Exemple : Gestion d’une stratégie de type Avancé 

Dans cet exemple, nous allons créer quelques stratégies pour montrer comment fonctionne le système de priorité, et comment vous pouvez gérer plusieurs stratégies sont appliquées à plusieurs objets. Cela vous donne une idée de la priorité des stratégies expliqué ci-dessus et permet également de gérer des scénarios plus complexes. 

1.  **Créer une stratégie de jeton de durée de vie**

Jusqu'à présent assez simple. Nous avons créé une stratégie par défaut client qui définit la durée de vie seul facteur actualiser jeton à 30 jours. 

    New-AzureADPolicy -Definition @("{`"TokenLifetimePolicy`":{`"Version`":1,`"MaxAgeSingleFactor`":`"30.00:00:00`"}}") -DisplayName ComplexPolicyScenario -IsTenantDefault $true -Type TokenLifetimePolicy
Pour voir votre nouvelle stratégie et obtenir est ObjectID, exécutez la commande suivante.
 
    Get-AzureADPolicy

&nbsp;&nbsp;2. **affectez la stratégie à un Principal de Service**

Nous avons une stratégie sur le locataire entière.  Supposons que nous souhaitons conserver cette stratégie de 30 jours pour un principal de service spécifique, mais modifier la stratégie par défaut de clients pour être la limite supérieure de « jusqu'à-révoqué ». 

Tout d’abord, nous allons associer cette nouvelle stratégie avec notre principal du service.  Vous aurez également besoin d’un moyen d’accéder aux **ObjectId** de votre service principal. Vous pouvez interroger le [Microsoft Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity) ou accédez à notre [Outil de graphe Explorer](https://graphexplorer.cloudapp.net/) et connectez-vous à votre compte de publicité Azure pour afficher les entités de service de tous vos clients. 

Une fois que vous avez **ObjectId**, exécutez la commande suivante.

    Add-AzureADServicePrincipalPolicy -ObjectId <ObjectID of the Service Principal> -RefObjectId <ObjectId of the Policy>

&nbsp;&nbsp;3. **définir l’indicateur IsTenantDefault sur false à l’aide de la commande suivante**. 

    Set-AzureADPolicy -ObjectId <ObjectId of Policy> -DisplayName ComplexPolicyScenario -IsTenantDefault $false
&nbsp;&nbsp;4. **créer une nouvelle stratégie client par défaut**

    New-AzureADPolicy -Definition @("{`"TokenLifetimePolicy`":{`"Version`":1,`"MaxAgeSingleFactor`":`"until-revoked`"}}") -DisplayName ComplexPolicyScenarioTwo -IsTenantDefault $true -Type TokenLifetimePolicy

&nbsp;&nbsp;5. **vous avez terminé !** 

Vous avez maintenant la stratégie d’origine liée à votre principal du service et la nouvelle stratégie à définir comme votre stratégie par défaut de clients.  Il est important de se rappeler que les stratégies appliquées aux entités de service ont priorité sur les stratégies de clients par défaut. 


## <a name="cmdlet-reference"></a>Référence de l’applet de commande

### <a name="manage-policies"></a>Gérer les stratégies
Les applets de commande suivantes peut être utilisé pour gérer les stratégies.</br></br>



#### <a name="new-azureadpolicy"></a>Nouvelle-AzureADPolicy
Crée une nouvelle stratégie.

    New-AzureADPolicy -Definition <Array of Rules> -DisplayName <Name of Policy> -IsTenantDefault <boolean> -Type <Policy Type> 

Paramètres|Description|Exemple|
-----| ----- |-----|
-Définition| Tableau de stringified JSON qui contient toutes les règles de la stratégie.|-Definition @("{`"TokenLifetimePolicy`":{`"Version`":1,`"MaxInactiveTime`":`"20:00:00`"}}") 
-DisplayName|Chaîne du nom de la stratégie|DisplayName - MyTokenPolicy
-IsTenantDefault|Si la valeur true définit la stratégie en tant que stratégie par défaut du locataire, si la valeur false n’a aucun effet|IsTenantDefault - $true
-Type|Le type de stratégie, pour la durée de vie jeton toujours utiliser « TokenLifetimePolicy »|-Tapez TokenLifetimePolicy
AlternativeIdentifier-[facultatif]|Définit un autre id à la stratégie.|AlternativeIdentifier - myAltId
</br></br>

#### <a name="get-azureadpolicy"></a>Get-AzureADPolicy         
Obtient toutes les stratégies de AzureAD ou de stratégie spécifiée 
        
    Get-AzureADPolicy 

Paramètres|Description|Exemple|
-----| ----- |-----|
ObjectId-[facultatif]|L’Id de la stratégie de l’objet que vous souhaitez obtenir. |ObjectId - &lt;ObjectID de stratégie&gt; 
</br></br>

#### <a name="get-azureadpolicyappliedobject"></a>Get-AzureADPolicyAppliedObject         
Obtient toutes les applications et les identités de service liées à une stratégie
        
    Get-AzureADPolicyAppliedObject -ObjectId <object id of policy> 

Paramètres|Description|Exemple|
-----| ----- |-----|
-ObjectId|L’Id de la stratégie de l’objet que vous souhaitez obtenir.|ObjectId - &lt;ObjectID de stratégie&gt;
</br></br>

#### <a name="set-azureadpolicy"></a>Ensemble-AzureADPolicy
Met à jour une stratégie existante
        
    Set-AzureADPolicy -ObjectId <object id of policy> -DisplayName <string> 
 
Paramètres|Description|Exemple|
-----| ----- |-----|
-ObjectId|L’Id de la stratégie de l’objet que vous souhaitez obtenir.|ObjectId - &lt;ObjectID de stratégie&gt;
-DisplayName|Chaîne du nom de la stratégie|DisplayName - MyTokenPolicy
-Définition [facultatif]|Tableau de stringified JSON qui contient toutes les règles de la stratégie.|-Definition @("{`"TokenLifetimePolicy`":{`"Version`":1,`"MaxInactiveTime`":`"20:00:00`"}}") 
IsTenantDefault-[facultatif]|Si la valeur true définit la stratégie en tant que stratégie par défaut du locataire, si la valeur false n’a aucun effet|IsTenantDefault - $true
-Type [facultatif]|Le type de stratégie, pour la durée de vie jeton toujours utiliser « TokenLifetimePolicy »|-Tapez TokenLifetimePolicy
AlternativeIdentifier-[facultatif]|Définit un autre id à la stratégie.|AlternativeIdentifier - myAltId
</br></br>

#### <a name="remove-azureadpolicy"></a>Supprimer-AzureADPolicy         
Supprime la stratégie spécifiée

     Remove-AzureADPolicy -ObjectId <object id of policy>

Paramètres|Description|Exemple|
-----| ----- |-----|
-ObjectId|L’Id de la stratégie de l’objet que vous souhaitez obtenir.|ObjectId - &lt;ObjectID de stratégie&gt;
</br></br>

### <a name="application-policies"></a>Stratégies d’application
Les applets de commande suivant peut être utilisé pour les stratégies d’application.</br></br>

#### <a name="add-azureadapplicationpolicy"></a>Ajouter-AzureADApplicationPolicy         
Liens de la stratégie spécifiée pour une application

    Add-AzureADApplicationPolicy -ObjectId <object id of application> -RefObjectId <object id of policy>

Paramètres|Description|Exemple|
-----| ----- |-----|
-ObjectId|Id de l’Application de l’objet.|ObjectId - &lt;ObjectID de l’Application&gt; 
-RefObjectId|L’objet, l’Id de la stratégie. |RefObjectId - &lt;ObjectID de stratégie&gt;
</br></br>

#### <a name="get-azureadapplicationpolicy"></a>Get-AzureADApplicationPolicy        
Obtient la stratégie attribuée à une application

    Get-AzureADApplicationPolicy -ObjectId <object id of application>

Paramètres|Description|Exemple|
-----| ----- |-----|
-ObjectId|Id de l’Application de l’objet.|ObjectId - &lt;ObjectID de l’Application&gt; 
</br></br>

#### <a name="remove-azureadapplicationpolicy"></a>Supprimer-AzureADApplicationPolicy        
Supprime une stratégie d’une application

    Remove-AzureADApplicationPolicy -ObjectId <object id of application> -PolicyId <object id of policy>

Paramètres|Description|Exemple|
-----| ----- |-----|
-ObjectId|Id de l’Application de l’objet.|ObjectId - &lt;ObjectID de l’Application&gt; 
-PolicyId| L’ObjectId de stratégie.|PolicyId - &lt;ObjectID de stratégie&gt;
</br></br>

### <a name="service-principal-policies"></a>Stratégies du service principales
Les applets de commande suivant peut être utilisé pour les stratégies de service principal.</br></br>

#### <a name="add-azureadserviceprincipalpolicy"></a>Ajouter-AzureADServicePrincipalPolicy         
Lie la stratégie spécifiée pour un principal de service

    Add-AzureADServicePrincipalPolicy -ObjectId <object id of service principal> -RefObjectId <object id of policy>

Paramètres|Description|Exemple|
-----| ----- |-----|
-ObjectId|Id de l’Application de l’objet.|ObjectId - &lt;ObjectID de l’Application&gt; 
-RefObjectId|L’objet, l’Id de la stratégie. |RefObjectId - &lt;ObjectID de stratégie&gt;
</br></br>

#### <a name="get-azureadserviceprincipalpolicy"></a>Get-AzureADServicePrincipalPolicy        
Obtient toute stratégie liée à l’entité du service spécifié

    Get-AzureADServicePrincipalPolicy -ObjectId <object id of service principal>
 
Paramètres|Description|Exemple|
-----| ----- |-----|
-ObjectId|Id de l’Application de l’objet.|ObjectId - &lt;ObjectID de l’Application&gt; 
</br></br>

#### <a name="remove-azureadserviceprincipalpolicy"></a>Supprimer-AzureADServicePrincipalPolicy         
Supprime la stratégie principale de service spécifié

    Remove-AzureADServicePrincipalPolicy -ObjectId <object id of service principal>  -PolicyId <object id of policy>
 
Paramètres|Description|Exemple|
-----| ----- |-----|
-ObjectId|Id de l’Application de l’objet.|ObjectId - &lt;ObjectID de l’Application&gt; 
-PolicyId| L’ObjectId de stratégie.|PolicyId - &lt;ObjectID de stratégie&gt;
