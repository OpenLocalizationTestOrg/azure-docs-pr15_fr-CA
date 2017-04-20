<properties
    pageTitle="Conseils de renouvellement pour les utilisateurs d’Office 365 et Azure Active Directory de certificats | Microsoft Azure"
    description="Cet article explique aux utilisateurs d’Office 365, comment faire pour résoudre les problèmes avec les e-mails concernant le renouvellement d’un certificat."
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
    ms.date="08/08/2016"
    ms.author="billmath"/>


# <a name="renew-federation-certificates-for-office-365-and-azure-active-directory"></a>Renouveler des certificats de la fédération pour Office 365 et Azure Active Directory

##<a name="overview"></a>Vue d’ensemble

Pour la fédération réussie entre Azure Active Directory (AD Azure) et les Services de fédération Active Directory (Active Directory Federation Services), les certificats utilisés par ADFS pour signer les jetons de sécurité à Active Directory Azure doivent correspondre à ce qui est configuré dans Azure AD. Toute incompatibilité peut conduire à une approbation rompue. Annonce Azure permet de s’assurer que ces informations soient synchronisées lorsque vous déployez AD FS et des Proxy de l’Application Web (pour l’accès extranet).

Cet article vous fournit des informations supplémentaires pour gérer vos certificats de signature de jeton et les maintenir synchronisées avec AD Azure, dans les cas suivants :

* Vous ne déployez pas le Proxy d’Application Web, et les métadonnées de fédération ne sont donc pas disponible dans l’extranet.
* Vous n’utilisez pas la configuration par défaut d’ADFS pour les certificats de signature de jetons.
* Vous utilisez un fournisseur d’identité du tiers.

## <a name="default-configuration-of-ad-fs-for-token-signing-certificates"></a>Configuration par défaut d’ADFS pour les certificats de signature de jetons

La signature des jetons et du jeton de décryptage des certificats sont généralement les certificats auto-signés et sont valides pendant un an. Par défaut, AD FS comprend un processus de renouvellement automatique appelé **AutoCertificateRollover**. Si vous utilisez AD FS 2.0 ou version ultérieure, Office 365 et AD Azure automatiquement mettre à jour votre certificat avant son expiration.

### <a name="renewal-notification-from-the-office-365-portal-or-an-email"></a>Notification de renouvellement depuis le portail Office 365 ou un message électronique

