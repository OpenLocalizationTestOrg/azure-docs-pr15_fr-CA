<properties 
    pageTitle="Mise en route de l’authentification de certificat basée sur Android | Microsoft Azure" 
    description="Découvrez comment configurer l’authentification par certificat de solutions avec des appareils Android" 
    services="active-directory" 
    authors="markusvi"  
    documentationCenter="na" 
    manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="10/10/2016" 
    ms.author="markvi" />



# <a name="get-started-with-certificate-based-authentication-on-android---public-preview"></a>Mise en route de l’authentification de certificat basée sur Android - version d’évaluation  

> [AZURE.SELECTOR]
- [e/s](active-directory-certificate-based-authentication-ios.md)
- [Android](active-directory-certificate-based-authentication-android.md)


Cette rubrique vous indique comment configurer et utiliser l’authentification par certificat (CBA) sur un appareil Android pour les utilisateurs de clients dans Office 365, activité et l’entreprise, formation des plans. 

CBA vous permet d’être authentifiés par Azure Active Directory avec un certificat client sur un appareil Android ou d’e/s lors de la connexion de votre compte Exchange en ligne : 

- Applications mobiles Office tels que Microsoft Outlook et Microsoft Word   
- Clients Exchange ActiveSync (EAS) 

Configuration de cette fonctionnalité élimine le besoin d’entrer une combinaison nom d’utilisateur et le mot de passe dans certaines courrier et des applications de Microsoft Office sur votre appareil mobile. 
 

## <a name="supported-scenarios-and-requirements"></a>Exigences et scénarios pris en charge  



### <a name="general-requirements"></a>Exigences générales 


Pour tous les scénarios de cette rubrique, les tâches suivantes sont requises :  

- Accès au certificat ou les autorités d’émettre des certificats client.  

