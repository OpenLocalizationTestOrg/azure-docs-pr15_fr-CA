<properties 
   pageTitle="Configuration de MPIO pour votre périphérique StorSimple | Microsoft Azure"
   description="Décrit comment faire pour configurer le proxy (Multipath i / o) de votre périphérique StorSimple connecté à un hôte exécutant Windows Server 2012 R2."
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
   ms.date="08/17/2016"
   ms.author="alkohli" />

# <a name="configure-multipath-io-for-your-storsimple-device"></a>Configurer MPIO pour votre périphérique StorSimple

Microsoft a créé le support pour la fonctionnalité d’e/s à chemins multiples (MPIO) dans Windows Server configurations SAN aide build hautement disponibles et à tolérance de pannes. MPIO utilise des composants redondants de chemin d’accès physique, adaptateurs, câbles et commutateurs, pour créer des chemins d’accès logiques entre le serveur et le périphérique de stockage. S’il existe une défaillance d’un composant, à l’origine d’un chemin d’accès logique à échouer, logique de gestion multivoie utilise un autre chemin d’e/s afin que les applications peuvent toujours accéder à leurs données. De plus selon votre configuration, MPIO peut également améliorer les performances par l’équilibre la charge sur tous les chemins d’accès. Pour plus d’informations, consultez [vue d’ensemble MPIO]des(https://technet.microsoft.com/library/cc725907.aspx "fonctions et vue d’ensemble MPIO").  

Pour la haute disponibilité de votre solution de StorSimple, MPIO doit être configuré sur le périphérique StorSimple. MPIO est installé sur vos serveurs hôtes Windows Server 2012 R2, les serveurs peuvent tolérer puis un lien, un réseau ou une défaillance de l’interface. 

MPIO est une fonction facultative de Windows Server et n’est pas installé par défaut. Il doit être installé en tant que fonction via le Gestionnaire de serveur. Cette rubrique décrit les étapes que vous devez suivre pour installer et utiliser la fonctionnalité MPIO sur un hôte Windows Server 2012 R2 en cours d’exécution et connecté à un périphérique physique StorSimple.

>[AZURE.NOTE] **Cette procédure est applicable pour les séries StorSimple 8000 uniquement. MPIO n’est actuellement pas pris en charge sur un périphérique virtuel StorSimple.**

Vous devez suivre ces étapes pour configurer MPIO sur votre périphérique de StorSimple :

- Étape 1 : Installer MPIO sur l’hôte Windows Server

- Étape 2 : Configuration de MPIO pour les volumes de StorSimple

- Étape 3 : Les volumes StorSimple de montage sur l’hôte

- Étape 4 : Configuration de MPIO pour une disponibilité élevée et l’équilibrage de charge

Chacune des étapes ci-dessus est abordée dans les sections suivantes.

## <a name="step-1-install-mpio-on-the-windows-server-host"></a>Étape 1 : Installer MPIO sur l’hôte Windows Server

Pour installer cette fonctionnalité sur votre hôte Windows Server, procédez comme suit.

#### <a name="to-install-mpio-on-the-host"></a>Pour installer MPIO sur l’hôte

1. Ouvrez le Gestionnaire de serveur sur votre hôte Windows Server. Par défaut, le Gestionnaire de serveur démarre lorsqu’un membre du groupe Administrateurs ouvre une session sur un ordinateur qui exécute Windows Server 2012 R2 ou Windows Server 2012. Si le Gestionnaire de serveur n’est pas déjà ouvert, cliquez sur **Démarrer > Server Manager**.
![Gestionnaire de serveur](./media/storsimple-configure-mpio-windows-server/IC740997.png)
2. Cliquez sur **le Gestionnaire de serveur > tableau de bord > ajouter des rôles et des fonctionnalités**. Cette opération démarre l’Assistant **Ajout de rôles et de fonctionnalités** .
![Ajouter des rôles et fonctionnalités Assistant 1](./media/storsimple-configure-mpio-windows-server/IC740998.png)
3. Dans l’Assistant **Ajout de rôles et de fonctionnalités** , effectuez les opérations suivantes :

    - Sur la page **avant de commencer** , cliquez sur **suivant**.
    - Dans la page **Sélectionnez le type d’installation** , acceptez le paramètre par défaut d’installation **basée sur le rôle ou fonctionnalité** . Cliquez sur **suivant**. ![Ajouter des rôles et fonctionnalités Assistant 2](./media/storsimple-configure-mpio-windows-server/IC740999.png)
    - Dans la page **Sélectionner le serveur de destination** , cliquez sur **Sélectionnez un serveur à partir du pool du serveur**. Votre serveur hôte doit être découverte automatiquement. Cliquez sur **suivant**.
    - Dans la page **Sélectionnez des rôles de serveur** , cliquez sur **suivant**.
    - Dans la page **Sélectionner les fonctionnalités** , sélectionnez **Multipath i/o**et cliquez sur **suivant**. ![Ajouter des rôles et fonctionnalités Assistant 5](./media/storsimple-configure-mpio-windows-server/IC741000.png)
    - Dans la page de **sélections pour confirmer l’installation** , confirmer la sélection, puis sélectionnez **redémarrer le serveur de destination automatiquement si nécessaire**, comme illustré ci-dessous. Cliquez sur **installer**. ![Ajouter des rôles et fonctionnalités Assistant 8](./media/storsimple-configure-mpio-windows-server/IC741001.png)
    - Vous serez averti lorsque l’installation est terminée. Cliquez sur **Fermer** pour fermer l’Assistant. ![Ajouter des rôles et fonctionnalités Assistant 9](./media/storsimple-configure-mpio-windows-server/IC741002.png)

## <a name="step-2-configure-mpio-for-storsimple-volumes"></a>Étape 2 : Configuration de MPIO pour les volumes de StorSimple

MPIO doit être configurée pour identifier les volumes StorSimple. Pour configurer le MPIO pour reconnaître les volumes StorSimple, effectuez les opérations suivantes.

#### <a name="to-configure-mpio-for-storsimple-volumes"></a>Configuration de MPIO pour les volumes de StorSimple

1. Ouvrir la **configuration de MPIO**. Cliquez sur **le Gestionnaire de serveur > tableau de bord > Outils > MPIO**.

2. Dans la boîte de dialogue **Propriétés de MPIO** , sélectionnez l’onglet **Découvrir plusieurs chemins** .

3. Sélectionnez **Ajouter une prise en charge des périphériques iSCSI**, puis cliquez sur **Ajouter**.  
![Propriétés de MPIO découvrir plusieurs chemins](./media/storsimple-configure-mpio-windows-server/IC741003.png)

4. Redémarrez le serveur lorsque vous y êtes invité.
5. Dans la boîte de dialogue **Propriétés de MPIO** , cliquez sur l’onglet **Périphériques de MPIO** . Cliquez sur **Ajouter**.
    </br>![Périphériques MPIO propriétés MPIO](./media/storsimple-configure-mpio-windows-server/IC741004.png)
6. Dans la boîte de dialogue **Ajouter la prise en charge MPIO** , sous **ID de matériel du périphérique**, entrez votre numéro de série du périphérique. Vous pouvez obtenir le numéro de série du périphérique en accédant à votre service de gestionnaire de StorSimple et en accédant à **périphériques > tableau de bord**. Le numéro de série du périphérique s’affiche dans la volet **d’Aperçu rapide** du tableau de bord périphérique de droite.
    </br>![Ajouter la prise en charge MPIO](./media/storsimple-configure-mpio-windows-server/IC741005.png)
7. Redémarrez le serveur lorsque vous y êtes invité.

## <a name="step-3-mount-storsimple-volumes-on-the-host"></a>Étape 3 : Les volumes StorSimple de montage sur l’hôte

Après que MPIO est configuré sur Windows Server, ou des volumes créés sur le périphérique StorSimple peuvent être montés et peuvent ensuite profiter de MPIO pour assurer la redondance. Pour monter un volume, procédez comme suit.

#### <a name="to-mount-volumes-on-the-host"></a>Pour monter les volumes sur l’hôte

1. Ouvrez la fenêtre **Propriétés de l’initiateur iSCSI** sur l’hôte Windows Server. Cliquez sur **le Gestionnaire de serveur > tableau de bord > Outils > initiateur iSCSI**.
2. Dans la boîte de dialogue **Propriétés de l’initiateur iSCSI** , cliquez sur l’onglet Détection, puis cliquez sur **Découvrir le portail cible**.
3. Dans la boîte de dialogue de **Découvrir un portail cible** , effectuez le des opérations suivantes :
    
    - Entrez l’adresse IP du port de données de votre périphérique StorSimple (par exemple, entrer 0).
    - Cliquez sur **OK** pour revenir à la boîte de dialogue **Propriétés de l’initiateur iSCSI** .

    >[AZURE.IMPORTANT] **Si vous utilisez un réseau privé pour les connexions iSCSI, entrez l’adresse IP du port de données est connecté au réseau privé.**

4. Répétez les étapes 2 et 3 pour une autre interface de réseau (par exemple, données 1) sur votre périphérique. Gardez à l’esprit que ces interfaces doivent être activés pour iSCSI. Pour en savoir plus à ce sujet, voir [Modifier les interfaces réseau](storsimple-modify-device-config.md#modify-network-interfaces).
5. Dans la boîte de dialogue **Propriétés de l’initiateur iSCSI** , sélectionnez l’onglet **Targets** . Vous devez voir la cible de périphérique StorSimple IQN sous **Cibles découvertes**.
 ![iSCSI Initiator propriétés cibles onglet](./media/storsimple-configure-mpio-windows-server/IC741007.png)
6. Cliquez sur **connexion** pour établir une session iSCSI avec votre périphérique StorSimple. Une boîte de dialogue **se connecter à la cible** s’affiche.

7. Dans la boîte de dialogue **se connecter à la cible** , sélectionnez la case à cocher **Activer les chemins d’accès multiples** . Cliquez sur **Avancé**.

8. Dans la boîte de dialogue **Paramètres avancés** , effectuez le des opérations suivantes :                                       
    -    Dans la liste déroulante **Local Adapter** , sélectionnez **Microsoft iSCSI Initiator**.
    -    Dans la liste déroulante **Initiator IP** , sélectionnez l’adresse IP de l’hôte.
    -    Dans la liste déroulante IP de **Portail cible** , sélectionnez l’adresse IP de l’interface de périphérique.
    -    Cliquez sur **OK** pour revenir à la boîte de dialogue **Propriétés de l’initiateur iSCSI** .

9. Cliquez sur **Propriétés**. Dans la boîte de dialogue **Propriétés** , cliquez sur **Ajout d’une Session**.
10. Dans la boîte de dialogue **se connecter à la cible** , sélectionnez la case à cocher **Activer les chemins d’accès multiples** . Cliquez sur **Avancé**.
11. Dans la boîte de dialogue **Paramètres avancés** :                                        
    -  Dans la liste déroulante **Local adapter** , sélectionnez Microsoft iSCSI Initiator.
    -  Dans la liste déroulante **Initiator IP** , sélectionnez l’adresse IP correspondant à l’hôte. Dans ce cas, vous vous connectez deux interfaces réseau sur le périphérique à une seule interface réseau sur l’hôte. Par conséquent, cette interface est la même que celle fournie pour la première session.
    -  Dans la liste déroulante **Adresse IP de portail cible** , sélectionnez l’adresse IP de la seconde interface de données activée sur le périphérique.
    -  Cliquez sur **OK** pour revenir à la boîte de dialogue Propriétés de l’initiateur iSCSI. Vous avez ajouté une deuxième session à la cible.

12. Ouvrez **Gestion de l’ordinateur** , accédez à **Server Manager > tableau de bord > Gestion de l’ordinateur**. Dans le volet gauche, cliquez sur **stockage > Gestion des disques**. L’ou les volumes créé sur le périphérique StorSimple qui sont visibles par cet hôte apparaissent sous **Gestion des disques** comme disques de nouveau.

13. Initialiser le disque et créer un nouveau volume. Pendant le processus de formatage, sélectionnez une taille de bloc de 64 Ko.
![Gestion des disques](./media/storsimple-configure-mpio-windows-server/IC741008.png)
14. Sous **Gestion des disques**, cliquez sur le **disque** et sélectionnez **Propriétés**.
15. Dans le modèle de StorSimple ### boîte de dialogue **Propriétés de périphérique de disque de chemins d’accès multiples** , cliquez sur l’onglet **MPIO** .
![DeviceProp de disque de chemins d’accès multiples StorSimple 8100.](./media/storsimple-configure-mpio-windows-server/IC741009.png)

16. Dans la section **Nom du module spécifique au périphérique** , cliquez sur **Détails** et vérifiez que les paramètres sont les paramètres par défaut. Les paramètres par défaut sont les suivants :

    - Chemin d’accès de vérifier la période = 30
    - Nombre de tentatives = 3
    - AOP supprimer période = 20
    - Intervalle avant nouvelle tentative = 1
    - Vérification du chemin d’accès activé = désactivé.


>[AZURE.NOTE] **Ne modifiez pas les paramètres par défaut.**

## <a name="step-4-configure-mpio-for-high-availability-and-load-balancing"></a>Étape 4 : Configuration de MPIO pour une disponibilité élevée et l’équilibrage de charge

Pour les chemins d’accès multiples basés sur haute disponibilité et l’équilibrage de charge, plusieurs sessions doivent être ajoutées manuellement pour déclarer les différents chemins disponibles. Par exemple, si l’hôte a deux interfaces connectées au réseau SAN et le périphérique présente deux interfaces connectées au SAN, puis vous avez besoin de quatre sessions configurées avec les permutations de chemin d’accès approprié (seulement deux sessions seront requis si chaque interface de données et l’interface de l’hôte se trouvant sur un sous-réseau IP différent et ne sont pas routable).

>[AZURE.IMPORTANT] **Nous vous recommandons de ne pas mélanger 1 Gigabit Ethernet et les interfaces de réseau 10 Gigabit Ethernet. Si vous utilisez deux interfaces réseau, les deux interfaces doivent être du même type.**

La procédure suivante décrit comment ajouter des sessions lorsqu’un StorSimple avec deux interfaces réseau est connectée à un hôte avec deux interfaces réseau.

### <a name="to-configure-mpio-for-high-availability-and-load-balancing"></a>Configuration de MPIO pour une disponibilité élevée et l’équilibrage de charge

1. Exécuter une découverte de la cible : dans la boîte de dialogue **Propriétés de l’initiateur iSCSI** , sous l’onglet **découverte** , cliquez sur **Découvrir le portail**.
2. Dans la boîte de dialogue **se connecter à la cible** , entrez l’adresse IP d’une des interfaces réseau périphérique.
3. Cliquez sur **OK** pour revenir à la boîte de dialogue **Propriétés de l’initiateur iSCSI** .

4. Dans la boîte de dialogue **Propriétés de l’initiateur iSCSI** , sélectionnez l’onglet **Targets** , sélectionnez la cible de découverte et puis cliquez sur **se connecter**. La boîte de dialogue **se connecter à la cible** s’affiche.

5. Dans la boîte de dialogue **se connecter à la cible** :
    
    - Laissez la cible sélectionnée par défaut définissant pour **Ajouter cette connexion** à la liste des cibles favorites. Cela rendra le périphérique tente automatiquement de relancer la connexion de chaque redémarrage de cet ordinateur.
    - Activez la case à cocher **Activer les chemins d’accès multiples** .
    - Cliquez sur **Avancé**.

6. Dans la boîte de dialogue **Paramètres avancés** :
    - Dans la liste déroulante **Local Adapter** , sélectionnez **Microsoft iSCSI Initiator**.
    - Dans la liste déroulante **Initiator IP** , sélectionnez l’adresse IP de l’hôte.
    - Dans la liste déroulante **Adresse IP de portail cible** , sélectionnez l’adresse IP de l’interface de données activée sur le périphérique.
    - Cliquez sur **OK** pour revenir à la boîte de dialogue Propriétés de l’initiateur iSCSI.

7. Cliquez sur **Propriétés**et dans la boîte de dialogue **Propriétés** , cliquez sur **Ajout d’une Session**.

8. Dans la boîte de dialogue **se connecter à la cible** , activez la case à cocher **Activer les chemins d’accès multiples** , puis cliquez sur **Avancé**.

9. Dans la boîte de dialogue **Paramètres avancés** :
    1. Dans la liste déroulante **Local adapter** , sélectionnez **Microsoft iSCSI Initiator**.
    2. Dans la liste déroulante **Initiator IP** , sélectionnez l’adresse IP correspondant à la seconde interface sur l’hôte.
    3. Dans la liste déroulante **Adresse IP de portail cible** , sélectionnez l’adresse IP de la seconde interface de données activée sur le périphérique.
    4. Cliquez sur **OK** pour revenir à la boîte de dialogue **Propriétés de l’initiateur iSCSI** . Vous avez maintenant ajouté une deuxième session à la cible.

10. Répétez les étapes 8 à 10 pour ajouter d’autres sessions (chemins d’accès) à la cible. Avec deux interfaces sur l’hôte et deux sur le périphérique, vous pouvez ajouter un total de quatre sessions.

11. Après avoir ajouté les sessions de votre choix (chemins), dans la boîte de dialogue **Propriétés de l’initiateur iSCSI** , sélectionnez la cible et cliquez sur **Propriétés**. Sous l’onglet Sessions de la boîte de dialogue **Propriétés** , notez la session quatre identificateurs correspondant pour les permutations de chemin d’accès possible. Pour annuler une session, activez la case à cocher en regard d’un identificateur de session, puis cliquez sur **Déconnecter**.

12. Pour afficher les volumes présentés au sein de sessions, sélectionnez l’onglet **périphériques** . Pour configurer la stratégie MPIO pour un périphérique sélectionné, cliquez sur **MPIO**. La boîte de dialogue **Détails du périphérique** s’affiche. Sous l’onglet **MPIO** , vous pouvez sélectionner les paramètres appropriés de la **Stratégie d’équilibrage de charge** . Vous pouvez également afficher le type de chemin d’accès **Active** ou **mise en veille** .

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur [le service de gestionnaire de StorSimple pour modifier la configuration de votre périphérique StorSimple](storsimple-modify-device-config.md).
 
