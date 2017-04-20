<properties
    pageTitle="Des fonctionnalités de service de synchronisation AD Connect Azure et configuration | Microsoft Azure"
    description="Décrit les fonctionnalités du côté service pour le service de synchronisation d’Azure Connect d’Active Directory."
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
    ms.date="08/22/2016"
    ms.author="andkjell;markvi"/>

# <a name="azure-ad-connect-sync-service-features"></a>Fonctionnalités de service de synchronisation AD Connect Azure

La fonctionnalité de synchronisation de Azure AD Connect a deux composants :

- Le composant local nommé **Azure Connect de AD sync**, également appelé **moteur de synchronisation**.
- Le service résidant dans Azure annonce également connu sous le nom de **service de synchronisation d’Azure Connect de publicité**

Cette rubrique explique comment les fonctionnalités suivantes du **service de synchronisation d’Azure Connect de publicité** fonctionnent et comment vous pouvez les configurer à l’aide de Windows PowerShell.

Ces paramètres sont configurés par le [Azure Active Directory Module pour Windows PowerShell](http://aka.ms/aadposh). Télécharger et l’installer séparément sur Azure Connect d’Active Directory. Les applets de commande documentées dans cette rubrique ont été introduites dans le [2016 mars version (version 9031.1)](http://social.technet.microsoft.com/wiki/contents/articles/28552.microsoft-azure-active-directory-powershell-module-version-release-history.aspx#Version_9031_1). Si vous n’avez pas les applets de commande documentées dans cette rubrique, ou qu’ils ne produisent pas le même résultat, vérifiez que vous exécutez la version la plus récente.

Pour voir la configuration de votre annuaire AD Azure, exécutez `Get-MsolDirSyncFeatures`.  
![Résultat de Get-MsolDirSyncFeatures](./media/active-directory-aadconnectsyncservice-features/getmsoldirsyncfeatures.png)

La plupart de ces paramètres seulement peuvent être modifiées que par Azure Connect d’Active Directory.

Les paramètres suivants peuvent être configurés par `Set-MsolDirSyncFeature`:

DirSyncFeature | Commentaire
--- | ---
[DuplicateProxyAddressResiliency<br/>DuplicateUPNResiliency](#duplicate-attribute-resiliency) | Permet à un attribut de mise en quarantaine lorsqu’il s’agit d’un doublon d’un autre objet plutôt que d’échouer l’ensemble de l’objet pendant l’exportation.
[EnableSoftMatchOnUpn](#userprincipalname-soft-match) | Permet aux objets à rejoindre l’userPrincipalName en plus de l’adresse SMTP principale.
[SynchronizeUpnForManagedUsers](#synchronize-userprincipalname-updates) | Permet de mettre à jour l’attribut userPrincipalName gérés/sous licence des utilisateurs (non fédérés), le moteur de synchronisation.

Après avoir activé une fonctionnalité, il ne peut pas être désactivé à nouveau.

>[AZURE.NOTE] À partir du 24 août 2016, la fonctionnalité de *résilience d’attribut en double* est activée par défaut pour Azure de nouveaux répertoires d’Active Directory. Cette fonctionnalité sera également être déployée et activée sur les répertoires créés avant cette date. Vous recevrez une notification par e-mail lorsque votre répertoire est sur le point d’obtenir cette fonctionnalité est activée.

Les paramètres suivants sont configurés par Azure Connect d’Active Directory et ne peut pas être modifiées par `Set-MsolDirSyncFeature`:

DirSyncFeature | Commentaire
--- | ---
DeviceWriteback | [Azure AD Connect : Activation d’écriture différée du périphérique](active-directory-aadconnect-feature-device-writeback.md)
DirectoryExtensions | [Azure Connect de AD sync : extensions de répertoire](active-directory-aadconnectsync-feature-directory-extensions.md)
PasswordSync | [Mise en oeuvre de la synchronisation de mot de passe avec la synchronisation Azure Connect de publicité](active-directory-aadconnectsync-implement-password-synchronization.md)
UnifiedGroupWriteback | [Visualisation : L’écriture différée du groupe](active-directory-aadconnect-feature-preview.md#group-writeback)
UserWriteback | N’est actuellement pas pris en charge.

## <a name="duplicate-attribute-resiliency"></a>Résilience d’attribut en double
Au lieu de basculer à la disposition des objets avec des noms UPN en double / proxyAddresses, l’attribut dupliqué est « mis en quarantaine » et une valeur temporaire est assignée. Lorsque le conflit est résolu, l’UPN temporaire est modifié automatiquement à la valeur correcte. Ce comportement peut être activé pour l’UPN et proxyAddress séparément. Pour plus d’informations, consultez [synchronisation d’identité et de résilience d’attribut en double](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md).

## <a name="userprincipalname-soft-match"></a>Correspondance de mou UserPrincipalName
Lorsque cette fonctionnalité est activée, soft match est activée pour l’UPN en plus de l' [adresse SMTP principale](https://support.microsoft.com/kb/2641663), qui est toujours activée. Soft-correspondance est utilisée pour faire correspondre les utilisateurs existants du cloud dans Azure annonce avec des utilisateurs locaux.

Si vous devez respecter les comptes Active Directory avec des comptes existants créés dans le nuage sur site et vous n’utilisez pas Exchange Online, puis cette fonctionnalité est utile. Dans ce scénario, vous n’avez généralement une raison pour définir l’attribut SMTP dans le nuage.

Cette fonctionnalité sur par défaut pour nouvellement créée les répertoires AD Azure. Vous pouvez voir si cette fonctionnalité est activée pour vous en exécutant :  
```
Get-MsolDirSyncFeatures -Feature EnableSoftMatchOnUpn
```

Si cette fonctionnalité n’est pas activée pour votre annuaire AD Azure, vous pouvez l’activer en exécutant :  
```
Set-MsolDirSyncFeature -Feature EnableSoftMatchOnUpn -Enable $true
```

## <a name="synchronize-userprincipalname-updates"></a>Synchroniser les mises à jour d’userPrincipalName
Historiquement, les mises à jour à l’attribut UserPrincipalName utilisant le service de synchronisation du site a été bloqué, sauf si les deux conditions suivantes sont remplies :

- L’utilisateur est géré (non fédéré).
- L’utilisateur n’a pas été affecté à une licence.

Pour plus d’informations, consultez [noms d’utilisateur dans Office 365, Azure ou Intune ne correspondent pas à la local UPN ou un ID de connexion de remplacement](https://support.microsoft.com/kb/2523192).

L’activation de cette fonctionnalité permet de mettre à jour l’userPrincipalName lorsqu’il est modifié en local et que vous utilisez la synchronisation de mot de passe, le moteur de synchronisation. Si vous utilisez la fédération, cette fonctionnalité n’est pas pris en charge.

Cette fonctionnalité sur par défaut pour nouvellement créée les répertoires AD Azure. Vous pouvez voir si cette fonctionnalité est activée pour vous en exécutant :  
```
Get-MsolDirSyncFeatures -Feature SynchronizeUpnForManagedUsers
```

Si cette fonctionnalité n’est pas activée pour votre annuaire AD Azure, vous pouvez l’activer en exécutant :  
```
Set-MsolDirSyncFeature -Feature SynchronizeUpnForManagedUsers -Enable $true
```

Après avoir activé cette fonctionnalité, les valeurs userPrincipalName existantes demeurent en tant que-est. Sur la prochaine modification d’userPrincipalName attribut sur site, la synchronisation delta normal des utilisateurs met à jour l’UPN.  

## <a name="see-also"></a>Voir aussi

- [Synchronisation d’Active Directory Connect Azure](active-directory-aadconnectsync-whatis.md)
- [Intégration des identités avec Azure Active Directory local](active-directory-aadconnect.md).
