<properties
    pageTitle="Résolution des problèmes de l’Extension de panneau d’accès pour Internet Explorer | Microsoft Azure"
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

#<a name="troubleshooting-the-access-panel-extension-for-internet-explorer"></a>Résolution des problèmes de l’Extension de panneau d’accès pour Internet Explorer

Cet article vous aidera à résoudre les problèmes suivants :

- Vous ne parvenez pas à accéder à vos applications via le portail de mes applications lors de l’utilisation d’Internet Explorer.
- Vous voyez le message « Installer le logiciel » même si vous avez déjà installé le logiciel.

Si vous êtes un administrateur, voir aussi : [le déploiement de l’Extension du Panneau de configuration de l’accès pour Internet Explorer à l’aide de la stratégie de groupe](active-directory-saas-ie-group-policy.md)

##<a name="run-the-diagnostic-tool"></a>Exécutez l’outil de Diagnostic

Vous pouvez diagnostiquer les problèmes d’installation avec l’Extension de panneau d’accès en téléchargeant et en exécutant l’outil de diagnostic du panneau d’accès :

1. [Cliquez ici pour télécharger l’outil de diagnostic.](https://account.activedirectory.windowsazure.com/applications/AccessPanelExtensionDiagnosticTool/AccessPanelExtensionDiagnosticTool.zip)

2. Ouvrez le fichier, cliquez sur **Extraire tout** .

    ![Appuyez sur Extraire tous les](./media/active-directory-saas-ie-troubleshooting/extract1.png)

3. Appuyez sur le bouton **Extraire** pour continuer.

    ![Appuyez sur la touche extrait](./media/active-directory-saas-ie-troubleshooting/extract2.png)

4. Pour exécuter l’outil, cliquez sur le fichier nommé **AccessPanelExtensionDiagnosticTool**, puis sélectionnez **Ouvrir avec > Microsoft Windows Based Script Host**.

    ![Ouvrir avec > base de Microsoft Windows Script Host](./media/active-directory-saas-ie-troubleshooting/open_tool.png)

5. Vous verrez ensuite la fenêtre diagnostic suivante, qui décrit ce qui peut être votre installation.

    ![Un exemple de la fenêtre de diagnostic](./media/active-directory-saas-ie-troubleshooting/tool_preview.png)

6. Cliquez sur «**Oui**» pour autoriser le programme à résoudre les problèmes qui ont été trouvés.

7. Pour enregistrer ces modifications, fermez toutes les fenêtres Internet Explorer et rouvrez Internet Explorer.<br />Si vous ne pouvez toujours pas accéder à vos applications, essayez les étapes ci-dessous.

##<a name="check-that-the-access-panel-extension-is-enabled"></a>Vérifiez que l’Extension de panneau d’accès est activée.

Pour vérifier que l’Extension de panneau d’accès est activée dans Internet Explorer :

1. Dans Internet Explorer, cliquez sur l' **icône ENGRENAGE** sur le coin supérieur droit de la fenêtre. Puis sélectionnez **options Internet**.<br />(Dans les versions antérieures d’Internet Explorer, vous pouvez trouver sous **Outils > options Internet**.

    ![Accédez à outils > Options Internet](./media/active-directory-saas-ie-troubleshooting/internetoptions.png)

2. Cliquez sur l’onglet **programmes** , puis cliquez sur le bouton **Gérer les modules complémentaires** .

    ![Cliquez sur Gérer les modules complémentaires](./media/active-directory-saas-ie-troubleshooting/internetoptions_programs.png)

3. Dans cette boîte de dialogue, sélectionnez **l’Extension de panneau de configuration de l’accès** et puis cliquez sur le bouton **Activer** .

    ![Cliquez sur Activer](./media/active-directory-saas-ie-troubleshooting/enableaddon.png)

4. Pour enregistrer ces modifications, fermez toutes les fenêtres Internet Explorer et rouvrez Internet Explorer.

##<a name="enable-extensions-for-inprivate-browsing"></a>Activer les Extensions de navigation InPrivate

Si vous utilisez le mode navigation InPrivate :

1. Dans Internet Explorer, cliquez sur l' **icône ENGRENAGE** sur le coin supérieur droit de la fenêtre. Puis sélectionnez **options Internet**.<br />(Dans les versions antérieures d’Internet Explorer, vous pouvez trouver sous **Outils > options Internet**.

    ![Un exemple de la fenêtre de diagnostic](./media/active-directory-saas-ie-troubleshooting/inprivateoptions.png)

2. Cliquez sur l’onglet **confidentialité** , puis et **désactivez** la case à cocher **désactiver les barres d’outils et extensions au démarrage de la navigation InPrivate**</p>

    ![Désactivez les extensions et les barres d’outils de la désactiver au démarrage de la navigation InPrivate](./media/active-directory-saas-ie-troubleshooting/enabletoolbars.png)

3. Pour enregistrer ces modifications, fermez toutes les fenêtres Internet Explorer et rouvrez Internet Explorer.

##<a name="uninstall-the-access-panel-extension"></a>Désinstaller l’Extension de panneau d’accès

Pour désinstaller l’extension du panneau d’accès de votre ordinateur :

1. Sur votre clavier, appuyez sur la **touche Windows** pour ouvrir le menu Démarrer. Lorsque le menu est ouvert, vous pouvez taper quoi que ce soit pour effectuer une recherche. Tapez « Panneau de configuration » et ouvrez le **Panneau de configuration** lorsqu’il apparaît dans les résultats de recherche.

    ![Recherchez le panneau de configuration](./media/active-directory-saas-ie-troubleshooting/search_sm.png)

2. Dans le coin supérieur droit du Panneau de configuration, modifiez l’option **d’affichage par** de **grandes icônes**. Ensuite, recherchez et cliquez sur le bouton de **programmes et fonctionnalités** .

    ![Chang l’affichage pour afficher de grandes icônes](./media/active-directory-saas-ie-troubleshooting/control_panel.png)

3. Dans la liste, sélectionnez **l’Extension de panneau de configuration de l’accès**, puis cliquez sur le bouton **désinstaller** .

    ![Cliquez sur Désinstaller](./media/active-directory-saas-ie-troubleshooting/uninstall.png)

4. Vous pouvez ensuite essayer d’installer l’extension pour voir si le problème a été résolu.

Si vous rencontrez des problèmes de désinstallation de l’extension, vous pouvez également le supprimer à l’aide de l’outil [Microsoft Fix It](https://go.microsoft.com/?linkid=9779673) .

## <a name="related-articles"></a>Articles connexes

- [Index des articles pour la gestion d’Application dans Azure Active Directory](active-directory-apps-index.md)
- [Accès aux applications et à l’ouverture de session unique avec Active Directory de Azure](active-directory-appssoaccess-whatis.md)
- [Le déploiement de l’Extension de panneau d’accès pour Internet Explorer à l’aide de la stratégie de groupe](active-directory-saas-ie-group-policy.md)
