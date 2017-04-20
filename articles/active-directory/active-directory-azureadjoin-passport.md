<properties
    pageTitle="Authentification des identités sans mots de passe Microsoft Passport | Microsoft Azure"
    description="Fournit une vue d’ensemble de Microsoft Passport et des informations supplémentaires sur le déploiement de Microsoft Passport."
    services="active-directory"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor=""
    tags="azure-classic-portal"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="femila"/>

# <a name="authenticating-identities-without-passwords-through-microsoft-passport"></a>Authentification des identités sans mots de passe Microsoft Passport

Les méthodes en cours de l’authentification avec des mots de passe seuls ne suffisent pas à protéger les utilisateurs. Les utilisateurs réutiliser et oublient de mots de passe. Mots de passe sont pouvant, phishable, sujet aux fissures et à deviner. Ils obtiennent également difficiles à mémoriser et sujets aux attaques de type «[passer la valeur de hachage](https://technet.microsoft.com/dn785092.aspx)».

## <a name="about-microsoft-passport"></a>À propos de Microsoft Passport
Microsoft Passport est une approche de l’authentification basée sur les certificats pour les entreprises et les consommateurs qui va au-delà des mots de passe ou une clé privée/publique. Cette forme d’authentification s’appuie sur les informations d’identification de la paire de clés qui peuvent remplacer les mots de passe et sont résistantes aux failles, vols et le phishing.

 Microsoft Passport permet à un utilisateur de s’authentifier auprès d’un compte Microsoft, un compte Active Directory de Windows Server, un compte Microsoft Azure Active Directory (AD Azure) ou un service non Microsoft qui prend en charge de l’authentification rapide identité en ligne (Rex). Après une vérification en deux étapes initiales lors de l’inscription de Microsoft Passport, Microsoft Passport est configuré sur le périphérique de l’utilisateur et l’utilisateur définit un mouvement, qui peut être Windows Hello ou un code confidentiel. L’utilisateur fournit le mouvement pour vérifier son identité. Windows utilise Microsoft Passport pour authentifier l’utilisateur et de les aider à accéder aux ressources protégées et les services.

La clé privée est rendue disponible uniquement via un mouvement « utilisateur » comme un code confidentiel, biométrie ou un périphérique distant, comme une carte à puce pour vous connecter au périphérique de l’utilisateur. Cette information est liée à un certificat ou d’une paire de clés asymétrique. La clé privée est attestée si le périphérique possède une puce de Module de plateforme sécurisée (TPM) de matériel. La clé privée ne quitte jamais le périphérique.

La clé publique est inscrit avec Azure Active Directory et de Windows Server Active Directory (pour les locaux). Les fournisseurs d’identité (IDPs) valider l’utilisateur en mappant la clé publique de l’utilisateur à la clé privée et fournissent des informations de connexion par mot de passe une à la fois (OTP), PhoneFactor ou un mécanisme de notification différents.

## <a name="why-enterprises-should-adopt-microsoft-passport"></a>Pourquoi les entreprises doivent adopter Microsoft Passport

En activant Microsoft Passport, les entreprises peuvent sécuriser leurs ressources encore plus par :

* Configuration de Microsoft Passport avec l’option matériel par défaut. Cela signifie que les clés seront générées sur TPM 1.2 ou 2.0 de module de plateforme sécurisée lorsqu’elle est disponible. Lorsque le module de plateforme sécurisée n’est pas disponible, logiciel génère la clé.

* Définition de la complexité et la longueur de l’axe, et si l’utilisation de Hello est activée dans votre organisation.

* Configuration de Microsoft Passport pour prendre en charge des scénarios de type carte à puce en utilisant le certificat d’approbation.

## <a name="how-microsoft-passport-works"></a>Mode de fonctionnement de Microsoft Passport
1. Clés sont générées sur le matériel par le logiciel ou le module de plateforme sécurisée. De nombreux périphériques ont une puce TPM intégrée qui permet de sécuriser le matériel en intégrant des clés de chiffrement dans les périphériques. Module de plateforme sécurisée 1.2 ou 2.0 de module de plateforme sécurisée génère des clés ou des certificats qui sont créés à partir des clés générées.

2. Le module de plateforme sécurisée atteste ces clés liés aux matériels.

3. Un mouvement unique déverrouiller déverrouille le périphérique. Ce mouvement permet d’accéder à plusieurs ressources si le périphérique est joint au domaine ou Azure associés à Active Directory.

## <a name="how-the-microsoft-passport-lifecycle-works"></a>Mode de fonctionnement du cycle de vie de Microsoft Passport

![Cycle de vie de Microsoft Passport](./media/active-directory-azureadjoin/active-directory-azureadjoin-microsoft-passport.png)

Le schéma ci-dessus illustre la paire de clés publique/privée et de la validation par le fournisseur d’identité. Chacune de ces étapes est expliquée en détail ici :

1. L’utilisateur prouve son identité par le biais de plusieurs méthodes de vérification linguistiques intégrés (mouvements, physique des cartes à puce, l’authentification multifacteur) et envoie ces informations à un fournisseur identité (IDP) comme Azure Active Directory ou Active Directory local.

2. Le périphérique puis crée la clé, atteste de la clé, prend la partie publique de cette clé, attache avec les instructions de la station, se connecte et envoie à la IDP pour enregistrer la clé.

4. Dès que le IDP enregistre la partie publique de la clé, la IDP défis du périphérique avec la partie privée de la clé.

5. Le IDP valide, puis émet le jeton d’authentification qui permet à l’utilisateur et l’accès aux périphériques les ressources protégées. IDPs pouvez écrire les applications multiplates-formes ou utiliser des navigateurs pris en charge (via les API JavaScript/Webcrypto) pour créer et utiliser des informations d’identification Microsoft Passport pour leurs utilisateurs.

## <a name="the-deployment-requirements-for-microsoft-passport"></a>Les spécifications de déploiement de Microsoft Passport
### <a name="at-the-enterprise-level"></a>Au niveau de l’entreprise

* L’entreprise a un abonnement Azure.

### <a name="at-the-user-level"></a>Au niveau de l’utilisateur

* L’ordinateur exécute Windows 10 Professionnel ou entreprise.

Pour des instructions de déploiement détaillées, reportez-vous à la section [Activer Microsoft Passport pour le travail de l’organisation](active-directory-azureadjoin-passport-deployment.md).


## <a name="additional-information"></a>Informations supplémentaires

* [Windows 10 pour l’entreprise : méthodes d’utilisation de périphériques pour le travail](active-directory-azureadjoin-windows10-devices-overview.md)
* [Extension des fonctionnalités du nuage pour périphériques Windows 10 par le biais de joindre de Azure Active Directory](active-directory-azureadjoin-user-upgrade.md)
* [En savoir plus sur les scénarios d’utilisation pour joindre des annonces d’Azure](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Connecter des périphériques d’à un domaine à AD Azure pour des expériences Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Paramétrage de joindre des annonces d’Azure](active-directory-azureadjoin-setup.md)
