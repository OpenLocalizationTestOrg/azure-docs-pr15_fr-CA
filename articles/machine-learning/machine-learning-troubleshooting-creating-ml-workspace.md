<properties
    pageTitle="Résolution des problèmes : Créer et vous connecter à un espace de travail d’apprentissage automatique | Microsoft Azure"
    description="Solutions pour les problèmes courants lors de la création et la connexion à un espace de travail formation de Machine Azure"
    services="machine-learning"
    documentationCenter=""
    authors="garyericson"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/09/2016"
    ms.author="garye"/>


# <a name="troubleshooting-guide-create-and-connect-to-an-machine-learning-workspace"></a>Guide de dépannage : créer et vous connecter à un espace de travail d’apprentissage automatique

Ce guide fournit des solutions pour certains fréquemment défis lorsque vous définissez des espaces de travail de formation de Machine Azure.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="workspace-owner"></a>Propriétaire de l’espace de travail

Lorsque vous créez un nouvel espace de travail de formation de la Machine, le code que vous saisissez dans le champ propriétaire de l’espace de travail doit être un compte valide de Microsoft (anciennement Windows Live ID),, par exemple, john-contoso@live.com ou john-contoso@hotmail.com. Il ne peut pas être un compte non Microsoft, comme votre compte de messagerie de l’entreprise. Pour créer un compte gratuit de Microsoft, allez dans [www.live.com](http://www.live.com).

Notez que le compte que vous avez utilisé pour vous connecter au portail Azure pour créer de l’espace de travail pas automatiquement a l’autorisation requise pour *Ouvrir* cet espace de travail, sauf si vous spécifiez ce compte comme propriétaire. Pour ouvrir un espace de travail dans le Studio d’apprentissage Machine, vous devez être connecté à la Account de Microsoft qui a été défini comme propriétaire de l’espace de travail, ou vous devez recevoir une invitation au propriétaire à rejoindre l’espace de travail. À partir du portail classique Azure, cependant, vous pouvez *Gérer* l’espace de travail, ce qui inclut la possibilité de changer le propriétaire et la configuration de l’accès.

Pour plus d’informations sur la gestion d’un espace de travail, consultez [Gestion d’un espace de travail de formation de Machine d’Azure].

[Gérer un espace de travail formation de Machine Azure]: machine-learning-manage-workspace.md

## <a name="allowed-regions"></a>Zones autorisées

Apprentissage automatique est actuellement disponible dans un nombre limité de régions. Si votre abonnement n’inclut pas une de ces régions, vous pouvez voir le message d’erreur, « Vous ne possédez aucun abonnement dans les zones autorisées. »

Pour demander qu’une région est ajouté à votre abonnement, sélectionnez **Contacter le support technique Microsoft** à partir du portail classique Azure, choisissez le type de problème de **facturation** et suivez les instructions pour soumettre votre demande.

![Contactez le support technique de Microsoft][screen1]

## <a name="storage-account"></a>Compte de stockage

Le service formation de Machine a besoin d’un compte de stockage pour stocker des données. Vous pouvez utiliser un compte de stockage existant, ou vous pouvez créer un nouveau compte de stockage lorsque vous créez le nouvel espace de travail d’apprentissage automatique (si vous avez des quotas pour créer un nouveau compte de stockage).

<!-- These instructions no longer work, but I'm not sure what to replace them with
To see if you can create a new storage account, in the Classic Portal, go to **Settings** and then click **Usage**.
-->

![Créer l’espace de travail][screen2]

Après avoir créé le nouvel espace de travail d’apprentissage automatique, vous pouvez vous connecter au Studio de formation de Machine en utilisant le compte Microsoft que vous avez spécifié comme propriétaire de l’espace de travail. Si vous rencontrez le message d’erreur « Espace de travail introuvable » (semblable à la capture d’écran suivante), utilisez les étapes suivantes pour supprimer les cookies de votre navigateur.

![Espace de travail introuvable][screen3]

**Pour supprimer les cookies de navigateur**

Si vous utilisez Internet Explorer, cliquez sur le bouton **Outils** , dans le coin supérieur droit et sélectionnez **options Internet**.  

![Options Internet][screen4]

Sous l’onglet **Général** , cliquez sur **Supprimer...**

![Onglet Général][screen5]

Dans la boîte de dialogue **Supprimer l’historique de navigation** , vérifiez que **les Cookies et les données des sites Web** est activée, puis cliquez sur **Supprimer**.

![Supprimer les cookies][screen6]

Une fois les cookies supprimés, redémarrez le navigateur et passez à la page [Formation de Machine Microsoft Azure](https://studio.azureml.net) . Lorsque vous êtes invité à entrer un nom d’utilisateur et le mot de passe, entrez le même compte Microsoft que vous avez spécifié comme propriétaire de l’espace de travail.

Notre objectif est de rendre l’expérience d’apprentissage automatique aussi transparente que possible. Veuillez publier des commentaires et des problèmes liés à la [formation de Machine Azure forum](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning) pour nous aider à mieux vous servir.

[screen1]:media/machine-learning-troubleshooting-creating-ml-workspace/screen1.png
[screen2]:media/machine-learning-troubleshooting-creating-ml-workspace/screen2.png
[screen3]:media/machine-learning-troubleshooting-creating-ml-workspace/screen3.png
[screen4]:media/machine-learning-troubleshooting-creating-ml-workspace/screen4.png
[screen5]:media/machine-learning-troubleshooting-creating-ml-workspace/screen5.png
[screen6]:media/machine-learning-troubleshooting-creating-ml-workspace/screen6.png
