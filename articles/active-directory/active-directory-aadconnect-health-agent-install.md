<properties
    pageTitle="Installation de l’Agent de santé de connexion AD Azure | Microsoft Azure"
    description="Il s’agit de la page AD Azure se connecter la santé qui décrit l’installation de l’agent pour AD FS et de synchronisation."
    services="active-directory"
    documentationCenter=""
    authors="karavar"
    manager="samueld"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/18/2016"
    ms.author="vakarand"/>


# <a name="azure-ad-connect-health-agent-installation"></a>AD Azure se connecter l’Installation de l’Agent de la santé

Ce document vous guide à travers l’installation et configurer les Agents de santé se connecter AD Azure. Vous pouvez télécharger les agents à partir [d’ici](active-directory-aadconnect-health.md#download-and-install-azure-ad-connect-health-agent).

##  <a name="requirements"></a>Configuration requise
Le tableau suivant est une liste de conditions d’utilisation de santé de connexion AD Azure.

| Exigence | Description|
| ----------- | ---------- |
|Azure AD Premium| Santé de connexion AD Azure est une fonctionnalité d’Azure AD Premium et nécessite des Azure AD Premium. </br></br>Pour plus d’informations, consultez [mise en route avec Azure AD Premium](active-directory-get-started-premium.md) </br>Pour démarrer une version d’évaluation gratuite de 30 jours, consultez [Démarrer une version d’évaluation.](https://azure.microsoft.com/trial/get-started-active-directory/)|
|Vous devez être un administrateur global de votre annonce Azure mise en route la santé se connecter Azure AD|Par défaut, seuls les administrateurs globaux peuvent installer et configurer les agents de santé pour commencer, accéder au portail et effectuer toutes les opérations au sein de la santé se connecter Azure AD. Pour plus d’informations, voir [administration de votre annuaire AD Azure](active-directory-administer.md). <br><br> À l’aide du contrôle d’accès rôle vous pouvez autoriser l’accès Azure santé AD de se connecter à d’autres utilisateurs dans votre organisation. Pour plus d’informations, consultez [rôle de contrôle d’accès pour la santé se connecter Azure AD.](active-directory-aadconnect-health-operations.md#manage-access-with-role-based-access-control) </br></br>**Important :** Le compte utilisé lors de l’installation des agents doit être un compte de travail ou l’école. Il ne peut pas être un compte Microsoft. Pour plus d’informations, consultez [inscrire pour Azure en tant qu’organisation](sign-up-organization.md)
|L’Agent d’intégrité connexion AD Azure est installé sur chaque serveur cible| Santé de connexion AD Azure nécessite l’installation d’agents sur les serveurs cibles, pour fournir les données qui sont affichées dans le portail. </br></br>Par exemple, pour obtenir des données à partir de votre infrastructure de locaux AD FS, l’agent doit être installé sur les serveurs ADFS, AD FS Proxy et le Proxy d’Application Web. De même, pour obtenir les données de vos locaux sur infrastructure de domaine Active Directory, l’agent doit être installé sur les contrôleurs de domaine. </br></br>**Important :** Le compte utilisé lors de l’installation des agents doit être un compte de travail ou l’école. Il ne peut pas être un compte Microsoft. Pour plus d’informations, consultez [inscrire pour Azure en tant qu’organisation](sign-up-organization.md)|
|Connectivité sortante pour les points de terminaison de service Azure|Pendant l’installation et l’exécution, l’agent nécessite une connectivité aux points de terminaison de service AD Azure se connecter la santé. Si la connectivité sortante est bloquée, vérifiez que les points de terminaison suivants sont ajoutés à la liste autorisée : </br></br><li>& #42 ;. BLOB.Core.Windows.NET </li><li>& #42 ;. Queue.Core.Windows.NET</li><li>adhsprodwus.ServiceBus.Windows.NET - Port : 5671 </li><li>https://Management.Azure.com </li><li>https://s1.adhybridhealth.Azure.com/</li><li>https://policykeyservice.DC.AD.msft.NET/</li><li>https://login.Windows.NET</li><li>https://login.microsoftonline.com</li><li>https://Secure.aadcdn.microsoftonline-p.com</li> |
|Ports du pare-feu sur le serveur qui exécute l’agent.| L’agent requiert les ports de pare-feu suivantes pour être ouvert pour l’agent communiquer avec les points de terminaison de service Azure AD santé.</br></br><li>TCP/UDP port 443</li><li>Port TCP/UDP 5671</li>
|Autoriser les sites Web suivants si la sécurité renforcée d’Internet Explorer est activée.| Si la sécurité renforcée d’Internet Explorer est activée, les sites Web suivants doivent être autorisés sur le serveur qui va l’agent est installé.</br></br><li>https://login.microsoftonline.com</li><li>https://Secure.aadcdn.microsoftonline-p.com</li><li>https://login.Windows.NET</li><li>Le serveur de fédération pour votre organisation approuvé par Azure Active Directory. Par exemple : https://sts.contoso.com</li>




## <a name="installing-the-azure-ad-connect-health-agent-for-ad-fs"></a>L’installation de la publicité Azure connecter l’Agent d’intégrité pour AD FS
Pour démarrer l’installation de l’agent, double-cliquez sur le fichier .exe que vous avez téléchargé. Dans le premier écran, cliquez sur Installer.

![Vérifier Active Directory Azure se connecter la santé](./media/active-directory-aadconnect-health-requirements/install1.png)

Une fois l’installation terminée, cliquez sur Configurer maintenant.

![Vérifier Active Directory Azure se connecter la santé](./media/active-directory-aadconnect-health-requirements/install2.png)

Une invite de commande est lancée, suivie par certains PowerShell qui exécute l’AzureADConnectHealthADFSAgent de la caisse enregistreuse. Lorsque vous êtes invité à vous connecter à Azure, continuez et ouvrez une session.

![Vérifier Active Directory Azure se connecter la santé](./media/active-directory-aadconnect-health-requirements/install3.png)


Après l’ouverture de session, PowerShell va continuer. Une fois terminée, vous pouvez fermer PowerShell, et la configuration est terminée.

À ce stade, les services doivent être démarrés automatiquement permettant à l’agent surveiller et collecter des données. Si vous ne remplissez pas toutes les conditions préalables décrites dans les sections précédentes, les avertissements s’affichent dans la fenêtre PowerShell. Veillez à effectuer [Configuration requise](active-directory-aadconnect-health-agent-install.md#requirements) avant l’installation de l’agent. La capture d’écran suivante est un exemple de ces erreurs.

![Vérifier Active Directory Azure se connecter la santé](./media/active-directory-aadconnect-health-requirements/install4.png)

Pour vérifier que l’agent a été installé, recherchez les services suivants sur le serveur. Si vous avez terminé la configuration, ils doivent déjà être en cours d’exécution. Dans le cas contraire, ils sont arrêtés jusqu'à ce que la configuration est terminée.

- Annonce Azure se connecter santé AD FS Diagnostics Service
- Annonce Azure se connecter Service de perspectives sur la santé AD FS
- Azure AD FS santé AD Service de surveillance de se connecter

![Vérifier Active Directory Azure se connecter la santé](./media/active-directory-aadconnect-health-requirements/install5.png)


### <a name="agent-installation-on-windows-server-2008-r2-servers"></a>Installation de l’agent sur les serveurs de Windows Server 2008 R2

Étapes pour les serveurs de Windows Server 2008 R2 :

1. Assurez-vous que le serveur est en cours d’exécution au niveau du Service Pack 1 ou version ultérieure.
1. Désactiver renforcée d’Internet Explorer pour l’installation de l’agent :
1. Installez Windows PowerShell 4.0 sur chacun des serveurs avant l’installation de l’agent d’intégrité d’Active Directory. Pour installer Windows PowerShell 4.0 :
 - Installez [Microsoft.NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=40779) en utilisant le lien suivant pour télécharger le programme d’installation en mode hors connexion.
 - Installer PowerShell ISE (à partir des fonctionnalités de Windows)
 - Installer le [de Windows Management Framework 4.0.](https://www.microsoft.com/download/details.aspx?id=40855)
 - Installer Internet Explorer version 10 ou ultérieure sur le serveur. (Requis par le Service de santé à s’authentifier à l’aide de vos informations d’identification d’administrateur d’Azure).
1. Pour plus d’informations sur l’installation de Windows PowerShell 4.0 sur Windows Server 2008 R2, consultez l’article wiki [ici](http://social.technet.microsoft.com/wiki/contents/articles/20623.step-by-step-upgrading-the-powershell-version-4-on-2008-r2.aspx).

### <a name="enable-auditing-for-ad-fs"></a>Activer l’audit pour AD FS

> [AZURE.NOTE] Cette section ne s’applique qu’aux serveurs de fédération AD FS.

Afin que la fonctionnalité d’utilisation Analytique recueillir et analyser des données, l’agent d’intégrité de connexion AD Azure a besoin des informations dans les journaux d’Audit AD FS. Ces journaux ne sont pas activés par défaut. Utilisez les procédures suivantes pour activer l’audit de AD FS et pour localiser les journaux d’audit AD FS, sur vos serveurs ADFS.

#### <a name="to-enable-auditing-for-ad-fs-20"></a>Pour activer l’audit pour AD FS 2.0

1. Cliquez sur **Démarrer**, pointez sur **programmes**, pointez sur **Outils d’administration**, puis cliquez sur **Stratégie de sécurité locale**.
2. Accédez au dossier **Sécurité paramètres de sécurité\Stratégies locales\Attribution des droits gestion** , puis sur Générer des audits de sécurité.
3. Sous l’onglet **Paramètre de sécurité locale** , vérifiez que le compte de service 2.0 AD FS est répertorié. Si elle n’est pas présente, cliquez sur **Ajouter un utilisateur ou un groupe** et l’ajouter à la liste et puis cliquez sur **OK**.
4. Pour activer l’audit, ouvrez une invite de commandes avec des privilèges élevés et exécutez la commande suivante :<code>auditpol.exe /set /subcategory:"Application Generated" /failure:enable /success:enable</code>
5. Fermez la stratégie de sécurité locale et ouvrez le composant logiciel enfichable Gestion. Pour ouvrir le composant logiciel enfichable, cliquez sur **Démarrer**, pointez sur **programmes**, pointez sur **Outils d’administration**, puis cliquez sur AD FS 2.0 Management.
6. Dans le volet Actions, cliquez sur Modifier les propriétés du Service de fédération.
7. Dans la boîte de dialogue **Propriétés du Service de fédération** , cliquez sur l’onglet **événements** .
8. Activez les cases à cocher **des audits de succès** et les **audits des échecs** .
9. Cliquez sur **OK**.

#### <a name="to-enable-auditing-for-ad-fs-on-windows-server-2012-r2"></a>Pour activer l’audit pour AD FS dans Windows Server 2012 R2

1. Ouvrez **Stratégie de sécurité locale** en ouvrant **Le Gestionnaire de serveur** sur l’écran de démarrage, ou le Gestionnaire de serveur dans la barre des tâches sur le bureau, puis cliquez sur **Stratégie de sécurité des outils/Local**.
2. Accédez au dossier **Paramètres de sécurité\Stratégies Locales\attribution des droits** et puis double-cliquez sur **des audits de sécurité de générer**.
3. Sous l’onglet **Paramètre de sécurité locale** , vérifiez que le compte de service AD FS est répertorié. Si elle n’est pas présente, cliquez sur **Ajouter un utilisateur ou un groupe** et l’ajouter à la liste et puis cliquez sur **OK**.
4. Pour activer l’audit, ouvrez une invite de commandes avec des privilèges élevés et exécutez la commande suivante :<code>auditpol.exe /set /subcategory:"Application Generated" /failure:enable /success:enable.</code>
5. Fermez la **Stratégie de sécurité locale**et ouvrez le composant logiciel enfichable **Gestion d’Active Directory Federation Services** (dans le Gestionnaire de serveur, cliquez sur outils et sélectionnez gestion d’Active Directory Federation Services).
6. Dans le volet Actions, cliquez sur **Modifier les propriétés du Service de fédération**.
7. Dans la boîte de dialogue Propriétés de Service de fédération, cliquez sur l’onglet **événements** .
8. Activez les cases à cocher **des audits de réussite et d’échec des audits** et puis cliquez sur **OK**.






#### <a name="to-locate-the-ad-fs-audit-logs"></a>Pour localiser l’audit AD FS se connecte


1. Ouvrez **l’Observateur d’événements**.
2. Accédez aux journaux Windows et sélectionnez **sécurité**.
3. Sur la droite, cliquez sur **Filtrer les journaux en cours**.
4. Sous Source de l’événement, sélectionnez **Audit de AD FS**.

![AD FS des journaux d’audit](./media/active-directory-aadconnect-health-requirements/adfsaudit.png)

> [AZURE.WARNING] Si une stratégie de groupe qui désactive AD FS audit existe, l’Agent d’intégrité connexion AD Azure est alors impossible de collecter les informations. Assurez-vous que vous n’avez pas une stratégie de groupe qui peut être la désactivation de l’audit.

[//]: # (Start of Agent Proxy Configuration Section)

## <a name="installing-the-azure-ad-connect-health-agent-for-sync"></a>Installation de l’agent de la santé se connecter Azure AD pour la synchronisation
L’agent d’intégrité de connexion Azure d’Active Directory pour la synchronisation est installé automatiquement dans la dernière version de Azure Connect d’Active Directory. Pour utiliser Azure Connect d’Active Directory pour la synchronisation, vous devez télécharger la dernière version d’Azure Connect d’annonce et de l’installer. Vous pouvez télécharger la dernière version [ici](http://www.microsoft.com/download/details.aspx?id=47594).

Pour vérifier que l’agent a été installé, recherchez les services suivants sur le serveur. Si vous avez terminé la configuration, ils doivent déjà être en cours d’exécution. Dans le cas contraire, ils sont arrêtés jusqu'à ce que la configuration est terminée.

- AD Azure se connecter Service de perspectives sur la synchronisation de santé
- Annonce Azure connexion synchronisation de santé Service de surveillance

![Vérifier AD Azure se connecter la santé pour la synchronisation](./media/active-directory-aadconnect-health-sync/services.png)

> [AZURE.NOTE] N’oubliez pas que l’utilisation d’état connexion AD Azure requiert Azure AD Premium. Si vous n’avez pas d’Azure AD Premium, vous ne pouvez pas terminer la configuration du portail Azure. Pour plus d’informations, consultez la [page Configuration requise](active-directory-aadconnect-health-agent-install.md#requirements).


## <a name="manual-azure-ad-connect-health-for-sync-registration"></a>Manuel AD Azure se connecter de santé pour l’enregistrement de synchronisation
Si l’état connexion AD Azure pour l’inscription de l’agent de synchronisation échoue après avoir installé correctement Azure Connect d’Active Directory, vous pouvez utiliser la commande PowerShell suivante pour enregistrer manuellement l’agent.

>[AZURE.IMPORTANT] À l’aide de cette commande PowerShell est uniquement requis si l’inscription de l’agent échoue après l’installation d’Azure Connect d’Active Directory.

PowerShell suivant commande est nécessaire uniquement lorsque l’inscription de l’agent de santé échoue même après une installation réussie et de la configuration de Azure Connect d’Active Directory. Les services de santé de connexion AD Azure démarrera une fois que l’agent a été correctement inscrit.

Vous pouvez manuellement inscrire l’agent d’intégrité de connecter Azure d’Active Directory pour la synchronisation à l’aide de la commande PowerShell suivante :

`Register-AzureADConnectHealthSyncAgent -AttributeFiltering $false -StagingMode $false`

La commande accepte des paramètres à la suite :

- AttributeFiltering : $true (par défaut) - si Azure AD Connect est ne synchronise pas l’attribut par défaut définie et a été personnalisé pour utiliser un jeu d’attributs filtré. $false dans le cas contraire.
- StagingMode : $false (par défaut) - si le serveur Azure AD Connect n’est pas la zone de transit mode, $true si le serveur est configuré pour être en mode de mise en attente.

Lorsque vous y êtes invité pour l’authentification, vous devez utiliser le même compte d’administrateur global (tel que admin@domain.onmicrosoft.com) qui a été utilisé pour la configuration Azure Connect d’Active Directory.

## <a name="installing-the-azure-ad-connect-health-agent-for-ad-ds"></a>L’installation de la publicité Azure connexion Agent d’intégrité pour les services AD DS
Pour démarrer l’installation de l’agent, double-cliquez sur le fichier .exe que vous avez téléchargé. Dans le premier écran, cliquez sur Installer.

![Vérifier Active Directory Azure se connecter la santé](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install1.png)

Une fois l’installation terminée, cliquez sur Configurer maintenant.

![Vérifier Active Directory Azure se connecter la santé](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install2.png)

Une invite de commande est lancée, suivie par certains PowerShell qui exécute l’AzureADConnectHealthADDSAgent de la caisse enregistreuse. Lorsque vous êtes invité à vous connecter à Azure, continuez et ouvrez une session.

![Vérifier Active Directory Azure se connecter la santé](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install3.png)

Après l’ouverture de session, PowerShell va continuer. Une fois terminée, vous pouvez fermer PowerShell, et la configuration est terminée.

À ce stade, les services doivent être démarrés automatiquement permettant à l’agent surveiller et collecter des données. Si vous ne remplissez pas toutes les conditions préalables décrites dans les sections précédentes, les avertissements s’affichent dans la fenêtre PowerShell. Veillez à effectuer [Configuration requise](active-directory-aadconnect-health-agent-install.md#requirements) avant l’installation de l’agent. La capture d’écran suivante est un exemple de ces erreurs.

![Vérifier Active Directory Azure se connecter santé pour AD DS](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install4.png)

Pour vérifier que l’agent a été installé, recherchez les services suivants sur le contrôleur de domaine.

- Annonce Azure se connecter le Service de perspectives sur la santé, les services AD DS
- Azure AD DS de santé AD Service de surveillance de se connecter

Si vous avez terminé la configuration, ces services doivent déjà être en cours d’exécution. Dans le cas contraire, ils sont arrêtés jusqu'à ce que la configuration est terminée.

![Vérifier Active Directory Azure se connecter la santé](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install5.png)

## <a name="installing-the-azure-ad-connect-health-agent-for-ad-ds-on-server-core"></a>L’installation de la publicité Azure connexion Agent d’intégrité pour les services AD DS sur Server Core.
Après avoir installé le fichier .exe, vous pouvez exécuter le processus d’inscription à l’aide de la commande PowerShell suivante :

`Register-AzureADConnectHealthADDSAgent -Credential $cred`

## <a name="configure-azure-ad-connect-health-agents-to-use-http-proxy"></a>Configurer Azure AD connexion Agents d’intégrité pour utiliser le HTTP Proxy
Vous pouvez configurer Azure AD connexion Agents d’intégrité à utiliser un HTTP Proxy.

>[AZURE.NOTE]
- À l’aide de « Netsh WinHttp set ProxyServerAddress » n’est pas pris en charge que l’agent utilise System.Net pour effectuer des demandes web au lieu des Services HTTP Microsoft Windows.
- L’adresse du serveur Http Proxy configuré est utilisé pour des messages Https chiffrés pass-through.
- Proxy authentifié (en utilisant HTTPBasic) n’est pas pris en charge.

### <a name="change-health-agent-proxy-configuration"></a>Configuration de Proxy de l’Agent de santé modification
Vous disposez des options suivantes pour configurer Azure se connecter Health Agent AD pour utiliser un HTTP Proxy.

>[AZURE.NOTE]Tous les services Azure Agent de santé se connecter AD doivent être redémarrés afin que les paramètres de proxy d’être mis à jour. Exécutez la commande suivante :<br>
Redémarrage du Service AdHealth *

#### <a name="import-existing-proxy-settings"></a>Importer les paramètres de proxy existant

##### <a name="import-from-internet-explorer"></a>Importer à partir d’Internet Explorer
Les paramètres de proxy Internet Explorer HTTP peuvent être importés pour être utilisés par les Agents d’intégrité connexion AD Azure. Sur chacun des serveurs exécutant l’agent d’intégrité, exécutez la commande PowerShell suivante :

    Set-AzureAdConnectHealthProxySettings -ImportFromInternetSettings

##### <a name="import-from-winhttp"></a>Importation de WinHTTP
Paramètres de proxy WinHTTP peuvent être importés pour être utilisé par les Agents d’intégrité connexion AD Azure. Sur chacun des serveurs exécutant l’agent d’intégrité, exécutez la commande PowerShell suivante :

    Set-AzureAdConnectHealthProxySettings -ImportFromWinHttp

#### <a name="specify-proxy-addresses-manually"></a>Spécifiez manuellement les adresses Proxy
Vous pouvez spécifier manuellement un serveur proxy sur chacun des serveurs exécutant l’Agent d’intégrité, par l’exécution de la commande PowerShell suivante :

    Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress address:port

Exemple : *Set-AzureAdConnectHealthProxySettings - HttpsProxyAddress myproxyserver : 443*

- « adresse » peut être un nom de serveur résolu DNS ou une adresse IPv4
- « port » peut être omis. En cas d’omission puis 443 est choisi comme port par défaut.

#### <a name="clear-existing-proxy-configuration"></a>Effacer la configuration proxy existant
Vous pouvez effacer la configuration existante de proxy en exécutant la commande suivante :

    Set-AzureAdConnectHealthProxySettings -NoProxy


### <a name="read-current-proxy-settings"></a>Lire les paramètres de proxy en cours
Vous pouvez lire les paramètres de proxy actuellement configuré en exécutant la commande suivante :

    Get-AzureAdConnectHealthProxySettings


## <a name="test-connectivity-to-azure-ad-connect-health-service"></a>Tester la connectivité à Active Directory Azure se connecter le Service de santé
Il est possible que les problèmes pouvant survenir entraînant l’agent d’intégrité de se connecter de publicité Azure perte de connexion avec le service de santé se connecter Azure AD. Celles-ci incluent des problèmes de réseau, de problèmes d’autorisation ou de diverses autres raisons.

Si l’agent ne parvient pas à envoyer des données vers le service de santé de connexion AD Azure pendant plus de deux heures, il est indiqué par l’alerte suivante dans le portail : « les données de Service de santé ne sont pas à jour. » Vous pouvez vérifier si la publicité Azure affectée se connecter agent d’intégrité est en mesure de télécharger les données vers le service de santé se connecter Azure AD en exécutant la commande PowerShell suivante :

    Test-AzureADConnectHealthConnectivity -Role ADFS

Le paramètre de rôle prend les valeurs suivantes :

- ADFS
- Synchronisation
- AJOUTE

Vous pouvez utiliser l’indicateur - ShowResults dans la commande pour afficher les journaux détaillés. Utilisez l’exemple suivant :

    Test-AzureADConnectHealthConnectivity -Role Sync -ShowResult

>[AZURE.NOTE]Pour utiliser l’outil de connectivité, vous devez d’abord effectuer l’inscription de l’agent. Si vous n’êtes pas en mesure de terminer l’enregistrement de l’agent, assurez-vous que vous remplissez toutes les [conditions requises](active-directory-aadconnect-health-agent-install.md#requirements) pour la santé se connecter Azure AD. Ce test de connexion est effectué par défaut lors de l’inscription de l’agent.



## <a name="related-links"></a>Liens connexes

* [Annonce Azure se connecter à la santé](active-directory-aadconnect-health.md)
* [AD Azure se connecter à des opérations de santé](active-directory-aadconnect-health-operations.md)
* [À l’aide d’Active Directory Azure se connecter la santé avec AD FS](active-directory-aadconnect-health-adfs.md)
* [À l’aide de la santé se connecter Azure AD pour la synchronisation](active-directory-aadconnect-health-sync.md)
* [À l’aide d’Active Directory Azure se connecter la santé avec les services AD DS](active-directory-aadconnect-health-adds.md)
* [Annonce Azure se connecter le Forum aux questions sur la santé](active-directory-aadconnect-health-faq.md)
* [Annonce Azure se connecter à l’historique de Version de la santé](active-directory-aadconnect-health-version-history.md)
