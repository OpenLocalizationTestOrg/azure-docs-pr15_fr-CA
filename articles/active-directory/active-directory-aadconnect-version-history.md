<properties
   pageTitle="AD Azure se connecter : Historique de publication de Version | Microsoft Azure"
   description="Cette rubrique répertorie toutes les versions d’Azure AD Connect et Azure AD Sync"
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
   ms.workload="identity"
   ms.date="08/23/2016"
   ms.author="billmath"/>

# <a name="azure-ad-connect-version-release-history"></a>AD Azure se connecter : Historique de publication de Version

L’équipe Azure Active Directory met régulièrement à jour Azure Connect d’Active Directory avec les nouvelles fonctionnalités. Pas tous les ajouts sont applicables à tous les publics.

Cet article est conçu pour vous aider à garder une trace des versions qui ont été publiées et à comprendre si vous devez mettre à jour vers la version la plus récente ou non.

Il s’agit de la liste des rubriques connexes :

Rubrique |  
--------- | --------- |
Étapes pour mettre à niveau Azure Connect de publicité | Différentes méthodes [mise à niveau depuis une version antérieure à la dernière](active-directory-aadconnect-upgrade-previous-version.md) annonce Azure Connect version.
Autorisations requises | Pour les autorisations requises pour appliquer une mise à jour, reportez-vous à la section [comptes et autorisations](./connect/active-directory-aadconnect-accounts-permissions.md#upgrade)
Télécharger| [Connexion de téléchargement AD Azure](http://go.microsoft.com/fwlink/?LinkId=615771)

## <a name="112810"></a>1.1.281.0
Publié : l’août 2016

**Problèmes résolus :**

- Les modifications apportées à l’intervalle de synchronisation ne prend pas place jusqu’après la fin du prochain cycle de synchronisation.
- Assistant de connexion de publicité Azure n’accepte pas de compte Azure AD dont nom d’utilisateur commence par un trait de soulignement (\_).
- Assistant de connexion de publicité Azure ne parvient pas à authentifier le compte Azure AD fourni si le mot de passe contient trop de caractères spéciaux. Message d’erreur « Impossible de valider les informations d’identification. Une erreur inattendue s’est produite. » est retourné.
- Désinstallation du serveur de test désactive la synchronisation de mot de passe dans les clients AD Azure et provoque la synchronisation de mot de passe échouera sur le serveur actif.
- Synchronisation de mot de passe échoue dans les cas exceptionnels, lorsqu’il n’y a aucun hachage de mot de passe stocké sur l’utilisateur.
- Lorsque Azure Connect d’AD server est activée pour le mode de mise en attente, l’écriture différée de mot de passe n’est pas temporairement désactivée.
- Assistant de connexion de publicité Azure n’affiche pas la synchronisation de mot de passe réel et la configuration de d’écriture différée de mot de passe lorsque le serveur est en mode de mise en attente. Il leur apparaît toujours comme étant désactivée.
- Modifications de la configuration de la synchronisation de mot de passe et le mot de passe d’écriture en différé ne sont pas conservées par l’Assistant d’Azure Connect d’Active Directory lorsque le serveur est en mode de mise en attente.

**Améliorations :**

- Cmdlet de début-ADSyncSyncCycle mis à jour pour indiquer si elle est en mesure de démarrer avec succès un nouveau cycle de synchronisation ou non.
- Cmdlet de Stop-ADSyncSyncCycle ajouté à la fin du cycle de synchronisation et l’opération qui sont actuellement en cours.
- Mise à jour cmdlet de ADSyncScheduler-Stop pour terminer le cycle de synchronisation et l’opération qui sont actuellement en cours.
- Lors de la configuration des [Extensions du répertoire](active-directory-aadconnectsync-feature-directory-extensions.md) dans l’Assistant d’Azure Connect de publicité, annonce attribut de type « Chaîne Teletex » peut maintenant être sélectionné.

## <a name="111890"></a>1.1.189.0
Publié : 2016 juin

**Problèmes résolus et les améliorations :**

- Azure Connect d’annonce peut désormais être installé sur un serveur compatible FIPS.
    - Pour la synchronisation de mot de passe, reportez-vous à la section [synchronisation de mot de passe et FIPS](active-directory-aadconnectsync-implement-password-synchronization.md#password-synchronization-and-fips)
- Résout un problème où un nom NetBIOS ne peut pas être résolu en nom de domaine complet dans le connecteur Active Directory.

## <a name="111800"></a>1.1.180.0
Publié : 2016 mai

**Nouvelles fonctionnalités :**

- Vous avertit et vous permet de vérifier des domaines si vous n’avez pas il avant d’exécuter Azure Connect d’AD.
- Prise en charge supplémentaire pour [l’Allemagne de nuage de Microsoft](active-directory-aadconnect-instances.md#microsoft-cloud-germany).
- Prise en charge supplémentaire pour l’infrastructure de [cloud de Microsoft Azure gouvernement](active-directory-aadconnect-instances.md#microsoft-azure-government-cloud) dernière avec nouvelles exigences de l’URL.

**Problèmes résolus et les améliorations :**

- Un filtrage supplémentaire pour l’éditeur de règles de synchronisation pour faciliter la recherche de règles de synchronisation.
- Amélioration des performances lors de la suppression d’un espace de connecteur.
- Résolu un problème lors du même objet a été supprimé et ajouté dans le même exécuter (appelée supprimer/ajouter).
- Une règle de synchronisation désactivés ne seront plus réactiver inclus les objets et les attributs sur le schéma de mise à niveau ou de répertoire d’actualisation.

## <a name="111300"></a>1.1.130.0
Publié : 2016 avril

**Nouvelles fonctionnalités :**

- Prise en charge supplémentaire pour les attributs à valeurs multiples dans le [Répertoire Extensions](active-directory-aadconnectsync-feature-directory-extensions.md).
- Prise en charge supplémentaire pour les variations de configuration plus à [une mise à niveau automatique](active-directory-aadconnect-feature-automatic-upgrade.md) vers d’être éligible pour la mise à niveau.
- Ajouter des applets de commande pour un [planificateur personnalisé](active-directory-aadconnectsync-feature-scheduler.md#custom-scheduler).

## <a name="111190"></a>1.1.119.0
Publié : 2016 mars

**Problèmes résolus :**

- Fait que l’installation Express ne peut pas être utilisée sur Windows Server 2008 (pre-R2) depuis la synchronisation de mot de passe n'est pas pris en charge sur ce système d’exploitation.
- Mise à niveau à partir de la synchronisation d’annuaire avec une configuration de filtre personnalisé n’a pas fonctionné comme prévu.
- Lors de la mise à niveau vers une version plus récente et il n’y a aucune modification à la configuration, une importation/la synchronisation complète ne doit pas être planifiée.

## <a name="111100"></a>1.1.110.0
Publié : le février 2016

**Problèmes résolus :**

- Mise à niveau à partir de versions antérieures ne fonctionne pas si l’installation n’est pas dans le dossier **C:\Program Files** .
- Si vous installez et que vous désélectionnez **Démarrer le processus de synchronisation...** à la fin de l’Assistant d’installation, exécuter l’Assistant installation n’activera pas le planificateur.
- Le planificateur ne fonctionnera pas comme prévu sur les serveurs où le format de date/heure n’est pas en-US. Il bloque également `Get-ADSyncScheduler` pour retourner les heures correctes.
- Si vous avez installé une version antérieure d’Azure AD Connect avec ADFS comme l’option de connexion et de la mise à niveau, vous ne pouvez pas réexécuter l’Assistant installation.

## <a name="111050"></a>1.1.105.0
Publié : le février 2016

**Nouvelles fonctionnalités :**

- Fonctionnalité de [mise à niveau automatique](active-directory-aadconnect-feature-automatic-upgrade.md) pour les clients de paramètres Express.
- Prise en charge de l’administrateur global à l’aide d’AMF et PIM dans l’Assistant d’installation.
    - Vous devez autoriser votre proxy afin que le trafic à https://secure.aadcdn.microsoftonline-p.com si vous utilisez AMF.
    - Vous devez ajouter https://secure.aadcdn.microsoftonline-p.com à votre liste de sites de confiance de l’AMF de fonctionner correctement.
- Autoriser le changement de méthode de connexion de l’utilisateur après l’installation initiale.
- Autoriser le [filtrage de domaine et unité d’organisation](./connect/active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering) dans l’Assistant d’installation. Cela permet également la connexion à des forêts où pas tous les domaines sont disponibles.
- [Le planificateur](active-directory-aadconnectsync-feature-scheduler.md) est intégré au moteur de synchronisation.

**Promu à partir de l’aperçu à GA de fonctionnalités :**

- [L’écriture différée du périphérique](active-directory-aadconnect-feature-device-writeback.md).
- [Extensions de répertoire](active-directory-aadconnectsync-feature-directory-extensions.md).

**Nouvelles fonctionnalités d’aperçu :**

- La nouvelle valeur par défaut de synchronisation cycle intervalle est de 30 minutes. Utilisé à 3 heures pour toutes les versions antérieures. Ajoute la prise en charge pour modifier le comportement du [Planificateur](active-directory-aadconnectsync-feature-scheduler.md) .

**Problèmes résolus :**

- La page de domaines vérifier DNS n’a pas reconnu toujours des domaines.
- Demande d’informations d’identification d’administrateur de domaine lors de la configuration d’ADFS.
- Les locaux sur les comptes Active Directory ne sont pas reconnus par l’Assistant d’installation si situé dans un domaine avec une autre arborescence DNS du domaine racine.

## <a name="1091310"></a>1.0.9131.0
Publié : le décembre 2015

**Problèmes résolus :**

- Synchronisation de mot de passe ne fonctionne pas lorsque vous modifiez les mots de passe dans les services AD DS, mais fonctionne lorsque vous configurez le mot de passe.
- Lorsque vous avez un serveur proxy, l’authentification AD Azure peut échouer lors de l’installation ou annuler mise à niveau de la page de configuration.
- Mise à jour d’une version précédente d’Azure Connect AD intégral de SQL Server échoue si vous n’êtes pas administrateur système dans SQL.
- Mise à jour d’une version précédente d’Azure Connect AD avec une télécommande de SQL Server affiche l’erreur « Impossible d’accéder à la base de données ADSync SQL ».

## <a name="1091250"></a>1.0.9125.0
Publié : le novembre 2015

**Nouvelles fonctionnalités :**

- Pouvez reconfigurer l’ADFS d’approbation AD Azure.
- Permet d’actualiser le schéma Active Directory et de régénérer les règles de synchronisation.
- Peut désactiver une règle de synchronisation.
- Peut définir des « AuthoritativeNull » en tant que nouveau littéral dans une règle de synchronisation.

**Nouvelles fonctionnalités d’aperçu :**

- [Azure AD connecter la santé pour la synchronisation](active-directory-aadconnect-health-sync.md).
- Prise en charge pour la synchronisation de mot de passe de [Services de domaine Active Directory de Azure](active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords) .

**Nouveau scénario pris en charge :**

- Prend en charge plusieurs organisations de Exchange sur site. Pour plus d’informations, reportez-vous à la section [Déploiement hybride avec plusieurs forêts Active Directory](https://technet.microsoft.com/library/jj873754.aspx) .

**Problèmes résolus :**

- Problèmes de synchronisation de mot de passe :
    - Un objet déplacé d’hors de portée à portée n’aura pas son mot de passe synchronisé. Cette comprend à la fois d’unité d’organisation et de filtrage de l’attribut.
    - Sélection d’une nouvelle unité d’organisation à inclure dans la synchronisation ne nécessite pas de synchronisation de mot de passe.
    - Lorsqu’un utilisateur désactivé est activé le mot de passe n’est pas synchronisée.
    - La file d’attente des nouvelles tentatives de mot de passe est illimité et la limite précédente de 5 000 objets à a été supprimée.
    - [Résolution des problèmes de l’amélioration](active-directory-aadconnectsync-implement-password-synchronization.md#troubleshoot-password-synchronization).
- Impossible de se connecter à Active Directory avec le niveau fonctionnel de forêt de Windows Server 2016.
- Impossible de modifier le groupe utilisé pour le filtrage de groupe après l’installation initiale.
- Ne plus créera un nouveau profil utilisateur sur le serveur Azure Connect d’Active Directory pour chaque utilisateur effectuant un changement de mot de passe avec l’écriture différée de mot de passe activée.
- Pas en mesure d’utiliser les valeurs entier Long synchronisation étendues de règles.
- La case à cocher « d’écriture différée du périphérique » reste désactivé s’il existe des contrôleurs de domaine non joignable.

## <a name="1086670"></a>1.0.8667.0
Publié : l’août 2015

**Nouvelles fonctionnalités :**

- Azure Connect d’Active Directory, l’Assistant d’installation est désormais localisé à toutes les langues de Windows Server.
- Prise en charge supplémentaire pour le compte de déverrouillage lorsque vous utilisez la gestion des mots de passe Active Directory Azure.

**Problèmes résolus :**

- Assistant d’installation AD Connect Azure se bloque si un autre utilisateur continue l’installation plutôt que la personne qui a commencé à l’installation.
- Si une désinstallation précédente d’échoue Azure AD Connect pour désinstaller Azure AD connexion synchronisation correctement, il n’est pas possible de réinstaller.
- Impossible d’installer Azure Connect AD à l’aide d’installation Express si l’utilisateur n’est pas dans le domaine racine de la forêt, ou si une version non anglaise d’Active Directory est utilisée.
- Si le nom de domaine complet du compte d’utilisateur Active Directory ne peut pas être résolu, un message d’erreur « Impossible de valider le schéma » trompeur est affiché.
- Si le compte utilisé sur le connecteur Active Directory est modifié en dehors de l’Assistant, l’Assistant échoue lors des exécutions suivantes.
- Azure AD connexion échoue parfois d’installer sur un contrôleur de domaine.
- Impossible d’activer et de désactiver les « Mode de mise en attente » si les attributs d’extension ont été ajoutés.
- L’écriture différée de mot de passe échoue dans une configuration en raison d’un mauvais mot de passe sur le connecteur Active Directory.
- Synchronisation d’annuaire ne peut pas être mis à niveau si le nom de domaine est utilisé dans le filtrage de l’attribut.
- Utilisation du processeur excessive lors de l’utilisation de réinitialisation de mot de passe.

**Fonctionnalités supprimées :**

- [L’écriture différée de l’utilisateur](active-directory-aadconnect-feature-preview.md#user-writeback) a été supprimé temporairement la fonction d’aperçu en fonction des commentaires de nos clients Aperçu. Il va être rajouté ultérieurement lorsque nous avons abordé les commentaires fournis.

## <a name="1086410"></a>1.0.8641.0
Publié : 2015 juin

**Version initiale d’Azure Connect d’Active Directory.**

Renommée dans Azure AD Sync pour Azure Connect d’Active Directory.

**Nouvelles fonctionnalités :**

- Installation [Express de paramètres](./connect/active-directory-aadconnect-get-started-express.md)
- Pouvez [configurer ADFS](./connect/active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs)
- Peut [mettre à niveau à partir de la synchronisation d’annuaire](./connect/active-directory-aadconnect-dirsync-upgrade-get-started.md)
- [Empêcher des suppressions accidentelles](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md)
- Introduit le [mode de mise en attente](active-directory-aadconnectsync-operations.md#staging-mode)

**Nouvelles fonctionnalités d’aperçu :**

- [Écriture différée de l’utilisateur](active-directory-aadconnect-feature-preview.md#user-writeback)
- [Écriture différée du groupe](active-directory-aadconnect-feature-preview.md#group-writeback)
- [Écriture différée de périphérique](active-directory-aadconnect-feature-device-writeback.md)
- [Extensions de répertoire](active-directory-aadconnect-feature-preview.md#directory-extensions)


## <a name="104940501"></a>1.0.494.0501
Publié : 2015 mai

**Nouvelle exigence :**

- Azure AD Sync requiert désormais le .net framework, version 4.5.1 être installé.

**Problèmes résolus :**

- L’écriture différée de mot de passe à partir d’AD Azure échoue avec une erreur de connectivité servicebus.

## <a name="104910413"></a>1.0.491.0413
Publié : 2015 avril

**Problèmes résolus et les améliorations :**

- Le connecteur Active Directory ne traite pas correctement les suppressions si la Corbeille est activée et il existe plusieurs domaines dans la forêt.
- Les performances des opérations d’importation a été amélioré pour le connecteur Active Directory d’Azure.
- Lorsqu’un groupe a dépassé la limite l’appartenance (par défaut, la limite est fixée à 50 Ko objets), le groupe a été supprimé dans Azure Active Directory. Le nouveau comportement est que le groupe reste, une erreur est levée et aucune nouvelle modification de l’appartenance à exporter.
- Un objet ne peut pas être mis en service si une suppression nouvellement créée avec le même nom de domaine est déjà présente dans l’espace de connecteur.
- Certains objets sont marqués pour la synchronisation au cours d’une synchronisation delta même si aucune modification n’est mis en place sur l’objet.
- Forcer une synchronisation de mot de passe supprime également la liste de contrôleurs de domaine par défaut.
- CSExportAnalyzer a des problèmes avec certains États d’objets.

**Nouvelles fonctionnalités :**

- Une jointure peut maintenant se connecter au type d’objet « ANY » dans le MV.

## <a name="104850222"></a>1.0.485.0222
Publié : le février 2015

**Améliorations :**

- Performances améliorées.

**Problèmes résolus :**

- Synchronisation de mot de passe respecte l’attribut cloudFiltered utilisé par le filtrage de l’attribut. Les objets filtrés ne sera plus dans la portée pour la synchronisation de mot de passe.
- Dans de rares situations où la topologie était très grand nombre de contrôleurs de domaine, la synchronisation de mot de passe ne fonctionne pas.
- « Arrêté-server » lors de l’importation à partir du connecteur Active Directory Azure après la gestion de périphérique a été activée dans Azure AD/Intune.
- Rejoindre des entités de sécurité étrangères (FSP) à partir de plusieurs domaines dans la même forêt provoque une erreur de jointure ambiguës.

## <a name="104751202"></a>1.0.475.1202
Publié : décembre de 2014

**Nouvelles fonctionnalités :**

- Il est maintenant pris en charge pour faire la synchronisation de mot de passe avec le filtrage de l’attribut de base. Pour plus d’informations, consultez [synchronisation de mot de passe avec le filtrage](active-directory-aadconnectsync-configure-filtering.md).
- L’attribut msDS-ExternalDirectoryObjectID est réécrites vers Active Directory. Cette prise en charge pour les applications d’Office 365 à l’aide de OAuth2 pour accéder à la fois, en ligne et boîtes aux lettres dans un déploiement de Exchange hybride sur site.

**Les problèmes de mise à niveau :**

- Une version plus récente de l’assistant de connexion est disponible sur le serveur.
- Un chemin d’installation personnalisé a été utilisé pour installer AD d’Azure Sync.
- Un critère de jointure personnalisée non valide empêche la mise à niveau.

**Autres correctifs :**

- Fixe les modèles pour Office Pro Plus.
- Problèmes d’installation fixe provoqués par des noms d’utilisateur qui commencent par un tiret.
- Fixe la perte du paramètre sourceAnchor lors de l’exécution de l’Assistant d’installation une deuxième fois.
- Suivi ETW fixe pour la synchronisation de mot de passe

## <a name="104701023"></a>1.0.470.1023
Publié : octobre de 2014

**Nouvelles fonctionnalités :**

- Synchronisation de mot de passe à partir de plusieurs locaux annonce publicitaire Azure.
- Installation interface utilisateur localisée pour toutes les langues de Windows Server.

**Mise à niveau à partir de AADSync 1.0 GA**

Si vous avez déjà Azure AD Sync est installé, il existe une étape supplémentaire, que vous devez prendre au cas où vous avez modifié les règles de synchronisation prête à l’emploi. Une fois que vous avez mis à niveau vers le 1.0.470.1023 version, la synchronisation, vous avez modifié des règles sont dupliqués. Pour chaque règle de synchronisation modifié, effectuez les opérations suivantes :

- Recherchez la règle de synchronisation, vous avez modifié et prenez note des modifications.
- Supprimer la règle de la synchronisation.
- Recherchez la nouvelle règle de synchronisation créés par Azure AD Sync et appliquez à nouveau les modifications.

**Autorisations pour le compte Active Directory**

Le compte Active Directory doit bénéficier d’autorisations supplémentaires pour être en mesure de lire les hachages de mot de passe dans Active Directory. Les autorisations à accorder sont nommées « Réplication des changements de répertoire » et « Réplication de répertoire toutes les modifications ». Les deux autorisations sont nécessaires pour être en mesure de lire les hachages de mot de passe

## <a name="104190911"></a>1.0.419.0911
Publié : septembre de 2014

**Version initiale d’Azure AD Sync.**

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur [l’intégration des identités avec Azure Active Directory local](active-directory-aadconnect.md).
