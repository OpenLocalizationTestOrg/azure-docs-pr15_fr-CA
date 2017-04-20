<properties
    pageTitle="Azure Active Directory hybride identité considérations de conception - déterminer les besoins de synchronisation de répertoire | Microsoft Azure"
    description="Identifier les exigences sont nécessaires pour la synchronisation de tous les utilisateurs entre on = locaux et cloud pour l’entreprise."
    documentationCenter=""
    services="active-directory"
    authors="billmath"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
    ms.date="08/08/2016"
    ms.author="billmath"/>

# <a name="determine-directory-synchronization-requirements"></a>Déterminer les besoins de synchronisation d’annuaire
Synchronisation consiste à fournir aux utilisateurs une identité dans le nuage en fonction de leur identité sur site. Ils utiliseront synchronisée de compte pour l’authentification ou l’authentification fédérée, ou non les utilisateurs doivent toujours disposer d’une identité dans le nuage.  Cette identité devra être maintenu et mis à jour périodiquement.  Les mises à jour peuvent prendre différentes formes, des changements de titre pour les modifications de mot de passe.  

Commencez par évaluer les organisations locales identité solution et utilisateurs requis. Cette évaluation est importante de définir les exigences techniques de comment les identités utilisateur seront créées et mis à jour dans le nuage.  Pour la plupart des organisations, Active Directory est sur site et sera le répertoire local que les utilisateurs seront en synchronisée à partir, cependant dans certains cas il ne sera pas le cas.  

Veillez à répondre aux questions suivantes :


- Vous avez une forêt Active Directory, plusieurs ou aucun ?
 - Vous les répertoires AD Azure combien sera synchronisation à ?
 
    1. Vous utilisez le filtrage ?
    2. Vous disposez de plusieurs serveurs Azure Connect de publicité prévues ?
  
- Disposez-vous actuellement une synchronisation outil sur site ?
  - Si Oui, ne les utilisateurs si les utilisateurs disposent d’une répertoire virtuelle/intégration d’identités ?
- Vous avez tout autre répertoire local que vous voulez synchroniser (par exemple, LDAP Directory, base de données des ressources humaines, etc.) ?
  - Vous allez faire tout GALSync ?
  - Quel est l’état actuel de l’UPN dans votre organisation ? 
  - Disposez-vous d’un répertoire autre que les utilisateurs s’authentifient ?
  - Votre société utilise-t-elle des Microsoft Exchange ?
    - Prévoyez d’avoir un déploiement d’exchange hybride ?

Maintenant que vous avez une idée sur vos besoins en matière de synchronisation, vous devez déterminer quel outil est celui qui convient pour répondre à ces exigences.  Microsoft fournit plusieurs outils pour effectuer la synchronisation et l’intégration d’annuaire.  Consultez le [tableau comparatif des outils intégration hybride identité directory](active-directory-hybrid-identity-design-considerations-tools-comparison.md) pour plus d’informations. 
   
Maintenant que vous avez vos exigences en matière de synchronisation et de l’outil qui réalise cela pour votre société, vous devez évaluer les applications qui utilisent ces services d’annuaire. Cette évaluation est importante de définir les exigences techniques pour l’intégration de ces applications dans le nuage. Veillez à répondre aux questions suivantes :

- Ces applications sont déplacées vers le nuage et utiliser ce répertoire ?
- Y a-t-il des attributs spéciaux qui doivent être synchronisées vers le nuage afin que ces applications peuvent les utiliser correctement ?
- Ces applications devront être réécrites pour tirer parti de l’authentification de nuage ?
- Ces applications continueront à live sur site, tandis que les utilisateurs d’y accéder à l’aide de l’identité du nuage ?

Vous devez également déterminer la synchronisation d’annuaire de configuration requise et les contraintes de sécurité. Cette évaluation est importante obtenir une liste des exigences qui seront nécessaires pour créer et gérer les identités des utilisateurs dans le nuage. Veillez à répondre aux questions suivantes :

- Où le serveur de synchronisation se trouver ?
- Il sera joint au domaine ?
- Le serveur se trouvera dans un réseau restreint derrière un pare-feu, par exemple une zone DMZ ?
  - Vous pourrez ouvrir les ports de pare-feu requis pour prendre en charge la synchronisation ?
- Avez-vous un plan de récupération d’urgence pour le serveur de synchronisation ?
- Vous avez un compte avec les autorisations correctes pour toutes les forêts que vous souhaitez synchroniser avec ?
 - Si votre entreprise ne connaît pas la réponse à cette question, consultez la section « Autorisations pour la synchronisation de mot de passe » dans l’article [installer le Service de synchronisation Active Directory Azure](https://msdn.microsoft.com/library/azure/dn757602.aspx#BKMK_CreateAnADAccountForTheSyncService) et déterminer si vous disposez déjà d’un compte disposant de ces autorisations, ou si vous devez en créer un.
- Si vous disposez de synchronisation des forêts multiples s’est le serveur de synchronisation en mesure d’obtenir pour chaque forêt ?
 
>[AZURE.NOTE]
Veillez à prendre des notes de chaque réponse et de comprendre le raisonnement derrière la réponse. [Exigences en matière de réponse aux incidents de déterminer](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md) iront sur les options disponibles. En ayant répondu à ces questions que vous sélectionnez option les adaptée à votre entreprise a besoin.

## <a name="next-steps"></a>Étapes suivantes
[Déterminer les besoins d’authentification à plusieurs facteurs](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md)

## <a name="see-also"></a>Voir aussi
[Vue d’ensemble des considérations relatives à la conception](active-directory-hybrid-identity-design-considerations-overview.md)
