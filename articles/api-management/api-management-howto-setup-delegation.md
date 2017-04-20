<properties 
    pageTitle="Comment déléguer l’abonnement produit et l’enregistrement d’utilisateur" 
    description="Découvrez comment déléguer l’abonnement produit et l’enregistrement d’utilisateur à un tiers dans la gestion des API Azure." 
    services="api-management" 
    documentationCenter="" 
    authors="antonba" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="antonba"/>

# <a name="how-to-delegate-user-registration-and-product-subscription"></a>Comment déléguer l’abonnement produit et l’enregistrement d’utilisateur

Délégation vous permet d’utiliser votre site Web existant pour la gestion de développeur signe-dans/connexion-haut et abonnement aux produits au lieu d’utiliser la fonctionnalité intégrée dans le portail des développeurs. Cela permet à votre site Web pour les propriétaires des données utilisateur et d’effectuer la validation de ces étapes de façon personnalisée.

## <a name="delegate-signin-up"> </a>Développeur de délégation, connexion et d’inscription

Pour déléguer un développeur de connexion et d’inscription à votre site Web existant, que vous devez créer un point de terminaison de délégation spéciaux sur votre site qui agit comme le point d’entrée pour une telle demande initiée à partir du portail de développement de gestion de l’API.

Le flux de travail final sera comme suit :

1. Clics sur le lien de connexion ou d’inscription sur le portail des développeurs gestion des API de développeur
2. Lorsque le navigateur est redirigé vers le point de terminaison de délégation
3. Point de terminaison de délégation en retour la redirige vers ou présente l’interface utilisateur demandant à l’utilisateur pour la connexion ou d’inscription
4. En cas de réussite, l’utilisateur est redirigé vers la page du portail développeur API gestion de qu'ils est démarrés à partir


Pour commencer, nous allons première gestion d’API de configuration pour router les demandes via le point de terminaison de délégation. L’API portail de gestion publisher, cliquez sur **sécurité** et puis cliquez sur l’onglet **délégation** . Cliquez sur la case à cocher pour activer 'Délégué signe & d’abonnement'.

![Page de délégation][api-management-delegation-signin-up]

* Décidez de ce que l’URL de votre point de terminaison de délégation spéciaux est et saisissez-le dans le champ **URL de point de terminaison de délégation** . 

* Dans le champ **clé de délégation d’authentification** , entrez un code secret qui sera utilisé pour le calcul d’une signature fournie pour vérification pour s’assurer que la demande provient en effet Azure API de gestion. Vous pouvez cliquer sur le bouton **Générer** pour générer de façon aléatoire une clé pour vous Managemnet de l’API.

Vous devez maintenant créer **point de terminaison de délégation**. Il doit effectuer un certain nombre d’actions :

1. Recevoir une demande sous la forme suivante :

    > *http://www.yourwebsite.com/apimdelegation?operation=SignIn&returnUrl= {URL de la page source} & SEL = {chaîne} & sig = {string}*

    Paramètres de la requête pour le cas de l’ouverture de session / d’inscription :
    - **opération**: identifie la demande quel type de délégation est - il ne peut **Ouvrir une session** dans ce cas
    - **returnUrl**: l’URL de la page où l’utilisateur a cliqué sur un lien de connexion ou d’inscription
    - **sel**: une chaîne de sel spéciale utilisée pour calculer un hachage de sécurité
    - **SIG**: un hachage de sécurité calculé à utiliser pour votre propre comparaison de hachage calculé

2. Vérifiez que la demande provient de gestion des API Azure (facultatif, mais fortement recommandé pour la sécurité)

    * Calculer un hachage SHA512 HMAC d’une chaîne basée sur les paramètres de requête **returnUrl** et **sel** ([exemple de code ci-dessous]) :
        > HMAC (**sel** '\n' + **returnUrl**)
         
    * Comparer le hachage calculé ci-dessus à la valeur du paramètre de requête **sig** . Si les deux hachages correspondent, passez à l’étape suivante, sinon, refuser la demande.

2. Vérifiez que vous recevez une demande de connexion-dans/signe-up : le paramètre **d’opération** de requête aura la valeur «**connexion**».

3. Présenter à l’utilisateur avec l’interface utilisateur pour la connexion ou d’inscription

4. Si l’utilisateur est la signature à distance, vous devez créer un compte correspondant pour les API de gestion. [Créer un utilisateur] avec l’API REST de gestion API. Lorsque vous procédez ainsi, vous assurer que le code utilisateur à celle à laquelle qu'il est dans votre magasin de l’utilisateur ou à un ID que vous pouvez effectuer le suivi de.

5. Lorsque l’utilisateur est authentifié avec succès :

    * la [demande de jeton unique single-sign-on (SSO)] via l’API REST de gestion API

    * Ajouter un paramètre de requête returnUrl à l’URL de l’authentification unique que vous avez reçu de l’appel d’API ci-dessus :
        > par exemple, https://customer.portal.azure-api.net/signin-sso?token&returnUrl=/return/url 

    * rediriger l’utilisateur vers l’URL de produits ci-dessus

En plus de l’opération de **connexion** , vous pouvez également effectuer la gestion de compte en suivant les étapes précédentes et en utilisant l’une des opérations suivantes.

-   **ChangePassword**
-   **ChangeProfile**
-   **CloseAccount**

Vous devez passer les paramètres de requête suivants pour les opérations de gestion de compte.

