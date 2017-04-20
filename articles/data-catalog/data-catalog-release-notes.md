<properties
   pageTitle="Notes de version de catalogue de données Azure | Microsoft Azure"
   description="Notes de publication pour le catalogue de données Azure."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="09/21/2016"
   ms.author="maroche"/>

# <a name="azure-data-catalog-release-notes"></a>Notes de version de catalogue de données Azure

## <a name="notes-for-the-november-20-2015-release-of-azure-data-catalog"></a>Notes pour le 20 novembre 2015 version du catalogue de données Azure

### <a name="opening-data-sources-in-power-bi-desktop"></a>Ouverture des Sources de données de bureau BI de puissance

Lorsque vous utilisez l’option « Ouvrir dans alimentation BI Bureau » à partir du portail de **Catalogue de données Azure** , les utilisateurs peuvent rencontrer un des deux problèmes dans l’application de bureau de BI d’alimentation :

- Une boîte de dialogue avec le titre « Impossible d’ouvrir un Document » s’affiche.
- L’application de bureau de BI d’alimentation s’ouvre, mais le fichier semble être vide

Pour chaque cas, le problème peut être résolu en téléchargeant et en installant la dernière version de bureau de BI de puissance à partir de [PowerBI.com](https://powerbi.com).

## <a name="notes-for-the-november-13-2015-release-of-azure-data-catalog"></a>Notes pour le 13 novembre 2015 version du catalogue de données Azure

### <a name="registering-and-connecting-to-teradata"></a>Inscription et connexion à Teradata

Lors de la connexion aux sources de données Teradata utilisateurs doivent avoir installé le pilote ODBC de Teradata qui correspondent au nombre de bits (32 bits ou 64 bits) du logiciel en cours d’utilisation.

À cette date de version de connecteur Active Directory, le [pilote ODBC de Teradata pour windows (version 15.10)](http://downloads.teradata.com/download/connectivity/odbc-driver/windows) le plus récent est compatible avec Office 2013, mais pas avec Office 2016.

## <a name="notes-for-the-july-13-2015-release-of-azure-data-catalog"></a>Mise à jour du catalogue de données Azure de notes pour le 13 juillet 2015

### <a name="registering-and-connecting-to-oracle-database"></a>Inscription et connexion à la base de données Oracle

Lors de la connexion aux sources de données de base de données Oracle les utilisateurs doivent avoir installé les pilotes Oracle appropriés qui correspondent au nombre de bits (32 bits ou 64 bits) du logiciel en cours d’utilisation.

-   Lors de l’enregistrement des sources de données Oracle sur un ordinateur exécutant Windows 32 bits, les pilotes Oracle 32 bits seront utilisés
-   Lors de l’enregistrement des sources de données Oracle sur un ordinateur fonctionnant sous Windows 64 bits, les pilotes Oracle 64 bits seront utilisés
-   Lors de la connexion aux sources de données Oracle à l’aide de Excel sur un ordinateur exécutant la version 32 bits de Microsoft Office, y compris sur Windows 64 bits, les pilotes Oracle 32 bits servira
-   Lors de la connexion aux sources de données Oracle à l’aide de Excel sur un ordinateur exécutant la version 64 bits de Microsoft Office, les pilotes Oracle 64 bits seront utilisés

### <a name="registering-and-connecting-to-sql-server-reporting-services"></a>Inscription et connexion à SQL Server Reporting Services

Prise en charge des sources de données SQL Server Reporting Services (SSRS) est actuellement limitée aux serveurs en Mode natif uniquement. Prise en charge pour SSRS en mode SharePoint sera ajouté dans une version ultérieure.

### <a name="opening-data-assets-in-excel"></a>Ressources de données ouverture dans Excel

Lors de l’ouverture des ressources de données dans Microsoft Excel à partir du portail de **Catalogue de données Azure** , les utilisateurs peuvent être invités avec une boîte de dialogue de **Notification de sécurité de Microsoft Excel** . Il s’agit de standard, le comportement attendu et les utilisateurs peuvent sélectionner **Activer** pour continuer.

Pour plus d’informations, voir [Activer ou désactiver les alertes de sécurité concernant les liens et les fichiers à partir des sites Web suspects](https://support.office.com/article/Enable-or-disable-security-alerts-about-links-and-files-from-suspicious-websites-A1AC6AE9-5C4A-4EB3-B3F8-143336039BBE).

### <a name="proxy-and-policy-configuration-and-data-source-registration"></a>Configuration de proxy et de la stratégie et les données de la source d’enregistrement

Les utilisateurs peuvent rencontrer une situation où ils peuvent se connecter le portail du catalogue de données Azure, mais lorsqu’ils tentent de se connecter à l’outil d’enregistrement de source de données qu’ils rencontrent un message d’erreur qui empêche l’ouverture de session.

Il existe deux causes possibles à ce problème :

**Cause 1 : configuration des Services de fédération Active Directory** L’outil de l’enregistrement de source de données utilise l’authentification par formulaire pour valider des ouvertures de session utilisateur dans Active Directory. Pour l’ouverture de session réussie, l’authentification par formulaires doit être activée dans la stratégie d’authentification globale par un administrateur Active Directory.

Dans certains cas, cette erreur peut également survenir uniquement lorsque l’utilisateur est connecté au réseau d’entreprise ou uniquement lorsque l’utilisateur se connecte à partir de l’extérieur du réseau d’entreprise. La stratégie d’authentification globale permet de méthodes d’authentification être activé séparément pour l’intranet et les connexions extranet. Erreurs de connexion peuvent se produire si l’authentification par formulaire n’est pas activée pour le réseau à partir duquel l’utilisateur se connecte.

Pour plus d’informations, consultez [Configuration des stratégies d’authentification](https://technet.microsoft.com/library/dn486781.aspx).

**Cause 2 : configuration de proxy réseau** Si le réseau d’entreprise utilise un serveur proxy, l’outil d’inscription n’est peut-être pas capable de se connecter à Azure Active Directory via le serveur proxy. Les utilisateurs peuvent garantir que l’outil d’inscription en modifiant le fichier de configuration de l’outil, l’ajout de cette section dans le fichier :


      <system.net>
        <defaultProxy useDefaultCredentials="true" enabled="true">
          <proxy usesystemdefault="True"
                          proxyaddress="http://<your corporate network proxy url>"
                          bypassonlocal="True"/>
        </defaultProxy>
      </system.net>


Pour localiser le fichier RegistrationTool.exe.config, lancez l’outil d’inscription, puis ouvrez le Gestionnaire des tâches Windows. Sous l’onglet Détails, dans le Gestionnaire des tâches, avec le bouton droit sur RegistrationTool.exe et choisissez un emplacement d’ouvrir le fichier dans le menu contextuel.
