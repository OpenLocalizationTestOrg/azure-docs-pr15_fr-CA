<properties
    pageTitle="Azure AD Connect : Route en utilisant les paramètres express | Microsoft Azure"
    description="Découvrez comment télécharger, installer et exécuter l’Assistant Installation pour Azure Connect d’Active Directory."
    services="active-directory"
    documentationCenter=""
    authors="andkjell"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/13/2016"
    ms.author="billmath"/>

# <a name="getting-started-with-azure-ad-connect-using-express-settings"></a>Mise en route avec Azure AD se connecter en utilisant les paramètres express
De connexion AD Azure **Express des paramètres** est utilisé lorsque vous avez une topologie de forêt unique et la [synchronisation de mot de passe](../active-directory-aadconnectsync-implement-password-synchronization.md) pour l’authentification. **Les paramètres Express** est l’option par défaut et est utilisé pour le scénario plus couramment déployé. Vous êtes uniquement quelques clics courtes distance pour étendre votre annuaire local dans le nuage.

Avant de commencer l’installation Azure Connect d’Active Directory, assurez-vous que [Télécharger Azure Connect de publicité](http://go.microsoft.com/fwlink/?LinkId=615771) et les étapes préalable complète [Azure AD Connect : matériel et conditions préalables](../active-directory-aadconnect-prerequisites.md).

Si les paramètres express ne correspond pas à votre topologie, consultez [sa documentation](#related-documentation) pour d’autres scénarios.

## <a name="express-installation-of-azure-ad-connect"></a>Installation rapide d’Azure Connect de publicité
Vous pouvez voir ces étapes en action dans la section des [vidéos](#videos) .

1. Ouvrez une session en tant qu’administrateur local pour le serveur que vous souhaitez installer Azure Connect de publicité sur. Vous devez pour cela sur le serveur que vous souhaitez être le serveur de synchronisation.
2. Localisez et double-cliquez sur **AzureADConnect.msi**.
3. Sur l’écran de bienvenue, activez la case J’accepte les termes du contrat de licence, puis cliquez sur **Continuer**.  
4. Dans l’écran Paramètres Express, cliquez sur **utiliser les paramètres express**.  
![Bienvenue dans AD Azure se connecter](./media/active-directory-aadconnect-get-started-express/express.png)
5. Sur la connexion à l’écran d’annonce d’Azure, entrez le nom d’utilisateur et le mot de passe d’un administrateur global pour votre annonce Azure. Cliquez sur **suivant**.  
![Connexion à Active Directory Azure](./media/active-directory-aadconnect-get-started-express/connectaad.png) si vous recevez un message d’erreur et que vous avez des problèmes de connectivité, consultez [résoudre les problèmes de connectivité](../active-directory-aadconnect-troubleshoot-connectivity.md).
6. Sur la connexion à l’écran des services AD DS, entrez le nom d’utilisateur et le mot de passe pour un compte administrateur d’entreprise. Vous pouvez saisir la partie domaine au format NetBios ou nom de domaine complet, c'est-à-dire que FABRIKAM\administrator ou fabrikam.com\administrator. Cliquez sur **suivant**.  
![Se connecter aux services AD DS](./media/active-directory-aadconnect-get-started-express/connectad.png)
7. La page [**configuration de connexion AD Azure**](../active-directory-aadconnect-user-signin.md#azure-ad-sign-in-configuration) affiche uniquement si vous n’avez pas terminé de [vérifier vos domaines](../active-directory-add-domain.md) dans les [conditions préalables](../active-directory-aadconnect-prerequisites.md).
![Domaines non vérifiés](./media/active-directory-aadconnect-get-started-express/unverifieddomain.png)  
Si vous voyez cette page, puis passez en revue chaque domaine marquée **N’a pas ajouté** et **N’a pas vérifié**. Vérifiez que ces domaines que vous utilisez ont été vérifiés dans Azure AD. Lorsque vous avez vérifié vos domaines, cliquez sur le symbole d’actualisation.
8. Sur la prêt à configurer l’écran, cliquez sur **installer**.
    - Si vous le souhaitez sur la prêt à configurer la page, vous pouvez désélectionner la case à cocher **Démarrer le processus de synchronisation, dès que la configuration est terminée** . Vous devez désélectionner cette case à cocher si vous souhaitez effectuer une configuration supplémentaire, comme le [filtrage de](../active-directory-aadconnectsync-configure-filtering.md). Si vous désélectionnez cette option, l’Assistant configure la synchronisation mais laisse le planificateur désactivé. Il ne s’exécute pas jusqu'à ce que vous l’activer manuellement en [exécutant de nouveau l’Assistant d’installation](../active-directory-aadconnectsync-installation-wizard.md).
    - Si vous avez Exchange dans Active Directory sur site, vous disposez également d’une option pour activer [**le déploiement hybride d’Exchange**](https://technet.microsoft.com/library/jj200581.aspx). Activez cette option si vous voulez que les boîtes aux lettres Exchange dans le nuage et sur site en même temps.
![Prêt à configurer Azure Connect de publicité](./media/active-directory-aadconnect-get-started-express/readytoconfigure.png)
9. Une fois l’installation terminée, cliquez sur **Quitter**.
10. Une fois l’installation terminée, vous déconnecter et vous reconnecter avant d’utiliser le Gestionnaire de Service de synchronisation ou l’éditeur de règles de synchronisation.

## <a name="videos"></a>Vidéos

Pour une vidéo sur l’utilisation de l’installation express, voir :

>[AZURE.VIDEO azure-active-directory-connect-express-settings]

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez Azure Connect Active Directory installé, vous pouvez [vérifier l’installation et l’attribution de licences](../active-directory-aadconnect-whats-next.md).

En savoir plus sur ces fonctionnalités, qui ont été activées lors de l’installation : [mise à niveau automatique](../active-directory-aadconnect-feature-automatic-upgrade.md), de [pas accidentelle de suppression](../active-directory-aadconnectsync-feature-prevent-accidental-deletes.md)et de [La santé se connecter Azure AD](../active-directory-aadconnect-health-sync.md).

En savoir plus sur ces sujets courants : [Planificateur et le mode de déclenchement de la synchronisation](../active-directory-aadconnectsync-feature-scheduler.md).

Pour en savoir plus sur [l’intégration des identités avec Azure Active Directory local](../active-directory-aadconnect.md).

## <a name="related-documentation"></a>Documentation connexe

Rubrique |  
--------- | ---------
Vue d’ensemble de connexion de publicité Azure | [Intégration des identités sur site à Azure Active Directory](../active-directory-aadconnect.md)
Installation à l’aide de paramètres personnalisés | [Installation personnalisée de Azure Connect de publicité](active-directory-aadconnect-get-started-custom.md)
Mise à niveau à partir de la synchronisation d’annuaire | [Mise à niveau à partir de l’outil de synchronisation AD Azure (DirSync)](active-directory-aadconnect-dirsync-upgrade-get-started.md)
Comptes utilisés pour l’installation | [En savoir plus sur les autorisations et les comptes d’Azure Connect de publicité](active-directory-aadconnect-accounts-permissions.md)
