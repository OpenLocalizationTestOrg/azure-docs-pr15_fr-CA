<properties
    pageTitle="Azure applets de commande de Active Directory pour configurer les paramètres de groupe | Microsoft Azure"
    description="Comment gérer les paramètres de groupes à l’aide des applets de commande Azure Active Directory."
    services="active-directory"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/22/2016"
    ms.author="curtand"/>


# <a name="azure-active-directory-cmdlets-for-configuring-group-settings"></a>Azure applets de commande de Active Directory pour configurer les paramètres de groupe

Les paramètres suivants pour les groupes unifiées peuvent être configurés dans votre répertoire :

1.  Classifications : séparés par des virgules liste des classifications que les utilisateurs peuvent définir sur un groupe. Exemples serait « Classique », « Secret » et « Top Secret ».

2.  URL de directives d’utilisation : une URL vers les utilisateurs les conditions d’utilisation pour l’utilisation de groupes unifiée, tel que défini par votre organisation. Cette URL apparaît dans l’interface utilisateur où les utilisateurs utilisent les groupes.

3.  Groupe création activée : Si aucun, certains ou tous les utilisateurs sont autorisés à créer des groupes d’unifiée. Lorsque la valeur de, tous les utilisateurs peuvent créer des groupes. Lorsque cette propriété a la valeur off, aucun utilisateur ne peut créer des groupes. Lorsque off, vous pouvez également spécifier un groupe de sécurité dont les utilisateurs sont toujours autorisés à créer des groupes.

Ces paramètres sont configurés à l’aide d’un fichier de paramètres et objets de SettingsTemplate. Au départ, vous ne verrez pas les objets paramètres dans votre répertoire. Cela signifie que votre répertoire est configuré avec les paramètres par défaut. Pour modifier les paramètres par défaut, vous allez créer un nouvel objet de paramètres à l’aide d’un modèle de paramètres. Les modèles de paramètres sont définis par Microsoft.

