<properties 
    pageTitle="Authentification à plusieurs facteurs Azure - mode de fonctionnement"
    description="Azure plusieurs facteurs d’authentification vous permet de garantir l’accès aux données et aux applications tout en répondant à la demande de l’utilisateur pour un processus de connexion simple. Il fournit une sécurité supplémentaire en exigeant un second formulaire d’authentification et offre une authentification renforcée via une gamme d’options de vérification facile."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtland"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

#<a name="how-azure-multi-factor-authentication-works"></a>Fonctionnement de l’authentification à plusieurs facteurs Azure

La sécurité de l’authentification multicritères réside dans son approche en couches. Compromettre plusieurs facteurs d’authentification un défi important pour les pirates. Même si un intrus parvient à obtenir le mot de passe, il est inutile sans avoir également de possession du dispositif approuvé. L’utilisateur doit de perdre le périphérique, la personne qui la trouve ne pourrez utiliser si il ou elle sait également le mot de passe de l’utilisateur.

![Proofup](./media/multi-factor-authentication-how-it-works/howitworks.png)



Azure plusieurs facteurs d’authentification vous permet de garantir l’accès aux données et aux applications tout en répondant à la demande de l’utilisateur pour un processus de connexion simple.  Il fournit une sécurité supplémentaire en exigeant un second formulaire d’authentification et offre une authentification renforcée via une gamme d’options de vérification facile :

- appel téléphonique
- message de texte
- notification d’une application mobile, qui permet aux utilisateurs de choisir la méthode qu’ils préfèrent
- code de vérification d’application mobile
- jetons de serment 3ème partie

Pour plus d’informations oh comment il fonctionne voir la vidéo suivante.

>[AZURE.VIDEO multi-factor-authentication-deep-dive-securing-access-on-premises]

##<a name="methods-available-for-multi-factor-authentication"></a>Méthodes disponibles pour l’authentification multifactorielle
Lorsqu’un utilisateur se connecte, une vérification supplémentaire est envoyée à l’utilisateur.  Voici une liste de méthodes qui peuvent être utilisées pour cette vérification second.

Méthode de vérification  | Description
------------- | ------------- |
Appel téléphonique | Un appel est passé au téléphone intelligent de l’utilisateur lui demandant de vérifier qu’ils sont la connexion en appuyant sur le signe #.  Cela va terminer le processus de vérification.  Cette option n’est configurable et peut être remplacée par un code que vous spécifiez.
Message de texte | Vous recevrez un message de texte le numéro de téléphone actives d’un utilisateur avec un code à 6 chiffres.  Entrez ce code pour terminer le processus de vérification.
Notification de l’application mobile | Vous recevrez une demande de vérification à leur demandant complète la vérification en sélectionnant vérifier à partir de l’application mobile Smartphone d’un utilisateur. Cela se produit si vous avez sélectionné la notification d’application comme méthode principale de vérification.  S’ils reçoivent ce lorsqu’ils ne sont pas l’ouverture de session, ils peuvent choisir de la déclarer comme fraude.
Code de vérification avec une application Mobile | Un code de vérification sera envoyé à l’application mobile qui s’exécute sur le téléphone intelligent d’un utilisateur.  Cela se produit si vous avez sélectionné un code de vérification comme méthode principale de vérification.


##<a name="available-versions-of-azure-multi-factor-authentication"></a>Versions disponibles de l’authentification à plusieurs facteurs Azure
Authentification à plusieurs facteurs Azure est disponible en trois versions différentes.  Le tableau ci-dessous décrit chacun de ces plus en détail.