- L’ou les autorités certificats doivent être configurée dans Azure Active Directory. Vous trouverez la procédure détaillée effectuer la configuration dans la section [Mise en route](#getting-started) .  

- L’autorité de certification racine et les autorités de certification intermédiaires doivent être configurées dans Azure Active Directory.  

- Chaque autorité de certification doit avoir une liste de révocation de certificats (CRL) qui peut être référencée via une URL d’ouvert sur Internet.  

- Le certificat client doit être émis pour l’authentification du client.  


- Pour des clients Exchange ActiveSync, le certificat client doit avoir l’adresse de l’utilisateur e-mail routable dans Exchange en ligne dans le nom de l’entité de sécurité ou de la valeur de nom RFC822 du champ Subject Alternative Name. Azure Active Directory mappe la valeur RFC822 à l’attribut d’adresse Proxy dans le répertoire.  



### <a name="office-mobile-applications-support"></a>Prise en charge des applications mobiles Office 

| Applications                      | Prise en charge      |
| ---                       | ---          |
| Word / Excel / PowerPoint | ![À cocher][1]  |
| OneNote                   | À venir  |
| OneDrive                  | ![À cocher][1]  |
| Outlook                   | ![À cocher][1]  |
| Yammer                    | ![À cocher][1]  |
| Skype pour entreprise        | ![À cocher][1]  |


### <a name="requirements"></a>Configuration requise  

La version du système d’exploitation de périphérique doit être Android 5.0 (Lollipop) et les versions ultérieures. 

Un serveur de fédération doit être configuré.  


Pour Azure Active Directory révoquer un certificat client, le jeton ADFS doit avoir les déclarations suivantes :  

  - `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>`  
(Le numéro de série du certificat client) 

  - `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>`  
(La chaîne pour l’émetteur du certificat client) 

Azure Active Directory ajoute ces revendications dans le jeton d’actualisation s’ils sont disponibles dans le jeton d’ADFS (ou tout autre jeton SAML). Lorsque le jeton d’actualisation doit être validé, ces informations sont utilisées pour vérifier la révocation. 

Pour obtenir les meilleurs résultats, vous devez mettre à jour les pages d’erreur ADFS avec des instructions sur l’obtention d’un certificat d’utilisateur. 

Pour plus d’informations, consultez [Personnalisation de Pages AD FS Sign-in](https://technet.microsoft.com/library/dn280950.aspx).  



### <a name="exchange-activesync-clients-support"></a>Prise en charge des clients Exchange ActiveSync 


Certaines applications Exchange ActiveSync sur Android 5.0 (Lollipop) ou version ultérieure sont pris en charge. Pour déterminer si votre application de messagerie prend en charge cette fonctionnalité, contactez le développeur de votre application. 



## <a name="getting-started"></a>Mise en route 


Pour commencer, vous devez configurer les autorités de certification dans Azure Active Directory. Pour chaque autorité de certification, de télécharger les éléments suivants : 

- La partie publique du certificat, dans le format *.cer* 

- Internet face URL où se trouvent les listes révocation de certificats (CRL)
 

Voici le schéma pour une autorité de certification : 

    class TrustedCAsForPasswordlessAuth 
    { 
       CertificateAuthorityInformation[] certificateAuthorities;    
    } 

    class CertificateAuthorityInformation 

    { 
        CertAuthorityType authorityType; 
        X509Certificate trustedCertificate; 
        string crlDistributionPoint; 
        string deltaCrlDistributionPoint; 
        string trustedIssuer; 
        string trustedIssuerSKI; 
    }                

    enum CertAuthorityType 
    { 
        RootAuthority = 0, 
        IntermediateAuthority = 1 
    } 


Pour télécharger les informations, vous pouvez utiliser le module AD Azure via Windows PowerShell.  
Vous trouverez ci-dessous des exemples pour l’ajout, la suppression ou la modification d’une autorité de certification. 



### <a name="configuring-your-azure-ad-tenant-for-certificate-based-authentication"></a>Configuration de vos clients AD Azure pour le certificat de l’authentification basée sur 

1. Démarrez Windows PowerShell avec des droits d’administrateur. 

2. Installez le module AD Azure. Vous devez installer la Version [1.1.143.0](http://www.powershellgallery.com/packages/AzureADPreview/1.1.143.0) ou ultérieure.  

        Install-Module -Name AzureADPreview –RequiredVersion 1.1.143.0 

3. Se connecter à votre client cible : 

        Connect-AzureAD 

### <a name="adding-a-new-certificate-authority"></a>Ajout d’une nouvelle autorité de certificat

1. Définir les différentes propriétés de l’autorité de certification et l’ajouter à Active Directory de Azure : 

        $cert=Get-Content -Encoding byte "[LOCATION OF THE CER FILE]" 
        $new_ca=New-Object -TypeName Microsoft.Open.AzureAD.Model.CertificateAuthorityInformation 
        $new_ca.AuthorityType=0 
        $new_ca.TrustedCertificate=$cert 
        New-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $new_ca 

5. Obtenir les autorités de certification : 

        Get-AzureADTrustedCertificateAuthority 


### <a name="retrieving-the-list-certificate-authorities"></a>Récupérer les autorités de certification de la liste

Récupérer les autorités de certification actuellement stockées dans Azure Active Directory pour vos clients : 

        Get-AzureADTrustedCertificateAuthority 


### <a name="removing-a-certificate-authority"></a>Suppression d’une autorité de certification

1.  Récupérer les autorités de certification : 

        $c=Get-AzureADTrustedCertificateAuthority 


2. Supprimer le certificat de l’autorité de certification : 

        Remove-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[2] 


### <a name="modfiying-a-certificate-authority"></a>Modfiying d’une autorité de certification 

1.  Récupérer les autorités de certification : 

        $c=Get-AzureADTrustedCertificateAuthority 


2. Modifier les propriétés de l’autorité de certification : 

        $c[0].AuthorityType=1 

3. Configurer l' **autorité de certification**: 

        Set-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[0] 




## <a name="testing-office-mobile-applications"></a>Test d’applications Office mobile  

Pour tester l’authentification de certificats dans votre application Office mobile : 

1.  Sur votre périphérique de test, installez une application mobile de Office (par exemple, OneDrive) à partir du magasin de lecture de Google.

2.  Vérifiez que le certificat d’utilisateur a été configuré pour votre périphérique de test. 

3.  Lancer l’application. 

4.  Entrez votre nom d’utilisateur et choisissez le certificat d’utilisateur que vous souhaitez utiliser. 

Vous devez être connecté. 





## <a name="testing-exchange-activesync-client-applications"></a>Test des applications de client Exchange ActiveSync

Pour accéder à Exchange ActiveSync via l’authentification par certificat, un profil EAS contenant le certificat client doit être disponible pour l’application. Le profil d’EAS doit contenir les informations suivantes :

- Le certificat d’utilisateur à utiliser pour l’authentification 

- Le point de terminaison EAS doit être outlook.office365.com (cette fonctionnalité est actuellement pris en charge uniquement dans l’environnement de plusieurs utilisateurs Exchange en ligne)

Un profil d’EAS peut être configuré et mis sur le périphérique par le biais de l’utilisation d’un MDM Intune ou par manuellement en plaçant le certificat dans le profil d’EAS sur le périphérique.  


### <a name="testing-eas-client-applications-on-android"></a>Tester les applications client EAS sur Android 

Pour tester l’authentification de certificat avec une application d’Android 5.0 (Lollipop) ou une version ultérieure, effectuez les opérations suivantes :  

1. Configuration d’un profil d’EAS dans l’application répondant aux critères ci-dessus.  


2. Une fois que le profil est configuré correctement, ouvrez l’application et vérifiez que courrier est en cours de synchronisation. 




## <a name="revocation"></a>Révocation

Pour révoquer un certificat client, Azure Active Directory lit la liste de révocation de certificats (CRL) à partir des URL téléchargés dans le cadre des informations d’autorité de certificat et met en cache. Publication de la dernière estampille (**Date d’effet** de propriété) dans la liste de révocation permet de vérifier la liste de révocation est toujours valide. La liste de révocation est régulièrement référencé pour révoquer l’accès aux certificats qui font partie de la liste.

Si une révocation plus instantanée est requise (par exemple, si un utilisateur perd un périphérique), le jeton d’autorisation de l’utilisateur peut être invalidé. Pour invalider le jeton d’autorisation, la valeur du champ **StsRefreshTokenValidFrom** pour cet utilisateur particulier à l’aide de Windows PowerShell. Vous devez mettre à jour le champ **StsRefreshTokenValidFrom** pour chaque utilisateur que vous souhaitez révoquer l’accès pour.
 
Pour garantir que la révocation persiste, vous devez définir la **Date d’effet** de la liste CRL à une date, une fois la valeur définie par **StsRefreshTokenValidFrom** et assurez-vous que le certificat en question est dans la liste de révocation.
 
Les étapes suivantes décrivent le processus de mise à jour et d’invalider le jeton d’autorisation en définissant le champ **StsRefreshTokenValidFrom** . 

1. Se connecter avec les informations d’identification d’administration pour le service MSOL : 

        $msolcred = get-credential 
        connect-msolservice -credential $msolcred 

1.  Récupérer la valeur actuelle de la StsRefreshTokensValidFrom d’un utilisateur : 

        $user = Get-MsolUser -UserPrincipalName test@yourdomain.com` 
        $user.StsRefreshTokensValidFrom 


1.  Configurer une nouvelle valeur StsRefreshTokensValidFrom pour l’utilisateur égale à l’estampille courante : 

        Set-MsolUser -UserPrincipalName test@yourdomain.com -StsRefreshTokensValidFrom ("03/05/2016")


La date que vous indiquez doit être dans le futur. Si la date n’est pas à l’avenir, la propriété **StsRefreshTokensValidFrom** n’est pas définie. Si la date est dans le futur, **StsRefreshTokensValidFrom** est définie à l’heure actuelle (pas la date indiquée par commande de jeu-MsolUser). 


<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-android/ic195031.png