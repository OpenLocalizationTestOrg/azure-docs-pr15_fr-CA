<properties
    pageTitle="Comment créer et déployer un service en nuage | Microsoft Azure"
    description="Découvrez comment créer et déployer un service cloud à l’aide de la méthode de création rapide dans Azure."
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
    ms.date="09/06/2016"
    ms.author="adegeo"/>




# <a name="how-to-create-and-deploy-a-cloud-service"></a>Comment créer et déployer un Service Cloud

> [AZURE.SELECTOR]
- [Azure portal](cloud-services-how-to-create-deploy-portal.md)
- [Azure portal classique](cloud-services-how-to-create-deploy.md)

Le portail classique Azure fournit deux méthodes pour créer et déployer un service cloud : **Créer rapide** et **Personnalisée**.

Cette rubrique explique comment utiliser la méthode de création rapide pour créer un nouveau service en nuage, puis **Télécharger** pour télécharger et déployer un package de service cloud dans Azure. Lorsque vous utilisez cette méthode, le portail classique Azure permet de liens pratiques disponibles pour l’exécution de toutes les demandes à mesure que vous progressez. Si vous êtes prêt à déployer votre service cloud lors de sa création, vous pouvez effectuer les deux en même temps à l’aide de **Créer des personnalisées**.

> [AZURE.NOTE] Si vous envisagez de publier votre service en nuage à partir de Visual Studio Team Services (VSTS), utilisez la création rapide et ensuite configurer la publication VSTS de **Quick Start** ou le tableau de bord. Pour plus d’informations, consultez [Fourniture continue vers Azure par à l’aide de Visual Studio Team Services][TFSTutorialForCloudService], ou consultez l’aide de la page de **Démarrage rapide** .

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

