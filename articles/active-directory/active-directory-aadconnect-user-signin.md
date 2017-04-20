<properties
    pageTitle="Annonce Azure se connecter : L’utilisateur se connecter | Microsoft Azure"
    description="Azure AD connecter connexion utilisateur pour les paramètres personnalisés."
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
    ms.date="10/16/2016"
    ms.author="billmath"/>



# <a name="azure-ad-connect-user-sign-on-options"></a>Azure AD se connecter connexion utilisateur sur les options

Azure Connect d’Active Directory permet aux utilisateurs de se connecter aux ressources de cloud et sur site à l’aide des mêmes mots de passe. Cet article décrit des concepts clés pour chaque modèle d’identité pour vous aider à choisir l’identité que vous souhaitez utiliser pour votre connexion à Active Directory Azure.

Si vous êtes familiarisé avec le modèle d’identité Azure AD et que vous souhaitez en savoir plus sur une méthode spécifique, cliquez ci-dessous sur rubrique appropriée.

* [Synchronisation de mot de passe](#password-synchronization)
* [SSO fédéré (avec ADFS)](#federation-using-a-new-or-existing-ad-fs-in-windows-server-2012-r2-farm)


## <a name="choosing-a-user-sign-in-method"></a>Choix d’une méthode de connexion utilisateur
Pour la plupart des organisations qui veulent juste activer utilisateur se connecter à Office 365, les applications SaaS et autres annonces Azure en fonction des ressources, l’option de synchronisation de mot de passe par défaut est recommandée.
Certaines organisations, toutefois, ont des raisons particulières pour l’utilisation du signe fédéré option tel que AD FS.  Celles-ci comprennent :

- Votre organisation possède déjà AD FS ou un 3ème partie fournisseur de fédération déployé
- Votre stratégie de sécurité empêche la synchronisation des hachages de mot de passe pour le nuage
- Vous avez besoin que les utilisateurs connaissent l’authentification transparente (sans invite de mot de passe supplémentaire) lors de l’accès aux ressources de cloud de domaine rejoint les ordinateurs sur le réseau d’entreprise
- Vous avez besoin de certaines capacités spécifiques QU'AD FS a
    - Authentification à plusieurs facteurs sur site à l’aide d’un fournisseur tiers ou des cartes à puce (en savoir plus sur les fournisseurs tiers AMF pour AD FS dans Windows Server 2012 R2)
    - Fonctionnalités d’intégration Active Directory telles que le verrouillage de compte paramétré ou mot de passe et travail heures une stratégie AD
    - Accès conditionnel à la fois locaux et cloud de ressources à l’aide de l’enregistrement de dispositifs, Azure AD join ou les stratégies Intune MDM

### <a name="password-synchronization"></a>Synchronisation de mot de passe
Avec la synchronisation de mot de passe, les hachages de mots de passe utilisateur sont synchronisés à partir d’Active Directory sur site vers Active Directory Azure.  Lorsque les mots de passe sont modifiés ou réinitialiser dans les locaux, les nouveaux mots de passe sont synchronisés immédiatement à AD Azure afin que vos utilisateurs puissent utiliser toujours le même mot de passe pour les ressources de cloud comme ils le font sur site.  Les mots de passe ne sont jamais envoyées à Active Directory Azure ni stockées dans AD Azure en texte clair.
Synchronisation de mot de passe peut servir avec le mot de passe différée pour activer self service le mot de passe réinitialisé dans Azure AD.

<center>![Nuage](./media/active-directory-aadconnect-user-signin/passwordhash.png)</center>

[Plus d’informations sur la synchronisation de mot de passe](active-directory-aadconnectsync-implement-password-synchronization.md)


### <a name="federation-using-a-new-or-existing-ad-fs-in-windows-server-2012-r2-farm"></a>Fédération à l’aide d’une nouvelle ou existante AD FS dans la batterie de serveurs Windows Server 2012 R2
Avec authentification fédérée, vos utilisateurs peuvent d’authentification aux services Azure AD sur leurs mots de passe locaux et, tandis que sur le réseau d’entreprise, sans avoir à entrer leur mot de passe à nouveau.  La fédération avec AD FS vous permet de déployer une nouvelle ou spécifier une Federation Services existant dans la batterie de serveurs Windows Server 2012 R2.  Si vous choisissez de spécifier d’une batterie de serveurs existante, Azure Connect de publicité configurera l’approbation entre votre batterie de serveurs et la publicité Azure afin que vos utilisateurs peuvent ouvrir une session.

<center>![Nuage](./media/active-directory-aadconnect-user-signin/federatedsignin.png)</center>

#### <a name="to-deploy-federation-with-ad-fs-in-windows-server-2012-r2-you-will-need-the-following"></a>Pour déployer la fédération avec AD FS dans Windows Server 2012 R2, vous devez le suivant

Si vous déployez une nouvelle batterie de serveurs :

- Un serveur Windows Server 2012 R2 pour le serveur de fédération.
- Un serveur Windows Server 2012 R2 pour le Proxy d’Application Web.
- un fichier .pfx avec un certificat SSL pour le nom de votre service de fédération prévue, par exemple fs.contoso.com.

Si vous déployez une nouvelle batterie de serveurs ou à l’aide d’une batterie de serveurs existante :

- Les informations d’identification d’administrateur local sur les serveurs de votre fédération.
- Informations d’identification d’administrateur local sur n’importe quel groupe de travail (non joint à un domaine) serveurs sur lequel vous avez l’intention de déployer le rôle de Proxy de l’Application Web.
- L’ordinateur sur lequel vous exécutez l’Assistant doit être en mesure de se connecter à tous les autres ordinateurs sur lequel vous souhaitez installer AD FS ou Proxy de l’Application Web via la gestion à distance de Windows.

[Configuration de l’authentification unique avec ADFS](./aad-connect/active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs) 

#### <a name="sign-on-using-an-earlier-version-of-ad-fs-or-a-third-party-solution"></a>Ouverture de session à l’aide d’une version antérieure de AD FS ou un tiers de solution

Si vous avez déjà configuré le nuage signe sur l’utilisation d’une version antérieure de Federation Services (tel que AD FS 2.0) ou d’un fournisseur de fédération de tierce partie, vous pouvez choisir d’ignorer le configuration de connexion utilisateur via les connexion AD Azure.  Cela vous permettra d’obtenir la dernière synchronisation et autres fonctionnalités d’Azure Connect de publicité tout en utilisant votre solution existante pour signe sur.

[Liste de compatibilité de tiers fédération AD Azure](./active-directory-aadconnect-federation-compatibility.md)

## <a name="user-sign-in-and-user-principal-name-upn"></a>Connexion de l’utilisateur et nom d’utilisateur principal (UPN)

### <a name="understanding-user-principal-name"></a>Nom d’utilisateur principal présentation

Dans Active Directory, le suffixe UPN par défaut est le nom DNS du domaine dans lequel le compte d’utilisateur créé. Dans la plupart des cas, c’est le nom de domaine enregistré comme domaine de l’entreprise sur Internet. Toutefois, vous pouvez ajouter des suffixes UPN plus utilisant les approbations et les domaines Active Directory.

Le nom UPN de l’utilisateur est au format username@domai. Par exemple, un serveur active directory contoso.com utilisateur Jean peut être UPN john@contoso.com. Le nom UPN de l’utilisateur est basé sur RFC 822. Bien que nom UPN et messagerie partagent le même format, la valeur de nom UPN pour un utilisateur peut ou ne peut pas être égale à l’adresse e-mail de l’utilisateur.

### <a name="user-principal-name-in-azure-ad"></a>Nom principal d’utilisateur dans Active Directory Azure

Assistant de connexion de publicité Azure utiliser l’attribut userPrincipalName ou vous permettent de spécifier l’attribut (installation personnalisée) pour être utilisé en local comme nom principal d’utilisateur dans Active Directory Azure. Il s’agit de la valeur qui sera utilisée pour la connexion à Active Directory Azure. Si la valeur de l’attribut de nom principal d’utilisateur ne correspond pas à un domaine vérifié dans AD Azure, puis AD Azure remplacera avec une valeur par défaut. onmicrosoft.com valeur.

Chaque répertoire dans Azure Active Directory est livré avec un nom de domaine intégré dans le formulaire contoso.onmicrosoft.com qui vous permet de commencer à utiliser Azure ou autres services de Microsoft. Vous pouvez améliorer et simplifier l’expérience de connexion à l’aide de domaines personnalisés. Pour plus d’informations sur le domaine personnalisé noms dans AD Azure et comment vérifier un domaine, consultez [Ajouter votre nom de domaine personnalisé pour Azure Active Directory](active-directory-add-domain.md#add-your-custom-domain-name-to-azure-active-directory)

## <a name="azure-ad-sign-in-configuration"></a>Configuration d’authentification AD Azure

### <a name="azure-ad-sign-in-configuration-with-azure-ad-connect"></a>Azure AD-in configuration avec Azure Connect de publicité
Expérience de connexion AD Azure dépend de si AD Azure est en mesure de faire correspondre le suffixe de nom principal d’utilisateur d’un utilisateur en cours de synchronisation à l’un des domaines personnalisés vérifiés dans l’annuaire AD Azure. Azure Connect d’Active Directory fournit de l’aide lors de la configuration des paramètres de connexion AD Azure, afin que l’expérience utilisateur de connexion dans le nuage est semblable à l’expérience sur site. Azure AD Connect répertorie les suffixes UPN définis pour les domaines et essaie de les mettre en correspondance avec un domaine personnalisé dans Azure AD et vous aide à utiliser l’action appropriée à prendre.
La page de connexion AD Azure répertorie les les suffixes UPN définis pour Active Directory sur site et affiche l’état correspondant à chaque suffixe. Les valeurs d’état peuvent être un de le ci-dessous :

| État | Description | Action nécessaire |
|:----|:----|:----|
|Vérifié| Azure Connect d’AD a trouvé qu'une correspondance vérifiée de domaine dans Active Directory Azure. Tous les utilisateurs de ce domaine peuvent signer à l’aide de leurs informations d’identification sur site| Aucune action n’est nécessaire. |
|Non vérifié| Azure AD Connect a trouvé un domaine personnalisé correspondant dans Active Directory Azure, mais il n’est pas vérifiée. Suffixe de nom UPN des utilisateurs de ce domaine est modifié par défaut. suffixe onmicrosoft.com après la synchronisation, si le domaine n’est pas vérifié. | Vérifiez le domaine personnalisé dans Azure AD. [Pour en savoir plus](./active-directory-add-domain.md#verify-the-domain-name-with-azure-ad) |  
|Pas ajouté| Azure Connect d’Active Directory n’a pas pu trouver un domaine personnalisé correspondant au suffixe UPN. Suffixe de nom UPN des utilisateurs de ce domaine est modifié par défaut. onmicrosoft.com si le domaine n’est pas ajouté et verifeid dans Azure.| Ajouter et vérifier un domaine personnalisé correspondant au suffixe UPN [Pour en savoir plus](./active-directory-add-domain.md)|

Page de connexion AD Azure répertorie le suffix(s) UPN défini sur site Active Directory et le domaine personnalisé correspondant dans Azure annonces avec le statut en cours de vérification. Dans une installation personnalisée, vous pouvez désormais sélectionner l’attribut de nom principal d’utilisateur sur la page de **connexion AD Azure** .

![Page de connexion AD Azure](./media/active-directory-aadconnect-user-signin/custom_azure_sign_in.png)

Vous pouvez cliquer sur le bouton Actualiser à nouveau extraire le dernier état des domaines personnalisés d’Azure AD.

###<a name="selecting-attribute-for-user-principal-name-in-azure-ad"></a>Sélection d’attribut de nom principal d’utilisateur dans Active Directory Azure

UserPrincipalName - l’attribut userPrincipalName est l’attribut utilisateurs utilisent lorsqu’ils reconnectez-vous à AD Azure et Office 365. Les domaines utilisés, également connu sous le nom-suffixe UPN, doivent être vérifiées dans Azure annonce avant que les utilisateurs sont synchronisés. Il est fortement recommandé de conserver l’attribut userPrincipalName par défaut. Si cet attribut n’est pas routable et qu’il ne peut pas être vérifié, il est possible de sélectionner un autre attribut, par exemple par e-mail, comme l’attribut contenant l’ID de connexion. On parle de code de substitution. La valeur de l’attribut autre ID doit suivre la norme RFC822. Un ID autre peut être utilisé avec le mot de passe Single Sign-On (SSO) et fédération SSO que la solution d’ouverture de session.

>[AZURE.NOTE] À l’aide d’un autre ID n’est pas compatible avec toutes les charges de travail Office 365. Pour plus d’informations, reportez-vous à [Configuration d’un autre ID de connexion](https://technet.microsoft.com/library/dn659436.aspx).

#### <a name="different-custom-domain-states-and-effect-on-azure-sign-in-experience"></a>États de l’autre domaine personnalisé et impact sur l’expérience de connexion Azure
Il est très important de comprendre la relation entre les États de domaine personnalisé dans votre répertoire AD Azure et les suffixes UPN défini sur site. Nous, passent par les différentes possibles Azure connexion expériences lorsque vous configurez la synchronisation suivante à l’aide de la liaison de publicité Azure.

Pour les informations ci-dessous, supposons que nous sommes concernés par la contoso.com suffixe UPN qui est utilisée dans le répertoire local en tant que partie de l’UPN, par exemple user@contoso.com.

###### <a name="express-settings--password-synchronization"></a>Express paramètres / synchronisation de mot de passe
| État         | Effet sur l’expérience de l’utilisateur signe dans Azure |
|:-------------:|:----------------------------------------|
| Pas ajouté | Dans ce cas, aucun domaine personnalisé pour contoso.com n’a été ajouté dans le répertoire d’Azure AD. Les utilisateurs qui ont le nom UPN sur site avec le suffixe @contoso.com, ne sera pas en mesure d’utiliser leur UPN local pour ouvrir une session sur Azure. Au lieu de cela, ils devront utiliser un nouvel UPN fourni par AD Azure en ajoutant le suffixe du répertoire par défaut AD Azure. Par exemple, si vous synchronisez les utilisateurs AD Azure répertoire azurecontoso.onmicrosoft.com puis l’utilisateur local user@contoso.com aura un nom UPNuser@azurecontoso.onmicrosoft.com|
| Non vérifié | Dans ce cas, nous avons un contoso.com domaine personnalisé ajouté dans le répertoire de publicité Azure, mais il n’est pas encore vérifié. Si vous continuez avec la synchronisation des utilisateurs sans vérifier le domaine, puis les utilisateurs recevront un nouvel UPN par AD Azure comme c’était le cas dans le scénario « N’a pas ajouté ».|
| Vérifié | Dans ce cas, nous avons un contoso.com domaine personnalisé déjà ajouté et vérifié dans Azure AD pour le suffixe UPN. Les utilisateurs seront en mesure d’utiliser leur nom principal d’utilisateur local, par exemple, user@contoso.com, à la connexion vers Azure après qu’ils ont été synchronisées dans AD Azure|

###### <a name="ad-fs-federation"></a>Fédération AD FS
Vous ne pouvez pas créer une fédération avec la valeur par défaut. onmicrosoft.com domaine AD Azure ou un domaine personnalisé non vérifié dans Azure AD. Lorsque vous exécutez l’Assistant Azure Connect d’Active Directory, si vous sélectionnez un domaine non vérifié pour créer une fédération avec Azure puis connexion d’Active Directory vous invite avec les enregistrements nécessaires à créer hébergeant votre serveur DNS pour le domaine. Pour plus d’informations, voir [ici](active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation).

Si vous avez sélectionné les options utilisateur de connexion en tant que « Fédération avec AD FS », puis vous devez avoir un domaine personnalisé pour poursuivre la création d’une fédération dans Azure AD. Pour notre étude, cela signifie que nous devrions obtenir un contoso.com domaine personnalisé ajouté dans le répertoire d’Azure AD.

| État         | Effet sur l’expérience de l’utilisateur signe dans Azure |
|:-------------:|:----------------------------------------|
| Pas ajouté | Dans ce cas Azure Connect d’Active Directory ne trouve pas un domaine personnalisé correspondant pour le contoso.com suffixe UPN dans répertoire d’Azure AD. Vous devez ajouter un domaine personnalisé de contoso.com, si vous avez besoin d’utilisateurs de se connecter à l’aide d’ADFS avec leur UPN local comme user@contoso.com.|
| Non vérifié | Dans ce cas Azure Connect d’Active Directory vous demandera des détails utiles sur la façon dont vous pouvez vérifier votre domaine à un stade ultérieur|
| Vérifié | Dans ce cas vous pouvez continuez avec la configuration sans autre intervention|  

## <a name="changing-user-sign-in-method"></a>Modifier la méthode de connexion utilisateur

Vous pouvez modifier la méthode de connexion utilisateur de fédération pour la synchronisation de mot de passe à l’aide du disque de tâches dans Azure Connect d’Active Directory après la configuration initiale d’Azure Connect AD à l’aide de l’Assistant. Réexécutez l’Assistant Azure Connect d’Active Directory et s’affiche avec une liste de tâches que vous pouvez effectuer. Sélectionnez **modification authentification de l’utilisateur** dans la liste des tâches. 

![Modifier la connexion de l’utilisateur »](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

Sur la page suivante, vous devrez fournir les informations d’identification pour Azure AD.

![Connexion à Active Directory Azure](./media/active-directory-aadconnect-user-signin/changeusersignin2.png)

Sur la page de **connexion de l’utilisateur** , sélectionnez **Synchronisation de mot de passe**. Cette opération modifie le répertoire à partir de fédéré à un objet managé.

![Connexion à Active Directory Azure](./media/active-directory-aadconnect-user-signin/changeusersignin3.png)

>[AZURE.NOTE] Si vous effectuez uniquement un commutateur temporaire à la synchronisation de mot de passe, puis vérifiez les **comptes d’utilisateurs ne sont pas convertis**. Ne vérifie ne pas si l’option entraîne la conversion de chaque utilisateur fédéré et elle peut prendre plusieurs heures.
  
## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur [l’intégration des identités avec Azure Active Directory local](active-directory-aadconnect.md).

En savoir plus sur [Azure AD Connect : concepts de conception](active-directory-aadconnect-design-concepts.md)


