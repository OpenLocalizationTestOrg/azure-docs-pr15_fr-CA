
<properties 
   pageTitle="Notes de mise à jour de SDK Azure pour .NET 2.7 et .NET 2.7.1" 
   description="Notes de mise à jour de SDK Azure pour .NET 2.7 et .NET 2.7.1" 
   services="app-service\web" 
   documentationCenter=".net" 
   authors="Juliako" 
   manager="erikre" 
   editor=""/>

<tags
   ms.service="app-service"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="10/17/2016"
   ms.author="juliako"/>

# <a name="azure-sdk-for-net-27-and-net-271-release-notes"></a>Notes de mise à jour de SDK Azure pour .NET 2.7 et .NET 2.7.1

##<a name="overview"></a>Vue d’ensemble

Ce document contient les notes de publication pour le SDK Azure pour .NET 2.7 mise à jour. 

Le document contient également les notes de publication pour le SDK Azure pour .NET 2.7.1 version.

Azure SDK 2.7 est uniquement pris en charge dans Visual Studio 2015 et Visual Studio 2013. [Azure SDK 2.6](https://azure.microsoft.com/downloads/) est le dernier Kit de développement logiciel pour Visual Studio 2012.

Pour obtenir des informations détaillées sur cette version, reportez-vous à la section [annonce Azure SDK 2.7 valider](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/) et [valider d’annonce d’Azure SDK 2.7.1](http://go.microsoft.com/fwlink/?LinkId=623850).

##<a name="azure-sdk-for-net-27"></a>Azure SDK pour .NET 2.7

###<a name="sign-in-improvements-for-visual-studio-2015"></a>Signer dans les améliorations apportées à Visual Studio 2015

Azure 2.7 SDK pour Visual Studio 2015 prend en charge les nouvelles fonctionnalités de gestion d’identité dans Visual Studio 2015.  Cela inclut la prise en charge des comptes accès Azure via le rôle de base de contrôle d’accès, les fournisseurs de solutions de Cloud, DreamSpark et autres types de compte et d’abonnement.

Les améliorations de connexion incluses dans Azure SDK 2.7 sont uniquement disponibles dans Visual Studio 2015. Prise en charge de Visual Studio 2013 est inclus dans le Kit de développement logiciel Azure 2.7.1.


###<a name="mobile-sdk"></a>Kit de développement mobile

Mise à jour des modèles **d’Applications Mobile** afin de refléter le processus de [package NuGet](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) et le programme d’installation plus récent.

###<a name="service-bus"></a>Bus des services 

Général des correctifs de bogues et améliorations. Pour des détails sur les fonctionnalités et les mises à jour, veuillez vous reporter aux notes de version de la dernière [NuGet de Bus de Service](http://www.nuget.org/packages/WindowsAzure.ServiceBus/).

###<a name="hdinsight-tools"></a>Outils de HDInsight 

Dans cette version, les mises à jour suivantes ont été apportées. Ces mises à jour sont dans l’aperçu. Pour plus d’informations, consultez [le blog](http://go.microsoft.com/fwlink/?LinkId=619108).

- Graphiques de ruche pour ruche sur Tez travaux
- Prise en charge complète de la ruche de IntelliSense de DML
- Prise en charge des modèles de porc
- Modèles de Storm pour des services Azure

####<a name="breaking-changes"></a>Modifications avec rupture

- Ancien projet **tempête** doit être mis à niveau lors de l’utilisation de cette version des outils. Pour plus d’informations, consultez [le blog](http://go.microsoft.com/fwlink/?LinkId=619108).
- Web de Visual Studio Express n’est plus pris en charge. Pour plus d’informations, consultez [le blog](http://go.microsoft.com/fwlink/?LinkId=619108).

###<a name="azure-app-service-tools"></a>Outils de Service d’application Azure

Dans cette version, les mises à jour suivantes ont été apportées aux Extensions d’outils Web. Pour plus d’informations, consultez [le](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/) blog. 

- Prise en charge des comptes DreamSpark ajoutés
- Modification complète Azure Tools prises pour soutenir les nouvelles API de gestion de ressources Azure
- Prise en charge de Service d’application Azure ajouté à [l’Explorateur de nuage](#cloud_explorer)

####<a name="known-issues"></a>Problèmes connus

Nœuds d’emplacement de déploiement d’application Web ne s’affichent pas sous le nœud connecteurs dans l’Explorateur de serveurs, et ne se chargent pas noeuds emplacement de déploiement Web App sous Cloud Explorer. Ce problème a été résolu et prêt pour la prochaine version du Kit de développement logiciel. 


###<a name="cloud_explorer"></a>Explorateur de cloud pour Visual Studio 2015

Azure SDK 2.7 inclut Cloud Explorer pour Visual Studio 2015 qui vous permet d’afficher vos ressources Azure, examiner leurs propriétés et effectuer des actions de développeur de clé à partir de dans Visual Studio. 

Explorateur de nuage prend en charge les éléments suivants :

- Groupe de ressources et le Type de ressource de vues de vos ressources Azure 
- Rechercher des ressources par nom (disponible dans la vue du Type de ressource)
- Prise en charge des abonnements et des ressources qui ont le rôle base de contrôle d’accès (RBAC) appliqué 
- Panneau d’Action intégré qui affiche destinés aux développeurs des actions spécifiques pour les ressources sélectionnées. Par exemple : joindre le débogueur distant pour les Machines virtuelles créées à l’aide de la pile du Gestionnaire de ressources, afficher des données de diagnostic pour Machines virtuelles etc..
- Panneau de propriétés intégrée qui indique généralement nécessaires au cours du développement/test de propriétés destinés aux développeurs 
- Commutation rapide du compte à utiliser lors de l’énumération des ressources (utilisez la commande paramètres de barre d’outils) 
- Filtrage des abonnements à utiliser lors de l’énumération des ressources (utilisez la commande paramètres de barre d’outils) 
- Liens de profondes sur le portail Azure pour la gestion des ressources et des groupes de ressources 
 
 
###<a name="azure-resource-manager-tools"></a>Outils de gestionnaire de ressources Azure 

Les outils du Gestionnaire de ressources Azure ont été mis à jour pour fonctionner avec nouveaux types d’abonnement et de contrôle de l’accès basé sur rôle (RBAC).  Avec ces modifications offre la possibilité d’utiliser de nouveaux comptes de stockage, en plus du stockage classique, pour stocker les artefacts pendant le déploiement.  

Si vous utilisez un projet du groupe de ressources Azure à partir d’une version précédente du Kit de développement avec le Kit de développement logiciel 2.7, un nouveau script de déploiement est nécessaire pour déployer à l’aide d’un nouveau compte de stockage au lieu de stockage classique.  Vous serez invité avant que des modifications sont apportées à votre projet pour ajouter le nouveau script.  L’ancien script est renommé et vous devrez apporter manuellement des modifications au nouveau script.
 
 
###<a name="storage-explorer-tools"></a>Outils de l’Explorateur de stockage 

- Prise en charge pour l’affichage d’ajouter les objets BLOB. Plus d’infos dans [ce billet de blog](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/04/13/introducing-azure-storage-append-blob.aspx). 
- Prise en charge pour l’affichage des comptes de stockage de la prime à l’Explorateur de serveurs. Explorateur de serveurs n’affiche que les objets BLOB de page pour les comptes de stockage prime qu’ils sont le seul type pris en charge pour les comptes de stockage premium.

###<a name="azure-data-factory-tools-for-visual-studio"></a>Outils usine de données Azure pour Visual Studio 

Présentation des **Outils d’usine de données Azure** pour Visual Studio. Vous trouverez ci-dessous les fonctionnalités activées. Consultez [ce blog](http://go.microsoft.com/fwlink/?LinkId=617530) pour plus d’informations.

- **Création de modèle de base**: sélectionnez utiliser la casse en fonction de modèles, modèles de déplacement des données ou des modèles de traitement de données à déployer une solution d’intégration des données de bout en bout et commencez à travailler rapidement sur la fabrique de données. 
- **Intégration avec l’Explorateur de solutions pour la création et le déploiement des entités de données usine**: créer et déployer des pipelines et les entités associées en tant que projets de Visual Studio. 
- **Intégration avec diagramme afficher visual interaction lors de la programmation**: Créez visuellement de pipelines et les groupes de données à l’aide de la vue schéma. 
- **Intégration avec l’Explorateur de serveurs pour la navigation et d’interaction avec les entités déjà déployées**: tirer parti de l’Explorateur de serveurs pour les fabriques de données déjà déployée de parcourir et d’entités correspondantes. Importer une fabrique de données déployés ou toute entité (Pipeline, services liés, les groupes de données) dans votre projet. 
- **Modification de JSON avec validation de schéma et intellisense enrichi**: efficace de configurer et de modifier des documents JSON des entités de données usine avec validation puissante d’intellisense et le schéma 
- **Dans plusieurs environnements et publication**: publication créé des pipelines à l’environnement de production, de test ou de développement en créant des fichiers de configuration distincts pour chaque environnement.
- **Prise en charge du traitement des données de base de porc, ruche et .net**: prise en charge pour les porcs et la ruche de Scripts dans le projet de Data Factory. Prise en charge de référencer un projet C# de gestion .net activité.

##<a name="azure-sdk-for-net-271"></a>Azure SDK pour .NET 2.7.1

La section suivante contient des mises à jour qui ont été introduites avec le SDK Azure pour .NET 2.7.1 version.
###<a name="hdinsight-tools"></a>Outils de HDInsight 

Pour plus amples d’informations sur les mises à jour des outils de HDInsight, consultez [le blog](http://go.microsoft.com/fwlink/?LinkId=623831).

- Ruche de travail opérateur affichage (nouveauté)

    Pour vous aider à comprendre votre requête ruche mieux, la fonctionnalité d’affichage d’opérateur la ruche a été ajoutée. Pour voir tous les opérateurs à l’intérieur d’un sommet, double-cliquez sur les sommets du graphique travail. Pour afficher plus de détails d’un opérateur spécifique, pointez sur cet opérateur.
- Marqueur d’erreur ruche (nouveauté)

    Pour vous permettre d’afficher les erreurs de grammaire instantanément, la fonctionnalité de marque d’erreur de la ruche a été ajoutée. En outre, les messages d’erreur ont été améliorés et vous pouvez maintenant voir instantanément les fautes de grammaire détaillées (avant cette version, vous deviez envoyer un script ruche du cluster et attendez pendant un certain temps avant d’obtenir plus d’informations sur le message d’erreur).  
- Graphique de topologie Storm (nouveauté)

    Il est très important de visualisation lorsque vous souhaitez savoir si votre topologie fonctionne comme prévu. Dans cette version, nous avons ajouté la visualisation de graphes de tempête. Vous pouvez visualiser les mesures importantes pour votre topologie (par exemple, une couleur indique météo certains un boulon est « occupé » ou non). Vous pouvez également double-cliquer sur le boulon/BEC VERSEUR pour afficher plus de détails.

- Prise en charge de clusters HDInsight qui ont été créés dans le portail Azure (une résolution de bogue)

    Vous pouvez désormais utiliser Visual Studio pour afficher et soumettre des travaux à tous les clusters de votre HDInsight, quel que soit l’où le cluster ont été créés.

- Plus de prise en charge d’IntelliSense et de chargement plus rapide de la ruche métadonnées (amélioration)

    Nous avons amélioré l’IntelliSense en ajoutant plus de suggestions conviviaux pour l’utilisateur. Par exemple, les alias de table peuvent maintenant également proposé dans IntelliSense afin de pouvoir écrire plus facilement votre requête. En outre, nous avons amélioré les métadonnées de la ruche du chargement de sorte qu’il se contentera de prendre plusieurs secondes pour répertorier toutes les bases de données, les tables et les colonnes de votre metastore de la ruche.

Pour plus amples d’informations sur les mises à jour des outils de HDInsight, consultez [le blog](http://go.microsoft.com/fwlink/?LinkId=623831).

###<a name="improvements-in-visual-studio-2013"></a>Améliorations apportées à Visual Studio 2013

- Azure SDK 2.7.1 permet à Visual Studio 2013 accéder aux comptes d’Azure et abonnements via Dreamspark, fournisseurs de solutions de Cloud et contrôle d’accès rôle.
- Avec Azure SDK 2.7.1, la nouvelle fenêtre outil Cloud Explorer est désormais également disponible dans Visual Studio 2013.

###<a name="known-issues"></a>Problèmes connus

L’installation de la 2.6 SDK Azure ou 2.7.1 pour 2013 de communauté de Visual Studio sur un non - anglaise du système d’exploitation affiche un avertissement que les ressources en anglais et non anglaises de Visual Studio peuvent ne pas correspondre. Cet avertissement peut être ignoré en toute sécurité. Il se produira uniquement si l’ordinateur ne contenait pas l’installation préalable de 2013 de communauté de Visual Studio et que vous installez le Kit de développement sur un non - anglaise du système d’exploitation. L’avertissement s’affiche une fois le pack de langue s’applique les ressources RTM de Visual Studio, mais avant d’appliquer la mise à jour 4. Le pack de langue continuer et terminer l’application de la version 4 de mise à jour du contenu du pack de langue permettra de faire disparaître l’avertissement.

LightSwitch projets ne sont pas compatible avec cette version. Ce problème sera résolu avec la prochaine version du Kit de développement logiciel.

##<a name="also-see"></a>Voir aussi
[Azure SDK 2.7.1 post d’annonce](http://go.microsoft.com/fwlink/?LinkId=623850)

[Publication d’annonce Azure SDK 2.7](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/)

[Prise en charge et les informations de retraite pour le SDK Azure pour .NET et API](https://msdn.microsoft.com/library/azure/dn479282.aspx/)
