<properties
   pageTitle="Azure Connect de publicité : Conditions préalables et matériel | Microsoft Azure"
   description="Cette rubrique décrit les conditions préalables et la configuration matérielle requise pour les connexion AD Azure"
   services="active-directory"
   documentationCenter=""
   authors="andkjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="10/12/2016"
   ms.author="billmath"/>

# <a name="prerequisites-for-azure-ad-connect"></a>Conditions préalables pour Active Directory Azure se connecter
Cette rubrique décrit les conditions préalables et la configuration matérielle requise pour les connexion AD Azure.

## <a name="before-you-install-azure-ad-connect"></a>Avant d’installer Azure Connect de publicité
Avant d’installer Azure Connect d’Active Directory, il y a quelques choses dont vous avez besoin.

### <a name="azure-ad"></a>Annonce Azure
- Un abonnement Azure ou une [version d’essai Azure](https://azure.microsoft.com/pricing/free-trial/). Cette opération n’est nécessaire pour l’accès au portail Azure et ne pas à l’aide d’Azure Connect d’Active Directory. Si vous utilisez PowerShell ou Office 365 est inutile un abonnement Azure à utiliser Azure Connect d’Active Directory. Si vous disposez d’une licence Office 365, vous pouvez également utiliser le portail Office 365. Avec une licence Office 365 payante vous pouvez également obtenir au portail Azure depuis le portail Office 365.
    - Vous pouvez également utiliser la fonctionnalité d’aperçu AD Azure dans [Azure portal](https://portal.azure.com). Ce portail ne requiert pas de licence Azure.
- [Ajouter et vérifiez le domaine](active-directory-add-domain.md) vous prévoyez d’utiliser dans Azure AD. Par exemple, si vous prévoyez d’utiliser contoso.com pour vos utilisateurs, puis assurez-vous que ce domaine a été vérifié et n’utilisez pas le domaine par défaut de contoso.onmicrosoft.com.
- Un locataire AD Azure permet en objets par défaut de 50 Ko. Lorsque vous vérifiez votre domaine, la limite est augmentée à 300 objets de k. Si vous avez besoin de davantage d’objets dans Active Directory Azure, vous devez ouvrir un cas de support pour que la limite a augmenté davantage. Si vous avez besoin de plus de 500 objets de k vous avez besoin d’une licence, comme Office 365, Azure AD base, Azure AD Premium ou Suite de mobilité d’entreprise.

### <a name="prepare-your-on-premises-data"></a>Préparez vos données sur site
- Passez en revue [les fonctionnalités de synchronisation facultatif que vous pouvez activer dans Azure AD](active-directory-aadconnectsyncservice-features.md) et évaluer les fonctionnalités, vous devez activer.

### <a name="on-premises-servers-and-environment"></a>Environnement et des serveurs sur site
- L’Active Directory schema version et la forêt niveau fonctionnel doit être Windows Server 2003 ou version ultérieure. Les contrôleurs de domaine peuvent exécuter n’importe quelle version dans la mesure où les exigences de niveaux de schéma et de la forêt sont remplies.
- Si vous envisagez d’utiliser la fonctionnalité d' **écriture différée de mot de passe** les contrôleurs de domaine doit être Windows Server 2008 (avec dernier SP) ou une version ultérieure. Si vos contrôleurs de domaine sont 2008 (pre-R2), puis vous devez également appliquer le [correctif KB2386717](http://support.microsoft.com/kb/2386717).
- Le contrôleur de domaine utilisé par AD Azure doit être accessible en écriture. Il n’est pas pris en charge pour utiliser un RODC (contrôleur de domaine en lecture seule) et Azure Connect d’annonce ne suit pas les redirections d’écriture.
- Azure Connect d’Active Directory ne peut pas être installé sur Small Business Server ou sur Windows Server Essentials. Le serveur doit utiliser Windows Server standard ou supérieures.
- Le serveur Azure Connect d’Active Directory doit avoir une interface utilisateur graphique complète installée. Il n’est pas pris en charge pour installer sur server core.
- Azure Connect d’Active Directory doit être installé sur Windows Server 2008 ou version ultérieure. Ce serveur peut être un contrôleur de domaine ou un serveur membre, si vous utilisez les paramètres express. Si vous utilisez des paramètres personnalisés, le serveur peut également être autonome et ne doit pas être joint à un domaine.
- Si vous installez Azure Connect d’Active Directory sur Windows Server 2008, veillez à appliquer les derniers correctifs à partir de Windows Update. L’installation n’est pas en mesure de démarrer avec un serveur non corrigée.
- Si vous envisagez d’utiliser la fonction **synchronisation de mot de passe**, le serveur Azure Connect de publicité doit être sur Windows Server 2008 R2 SP1 ou version ultérieure.
- Le serveur Azure Connect de publicité doit être [.NET Framework 4.5.1](#component-prerequisites) ou version ultérieure et [Microsoft PowerShell 3.0](#component-prerequisites) ou version ultérieure.
- Si les Services de fédération Active Directory est déployé, les serveurs où sont installés les AD FS ou Proxy de l’Application Web doivent être Windows Server 2012 R2 ou une version ultérieure. [Gestion à distance de Windows](#windows-remote-management) doit être activée sur ces serveurs d’installation à distance.
- Si les Services de fédération Active Directory est déployé, vous avez besoin de [Certificats SSL](#ssl-certificate-requirements).
- Si les Services de fédération Active Directory est déployé, vous devez configurer la [résolution de nom](#name-resolution-for-federation-servers).
- Azure Connect d’Active Directory nécessite une base de données SQL Server pour stocker les données d’identité. Un SQL Server 2012 Express LocalDB (une version simplifiée de SQL Server Express) est installé par défaut et le compte de service pour le service est créé sur l’ordinateur local. Express de SQL Server a une limite de 10 Go qui vous permet de gérer environ 100 000 objets. Si vous devez gérer un volume plus important d’objets d’annuaire, vous devez pointer l’Assistant d’installation à une autre installation de SQL Server.
- Si vous utilisez un SQL Server distinct, ces exigences s’appliquent :
    - Azure AD Connect prend en charge toutes les versions de Microsoft SQL Server d’à partir de SQL Server 2008 (SP4) pour SQL Server 2014. Base de données SQL de Microsoft Azure est **non pris en charge** sous la forme d’une base de données.
    - Vous devez utiliser un classement SQL sans respecter la casse. Celles-ci sont identifiées par une \_CI_ dans leur nom. Il est **non pris en charge** pour utiliser un classement qui respecte la casse, identifié par \_CS_ dans leur nom.
    - Vous ne pouvez avoir qu’un seul moteur de synchronisation par instance de base de données. Il est de la **non prise en charge** de partager l’instance de base de données avec Azure AD Sync, synchronisation d’annuaire ou la synchronisation FIM/MIM.

### <a name="accounts"></a>Comptes
- Un compte Azure AD Global administrateur de l’annuaire AD Azure que vous souhaitez intégrer. Ce doit être un **compte de l’établissement ou de l’organisation** et ne peut pas être un **compte Microsoft**.
- Si vous utilisez les paramètres express ou mise à niveau à partir de la synchronisation d’annuaire, puis vous devez avoir un compte d’administrateur d’entreprise pour votre Active Directory local.
- [Comptes dans Active Directory](./connect/active-directory-aadconnect-accounts-permissions.md) si vous utilisez le chemin d’installation des paramètres personnalisés.

### <a name="azure-ad-connect-server-configuration"></a>Configuration du serveur AD Connect Azure
- Si vos administrateurs globaux ont AMF activé, alors l' URL **https://secure.aadcdn.microsoftonline-p.com** doit être dans la liste des sites de confiance. Vous êtes invité à ajouter à la liste des sites de confiance s’il n’est pas ajouté avant que vous y êtes invité pour un défi de l’AMF. Vous pouvez utiliser Internet Explorer pour l’ajouter à vos sites de confiance.

### <a name="connectivity"></a>Connectivité
- Le serveur Azure Connect de publicité doit la résolution DNS pour l’intranet et internet. Le serveur DNS doit être en mesure de résoudre des noms à la fois sur site Active Directory ainsi que les points de terminaison AD Azure.
- Si vous avez des pare-feux sur votre Intranet, et vous devez ouvrir des ports entre les serveurs Azure Connect d’annonces et de vos contrôleurs de domaine, puis reportez-vous à la section [Ports de connexion AD Azure](active-directory-aadconnect-ports.md) pour plus d’informations.
- Si votre proxy ou pare-feu limite URL accessible, puis les URL documentées dans les [plages d’adresses IP et Office 365 URL](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) doit être ouvert.
    - Si vous utilisez le Cloud Microsoft en Allemagne ou en nuage de Microsoft Azure gouvernement, puis consultez [Azure Connect de AD sync service instances](active-directory-aadconnect-instances.md) pour les URL.
- La connexion AD Azure est par défaut à l’aide de TLS 1.0 pour communiquer avec Active Directory Azure. Vous pouvez le modifier pour TLS 1.2 en suivant les étapes décrites dans [Activer TLS 1.2 pour Azure Connect d’Active Directory](#enable-tls-12-for-azure-ad-connect).
- Si vous utilisez un serveur proxy sortant pour se connecter à Internet, le paramètre suivant dans le fichier **C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config** doit être ajouté pour l’Assistant installation et l’Azure Connect de AD sync pouvoir se connecter à Internet et Azure AD. Ce texte doit être entré au bas du fichier. Dans ce code, &lt;PROXYADRESS&gt; représente le proxy réel IP adresse ou nom d’hôte.

```
    <system.net>
        <defaultProxy>
            <proxy
            usesystemdefault="true"
            proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
            bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

- Si votre serveur proxy requiert une authentification, puis le [compte de service](./connect/active-directory-aadconnect-accounts-permissions.md#azure-ad-connect-sync-service-accounts) doit se trouver dans le domaine et vous devez utiliser le chemin d’installation de paramètres personnalisés pour spécifier un [compte de service personnalisé](./connect/active-directory-aadconnect-get-started-custom.md#install-required-components). Vous devez également une modification différente dans machine.config. Avec cette modification dans le fichier machine.config de l’Assistant d’installation et le moteur de synchronisation de répondent aux demandes d’authentification du serveur proxy. Dans l’Assistant installation de toutes les pages, à l’exclusion de la page de **configuration** , les informations d’identification de l’utilisateur sont utilisés. Sur la page **configurer** à la fin de l’Assistant d’installation, le contexte est basculé sur le [compte de service](./connect/active-directory-aadconnect-accounts-permissions.md#azure-ad-connect-sync-service-accounts) qui a été créée par vous. La section machine.config doit ressembler à ceci.

```
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy
            usesystemdefault="true"
            proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
            bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

Consultez MSDN pour plus d’informations sur l' [élément du proxy par défaut](https://msdn.microsoft.com/library/kd3cf2ex.aspx).

Si vous avez des problèmes de connectivité, consultez [résoudre les problèmes de connectivité](active-directory-aadconnect-troubleshoot-connectivity.md).

### <a name="other"></a>Autres
- Facultatif : Un compte d’utilisateur test pour vérifier la synchronisation.

## <a name="component-prerequisites"></a>Composants requis

### <a name="powershell-and-net-framework"></a>PowerShell et .net Framework
Azure Connect d’Active Directory dépend de Microsoft PowerShell et.NET Framework 4.5.1. Vous avez besoin de cette version ou une version ultérieure est installé sur votre serveur. En fonction de votre version de Windows Server, effectuez les opérations suivantes :

- Windows Server 2012R2
  - Microsoft PowerShell est installé par défaut, aucune action n’est requise.
  - .NET Framework 4.5.1 et versions ultérieures sont proposées par le biais de Windows Update. Assurez-vous que vous avez installé les dernières mises à jour pour Windows Server dans le panneau de configuration.
- Windows Server 2008 R2 et Windows Server 2012
  - La version la plus récente de Microsoft PowerShell est disponible dans **Windows Management Framework 4.0**, disponible sur le [Centre de téléchargement Microsoft](http://www.microsoft.com/downloads).
  - .NET Framework 4.5.1 et versions ultérieures sont disponibles sur le [Centre de téléchargement Microsoft](http://www.microsoft.com/downloads).
- Windows Server 2008
  - La version la plus récente prise en charge de PowerShell est disponible dans **Windows Management Framework 3.0**, disponible sur le [Centre de téléchargement Microsoft](http://www.microsoft.com/downloads).
 - .NET Framework 4.5.1 et versions ultérieures sont disponibles sur le [Centre de téléchargement Microsoft](http://www.microsoft.com/downloads).

### <a name="enable-tls-12-for-azure-ad-connect"></a>Activer TLS 1.2 pour AD Azure Connect
Azure Connect de publicité utilise TLS 1.0 par défaut pour chiffrer les communications entre le serveur de moteur de synchronisation et Azure AD. Vous pouvez changer cela en configurant des applications .net à utiliser TLS 1.2 par défaut sur le serveur. Vous trouverez plus d’informations sur TLS 1.2 dans [2960358 avis de sécurité Microsoft](https://technet.microsoft.com/security/advisory/2960358).

1. TLS 1.2 ne peut pas être activé sur Windows Server 2008. Vous avez besoin de Windows Server 2008 R2 ou une version ultérieure. Assurez-vous que vous avez le correctif .net 4.5.1 pour votre système d’exploitation, voir [2960358 avis de sécurité Microsoft](https://technet.microsoft.com/security/advisory/2960358). Vous pouvez avoir ce ou une version ultérieure est déjà installé sur votre serveur.
2. Si vous utilisez Windows Server 2008 R2, vérifiez que TLS 1.2 est activé. Sur les serveurs Windows Server 2012 et les versions ultérieures, TLS 1.2 doit déjà être activé.
```
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
```
3. Pour tous les systèmes d’exploitation, la valeur de cette clé de Registre et redémarrez le serveur.
```
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319
"SchUseStrongCrypto"=dword:00000001
```
4. Si vous souhaitez également activer TLS 1.2 entre le serveur de moteur de synchronisation et d’un SQL Server distant, vérifiez que vous avez les versions requises pour [TLS 1.2 prise en charge de Microsoft SQL Server](https://support.microsoft.com/kb/3135244).

## <a name="prerequisites-for-federation-installation-and-configuration"></a>Conditions préalables pour l’installation de la fédération et de la configuration

### <a name="windows-remote-management"></a>Gestion à distance de Windows
Lorsque vous utilisez Azure Connect d’Active Directory pour déployer Active Directory Federation Services ou le Proxy d’Application Web, vérifiez la configuration requise ci-dessous afin de garantir la réussiront de connectivité et configuration :

- Si le serveur cible est joint au domaine, assurez-vous que l’option Gestion à distance de Windows est activé
    - Dans une fenêtre de commande avec élévation de privilèges PSH, utilisez la commande`Enable-PSRemoting –force`
- Si le serveur cible est un ordinateur WAP non-domaine joint, il y a quelques exigences supplémentaires
    - Sur l’ordinateur cible (machine WAP) :
         - Garantir la winrm (gestion à distance de Windows / WS-Management) service est en cours d’exécution via le composant logiciel enfichable Services
         - Dans une fenêtre de commande avec élévation de privilèges PSH, utilisez la commande`Enable-PSRemoting –force`
    - Sur l’ordinateur sur lequel l’Assistant est en cours d’exécution (si l’ordinateur cible est non-approuvé ou joint un domaine) :
        - Dans une fenêtre de commande avec élévation de privilèges PSH, utilisez la commande`Set-Item WSMan:\localhost\Client\TrustedHosts –Value <DMZServerFQDN> -Force –Concatenate`
        - Dans le Gestionnaire de serveur :
             - Ajouter un hôte DMZ WAP à un pool de machines (Gestionnaire de serveur -> Gérer ->... d’ajouter des serveurs DNS onglet)
             - Onglet du Gestionnaire de serveur tous les serveurs : cliquez avec le bouton droit sur serveur WAP et cliquez sur Gérer sous..., entrez pas domaine local creds pour la machine WAP
             - Pour vérifier la connectivité PSH à distance, sous l’onglet serveurs de tout gestionnaire de serveur : cliquez avec le bouton droit sur serveur WAP et choisissez Windows PowerShell.  Une session PSH à distance doit s’ouvrir pour vous assurer des sessions de PowerShell distantes peuvent être établies.

### <a name="ssl-certificate-requirements"></a>Configuration requise des certificats SSL
**Important :** il est fortement recommandé d’utiliser le même certificat SSL sur tous les nœuds de votre batterie de serveurs AD FS ainsi que sur tous les serveurs de proxy d’Application Web.

- Le certificat doit être un X509 certificat.
- Vous pouvez utiliser un certificat signé automatiquement sur les serveurs de fédération dans un environnement de test. Toutefois, pour un environnement de production, nous vous recommandons de vous procurer le certificat d’une autorité de certification publique.
    - Si vous utilisez un certificat qui n’est pas publiquement approuvé, assurez-vous que le certificat installé sur chaque serveur de Proxy de l’Application Web est approuvé sur le serveur local et sur tous les serveurs de fédération
- L’identité du certificat doit correspondre au nom de service de fédération (par exemple, sts.contoso.com).
    - L’identité est une extension autre nom (SAN) de sujet de type NomDNS ou, s’il n’y a pas d’entrées SAN, le nom de l’objet spécifié en tant que nom commun.  
    - Plusieurs entrées de SAN peuvent être présentes dans le certificat, sous réserve d’un d’eux correspond au nom de service de fédération.
    - Si vous envisagez d’utiliser la jointure de l’espace de travail, un SAN supplémentaire est requis avec la valeur **enterpriseregistration.** suivi par le suffixe de nom d’utilisateur Principal (UPN) de votre organisation, par exemple, **enterpriseregistration.contoso.com**.
- Les certificats basés sur des clés de génération (CNG) suivant CryptoAPI et les fournisseurs de stockage de clés ne sont pas pris en charge. Cela signifie que vous devez utiliser un certificat basé sur un CSP (fournisseur de services cryptographiques) et non un KSP (fournisseur de stockage de la clé).
- Les certificats génériques sont pris en charge.

### <a name="name-resolution-for-federation-servers"></a>Résolution de noms pour les serveurs de fédération
- Configurer des enregistrements DNS pour le nom de service de fédération AD FS (par exemple, sts.contoso.com) pour l’intranet (votre serveur DNS interne) et de l’extranet (DNS public par le biais de votre registraire de domaine). Pour l’intranet DNS enregistrement vous assurer que vous utilisez A pas les enregistrements CNAME et les enregistrements. Ceci est nécessaire pour l’authentification windows fonctionne correctement à partir de votre ordinateur de joint de domaine.
- Si vous déployez plusieurs serveur AD FS ou serveur de Proxy de l’Application Web, vérifiez que vous avez configuré votre équilibreur de charge et que les enregistrements DNS pour le nom de service de fédération AD FS (par exemple sts.contoso.com) pointent vers l’équilibreur de charge.
- Pour l’authentification intégrée windows pour les applications de navigateur à l’aide d’Internet Explorer de votre intranet, vérifiez que le nom de service de fédération AD FS (par exemple, sts.contoso.com) est ajouté à la zone intranet dans Internet Explorer. Cela peut être contrôlé via la stratégie de groupe et déployée sur tous les ordinateurs de votre domaine joint.

## <a name="azure-ad-connect-supporting-components"></a>Azure Connect AD prise en charge des composants
Voici une liste de composants Azure Connect d’Active Directory installe sur le serveur où Azure Connect d’Active Directory est installé. Cette liste est pour une installation Express de base.  Si vous choisissez d’utiliser un autre de SQL Server sur la page services de synchronisation installer, SQL Express LocalDB n'est pas installée localement.

- Annonce Azure se connecter à la santé
- Assistant Microsoft Online Services-In pour les professionnels de l’informatique (installé mais aucune dépendance dessus)
- Utilitaires de ligne de commande Microsoft SQL Server 2012
- LocalDB Express de Microsoft SQL Server 2012
- Client natif de Microsoft SQL Server 2012
- Package de Redistribution 2013 Microsoft Visual C++

## <a name="hardware-requirements-for-azure-ad-connect"></a>Configuration matérielle requise pour Azure Connect de publicité
Le tableau ci-dessous présente la configuration minimale requise pour l’ordinateur de la synchronisation Azure Connect d’Active Directory.

| Nombre d’objets dans Active Directory | UNITÉ CENTRALE | Mémoire | Taille du disque dur |
| ------------------------------------- | --- | ------ | --------------- |
| Moins de 10 000 | 1,6 GHz | 4 GO | 70 GO |
| 10 000 – 50 000 | 1,6 GHz | 4 GO | 70 GO |
| 50 000 ou 100 000 | 1,6 GHz | 16 GO | 100 GO |
| Pour les objets de 100 000 ou plus, la version complète de SQL Server est requise|  |  |  |
| 100 000 et 300 000 | 1,6 GHz | 32 GO | 300 GO |
| 300 000 – 600 000 | 1,6 GHz | 32 GO | 450 GO |
| Plus de 600 000 | 1,6 GHz | 32 GO | 500 GO |

La configuration minimale requise pour les ordinateurs exécutant Federation Services ou des serveurs d’applications Web est le suivant :

- Processeur : Double coeur 1,6 GHz ou supérieur
- MÉMOIRE : 2 Go ou plus
- VM Azure : Configuration A2 ou supérieur

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur [l’intégration des identités avec Azure Active Directory local](active-directory-aadconnect.md).
