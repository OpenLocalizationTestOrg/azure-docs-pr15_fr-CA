<properties
   pageTitle="Authentification et autorisation avec alimentation BI incorporé"
   description="Authentification et autorisation avec alimentation BI incorporé"
   services="power-bi-embedded"
   documentationCenter=""
   authors="guyinacube"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="10/04/2016"
   ms.author="asaxton"/>

# <a name="authenticating-and-authorizing-with-power-bi-embedded"></a>Authentification et autorisation avec alimentation BI incorporé

Le service d’alimentation BI incorporé utilise des **clés** et des **Jetons de l’application** pour l’authentification et l’autorisation, au lieu de l’authentification de l’utilisateur final explicite. Dans ce modèle, votre application gère l’authentification et autorisation pour les utilisateurs finaux. Si nécessaire, votre application crée et envoie les jetons de l’application qui indique à notre service pour rendre le rapport demandé. Cette conception ne nécessite pas votre application d’utiliser Azure Active Directory pour l’authentification des utilisateurs et l’autorisation, bien que vous puissiez toujours.

## <a name="two-ways-to-authenticate"></a>Deux façons pour authentifier

**Clé** - vous pouvez utiliser des clés pour tous les appels de l’API REST incorporé de puissance BI. Les clés sont accessibles dans le **portail Azure** en cliquant sur **tous les paramètres** , puis les **touches d’accès rapide**. Toujours traiter votre clé comme s’il s’agissait d’un mot de passe. Ces clés ont les autorisations nécessaires pour appeler les API REST sur une collection de l’espace de travail particulier.

Pour utiliser une clé sur un appel reste, ajoutez l’en-tête d’autorisation suivant :            

    Authorization: AppKey {your key}

**Jeton de app** - App jetons sont utilisés pour toutes les demandes de l’incorporation. Ils sont conçus pour être exécuté côté client, ils sont limités à un seul rapport et il est recommandé de définir une heure d’expiration.

Jetons d’application sont un JWT (jeton Web JSON) qui est signé par l’une de vos clés.

Votre code d’application peut contenir les déclarations suivantes :

| Demande d’indemnisation      | Description        |
|--------------|------------|
| **ver**      | La version de jeton de l’application. 0.2.0 est la version actuelle.       |
| **AUD**      | Le destinataire prévu du jeton. Pour l’utilisation d’énergie BI Embedded : « https://analysis.windows.net/powerbi/api ».  |
| **ISS**      |  Chaîne indiquant l’application qui a émis le jeton.    |
| **type de**     | Le type de jeton d’application en cours de création. Active le seul type pris en charge est **incorporer**.   |
| **Windows Connect Now**      | Nom de collection d’espace de travail le jeton est émise.  |
| **WID**      | ID de l’espace de travail le jeton est émise.  |
| **RID**      | ID de l’état du jeton est émise.     |
| **nom d’utilisateur** (facultatif) |  Utilisé avec RLS, il s’agit d’une chaîne qui peut aider à identifier l’utilisateur lors de l’application des règles RLS. |
| **rôles** (facultatif)   |   Chaîne contenant les rôles à sélectionner lors de l’application des règles de sécurité de niveau ligne. Si vous passez plusieurs rôles, il doivent être passés sous la forme d’un tableau de chaîne.    |
| **exp** (facultatif)    |   Indique le délai dans lequel le jeton expirera. Celles-ci doivent être transmises sous la forme d’horodatages d’Unix.   |
| **NBF** (facultatif)    |   Indique le démarrage dans laquelle le jeton est valide. Celles-ci doivent être transmises sous la forme d’horodatages d’Unix.   |

Un jeton d’application exemple se présente comme suit :

![](media\power-bi-embedded-app-token-flow\power-bi-embedded-app-token-flow-sample-coded.png)


Lorsque décodé, il ressemble à ceci :

![](media\power-bi-embedded-app-token-flow\power-bi-embedded-app-token-flow-sample-decoded.png)


## <a name="heres-how-the-flow-works"></a>Voici comment le flux fonctionne

1. Copier les clés de l’API pour votre application. Vous pouvez obtenir les clés dans **Azure Portal**.

    ![](media\powerbi-embedded-get-started-sample\azure-portal.png)

2. Jeton déclare une réclamation et a un délai d’expiration.

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-2.png)

3. Jeton obtient signé avec touches d’accès d’une API.

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-3.png)

4. Demandes de l’utilisateur pour afficher un rapport.

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-4.png)

5.  Jeton est validé avec touches d’accès d’une API.

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-5.png)

6.  Incorporées de BI d’alimentation envoie un rapport à l’utilisateur.

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-6.png)

Une fois **Incorporées de BI d’alimentation** envoie un rapport à l’utilisateur, l’utilisateur peut afficher le rapport dans votre application personnalisée. Par exemple, si vous avez importé l' [exemple d’analyse de PBIX de données de ventes](http://download.microsoft.com/download/1/4/E/14EDED28-6C58-4055-A65C-23B4DA81C4DE/Analyzing_Sales_Data.pbix), l’exemple d’application web ressemblerait à ceci :

![](media\powerbi-embedded-get-started-sample\sample-web-app.png)

## <a name="see-also"></a>Voir aussi
- [Mise en route avec Microsoft Power BI incorporé, exemple](power-bi-embedded-get-started-sample.md)
- [Scénarios courants de Microsoft d’alimentation BI incorporé](power-bi-embedded-scenarios.md)
- [Mise en route de Microsoft d’alimentation BI incorporé](power-bi-embedded-get-started.md)