>[AZURE.NOTE] Si vous avez reçu un e-mail ou une notification de portail vous invitant à renouveler votre certificat pour Office, consultez [Gestion des modifications au certificats de signature de jeton](#managecerts) pour vérifier si vous avez besoin exécuter toute action. Microsoft a connaissance d’un problème qui peut entraîner des notifications de renouvellement de certificat envoyée, même si aucune action n’est requise.

AD Azure tente d’analyser les métadonnées de fédération et mettre à jour des certificats de signature comme indiqué par les métadonnées de ce jeton. 30 jours avant la date d’expiration du jeton de signature de certificats, de publicité Azure vérifie si les nouveaux certificats sont disponibles en interrogeant les métadonnées de fédération.

* Si elle peut interroger les métadonnées de fédération et récupérer les nouveaux certificats correctement, aucune notification par courrier électronique ou un avertissement dans le portail Office 365 n’est délivré à l’utilisateur.
* Si elle ne peut pas extraire le jeton de nouveaux certificats de signature, soit parce que les métadonnées de fédération ne sont pas accessible ou substitution de certificat automatique n’est pas activée, AD Azure émet une notification par e-mail et un message d’avertissement dans le portail Office 365.

![Notification de portail Office 365](./media/active-directory-aadconnect-o365-certs/notification.png)

>[AZURE.IMPORTANT] Si vous utilisez AD FS, afin de garantir la continuité d’activité, vérifiez que vos serveurs disposent des mises à jour suivantes afin que les échecs d’authentification pour les problèmes ne se produisent pas. Cela permet d’atténuer les problèmes de serveur de proxy connus AD FS pour ce renouvellement et les périodes de renouvellement futures :
>
>Server 2012 R2 - [Windows Server correctif cumulatif de mai 2014](http://support.microsoft.com/kb/2955164)
>
>Server 2008 R2 et 2012 - [Échec de l’authentification par proxy dans Windows 2008 R2 SP1 ou dans Windows Server 2012](http://support.microsoft.com/kb/3094446)

## Vérifier si les certificats doivent être mis à jour<a name="managecerts"></a>

### <a name="step-1-check-the-autocertificaterollover-state"></a>Étape 1 : Vérifier l’état de AutoCertificateRollover

Sur votre serveur ADFS, ouvrez PowerShell. Vérifiez que la valeur de AutoCertificateRollover est définie sur True.

    Get-Adfsproperties

![AutoCertificateRollover](./media/active-directory-aadconnect-o365-certs/autocertrollover.png)

[AZURE.NOTE] Si vous utilisez AD FS 2.0, exécutez d’abord Microsoft.Adfs.Powershell de Add-Pssnapin.

### <a name="step-2-confirm-that-ad-fs-and-azure-ad-are-in-sync"></a>Étape 2 : Confirmez que AD FS et AD Azure sont synchronisés

Sur votre serveur ADFS, ouvrez l’invite de PowerShell de publicité Azure et de se connecter à Active Directory Azure.

>[AZURE.NOTE] Vous pouvez télécharger PowerShell de publicité Azure [ici](https://technet.microsoft.com/library/jj151815.aspx).

    Connect-MsolService

Vérifiez les certificats configurés dans AD FS et Azure AD confiance des propriétés pour le domaine spécifié.

    Get-MsolFederationProperty -DomainName <domain.name> | FL Source, TokenSigningCertificate

![Get-MsolFederationProperty](./media/active-directory-aadconnect-o365-certs/certsync.png)

Si les empreintes dans les deux sorties correspondent, vos certificats sont synchronisées avec Active Directory Azure.

### <a name="step-3-check-if-your-certificate-is-about-to-expire"></a>Étape 3 : Vérifiez si votre certificat est sur le point d’expirer

Dans la sortie de Get-MsolFederationProperty ou Get-AdfsCertificate, recherchez la date sous « Pas après ». Si la date est inférieure à 30 jours, vous devez prendre la mesure.

| AutoCertificateRollover | Certificats de synchronisation avec Active Directory Azure | Métadonnées de fédération sont accessible au public | Validité | Action |
|:-----------------------:|:-----------------------:|:-----------------------:|:-----------------------:|:-----------------------:|
| Oui | Oui | Oui | - | Aucune action n’est nécessaire. Voir [jeton de renouveler automatiquement le certificat de signature](#autorenew). |
| Oui | N°  | - | Inférieur à 15 jours | Renouvelez immédiatement. Voir [jeton renouveler manuellement le certificat de signature](#manualrenew). |
| N° | - | - | Moins de 30 jours | Renouvelez immédiatement. Voir [jeton renouveler manuellement le certificat de signature](#manualrenew). |

\[-] N’a pas d’importance

## Renouveler le jeton de signature de certificat automatiquement (recommandé)<a name="autorenew"></a>

Vous n’avez pas besoin d’effectuer les étapes manuelles si les deux opérations suivantes sont remplies :
- Vous avez déployé le Proxy d’Application Web, qui permet d’accéder aux métadonnées de fédération à partir de l’extranet.
- Vous utilisez la configuration par défaut AD FS (AutoCertificateRollover est activé).

Vérifier les informations suivantes pour confirmer que le certificat peut être automatiquement mis à jour.

**1. la propriété AD FS AutoCertificateRollover doit être définie sur True.** Cela indique que AD FS génère automatiquement nouvelle signature des jetons et des certificats de décryptage de jeton, avant l’ancien celles expirent.

**2. les métadonnées de fédération AD FS sont accessible au public.** Vérifiez que vos métadonnées de fédération sont accessibles au public en accédant à l’URL suivante à partir d’un ordinateur sur le réseau internet public (sur le réseau d’entreprise) :


https:// (your_FS_name) /federationmetadata/2007-06/federationmetadata.xml

où `(your_FS_name) `est remplacé par le nom d’hôte de service federation votre organisation, par exemple fs.contoso.com.  Si vous êtes en mesure de vérifier les deux de ces paramètres correctement, ne pas faire autre chose.  

Exemple : https://fs.contoso.com/federationmetadata/2007-06/federationmetadata.xml

## Renouveler le jeton manuellement le certificat de signature<a name="manualrenew"></a>

Vous pouvez choisir de renouveler le jeton manuellement des certificats de signature. Par exemple, les scénarios suivants peuvent fonctionner mieux pour le renouvellement manuel :
* Jetons de certificats de signature ne sont pas des certificats auto-signés. La raison la plus courante pour cela est que votre organisation gère les certificats AD FS inscrites à partir d’une autorité de certification d’organisation.
* Sécurité réseau n’autorise pas les métadonnées de fédération à la disposition du public.

Dans ces scénarios, chaque fois que vous mettez à jour le jeton de signature de certificats, vous devez également mettre à jour votre domaine à Office 365 à l’aide de la commande PowerShell, Update-MsolFederatedDomain.

### <a name="step-1-ensure-that-ad-fs-has-new-token-signing-certificates"></a>Étape 1 : Assurez-vous que AD FS possède des certificats de signature de jeton de nouveau

**Non défini par défaut**

Si vous utilisez une configuration par défaut de AD FS (où **AutoCertificateRollover** est la valeur **False**), vous utilisez probablement les certificats personnalisés (ne pas signés lui-même). Pour plus d’informations sur la manière de renouveler les certificats de signature de jetons de AD FS, voir [conseils pour les clients qui n’utilisent ne pas AD FS des certificats auto-signés](https://msdn.microsoft.com/library/azure/JJ933264.aspx#BKMK_NotADFSCert).

**Métadonnées de fédération ne sont pas accessible au public**

D’autre part, si **AutoCertificateRollover** est défini sur **True**, mais vos métadonnées de fédération ne sont pas accessible publiquement, tout d’abord vous assurer que les nouveaux certificats de signature de jetons ont été générées par AD FS. Vérifiez que vous avez jeton de nouveaux certificats de signature en procédant aux étapes suivantes :

1. Vérifiez que vous êtes connecté au serveur primaire AD FS.
2. Vérifiez les certificats de signature actuels dans AD FS en ouvrant une fenêtre de commande PowerShell et en exécutant la commande suivante :

    \>signature de jeton Get-ADFSCertificate – CertificateType

    >[AZURE.NOTE] Si vous utilisez Active Directory Federation Services 2.0, vous devez exécuter tout d’abord les Add-Pssnapin Microsoft.Adfs.Powershell.

3. Examinez la sortie de commande à des certificats répertoriés. Si AD FS a généré un nouveau certificat, vous devriez voir deux certificats dans la sortie : une pour lequel la valeur de **IsPrimary** est **True** et que la date du **NotAfter** est dans les 5 jours, et l’autre pour lesquels **IsPrimary** est **False** et **NotAfter** sur une année à l’avenir.

4. Si vous ne voyez qu’un seul certificat, et la date du **NotAfter** est dans les 5 jours, vous devez générer un nouveau certificat.

5. Pour générer un nouveau certificat, exécutez la commande suivante à une invite de commande PowerShell : `PS C:\>Update-ADFSCertificate –CertificateType token-signing`.

6. Vérifier la mise à jour en exécutant la commande suivante à nouveau : PS C:\>signature de jeton Get-ADFSCertificate – CertificateType

Deux certificats doivent être répertoriés en maintenant une ayant une date de **NotAfter** d’environ un an dans le futur et pour lesquels la valeur de **IsPrimary** est **False**.

### <a name="step-2-update-the-new-token-signing-certificates-for-the-office-365-trust"></a>Étape 2 : Mettre à jour le nouveau jeton de signature de certificats pour l’approbation d’Office 365

Mise à jour d’Office 365 avec le nouveau jeton de signature de certificats à utiliser pour l’approbation, comme suit.

1.  Ouvrez le Module de Active Directory de Microsoft Azure pour Windows PowerShell.
2.  Exécutez $cred = Get-Credential. Lorsque cette applet de commande vous demande des informations d’identification, tapez vos informations d’identification administrateur compte de cloud service.
3.  Exécutez Connect-MsolService – $cred des informations d’identification. Cette applet de commande vous connecte au service cloud. Création d’un contexte qui vous connecte au service cloud est nécessaire avant d’exécuter des applets de commande supplémentaire installé par l’outil.
4.  Si vous exécutez ces commandes sur un ordinateur qui n’est pas le serveur principal de fédération AD FS, exécutez Set-MSOLAdfscontext-ordinateur <AD FS primary server>, où <AD FS primary server> est le nom de domaine complet interne du serveur ADFS principal. Cette applet de commande crée un contexte qui vous connecte à AD FS.
5.  Exécutez Update-MSOLFederatedDomain-DomainName <domain>. Cette applet de commande met à jour les paramètres à partir d’AD FS dans le service cloud et configure la relation d’approbation entre les deux.


>[AZURE.NOTE] Si vous avez besoin prendre en charge de plusieurs domaines de niveau supérieur, tels que les forêts contoso.com et fabrikam.com, vous devez utiliser le commutateur de **SupportMultipleDomain** avec les applets de commande. Pour plus d’informations, consultez [prise en charge de plusieurs domaines de premier niveau](active-directory-aadconnect-multiple-domains.md).

## Réparer d’approbation AD Azure à l’aide de Azure Connect de publicité<a name="connectrenew"></a>

Si vous avez configuré votre batterie de serveurs AD FS et l’approbation Azure AD à l’aide d’Azure Connect d’Active Directory, vous pouvez utiliser Azure Connect d’Active Directory pour détecter si vous devez intervenir pour vos certificats de signature de jetons. Si vous avez besoin renouveler les certificats, vous pouvez utiliser Azure Connect de publicité au pour faire.

Pour plus d’informations, consultez [réparation de l’approbation](./active-directory-aadconnect-federation-management.md#repairing-the-trust).
