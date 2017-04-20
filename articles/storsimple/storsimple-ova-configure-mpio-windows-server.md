<properties 
   pageTitle="Configuration de MPIO sur votre hôte de tableau virtuel StorSimple | Microsoft Azure"
   description="Décrit comment configurer les proxy (Multipath i / o) pour votre StorSimple tableau virtuel connecté à un hôte exécutant Windows Server 2012 R2."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/04/2016"
   ms.author="alkohli" />

# <a name="configure-multipath-io-on-windows-server-host-for-the-storsimple-virtual-array"></a>Configurer MPIO sur un hôte Windows Server pour le tableau virtuel StorSimple

## <a name="overview"></a>Vue d’ensemble

Cet article décrit comment installer Multipath i/o (MPIO) sur votre hôte Windows Server et appliquer les paramètres de configuration spécifiques pour les volumes StorSimple seule puis vérifiez MPIO pour les volumes de StorSimple. La procédure suppose que votre tableau virtuel de StorSimple 1200 avec deux interfaces réseau est connecté à un hôte Windows Server avec deux interfaces réseau. Les informations contenues dans cet article s’applique uniquement au tableau virtuel. Pour plus d’informations sur les périphériques série 8000 de StorSimple, accédez à [Configuration de MPIO pour StorSimple hôte](storsimple-configure-mpio-windows-server.md). 

