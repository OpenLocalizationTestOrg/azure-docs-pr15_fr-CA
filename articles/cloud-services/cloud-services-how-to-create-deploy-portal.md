<properties
    pageTitle="Comment créer et déployer un service en nuage | Microsoft Azure"
    description="Découvrez comment créer et déployer un service cloud à l’aide du portail Azure."
    services="cloud-services"
    documentationCenter=""
    authors="Thraka"
    manager="timlt"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/11/2016"
    ms.author="adegeo"/>




# <a name="how-to-create-and-deploy-a-cloud-service"></a>Comment créer et déployer un service cloud

> [AZURE.SELECTOR]
- [Azure portal](cloud-services-how-to-create-deploy-portal.md)
- [Azure portal classique](cloud-services-how-to-create-deploy.md)

Le portail Azure fournit deux méthodes pour créer et déployer un service cloud : *Créer rapide* et *Personnalisée*.

Cet article explique comment utiliser la méthode de création rapide pour créer un nouveau service en nuage, puis **Télécharger** pour télécharger et déployer un package de service cloud dans Azure. Lorsque vous utilisez cette méthode, le portail Azure permet de liens pratiques disponibles pour l’exécution de toutes les demandes à mesure que vous progressez. Si vous êtes prêt à déployer votre service cloud lors de sa création, vous pouvez effectuer les deux en même temps à l’aide de créer des personnalisées.

> [AZURE.NOTE] Si vous envisagez de publier votre service en nuage à partir de Visual Studio Team Services (VSTS), utilisez la création rapide et ensuite configurer la publication VSTS dans le démarrage rapide d’Azure ou le tableau de bord. Pour plus d’informations, consultez [Fourniture continue vers Azure par à l’aide de Visual Studio Team Services][TFSTutorialForCloudService], ou consultez l’aide de la page de **Démarrage rapide** .

## <a name="concepts"></a>Concepts
Trois composants sont requis pour déployer une application sous la forme d’un service en nuage dans Azure :

- **Définition de service**  
  Le fichier de définition de service cloud (.csdef) définit le modèle de service, y compris le nombre de rôles.

- **Configuration du service**  
  Le fichier de configuration de service cloud (.cscfg) fournit des paramètres de configuration pour le cloud services et des rôles, notamment le nombre d’instances de rôles.

- **Package de service**  
  Le package de service (.cspkg) contient le code d’application et les configurations et le fichier de définition de service.

Plus d’informations sur ces et comment créer un package [ici](cloud-services-model-and-package.md).

