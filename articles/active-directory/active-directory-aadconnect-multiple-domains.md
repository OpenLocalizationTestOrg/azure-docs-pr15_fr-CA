<properties
    pageTitle="Annonce Azure se connecter à plusieurs domaines"
    description="Ce document décrit la définition et la configuration de plusieurs domaines de niveau supérieur avec O365 et Azure AD."
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

# <a name="multiple-domain-support-for-federating-with-azure-ad"></a>La prise en charge de domaine multiples pour fédérer avec Azure AD
La documentation suivante fournit des conseils sur l’utilisation de plusieurs domaines de niveau supérieur et des sous-domaines lorsque se fédérer avec Office 365 ou les domaines AD Azure.

## <a name="multiple-top-level-domain-support"></a>Prise en charge de plusieurs domaines de premier niveau
Fédérer plusieurs, domaines de niveau supérieur avec AD Azure nécessite une configuration supplémentaire qui n’est pas nécessaire lorsque vous fédérer avec un domaine de niveau supérieur.

Lorsqu’un domaine est fédéré à AD Azure, plusieurs propriétés sont définies sur le domaine dans Azure.  Important une seule est IssuerUri.  Il s’agit d’un URI qui est utilisé pour identifier le domaine auquel est associé le jeton par AD Azure.  L’URI n’a pas besoin résoudre quoi que ce soit mais qu'il doit être un URI valide.  Par défaut, AD Azure définit cette valeur à la valeur de l’identificateur de service de fédération dans vos locaux AD FS configuration.

>[AZURE.NOTE]L’identificateur de service de fédération est un URI qui identifie de manière unique un service de fédération.  Le service de fédération est une instance d’AD FS qui fonctionne en tant que le service de jeton de sécurité. 

Vous pouvez afficher IssuerUri à l’aide de la commande PowerShell `Get-MsolDomainFederationSettings - DomainName <your domain>`.

![Get-MsolDomainFederationSettings](./media/active-directory-multiple-domains/MsolDomainFederationSettings.png)

Un problème se pose lorsque vous souhaitez ajouter plus d’un domaine de niveau supérieur.  Par exemple, supposons que vous disposez de fédération de configuration entre Active Directory Azure et votre environnement local.  Pour ce document, j’utilise bmcontoso.com.  Maintenant j’ai ajouté un domaine de premier niveau, de deuxième, bmfabrikam.com.

![Domaines](./media/active-directory-multiple-domains/domains.png)

Lorsque vous essayez de convertir notre domaine bmfabrikam.com être fédéré, nous recevons une erreur.  La raison à cela est, AD Azure a une contrainte qui n’autorise pas la propriété IssuerUri d’avoir la même valeur pour plusieurs domaines.  
  

![Erreur de fédération](./media/active-directory-multiple-domains/error.png)

### <a name="supportmultipledomain-parameter"></a>Paramètre de SupportMultipleDomain

Pour contourner ce problème, nous devons ajouter une autre IssuerUri qui peut être effectuée à l’aide de la `-SupportMultipleDomain` paramètre.  Ce paramètre est utilisé avec les applets de commande suivantes :
    
- `New-MsolFederatedDomain`
- `Convert-MsolDomaintoFederated`
- `Update-MsolFederatedDomain`

Ce paramètre permet de configurer le IssuerUri afin qu’il soit basé sur le nom du domaine AD Azure.  Cela sera unique dans les répertoires dans Azure AD.  Le paramètre permet la commande PowerShell terminer avec succès.

![Erreur de fédération](./media/active-directory-multiple-domains/convert.png)

Examinez les paramètres de notre nouveau domaine bmfabrikam.com vous pouvez consultez les rubriques suivantes :

![Erreur de fédération](./media/active-directory-multiple-domains/settings.png)

Notez que `-SupportMultipleDomain` ne modifie pas les autres points de terminaison qui sont toujours configurés pour pointer vers notre service de fédération sur adfs.bmcontoso.com.

Une autre chose que `-SupportMultipleDomain` est consiste à s’assurer que le système AD FS comprend la valeur correcte de l’émetteur dans les jetons émis pour Azure AD. Il effectue cette opération en prenant de la partie domaine de l’UPN des utilisateurs et en définissant ce que le domaine de la IssuerUri, c'est-à-dire https://{upn suffixe} / adfs/services/approbation. 

