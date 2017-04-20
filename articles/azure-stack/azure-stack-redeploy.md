<properties
    pageTitle="Redéployer la pile Azure | Microsoft Azure"
    description="Redéployer la pile Azure."
    services="azure-stack"
    documentationCenter=""
    authors="ErikjeMS"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/29/2016"
    ms.author="erikje"/>

# <a name="redeploy-azure-stack"></a>Redéployer la pile Azure

Pour redéployer la pile d’Azure, vous devez démarrer sur à partir de zéro comme décrit ci-dessous.

## <a name="steps-to-redeploy-azure-stack"></a>Procédure pour redéployer la pile d’Azure

1. Redémarrez l’hôte dans le système d’exploitation d’origine (installé à vierge). Ce n’est pas le paramètre par défaut dans le menu de démarrage, vous devez donc utiliser KVM ou console locale pour la sélectionner lors du redémarrage (lors de l’installation, vous avez nommé le « démarrage à partir de disque dur virtuel » du système d’exploitation à « AzureStack TP2 », cela vous aidera à qui le système d’exploitation est).

    Vous n’êtes pas obligé de supprimer l’entrée de démarrage existante (la nouvelle prise en charge de script « PrepareBootFromVHD.ps1 » prend en charge que pour vous.)

2. Si vous n’avez pas de KVM, ou pour sélectionner le système d’exploitation de démarrage avant le redémarrage :
    
    1. Recherchez le script.\BootMenuNoKVM.ps1. Ce fichier est disponible avec les autres scripts de prise en charge fournis avec cette version.
    
    2. Exécuter le script avec des privilèges élevés. Sélectionnez le nom du système d’exploitation d’origine des hôtes. Cela démarrera l’hôte à l’hôte d’origine du système d’exploitation sans nécessiter d’accès KVM.
    
    3. Une fois le script terminé, vous devrez confirmer le redémarrage.

    - Si des utilisateurs sont connectés, cette commande échoue.

    - Veuillez exécuter la commande suivante : Restart-Computer-force 
 
3. Supprimez le fichier CloudBuilder.vhdx qui a été utilisé dans le cadre du déploiement précédent.

    Vous n’avez pas besoin de supprimer le Pool de stockage existant dans le déploiement TP2 précédent. Le script de déploiement détecte et nettoie les fichiers, puis crée de nouvelles.

5. Redéploiement de copier une nouvelle copie de la CloudBuilder.vhdx, démarrage, etc..

## <a name="next-steps"></a>Étapes suivantes

[Se connecter à la pile Azure](azure-stack-connect-azure-stack.md)
