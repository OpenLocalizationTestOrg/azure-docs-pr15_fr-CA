<properties
    pageTitle="Comment créer une collection hybride pour Azure RemoteApp | Microsoft Azure"
    description="Apprenez à créer un déploiement de RemoteApp qui se connecte à votre réseau interne."
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo"/>

# <a name="how-to-create-a-hybrid-collection-for-azure-remoteapp"></a>Comment créer une collection hybride pour Azure RemoteApp

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lecture de l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus de détails.

Il existe deux types de collections de RemoteApp d’Azure :

- Cloud : réside entièrement dans Azure. Vous pouvez choisir d’enregistrer toutes les données dans le nuage (pour une collection nuage uniquement) ou de votre collection de se connecter à une VNET et enregistrer les données.   
- Hybride : inclut un réseau virtuel pour l’accès local - cela nécessite l’utilisation d’AD Azure et un environnement d’Active Directory sur site.

Vous ne savez dont vous avez besoin ? Vérifiez [le type de collection vous faut-il pour Azure RemoteApp](remoteapp-collections.md).

Ce didacticiel vous guide tout au long du processus de création d’une collection hybride. Il existe huit étapes :

1.  Décider quelle [image](remoteapp-imageoptions.md) à utiliser pour votre collection. Vous pouvez créer une image personnalisée ou utiliser une des images de Microsoft inclus dans votre abonnement.
2. Configurez votre réseau virtuel. Vérifiez les informations de [planification de la VNET](remoteapp-planvnet.md) et de [redimensionnement](remoteapp-vnetsizing.md) .
2.  Créez une collection.
2.  Joindre votre collection à votre domaine local.
3.  Ajouter une image de modèle à votre collection.
4.  Configurer la synchronisation d’annuaire. Azure RemoteApp nécessite que vous intégrer à Active Directory de Azure par soit 1) configuration Azure Active Directory Sync avec l’option de synchronisation de mot de passe, ou 2) configuration Azure Active Directory synchronisation sans l’option de synchronisation de mot de passe, mais à l’aide d’un domaine qui est fédéré ADFS. Extraire les [informations de configuration d’Active Directory avec RemoteApp](remoteapp-ad.md).
5.  Publier les applications RemoteApp.
6.  Configurer l’accès utilisateur.

**Avant de commencer**

Vous devez effectuer les tâches suivantes avant de créer la collection :

