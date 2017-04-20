
<properties 
   pageTitle="Notes de mise à jour de SDK Azure pour .NET 2.8" 
   description="Notes de mise à jour de SDK Azure pour .NET 2.8" 
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
 
# <a name="azure-sdk-for-net-28-281-and-282"></a>Azure SDK pour .NET 2.8, 2.8.1 et point 2.8.2

##<a name="overview"></a>Vue d’ensemble
 
Cet article contient les notes de version (ce qui inclut les problèmes connus et modifications avec rupture) pour le SDK Azure pour .NET 2.8, 2.8.1 et point 2.8.2 mises à jour. 

Pour obtenir une liste complète des nouvelles fonctionnalités et les mises à jour dans cette version, consultez l’annonce [2.8 SDK de Azure pour Visual Studio 2013 et 2015 de Visual Studio](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/) . 

##  <a name="azure-sdk-for-net-28"></a>Azure SDK pour .NET 2.8

### <a name="download-azure-sdk-for-net-28"></a>Télécharger le Kit de développement logiciel Azure pour .NET 2.8

[Azure SDK pour .NET 2.8 pour Visual Studio 2015](http://go.microsoft.com/fwlink/?LinkId=699285) 

[Azure SDK pour .NET 2.8 pour Visual Studio 2013](http://go.microsoft.com/fwlink/?LinkId=699287)
 
### <a name="net-452-support"></a>Prise en charge de .NET 4.5.2 

####<a name="known-issues"></a>Problèmes connus

Azure 2.8 SDK de .NET vous permet de créer de .NET 4.5.2 packages de Service Cloud. Toutefois 4.5.2 de .NET framework ne sera pas installé sur la valeur par défaut de mise à jour des images de système d’exploitation invité jusqu'à janvier 2016, système d’exploitation invité. Avant, point 4.5.2 de .NET framework sera disponible dans une version finale du système d’exploitation invité distincte – novembre 2015-02. Consultez la page [versions de système d’exploitation invité Azure et matrice de compatibilité SDK](../cloud-services/cloud-services-guestos-update-matrix.md) à suivre lorsque l’image sera libéré.  Une fois l’image de 2015-02 novembre est lancé, vous pouvez choisir d’utiliser cette image par la mise à jour de votre fichier de configuration Cloud Service (.cscfg). Dans la configuration du service de fichier défini l’attribut osVersion de l’élément de ServiceConfiguration à la chaîne « WA-invité-OS-4.26_201511-02 ». Si vous choisissez de participer à utiliser cette image, puis vous ne bénéficierez plus des mises à jour automatiques pour le système d’exploitation invité. Pour obtenir les mises à jour automatiques osVersion doit avoir la valeur « * » et .NET 4.5.2 sera uniquement disponible par le biais de mises à jour automatiques en janvier 2016.

###<a name="azure-data-factory"></a>Usine de données Azure

####<a name="known-issues"></a>Problèmes connus 

Lors de la création de projet de **Modèle de fabrique de données** impliquant des exemples de données, les script de shell d’alimentation azure peut échouer si version du shell d’alimentation azure installée sur l’ordinateur est après 0.9.8.

Pour créer ce type de projet, vous devez installer la [version du shell d’alimentation azure 0.9.8](https://github.com/Azure/azure-powershell/releases/download/v0.9.8-September2015/azure-powershell.0.9.8.msi).


### <a name="azure-resource-manager-tools"></a>Outils de gestionnaire de ressources Azure 

####<a name="breaking-changes"></a>Modifications avec rupture

Le script PowerShell fourni par le projet du groupe de ressources Azure a été mis à jour dans cette version pour travailler avec les applets de commande PowerShell de Azure nouvelle version 1.0.  Ce nouveau script ne fonctionne pas à partir d’avec Visual Studio, lorsque vous utilisez une version du Kit de développement avant 2.8.  

Scripts des projets créés dans des versions antérieures du Kit de développement ne seront exécutera pas à partir de dans Visual Studio lorsque vous utilisez le Kit de développement logiciel 2,8.  Tous les scripts continueront à fonctionner en dehors de Visual Studio avec la version appropriée des applets de commande PowerShell d’Azure.  

Le Kit de développement logiciel 2,8 requiert la version 1.0 des applets de commande PowerShell d’Azure.  Toutes les autres versions du Kit de développement requièrent la version 0.9.8 des applets de commande PowerShell d’Azure.  Pour plus d’informations, consultez [le](http://go.microsoft.com/fwlink/?LinkID=623011) blog.

###<a name="web-tools-extensions"></a>Les Extensions d’outils Web

####<a name="known-issues"></a>Problèmes connus

Les problèmes connus suivants seront résolus dans la version suivante.

- Service d’application associé Cloud et Explorateur de serveurs de mouvement pour des environnements de production non (comme Azure Chine ou les clients de la pile d’Azure) ne fonctionnent pas. Pour les clients dans ces domaines d’impactés, télécharger le profil de publication à partir du portail Azure permettra de possibilité de publication. Une version future réparera mouvements tels que « Joindre le débogueur » et « Afficher les journaux en continu » pour les clients de la pile et de la Chine d’Azure. 
- Les clients peuvent voir des erreurs lors de la création d’instance à laquelle ils déploient les perspectives d’application App Service dans une région autre que les États-Unis. Dans ces scénarios, la création d’un Service de l’application dans le portail et de télécharger le profil de publication permettront aux scénarios de publication. 

###<a name="azure-hdinsight-tools"></a>Outils d’HDInsight Azure

####<a name="new-updates"></a>Nouvelles mises à jour

- Vous pouvez exécuter votre requête de la ruche du cluster via HiveServer2 avec presque pas de surcharge et voir que le travail se connecte en temps réel.
- À l’aide de la nouvelle ruche d’exécution affichage des tâches vous pouvez aller dans votre travail plu, trouver plus d’informations et identifier les problèmes potentiels.

Pour plus d’informations, reportez-vous à la section [2.8 du SDK Azure pour Visual Studio 2013 et 2015 de Visual Studio](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/). 

## <a name="azure-sdk-for-net-281"></a>Azure SDK pour .NET 2.8.1

### <a name="known-issues-for-visual-studio-2013-and-visual-studio-2015"></a>Problèmes connus concernant Visual Studio 2013 et 2015 de Visual Studio
 
1. Déclenchée WebJob publie aux emplacements s’affiche et l’erreur et n’établissez un calendrier, mais il fera la WebJob vers Azure. Les clients qui ont besoin d’un travail planifié peuvent ensuite utiliser le portail Azure pour configurer la planification de la WebJob. 
2. Les clients les Python peuvent rencontrer des problèmes de débogueur. L’équipe du service développe un correctif pour ce mais si les clients sont concernés, veuillez laisser Microsoft savoir dans les forums ou sur le blog de l’annonce ou la section de commentaires de notes de version. 
3. Clients dans certaines régions (par exemple, du Sud de l’Inde) rencontreront Service application mise en service des erreurs. Ceci est cohérent avec le portail et les clients qui rencontrent ce problème peuvent utiliser le portail de Azure pour demander l’accès à publier sur ces régions géo. Une fois qu’ils demandent à accéder à ces régions à l’aide du provisionnement portail Azure doit fonctionner. 

##<a name="azure-sdk-for-net-282"></a>Azure SDK pour .NET point 2.8.2

Après l’installation du point 2.8.2 Outils, les clients peuvent rencontrer le problème suivant.         

- Si vous utilisez Windows 10 et que vous n’avez pas installé Internet Explorer, vous obtiendrez une erreur « Internet Explorer n’a pas pu être trouvé ».
Pour résoudre ce problème, installez Internet Explorer à l’aide de la boîte de dialogue Ajouter/supprimer des composants Windows.

Si vous constatez ce problème, utilisez la fonctionnalité de-envoyer un sourire à signaler.

Pour plus d’informations, consultez [cette](https://azure.microsoft.com/blog/announcing-azure-sdk-2-8-2-for-net/) publication.
##<a name="other-updates"></a>Autres mises à jour

Pour d’autres mises à jour, voir [l’annonce Azure SDK 2.8 valider](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/).

##<a name="also-see"></a>Voir aussi

[Publication d’annonce Azure SDK 2.8](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/)

[Prise en charge et les informations de retraite pour le SDK Azure pour .NET et API](https://msdn.microsoft.com/library/azure/dn479282.aspx)