Ainsi, lors de l’authentification dans AD Azure ou Office 365, l’élément IssuerUri dans le jeton d’utilisateur est utilisée pour localiser le domaine dans Active Directory Azure.  Si une correspondance ne peut pas être trouvée, que l’authentification échouera. 

Par exemple, si le nom UPN de l’utilisateur est bsimon@bmcontoso.com, l’élément IssuerUri de problèmes jeton AD FS est fixé à http://bmcontoso.com/adfs/services/trust. Cela correspond à la configuration AD Azure, et l’authentification réussit.

Voici la règle de revendication personnalisée qui implémente cette logique :

    c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type =   "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)", "http://${domain}/adfs/services/trust/"));


>[AZURE.IMPORTANT]Pour utiliser le commutateur - SupportMultipleDomain lorsque vous essayez d’ajouter de nouveaux ou convertir déjà ajouté des domaines, vous avez besoin pour que le programme d’installation à votre approbation fédérée pour prendre en charge à l’origine.  


## <a name="how-to-update-the-trust-between-ad-fs-and-azure-ad"></a>Comment mettre à jour l’approbation entre AD FS et Azure AD
Si vous n’avez pas paramétré l’approbation fédérée entre AD FS et votre instance AD Azure, vous devrez peut-être recréer cette approbation.  C’est pourquoi, lorsqu’il est à l’origine le programme d’installation sans le `-SupportMultipleDomain` paramètre, que la IssuerUri est définie avec la valeur par défaut.  Dans la capture d’écran ci-dessous, vous pouvez voir que la IssuerUri est définie sur https://adfs.bmcontoso.com/adfs/services/trust.

Donc maintenant, si nous avez ajouté un nouveau domaine dans le portail Azure AD et que vous essayez de convertir à l’aide de `Convert-MsolDomaintoFederated -DomainName <your domain>`, nous obtenons l’erreur suivante.

![Erreur de fédération](./media/active-directory-multiple-domains/trust1.png)

Si vous essayez d’ajouter le `-SupportMultipleDomain` commutateur nous recevra l’erreur suivante :

![Erreur de fédération](./media/active-directory-multiple-domains/trust2.png)

Il suffit que vous essayez d’exécuter `Update-MsolFederatedDomain -DomainName <your domain> -SupportMultipleDomain` dans le domaine d’origine également provoquera une erreur.

![Erreur de fédération](./media/active-directory-multiple-domains/trust3.png)

Utilisez les étapes ci-dessous pour ajouter un domaine de niveau supérieur supplémentaire.  Si vous avez déjà ajouté d’un domaine et n’avez pas utilisé le `-SupportMultipleDomain` début de paramètre avec les étapes de suppression et de mise à jour de votre domaine d’origine.  Si vous n’avez pas ajouté un domaine de niveau supérieur vous pouvez encore démarrer avec les étapes de l’ajout d’un domaine à l’aide de PowerShell d’Azure AD Connect.

Utilisez la procédure suivante pour supprimer l’approbation de Microsoft Online et mettre à jour votre domaine d’origine.

2.  Sur votre serveur de fédération AD FS ouvrir **AD FS gestion.** 
2.  Sur la gauche, développez les **Relations d’approbation** et les **Approbations des parties de confiance**
3.  Sur la droite, supprimez l’entrée de la **Plate-forme d’identité Microsoft Office 365** .
![Supprimer Microsoft en ligne](./media/active-directory-multiple-domains/trust4.png)
1.  Sur un ordinateur qui a [Azure Active Directory Module pour Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx) est installé, exécutez la procédure suivante : `$cred=Get-Credential`.  
2.  Entrez le nom d’utilisateur et le mot de passe d’un administrateur global pour le domaine AD Azure avec que vous vous fédérez
2.  Dans PowerShell, entrez`Connect-MsolService -Credential $cred`
4.  Dans PowerShell, entrez `Update-MSOLFederatedDomain -DomainName <Federated Domain Name> -SupportMultipleDomain`.  Il s’agit du domaine d’origine.  À l’aide de domaines ci-dessus qu'il serait :`Update-MsolFederatedDomain -DomainName bmcontoso.com -SupportMultipleDomain`


Procédez comme suit pour ajouter le nouveau domaine de niveau supérieur à l’aide de PowerShell