Version  | Description
------------- | ------------- |
Authentification à plusieurs facteurs pour Office 365 | Cette version fonctionne uniquement avec les applications d’Office 365 et est gérée depuis le portail Office 365. Afin que les administrateurs peuvent maintenant aider à sécuriser leurs ressources Office 365 à l’aide d’une authentification multifactorielle.  Cette version est fournie avec un abonnement à Office 365.
Authentification à plusieurs facteurs pour les administrateurs Azure | Le même sous-ensemble de fonctionnalités d’authentification à plusieurs facteurs pour Office 365 sera disponible sans frais à tous les administrateurs Azure. Chaque compte administratif d’un abonnement Azure peut maintenant obtenir une protection supplémentaire à l’activation de cette fonctionnalité d’authentification à plusieurs facteurs principaux. Pour un administrateur qui souhaite accéder au portail Azure pour créer un ordinateur virtuel, un site web, gestion du stockage, services mobiles ou tout autre Service Azure peut ajouter plusieurs facteurs d’authentification à son compte d’administrateur.
Authentification à plusieurs facteurs Azure | Authentification à plusieurs facteurs Azure offre le plus riche éventail de fonctionnalités. <br><br>Il fournit des options de configuration supplémentaires via le portail de gestion d’Azure, avancée de reporting et prise en charge pour une plage de locaux et d’applications en nuage. Authentification à plusieurs facteurs Azure peut être achetée sous la forme d’une licence autonome et est fournie dans Azure Active Directory prime et Suite de mobilité d’entreprise. <br><br>Il peut également être acheté par de consommation en créant un fournisseur d’authentification selon plusieurs facteurs Azure dans un abonnement Azure.
##<a name="feature-comparison-of-versions"></a>Comparaison des fonctionnalités des versions
Le tableau suivant ci-dessous fournit une liste des fonctionnalités qui sont disponibles dans les différentes versions d’Azure, plusieurs facteurs d’authentification.


Fonctionnalité  | Authentification à plusieurs facteurs pour Office 365 (inclus dans Office 365 SKU)|Authentification à plusieurs facteurs pour les administrateurs d’Azure (inclus dans l’abonnement Azure) | Authentification à plusieurs facteurs Azure (inclus dans Azure AD Premium et Suite de mobilité d’entreprise)
------------- | :-------------: |:-------------: |:-------------: |
Les administrateurs peuvent protéger les comptes avec AMF| * | * (Disponible uniquement pour les comptes d’administrateur d’Azure)|*
Application mobile sous la forme d’un second facteur|* | * | *
Appel téléphonique en tant que second facteur|* | * | *
SMS en tant que second facteur|* | * | *
Mots de passe App pour les clients qui ne prennent pas en charge les AMF|* | * | *
Contrôle d’administration sur les méthodes d’authentification| *| *| *
Mode de broche| | | *
Alerte à la fraude| | | *
Rapports de l’AMF| | | *
Contournement unique| | | *
Voeux personnalisés pour les appels téléphoniques| | | *
Personnalisation de l’ID de l’appelant pour les appels téléphoniques| | | *
Confirmation de l’événement| | | *
Adresses IP autorisées| | | *
Suspendre AMF pour dispositifs mémorisées (version d’évaluation)| | | *
KIT DE DÉVELOPPEMENT LOGICIEL AMF| | | *
AMF pour applications sur site à l’aide du serveur de l’AMF| | | *


##<a name="how-to-get-azure-multi-factor-authentication"></a>Comment obtenir d’Azure, plusieurs facteurs d’authentification

Si vous souhaitez que toutes les fonctionnalités offertes par authentification à plusieurs facteurs Azure au lieu de simplement celles fournies pour les utilisateurs d’Office 365 et administrateurs d’Azure, il existe plusieurs options pour l’obtenir :

1.  Acheter des licences d’Azure, plusieurs facteurs d’authentification et les affecter à vos utilisateurs.
2.  Acheter des licences qui sont fournis dans les Azure Active Directory prime ou Suite de mobilité d’entreprise pour l’authentification selon plusieurs facteurs Azure et les affecter à vos utilisateurs.
3.  Créer un fournisseur d’authentification selon plusieurs facteurs Azure dans un abonnement Azure. Si vous ne disposez pas d’un abonnement Azure, vous pouvez vous inscrire pour un abonnement d’essai Azure. Les abonnements d’essai doivent à convertir en abonnements réguliers avant expiration d’évaluation.

Lorsque vous utilisez un fournisseur d’authentification selon plusieurs facteurs Azure il existe deux modèles d’utilisation disponibles qui sont facturés par le biais de votre abonnement Azure :


- **Par utilisateur**. Pour les entreprises qui veulent généralement activer l’authentification pour un nombre fixe d’employés ayant régulièrement besoin d’authentification à plusieurs facteurs.
- **Par l’authentification**. Pour les entreprises qui veulent généralement activer l’authentification pour un grand groupe d’utilisateurs externes qui ont rarement besoin d’authentification à plusieurs facteurs.

Pour connaître les prix voir [de tarification AMF Azure.](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)

Choisissez le par siège ou un modèle basé sur la consommation qui convient le mieux pour votre organisation.   Puis à obtenir le démarrage voir [Mise en route](multi-factor-authentication-get-started.md)
