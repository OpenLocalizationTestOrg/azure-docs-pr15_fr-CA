<properties
   pageTitle="Internet des meilleures pratiques de sécurité choses | Microsoft Azure"
   description="L’article fournit une liste curated de Microsoft Internet des meilleures pratiques de sécurité des choses et des recommandations générales."
   services="security"
   documentationCenter="na"
   authors="TomShinder"
   manager="StevenPo"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/25/2016"
   ms.author="yurid"/>

# <a name="internet-of-things-security-best-practices"></a>Internet des meilleures pratiques de sécurité des éléments

Sécurisation de l’infrastructure de l’Internet des objets (IoT) est une entreprise critique pour toute personne travaillant avec les solutions IoT. En raison du nombre de périphériques concernés et de la nature distribuée de ces périphériques, l’impact d’un événement de sécurité liées à compromettre des millions de périphériques de IoT est non triviale et peut avoir des répercussions très répandue.

Pour cette raison, IoT sécurité a besoin d’une approche de la sécurité en profondeur. Les données doivent être en sécurité dans le nuage et lorsqu’il se déplace sur des réseaux publics et privés. Les méthodes doivent être en place en toute sécurité mettre en service les appareils IoT eux-mêmes. Chaque couche, périphérique, réseau, vers le back-end de cloud a besoin de garanties de sécurité renforcée.

IoT meilleures pratiques peuvent être classées de la manière suivante :

- IoT le fabricant ou l’intégrateur
- Développeurs de solutions IoT
- Responsable du déploiement de la solution IoT
- Opérateur de solution IoT

Cet article résume [Internet de choses meilleures pratiques de sécurité](../iot-suite/iot-security-best-practices.md). Reportez-vous à cet article pour obtenir des informations plus détaillées.

## <a name="iot-hardware-manufacturer-or-integrator"></a>IoT le fabricant ou l’intégrateur

Suivez les méthodes conseillées ci-dessous si vous êtes un fabricant de matériel IoT ou une intégration de matériel :

- **Matériel de portée pour la configuration minimale requise**: la conception du matériel doit inclure les caractéristiques minimales requises pour le fonctionnement du matériel et rien de plus. 
- **Rendre les falsifications de matériel**: création de mécanismes pour détecter les falsifications physique du matériel, comme l’ouverture du capot du périphérique, la suppression d’une partie de l’équipement, etc.. 
- **Construire autour de matériel sécurisé**: si les [COGS](https://en.wikipedia.org/wiki/Cost_of_goods_sold) permettent, générer des fonctionnalités de sécurité comme le stockage sécurisé et crypté et la fonctionnalité de démarrage de base de Module de plateforme sécurisée TPM.
- **Vérifiez les mises à jour sécurisées**: mise à jour pendant la durée de vie de l’équipement est inévitable.

## <a name="iot-solution-developer"></a>Développeurs de solutions IoT

Suivez les méthodes conseillées ci-dessous si vous êtes un développeur de solutions IoT :

- **Méthodologie de développement de logiciels sécurisés suivantes**: développement de logiciels sécurisés nécessite des termes spécialisé de sécurité depuis le commencement du projet à sa mise en œuvre, de test et de déploiement.
- **Choisir les logiciels open source avec précaution**: logiciel open source permet de développer rapidement des solutions.
- **Intégrer avec précaution**: la plupart des failles de sécurité du logiciel existent à la limite de bibliothèques et d’API. 

## <a name="iot-solution-deployer"></a>Responsable du déploiement de la solution IoT

Suivez les méthodes conseillées ci-dessous si vous êtes un responsable du déploiement de la solution IoT :

- **Déploiement de matériel en toute sécurité**: IoT déploiements peuvent nécessiter un matériel pour être déployé dans des emplacements non sécurisés, comme dans les espaces publics ou les paramètres régionaux qui fonctionne sans surveillance.
- **Protéger les clés d’authentification**: au cours du déploiement, chaque périphérique requiert l’ID de périphérique et associés à des clés d’authentification générés par le service en nuage. Conservez ces clés physiquement même après le déploiement. Toute clé compromise peut être utilisée par un dispositif malveillant d’usurper l’identité d’un périphérique existant.

## <a name="iot-solution-operator"></a>Opérateur de solution IoT

Suivez les méthodes conseillées ci-dessous si vous êtes un opérateur de solution IoT :

- **Maintenir les systèmes à jour**: Assurez-vous que tous les pilotes de périphériques et de systèmes d’exploitation de périphérique sont mis à jour avec les dernières versions. 
- **Protection contre les pirates**: si le système d’exploitation permet, placez les dernières fonctionnalités de protection contre les virus et les logiciels anti-malware sur chaque système d’exploitation de périphérique. 
- **Audit fréquemment**: IoT infrastructure d’audit de problèmes est clé lors de la réponse aux incidents de sécurité liés à la sécurité.
- **Protégez physiquement l’infrastructure IoT**: les attaques de sécurité pires contre IoT infrastructure sont lancés à l’aide de l’accès physique aux périphériques.
- **Informations d’identification de protéger cloud**: nuage d’identification utilisée pour configurer et exploiter un déploiement IoT est probablement le moyen le plus simple d’accéder et de compromettre un système IoT. 