- [Inscrivez-vous](https://azure.microsoft.com/services/remoteapp/) pour Azure RemoteApp.
- Créer un compte d’utilisateur dans Active Directory à utiliser comme compte de service Azure RemoteApp. Restreindre les autorisations de ce compte afin qu’il seulement peut joindre des ordinateurs au domaine.
- Rassembler des informations sur votre réseau local : adresse IP d’informations et de détails de périphérique VPN.
- Installez le module [PowerShell de Azure](../powershell-install-configure.md) .
- Rassembler des informations sur les utilisateurs que vous souhaitez accorder l’accès à. Vous devez le nom principal d’utilisateur Azure Active Directory (par exemple, name@contoso.com) pour chaque utilisateur. Assurez-vous que le nom UPN correspond entre AD Azure et Active Directory.
- Choisissez votre image du modèle. Une image du modèle Azure RemoteApp contient les applications et les programmes que vous voulez publier pour vos utilisateurs. Pour plus d’informations, reportez-vous à la section [options d’image Azure RemoteApp](remoteapp-imageoptions.md) .
- Vous souhaitez utiliser l’image d’Office 365 ProPlus ? Extraire les informations [ici](remoteapp-officesubscription.md).
- [Configurer Active Directory pour RemoteApp](remoteapp-ad.md).



## <a name="step-1-set-up-your-virtual-network"></a>Étape 1 : Configurer votre réseau virtuel
Vous pouvez déployer une collection hybride qui utilise un réseau virtuel Azure, ou vous pouvez créer un nouveau réseau virtuel. Un réseau virtuel permet à vos utilisateurs d’accéder aux données sur votre réseau local par le biais de RemoteApp à des ressources distantes. À l’aide d’un réseau virtuel Azure permet un accès réseau direct votre collection autres services Azure et les ordinateurs virtuels déployés à ce réseau virtuel.

Vérifiez que vous examinez les informations de [taille VNET](remoteapp-vnetsizing.md) et de [VNET de planification](remoteapp-planvnet.md) avant de créer votre VNET.

### <a name="create-an-azure-vnet-and-join-it-to-your-active-directory-deployment"></a>Créer une VNET Azure et de l’intégrer à votre déploiement d’Active Directory

Commencez par créer un [réseau virtuel](../virtual-network/virtual-networks-create-vnet-arm-pportal.md). Pour cela, dans l’onglet **réseau** dans le portail Azure. Vous devez connecter votre réseau virtuel pour le déploiement de Active Directory qui est synchronisé à vos clients d’Azure Active Directory.

Pour plus d’informations, consultez [Création d’un réseau virtuel via le portail Azure](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) .

### <a name="make-sure-your-virtual-network-is-ready-for-azure-remoteapp"></a>Assurez-vous que votre réseau virtuel est prêt pour Azure RemoteApp
Avant de créer votre collection, assurons-nous que votre nouveau réseau virtuel est prêt. Vous pouvez le valider en procédant comme suit :

1. Créer un ordinateur virtuel Azure à l’intérieur du sous-réseau du réseau virtuel que vous venez de créer pour RemoteApp.
2. Utilisez le Bureau à distance pour vous connecter à l’ordinateur virtuel. (Cliquez sur **se connecter**.)
3. Joindre à la même déploiement d’Active Directory que vous souhaitez utiliser pour RemoteApp.

Qui a-t-il fonctionné ? Votre réseau virtuel et un sous-réseau sont prêts pour Azure RemoteApp !

Vous trouverez plus d’informations sur la création des machines virtuelles Azure et connexion à leur bureau à distance [ici](https://msdn.microsoft.com/library/azure/jj156003.aspx).

## <a name="step-2-create-an-azure-remoteapp-collection"></a>Étape 2 : Créer une collection Azure RemoteApp ##



1. Dans le [portail Azure](http://manage.windowsazure.com), accédez à la page d’Azure RemoteApp.
2. Cliquez sur **Nouveau > créer avec VNET**.
3. Entrez un nom pour la collection.
4. Sélectionnez le plan que vous souhaitez utiliser - standard ou de base.
5. Choisissez votre VNET dans la liste déroulante et ensuite votre sous-réseau.
6. Choisir de joindre à votre domaine.
5. Cliquez sur **créer un RemoteApp collection**.

Après avoir créé votre collection Azure RemoteApp, double-cliquez sur le nom de la collection. Qui vous amènera à la page de **Démarrage rapide** : il s’agit où vous avez terminé la configuration de la collection.

A quelque chose de mal se passer ? Vérifiez les [informations de résolution des problèmes de collection hybride](remoteapp-hybridtrouble.md).

## <a name="step-3-link-your-collection-to-the-local-domain"></a>Étape 3 : Relier votre collection vers le domaine local ##


1. Sur la page de **Démarrage rapide** , cliquez sur **joindre un domaine local**.
2. Ajoutez le compte de service Azure RemoteApp à votre domaine Active Directory local. Vous aurez besoin du nom de domaine, unité d’organisation, nom d’utilisateur de compte de service et de mot de passe.

    Voici les informations recueillies si vous avez suivi les étapes de [Configuration d’Active Directory pour Azure RemoteApp](remoteapp-ad.md).


## <a name="step-4-link-to-an-azure-remoteapp-image"></a>Étape 4 : Les liens d’une image d’Azure RemoteApp ##

Une image du modèle Azure RemoteApp contient les programmes que vous souhaitez partager avec les utilisateurs. Vous pouvez créer une nouvelle [image du modèle](remoteapp-imageoptions.md) ou créer un lien vers une image existante (une déjà importé ou téléchargées vers Azure RemoteApp). Vous pouvez également lier à une des Azure RemoteApp [images du modèle](remoteapp-images.md) qui contient Office 365 ou les programmes Office 2013 (pour un essai).

Si vous téléchargez la nouvelle image, vous devez entrer le nom et choisir l’emplacement de l’image. Sur la page suivante de l’Assistant, vous voyez un jeu d’applets de commande PowerShell - copie et l’exécution de ces applets de commande à partir d’une invite de Windows PowerShell élevée à télécharger l’image spécifiée.

Si vous le liez à une image du modèle existant, spécifiez simplement un abonnement Azure associé, l’emplacement et le nom de l’image.



## <a name="step-5-configure-active-directory-directory-synchronization"></a>Étape 5 : Configuration de la synchronisation d’annuaire Active Directory ##

Azure RemoteApp nécessite que vous intégrer à Active Directory de Azure par soit 1) configuration Azure Active Directory Sync avec l’option de synchronisation de mot de passe, ou 2) configuration Azure Active Directory synchronisation sans l’option de synchronisation de mot de passe, mais à l’aide d’un domaine qui est fédéré ADFS.

Extraire les [annonces Connect](https://blogs.technet.microsoft.com/enterprisemobility/2014/08/04/connecting-ad-and-azure-ad-only-4-clicks-with-azure-ad-connect/) : cet article vous permet de configurer l’intégration d’annuaire en 4 étapes.

Pour la planification des informations et des procédures détaillées, reportez-vous à la section [planning de synchronisation d’annuaire](http://msdn.microsoft.com//library/azure/hh967642.aspx) .

## <a name="step-6-publish-apps"></a>Étape 6 : Publier des applications ##

Une application Azure RemoteApp est l’application ou le programme que vous fournissez à vos utilisateurs. Il se trouve dans l’image de modèle que vous avez téléchargé pour la collection. Lorsqu’un utilisateur accède à une application, il s’ouvre automatiquement dans leur environnement local, mais il est réellement exécuté dans Azure.

Avant que vos utilisateurs peuvent accéder aux applications, vous devez les publier – ainsi, vos utilisateurs accéder les applications via le client Bureau à distance.

Vous pouvez publier plusieurs applications à votre collection. À partir de la page de publication, cliquez sur **Publier** pour ajouter une application. Vous pouvez soit publier dans le menu **Démarrer** de l’image du modèle ou en spécifiant le chemin d’accès sur l’image pour l’application du modèle. Si vous choisissez d’ajouter dans le menu **Démarrer** , choisissez le programme à ajouter. Si vous choisissez de fournir le chemin d’accès à l’application, fournir un nom pour l’application et le chemin d’accès où il est installé sur l’image du modèle.

## <a name="step-7-configure-user-access"></a>Étape 7 : Configurer l’accès utilisateur ##

Maintenant que vous avez créé votre collection, vous devez ajouter les utilisateurs que vous souhaitez être en mesure d’utiliser vos ressources à distance. Les utilisateurs que vous offrent un accès doivent exister dans les clients Active Directory associé à l’abonnement que vous avez utilisé pour créer cette collection Azure RemoteApp.

1.  À partir de la page de démarrage rapide, cliquez sur **configurer l’accès des utilisateurs**.
2.  Permet d’entrer le compte de travail (à partir de Active Directory) ou le compte Microsoft que vous souhaitez accorder l’accès.

    **Remarques :**

    Assurez-vous que vous utilisez la “user@domain.com” format.

    Si vous utilisez Office 365 ProPlus dans votre collection, vous devez utiliser les identités Active Directory pour vos utilisateurs. Cela permet de valider la licence.


3.  Une fois que les utilisateurs sont validés, cliquez sur **Enregistrer**.


## <a name="next-steps"></a>Étapes suivantes ##
C’est tout - vous avez correctement créé et déployé votre collection hybride de RemoteApp d’Azure. L’étape suivante consiste à inviter les utilisateurs à télécharger et installer le client Bureau à distance. Vous pouvez trouver l’URL pour le client sur la page de démarrage rapide de Azure RemoteApp. Ensuite, que les utilisateurs ouvrent dans le client et accéder les applications que vous avez publié.



### <a name="help-us-help-you"></a>Aidez-nous à vous aider
Saviez-vous qu’en plus de cet article d’évaluation et faire des commentaires vers le bas ci-dessous, vous pouvez modifier l’article lui-même ? Un élément manquant ? Un problème ? Écrire quelque chose de déroutant juste ? Faites défiler et cliquez sur **Modifier sur GitHub** pour apporter des modifications - ceux arriveront à nous pour révision, et puis, une fois que nous avons approuver les, vous verrez vos modifications et les améliorations ici.
