<properties
   pageTitle="Vue d’ensemble de la sécurité Internet des objets | Microsoft Azure"
   description=" Azure internet de services d’éléments (IoT) offrent une large gamme de fonctions. Cet article vous aide à comprendre comment sécuriser vos solutions IoT dans Azure. "
   services="security"
   documentationCenter="na"
   authors="TomShinder"
   manager="MBaldwin"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/09/2016"
   ms.author="terrylan"/>

# <a name="internet-of-things-security-overview"></a>Vue d’ensemble de la sécurité Internet des objets

Azure internet de services d’éléments (IoT) offrent une large gamme de fonctions. Ces services de classe entreprise vous permettent de :

- Collecte de données à partir de périphériques
- Analysez le flux dans-déplacement de données
- Stocker et d’interroger les grands ensembles de données
- Visualiser les données historiques et en temps réel
- Intégration avec les systèmes back-office

Pour fournir ces fonctionnalités, les packages d’Azure IoT Suite ensemble plusieurs services Azure avec extensions personnalisées en tant que solutions préconfigurées. Ces solutions préconfigurées sont des implémentations de base de modèles de solution IoT communs qui permettant de réduire le temps de que vous prenez pour remettre vos solutions IoT. Avec les kits de développement logiciel IoT, vous pouvez personnaliser et étendre ces solutions afin de répondre à vos besoins. Vous pouvez également utiliser ces solutions comme des exemples ou modèles lorsque vous développez de nouvelles solutions IoT.

La suite Azure IoT est une solution puissante pour vos besoins IoT. Toutefois, il est d’une importance upmost que vos solutions IoT sont conçues avec la sécurité à l’esprit dès le début. En raison du nombre impressionnant de périphériques de IoT, un incident de sécurité peut rapidement devenir un événement généralisée des conséquences significatives.

Pour vous aider à comprendre comment sécuriser vos solutions IoT, nous disposons des informations suivantes.

## <a name="security-architecture"></a>Architecture de sécurité

Lors de la conception d’un système, il est important de comprendre les menaces potentielles à ce système et ajouter les défenses en conséquence, le système est conçu et architecture. Il est particulièrement important de concevoir le produit à partir du début avec la sécurité à l’esprit, comprendre comment un intrus peut être en mesure de compromettre un système vous permet de rendre les atténuations appropriées qu’étant en place depuis le début.

Vous pouvez en savoir plus sur architecture de sécurité IoT en lisant [Internet d’Architecture de sécurité de choses](../iot-suite/iot-security-architecture.md).

Cet article traite des sujets suivants :

- [La sécurité commence par un modèle de menace](../iot-suite/iot-security-architecture.md#security-starts-with-a-threat-model)
- [Sécurité dans IoT](../iot-suite/iot-security-architecture.md#security-in-iot)
- [L’Architecture de référence de IoT Azure de modélisation des menaces](../iot-suite/iot-security-architecture.md#threat-modeling-the-azure-iot-reference-architecture)

## <a name="security-from-the-ground-up"></a>Sécurité de bas en haut

Le IoT présente des défis uniques de sécurité, de confidentialité et de conformité pour les entreprises du monde entier. Contrairement à la technologie cyber traditionnel où ces problèmes sont axés sur les logiciels et la façon dont il est implémenté, IoT concerne ce qui se passe lorsque les cybercriminels et les mondes physiques convergent. IoT solutions de protection nécessite de garantir la mise en service sécurisé des périphériques, des connexions sécurisées entre ces périphériques et le nuage et protection de sécuriser les données dans le nuage pendant le traitement et le stockage. Utilisation par rapport à ces fonctionnalités, cependant, sont des périphériques à ressources limitées, la répartition géographique des déploiements et un grand nombre de périphériques au sein d’une solution.

Vous pouvez apprendre comment gérer la sécurité dans ces domaines en lisant la [sécurité Internet des objets de bas en haut](../iot-suite/securing-iot-ground-up.md).

L’article traite des sujets suivants :

- [Infrastructure sécurisée de bas en haut](../iot-suite/securing-iot-ground-up.md#secure-infrastructure-from-the-ground-up)
- [Microsoft Azure – l’infrastructure IoT sécurisée pour votre entreprise](../iot-suite/securing-iot-ground-up.md#microsoft-azure---secure-iot-infrastructure-for-your-business)

## <a name="best-practices"></a>Meilleures pratiques

Sécurisation d’une infrastructure IoT nécessite une stratégie de sécurité en profondeur rigoureuse. À partir de sécurisation des données dans le nuage, à la protection de l’intégrité des données lors de leur transit sur le réseau internet public et qui permet de configurer en toute sécurité les périphériques, chaque couche génère une meilleure garantie de sécurité de l’infrastructure globale.

Vous pouvez en savoir plus sur la sécurité de l’Internet des objets meilleures pratiques en lisant les [meilleures pratiques de sécurité Internet des objets](../iot-suite/iot-security-best-practices.md).

L’article traite des sujets suivants :

- [Fabricant de matériel IoT/intégrateur](../iot-suite/iot-security-best-practices.md#iot-hardware-manufacturerintegrator)
- [Développeurs de solutions IoT](../iot-suite/iot-security-best-practices.md#iot-solution-developer)
- [Responsable du déploiement de la solution IoT](../iot-suite/iot-security-best-practices.md#iot-solution-deployer)
- [Opérateur de solution IoT](../iot-suite/iot-security-best-practices.md#iot-solution-operator)