- Si vous souhaitez déployer un service cloud qui utilise Secure Sockets Layer (SSL) pour le cryptage de données, [Configurez votre application](cloud-services-configure-ssl-certificate.md#step-2-modify-the-service-definition-and-configuration-files) pour SSL.

- Si vous souhaitez configurer des connexions Bureau à distance à des instances de rôle, de [configurer les rôles](cloud-services-role-enable-remote-desktop.md) pour le Bureau à distance.

- Si vous souhaitez configurer les commentaires d’analyse pour votre service cloud, activer les Diagnostics de Azure pour le service en nuage. *Contrôle minimal* (niveau de surveillance par défaut) utilise des compteurs de performance collectées à partir des systèmes d’exploitation hôte pour les instances de rôle (machines virtuelles). « Analyse détaillée * rassemble des statistiques supplémentaires, en fonction des données de performances dans les instances de rôles pour permettre une analyse plus approfondie des problèmes qui se produisent pendant le traitement de l’application. Pour savoir comment activer les Diagnostics d’Azure, consultez [Activation des Diagnostics dans Azure](cloud-services-dotnet-diagnostics.md).

Pour créer un service cloud avec les déploiements de rôles web ou rôles de travail, vous devez [créer le package de service](cloud-services-model-and-package.md#servicepackagecspkg).

## <a name="before-you-begin"></a>Avant de commencer

- Si vous n’avez pas installé le SDK Azure, cliquez sur **Installer le SDK Azure** pour ouvrir la [page de téléchargements d’Azure](https://azure.microsoft.com/downloads/)et ensuite télécharger le Kit de développement de la langue dans laquelle vous souhaitez développer votre code. (Vous aurez la possibilité de le faire ultérieurement).

- Si toutes les instances de rôle requièrent un certificat, créez les certificats. Les services en nuage requièrent un fichier .pfx avec une clé privée. Vous pouvez [télécharger les certificats pour Azure](cloud-services-configure-ssl-certificate.md#step-3-upload-a-certificate) que vous créez et déployez le service en nuage.

- Si vous envisagez de déployer le service en nuage à un groupe d’affinité, créez le groupe de l’affinité. Vous pouvez utiliser un groupe d’affinité pour le déploiement de votre service cloud et autres services Azure vers le même emplacement dans une région. Vous pouvez créer l’affinité du groupe dans la zone **réseaux** du portail Azure classique, dans la page **groupes d’affinité** .


## <a name="how-to-create-a-cloud-service-using-quick-create"></a>Comment : créer un service cloud à l’aide de création rapide

1. Dans [Azure portal classique](http://manage.windowsazure.com/), cliquez sur **Nouveau**>**Calculer**>**Service Cloud**>**Création rapide**.

    ![CloudServices_QuickCreate](./media/cloud-services-how-to-create-deploy/CloudServices_QuickCreate.png)

2. Dans **URL**, entrez un nom de sous-domaine à utiliser dans l’URL publique pour accéder à votre service cloud dans les déploiements de production. Format d’URL pour les déploiements de production : http://*myURL*. cloudapp.net.

3. Dans une **région ou un groupe d’affinité**, sélectionnez la région géographique ou un groupe d’affinité pour déployer le service en nuage à. Sélectionnez un groupe d’affinité si vous souhaitez déployer votre service cloud dans le même emplacement que les autres services Azure dans une région.

4. Cliquez sur **créer un Service Cloud**.

    ![CloudServices_Region](./media/cloud-services-how-to-create-deploy/CloudServices_Regionlist.png)

    Vous pouvez surveiller l’état du processus dans la zone de message en bas de la fenêtre.

    La zone de **Services de Cloud** s’ouvre, avec le nouveau service cloud s’affiché. Lorsque l’état change à créé, création de services de cloud est terminée.

    ![CloudServices_CloudServicesPage](./media/cloud-services-how-to-create-deploy/CloudServices_CloudServicesPage.png)


## <a name="how-to-upload-a-certificate-for-a-cloud-service"></a>Comment : télécharger un certificat pour un service en nuage

1. Dans [Azure portal classique](http://manage.windowsazure.com/), cliquez sur **Services dans le nuage**, cliquez sur le nom du service cloud, puis cliquez sur **certificats**.

    ![CloudServices_QuickCreate](./media/cloud-services-how-to-create-deploy/CloudServices_EmptyDashboard.png)


2. Cliquez sur **télécharger un certificat** ou sur **Télécharger**.

3. Dans le **fichier**, utilisez **Parcourir** pour sélectionner le certificat (fichier .pfx).

4. Dans **mot de passe**, entrez la clé privée pour le certificat.

5. Cliquez sur **OK** (coche).

    ![CloudServices_AddaCertificate](./media/cloud-services-how-to-create-deploy/CloudServices_AddaCertificate.png)

    Vous pouvez surveiller la progression du téléchargement dans la zone de message, illustré ci-dessous. Une fois le téléchargement terminé, le certificat est ajouté à la table. Dans la zone de message, cliquez sur OK pour fermer le message.

    ![CloudServices_CertificateProgress](./media/cloud-services-how-to-create-deploy/CloudServices_CertificateProgress.png)

## <a name="how-to-deploy-a-cloud-service"></a>Comment : déployer un service cloud

1. Dans [Azure portal classique](http://manage.windowsazure.com/), cliquez sur **Services dans le nuage**, cliquez sur le nom du service cloud, puis cliquez sur **tableau de bord**.

2. Cliquez sur **télécharger un nouveau déploiement de production** ou sur **Télécharger**.

3. Dans **étiquette de déploiement**, entrez un nom pour le nouveau déploiement - par exemple, MyCloudServicev4.

3. Dans le **Package**, utilisez **Parcourir** pour sélectionner le fichier de package de service (.cspkg) à utiliser.

4. Dans la **Configuration**, utilisez **Parcourir** pour sélectionner le fichier de configuration de service (.cscfg) à utiliser.

5. Si le service en nuage doit inclure tous les rôles à une seule instance, activez la case à cocher **déployer même si un ou plusieurs rôles contiennent une seule instance** pour activer le déploiement continuer.

    Azure ne garantissent l’accès au service cloud 99,95 % au cours des mises à jour de maintenance et de service si chaque rôle possède au moins deux instances. Si nécessaire, vous pouvez ajouter des instances de rôle supplémentaire sur la page **d’échelle** après avoir déployé le service en nuage. Pour plus d’informations, consultez [Les contrats de niveau de Service](https://azure.microsoft.com/support/legal/sla/).

6. Cliquez sur **OK** (coche) pour commencer le déploiement de service cloud.

    ![CloudServices_UploadaPackage](./media/cloud-services-how-to-create-deploy/CloudServices_UploadaPackage.png)

    Vous pouvez surveiller l’état du déploiement apparaît dans la zone de message. Cliquez sur OK pour masquer le message.

    ![CloudServices_UploadProgress](./media/cloud-services-how-to-create-deploy/CloudServices_UploadProgress.png)

## <a name="verify-your-deployment-completed-successfully"></a>Vérifiez votre déploiement est terminé

1. Cliquez sur le **tableau de bord**.

    L’état doit indiquer que le service est **en cours d’exécution**.

2. **Coup de œil rapide**, cliquez sur l’URL du site pour ouvrir votre service cloud dans un navigateur web.

    ![CloudServices_QuickGlance](./media/cloud-services-how-to-create-deploy/CloudServices_QuickGlance.png)


[TFSTutorialForCloudService]: cloud-services-continuous-delivery-use-vso.md
 
## <a name="next-steps"></a>Étapes suivantes

* [Configuration générale de votre service cloud](cloud-services-how-to-configure.md).
* Configurer un [nom de domaine personnalisé](cloud-services-custom-domain-name.md).
* [Gérer votre service cloud](cloud-services-how-to-manage.md).
* Configurer des [certificats ssl](cloud-services-configure-ssl-certificate.md).