Vous pouvez télécharger le module contenant les applets de commande utilisé pour ces opérations à partir du [site Microsoft Connect](http://connect.microsoft.com/site1164/Downloads/DownloadDetails.aspx?DownloadID=59185).

## <a name="create-settings-at-the-directory-level"></a>Créer des paramètres au niveau du répertoire

Ces étapes créent des paramètres au niveau du répertoire, qui s’appliquent à tous les groupes d’Office dans le répertoire.

1. Si vous ne savez pas quel SettingTemplate à utiliser, cette applet de commande renvoie la liste des modèles de paramètres :

    `Get-MsolAllSettingTemplate`

    ![Liste des modèles de paramètres](./media/active-directory-accessmanagement-groups-settings-cmdlets/list-of-templates.png)

2. Pour ajouter une URL de l’indication l’utilisation, vous devez d’abord obtenir l’objet SettingsTemplate qui définit la valeur d’URL utilisation indicative ; Autrement dit, le modèle de Group.Unified :

    `$template = Get-MsolSettingTemplate –TemplateId 62375ab9-6b52-47ed-826b-58e47e0e304b`

3. Ensuite, créez un nouvel objet paramètres basé sur ce modèle :

    `$setting = $template.CreateSettingsObject()`

4. Mettre à jour la valeur indicative d’utilisation :

    `$setting["UsageGuidelinesUrl"] = "<https://guideline.com>"`

5. Enfin, appliquez les paramètres :

    `New-MsolSettings –SettingsObject $setting`

    ![Ajouter une URL de l’indication utilisation](./media/active-directory-accessmanagement-groups-settings-cmdlets/add-usage-guideline-url.png)

Voici les paramètres définis dans les Group.Unified SettingsTemplate.

 **Paramètre**                          | **Description**                                                                                             
--------------------------------------|-----------------------------------------------
 <ul><li>ClassificationList<li>Type : chaîne<li>Par défaut : « »                  | Une liste délimitée par des virgules des valeurs de classement valide qui peuvent être appliqués à des groupes d’unifiée.                
 <ul><li>EnableGroupCreation<li>Type : Boolean<li>Par défaut : True              | Le drapeau indiquant si la création d’un groupe d’unifiée est autorisée dans le répertoire.                               
 <ul><li>GroupCreationAllowedGroupId<li>Type : chaîne<li>Par défaut : « »         | GUID du groupe de sécurité qui est autorisé à créer des groupes d’unifiée même lorsque EnableGroupCreation == false.
 <ul><li>UsageGuidelinesUrl<li>Type : chaîne<li>Par défaut : « »                  | Un lien vers les instructions d’utilisation de groupe.                                                                       

## <a name="read-settings-at-the-directory-level"></a>Lire des paramètres au niveau du répertoire

Ces étapes lire des paramètres au niveau du répertoire, qui s’appliquent à tous les groupes d’Office dans le répertoire.

1. Lire tous les paramètres de répertoire existant :

    `Get-MsolAllSettings`

2. Lire tous les paramètres pour un groupe spécifique :

    `Get-MsolAllSettings -TargetType Groups -TargetObjectId <groupObjectId>`

3. Lire les paramètres de répertoire spécifique, à l’aide de SettingId GUID :

    `Get-MsolSettings –SettingId dbbcb0ea-a6ff-4b44-a1f3-9d7cef74984c`

    ![Les paramètres ID GUID](./media/active-directory-accessmanagement-groups-settings-cmdlets/settings-id-guid.png)

## <a name="update-settings-at-the-directory-level"></a>Mettre à jour les paramètres au niveau du répertoire

Ces étapes de mettre à jour les paramètres au niveau du répertoire, qui s’appliquent à tous les groupes d’Office dans le répertoire.

1. Obtenir l’objet de paramètres existant :

    `$setting = Get-MsolSettings –SettingId dbbcb0ea-a6ff-4b44-a1f3-9d7cef74984c`

2. Obtenir la valeur que vous souhaitez mettre à jour :

    `$value = $Setting.GetSettingsValue()`

3. Mise à jour de la valeur :

    `$value["AllowToAddGuests"] = "false"`

4. Mettre à jour le paramètre :

    `Set-MsolSettings –SettingId dbbcb0ea-a6ff-4b44-a1f3-9d7cef74984c –SettingsValue $value`

## <a name="remove-settings-at-the-directory-level"></a>Supprimer les paramètres au niveau du répertoire

Cette étape supprime les paramètres au niveau du répertoire, qui s’appliquent à tous les groupes d’Office dans le répertoire.

    `Remove-MsolSettings –SettingId dbbcb0ea-a6ff-4b44-a1f3-9d7cef74984c`

## <a name="cmdlet-syntax-reference"></a>Référence de la syntaxe applet de commande

Vous trouverez plus Azure Active Directory PowerShell documentation sur [Azure Active Directory applets de commande](http://go.microsoft.com/fwlink/p/?LinkId=808260).

## <a name="settingstemplate-object-reference-groupunified-settingstemplate-object"></a>Référence d’objet SettingsTemplate (Group.Unified SettingsTemplate objet)

- « nom » : « EnableGroupCreation », « type » : « System.Boolean », « defaultValue » : « true », « description » : « Un indicateur booléen indiquant si la fonctionnalité de création de groupe d’unifiée est activée. »

- « nom » : « GroupCreationAllowedGroupId », « type » : « System.Guid », « defaultValue » : « », « description » : « GUID du groupe de sécurité qui est la liste d’autorisation pour créer des groupes d’unifiée ».

- « nom » : « ClassificationList », « type » : « System.String », « defaultValue » : « », « description » : « CSV liste des valeurs de classement valide qui peut être appliqué aux groupes unifiée. »

- « nom » : « UsageGuidelinesUrl », « type » : « System.String », « defaultValue » : « », « description » : « Lien vers les instructions d’utilisation de groupe ».

nom | type de | defaultValue | Description
----------  | ----------  | ---------  | ----------
« EnableGroupCreation »  | « System.Boolean »  | « true »  | « Indicateur booléen indiquant si la fonctionnalité de création de groupe d’unifiée est activée. »
« GroupCreationAllowedGroupId »  | « System.Guid. »  | ""  | « GUID du groupe de sécurité qui est la liste d’autorisation pour créer des groupes d’unifiée ».
« ClassificationList »  | « System.String »  | ""  | « CSV liste de valeurs de classement valide qui peuvent être appliqués à des groupes d’unifiée. »
« UsageGuidelinesUrl »  | « System.String »  | ""  | « Lien vers les instructions d’utilisation de groupe. »

## <a name="next-steps"></a>Étapes suivantes

Vous trouverez plus Azure Active Directory PowerShell documentation sur [Azure Active Directory applets de commande](http://go.microsoft.com/fwlink/p/?LinkId=808260).

Des instructions supplémentaires à partir du Gestionnaire de programme de Microsoft Rob de Jong sont disponible au [Blog de groupes de Rob](http://robsgroupsblog.com/blog/configuring-settings-for-office-365-groups-in-azure-ad).

* [Gestion de l’accès aux ressources avec groupes Azure Active Directory](active-directory-manage-groups.md)

* [Intégration des identités sur site à Azure Active Directory](active-directory-aadconnect.md)
