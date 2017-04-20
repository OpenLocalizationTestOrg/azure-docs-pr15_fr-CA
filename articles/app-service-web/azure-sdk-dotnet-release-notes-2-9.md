<properties 
   pageTitle="Notes de mise à jour de SDK Azure pour .NET 2.9" 
   description="Notes de mise à jour de SDK Azure pour .NET 2.9" 
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

# <a name="azure-sdk-for-net-29-release-notes"></a>Notes de mise à jour de SDK Azure pour .NET 2.9

##<a name="overview"></a>Vue d’ensemble

Ce document contient les notes de publication pour le SDK Azure pour .NET 2.9 mise à jour. 

Pour obtenir des informations détaillées sur les mises à jour dans cette version, consultez l' [annonce Azure SDK 2.9 valider](https://azure.microsoft.com/blog/announcing-visual-studio-azure-tools-and-sdk-2-9/).

## <a name="azure-sdk-29-for-visual-studio-2015-update-2-and-visual-studio-15-preview"></a>Afficher un aperçu du SDK Azure 2.9 « 15 » mise à jour de Visual Studio 2015 2 et Visual Studio
 
Cette mise à jour inclut les correctifs de bogue suivants :

- Questions sur le reste de génération de Client API dans apparaît comme le nom du dossier de génération de code ou le nom de l’espace de noms dans lequel la chaîne « Type inconnu » déposé dans le code généré.
- Problème relatif à WebJobs prévu dans lequel les informations d’authentification a échoué à passer au planificateur de processus d’approvisionnement.

Cette mise à jour inclut la nouvelle fonctionnalité suivante :

- Prise en charge pour les Services d’application secondaire dans l’onglet « Services » de la boîte de dialogue de configuration de Service de l’application. 

##<a name="azure-data-lake-tools-for-visual-studio-2015-update-2"></a>Outils de LAC données Azure pour la mise à jour de Visual Studio 2015 2
 
Cette mise à jour inclut les éléments suivants :

- **Lac de données azure Tools** pour Visual Studio est maintenant fusionnée à la Azure SDK pour la version de .NET. L’outil est installé automatiquement lorsque vous installez le Kit de développement logiciel Azure. 

    L’outil est mis à jour fréquemment, allez [ici](http://aka.ms/datalaketool) pour obtenir les mises à jour.

- **Explorateur de serveurs** vous permet désormais afficher toutes les et de créer des entités de métadonnées U-SQL. Pour plus d’informations, consultez [le](https://azure.microsoft.com/documentation/services/data-lake-analytics/) blog.


##<a name="hdinsight-tools"></a>Outils de HDInsight 

**HDInsight outils** de Visual Studio prend désormais en charge les HDInsight version 3.3, y compris montrant Tez graphiques et résout d’autres langues.


##<a name="azure-resource-manager"></a>Gestionnaire de ressources Azure 

Cette version ajoute la prise en charge de [KeyVault](../resource-manager-keyvault-parameter.md) de modèles ARM.

##<a name="see-also"></a>Voir aussi

[Publication d’annonce Azure SDK 2.9](https://azure.microsoft.com/blog/announcing-visual-studio-azure-tools-and-sdk-2-9/)
