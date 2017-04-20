<properties 
    pageTitle="Serveur d’authentification à plusieurs facteurs d’authentification RADIUS et Azure"
    description="Il s’agit de la page d’authentification Azure plusieurs facteurs qui les aidera dans le déploiement de l’authentification RADIUS et le serveur de l’authentification multifactorielle Azure."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/15/2016"
    ms.author="kgremban"/>



# <a name="radius-authentication-and-azure-multi-factor-authentication-server"></a>Serveur d’authentification à plusieurs facteurs d’authentification RADIUS et Azure

La section de l’authentification RADIUS vous permet d’activer et configurer l’authentification RADIUS pour le serveur de l’authentification multifactorielle Azure. RADIUS est un protocole standard pour accepter des demandes d’authentification et pour traiter ces requêtes. Le serveur de l’authentification multifactorielle Azure agit comme un serveur RADIUS et est inséré entre votre client RADIUS (par exemple, solution de VPN) et sur la cible de l’authentification, qui peut être de Active Directory (AD), un répertoire LDAP ou un autre serveur RADIUS, pour ajouter l’authentification à plusieurs facteurs Azure. Pour Azure plusieurs facteurs d’authentification de la fonction, vous devez configurer le serveur de l’authentification multifactorielle Azure afin que celle-ci puisse communiquer avec les serveurs du client et la cible de l’authentification. Le serveur de l’authentification multifactorielle Azure accepte les demandes émanant d’un client RADIUS valide les informations d’identification par rapport à la cible de l’authentification, ajoute d’Azure, plusieurs facteurs d’authentification et envoie une réponse au client RADIUS. L’authentification complète ne réussira que si le l’authentification principale et l’authentification à plusieurs facteurs Azure réussissent.

>[AZURE.NOTE]
>Le serveur de l’AMF prend uniquement en charge PAP (protocole d’authentification de mot de passe) et MSCHAPv2 (Microsoft Challenge-Handshake Authentication Protocol) les protocoles RADIUS lorsqu’il agit comme un serveur RADIUS.  D’autres protocoles tels que EAP (protocole d’authentification extensible) peuvent être utilisés lorsque le serveur de l’AMF agit comme un proxy RADIUS à un serveur RADIUS qui prend en charge ce protocole, tels que Microsoft NPS.
></br>
>Lors de l’utilisation d’autres protocoles dans cette configuration, les jetons SMS et serment à sens unique ne fonctionnera pas car le serveur de l’AMF n’est pas en mesure d’initier une réponse RADIUS défi réussie à l’aide de ce protocole.


![Authentification RADIUS](./media/multi-factor-authentication-get-started-server-rdg/radius.png)

## <a name="radius-authentication-configuration"></a>Configuration de l’authentification RADIUS

Pour configurer l’authentification RADIUS, installez le serveur de l’authentification multifactorielle Azure sur un serveur Windows. Si vous disposez d’un environnement Active Directory, le serveur doit être joint au domaine à l’intérieur du réseau. Utilisez la procédure suivante pour configurer le serveur de l’authentification multifactorielle Azure :