1.  Sur un ordinateur qui a [Azure Active Directory Module pour Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx) est installé, exécutez la procédure suivante : `$cred=Get-Credential`.  
2.  Entrez le nom d’utilisateur et le mot de passe d’un administrateur global pour le domaine AD Azure avec que vous vous fédérez
2.  Dans PowerShell, entrez`Connect-MsolService -Credential $cred`
3.  Dans PowerShell, entrez`New-MsolFederatedDomain –SupportMultipleDomain –DomainName`

Utilisez les étapes suivantes pour ajouter le nouveau domaine de niveau supérieur à l’aide d’Azure Connect d’Active Directory.

1.  Lancement d’Azure Connect d’Active Directory à partir du bureau ou le menu Démarrer
2.  Cliquez sur « Ajouter un domaine supplémentaire de publicité Azure » ![ajouter un autre domaine AD Azure](./media/active-directory-multiple-domains/add1.png)
3.  Entrez votre annonce Azure et informations d’identification d’Active Directory
4.  Sélectionnez le deuxième domaine que vous souhaitez configurer pour la fédération.
![Ajouter un autre domaine AD Azure](./media/active-directory-multiple-domains/add2.png)
5.  Cliquez sur Installer


### <a name="verify-the-new-top-level-domain"></a>Vérifiez que le nouveau domaine de niveau supérieur
À l’aide de la commande PowerShell `Get-MsolDomainFederationSettings - DomainName <your domain>`vous pouvez afficher le IssuerUri mis à jour.  La capture d’écran ci-dessous montre la fédération de paramètres ont été mis à jour sur notre http://bmcontoso.com/adfs/services/trust domaine d’origine

![Get-MsolDomainFederationSettings](./media/active-directory-multiple-domains/MsolDomainFederationSettings.png)

Et la IssuerUri sur notre nouveau domaine a été défini pour https://bmfabrikam.com/adfs/services/trust

![Get-MsolDomainFederationSettings](./media/active-directory-multiple-domains/settings2.png)


##<a name="support-for-sub-domains"></a>Prise en charge de sous-domaines
Lorsque vous ajoutez un sous-domaine, en raison des domaines AD façon Azure géré, il hérite les paramètres du parent.  Cela signifie que le IssuerUri doit correspondre à des parents.

Donc Supposons par exemple que j’ai bmcontoso.com et que vous ajoutez ensuite corp.bmcontoso.com.  Cela signifie que l’IssuerUri d’un utilisateur à partir de corp.bmcontoso.com doivent être **http://bmcontoso.com/adfs/services/trust.**  Toutefois, la règle standard implémentée ci-dessus pour Azure AD, génère un jeton avec un émetteur en tant que **http://corp.bmcontoso.com/adfs/services/trust.** qui ne correspondront pas du domaine de valeur requise et si l’authentification échoue.

### <a name="how-to-enable-support-for-sub-domains"></a>Comment faire pour activer la prise en charge de sous-domaines
Pour contourner ce problème l’ADFS approbation de partie de confiance pour Microsoft Online doit être mis à jour.  Pour ce faire, vous devez configurer une règle de revendication personnalisée afin qu’il retire les sous-domaines de suffixe de nom UPN de l’utilisateur lors de la construction de la valeur d’émetteur personnalisée. 

La demande suivante le fera :

    c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, "^((.*)([.|@]))?(?<domain>[^.]*[.].*)$", "http://${domain}/adfs/services/trust/"));

Utilisez les étapes suivantes pour ajouter une revendication personnalisée pour prendre en charge des sous-domaines.

1.  Ouvrez AD FS gestion
2.  Cliquez avec le bouton droit de la souris sur l’approbation de RP en ligne de Microsoft, puis choisissez les règles de la demande de modification
3.  Sélectionnez la règle de revendication troisième et remplacez ![demande de modification](./media/active-directory-multiple-domains/sub1.png)
4.  Remplacer la demande en cours :
    
        c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)","http://${domain}/adfs/services/trust/"));
        
    avec
    
        `c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, "^((.*)([.|@]))?(?<domain>[^.]*[.].*)$", "http://${domain}/adfs/services/trust/"));`
    
![Remplacer la demande](./media/active-directory-multiple-domains/sub2.png)
5.  Cliquez sur Ok.  Cliquez sur Appliquer.  Cliquez sur Ok.  Fermez la console Gestion de AD FS.

