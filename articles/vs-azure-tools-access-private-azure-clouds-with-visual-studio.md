<properties 
   pageTitle="L’accès privés nuages Azure avec Visual Studio | Microsoft Azure"
   description="Découvrez comment accéder aux ressources de cloud privé à l’aide de Visual Studio."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="accessing-private-azure-clouds-with-visual-studio"></a>L’accès privés nuages Azure avec Visual Studio

##<a name="overview"></a>Vue d’ensemble

Par défaut, Visual Studio prend en charge les points de terminaison REST public Azure cloud. Cela peut être un problème, cependant, si vous utilisez Visual Studio avec Azure cloud privé. Vous pouvez utiliser des certificats pour configurer Visual Studio pour accéder aux points de terminaison REST Azure de cloud privé. Vous pouvez obtenir ces certificats via votre Azure publier le fichier de paramètres.

## <a name="to-access-a-private-azure-cloud-in-visual-studio"></a>Pour accéder à un Azure privé nuage dans Visual Studio

1. Dans [Azure portal classique](http://go.microsoft.com/fwlink/?LinkID=213885) pour le cloud privé, télécharger votre fichier de paramètres de publication, ou contactez votre administrateur pour un fichier de paramètres de publication. Dans la version publique d’Azure, le lien à télécharger est [https://manage.windowsazure.com/publishsettings/](https://manage.windowsazure.com/publishsettings/). (Le fichier que vous téléchargez doit avoir une extension .publishsettings.)

1. Dans l' **Explorateur de serveurs** dans Visual Studio, choisissez le nœud **Azure** et, dans le menu contextuel, choisissez la commande **Gérer les abonnements** .

    ![Gérer les commandes d’abonnements](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790778.png)

1. Dans la boîte de dialogue **Gérer les abonnements Microsoft Azure** , cliquez sur l’onglet **certificats** , puis cliquez sur le bouton **Importer** .

    ![Importation de certificats Azure](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790779.png)

1. Dans la boîte de dialogue **Importer les abonnements Microsoft Azure** , accédez au dossier dans lequel vous enregistré le fichier de paramètres de publication et sélectionnez le fichier, puis cliquez sur le bouton **Importer** . Il importe les certificats dans le fichier de paramètres de publication dans Visual Studio. Vous devez désormais être en mesure d’interagir avec vos ressources de cloud privé.

    ![Importation des paramètres de publication](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790780.png)

## <a name="next-steps"></a>Étapes suivantes

[Publication d’un Service Cloud Azure à partir de Visual Studio](https://msdn.microsoft.com/library/azure/ee460772.aspx)

[Comment : publient de téléchargement et importer les paramètres et les informations d’abonnement](https://msdn.microsoft.com/library/dn385850(v=nav.70).aspx)

