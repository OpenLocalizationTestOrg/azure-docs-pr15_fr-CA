<properties
    pageTitle="Le déploiement de l’Extension de panneau d’accès pour Internet Explorer à l’aide de la stratégie de groupe | Microsoft Azure"
    description="Comment utiliser Stratégie de groupe pour déployer le module complémentaire d’Internet Explorer pour le portail de mes applications."
    services="active-directory"
    documentationCenter=""
    authors="MarkusVi"
    manager="femila"
    editor=""/>
<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="08/16/2016"
    ms.author="markvi"/>

#<a name="how-to-deploy-the-access-panel-extension-for-internet-explorer-using-group-policy"></a>Le déploiement de l’Extension de panneau d’accès pour Internet Explorer à l’aide de la stratégie de groupe

Ce didacticiel explique comment utiliser la stratégie de groupe pour installer à distance de l’extension du panneau d’accès pour Internet Explorer sur les ordinateurs de vos utilisateurs. Cette extension est nécessaire pour les utilisateurs d’Internet Explorer qui ont besoin de se connecter à des applications qui sont configurées à l’aide de [mot de passe de l’authentification unique](active-directory-appssoaccess-whatis.md#password-based-single-sign-on).

Il est recommandé que les administrateurs automatisent le déploiement de cette extension. Dans le cas contraire, les utilisateurs doivent télécharger et installer l’extension eux-mêmes, qui est sujette à des erreurs des utilisateurs et requiert des autorisations d’administrateur. Ce didacticiel décrit une méthode d’automatisation des déploiements de logiciels à l’aide de stratégie de groupe. [Pour en savoir plus sur la stratégie de groupe.](https://technet.microsoft.com/windowsserver/bb310732.aspx)

L’extension du panneau d’accès est également disponible pour [Chrome](https://go.microsoft.com/fwLink/?LinkID=311859) et [Firefox](https://go.microsoft.com/fwLink/?LinkID=626998), qui ne requièrent des autorisations d’administrateur pour l’installer.

##<a name="prerequisites"></a>Conditions préalables

- Vous avez configuré [Les Services de domaine Active Directory](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx), et que vous avez joint des ordinateurs de vos utilisateurs à votre domaine.
- Vous devez disposer de l’autorisation « Modifier les paramètres » pour modifier des objets de stratégie de groupe (GPO). Par défaut, les membres des groupes de sécurité suivants ont cette autorisation : les administrateurs de domaine, administrateurs de l’entreprise et propriétaires créateurs de la stratégie de groupe. [Pour en savoir plus.](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx)

##<a name="step-1-create-the-distribution-point"></a>Étape 1 : Créer le Point de Distribution

Tout d’abord, vous devez placer le package d’installation sur un emplacement réseau accessible à partir de tous les ordinateurs que vous souhaitez installer à distance de l’extension de. Pour ce faire, procédez comme suit :

1. Ouvrez une session sur le serveur en tant qu’administrateur

2. Dans la fenêtre **Gestionnaire de serveur** , accéder aux **fichiers et aux Services de stockage**.

    ![Ouvrir des fichiers et des Services de stockage](./media/active-directory-saas-ie-group-policy/files-services.png)

3. Accédez à l’onglet **actions** . Puis cliquez sur **tâches** > **Nouveau partage...**

    ![Ouvrir des fichiers et des Services de stockage](./media/active-directory-saas-ie-group-policy/shares.png)

4. Exécutez l' **Assistant Nouveau partage** et définir des autorisations pour s’assurer qu’il est accessible à partir des ordinateurs de vos utilisateurs. [Pour en savoir plus sur les partages.](https://technet.microsoft.com/library/cc753175.aspx)

5. Télécharger le package de Microsoft Windows Installer (fichier .msi) suivant : [Extension.msi de panneau d’accès](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access Panel Extension.msi)

6. Copiez le package d’installation vers l’emplacement souhaité sur le partage.

    ![Copiez le fichier .msi, vous êtes le partage.](./media/active-directory-saas-ie-group-policy/copy-package.png)

8. Vérifiez que vos ordinateurs clients sont en mesure d’accéder le package du programme d’installation à partir du partage. 

##<a name="step-2-create-the-group-policy-object"></a>Étape 2 : Créer l’objet de stratégie de groupe

1. Ouvrez une session sur le serveur qui héberge votre installation des Services de domaine Active Directory (AD DS).

2. Dans le Gestionnaire de serveur, accédez à **Outils** > **Gestion des stratégies de groupe**.

    ![Accédez à outils > Gestion de la stratégie de groupe](./media/active-directory-saas-ie-group-policy/tools-gpm.png)

3. Dans le volet gauche de la fenêtre de **Gestion des stratégies de groupe** , affichage de votre hiérarchie d’unité d’organisation (UO) et déterminer à quelle étendue vous souhaitez appliquer la stratégie de groupe. Par exemple, vous pouvez décider de sélectionner une unité d’organisation petite à déployer pour un petit nombre d’utilisateurs pour le test, ou vous pouvez choisir une unité d’organisation de niveau supérieur à déployer dans toute l’organisation.

    > [AZURE.NOTE] Si vous souhaitez créer ou modifier vos unités d’organisation (UO), passer au serveur gestionnaire et accédez à **Outils** > **Active Directory utilisateurs et ordinateurs**.

4. Une fois que vous avez sélectionné une unité d’organisation, avec le bouton droit dessus et sélectionnez **créer un objet GPO dans ce domaine et le lier ici...**

    ![Créer un nouvel objet de stratégie de groupe](./media/active-directory-saas-ie-group-policy/create-gpo.png)

5. À l’invite du **Nouvel objet GPO** , tapez un nom pour le nouvel objet de stratégie de groupe.

    ![Nom de l’objet stratégie de groupe](./media/active-directory-saas-ie-group-policy/name-gpo.png)

6. Avec le bouton droit sur l’objet de stratégie de groupe que vous venez de créer et sélectionnez **Modifier**.

    ![Modifier l’objet stratégie de groupe](./media/active-directory-saas-ie-group-policy/edit-gpo.png)

##<a name="step-3-assign-the-installation-package"></a>Étape 3 : Affecter le Package d’Installation

1. Déterminez si vous souhaitez déployer l’extension en fonction de la **Configuration de l’ordinateur** ou **Configuration utilisateur**. Lorsque vous utilisez la [configuration de l’ordinateur](https://technet.microsoft.com/library/cc736413%28v=ws.10%29.aspx), l’extension sera installée sur l’ordinateur, le connecter aux utilisateurs. D’autre part, avec la [configuration de l’utilisateur](https://technet.microsoft.com/library/cc781953%28v=ws.10%29.aspx), les utilisateurs auront l’extension installée pour eux que les ordinateurs ils ouvrent une session sur.

2. Dans le volet gauche de la fenêtre de **l’Éditeur de gestion de stratégie de groupe** , accédez à un des chemins de dossier suivant, selon le type de configuration que vous avez choisi :
    - `Computer Configuration/Policies/Software Settings/`
    - `User Configuration/Policies/Software Settings/`

3. Avec le bouton droit sur **installation de logiciel**, puis sélectionnez **Nouveau** > **Package...**

    ![Créer un nouveau package d’installation de logiciel](./media/active-directory-saas-ie-group-policy/new-package.png)

4. Accédez au dossier partagé qui contient le package de programme d’installation à partir de [étape 1 : créer le Point de Distribution](#step-1-create-the-distribution-point), sélectionnez le fichier .msi et cliquez sur **Ouvrir**.

    > [AZURE.IMPORTANT] Si le partage est situé sur le même serveur, vérifiez que vous accédez le fichier .msi via le chemin d’accès réseau, plutôt que le chemin d’accès du fichier local.

    ![Sélectionnez le package d’installation à partir du dossier partagé.](./media/active-directory-saas-ie-group-policy/select-package.png)

5. À l’invite de **Déploiement du logiciel** , sélectionnez **affecté** pour votre méthode de déploiement. Puis cliquez sur **OK**.

    ![Sélectionnez affecté, puis cliquez sur OK.](./media/active-directory-saas-ie-group-policy/deployment-method.png)

L’extension est désormais déployée sur l’unité d’organisation que vous avez sélectionné. [Pour en savoir plus sur l’Installation de logiciels Stratégie de groupe.](https://technet.microsoft.com/library/cc738858%28v=ws.10%29.aspx)

##<a name="step-4-auto-enable-the-extension-for-internet-explorer"></a>Étape 4 : Activation automatique l’Extension pour Internet Explorer 

En plus d’exécuter le programme d’installation, toutes les extensions pour Internet Explorer doivent être explicitement activée avant de pouvoir être utilisé. Suivez les étapes ci-dessous pour activer l’Extension du Panneau de configuration de l’accès à l’aide de la stratégie de groupe :

1. Dans la fenêtre de **l’Éditeur de gestion de stratégie de groupe** , accédez à un des chemins suivants, selon le type de configuration que vous avez choisi dans [étape 3 : affecter le Package d’Installation](#step-3-assign-the-installation-package):
    - `Computer Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`
    - `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`

2. Avec le bouton droit sur la **Liste des modules complémentaires**et sélectionnez **Modifier**.
    ![Modifier la liste des modules complémentaires.](./media/active-directory-saas-ie-group-policy/edit-add-on-list.png)

3. Dans la fenêtre **Liste des modules complémentaires** , sélectionnez **activé**. Puis, dans la section **Options** , cliquez sur **afficher...**.

    ![Cliquez sur Activer, puis cliquez sur Afficher...](./media/active-directory-saas-ie-group-policy/edit-add-on-list-window.png)

4. Dans la fenêtre **Afficher le contenu** , procédez comme suit :

    1. Pour la première colonne (le champ **Nom de la valeur** ), copier et coller le code de classe suivant :`{030E9A3F-7B18-4122-9A60-B87235E4F59E}`

    2. Pour la deuxième colonne (le champ de **valeur** ), tapez la valeur suivante :`1`

    3. Cliquez sur **OK** pour fermer la fenêtre **Afficher le contenu** .

    ![Remplissez les valeurs comme indiqué ci-dessus.](./media/active-directory-saas-ie-group-policy/show-contents.png)

5. Cliquez sur **OK** pour appliquer vos modifications et fermer la fenêtre de la **Liste des modules complémentaires** .

L’extension doit désormais être activée pour les ordinateurs dans l’unité d’organisation sélectionnée. [Pour en savoir plus sur l’utilisation de la stratégie de groupe pour activer ou désactiver les modules complémentaires d’Internet Explorer.](https://technet.microsoft.com/library/dn454941.aspx)

##<a name="step-5-optional-disable-remember-password-prompt"></a>Étape 5 (facultatif) : désactiver l’invite de « Mémoriser le mot de passe »

Lorsque les utilisateurs reconnectez-vous à des sites Web à l’aide de l’Extension de panneau d’accès, Internet Explorer peut afficher le message suivant vous demande « Voulez-vous stocker votre mot de passe ? »

![](./media/active-directory-saas-ie-group-policy/remember-password-prompt.png)

Si vous souhaitez empêcher les utilisateurs d’afficher ce message, puis suivez les étapes ci-dessous pour empêcher l’exécution automatique de mémorisation des mots de passe :

1. Dans la fenêtre de **l’Éditeur de gestion de stratégie de groupe** , consultez le chemin d’accès ci-dessous. Notez que ce paramètre de configuration est uniquement disponible sous **Configuration de l’utilisateur**.
    - `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/`

2. Recherchez le paramètre appelé **Activer la saisie semi-automatique des noms d’utilisateur et mots de passe sur les formulaires**.

    > [AZURE.NOTE] Les versions précédentes de Active Directory peuvent répertorier ce paramètre avec le nom de **ne pas autoriser la saisie semi-automatique à enregistrer des mots de passe**. La configuration de ce paramètre diffère le paramètre décrit dans ce didacticiel.

    ![N’oubliez pas de rechercher ce sous paramètres de l’utilisateur.](./media/active-directory-saas-ie-group-policy/disable-auto-complete.png)

3. Cliquez avec le bouton droit sur le paramètre ci-dessus et sélectionnez **Modifier**.

4. Dans la fenêtre intitulée **Activer la saisie semi-automatique des noms d’utilisateur et mots de passe sur les formulaires**, sélectionnez **désactivé**.

    ![Sélectionnez désactiver](./media/active-directory-saas-ie-group-policy/disable-passwords.png)

5. Cliquez sur **OK** pour appliquer ces modifications et fermer la fenêtre.

Les utilisateurs ne seront plus en mesure de stocker leurs informations d’identification ou l’utilisation de saisie semi-automatique pour accéder à des informations d’identification enregistrées précédemment. Cependant, cette stratégie permet aux utilisateurs de continuer à utiliser la saisie semi-automatique pour les autres types de champs de formulaire, tels que les champs de recherche.

> [AZURE.WARNING] Si cette stratégie est activée, une fois que les utilisateurs ont choisi de stocker certaines informations d’identification, cette stratégie de ne *pas* effacer les informations d’identification qui ont déjà été enregistrées.

##<a name="step-6-testing-the-deployment"></a>Étape 6 : Test du déploiement

Suivez les étapes ci-dessous pour vérifier si le déploiement de l’extension a réussi :

1. Si vous avez déployé à l’aide de la **Configuration de l’ordinateur**, connectez-vous à un ordinateur client qui appartient à l’unité d’organisation que vous avez sélectionné dans [étape 2 : créer l’objet de stratégie de groupe](#step-2-create-the-group-policy-object). Si vous avez déployé à l’aide de la **Configuration de l’utilisateur**, veillez à vous connecter en tant qu’utilisateur appartenant à cette unité d’organisation.

2. Il peut prendre quelques signe mise à jour de la stratégie de groupe devient entièrement de modules avec cet ordinateur. Pour forcer la mise à jour, ouvrez une fenêtre **d’invite de commandes** et exécutez la commande suivante :`gpupdate /force`

3. Vous devez redémarrer l’ordinateur pour que l’installation ait lieu. Démarrage peut prendre beaucoup plus de temps qu’installe habituel lors de l’extension.

4. Après avoir redémarré, ouvrez **Internet Explorer**. Dans le coin supérieur droit de la fenêtre, cliquez sur **Outils** (l’icône représentant un engrenage) et puis sélectionnez **Gérer les modules complémentaires**.

    ![Accédez à outils > Gérer les modules complémentaires](./media/active-directory-saas-ie-group-policy/manage-add-ons.png)

5. Dans la fenêtre **Gérer les modules complémentaires** , vérifiez que l' **Extension du Panneau de configuration accès** a été installé et que son **état** a été défini sur **activé**.

    ![Vérifiez que l’Extension de panneau d’accès est installée et activée.](./media/active-directory-saas-ie-group-policy/verify-install.png)

## <a name="related-articles"></a>Articles connexes

- [Index des articles pour la gestion d’Application dans Azure Active Directory](active-directory-apps-index.md)
- [Accès aux applications et à l’ouverture de session unique avec Active Directory de Azure](active-directory-appssoaccess-whatis.md)
- [Résolution des problèmes de l’Extension de panneau d’accès pour Internet Explorer](active-directory-saas-ie-troubleshooting.md)
