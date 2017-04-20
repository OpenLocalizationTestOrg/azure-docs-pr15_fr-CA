<properties 
   pageTitle="Notes de mise à jour de SDK Azure pour .NET 2.6" 
   description="Notes de mise à jour de SDK Azure pour .NET 2.6" 
   services="app-service/web" 
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

 
# <a name="azure-sdk-for-net-26-release-notes"></a>Notes de mise à jour de SDK Azure pour .NET 2.6

Ce document contient les notes de publication pour le SDK Azure pour .NET 2.6 mise à jour. 

Avec Azure SDK 2.6, vous pouvez développer des applications de service cloud (PaaS) ciblant .NET 4.5.2 ou 4.6 de .NET si vous installez manuellement la cible du.NET Framework sur le rôle de Service Cloud. Reportez-vous à la section [installation .NET sur un rôle de Service de Cloud](http://go.microsoft.com/fwlink/?LinkID=309796).


##<a name="service-bus-updates"></a>Mises à jour de Bus des services

- Concentrateurs de l’événement : 

    - Permet à présent de contrôle d’accès cible lors de l’envoi des événements en exposant le point de terminaison supplémentaire de publisher pour les concentrateurs d’événement.
    - Stabilité et amélioration ajoutée à la fonctionnalité de concentrateurs de l’événement.
    - Ajout de la prise en charge du protocole de Amqp sur WebSocket pour la messagerie et des concentrateurs de l’événement.

##<a name="hdinsight-tools-for-visual-studio-updates"></a>Outils de HDInsight les mises à jour de Visual Studio

- **Amélioration d’IntelliSense**: suggestion de métadonnées à distance

    Outils d’HDInsight pour Visual Studio supporte à présent lors de l’obtention de métadonnées à distance lorsque vous modifiez votre script de la ruche. Par exemple, vous pouvez taper *FROM de *Sélectionner* ** et tous les noms de table seront affiche. En outre, les noms de colonne seront affiche après avoir spécifié une table.

- **Support d’émulateurs HDInsight**

    Maintenant les outils de HDInsight pour Visual Studio prennent en charge la connexion à l’émulateur de HDInsight, afin que vous puissiez développer vos scripts de ruche localement sans introduire de tous les coûts, puis exécuter ces scripts sur vos clusters HDInsight. 

    Pour plus d’informations, reportez-vous à [ce manuel](http://go.microsoft.com/fwlink/?LinkID=529540&clcid=0x409).

- **Outils d’HDInsight pour Visual Studio prise en charge des clusters d’Hadoop génériques** (Aperçu)

    Maintenant des outils de HDInsight pour Visual Studio prennent en charge les clusters Hadoop génériques, afin de pouvoir utiliser les outils de HDInsight pour Visual Studio pour effectuer les opérations suivantes :

    - se connecter à votre cluster, 
    - écrire la requête de ruche avec prise en charge IntelliSense-semi-automatique améliorée, 
    - Permet d’afficher toutes les tâches de votre cluster avec une interface utilisateur intuitive. 

    Pour plus d’informations, reportez-vous à [ce manuel](http://go.microsoft.com/fwlink/?LinkID=529540&clcid=0x409).

##<a name="in-role-cache-updates"></a>Mises à jour du Cache de dans le rôle

- **Dans le rôle Cache** a été mis à jour pour utiliser **Le stockage Microsoft Azure SDK** version 4.3. Jusqu'à présent, le **Rôle de Cache** a été à l’aide de stockage d’Azure SDK version 1.7.

    Clients à l’aide d’Azure SDK 2.5 ou ci-dessous doit mettre à jour vers Azure SDK 2.6 et déplacer vers la nouvelle version du Kit de développement de stockage Azure. 

    À ce stade, le stockage Azure version 2011-08-18 est planifié à supprimer le 1 août 2016. Les migrations de Cache dans le rôle Azure SDK 2.5 ou ci-dessous à 2.6 doivent être terminées à ce stade. Pour plus d’informations sur le déclassement de la version 2011-08-18 le stockage Azure, consultez [mise à jour d’un Version de suppression du Service de stockage Microsoft Azure : Extension de 2016](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/10/19/microsoft-azure-storage-service-version-removal-update-extension-to-2016.aspx).

>[AZURE.IMPORTANT]Nous allons annonce la retraite le 30 novembre 2016, pour le Service de Cache géré Azure et d’Azure dans le rôle Cache. Nous vous recommandons de migrer vers le Cache de Redis d’Azure dans la préparation de ce déclassement. Pour plus d’informations sur les dates et les directives de migration, reportez-vous à la section [offre de Cache d’Azure qui me convient ?](../redis-cache/cache-faq.md#which-azure-cache-offering-is-right-for-me)

##<a name="azure-app-service-tools"></a>Outils de Service d’application Azure

Les éléments suivants ont été mis à jour dans la version 2.6 du SDK Azure.

- Publication Azure améliorée pour inclure des applications d’API Azure sous la forme d’une cible de déploiement.
- Applications API de mise en service des fonctionnalités pour permettre aux utilisateurs avec la fonction de création et de la mise en service des API App.
- Explorateur de serveurs est modifiée pour refléter le nouveau nœud de Service de l’application, avec des applications Web, Mobile et API regroupées par groupe de ressources.
- Ajouter un mouvement de Client de l’application API Azure ajouté à la plupart des projets C# qui permettront la génération automatique de Swagger API applications s’exécutant dans un abonnement Azure d’un utilisateur.
- Outillage d’applications d’API et de nœuds de Service de l’application dans l’Explorateur de serveurs sont uniquement disponibles dans Visual Studio 2013. 

##<a name="azure-resource-manager-tools-updates"></a>Mises à jour des outils du Gestionnaire de ressources Azure

Les outils du Gestionnaire de ressources Azure ont été mis à jour pour inclure les modèles de Machines virtuelles, de réseau et de stockage. L’expérience d’édition de JSON a été mis à jour pour inclure une nouvelle vue hiérarchique pour les modèles et la possibilité de modifier les modèles d’utilisation d’extraits JSON. Modèles de déploiement de Visual Studio utilisent un script PowerShell fourni avec le projet, afin que les modifications apportées au script seront utilisées par Visual Studio.

##<a name="diagnostics-improvements-for-cloud-services"></a>Améliorations des diagnostics pour les Services en nuage

Azure SDK 2.6 ramène la prise en charge de la collecte des journaux de diagnostics dans l’émulateur de calcul Azure et les transférer vers un stockage de développement. Les tests de diagnostic se connecte (y compris la trace de l’application se connecte, le traçage pour les compteurs de performance, les journaux Windows (ETW), l’infrastructure des journaux d’événements windows et les journaux d’événements) générées lorsque l’application s’exécute dans l’émulateur peut être transférée vers le stockage de développement pour vérifier que la journalisation de diagnostics fonctionne sur votre ordinateur local. 

Le compte de stockage de Diagnostics peut maintenant être spécifié dans le fichier de configuration (.cscfg) de service, ce qui la rend plus facile d’utiliser des comptes de stockage différents tests de diagnostic pour les différents environnements. Il existe des différences notables entre comment la chaîne de connexion fonctionnait dans Azure SDK 2.4 et 2.6 du SDK Azure. Pour plus d’informations sur la façon d’utiliser la connexion de stockage de Diagnostics de chaîne et de leur impact sur vos projets voir [Configuration de Diagnostics pour les Services en nuage Azure](http://go.microsoft.com/fwlink/?LinkID=532784).

##<a name="breaking-changes"></a>Modifications avec rupture

###<a name="azure-resource-manager-tools"></a>Outils de gestionnaire de ressources Azure 

- Le type de projet de **Projets de déploiement de Cloud** disponible dans le SDK Azure 2.5 a été renommé **Groupe de ressources Azure**.
- Type de **Projets de déploiement de cloud** de projets créés dans le SDK Azure 2.5 peut être utilisé au point 2.6, mais déployer le modèle à partir de Visual Studio va échouer. Toutefois, le déploiement avec le script PowerShell fonctionne toujours comme avant.  Pour plus d’informations sur l’utilisation de **Projets de déploiement de Cloud** dans 2.6 lire cette [validation](http://go.microsoft.com/fwlink/?LinkID=534086).
 
##<a name="known-issues"></a>Problèmes connus

- Collecter les journaux de diagnostics dans l’émulateur nécessite un système d’exploitation de 64 bits. Les journaux de Diagnostics ne seront pas recueillies lors de l’exécution sur un système d’exploitation 32 bits. Cela n’affecte pas les autres fonctionnalités de l’émulateur. 

- Azure 2.6 SDK publiée le 4/29/2015 avait deux problèmes : 

    - Universal application n’a pas pu être chargée dans Visual Studio 2015 lorsque Azure SDK 2.6 a été installé sur l’ordinateur.
    - Débogage d’un projet de Service Cloud échoue dans Visual Studio 2013 et 2015 de Visual Studio où Visual Studio cesse de répondre et se bloque lors de l’affichage d’une boîte de dialogue avec le message « Configuration des tests de diagnostic pour l’émulateur ».
    
    Une mise à jour vers Azure SDK 2.6 a été publiée le 5/18/2015. La version mise à jour est 2.6.30508.1601 ; Il contient des correctifs pour deux problèmes décrits ci-dessus. Vous pouvez identifier la version SDK à partir du Panneau de configuration -> Programmes et fonctionnalités -> Microsoft Azure Tools pour Microsoft Visual Studio 2013 – v 2.6. La colonne Version affiche le numéro de build.

    Si vous êtes toujours confronté à problèmes mentionnés ci-dessus, installez la dernière version du SDK Azure 2.6 pour [VS 2012](http://go.microsoft.com/fwlink/p/?linkid=323511&clcid=0x409), [VS 2013](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409) ou [VS 2015](http://go.microsoft.com/fwlink/?linkid=518003&clcid=0x409).
 
##<a name="see-also"></a>Voir aussi

[Prise en charge et les informations de retraite pour le SDK Azure pour .NET et API](https://msdn.microsoft.com/library/azure/dn479282.aspx/)