La fonctionnalité MPIO dans les configurations de stockage hautement disponibles et à tolérance de pannes de Windows Server vous aide à build. MPIO utilise des composants redondants de chemin d’accès physique, adaptateurs, câbles et commutateurs, pour créer des chemins d’accès logiques entre le serveur et le périphérique de stockage. S’il existe une défaillance d’un composant, à l’origine d’un chemin d’accès logique à échouer, logique de gestion multivoie utilise un autre chemin d’e/s afin que les applications peuvent toujours accéder à leurs données. De plus selon votre configuration, MPIO peut également améliorer les performances par l’équilibre la charge sur tous les chemins d’accès. Pour plus d’informations, consultez [vue d’ensemble MPIO]des(https://technet.microsoft.com/library/cc725907.aspx "fonctions et vue d’ensemble MPIO").  

Pour la haute disponibilité de votre solution de StorSimple, configuration MPIO sur les hôtes Windows Server connectés à votre tableau virtuel de StorSimple 1200 (également appelé le local périphérique virtuel). Les serveurs hôtes peuvent ensuite tolérer un lien, un réseau ou une défaillance de l’interface. 

Vous devez suivre ces étapes pour configurer MPIO : 

- Configuration préalable

- Étape 1 : Installer MPIO sur l’hôte Windows Server

- Étape 2 : Configuration de MPIO pour les volumes de StorSimple

- Étape 3 : Les volumes StorSimple de montage sur l’hôte

Chacune des étapes ci-dessus est abordée dans les sections suivantes.


## <a name="prerequisites"></a>Conditions préalables

Cette section détaille la configuration préalable pour l’hôte Windows et votre tableau virtuel.

### <a name="on-windows-server-host"></a>Sur un hôte Windows Server

-  Assurez-vous que votre hôte Windows Server a 2 interfaces réseau activé.


### <a name="on-storsimple-virtual-array"></a>Sur le tableau virtuel de StorSimple

- Le tableau virtuel doit être configuré comme un serveur iSCSI. Pour plus d’informations, voir [configurer le tableau virtuel comme un serveur iSCSI](storsimple-ova-deploy3-iscsi-setup.md). Une ou plusieurs interfaces réseau doivent être activées sur la baie.   

- Les interfaces réseau sur votre baie virtuelle doivent être accessibles à partir de l’hôte Windows Server.

- Un ou plusieurs volumes doivent être créés sur votre tableau virtuel StorSimple. Pour plus d’informations, voir [Ajout d’un volume](storsimple-ova-deploy3-iscsi-setup.md#step-3-add-a-volume) sur votre baie virtuelle StorSimple 1200. Dans cette procédure, nous avons créé 3 volumes (2 localement épinglés et 1 hiérarchisé du volume comme indiqué ci-dessous) dans le tableau virtuel.
    
    ![mpio0](./media/storsimple-ova-configure-mpio-windows-server/mpio0.png)

### <a name="hardware-configuration-for-storsimple-virtual-array"></a>Configuration matérielle pour tableau virtuel de StorSimple

La figure ci-dessous illustre la configuration matérielle de haute disponibilité et l’équilibrage de la charge de chemins multiples pour votre hôte Windows et un tableau virtuel de StorSimple utilisé dans cette procédure.  

![configuration matérielle de MPIO](./media/storsimple-ova-configure-mpio-windows-server/1200hardwareconfig.png)

Comme illustré dans la figure précédente :

- Votre tableau virtuel StorSimple sur Hyper-V est un périphérique actif seul nœud configuré comme un serveur iSCSI.

- Deux interfaces réseau virtuel sont activées sur votre baie. Dans le site web local d’interface utilisateur de votre tableau virtuel 1200, vérifiez que les deux interfaces réseau sont activées en accédant aux **Paramètres de réseau** , comme illustré ci-dessous :

    ![Interfaces réseau activés sur 1200](./media/storsimple-ova-configure-mpio-windows-server/mpio9.png)
    
    Notez les adresses IPv4 des interfaces réseau activée (Ethernet, Ethernet 2 par défaut) et les enregistrer pour une utilisation ultérieure sur l’hôte.

- Deux interfaces réseau sont activées sur votre hôte Windows Server. Si les interfaces connectées pour l’hôte et la baie sont sur le même sous-réseau, puis il y aura 4 chemins d’accès disponibles. C’était le cas dans cette procédure. Cependant, si chaque interface réseau sur l’interface de l’hôte et la baie est sur un autre sous-réseau IP (et non routables), puis seulement 2 chemins sera disponibles.

## <a name="step-1-install-mpio-on-the-windows-server-host"></a>Étape 1 : Installer MPIO sur l’hôte Windows Server

MPIO est une fonction facultative de Windows Server et n’est pas installé par défaut. Il doit être installé en tant que fonction via le Gestionnaire de serveur. Pour installer cette fonctionnalité sur votre hôte Windows Server, procédez comme suit.

[AZURE.INCLUDE [storsimple-install-mpio-windows-server-host](../../includes/storsimple-install-mpio-windows-server-host.md)]


## <a name="step-2-configure-mpio-for-storsimple-volumes"></a>Étape 2 : Configuration de MPIO pour les volumes de StorSimple

MPIO doit être configurée pour identifier les volumes StorSimple. Pour configurer le MPIO pour reconnaître les volumes StorSimple, effectuez les opérations suivantes.

[AZURE.INCLUDE [storsimple-configure-mpio-volumes](../../includes/storsimple-configure-mpio-volumes.md)]

## <a name="step-3-mount-storsimple-volumes-on-the-host"></a>Étape 3 : Les volumes StorSimple de montage sur l’hôte

Après que MPIO est configuré sur Windows Server, ou des volumes créés sur le tableau de StorSimple peuvent être montés et peuvent ainsi profiter de MPIO pour assurer la redondance. Pour monter un volume, procédez comme suit.

#### <a name="to-mount-volumes-on-the-host"></a>Pour monter les volumes sur l’hôte

1. Ouvrez la fenêtre **Propriétés de l’initiateur iSCSI** sur l’hôte Windows Server. Cliquez sur **le Gestionnaire de serveur > tableau de bord > Outils > initiateur iSCSI**.
2. Dans la boîte de dialogue **Propriétés de l’initiateur iSCSI** , cliquez sur l’onglet Détection, puis cliquez sur **Découvrir le portail cible**.
3. Dans la boîte de dialogue de **Découvrir un portail cible** , effectuez le des opérations suivantes :
    
    - Entrez l’adresse IP de la première interface réseau activés sur votre tableau virtuel StorSimple. Par défaut, il s’agit **d’Ethernet**. 
    - Cliquez sur **OK** pour revenir à la boîte de dialogue **Propriétés de l’initiateur iSCSI** .

    >[AZURE.IMPORTANT] **Si vous utilisez un réseau privé pour les connexions iSCSI, entrez l’adresse IP du port de données est connecté au réseau privé.**

4. Répétez les étapes 2 et 3 pour une autre interface de réseau (par exemple, Ethernet 2) sur votre baie. 

5. Dans la boîte de dialogue **Propriétés de l’initiateur iSCSI** , sélectionnez l’onglet **Targets** . Pour votre tableau virtuel, vous devez voir chaque surface de volume en tant que cible sous **Cibles découvertes**. Dans ce cas, trois cibles (correspondant à trois volumes) seraient découvertes.

    ![mpio1](./media/storsimple-ova-configure-mpio-windows-server/mpio1.png)

6. Cliquez sur **connexion** pour établir une session iSCSI avec votre tableau StorSimple. Une boîte de dialogue **se connecter à la cible** s’affiche. Activez la case à cocher **Activer les chemins d’accès multiples** . Cliquez sur **Avancé**.

    ![mpio2](./media/storsimple-ova-configure-mpio-windows-server/mpio2.png)

8. Dans la boîte de dialogue **Paramètres avancés** , effectuez le des opérations suivantes :                                       
    -    Dans la liste déroulante **Local Adapter** , sélectionnez **Microsoft iSCSI Initiator**.
    -    Dans la liste déroulante **Initiator IP** , sélectionnez l’adresse IP de l’hôte.
    -    Dans la liste déroulante IP de **Portail cible** , sélectionnez l’adresse IP de l’interface du tableau.
    -    Cliquez sur **OK** pour revenir à la boîte de dialogue **Propriétés de l’initiateur iSCSI** .

    ![mpio3](./media/storsimple-ova-configure-mpio-windows-server/mpio3.png)

9. Cliquez sur **Propriétés**. 

    ![mpio4](./media/storsimple-ova-configure-mpio-windows-server/mpio4.png)
10. Dans la boîte de dialogue **Propriétés** , cliquez sur **Ajout d’une Session**.

    ![mpio5](./media/storsimple-ova-configure-mpio-windows-server/mpio5.png)

10. Dans la boîte de dialogue **se connecter à la cible** , sélectionnez la case à cocher **Activer les chemins d’accès multiples** . Cliquez sur **Avancé**.
11. Dans la boîte de dialogue **Paramètres avancés** :                                        
    -  Dans la liste déroulante **Local adapter** , sélectionnez Microsoft iSCSI Initiator.
    -  Dans la liste déroulante **Initiator IP** , sélectionnez l’adresse IP correspondant à l’hôte. Dans ce cas, vous vous connectez deux interfaces réseau sur le tableau à une seule interface réseau sur l’hôte. Par conséquent, cette interface est la même que celle fournie pour la première session.
    -  Dans la liste déroulante **Adresse IP de portail cible** , sélectionnez l’adresse IP de la seconde interface de données activée sur la baie.
    -  Cliquez sur **OK** pour revenir à la boîte de dialogue Propriétés de l’initiateur iSCSI. Vous avez ajouté une deuxième session à la cible.

        ![mpio11](./media/storsimple-ova-configure-mpio-windows-server/mpio11.png)

    - Après avoir ajouté les sessions de votre choix (chemins), dans la boîte de dialogue **Propriétés de l’initiateur iSCSI** , sélectionnez la cible et cliquez sur **Propriétés**. Sous l’onglet Sessions de la boîte de dialogue **Propriétés** , notez la session quatre identificateurs correspondant pour les permutations de chemin d’accès possible. Pour annuler une session, activez la case à cocher en regard d’un identificateur de session, puis cliquez sur **Déconnecter**.
 
    - Pour afficher les volumes présentés au sein de sessions, sélectionnez l’onglet **périphériques** . Pour configurer la stratégie MPIO pour un périphérique sélectionné, cliquez sur **MPIO**. Le **
    -  Détails** boîte de dialogue s’affiche. Sur le **MPIO** onglet, vous pouvez sélectionner le **stratégie d’équilibrage de charge** paramètres. Vous pouvez également afficher le **actif** ou **veille ** type de chemin d’accès.

10. Répétez les étapes 8 à 11 pour ajouter d’autres sessions (chemins d’accès) à la cible. Avec deux interfaces sur l’hôte et deux sur le tableau virtuel, vous pouvez ajouter un total de quatre sessions pour chaque cible. 

    ![mpio14](./media/storsimple-ova-configure-mpio-windows-server/mpio14.png)

11. Vous devez répéter ces étapes pour chaque volume (surfaces comme cible).

    ![mpio15](./media/storsimple-ova-configure-mpio-windows-server/mpio15.png)

12. Ouvrez **Gestion de l’ordinateur** , accédez à **Server Manager > tableau de bord > Gestion de l’ordinateur**. Dans le volet gauche, cliquez sur **stockage > Gestion des disques**. L’ou les volumes créé sur le tableau virtuel StorSimple qui sont visibles par cet hôte apparaissent sous **Gestion des disques** comme disques de nouveau.

13. Initialiser le disque et créer un nouveau volume. Pendant le processus de formatage, sélectionnez une taille d’unité d’allocation (Australie) de 64 Ko. Répétez le processus pour tous les volumes disponibles.

    ![Gestion des disques](./media/storsimple-ova-configure-mpio-windows-server/mpio20.png)

14. Sous **Gestion des disques**, cliquez sur le **disque** et sélectionnez **Propriétés**.

15. Dans la boîte de dialogue **Propriétés du périphérique disque Multi-Path** , cliquez sur l’onglet **MPIO** .

    ![Propriétés du disque](./media/storsimple-ova-configure-mpio-windows-server/mpio21.png)

16. Dans la section **Nom du module spécifique au périphérique** , cliquez sur **Détails** et vérifiez que les paramètres sont les paramètres par défaut. Les paramètres par défaut sont les suivants :

    - Chemin d’accès de vérifier la période = 30
    - Nombre de tentatives = 3
    - AOP supprimer période = 20
    - Intervalle avant nouvelle tentative = 1
    - Vérification du chemin d’accès activé = désactivé.

    >[AZURE.NOTE] **Ne modifiez pas les paramètres par défaut.**


## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur [le service de gestionnaire de StorSimple pour administrer votre tableau virtuel StorSimple](storsimple-ova-manager-service-administration.md).
 
