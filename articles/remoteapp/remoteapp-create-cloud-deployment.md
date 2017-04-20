<properties 
    pageTitle="Comment créer une collection de nuage d’Azure RemoteApp | Microsoft Azure" 
    description="Apprenez à créer un déploiement d’Azure RemoteApp qui enregistre les données dans le nuage Azure." 
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

# <a name="how-to-create-a-cloud-collection-of-azure-remoteapp"></a>Comment créer une collection de nuage d’Azure RemoteApp

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lecture de l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus de détails.

Il existe deux types de [collections de RemoteApp d’Azure](remoteapp-collections.md): 

- Cloud : réside entièrement dans Azure. Vous pouvez choisir d’enregistrer toutes les données dans le nuage (pour une collection nuage uniquement) ou de votre collection de se connecter à une VNET et enregistrer les données.   
- Hybride : inclut un réseau virtuel pour l’accès local - cela nécessite l’utilisation d’AD Azure et un environnement d’Active Directory sur site.

Ce didacticiel vous guide tout au long du processus de création d’une collection de nuage. Il existe quatre étapes : 

1.  Créer une collection Azure RemoteApp.
2.  Vous pouvez également configurer la synchronisation d’annuaire. Si vous utilisez Active Directory Azure + Active Directory, vous devez synchroniser les utilisateurs, les contacts et les mots de passe à partir d’Active Directory sur site pour vos clients AD Azure.
5.  Publier des applications.
6.  Configurer l’accès utilisateur.


**Avant de commencer**

Vous devez effectuer les tâches suivantes avant de créer la collection :

