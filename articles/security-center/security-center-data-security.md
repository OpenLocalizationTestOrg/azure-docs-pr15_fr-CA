<properties
   pageTitle="Sécurité des données de centre de sécurité Azure | Microsoft Azure"
   description="Ce document explique comment les données sont gérées et sauvegardées dans le centre de sécurité Azure."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/25/2016"
   ms.author="yurid"/>

# <a name="azure-security-center-data-security"></a>Sécurité des données de centre de sécurité Azure
Pour aider les clients à empêcher, détecter et répondre aux menaces, le centre de sécurité Azure collecte et traite les données concernant vos ressources Azure, y compris les informations de configuration, les métadonnées, les journaux des événements, bloquer des fichiers de vidage et bien plus encore. Nous rendre fort engagement à protéger la confidentialité et la sécurité de ces données. Microsoft respecte les directives de sécurité et de conformité strictes, depuis le codage pour l’exploitation d’un service. 

Cet article explique comment les données sont gérées et sauvegardées dans le centre de sécurité Azure.

## <a name="data-sources"></a>Sources de données

Le centre de sécurité Azure analyse les données à partir des sources suivantes :

- Services Azure : Lit les informations sur la configuration des services Azure que vous avez déployé en communiquant avec le fournisseur de ressources du service.
- Le trafic réseau : Lectures échantillonnées réseau du trafic des métadonnées à partir de l’infrastructure de Microsoft, tels que de la taille de paquet IP/port source ou de destination et protocole réseau.
- Solutions de partenaires : Collecte des alertes de sécurité à partir des solutions de partenaires intégrées, comme les pare-feu et les solutions contre les logiciels malveillants. Ces données sont stockées dans le stockage Azure le centre de sécurité, que qui se trouve actuellement aux États-Unis.
- Vos ordinateurs virtuels : Le centre de sécurité Azure peut collecter des informations de configuration et les informations sur les événements de sécurité, tels que les événements Windows et auditer les journaux, les journaux IIS, les messages syslog et les fichiers de vidage sur incident à partir de vos machines virtuelles à l’aide d’agents de collecte de données. Consultez la section « Collecte de données de gestion » ci-dessous pour plus de détails.  

En outre, des informations sur l’état de sécurité, des conseils et des alertes de sécurité sont stockées dans le stockage Azure le centre de sécurité, que qui se trouve actuellement aux États-Unis. Ces informations peuvent inclure des informations de configuration associées et les événements de sécurité collectées à partir de vos machines virtuelles en fonction des besoins pour vous fournir de l’alerte de sécurité, recommandation ou état de sécurité.

## <a name="data-protection"></a>Protection des données

**Segmentation des données**: les données sont conservées logiquement séparées sur chaque composant dans le service. Toutes les données est balisé par l’organisation. Ce balisage persiste tout au long du cycle de vie des données, et elle est appliquée au niveau de chaque couche du service. En outre, les données collectées à partir de vos ordinateurs virtuels sont stockées dans vos comptes de stockage.

**Accès aux données**: afin de fournir des recommandations de sécurité et étudier les menaces de sécurité potentielles, personnel de Microsoft peut-être accéder à des informations collectées ou analysées par des services Azure, y compris les fichiers de vidage sur incident. Fichiers de vidage sur incident et de traiter les événements de création peuvent inclure involontairement des données client ou des données à caractère personnel à partir de vos ordinateurs virtuels. Nous adhérer aux [Termes de Microsoft Online Services](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) et [Déclaration de confidentialité](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx), quel état que Microsoft ne doit pas utiliser les données clients ou dériver des informations à partir de celui-ci, à des fins commerciales de publicité ou similaires. Nous uniquement utiliser les données client en fonction des besoins pour vous fournir des services Azure, y compris à des fins compatibles avec la fourniture de ces services. Vous conservez tous les droits sur les données du client.

**Utilisent de données**: Microsoft utilise des modèles et des menaces, vu les différents utilisateurs pour améliorer nos capacités de détection et de prévention ; nous le faire conformément aux engagements de confidentialité décrits dans notre [Déclaration de confidentialité](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx).

