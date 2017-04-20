<properties
    pageTitle="Avec App-V applications RemoteApp Azure | Microsoft Azure"
    description="Apprenez à utiliser les applications de App-V dans Azure RemoteApp."
    services="remoteapp"
    documentationCenter=""
    authors="ericorman"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016" 
    ms.author="elizapo" />



# <a name="using-app-v-apps-in-azure-remoteapp"></a>À l’aide d’applications de App-V dans Azure RemoteApp

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lecture de l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus de détails.

Vous pouvez utiliser des applications App-V dans une collection hybride Azure RemoteApp, qui nécessite de jonction de domaine.

Avant de commencer, assurez-vous d’installer le client App-V 5.1 avec les dernières mises à jour. Vous devez créer une [image personnalisée](remoteapp-create-custom-image.md) qui inclut le client App-V.  

Il est facile d’utiliser votre infrastructure App-V avec Azure RemoteApp. Dans la mesure où une collection hybride est déployée dans un VNET d’Azure qui a accès à votre contrôleur de domaine et les ordinateurs virtuels sont joint au domaine, vous pouvez exploiter vos méthodes de déploiement et infrastructure de App-v existants pour l’application de App-V easyily hôte dans Azure RemoteApp. Voici quelques considérations que vous devez être conscient de basé sur le type de déploiement d’App-V qu'est actuellement :

| Options de configuration |                       | Positif                                                               | Négatif                                                                                              |
|-----------------------|-----------------------|------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------|
| Méthode de livraison       | Transmission en continu (sur demande) | App est toujours plus tard et frais                                     | Latence de temps premier                                                                                    |
|                       | Monté               | Plus rapide ; App est déjà présent sur l’ordinateur virtuel                              | Dilatation - occupe l’espace image (limite de 127 Go)                                                           |
| Stockage de l’emplacement d’application  | Contenu partagé        | Application qui s’exécute dans la mémoire de l’instance d’Azure RemoteApp                         | Mange de la bonne connexion à (fichier) serveur de diffusion et mémoire où l’application réside                      |
|                       | Disque (en cache)         | Exécution rapide. Application non dépendante de la disponibilité de la Source de contenu | Dilatation - occupe l’espace image (limite de 127 Go)                                                           |
| Ciblage             | Utilisateur                  | Nécessite une infrastructure App-V complète autonome                          |                                                                                                       |
|                       | Global (ordinateur)      |  Avant de publier ou de cible utilisant la publication sur serveur                         |  Mettez à jour votre image Azure si vous souhaitez mettre à jour de l’application (énorme). Occupe de l’espace sur l’image. |

 Après avoir créé votre image personnalisée et votre collection hybride, publier votre application, affecter des utilisateurs et profitez de vos applications existantes de App-V hébergées dans RemoteApp Azure remis à n’importe quel endroit de n’importe quel périphérique.
