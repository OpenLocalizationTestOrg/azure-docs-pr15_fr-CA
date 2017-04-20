<properties
    pageTitle="Paramètres personnalisés pour les environnements de Service d’application"
    description="Pour les environnements de Service d’application, les paramètres de configuration personnalisés"
    services="app-service"
    documentationCenter=""
    authors="stefsch"
    manager="nirma"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/22/2016"
    ms.author="stefsch"/>

# <a name="custom-configuration-settings-for-app-service-environments"></a>Pour les environnements de Service d’application, les paramètres de configuration personnalisés

## <a name="overview"></a>Vue d’ensemble ##
Environnements de Service d’application étant isolés à un client unique, il existe certains paramètres de configuration qui peuvent être appliqués exclusivement pour les environnements de Service d’application. Cet article explique les différentes personnalisations spécifiques qui sont disponibles pour les environnements de Service d’application.

Si vous ne disposez pas d’un environnement de Service d’application, reportez-vous à la section [comment créer un environnement de Service d’application](app-service-web-how-to-create-an-app-service-environment.md).

Vous pouvez stocker les personnalisations de l’environnement de Service d’application à l’aide d’un tableau dans le nouvel attribut **clusterSettings** . Cet attribut est trouvé dans le dictionnaire de « Propriétés » de l’entité du Gestionnaire de ressources Azure *hostingEnvironments* .

L’extrait de modèle de gestionnaire de ressources abrégé suivant illustre l’attribut de **clusterSettings** :


    "resources": [
    {
       "apiVersion": "2015-08-01",
       "type": "Microsoft.Web/hostingEnvironments",
       "name": ...,
       "location": ...,
       "properties": {
          "clusterSettings": [
             {
                 "name": "nameOfCustomSetting",
                 "value": "valueOfCustomSetting"
             }
          ],
          "workerPools": [ ...],
          etc...
       }
    }

L’attribut **clusterSettings** peut être inclus dans un modèle de gestionnaire de ressources mise à jour de l’environnement de Service d’application.

## <a name="use-azure-resource-explorer-to-update-an-app-service-environment"></a>Utilisez l’Explorateur de ressources Azure pour mettre à jour d’un environnement de Service d’application
Vous pouvez également mettre à jour l’environnement de Service d’application en utilisant [l’Explorateur de ressources Azure](https://resources.azure.com).  

1. Dans l’Explorateur de ressources, accédez au noeud pour l’environnement de Service d’application (**abonnements** > **resourceGroups** > **fournisseurs** > **Micrososft.Web** > **hostingEnvironments**). Cliquez ensuite sur l’environnement de Service application spécifique que vous souhaitez mettre à jour.

2. Dans le volet droit, cliquez sur **Lecture** dans la barre d’outils supérieure pour autoriser interactive édition dans l’Explorateur de ressources.  

3. Cliquez sur le bleu **Modifier** bouton pour modifier le modèle de gestionnaire de ressources.

4. Faites défiler vers le bas du volet de droite. L’attribut **clusterSettings** est tout en bas, dans laquelle vous pouvez entrer ou mettre à jour sa valeur.

5. Tapez (ou copiez et collez) du tableau de valeurs de configuration que vous souhaitez dans l’attribut **clusterSettings** .  

6. Cliquez sur le vert **placer** de bouton qui est située en haut du volet droit pour appliquer la modification à l’environnement de Service d’application.

Cependant vous soumettez la modification, il prend à peu près 30 minutes, multipliés par le nombre de ports front-end dans l’environnement de Service d’application pour que la modification prenne effet.
Par exemple, si un environnement de Service d’application dispose de quatre ports front-end, il prendra environ deux heures pour la mise à jour de configuration terminer. Lors de la modification de configuration est déployée, aucune autre mise à l’échelle des opérations ou les opérations de changement de configuration ne peuvent avoir lieu dans l’environnement de Service d’application.

## <a name="disable-tls-10"></a>Désactiver le protocole TLS 1.0 ##
Une question récurrente de clients, notamment les clients qui traitent avec la mise en conformité PCI des audits, comment désactiver explicitement TLS 1.0 pour leurs applications.

TLS 1.0 peut être désactivé via l’entrée **clusterSettings** suivante :

        "clusterSettings": [
            {
                "name": "DisableTls1.0",
                "value": "1"
            }
        ],

## <a name="change-tls-cipher-suite-order"></a>Modifier l’ordre des suite chiffrement TLS ##
Une autre question à partir de clients est qu’ils peuvent modifier la liste des chiffrements négocié par leur serveur et ceci peut être réalisé en modifiant le **clusterSettings** comme indiqué ci-dessous. La liste des suites de chiffrement disponibles peut être récupérée à partir de [cet article MSDN] (https://msdn.microsoft.com/library/windows/desktop/aa374757(v=vs.85\).aspx).

        "clusterSettings": [
            {
                "name": "FrontEndSSLCipherSuiteOrder",
                "value": "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384_P256,TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256_P256,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384_P256,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256_P256,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P256,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256"
            }
        ],

> [AZURE.WARNING]  Si des valeurs incorrectes sont définies pour la suite de chiffrement SChannel ne peut pas comprendre, toutes les communications TLS sur votre serveur peuvent cesser de fonctionner. Dans ce cas, vous devez supprimer l’entrée de *FrontEndSSLCipherSuiteOrder* de **clusterSettings** et de soumettre le modèle du Gestionnaire de ressources mis à jour pour restaurer les paramètres de suite de chiffrement par défaut.  Utilisez cette fonctionnalité avec précaution.

## <a name="get-started"></a>Mise en route
Le site de modèle du Gestionnaire de ressources du démarrage rapide Azure inclut un modèle avec la définition de base pour la [Création d’un environnement de Service d’application](https://azure.microsoft.com/documentation/templates/201-web-app-ase-create/).


<!-- LINKS -->

<!-- IMAGES -->
