<properties 
   pageTitle="Configurer la sécurité CHAP pour votre périphérique StorSimple | Microsoft Azure"
   description="Décrit comment configurer le CHAP Challenge Handshake Authentication Protocol () sur un périphérique StorSimple."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="TBD"
   ms.date="08/17/2016"
   ms.author="alkohli" />

# <a name="configure-chap-for-your-storsimple-device"></a>Configurer la sécurité CHAP pour votre périphérique StorSimple

Ce didacticiel explique comment configurer la sécurité CHAP pour votre périphérique StorSimple. La procédure détaillée dans cet article s’applique à la série 8000 de StorSimple ainsi que les périphériques StorSimple 1200.

CHAP est l’abréviation de Challenge Handshake Authentication Protocol. Il s’agit d’un schéma d’authentification utilisé par les serveurs pour valider l’identité des clients distants. La vérification est basée sur un mot de passe partagé ou le secret. CHAP peut être unidirectionnelle (unidirectionnelle) ou mutuelle (bidirectionnelle). Authentification CHAP unidirectionnelle est lorsque la cible authentifie l’initiateur. Protocole CHAP mutuelle ou inversée, d’autre part, requiert que la cible authentifier l’initiateur et puis l’initiateur authentifie la cible. Authentification de l’initiateur peut être implémentée sans authentification de la cible. Toutefois, l’authentification de la cible peut être implémentée uniquement si l’authentification de l’initiateur est également implémentée. 

Pour obtenir les meilleurs résultats, nous vous conseillons CHAP pour améliorer la sécurité iSCSI.

>[AZURE.NOTE] Gardez à l’esprit que IPSEC n'est pas actuellement pris en charge sur les périphériques StorSimple.

Les paramètres CHAP sur le périphérique StorSimple peuvent être configurés de la manière suivante :

- Authentification unidirectionnelle ou à sens unique

- Bidirectionnel ou authentification mutuelle ou inversée

Dans chacun de ces cas, le portail pour le périphérique et le logiciel d’initiateur iSCSI server doit être configuré. Les étapes détaillées de cette configuration sont décrites dans ce didacticiel.

## <a name="unidirectional-or-one-way-authentication"></a>Authentification unidirectionnelle ou à sens unique

Dans l’authentification unidirectionnelle, la cible authentifie l’initiateur. Ce type d’authentification que vous devez configurer les paramètres d’initiateur CHAP sur le périphérique StorSimple et le logiciel iSCSI Initiator sur l’hôte. Les procédures détaillées de votre dispositif de StorSimple et d’un hôte Windows sont décrites ci-après.

#### <a name="to-configure-your-device-for-one-way-authentication"></a>Pour configurer votre périphérique pour l’authentification à sens unique

1. Dans le portail Azure classique, dans la page des **périphériques** , cliquez sur l’onglet **configurer** .

    ![CHAP initiateur](./media/storsimple-configure-chap/IC740943.png)

2. Faites défiler vers le bas sur cette page et dans la section **CHAP l’initiateur** :
                                                    
    1. Fournir un nom d’utilisateur de votre initiateur CHAP.

    2. Fournir un mot de passe de votre initiateur CHAP.

         > [AZURE.IMPORTANT] Le nom d’utilisateur CHAP doit contenir moins de 233 caractères. Le mot de passe CHAP doit être comprise entre 12 et 16 caractères. Un nom d’utilisateur ou le mot de passe plus long entraîne un échec d’authentification sur l’hôte Windows.
    
    3. Confirmer le mot de passe.

4. Cliquez sur **Enregistrer**. Un message de confirmation s’affiche. Cliquez sur **OK** pour enregistrer les modifications.

#### <a name="to-configure-one-way-authentication-on-the-windows-host-server"></a>Pour configurer l’authentification à sens unique sur le serveur hôte Windows

1. Sur le serveur hôte Windows, démarrez l’initiateur iSCSI.

2. Dans la fenêtre **Propriétés de l’initiateur iSCSI** , effectuez les opérations suivantes :
                                                    
    1. Cliquez sur l’onglet **détection** .

        ![propriétés de l’initiateur iSCSI](./media/storsimple-configure-chap/IC740944.png)

    2. Cliquez sur **découvrir le portail**.

3. Dans la boîte de dialogue de **Découvrir un portail cible** :
                                                    
    1. Spécifiez l’adresse IP de votre périphérique.

    3. Cliquez sur **Avancé**.

        ![Découverte du portail cible](./media/storsimple-configure-chap/IC740945.png)

4. Dans la boîte de dialogue **Paramètres avancés** :
                                                    
    1. Activez la case à cocher **Activer CHAP session** .

    2. Dans le champ **nom** , indiquez le nom d’utilisateur que vous avez spécifié pour l’initiateur CHAP dans le portail classique.

    3. Dans le champ **Target secret** , fournissez le mot de passe que vous avez spécifié pour l’initiateur CHAP dans le portail classique.

    4. Cliquez sur **OK**.

        ![Paramètres avancés généraux](./media/storsimple-configure-chap/IC740946.png)

5. Sous l’onglet **Targets** de la fenêtre **Propriétés de l’initiateur iSCSI** , l’état du périphérique doit apparaître comme **connecté**. Si vous utilisez un périphérique StorSimple 1200, chaque volume sera monté comme une cible iSCSI comme indiqué ci-dessous. Par conséquent, étapes 3 et 4 devez être répété pour chaque volume.

    ![Volumes montés en tant que cibles distinctes](./media/storsimple-configure-chap/chap4.png)

    > [AZURE.IMPORTANT] Si vous modifiez le nom iSCSI, le nouveau nom servira de nouvelles sessions iSCSI. Nouveaux paramètres ne sont pas utilisés à nouveau pour existants jusqu'à la fermeture de sessions et ouverture de session.

