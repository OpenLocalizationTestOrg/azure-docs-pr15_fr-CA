<properties
    pageTitle="Azure AD Connect : Activation d’écriture différée du périphérique | Microsoft Azure"
    description="Ce document explique comment activer l’écriture différée de périphérique à l’aide d’Azure Connect de publicité"
    services="active-directory"
    documentationCenter=""
    authors="billmath"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="active-directory"  
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/29/2016"
    ms.author="billmath"/>

# <a name="azure-ad-connect-enabling-device-writeback"></a>Azure AD Connect : Activation d’écriture différée du périphérique

>[AZURE.NOTE] Un abonnement Azure AD Premium est requis pour l’écriture différée du périphérique.

La documentation suivante fournit des informations sur l’activation de la fonctionnalité d’écriture différée de périphérique dans Azure Connect d’AD. L’écriture différée de périphérique est utilisée dans les scénarios suivants :

- Activer l’accès conditionnel basé sur les périphériques pour ADFS (2012 R2 ou version ultérieure) (approbations de partie utilisatrice) d’applications protégées.

Cela fournit une sécurité supplémentaire et l’assurance que l’accès aux applications est accordé uniquement aux périphériques approuvés. Pour plus d’informations sur l’accès conditionnel, voir [Gestion des risques grâce à accès conditionnel](active-directory-conditional-access.md) et la [configuration d’accès conditionnel en local à l’aide d’Azure Active Directory périphérique d’enregistrement](https://msdn.microsoft.com/library/azure/dn788908.aspx).

>[AZURE.IMPORTANT]
<li>Périphériques doivent se trouver dans la même forêt que les utilisateurs. Dans la mesure où les périphériques doivent être réécrit dans une seule forêt, cette fonctionnalité ne pas prend en charge un déploiement à plusieurs forêts d’utilisateurs.</li>
<li>Objet de configuration qu’un seul périphérique d’enregistrement peut être ajouté à la forêt d’Active Directory sur site. Cette fonctionnalité n’est pas compatible avec une topologie où Active Directory local est synchronisé à plusieurs annuaires AD Azure.</li>

## <a name="part-1-install-azure-ad-connect"></a>Partie 1 : Installation AD Azure se connecter
1. Installer Azure Connect AD à l’aide de personnalisé ou Express des paramètres. Microsoft vous recommande d’abord tous les utilisateurs et les groupes synchronisés avec succès avant d’activer l’écriture différée du périphérique.

## <a name="part-2-prepare-active-directory"></a>Partie 2 : Préparation d’Active Directory
Utilisez les étapes suivantes pour vous préparer à l’utilisation d’écriture différée du périphérique.

1.  À partir de l’ordinateur sur lequel est installé Azure AD Connect, lancer PowerShell en mode élevé.

2.  Si le module PowerShell de Active Directory n’est pas installé, installez-le à l’aide de la commande suivante :

    `Install-WindowsFeature –Name AD-Domain-Services –IncludeManagementTools`

3. Si le module Azure Active Directory PowerShell n’est pas installé, puis télécharger et l’installer à partir [d’Azure Active Directory Module pour Windows PowerShell (version 64 bits)](http://go.microsoft.com/fwlink/p/?linkid=236297). Ce composant a une dépendance sur l’assistant de connexion, qui est installé avec Azure Connect d’Active Directory.

4.  Avec les informations d’identification d’entreprise admin, exécutez les commandes suivantes et puis quittez PowerShell.

    `Import-Module 'C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1'`

    `Initialize-ADSyncDeviceWriteback {Optional:–DomainName [name] Optional:-AdConnectorAccount [account]}`

Informations d’identification d’administrateur d’entreprise sont nécessaires dans la mesure où les modifications apportées à l’espace de noms de configuration sont nécessaires. Un administrateur de domaine n’aura pas suffisamment d’autorisations.

![PowerShell pour activer l’écriture différée de périphérique](./media/active-directory-aadconnect-feature-device-writeback/powershell.png)

Description :

- S’ils n’existent pas déjà, crée et configure les nouveaux conteneurs et objets sous CN = périphérique d’enregistrement Configuration, CN = Services, CN = Configuration, [forêt-dn].
- S’ils n’existent pas déjà, crée et configure les nouveaux conteneurs et objets sous CN = RegisteredDevices, [domaine-dn]. Les objets du périphérique seront créés dans ce conteneur.
- Définit les autorisations nécessaires sur le compte Azure le connecteur Active Directory, pour gérer des périphériques sur votre annuaire Active Directory.
- Seulement doit être exécutée sur une forêt, même si Azure Connect d’Active Directory est installé sur plusieurs forêts.

Paramètres :

- NomDomaine : Domaine Active Directory où seront créés les objets de périphérique. Remarque : Tous les périphériques pour une forêt Active Directory donnée seront créés dans un seul domaine.
- AdConnectorAccount : Compte Active Directory qui sera utilisé par Azure Connect d’Active Directory pour gérer des objets dans l’annuaire. C’est le compte utilisé par la synchronisation d’Azure Connect de publicité pour se connecter à Active Directory. Si vous avez installé en utilisant les paramètres express, il est le compte que le préfixe MSOL_.

## <a name="part-3-enable-device-writeback-in-azure-ad-connect"></a>Partie 3 : Activer périphérique écriture différée dans Azure Connect de publicité
Utilisez la procédure suivante pour activer l’écriture différée de dispositif dans Azure Connect d’Active Directory.

1.  Exécutez l’Assistant installation de nouveau. Sélectionnez **Personnaliser les options de synchronisation** à partir de la page des tâches supplémentaires et cliquez sur **suivant**.
![Installation personnalisée personnaliser les options de synchronisation](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback2.png)
2.  Dans la page fonctionnalités facultatives, l’écriture différée du périphérique sera n’est plus grisée. Veuillez Notez que si l’annonce Azure se connecter étapes de préparation sont en écriture différée du périphérique terminée est grisé out dans la page fonctionnalités facultatives. Cochez la case pour l’écriture différée du périphérique et cliquez sur **suivant**. Si la case à cocher est toujours désactivée, consultez la [section Dépannage](#the-writeback-checkbox-is-still-disabled).
![Les fonctionnalités facultatives d’écriture différée du périphérique d’installation personnalisée](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback3.png)
3.  Sur la page d’écriture différée, vous verrez le domaine fourni comme la forêt d’écriture différée du périphérique par défaut.
![Forêt de cible d’écriture différée de périphérique installation personnalisée](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback4.png)
4.  Terminer l’installation de l’Assistant sans qu’aucune modification de configuration supplémentaire. Si nécessaire, reportez-vous à [installation personnalisée de Azure AD connexion.](./connect/active-directory-aadconnect-get-started-custom.md)

## <a name="enable-conditional-access"></a>Activer l’accès conditionnel
Pour activer ce scénario d’obtenir des instructions détaillées sont disponibles dans [configurer les accès conditionnel sur site à l’aide d’Azure Active Directory périphérique d’enregistrement](https://msdn.microsoft.com/library/azure/dn788908.aspx).

## <a name="verify-devices-are-synchronized-to-active-directory"></a>Vérifiez que les périphériques sont synchronisées avec Active Directory
L’écriture différée de périphérique doit maintenant fonctionner correctement. N’oubliez pas qu’il peut prendre jusqu'à 3 heures pour les objets de périphérique soit précédent écrit à Active Directory.  Pour vérifier que vos périphériques sont en cours de synchronisation correctement, effectuez les opérations suivantes après que les règles de synchronisation effectué :

1.  Lancez le centre d’administration Active Directory.
2.  Développez RegisteredDevices, dans le domaine qui est en cours de fédéré.
![Centre d’administration Active Directory inscrit des périphériques](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback5.png)
3.  Les appareils inscrits en cours seront répertoriées.
![Centre d’administration Active Directory inscrit la liste des périphériques](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback6.png)

## <a name="troubleshooting"></a>Résolution des problèmes

### <a name="the-writeback-checkbox-is-still-disabled"></a>La case à cocher écriture différée est toujours désactivé.
Si la case à cocher pour l’écriture différée de périphérique n’est pas activé même si vous avez suivi les étapes ci-dessus, la procédure suivante vous guidera vérifie que l’Assistant installation avant que la case à cocher est activée.

Premièrement premier :

- Vérifiez au moins une forêt est Windows Server 2012R2. Le type d’objet de périphérique doit être présent.
- Si l’Assistant d’installation est déjà en cours d’exécution, les modifications ne seront pas détectées. Dans ce cas, exécutez l’Assistant installation et exécutez-le à nouveau.
- Assurez-vous que le compte que vous fournissez dans le script d’initialisation est en fait l’utilisateur correct utilisé par le connecteur Active Directory. Pour vérifier cela, procédez comme suit :
    - Dans le menu Démarrer, ouvrez le **Service de synchronisation**.
    - Ouvrez l’onglet **connecteurs** .
    - Rechercher le connecteur avec type de Services de domaine Active Directory et le sélectionner.
    - Sous **Actions**, sélectionnez **Propriétés**.
    - Aller à **se connecter à la forêt Active Directory**. Vérifiez que le nom de domaine et d’utilisateur spécifié sur cette correspondance écran le compte fourni pour le script.
![Compte de connecteur dans le Gestionnaire de Service de synchronisation](./media/active-directory-aadconnect-feature-device-writeback/connectoraccount.png)

Vérification de la configuration dans Active Directory :
- Vérifiez que le Service d’inscription de périphérique se trouve à l’emplacement ci-dessous (CN = DeviceRegistrationService, CN = Services d’inscription de périphérique, CN = périphérique d’enregistrement Configuration, CN = Services, CN = Configuration) sous le contexte de nommage de configuration.

![Résoudre les problèmes, DeviceRegistrationService dans l’espace de noms de configuration](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot1.png)

- Vérifiez qu’un seul objet de configuration en recherchant dans l’espace de noms de configuration. S’il en existe plusieurs, supprimer les doublons.

![Résoudre les problèmes, à rechercher les objets en double](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot2.png)

- Dans l’objet de Service d’enregistrement de périphérique, assurez-vous que l’attribut msDS-DeviceLocation est présente et a une valeur. Recherche de cet emplacement et l’Assurez-vous qu’il est présent avec le type d’objet msDS-DeviceContainer.

![Résoudre les problèmes, msDS-DeviceLocation](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot3.png)

![Résoudre les problèmes, la classe d’objet RegisteredDevices](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot4.png)

- Vérifiez que le compte utilisé par le connecteur Active Directory dispose des autorisations nécessaires sur le conteneur de périphériques inscrits trouvé à l’étape précédente. Il s’agit d’autorisations attendues sur ce conteneur :

![Résoudre les problèmes, vérifiez les autorisations sur le conteneur](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot5.png)

- Vérifiez que le compte Active Directory dispose d’autorisations sur CN = périphérique d’enregistrement Configuration, CN = Services, CN = objet de Configuration.

![Résoudre les problèmes, vérifiez les autorisations de Configuration du périphérique d’enregistrement](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot6.png)

## <a name="additional-information"></a>Informations supplémentaires
- [Gestion des risques à accès conditionnel](active-directory-conditional-access.md)
- [Configuration d’un accès conditionnel en local à l’aide d’Azure Active Directory périphérique d’enregistrement](https://msdn.microsoft.com/library/azure/dn788908.aspx)

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur [l’intégration des identités avec Azure Active Directory local](active-directory-aadconnect.md).
