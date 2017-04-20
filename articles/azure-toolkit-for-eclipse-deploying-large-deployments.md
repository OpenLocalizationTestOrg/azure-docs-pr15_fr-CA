<properties
    pageTitle="Déploiement des déploiements à grandes échelle"
    description="Apprenez à déployer des déploiements à l’aide de la Shared Computer Toolkit Azure pour Eclipse."
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/dn268601.aspx -->

# <a name="deploying-large-deployments"></a>Déploiement des déploiements à grandes échelle #

Si votre déploiement est trop volumineux pour être contenus dans le dossier approot, vous pouvez utiliser une ressource de stockage local en tant que dossier racine de déploiement pour votre JDK et serveur d’application.

## <a name="to-use-a-local-storage-resource-as-the-deployment-root-folder-for-large-deployments"></a>Pour utiliser une ressource de stockage local en tant que dossier racine de déploiement pour les déploiements à grandes échelle ##

1. Créer une nouvelle ressource de stockage local. Le nom de la ressource n’a pas d’importance. Les ressources de stockage sont définies au niveau du rôle. Est de la plus rapide pour accéder à la boîte de dialogue de configuration stockage local, à partir de laquelle vous pouvez créer une nouvelle ressource de stockage local, en procédant comme suit : cliquez sur le rôle dans **l’Explorateur de projet** (développez le nœud de votre projet Azure si vous ne voyez pas le rôle), **Azure**, puis **Stockage Local**. Dans la boîte de dialogue **Stockage Local** , cliquez sur **Ajouter** pour créer une nouvelle ressource de stockage local.
1. Définir la taille de votre choix d’au moins 2048 Mo (quoi que ce soit moins peut provoquer le même fichier de taille des problèmes que vous pouvez rencontrer dans l’approot).
1. Assurez-vous que l’option **Nettoyer le contenu lorsque l’instance de rôle est recyclé** est activée ; Cela empêchera logique de démarrage de déploiement à partir de l’exécution de conflits avec les fichiers existants dans la ressource lorsque l’instance de rôle est recyclé.
1. Assurez-vous que la valeur de la **variable d’environnement stocker le chemin du répertoire après le déploiement de la ressource** est définie à la chaîne **DEPLOYROOT**. Votre boîte de dialogue de ressource de stockage local est semblable à la suivante.
    ![][ic667943]

Ou bien, si vous utilisez **DEPLOYROOT** comme *nom* de votre ressource locale et que vous ne modifiez pas le nom de variable généré automatiquement par l’environnement (qui est fixé à **DEPLOYROOT_PATH** dans ce cas), qui fonctionnerait pour votre application.

Vous trouverez plus d’informations sur la création d’une ressource de stockage local à [Propriétés de stockage Local][].

## <a name="see-also"></a>Voir aussi ##

[Azure Shared Computer Toolkit pour Éclipse][]

[Création d’une Application du monde Hello pour Azure dans Éclipse][]

[L’installation de la Shared Computer Toolkit Azure pour Éclipse][] 

Pour plus d’informations sur l’utilisation d’Azure avec Java, consultez le [Centre pour développeurs Java Azure][].

<!-- URL List -->

[Centre de développement Java Azure]: http://go.microsoft.com/fwlink/?LinkID=699547
[Azure Shared Computer Toolkit pour Éclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Création d’une Application du monde Hello pour Azure dans Éclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[L’installation de la Shared Computer Toolkit Azure pour Éclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Propriétés de stockage local]: http://go.microsoft.com/fwlink/?LinkID=699525#local_storage_properties

<!-- IMG List -->

[ic667943]: ./media/azure-toolkit-for-eclipse-deploying-large-deployments/ic667943.png