1. Au sein du serveur de l’authentification multifactorielle Azure, cliquez sur l’icône de l’authentification RADIUS dans le menu de gauche.
2. Case à cocher Activer le RADIUS d’authentification.
3. Sous l’onglet Clients modifier l’ou les ports d’authentification et les ports de gestion si le service Azure plusieurs facteurs d’authentification RADIUS doit être lié à des ports non standard pour écouter les demandes RADIUS à partir des clients qui seront configurés.
4. Cliquez sur Ajouter... bouton.
5. Dans la boîte de dialogue Ajouter un Client RADIUS, entrez l’adresse IP de la solution matérielle-logicielle/serveur qui authentifie le serveur de l’authentification multifactorielle Azure, un nom de l’Application (facultatif) et un secret partagé. Vous devrez être le même sur le serveur de l’authentification multifactorielle Azure et serveur/le secret partagé. Le nom de l’Application apparaît dans les rapports d’authentification à plusieurs facteurs Azure et peut être affiché dans les messages d’authentification SMS ou application Mobile.
6. La case Exiger une authentification multifactorielle utilisateur correspondance si tous les utilisateurs ont été ou seront importées dans le serveur et de l’authentification multifactorielle. Si un nombre important d’utilisateurs n’ont pas encore été importé sur le serveur et/ou est exempté de l’authentification multicritères, laissez la case désactivée. Consultez le fichier d’aide pour plus d’informations sur cette fonctionnalité.
7. Cochez la case Activer la secours jeton de serment si les utilisateurs utilisent l’authentification de mobile application Azure, plusieurs facteurs d’authentification et que vous souhaitez utiliser des codes secrets de serment sous la forme d’une authentification de secours pour la notification d’appel, SMS ou push out-of-band téléphone.
8. Cliquez sur le bouton OK.
9. Vous pouvez répéter les étapes 4 à 8 pour ajouter des clients RADIUS supplémentaires.
10. Cliquez sur l’onglet cible.
11. Si le serveur de l’authentification multifactorielle Azure est installé sur un serveur à un domaine dans un environnement Active Directory, sélectionnez le domaine Windows.
12. Si les utilisateurs doivent être authentifiés par rapport à un annuaire LDAP, sélectionnez la liaison LDAP. Lorsque vous utilisez la liaison LDAP, vous devez cliquer sur l’icône d’intégration d’annuaire et modifier la configuration de LDAP dans l’onglet Paramètres, afin que le serveur peut se lier à votre répertoire. Vous trouverez des instructions pour la configuration de LDAP dans le guide de configuration du serveur Proxy LDAP.
13. Si les utilisateurs doivent être authentifiés par rapport à un autre serveur RADIUS, sélectionnez l’ou les serveurs RADIUS.
14. Configurer le serveur que le serveur proxy les demandes RADIUS à en cliquant sur Ajouter... bouton.
15. Dans la boîte de dialogue Ajouter un serveur RADIUS, entrez l’adresse IP du serveur RADIUS et un secret partagé. Le secret partagé devez être le même sur le serveur de l’authentification multifactorielle Azure et le rayon de serveur. Modifier le port d’authentification et le port de compte si des ports différents sont utilisés par le serveur RADIUS.
16. Cliquez sur le bouton OK.
17. Vous devez ajouter le serveur de l’authentification multifactorielle Azure comme un client RADIUS sur l’autre serveur RADIUS afin qu’il traite les demandes d’accès envoyés par le serveur de l’authentification multifactorielle Azure. Vous devez utiliser le même secret partagé configuré sur le serveur de l’authentification multifactorielle Azure.
18. Vous pouvez répéter cette étape pour ajouter d’autres serveurs RADIUS et configurer l’ordre dans lequel le serveur doit appeler les avec les boutons Déplacer vers le haut et déplacer vers le bas. Cela termine la configuration du serveur de l’authentification multifactorielle Azure. Le serveur est maintenant à l’écoute sur les ports configurés pour les demandes d’accès RADIUS des clients configurés.   


## <a name="radius-client-configuration"></a>Configuration du Client RADIUS

Pour configurer le client RADIUS, suivez les instructions :

- Configurez votre solution matérielle-logicielle/serveur pour s’authentifier via RADIUS à l’adresse IP du serveur de l’authentification de plusieurs facteurs Azure, qui agisse en tant que serveur RADIUS.
- Utiliser le même secret partagé qui a été configuré précédemment.
- Configurer le délai d’attente de rayon de 30 à 60 secondes, afin qu’il est temps de valider les informations d’identification de l’utilisateur, effectuer l’authentification selon plusieurs facteur, leur réponse et de répondre à la demande d’accès RADIUS.
