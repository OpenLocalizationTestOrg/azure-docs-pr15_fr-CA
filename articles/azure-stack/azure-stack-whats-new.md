<properties
    pageTitle="Quelles sont les nouveautés dans la pile d’Azure | Microsoft Azure"
    description="Quelles sont les nouveautés dans la pile d’Azure"
    services="azure-stack"
    documentationCenter=""
    authors="HeathL17"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="helaw"/>

# <a name="whats-new-in-azure-stack-technical-preview-2"></a>Quelles sont les nouveautés dans Azure pile technique Preview 2
Cette version fournit de nouvelles fonctionnalités pour les locataires et les administrateurs.

## <a name="network"></a>Réseau   
   - [les IDN](azure-stack-understanding-dns-in-tp2.md) fournit le réseau interne d’inscription et de résolution de nom de domaine (DNS) sans une infrastructure DNS supplémentaire.
   - [Les passerelles de réseau virtuel](azure-stack-create-vpn-connection-one-node-tp2.md) proposent des options de connectivité VPN aux ressources Azure ou sur site.
   - Les itinéraires définis par l’utilisateur peut router le trafic réseau via le pare-feu, de sécurité, équipements et autres services.
   - Vous pouvez créer des ressources réseau à partir du marché.   

## <a name="storage"></a>Stockage
 - [Files d’attente Azure](https://msdn.microsoft.com/library/dd179353.aspx) activer la messagerie fiable et permanent de service.
 - Données de performance [analytique du stockage](https://msdn.microsoft.com/library/azure/hh343270.aspx) capture de stockage. Vous pouvez utiliser ces données pour suivre les demandes, analyser les tendances d’utilisation et diagnostiquer les problèmes avec votre compte de stockage.
 - Stockage des objets BLOB prend en charge [l’opération de bloc d’ajout](https://msdn.microsoft.com/library/azure/mt427365.aspx).
 - Prise en charge de compte Premium API de stockage.
 - Client prise en charge du service de stockage pour les outils et les kits de développement SDK, comme CLI d’Azure, PowerShell, .NET, Python et Java SDK communs. 
 - [Signature de stockage compte partagés accès](https://msdn.microsoft.com/library/azure/mt584140.aspx) permettent une délégation granulaire de l’accès à vos services de stockage sans avoir à partager votre clé de compte complet.  
 - Services de stockage maintenant utilisent le [Groupe les comptes de Service](https://technet.microsoft.com/library/hh831477.aspx) d’une sécurité renforcée avec des coûts de gestion faibles.
 - Vous pouvez récupérer des clients inutilisés ressources à la demande.
 - Durée de rétention de compte de stockage supprimée est configurable.
 - Vous pouvez récupérer des comptes de stockage des clients supprimés.

## <a name="compute"></a>Calculer
- Vous pouvez libérer des machines virtuelles.
- Vous pouvez redéployer des extensions de machine virtuelle pour la configuration et la résolution des problèmes liés à des fins de gestion.
- Vous pouvez redimensionner des disques de l’ordinateur virtuel.
- Ordinateurs virtuels peuvent posséder plusieurs interfaces réseau.

### <a name="portal-experience"></a>Utilisation du portail
 - Les régions de pile Azure sont une unité logique d’échelle et de gestion au sein de la pile d’Azure. Dans cet aperçu, vous pouvez afficher des informations sur des services tels que le calcul, réseau, stockage et par région.
 - Vous pouvez maintenant afficher un aperçu de l’interface de [azure-pile-updates.md] (mises à jour).

## <a name="key-vault"></a>Chambre forte de clé
- [Coffre-fort de clé dans la pile d’Azure](azure-stack-kv-intro.md) fournit une gestion sécurisée de vos clés et les mots de passe pour des applications de cloud.
- Vous pouvez auditer et surveiller l’utilisation par les applications et les ordinateurs virtuels de la clé.

## <a name="billing-and-usage"></a>Facturation et utilisation
 - [Facturation et consommation API](azure-stack-billing-and-chargeback.md) exposent les données sur la façon dont vos services sont consommés.  
 - Fournisseurs de délégué activer des revendeurs de proposer les services Azure pile à leurs clients.

## <a name="monitoring-and-health"></a>Surveillance et la santé
 - Nouvelles fonctionnalités disponibles dans le portail et les API de contrôle vous permettent d’afficher et de gérer les alertes sur votre environnement de manière proactive.  

## <a name="next-steps"></a>Étapes suivantes
- [Comprendre l’Architecture de pile Azure POC](azure-stack-architecture.md)      
- [Comprendre les conditions préalables au déploiement](azure-stack-deploy.md)
- [Déployer la pile Azure](azure-stack-run-powershell-script.md)

  
