
<properties
    pageTitle="Nouveautés dans Azure RemoteApp ? | Microsoft Azure"
    description="En savoir plus sur les modifications et les améliorations apportées à Azure RemoteApp"
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />



# <a name="whats-new-in-azure-remoteapp"></a>Nouveautés dans Azure RemoteApp ?

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lecture de l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus de détails.

Un des avantages de Azure RemoteApp est que nous nous efforçons toujours pour l’améliorer. Chaque fois que nous faisons, nous annoncerons ici de ces modifications.

## <a name="future-updates"></a>Mises à jour futures
Bonjour - Saviez-vous que l’équipe Azure RemoteApp publie des mises à jour mensuelles sur le blog RDS ? Vous pouvez rechercher non seulement ce qui est des modification dans Azure RemoteApp mais également d’autres informations sur l’utilisation de RDS. Consultez son blog, [Blog de Services Bureau à distance](https://blogs.msdn.microsoft.com/rds/), pour plus d’informations. Par exemple, quelques semaines, ils validé une écriture sur [de levage et de déplacement de charges de travail avec RemoteApp d’Azure et d’Azure AD](https://blogs.msdn.microsoft.com/rds/2016/01/19/lift-and-shift-your-workloads-with-azure-remoteapp-and-azure-ad-domain-services/).
 
## <a name="september-2015"></a>Septembre 2015
- Infopath ajouté à l’image du modèle et de la galerie de Microsoft Office 365. Si vous souhaitez partager d’Infopath, veillez à mettre à jour vos collections avec la dernière image.
- Mises à jour du client :
    - Client de Windows mis à jour pour permettre aux utilisateurs de partager des commentaires, notamment en termes de problèmes de connexion.
    - client d’iOS mis à jour pour résoudre les messages d’erreur et résoudre un problème dans lequel vos informations d’identification a expiré plus tôt que prévu.
- Nous travaillons sur l’obtention de support Office 2016 testé. Une fois cette opération terminée, recherchez des images mises à jour.
- Publication d’un nouvel article sur les [différences entre les collections du nuage et hybrides](remoteapp-collections.md) - cela vous permettra de choisir le type de collection qui convient le mieux pour vos applications - nuage uniquement nuage + VNET ou hybride.
- Pour partager de QuickBooks à l’aide de RemoteApp d’Azure, mais n’êtes pas sûr de la procédure ? Consultez [l’article de nouveau d’Éric](remoteapp-quickbooks.md) vous indiquant exactement la procédure à suivre.

## <a name="august-2015"></a>Août 2015
Importante apportée au mois d’août - Voici les grandes lignes :

- Vous pouvez désormais utiliser un VNET Azure avec une collection de nuage ! Consultez les [instructions de création de cloud](remoteapp-create-cloud-deployment.md) pour les nouvelles étapes.
- Fait en sorte qu’ajouter des applications au menu **Démarrer **pour le client Windows RemoteApp. Applications s’affichera dans la liste des applications, et vous pouvez les épingler au menu **Démarrer **de Windows.
- Ajouter une nouvelle image dans la galerie d’Azure VM - Windows Server à distance hôte de Session bureau avec Microsoft Office 365 ProPlus.
- Fixe le client Mac applications avec fenêtres modales cessera de congélation.
- Décrit comment vous pouvez utiliser votre [abonnement Office 365 ProPlus](remoteapp-officesubscription.md) avec Azure RemoteApp.
- Détail comment vous pouvez [sécuriser les applications et les données](remoteapp-secure.md) de votre collection Azure RemoteApp.

## <a name="july-2015"></a>Juillet 2015

Juillet définie le stade pour apportés au mois d’août, il n’est pas beaucoup parler maintenant, principalement les mises à jour doc. Vous trouverez ici les modifications les plus récentes :

- Ajouter un onglet **prise en charge** au portail pour pouvoir accéder plus facilement à des ressources de support, telles que les forums.
- Retravailler les informations de dépannage pour la création d’une collection hybride. Découvrez [les dernières et les meilleures](remoteapp-hybridtrouble.md) pour obtenir des conseils comme, comment identifier les ports à configurer pour votre VNET.
- Décrit comment les [données utilisateur](remoteapp-upd.md) est créée et enregistrée dans Azure RemoteApp.
- Décrit comment faire pour [Verrouiller les applications](remoteapp-secure.md).
- Publier les [applets de commande Azure RemoteApp](https://msdn.microsoft.com/library/mt428031.aspx).
- Et enfin, nous avons commencé une conversation avec certains utilisateurs Azure RemoteApp à propos de la terminologie. Recherchez les modifications apportées à la façon dont nous faisons référence à ces différentes options.

## <a name="june-2015"></a>Juin 2015

Nombre de modifications ! L’équipe a été très occupé de juin :

- Repensé le Azure RemoteApp de [page d’accueil](https://www.remoteapp.windowsazure.com/) - Jetez-y un œil régulièrement !
- Mise à jour de logiciel dans toutes les images disponibles dans le cadre de votre abonnement.
- Apporté des améliorations aux collections hybride, y compris forcée prise en charge de tunneling et de la vérification de la taille du sous-réseau IP avant de tenter de créer la collection.
- Qui a découvert le * générique ne fonctionne pas pour les webcams. Au lieu de cela, vous devez spécifier l’ID d’instance ou d’un GUID. Nous allons mettre à jour les informations de redirection pour refléter celle.
- A vous pouvez donc ajouter des logiciels antivirus personnalisés à votre image lorsque vous créez une image du modèle dans la galerie d’Azure.

Nous avons plus de modifications introduites en juillet, afin que nous allons bientôt être avec une autre mise à jour de.

## <a name="may-2015"></a>Mai 2015

Un certain nombre d’ajouts (et de mois) depuis que nous avons créé cette rubrique, afin que cette liste triche un peu et commence au début du mois de mars ont été par le biais de mai. Découvrez ces nouvelles fonctionnalités :

- Automatiser tout - Azure RemoteApp a maintenant des [applets de commande du module PowerShell de Azure](remoteapp-tutorial-arawithpowershell.md).
- [Créer une image d’Azure RemoteApp à partir d’un ordinateur virtuel Azure](remoteapp-image-on-azurevm.md). Permet de télécharger votre image personnalisée sur Azure beaucoup plus rapide.
- Utilisez un VNET Azure au lieu d’un VNET RemoteApp pour connecter des ressources de votre réseau d’entreprise vers Azure. Nous avons mis à jour les [instructions de collection hybride](remoteapp-create-hybrid-deployment.md) pour vous guider dans la création d’un VNET d’Azure (il s’agit d’étape 1).
- À propos de VNETs, consultez [le Guide de nouveau](remoteapp-vnetsizing.md) autour des limites de taille VNET et limitations.
- Et en parlant de limites - simplement quels sont les [paramètres par défaut et des limites de service](../azure-subscription-service-limits.md)?

Vous souhaitez en savoir plus sur Azure RemoteApp ? L’équipe de RemoteApp était en vigueur au Ignite il y a quelques semaines. Extraire les vidéo d’Eric, [les principes de base de Microsoft Azure RemoteApp la gestion et l’Administration](http://channel9.msdn.com/Events/Ignite/2015/BRK3868).

Vous avez besoin de voir RemoteApp Azure dans le monde réel ? Consultez le didacticiel [d’exécuter n’importe quelle application sur n’importe quel endroit de n’importe quel périphérique](remoteapp-anyapp.md) - il vous montre comment partager l’accès à vos utilisateurs, y compris le partage des fichiers de base de données. Nous avons également un didacticiel sur la [Création d’Office 365](remoteapp-tutorial-o365anywhere.md) le même exécuter sur n’importe quel périphérique.

Merci pour l’égorgement avec nous - sauvegarder le mois prochain avec plus de mises à jour.


### <a name="help-us-help-you"></a>Aidez-nous à vous aider
Saviez-vous qu’en plus de cet article d’évaluation et faire des commentaires vers le bas ci-dessous, vous pouvez modifier l’article lui-même ? Un élément manquant ? Un problème ? Écrire quelque chose de déroutant juste ? Faites défiler et cliquez sur **Modifier sur GitHub** pour apporter des modifications - ceux arriveront à nous pour révision, et puis, une fois que nous avons approuver les, vous verrez vos modifications et les améliorations ici.