-   **opération**: identifie le type de demande de délégation il est (ChangePassword, ChangeProfile ou CloseAccount)
-   **ID utilisateur**: l’id d’utilisateur du compte à gérer
-   **sel**: une chaîne de sel spéciale utilisée pour calculer un hachage de sécurité
-   **SIG**: un hachage de sécurité calculé à utiliser pour votre propre comparaison de hachage calculé

## <a name="delegate-product-subscription"> </a>Délégation de l’abonnement

Délégation de l’abonnement de produit fonctionne comme utilisateur connexion/distance de délégation. Le flux de travail final se présente comme suit :

1. Développeur sélectionne un produit dans le portail de développement API gestion et clique sur le bouton s’abonner
2. Lorsque le navigateur est redirigé vers le point de terminaison de délégation
3. Point de terminaison de délégation effectue des opérations d’abonnement de produit requis - c’est à vous et peut entraîner la redirection vers une autre page pour demander des informations de facturation, poser des questions supplémentaires, ou simplement le stockage des informations et ne nécessitant ne pas une action de l’utilisateur


Pour activer la fonctionnalité, cliquez sur **abonnement délégué**sur la page **délégation** .

Vérifiez que le point de terminaison de délégation effectue les actions suivantes :


1. Recevoir une demande sous la forme suivante :

    > *http://www.yourwebsite.com/apimdelegation?operation= {opération} & productId = {product pour vous abonner à} & userId = {utilisateur effectue demande} & SEL = {chaîne} & sig = {string}*

    Paramètres de la requête pour le cas d’abonnement de produit :
    - **opération**: identifie le type de demande de délégation. Pour l’abonnement, demandes les options valides sont :
        - « S’abonner » : une demande pour s’abonner à un produit donné par l’utilisateur fourni ID (voir ci-dessous)
        - « Vous désinscrire » : une demande pour annuler l’abonnement d’un utilisateur à partir d’un produit
        - « Renouveler » : une demande de renouvellement d’un abonnement (par exemple, qui peut être sans date d’expiration)
    - **Réf produit**: l’ID du produit demandé par l’utilisateur pour s’abonner à
    - **ID utilisateur**: l’ID de l’utilisateur pour lequel la demande est effectuée.
    - **sel**: une chaîne de sel spéciale utilisée pour calculer un hachage de sécurité
    - **SIG**: un hachage de sécurité calculé à utiliser pour votre propre comparaison de hachage calculé


2. Vérifiez que la demande provient de gestion des API Azure (facultatif, mais fortement recommandé pour la sécurité)

    * Calcule un code HMAC-SHA512 d’une chaîne basée sur les paramètres de requête **productId**, **nom d’utilisateur** et **sel** :
        > HMAC (**sel** + '\n' + **productId** + '\n' + **ID utilisateur**)
         
    * Comparer le hachage calculé ci-dessus à la valeur du paramètre de requête **sig** . Si les deux hachages correspondent, passez à l’étape suivante, sinon, refuser la demande.
    
3. Effectuer tout traitement d’abonnement produit basé sur le type d’opération en **opération** , par exemple, facturation, autres questions, etc..

4. Sur l’utilisateur pour le produit de votre côté d’abonnement avec succès, abonnez-vous à l’utilisateur pour le produit de gestion de l’API en [appelant l’API REST pour l’abonnement].

## <a name="delegate-example-code"></a> Exemple de Code ##

Ces exemples de code montrent comment prendre la *clé de validation de délégation*, qui est définie dans l’écran de la délégation du portail publisher, pour créer un code HMAC qui est ensuite utilisé pour valider la signature, prouver la validité de la returnUrl passé. Le même code fonctionne pour productId et ID utilisateur avec une légère modification.

**Code C# pour générer le hachage de returnUrl**

    using System.Security.Cryptography;

    string key = "delegation validation key";
    string returnUrl = "returnUrl query parameter";
    string salt = "salt query parameter";
    string signature;
    using (var encoder = new HMACSHA512(Convert.FromBase64String(key)))
    {
        signature = Convert.ToBase64String(encoder.ComputeHash(Encoding.UTF8.GetBytes(salt + "\n" + returnUrl)));
        // change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
        // compare signature to sig query parameter
    }


**Code de NodeJS pour générer le hachage de returnUrl**

    var crypto = require('crypto');
    
    var key = 'delegation validation key'; 
    var returnUrl = 'returnUrl query parameter';
    var salt = 'salt query parameter';
    
    var hmac = crypto.createHmac('sha512', new Buffer(key, 'base64'));
    var digest = hmac.update(salt + '\n' + returnUrl).digest();
    // change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
    // compare signature to sig query parameter
    
    var signature = digest.toString('base64');

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la délégation, consultez la vidéo suivante.

> [AZURE.VIDEO delegating-user-authentication-and-product-subscription-to-a-3rd-party-site]

[Delegating developer sign-in and sign-up]: #delegate-signin-up
[Delegating product subscription]: #delegate-product-subscription
[demande de jeton unique single-sign-on (SSO)]: http://go.microsoft.com/fwlink/?LinkId=507409
[créer un utilisateur]: http://go.microsoft.com/fwlink/?LinkId=507655#CreateUser
[l’appel de l’API REST de l’abonnement]: http://go.microsoft.com/fwlink/?LinkId=507655#SSO
[Next steps]: #next-steps
[exemple de code ci-dessous]: #delegate-example-code

[api-management-delegation-signin-up]: ./media/api-management-howto-setup-delegation/api-management-delegation-signin-up.png 