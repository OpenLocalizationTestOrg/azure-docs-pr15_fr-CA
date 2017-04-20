<properties 
   pageTitle="Installer les mises à jour sur une baie virtuelle StorSimple | Microsoft Azure"
   description="Explique comment utiliser l’interface utilisateur du web StorSimple Virtual Array pour appliquer les mises à jour à l’aide de la méthode de portail et de correctif"
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="09/07/2016"
   ms.author="alkohli" />

# <a name="install-updates-on-your-storsimple-virtual-array"></a>Installer les mises à jour sur votre tableau virtuel StorSimple

## <a name="overview"></a>Vue d’ensemble

Cet article décrit les étapes nécessaires pour installer des mises à jour sur votre tableau de StorSimple virtuel via l’interface utilisateur de web local et via le portail classique Azure. Vous devez appliquer les mises à jour logicielles ou correctifs à jour votre tableau virtuel StorSimple. 

Gardez à l’esprit que l’installation d’une mise à jour ou un correctif redémarre votre appareil. Étant donné que le tableau virtuel StorSimple est un périphérique de nœud unique, les e/s en cours est interrompue et votre périphérique subit des interruptions de service. 

Avant d’appliquer une mise à jour, nous vous conseillons les volumes ou les partages en mode hors connexion sur l’hôte premier et puis sur le périphérique. Cela permet de réduire toute possibilité de corruption des données.

> [AZURE.IMPORTANT] Si vous exécutez la mise à jour 0,1 ou versions du logiciel GA, vous devez utiliser la méthode correctif via le web local de l’interface utilisateur pour installer la mise à jour de 0,3. Si vous exécutez la mise à jour 0,2, nous vous recommandons d’installer les mises à jour via le portail classique Azure.

## <a name="use-the-local-web-ui"></a>Utilisez l’interface utilisateur du web local 
 
Deux étapes sont nécessaires lors de l’utilisation de l’interface utilisateur de web local :

- Télécharger la mise à jour ou le correctif logiciel
- Installez la mise à jour ou le correctif logiciel

### <a name="download-the-update-or-the-hotfix"></a>Télécharger la mise à jour ou le correctif logiciel

Procédez comme suit pour télécharger la mise à jour de logiciel à partir du catalogue Microsoft Update.

#### <a name="to-download-the-update-or-the-hotfix"></a>Pour télécharger la mise à jour ou le correctif logiciel

1. Démarrez Internet Explorer et accédez à [http://catalog.update.microsoft.com](http://catalog.update.microsoft.com).

2. Si c’est la première fois à l’aide du catalogue Microsoft Update sur cet ordinateur, cliquez sur **installer** lorsque vous êtes invité à installer le module complémentaire du catalogue Microsoft Update.
  
3. Dans la zone de recherche du catalogue Microsoft Update, entrez le numéro de la Base de connaissances (KB) du correctif que vous souhaitez télécharger. Entrez **3182061** pour la mise à jour de 0,3 et puis cliquez sur **Rechercher**.

    La liste de correctif s’affiche, par exemple, **mise à jour de tableau StorSimple virtuelle 0,3**.

    ![Catalogue de recherche](./media/storsimple-ova-install-update-01/download1.png)

4. Cliquez sur **Ajouter**. La mise à jour est ajouté au panier.

5. Cliquez sur **Afficher le panier**.

6. Cliquez sur **Télécharger**. Spécifiez ou **accédez** à un emplacement local où vous souhaitez que les téléchargements s’affiche. Les mises à jour sont téléchargés à l’emplacement spécifié et placés dans un sous-dossier avec le même nom que la mise à jour. Le dossier peut également être copié sur un partage réseau qui est accessible à partir du périphérique.

7. Ouvrez le dossier copié, vous devriez voir un fichier de Package autonome Microsoft `WindowsTH-KB3011067-x64`. Ce fichier est utilisé pour installer la mise à jour ou le correctif.


### <a name="install-the-update-or-the-hotfix"></a>Installez la mise à jour ou le correctif logiciel

Avant l’installation du correctif ou de mise à jour, assurez-vous que vous disposez de la mise à jour ou le correctif téléchargé soit localement sur votre hôte ou accessible via un partage réseau. 

Cette méthode permet d’installer des mises à jour sur un périphérique exécutant GA ou mettre à jour les versions des logiciels 0,1. Cette procédure prend moins de 2 minutes. Procédez comme suit pour installer la mise à jour ou le correctif.


#### <a name="to-install-the-update-or-the-hotfix"></a>Pour installer la mise à jour ou le correctif logiciel

1. Dans l’interface utilisateur web local, accédez à **gestion** > **Mise à jour logicielle**.

    ![Dispositif de mise à jour](./media/storsimple-ova-install-update-01/update1m.png)

2. **Mettre à jour le chemin d’accès de fichier**, entrez le nom de fichier pour la mise à jour ou le correctif logiciel. Vous pouvez également parcourir le fichier d’installation de mise à jour ou le correctif si placé sur un partage réseau. Cliquez sur **Appliquer**.

    ![Dispositif de mise à jour](./media/storsimple-ova-install-update-01/update2m.png)

3.  Un avertissement s’affiche. Étant donné cette est un périphérique de nœud unique, après la mise à jour est appliquée, le périphérique redémarre et il est temps d’arrêt. Cliquez sur l’icône de vérification.

    ![Dispositif de mise à jour](./media/storsimple-ova-install-update-01/update3m.png)

4. La mise à jour commence. Une fois que le périphérique est correctement mis à jour, il redémarre. L’interface utilisateur locale n’est pas accessible dans cette durée.

    ![Dispositif de mise à jour](./media/storsimple-ova-install-update-01/update5m.png)

5. Une fois le redémarrage terminé, vous accédez à la page de **connexion** . Pour vérifier que le logiciel de périphérique a mis à jour, dans l’interface utilisateur, de site web local, accédez à **Gestion des** > **Mise à jour logicielle**. La version du logiciel affichés doit être **10.0.0.0.0.10288.0** pour la mise à jour de 0,3.

    > [AZURE.NOTE] Nous signaler les versions du logiciel de façon légèrement différente dans l’interface utilisateur de site web local et le portail classique Azure. Par exemple, **10.0.0.0.0.10288** des rapports de l’interface utilisateur de web local et le portail classique Azure rapports **10.0.10288.0** pour la même version. 

    ![Dispositif de mise à jour](./media/storsimple-ova-install-update-01/update6m.png)





## <a name="use-the-azure-classic-portal"></a>Utiliser le portail classique Azure

Si vous exécutez la mise à jour de 0,2, nous vous recommandons d’installer les mises à jour via le portail classique Azure. La procédure de portail, l’utilisateur d’analyser, téléchargez, puis installez les mises à jour. Cette procédure prend environ 7 minutes. Procédez comme suit pour installer la mise à jour ou le correctif.

[AZURE.INCLUDE [storsimple-ova-install-update-via-portal](../../includes/storsimple-ova-install-update-via-portal.md)]

Une fois l’installation terminée (comme indiqué par le statut de la tâche à 100 %), accédez à **périphériques > Maintenance > mises à jour de logiciel**. La version du logiciel affichés doit être 10.0.10288.0.

![Dispositif de mise à jour](./media/storsimple-ova-install-update-01/azupdate12m.png)

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur [l’administration de votre tableau virtuel StorSimple](storsimple-ova-web-ui-admin.md).
