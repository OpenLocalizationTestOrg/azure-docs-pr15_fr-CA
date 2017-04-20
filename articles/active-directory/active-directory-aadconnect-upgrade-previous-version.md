<properties
   pageTitle="Connexion de publicité Azure : Mise à niveau à partir d’une version précédente | Microsoft Azure"
   description="Explique les différentes méthodes de mise à niveau vers la dernière version d’Azure Active Directory se connecter, y compris la mise à niveau et migration de swing."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="Identity"
   ms.date="10/12/2016"
   ms.author="billmath"/>

# <a name="azure-ad-connect-upgrade-from-a-previous-version-to-the-latest"></a>Azure AD Connect : Mise à niveau depuis une version antérieure à plus tard
Cette rubrique décrit les différentes méthodes que vous pouvez utiliser pour mettre à niveau votre installation Azure Connect d’Active Directory vers la version la plus récente. Nous vous conseillons de conserver vous-même en cours avec les versions d’Azure Connect d’Active Directory. Les étapes décrites dans la [migration de swing](#swing-migration) sont également utilisés lorsque vous effectuez une configuration importante modification.

Si vous souhaitez mettre à niveau à partir de la synchronisation d’annuaire, reportez-vous à la section [mise à niveau à partir de l’outil de synchronisation AD Azure (DirSync)](./connect/active-directory-aadconnect-dirsync-upgrade-get-started.md) à la place.

Il existe quelques stratégies différentes à niveau Azure Connect d’Active Directory.

Méthode | Description
--- | ---
[Mise à niveau automatique](active-directory-aadconnect-feature-automatic-upgrade.md) | Pour les clients avec une installation rapide, c’est la méthode la plus simple.
[Mise à niveau sur place](#in-place-upgrade) | Si vous avez un seul serveur, mettez à niveau l’installation place sur le même serveur.
[Migration de swing](#swing-migration) | Avec deux serveurs, vous pouvez préparer un des serveurs avec la nouvelle version ou une configuration et modifier le serveur actif lorsque vous êtes prêt.

Pour connaître les autorisations requises, consultez [autorisations requises pour la mise à niveau](./connect/active-directory-aadconnect-accounts-permissions.md#upgrade).

## <a name="in-place-upgrade"></a>Mise à niveau sur place
Une mise à niveau sur place fonctionne pour le déplacement d’Azure AD Sync ou Azure Connect d’Active Directory. Il ne fonctionnera pas pour la synchronisation d’annuaire ou pour une solution avec FIM + Azure le connecteur Active Directory.

Cette méthode est préférable lorsque vous avez un seul serveur et inférieure à environ 100 000 objets. Si des modifications sont apportées aux règles de synchronisation d’out-of-box, une importation complète et une synchronisation complète se produisent après la mise à niveau. Cela garantit que la nouvelle configuration est appliquée à tous les objets existants dans le système. Cette opération peut prendre plusieurs heures selon le nombre d’objets dans la portée du moteur de synchronisation. Le Planificateur de synchronisation delta normale, par défaut, toutes les 30 minutes, est suspendu mais continue de la synchronisation de mot de passe. Vous pouvez envisager d’effectuer la mise à niveau sur place un week-end. S’il n’y a aucune modification à la configuration prête à l’emploi avec la nouvelle version d’Azure Connect de publicité, puis importation/synchronisation delta normal démarre à la place.  
![Mise à niveau sur Place](./media/active-directory-aadconnect-upgrade-previous-version/inplaceupgrade.png)

Si vous avez apporté des modifications aux règles de synchronisation d’out-of-box, il seront ramené à la configuration par défaut sur la mise à niveau. Pour vous assurer que votre configuration est conservée entre les mises à niveau, veillez à ce que les modifications sont apportées, comme décrit dans [les méthodes conseillées pour la modification de la configuration par défaut](active-directory-aadconnectsync-best-practices-changing-default-configuration.md).

## <a name="swing-migration"></a>Migration de swing
Si vous avez un déploiement complexe ou très grand nombre d’objets, il peut être difficile d’effectuer une mise à niveau sur le système. Cela peut prendre pour certains clients plusieurs jours et pendant cette période aucune modification delta n’est traitées. Cette méthode est également utilisée lorsque vous souhaitez apporter des modifications importantes à votre configuration et que vous souhaitez les essayer avant que celles-ci sont déplacées vers le nuage.

La méthode recommandée pour ces scénarios est d’utiliser une migration de swing. Vous avez besoin (au moins) deux serveurs, un actif et un serveur intermédiaire. Le serveur actif (traits pleins bleus dans l’image ci-dessous) est chargé de la charge de production active. Le serveur intermédiaire (lignes en pointillés violets dans l’image ci-dessous) est préparé avec la nouvelle version ou une configuration, et lorsqu’il est prêt, ce serveur devient actif. Le serveur actif précédent, maintenant avec l’ancienne version ou une configuration installé, est effectué le serveur intermédiaire et mis à niveau.

Les deux serveurs peuvent utiliser différentes versions. Par exemple, le serveur actif que vous prévoyez de mettre hors service peut utiliser Azure AD Sync et le nouveau serveur intermédiaire peut utiliser Azure Connect d’AD. Si la migration de swing vous permet de développer une nouvelle configuration, il est judicieux de disposer des mêmes versions sur les deux serveurs.  
![Serveur intermédiaire](./media/active-directory-aadconnect-upgrade-previous-version/stagingserver1.png)

Remarque : Il a été constaté que certains clients préfèrent avoir trois ou quatre serveurs pour ce scénario. Lorsque le serveur de test est mis à niveau, vous n’avez pas d’un serveur de sauvegarde en cas de [reprise après sinistre](active-directory-aadconnectsync-operations.md#disaster-recovery). Avec trois ou quatre serveurs, un ensemble de serveurs principal/secondaire avec la nouvelle version peut être préparé, en s’assurant toujours prêt à prendre un serveur de test.

Cette procédure fonctionne également pour déplacer d’Azure AD Sync ou d’une solution avec FIM + Azure le connecteur Active Directory. Ces étapes ne fonctionnent pas pour la synchronisation d’annuaire, mais la même méthode (également appelée déploiement parallèle) de migration du swing aux étapes de synchronisation d’annuaire, vous pouvez trouver dans la [Mise à niveau Azure Active Directory synchronisation (DirSync)](./connect/active-directory-aadconnect-dirsync-upgrade-get-started.md).

### <a name="swing-migration-steps"></a>Étapes de migration de swing

1. Si vous utilisez Azure Connect de publicité sur les deux serveurs et que vous prévoyez de ne faire une modification de configuration, assurez-vous que votre serveur active et serveur intermédiaire utilisent tous deux la même version. Qui la rendent plus facile de comparer les différences ultérieurement. Si vous mettez à niveau à partir de la synchronisation d’Active Directory Azure, ces serveurs ont des versions différentes. Si vous mettez à niveau à partir d’une ancienne version d’Azure Connect d’Active Directory, il est judicieux de commencer par les deux serveurs à l’aide de la même version, mais il n’est pas obligatoire.
2. Si vous avez apporté une configuration personnalisée et votre serveur de test n’en sont pas dotés, suivez les étapes sous [déplacer une configuration personnalisée d’actif pour le serveur intermédiaire](#move-custom-configuration-from-active-to-staging-server).
3. Si vous mettez à niveau depuis une version antérieure d’Azure Connect d’Active Directory, mettez à niveau le serveur intermédiaire vers la dernière version. Si vous vous déplacez d’Azure AD Sync, puis installez Azure Connect d’Active Directory sur votre serveur de test.
4. Exécuter le moteur de synchronisation importation complète et synchronisation complète sur votre serveur de test.
5. Vérifiez que la nouvelle configuration, ne générait des modifications inattendues suivant les étapes sous **Vérifier** de [vérifier la configuration d’un serveur](active-directory-aadconnectsync-operations.md#verify-the-configuration-of-a-server). Si un élément n’est pas sync et importation comme prévue, correcte, exécution et vérifiez jusqu'à ce que les données semblent bonnes. Vous trouverez ces étapes dans la rubrique liée.
6. Basculez le serveur intermédiaire pour le serveur actif. Cette règle est la dernière étape **commutateur active server** de [vérifier la configuration d’un serveur](active-directory-aadconnectsync-operations.md#verify-the-configuration-of-a-server).
7. Si vous mettez à niveau Azure Connect d’Active Directory, mettez à niveau le serveur en mode vers la dernière version de zone de transit. Pour obtenir les données et la configuration de mise à niveau, suivez les mêmes étapes qu’avant. Une mise à niveau à partir d’Azure AD Sync, vous pouvez maintenant désactiver et retirer votre ancien serveur.

### <a name="move-custom-configuration-from-active-to-staging-server"></a>Déplacer la configuration personnalisée à partir d’active à un serveur intermédiaire
Si vous avez apporté des modifications de configuration pour le serveur actif, vous devez vous assurer que les modifications sont répercutées sur le serveur intermédiaire.

Les règles de synchronisation personnalisé que vous avez créé peuvent être déplacés avec PowerShell. Autres modifications doivent être appliquées de la même façon sur les deux systèmes et ne peuvent pas être migrées.

Chose que vous devez vous assurer est configuré de la même façon sur les deux serveurs :

- Connexion aux forêts mêmes.
- N’importe quel domaine et une unité d’organisation de filtrage.
- Les mêmes fonctions en option, telles que la synchronisation de mot de passe et le mot de passe d’écriture en différé.

**Déplacer les règles de synchronisation**  
Pour déplacer une règle de synchronisation personnalisés, effectuez les opérations suivantes :

1. Ouvrez **l’Éditeur de règles de synchronisation** sur votre serveur active.
2. Sélectionnez votre règle personnalisée. Cliquez sur **Exporter**. Cela ouvre une fenêtre du bloc-notes. Enregistrer le fichier temporaire avec l’extension PS1. Cela rend un script PowerShell. Copiez le fichier ps1 sur le serveur intermédiaire.  
![Exportation des règles de synchronisation](./media/active-directory-aadconnect-upgrade-previous-version/exportrule.png)
3. Le GUID du connecteur est différent sur le serveur intermédiaire et doit être modifié. Pour obtenir le GUID, démarrez **l’Éditeur de règles de synchronisation**, sélectionnez l’une des règles out-of-box qui représente le même système connecté et cliquez sur **Exporter**. Remplacez le GUID dans votre fichier PS1 avec le GUID à partir du serveur intermédiaire.
4. Dans une invite de commandes PowerShell, exécutez le fichier PS1. Cela créera la règle personnalisé de la synchronisation sur le serveur intermédiaire.
5. Si vous avez plusieurs règles personnalisées, répétez pour toutes les règles personnalisées.

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur [l’intégration des identités avec Azure Active Directory local](active-directory-aadconnect.md).