**Emplacement des données**: un compte de stockage est spécifié pour chaque région où les ordinateurs virtuels sont en service. Cela vous permet de stocker des données dans la même région que la machine virtuelle à partir de laquelle les données sont collectées. Ces données, y compris les fichiers de vidage sur incident, être stockées de façon persistante dans votre compte de stockage. Le service stocke également des informations sur les alertes de sécurité, notamment les alertes de l’état de sécurité, des conseils et des solutions de partenaires intégrées dans le stockage Azure le centre de sécurité, que qui se trouve actuellement aux États-Unis.

## <a name="managing-data-collection-from-virtual-machines"></a>Gestion de la collecte de données à partir d’ordinateurs virtuels

Lorsque vous choisissez d’activer le centre de sécurité Azure, collecte des données est activée pour chacun de vos abonnements. Vous pouvez désactiver la collecte de données dans la section « Stratégie de sécurité » de votre tableau de bord du centre de sécurité Azure. Lors de la collecte de données est activée, centre de sécurité Azure dispositions l’Agent de surveillance Azure sur tout prise en charge des machines virtuelles et nouveaux est créés. Analyse de l’extension du contrôle de la sécurité Azure pour divers liés à la sécurité des configurations et des événements dans [Event Tracing for Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW) trace. En outre, le système d’exploitation déclenche des événements de journal des événements au cours de l’exécution de la machine. Voici quelques exemples de ce type de données : type de système d’exploitation et de version, le fonctionnement des journaux système (journaux d’événements Windows), exécution de processus, nom de l’ordinateur, les adresses IP, consigné dans utilisateur et ID de client. L’Agent de surveillance Azure lit les entrées de journal des événements et ETW trace et les copie dans votre compte de stockage pour l’analyse. 

Un compte de stockage est spécifié pour chaque région dans laquelle vous avez des ordinateurs virtuels en cours d’exécution, où les données collectées à partir des ordinateurs virtuels dans ce même région est stockée. Cela rend facile à conserver les données dans la même zone géographique à des fins données relevant de la souveraineté et de la confidentialité. Vous pouvez configurer des comptes de stockage pour chaque région dans la section « Stratégie de sécurité » de votre tableau de bord du centre de sécurité Azure.

L’Agent de surveillance Azure copie également les fichiers de vidage sur incident à votre compte de stockage.  Centre de sécurité Azure collecte éphémères copies de vos fichiers de vidage sur incident et les analyse pour preuve de compromis ayant abouti et les tentatives d’attaque.  Le centre de sécurité Azure effectue cette analyse au sein de la même région géographique, comme le compte de stockage et supprime les copies éphémères lorsque l’analyse est terminée.

Vous pouvez désactiver la collecte de données à partir d’ordinateurs virtuels à tout moment, ce qui supprimera tous les Agents de surveillance déjà installé par le centre de sécurité Azure.


## <a name="see-also"></a>Voir aussi

Dans ce document, vous avez appris comment les données sont gérées et sauvegardées dans le centre de sécurité Azure. Pour en savoir plus sur le centre de sécurité Azure, consultez :

- [Guide des opérations et la planification de la sécurité Azure centre](security-center-planning-and-operations-guide.md) : Apprenez à planifier et à comprendre les considérations de conception à adopter le centre de sécurité Azure.
- [Sécurité contrôle d’état dans le centre de sécurité Azure](security-center-monitoring.md) , découvrez comment surveiller la santé de vos ressources Azure
- [Gérer et répondre aux alertes de sécurité dans le centre de sécurité Azure](security-center-managing-and-responding-alerts.md) , découvrez comment gérer et répondre aux alertes de sécurité
- [Solutions de contrôle partenaire avec le centre de sécurité Azure](security-center-partner-solutions.md) : Apprenez à surveiller l’état de santé de vos solutions de partenaires.
- [Forum aux questions sur Centre de sécurité Azure](security-center-faq.md) : Forum aux questions sur l’utilisation du service de recherche
- [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/) : trouver des billets de blog sur la sécurité Azure et de conformité