## <a name="prepare-your-app"></a>Préparation de votre application
Avant de déployer un service en nuage, vous devez créer le package de service cloud (.cspkg) à partir de votre code d’application et un fichier de configuration de service cloud (.cscfg). Le Kit de développement Azure fournit des outils pour la préparation de ces fichiers de déploiement requis. Vous pouvez installer le Kit de développement à partir de la page de [Téléchargements d’Azure](https://azure.microsoft.com/downloads/) , dans la langue dans laquelle vous souhaitez développer votre code d’application.

Trois fonctionnalités de service cloud nécessitent une configuration spéciale avant d’exporter un package de service :

- Si vous souhaitez déployer un service cloud qui utilise Secure Sockets Layer (SSL) pour le cryptage de données, [Configurez votre application](cloud-services-configure-ssl-certificate-portal.md#modify) pour SSL.

- Si vous souhaitez configurer des connexions Bureau à distance à des instances de rôle, de [configurer les rôles](cloud-services-role-enable-remote-desktop.md) pour le Bureau à distance. Cela n’est possible dans le portail classique.

- Si vous souhaitez configurer les commentaires d’analyse pour votre service cloud, activer les Diagnostics de Azure pour le service en nuage. *Contrôle minimal* (niveau de surveillance par défaut) utilise des compteurs de performance collectées à partir des systèmes d’exploitation hôte pour les instances de rôle (machines virtuelles). *Analyse des commentaires* rassemble des statistiques supplémentaires, en fonction des données de performances dans les instances de rôles pour permettre une analyse plus approfondie des problèmes qui se produisent pendant le traitement de l’application. Pour savoir comment activer les Diagnostics d’Azure, reportez-vous à la section [activation des diagnostics dans Azure](cloud-services-dotnet-diagnostics.md).

Pour créer un service cloud avec les déploiements de rôles web ou rôles de travail, vous devez [créer le package de service](cloud-services-model-and-package.md#servicepackagecspkg).

## <a name="before-you-begin"></a>Avant de commencer

- Si vous n’avez pas installé le SDK Azure, cliquez sur **Installer le SDK Azure** pour ouvrir la [page de téléchargements d’Azure](https://azure.microsoft.com/downloads/)et ensuite télécharger le Kit de développement de la langue dans laquelle vous souhaitez développer votre code. (Vous aurez la possibilité de le faire ultérieurement).

- Si toutes les instances de rôle requièrent un certificat, créez les certificats. Les services en nuage requièrent un fichier .pfx avec une clé privée. [Vous pouvez télécharger les certificats pour Azure]() comme vous de créer et de déployer le service en nuage.

- Si vous envisagez de déployer le service en nuage à un groupe d’affinité, créez le groupe de l’affinité. Vous pouvez utiliser un groupe d’affinité pour le déploiement de votre service cloud et autres services Azure vers le même emplacement dans une région. Vous pouvez créer l’affinité du groupe dans la zone **réseaux** du portail Azure classique, dans la page **groupes d’affinité** .


## <a name="create-and-deploy"></a>Créer et déployer

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Cliquez sur **Nouveau > Machines virtuelles**, puis faites défiler vers le bas et cliquez sur le **Service en nuage**.

    ![Publier votre service cloud](media/cloud-services-how-to-create-deploy-portal/create-cloud-service.png)

3. Au bas de la page d’informations qui s’affiche, cliquez sur **créer**. 
4. Dans la nouvelle blade de **Service Cloud** , entrez une valeur pour le **nom DNS**.
5. Créer un nouveau **Groupe de ressources** ou sélectionnez-en une existante.
6. Sélectionnez un **emplacement**.
7. Cliquez sur le **Package**. Ceci ouvrira la lame **télécharger un package** . Renseignez les champs requis.  

     Si un de vos rôles contient une seule instance, assurez-vous de **que déployer le même si un ou plusieurs rôles contiennent une seule instance** est activée.

8. Assurez-vous que le **déploiement de démarrage** est sélectionné.
9. Cliquez sur **OK** , qui ferme la lame **télécharger un package** .
10. Si vous n’avez pas de certificats à ajouter, cliquez sur **créer**.

    ![Publier votre service cloud](media/cloud-services-how-to-create-deploy-portal/select-package.png)

## <a name="upload-a-certificate"></a>Télécharger un certificat

Si votre package de déploiement a été [configuré pour utiliser des certificats](cloud-services-configure-ssl-certificate-portal.md#modify), vous pouvez télécharger le certificat maintenant.

1. Sélectionnez **certificats**, sur la blade **d’Ajouter des certificats** , le fichier .pfx de certificat SSL et sélectionnez ensuite fournir le **mot de passe** du certificat,
2. Cliquez sur **joindre un certificat**, puis cliquez sur **OK** sur la blade **d’Ajouter des certificats** .
3. Cliquez sur **créer** sur la lame de **Service Cloud** . Lorsque le déploiement a atteint l’état **prêt** , vous pouvez procéder aux étapes suivantes.

    ![Publier votre service cloud](media/cloud-services-how-to-create-deploy-portal/attach-cert.png)


## <a name="verify-your-deployment-completed-successfully"></a>Vérifiez votre déploiement est terminé

1. Cliquez sur l’instance de service cloud.

    L’état doit indiquer que le service est **en cours d’exécution**.

2. **Essentials**, cliquez sur l' **URL du Site** pour ouvrir votre service cloud dans un navigateur web.

    ![CloudServices_QuickGlance](./media/cloud-services-how-to-create-deploy-portal/running.png)


[TFSTutorialForCloudService]: http://go.microsoft.com/fwlink/?LinkID=251796

## <a name="next-steps"></a>Étapes suivantes

* [Configuration générale de votre service cloud](cloud-services-how-to-configure-portal.md).
* Configurer un [nom de domaine personnalisé](cloud-services-custom-domain-name-portal.md).
* [Gérer votre service cloud](cloud-services-how-to-manage-portal.md).
* Configurer des [certificats ssl](cloud-services-configure-ssl-certificate-portal.md).