Pour plus d’informations sur la configuration du protocole CHAP sur le serveur hôte Windows, passez à [des considérations supplémentaires](#additional-considerations).


## <a name="bidirectional-or-mutual-authentication"></a>Bidirectionnel ou authentification mutuelle

Dans l’authentification bidirectionnelle, la cible authentifie l’initiateur, puis l’initiateur authentifie la cible. Cela requiert de l’utilisateur de configurer les paramètres d’initiateur CHAP, ainsi que les paramètres CHAP inversées sur le logiciel initiateur iSCSI et de périphérique sur l’hôte. Les procédures suivantes décrivent les étapes pour configurer l’authentification mutuelle sur l’appareil et sur l’hôte Windows.

#### <a name="to-configure-your-device-for-mutual-authentication"></a>Pour configurer votre périphérique pour l’authentification mutuelle

1. Dans le portail Azure classique, dans la page des **périphériques** , cliquez sur l’onglet **configurer** .

    ![CHAP cible](./media/storsimple-configure-chap/IC740948.png)

2. Faites défiler vers le bas sur cette page et dans la section **CHAP Target** :
                                                    
    1. Fournir un **nom d’utilisateur CHAP de contrepasser** pour votre périphérique.

    2. Fournir un **mot de passe inverser CHAP** pour votre périphérique.

    3. Confirmer le mot de passe.

3. Dans la section **CHAP l’initiateur** :
                                                
    1. Fournir un **nom d’utilisateur** pour votre périphérique.

    1. Fournir un **mot de passe** pour votre périphérique.

    3. Confirmer le mot de passe.

4. Cliquez sur **Enregistrer**. Un message de confirmation s’affiche. Cliquez sur **OK** pour enregistrer les modifications.

#### <a name="to-configure-bidirectional-authentication-on-the-windows-host-server"></a>Pour configurer l’authentification bidirectionnelle sur le serveur hôte Windows

1. Sur le serveur hôte Windows, démarrez l’initiateur iSCSI.

2. Dans la fenêtre **Propriétés de l’initiateur iSCSI** , cliquez sur l’onglet **Configuration** .

3. Cliquez sur **CHAP**.

4. Dans la boîte de dialogue **iSCSI Initiator code Secret CHAP mutuel** :
                                                    
    1. Entrez le **Inverser un mot de passe CHAP** que vous avez configurée dans Azure portal classique.

    2. Cliquez sur **OK**.

        ![code secret CHAP mutuel iSCSI initiator](./media/storsimple-configure-chap/IC740949.png)

5. Cliquez sur l’onglet **Targets** .

6. Cliquez sur le bouton **se connecter** . 

7. Dans la boîte de dialogue **Se connecter à la cible** , cliquez sur **Avancé**.

8. Dans la boîte de dialogue **Propriétés avancées** :
                                                    
    1. Activez la case à cocher **Activer CHAP session** .

    2. Dans le champ **nom** , indiquez le nom d’utilisateur que vous avez spécifié pour l’initiateur CHAP dans le portail classique.

    3. Dans le champ **Target secret** , fournissez le mot de passe que vous avez spécifié pour l’initiateur CHAP dans le portail classique.

    4. Activez la case à cocher **effectuer l’authentification mutuelle** .

        ![Authentification mutuelle des paramètres avancés](./media/storsimple-configure-chap/IC740950.png)

    5. Cliquez sur **OK** pour terminer la configuration du protocole CHAP
     
Pour plus d’informations sur la configuration du protocole CHAP sur le serveur hôte Windows, passez à [des considérations supplémentaires](#additional-considerations).

## <a name="additional-considerations"></a>Considérations supplémentaires

La fonctionnalité de **Connexion rapide** ne supporte pas les connexions que vous ont activé le protocole CHAP. Lorsque le protocole CHAP est activé, vérifiez que vous utilisez le bouton de **connexion** qui est disponible sur l’onglet **Targets** pour se connecter à une cible.

![Se connecter à la cible](./media/storsimple-configure-chap/IC740947.png)

Dans la boîte de dialogue **se connecter à la cible** qui s’affiche, sélectionnez la case à cocher **Ajouter cette connexion à la liste des cibles favorites** . Cela garantit que chaque fois que l’ordinateur redémarre, une tentative est effectuée pour restaurer la connexion aux cibles iSCSI du favori.

## <a name="errors-during-configuration"></a>Erreurs lors de la configuration

Si votre configuration CHAP est incorrecte, vous êtes susceptible de voir un message d’erreur **Échec de l’authentification** .

## <a name="verification-of-chap-configuration"></a>Vérification de la configuration du protocole CHAP

Vous pouvez vérifier que CHAP est utilisé par la procédure ci-dessous.

#### <a name="to-verify-your-chap-configuration"></a>Pour vérifier votre configuration CHAP

1. Cliquez sur **cibles favorites**.

2. Sélectionnez la cible pour laquelle vous avez activé l’authentification.

3. Cliquez sur **Détails**.

    ![cibles favorites des propriétés initiateur iSCSI](./media/storsimple-configure-chap/IC740951.png)

4. Dans la boîte de dialogue **Détails de la cible Favorite** , notez l’entrée dans le champ **authentification** . Si la configuration a réussi, vous devriez voir **CHAP**.

    ![Détails de la cible favorite](./media/storsimple-configure-chap/IC740952.png)

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur la [sécurité de StorSimple](storsimple-security.md).

- En savoir plus sur [le service de gestionnaire de StorSimple pour administrer votre périphérique StorSimple](storsimple-manager-service-administration.md).