- [Inscrivez-vous](https://azure.microsoft.com/services/remoteapp/) pour Azure RemoteApp. 
- Rassembler des informations sur les utilisateurs que vous souhaitez accorder l’accès à. Ce peut être soit les informations de compte Microsoft ou les informations de compte de travail Active Directory pour les utilisateurs.
- Cette procédure suppose que vous êtes soit d’utiliser une des images de modèle fournis dans le cadre de votre abonnement ou que vous avez déjà téléchargé l’image du modèle à utiliser. Si vous avez besoin de télécharger une image autre modèle, vous pouvez le faire à partir de la page d’Images du modèle. Cliquez simplement sur le **téléchargement d’une image du modèle** et suivez les étapes de l’Assistant. 
- Vous souhaitez utiliser l’image d’Office 365 ProPlus ? Extraire les informations [ici](remoteapp-officesubscription.md).
- Si vous souhaitez fournir des applications personnalisées ou LOB de programmes ? Créer une nouvelle [image](remoteapp-imageoptions.md) et utilisez-la dans votre collection de nuage.
- Déterminer si vous devez vous connecter à un VNET. Si vous choisissez de vous connecter à un VNET, assurez-vous qu’il respecte les [instructions de dimensionnement](remoteapp-vnetsizing.md) et qu’il [peut se connecter à RemoteApp](remoteapp-vnet.md). Consultez l' [article de planification VNET ](remoteapp-planvnet.md)pour plus d’informations.
- Si vous utilisez un VNET, décidez si vous souhaitez joindre à votre domaine Active Directory local.

## <a name="step-1-create-a-cloud-collection---with-or-without-a-vnet"></a>Étape 1 : Créer une collection de nuage - avec ou sans un VNET##


Utilisez les étapes suivantes pour **créer une collection de nuage uniquement**:

1. Dans le portail de gestion, accédez à la page de RemoteApp.
2. Cliquez sur **Nouveau > Création rapide**.
3. Entrez un nom pour la collection et sélectionnez votre région.
4. Sélectionnez le plan que vous souhaitez utiliser - standard ou de base.
5. Choisissez le modèle à utiliser pour cette collection. 

    **Conseil :** Votre abonnement à RemoteApp est fourni avec [des images de modèle](remoteapp-images.md) contenant Office 365 ou Office 2013 (pour un essai) programmes, certains publiée (par exemple, Word) et autres prêt à publier. Vous pouvez également créer une nouvelle [image](remoteapp-imageoptions.md) et l’utiliser dans votre collection de nuage.


1. Cliquez sur **créer un RemoteApp collection**.
    
    **Important :** Il peut prendre jusqu'à 30 minutes pour provisionner votre collection.

Après avoir créé votre collection Azure RemoteApp, double-cliquez sur le nom de la collection. Qui vous amènera à la page de **Démarrage rapide** : il s’agit où vous avez terminé la configuration de la collection.

Utilisez les étapes suivantes pour créer un **nuage + collection de VNET**:

1. Dans le portail de gestion, accédez à la page d’Azure RemoteApp.
2. Cliquez sur **Nouveau** > **créer avec VNET**.
3. Entrez un nom pour la collection.
4. Sélectionnez le plan que vous souhaitez utiliser - standard ou de base.
5. Choisissez le VNET que vous avez déjà créé. Ne savez pas comment procéder ? Pour l’instant, les étapes sont dans la rubrique [hybride](remoteapp-create-hybrid-deployment.md) .
6. Si vous souhaitez joindre votre collection sur votre domaine. Si Oui, vous devrez AD Connect permet d’intégrer AD Azure et votre environnement Active Directory. Qui est traitée ci-dessous à **l’étape 2**.
6. Cliquez sur **créer un RemoteApp collection**.


## <a name="step-2-configure-active-directory-directory-synchronization-optional"></a>Étape 2 : Configurer la synchronisation d’annuaire Active Directory (facultative) ##

Si vous souhaitez utiliser Active Directory, Azure RemoteApp nécessite la synchronisation d’annuaire entre Azure Active Directory et sur les sites Active Directory pour synchroniser les utilisateurs, les contacts et les mots de passe pour vos clients d’Azure Active Directory. Pour obtenir des informations, reportez-vous à la section [Configuration de Active Directory pour Azure RemoteApp](remoteapp-ad.md) . Vous pouvez également accéder directement à [Active Directory Connect](https://blogs.technet.microsoft.com/enterprisemobility/2014/08/04/connecting-ad-and-azure-ad-only-4-clicks-with-azure-ad-connect/) pour plus d’informations.

## <a name="step-3-publish-apps"></a>Étape 3 : Publier des applications ##

Une application Azure RemoteApp est l’application ou le programme que vous fournissez à vos utilisateurs. Il se trouve dans l’image de modèle que vous avez téléchargé pour la collection. Lorsqu’un utilisateur accède à une application, l’application s’ouvre automatiquement dans leur environnement local, mais il est réellement exécuté sur une machine virtuelle dans Azure. 

Avant que vos utilisateurs peuvent accéder aux applications, vous devez les publier – applications permet de publier vos utilisateurs accèdent les applications via le client Bureau à distance.
 
Vous pouvez publier plusieurs applications à votre collection Azure RemoteApp. À partir de la page de publication, cliquez sur **Publier** pour ajouter un programme. Vous pouvez soit publier dans le menu **Démarrer** de l’image du modèle ou en spécifiant le chemin d’accès sur l’image pour l’application du modèle. Si vous choisissez d’ajouter dans le menu **Démarrer** , choisissez l’application à publier. Si vous choisissez de fournir le chemin d’accès à l’application, fournir un nom pour l’application et le chemin d’accès où il est installé sur l’image du modèle.

## <a name="step-4-configure-user-access"></a>Étape 4 : Configurer l’accès utilisateur ##

Maintenant que vous avez créé votre collection, vous devez ajouter les utilisateurs que vous souhaitez être en mesure d’utiliser vos ressources à distance. Si vous utilisez Active Directory, les utilisateurs que vous offrent un accès doivent exister dans les clients Active Directory associé à l’abonnement que vous avez utilisé pour créer cette collection.

1.  À partir de la page de démarrage rapide, cliquez sur **configurer l’accès des utilisateurs**. 
2.  Permet d’entrer le compte de travail (à partir de Active Directory) ou le compte Microsoft que vous souhaitez accorder l’accès.

    **Remarques :** 

    Assurez-vous que vous utilisez la “user@domain.com” format.

    Si vous utilisez Office 365 ProPlus dans votre collection, vous devez utiliser les identités Active Directory pour vos utilisateurs. Cela permet de valider la licence. 

3.  Une fois que les utilisateurs sont validés, cliquez sur **Enregistrer**.


## <a name="next-steps"></a>Étapes suivantes ##

C’est tout - vous avez correctement créé et déployé votre collection de nuage Azure RemoteApp. L’étape suivante consiste à inviter les utilisateurs à télécharger et installer le client Bureau à distance. Vous pouvez trouver l’URL pour le client sur la page de démarrage rapide de Azure RemoteApp. Ensuite, que les utilisateurs ouvrent dans le client et accéder les applications que vous avez publié.

### <a name="help-us-help-you"></a>Aidez-nous à vous aider 
Saviez-vous qu’en plus de cet article d’évaluation et faire des commentaires vers le bas ci-dessous, vous pouvez modifier l’article lui-même ? Un élément manquant ? Un problème ? Écrire quelque chose de déroutant juste ? Faites défiler et cliquez sur **Modifier sur GitHub** pour apporter des modifications - ceux arriveront à nous pour révision, et puis, une fois que nous avons approuver les, vous verrez vos modifications et les améliorations ici.