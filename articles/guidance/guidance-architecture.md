
<properties
   pageTitle="Conseils Azure | modèles et pratiques | Microsoft Azure"
   description="Architectures de référence Azure"
   services=""
   documentationCenter="na"
   authors="bennage"
   manager="marksou"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="christb"/>

# <a name="azure-reference-architectures"></a>Architectures de référence Azure

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

_Ce contenu est en cours de développement actif. Il est utile de nos jours, afin que nous apportons disponible pour l’aperçu. Nous apprécions vos commentaires._

Nos architectures de référence sont organisées par scénario, de plusieurs architectures connexes regroupés.
Chaque architecture individuel propose des pratiques recommandées et des instructions, mais aussi un composant exécutable qui intègre les recommandations.
La plupart des architectures sont progressives ; création sur des architectures précédentes ayant des exigences moins.

## <a name="designing-your-infrastructure-for-resiliency"></a>Conception de l’infrastructure de reprise

Cette série commence par des pratiques recommandées pour la configuration de machine virtuelle optimale et se termine par un déploiement de plusieurs région avec basculement sur incident.

- [Sous une VM de Windows Azure](guidance-compute-single-vm.md)
- [Exécuter une machine virtuelle de Linux sur Azure](guidance-compute-single-vm-linux.md)
- [Exécution de plusieurs ordinateurs virtuels pour l’évolutivité et la disponibilité](guidance-compute-multi-vm.md)
- [Machines virtuelles de Windows en cours d’exécution pour une architecture à N niveaux](guidance-compute-n-tier-vm.md)
- [Exécution des VM de Linux pour une architecture à N niveaux](guidance-compute-n-tier-vm-linux.md)
- [Exécute les ordinateurs virtuels de Windows dans plusieurs régions pour une haute disponibilité](guidance-compute-multiple-datacenters.md)
- [Exécute les ordinateurs virtuels de Linux dans plusieurs régions pour une haute disponibilité](guidance-compute-multiple-datacenters-linux.md)

## <a name="connecting-your-on-premises-network-to-azure"></a>Connexion de votre réseau local vers Azure

Cette série commence en montrant comment connecter votre réseau existant vers Azure. Puis, il se développe pour couvrir les besoins de disponibilité et de sécurité.

- [Implémentation d’une architecture de réseau hybride avec Azure et VPN sur site](guidance-hybrid-network-vpn.md)
- [Implémentation d’une architecture de réseau hybride avec Azure ExpressRoute](guidance-hybrid-network-expressroute.md)
- [Implémentation d’une architecture de réseau hautement disponible hybride](guidance-hybrid-network-expressroute-vpn-failover.md)

## <a name="securing-your-hybrid-network"></a>Sécurisation de votre réseau hybride

Cette série traite des pratiques sur la création de zone DMZ dans Azure pour des connexions sécurisées en provenance de votre centre de données en local et à Internet.

- [Mise en œuvre d’un réseau de périmètre entre Azure et votre centre de données sur site](guidance-iaas-ra-secure-vnet-hybrid.md)
- [Mise en œuvre d’un réseau de périmètre entre Internet et les Azure](guidance-iaas-ra-secure-vnet-dmz.md)

## <a name="providing-identity-services"></a>Fournir des services d’identité

Cette série commence par illustrer l’Azure Active Directory permet de fournir une authentification utilisateur dans Azure. Puis, il se développe pour recouvrir les scénarios complexes en étendant votre infrastructure ADDS sur Azure et l’utilisation d’ADFS pour la délégation.

- [Implémentation d’Azure Active Directory](./guidance-identity-aad.md)
- [Extension des Services d’annuaire Active Directory (ADDS) pour Azure](./guidance-identity-adds-extend-domain.md)
- [Création d’une forêt de ressources des Services de répertoire Active Directory (ADDS) dans Azure](./guidance-identity-adds-resource-forest.md)
- [Mise en œuvre d’Active Directory Federation Services (ADFS) dans Azure](./guidance-identity-adfs.md)

## <a name="architecting-scalable-web-application-using-azure-paas"></a>Architecture d’application web évolutives à l’aide d’Azure PaaS

Cette série traite des recommandations pour la construction d’applications web évolutives et hautement disponibles. 

- [Application web de base](guidance-web-apps-basic.md)
- [Amélioration de l’évolutivité d’une application web](guidance-web-apps-scalability.md)
- [Application Web à haute disponibilité](guidance-web-apps-multi-region.md)
