<properties
   pageTitle="Connecteur de PowerShell | Microsoft Azure"
   description="Cet article décrit comment configurer le connecteur de Windows PowerShell de Microsoft."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="08/30/2016"
   ms.author="billmath"/>

# <a name="windows-powershell-connector-technical-reference"></a>Guide de référence technique de Windows PowerShell connecteur
Cet article décrit le connecteur de Windows PowerShell. L’article s’applique aux produits suivants :

- Gestionnaire d’identité Microsoft 2016 (MIM2016)
- Forefront Identity Manager 2010 R2 (FIM2010R2)
    -   Doit utiliser le correctif logiciel 4.1.3671.0 ou une version ultérieure [KB3092178](https://support.microsoft.com/kb/3092178).

Pour MIM2016 et FIM2010R2, le connecteur est disponible en téléchargement à partir du [Centre de téléchargement Microsoft](http://go.microsoft.com/fwlink/?LinkId=717495).

## <a name="overview-of-the-powershell-connector"></a>Vue d’ensemble du connecteur PowerShell
Le connecteur de PowerShell vous permet d’intégrer le service de synchronisation avec les systèmes externes qui offrent des Qu'api de base de Windows PowerShell. Le connecteur offre une passerelle entre les fonctionnalités de l’agent de gestion extensible connectivity basé sur un appel 2 (ECMA2) framework et Windows PowerShell. Pour plus d’informations sur le framework d’ECMA, consultez la [Référence d’Agent de gestion de 2.2 connectivité Extensible](https://msdn.microsoft.com/library/windows/desktop/hh859557.aspx).

### <a name="prerequisites"></a>Conditions préalables
Avant d’utiliser le connecteur, vérifiez que vous disposez des éléments suivants sur le serveur de synchronisation :

- 4.5.2 de Microsoft .NET Framework ou une version ultérieure
- Windows PowerShell 2.0, 3.0 ou 4.0

La stratégie d’exécution sur le serveur du Service de synchronisation doit être configurée pour autoriser le connecteur à exécuter des scripts Windows PowerShell. Sauf si les scripts de l’exécution du connecteur est signées numériquement, configurez la stratégie d’exécution en exécutant cette commande :  
`Set-ExecutionPolicy -ExecutionPolicy RemoteSigned`

## <a name="create-a-new-connector"></a>Créer un nouveau connecteur
Pour créer un connecteur de Windows PowerShell dans le service de synchronisation, vous devez fournir une série de scripts Windows PowerShell qui exécutent les opérations demandées par le service de synchronisation. Selon les fonctionnalités requises et que vous vous connectez à la source de données, les scripts, vous devez implémenter varie. Cette section décrit chacun des scripts qui peuvent être implémentées et lorsqu’ils sont requis.

Le connecteur de Windows PowerShell est conçu pour stocker de chacun des scripts à l’intérieur de la base de données du Service de synchronisation. Bien qu’il soit possible d’exécuter des scripts qui sont stockés sur le système de fichiers, il est plus facile d’insérer le corps de chaque script directement dans les configuration du connecteur.

Pour créer un connecteur de PowerShell, sélectionnez **Agent de gestion** et de **Création**de **Service de synchronisation** . Sélectionnez le connecteur **PowerShell (Microsoft)** .

![Créer le connecteur](./media/active-directory-aadconnectsync-connector-powershell/createconnector.png)

### <a name="connectivity"></a>Connectivité
Fournir les paramètres de configuration pour la connexion à un système distant. Ces valeurs sont stockées par le Service de synchronisation et mis à la disposition de vos scripts Windows PowerShell lors de l’exécution du connecteur est en toute sécurité.

![Connectivité](./media/active-directory-aadconnectsync-connector-powershell/connectivity.png)

Vous pouvez configurer les paramètres de connexion suivants :

**Connectivité**

Paramètre | Valeur par défaut | Objectif
--- | --- | ---
Serveur | <Blank> | Nom du serveur que le connecteur doit se connecter.
Domaine | <Blank> | Domaine de l’information d’identification pour stocker pour une utilisation lors de l’exécution du connecteur.
Utilisateur | <Blank> | Nom d’utilisateur des informations d’identification pour stocker pour une utilisation lors de l’exécution du connecteur.
Mot de passe | <Blank> | Mot de passe de l’information d’identification pour stocker pour une utilisation lors de l’exécution du connecteur.
Imiter le compte de connecteur | False | Lorsque la valeur est true, le service de synchronisation s’exécute les scripts Windows PowerShell dans le contexte des informations d’identification fournies. Lorsque cela est possible, il est recommandé que le paramètre **$Credentials** est passé à chaque script est utilisé à la place de l’emprunt d’identité. Pour plus d’informations sur les autorisations supplémentaires qui sont nécessaires pour utiliser cette option, reportez-vous à la section [Configuration supplémentaire pour l’emprunt d’identité](#additional-configuration-for-impersonation).
Charger le profil de l’utilisateur lorsque l’emprunt d’identité | False | Indique à Windows de charger le profil de l’utilisateur des informations d’identification du connecteur au cours de l’emprunt d’identité. Si l’emprunt d’identité utilisateur a un profil itinérant, le connecteur ne charge pas le profil itinérant. Pour plus d’informations sur les autorisations supplémentaires qui sont nécessaires pour utiliser ce paramètre, reportez-vous à la section [Configuration supplémentaire pour l’emprunt d’identité](#additional-configuration-for-impersonation).
Type d’ouverture de session lorsque l’emprunt d’identité | Aucun | Type d’ouverture de session au cours de l’emprunt d’identité. Pour plus d’informations, reportez-vous à la [dwLogonType] [ dw] documentation.
Uniquement des Scripts signés | False | Si la valeur est true, le connecteur de Windows PowerShell vérifie que chaque script possède une signature numérique valide. Si la valeur est false, assurez-vous que la stratégie d’exécution du serveur de Service de synchronisation de Windows PowerShell est RemoteSigned ou non restreint.

**Module commun**  
Le connecteur vous permet de stocker un module Windows PowerShell partagé dans la configuration. Lorsque le connecteur s’exécute un script, le module de Windows PowerShell est extrait dans le système de fichiers afin qu’il puisse être importé par chaque script.

Pour les scripts d’importation, d’exportation et de synchronisation de mot de passe, le module commun est extrait vers le dossier du connecteur MAData. Pour les scripts de découverte de schéma, la Validation, hiérarchie et Partition, le module commun est extrait dans le dossier % Temp%. Dans les deux cas, le script extrait de Module commun est nommé en fonction du paramètre de nom de Script Module commun.

Pour charger un module appelé FIMPowerShellConnectorModule.psm1 dans le dossier MAData, utilisez l’instruction suivante :`Import-Module (Join-Path -Path [Microsoft.MetadirectoryServices.MAUtils]::MAFolder -ChildPath "FIMPowerShellConnectorModule.psm1")`

Pour charger un module appelé FIMPowerShellConnectorModule.psm1 dans le dossier % Temp%, utilisez l’instruction suivante :`Import-Module (Join-Path -Path $env:TEMP -ChildPath "FIMPowerShellConnectorModule.psm1")`

**Validation des paramètres**  
Le Script de Validation est un script Windows PowerShell facultatif qui peut être utilisé pour s’assurer que les paramètres de configuration des connecteurs fournis par l’administrateur sont valides. Le serveur de validation, les informations d’identification de connexion et les paramètres de connectivité sont les usages courants du script de validation. Le script de validation est appelé après les onglets suivants et boîtes de dialogue sont modifiés :

- Connectivité
- Paramètres globaux
- Configuration de la partition

Le script de validation reçoit les paramètres suivants du connecteur :

Nom | Type de données | Description
--- | --- | ---
ConfigParameterPage | [ConfigParameterPage][cpp] | La boîte de dialogue qui a déclenché la demande de validation ou l’onglet configuration.
ConfigParameters | [KeyedCollection] [ keyk] [string, [ConfigParameter][cp]] | Tableau de paramètres de configuration pour le connecteur.
Informations d’identification | [PSCredential][pscred] | Contient des informations d’identification entrées par l’administrateur sur l’onglet connectivité.

Le script de validation doit retourner un seul objet ParameterValidationResult pour le pipeline.

**Découverte de schéma**  
Le script de découverte de schéma est obligatoire. Ce script renvoie les types d’objet, les attributs et les contraintes de l’attribut par le Service de synchronisation lors de la configuration des règles de flux des attributs. Le script de découverte de schéma est exécuté lors de la création du connecteur et remplit les schéma du connecteur. Il est également utilisé par l’action Actualiser le schéma dans le Gestionnaire de Service de synchronisation.

Le script de découverte de schéma reçoit les paramètres suivants du connecteur :

Nom | Type de données | Description
--- | --- | ---
ConfigParameters | [KeyedCollection] [ keyk] [string, [ConfigParameter][cp]] | Tableau de paramètres de configuration pour le connecteur.
Informations d’identification | [PSCredential][pscred] | Contient des informations d’identification entrées par l’administrateur sur l’onglet connectivité.

Le script doit renvoyer un seul [schéma] [ schema] objet au pipeline. L’objet de schéma est composé de [SchemaType] [ schemaT] objets qui représentent des types d’objet (par exemple : utilisateurs et groupes). L’objet de SchemaType contient une collection de [SchemaAttribute] [ schemaA] les objets qui représentent les attributs (par exemple : prénom, nom et adresse postale) du type.

**Paramètres supplémentaires**  
Outre les paramètres de configuration standard, vous pouvez définir des paramètres de configuration personnalisés supplémentaires qui sont spécifiques à l’instance du connecteur. Ces paramètres peuvent être spécifiés au niveau du connecteur, partition, ou exécution d’étape niveaux et accessible à partir du script Windows PowerShell approprié. Paramètres de configuration personnalisés peuvent être stockés dans la base de données du Service de synchronisation au format texte brut, ou ils peuvent être cryptés. Le Service de synchronisation automatiquement crypte et décrypte sécuriser les paramètres de configuration à la demande.

Pour spécifier les paramètres de configuration personnalisés, séparez le nom de chaque paramètre par une virgule (,).

Pour accéder aux paramètres de configuration personnalisés à partir d’un script, vous devez le suffixe le nom avec un trait de soulignement ( \_ ) et la portée du paramètre (Global, la Partition ou RunStep). Par exemple, pour accéder au paramètre de nom de fichier Global, vous devez utiliser cet extrait de code :`$ConfigurationParameters["FileName_Global"].Value`

### <a name="capabilities"></a>Fonctionnalités
L’onglet fonctionnalités du Concepteur de l’Agent de gestion définit le comportement et les fonctionnalités du connecteur. Les sélections effectuées dans cet onglet ne peut pas être modifiées lorsque le connecteur a été créé. Ce tableau répertorie les paramètres de la fonction.

![Fonctionnalités](./media/active-directory-aadconnectsync-connector-powershell/capabilities.png)

Capacité | Description |
--- | --- |
[Style de nom unique][dnstyle] | Indique si le connecteur prend en charge les noms uniques et par conséquent, le style.
[Type d’exportation][exportT] | Détermine le type des objets qui sont présentées dans le script d’exportation. <li>AttributeReplace – inclut l’ensemble des valeurs d’un attribut à valeurs multiples lorsque l’attribut est modifié.</li><li>AttributeUpdate – inclut uniquement les deltas à un attribut à valeurs multiples lorsque l’attribut est modifié.</li><li>MultivaluedReferenceAttributeUpdate - comprend un ensemble complet de valeurs pour les attributs à valeurs multiples sans référence et deltas uniquement pour les attributs de référence à valeurs multiples.</li><li>ObjectReplace – inclut tous les attributs d’un objet lorsque les modifications d’attribut</li>
[Normalisation des données][DataNorm] | Indique le Service de synchronisation pour normaliser les attributs d’ancrage avant qu’ils sont fournis aux scripts.
[Confirmation de l’objet][oconf] | Configure le comportement de l’importation en attente dans le Service de synchronisation. <li>Normal – par défaut qui attend les modifications tout exportées doit être confirmée par importation</li><li>NoDeleteConfirmation – lorsqu’un objet est supprimé, il n’y a aucune importation en attente générée.</li><li>NoAddAndDeleteConfirmation – lorsqu’un objet est créé ou supprimé, il n’y a aucune importation en attente générée.</li>
Utiliser un nom unique comme point d’ancrage | Si le Style de nom unique est défini sur LDAP, l’attribut d’ancrage pour l’espace de connecteur est également le nom unique.
Opérations simultanées de plusieurs connecteurs | Lors de la vérification, plusieurs connecteurs de Windows PowerShell peuvent s’exécuter simultanément.
Partitions | Lors de la vérification, le connecteur prend en charge plusieurs partitions et la découverte de la partition.
Hiérarchie | Lors de la vérification, le connecteur prend en charge une structure hiérarchique de style LDAP.
Activer l’importation | Lors de la vérification, le connecteur importe des données via des scripts d’importation.
Activer l’importation de Delta | Lors de la vérification, le connecteur peut demander des deltas des scripts d’importation.
Activer l’exportation | Lors de la vérification, le connecteur exporte des données via des scripts d’exportation.
Activer l’exportation complète | Lors de la vérification, les scripts d’exportation prend en charge les exporter l’espace de connecteur entier. Pour utiliser cette option, l’activer l’exportation doit également être activée.
Aucune valeur de référence de la première exportation passe | Lors de la vérification, les attributs de référence sont exportés dans un deuxième passage de l’exportation.
Activer le changement de nom objet | Lors de la vérification, les noms uniques peuvent être modifiés.
Ajouter supprimer que remplacer | Lors de la vérification, supprimer-ajouter des opérations sont exportées sous la forme d’une seule valeur de remplacement.
Activer les opérations de mot de passe | Lors de la vérification, les scripts de synchronisation de mot de passe sont pris en charge.
Activer l’exportation de mot de passe dans la première passe | Lors de la vérification, les mots de passe définis lors de la mise en service sont exportés lorsque l’objet est créé.

### <a name="global-parameters"></a>Paramètres globaux
L’onglet Paramètres globaux dans le Concepteur de l’Agent de gestion vous permet de configurer les scripts Windows PowerShell qui sont exécutées par le connecteur. Vous pouvez également configurer des valeurs globales pour les paramètres de configuration personnalisés définis sur l’onglet connectivité.

**Découverte de partition**  
Une partition est un espace de noms distinct au sein d’un seul schéma partagé. Par exemple, dans Active Directory, chaque domaine est une partition au sein d’une forêt. Une partition est le regroupement logique pour l’importation et l’exportation des opérations. Importation et exportation ont partition un contexte et toutes les opérations qui se trouve dans ce contexte. Les partitions sont censés pour représenter une hiérarchie dans l’annuaire LDAP. Le nom unique d’une partition est utilisé dans une importation pour vérifier que tous les objets retournés sont dans la portée d’une partition. Le nom unique de la partition est également utilisé au cours de la mise en service du métaverse à l’espace de connecteur pour déterminer la partition de qu'un objet doit être associé au cours de l’exportation.

Le script de découverte de partition reçoit les paramètres suivants du connecteur :

Nom | Type de données | Description
--- | --- | ---
ConfigParameters  | [KeyedCollection][keyk][string, [ConfigParameter][cp]] | Tableau de paramètres de configuration pour le connecteur.
Informations d’identification | [PSCredential][pscred] | Contient des informations d’identification entrées par l’administrateur sur l’onglet connectivité.

Le script doit renvoyer un soit une seule [Partition] [ part] objet ou une liste [T] des objets Partition dans le pipeline.

**Découverte de la hiérarchie**  
Le script de découverte de hiérarchie n’est utilisé que lorsque le Style de nom unique est LDAP. Le script est utilisé pour vous permettent de parcourir et de sélectionner un ensemble de conteneurs qui est considéré comme dans ou hors de portée d’importation et d’exportation des opérations. Le script doit uniquement fournir une liste des nœuds qui sont des enfants directs du nœud racine transmis au script.

Le script de découverte de hiérarchie reçoit les paramètres suivants du connecteur :

Nom | Type de données | Description
--- | --- | ---
ConfigParameters | [KeyedCollection][keyk][string, [ConfigParameter][cp]] | Tableau de paramètres de configuration pour le connecteur.
Informations d’identification | [PSCredential][pscred] | Contient des informations d’identification entrées par l’administrateur sur l’onglet connectivité.
ParentNode | [HierarchyNode][hn] | Le nœud racine de la hiérarchie sous lequel le script doit renvoyer les enfants directs.

Le script doit renvoyer un objet HierarchyNode enfant unique ou une [T] de la liste des objets de HierarchyNode enfants au pipeline.

#### <a name="import"></a>Importation
Les connecteurs qui prennent en charge les opérations d’importation doivent implémenter trois scripts.

**Commencer l’importation**  
Le script d’importation de début est exécuté au début d’une étape d’importation à exécuter. Au cours de cette étape, vous pouvez établir une connexion avec le système source et effectuez les étapes préparatoires avant d’importer des données depuis le système connecté.

Le script d’importation de début reçoit les paramètres suivants du connecteur :

Nom | Type de données | Description
--- | --- | ---
ConfigParameters | [KeyedCollection][keyk][string, [ConfigParameter][cp]] | Tableau de paramètres de configuration pour le connecteur.
Informations d’identification | [PSCredential][pscred] | Contient des informations d’identification entrées par l’administrateur sur l’onglet connectivité.
OpenImportConnectionRunStep | [OpenImportConnectionRunStep][oicrs] | Informe le script sur le type d’importation exécutée (delta ou complète), partition, hiérarchie, filigrane et la taille de la page attendue.
Types de | [Schéma][schema] | Schéma pour l’espace du connecteur qui est importé.

Le script doit renvoyer une seule [OpenImportConnectionResults] [ oicres] objet au pipeline, par exemple :`Write-Output (New-Object Microsoft.MetadirectoryServices.OpenImportConnectionResults)`

**Importation de données**  
Le script d’importation de données est appelé par le connecteur jusqu'à ce que le script indique qu’il n’existe plus aucune donnée à importer. Le connecteur de Windows PowerShell a une taille de page d’objets 9 999. Si votre script renvoie les objets de plus de 9 999 pour l’importation, vous devez prendre en charge l’échange. Expose le connecteur une propriété de données personnalisé que vous pouvez utiliser pour une banque d’un filigrane afin que chaque fois que le script d’importation de données est appelée, votre script reprend l’importation d’objets où il s’était arrêté.

Le script d’importation de données reçoit les paramètres suivants du connecteur :

Nom | Type de données | Description
--- | --- | ---
ConfigParameters | [KeyedCollection][keyk][string, [ConfigParameter][cp]] | Tableau de paramètres de configuration pour le connecteur.
Informations d’identification | [PSCredential][pscred] | Contient des informations d’identification entrées par l’administrateur sur l’onglet connectivité.
GetImportEntriesRunStep | [ImportRunStep][irs] | Contient le filigrane (CustomData) qui peut être utilisé lors de la réserve paginée importations et aux importations de delta.
OpenImportConnectionRunStep | [OpenImportConnectionRunStep][oicrs] | Informe le script sur le type d’importation exécutée (delta ou complète), partition, hiérarchie, filigrane et la taille de la page attendue.
Types de | [Schéma][schema] | Schéma pour l’espace du connecteur qui est importé.

Le script d’importation de données doit écrire une liste [[CSEntryChange][csec]] objet au pipeline. Cette collection est composée de CSEntryChange les attributs qui représentent chaque objet importé. Lors d’une exécution d’importation complète, cette collection doit avoir un jeu complet d’objets CSEntryChange ayant tous les attributs pour chaque objet. Au cours d’une importation Delta, l’objet CSEntryChange doit contenir les deltas de niveau d’attribut pour chaque objet à importer, ou une représentation complète des objets qui ont été modifiés (Replace mode).

**Importation de fin**  
À l’issue de l’importation de s’exécutée, le script d’importation de fin est exécuté. Ce script doit effectuer les tâches de nettoyage nécessaires (par exemple, fermer les connexions aux systèmes) et répondre aux pannes.

Le script d’importation de fin reçoit les paramètres suivants du connecteur :

Nom | Type de données | Description
--- | --- | ---
ConfigParameters | [KeyedCollection][keyk][string, [ConfigParameter][cp]] | Tableau de paramètres de configuration pour le connecteur.
Informations d’identification | [PSCredential][pscred] | Contient des informations d’identification entrées par l’administrateur sur l’onglet connectivité.
OpenImportConnectionRunStep | [OpenImportConnectionRunStep][oicrs] | Informe le script sur le type d’importation exécutée (delta ou complète), partition, hiérarchie, filigrane et la taille de la page attendue.
CloseImportConnectionRunStep | [CloseImportConnectionRunStep][cecrs] | Informe le script sur la raison pour laquelle que l’importation a été interrompue.

Le script doit renvoyer une seule [CloseImportConnectionResults] [ cicres] objet au pipeline, par exemple :`Write-Output (New-Object Microsoft.MetadirectoryServices.CloseImportConnectionResults)`

#### <a name="export"></a>Exporter
Identique à l’architecture de l’importation du connecteur, connecteurs qui prennent en charge d’exportation doivent implémenter trois scripts.

**Commencer l’exportation**  
Le script d’exportation de début est exécuté au début d’une étape d’exportation à exécuter. Au cours de cette étape, vous pouvez établir une connexion avec le système source et effectuer toutes les étapes préparatoires avant d’exporter les données vers le système connecté.

Le script d’exportation de début reçoit les paramètres suivants du connecteur :

Nom | Type de données | Description
--- | --- | ---
ConfigParameters | [KeyedCollection][keyk][string, [ConfigParameter][cp]] | Tableau de paramètres de configuration pour le connecteur.
Informations d’identification | [PSCredential][pscred] | Contient des informations d’identification entrées par l’administrateur sur l’onglet connectivité.
OpenExportConnectionRunStep | [OpenExportConnectionRunStep][oecrs] | Informe le script sur le type d’exportation exécutée (delta ou complète), partition, hiérarchie et la taille de la page attendue.
Types de | [Schéma][schema] | Schéma pour l’espace du connecteur qui est exporté.

Le script ne doit pas retourner de sortie pour le pipeline.

**Exporter des données**  
Le Service de synchronisation appelle le script d’exportation de données autant de fois que nécessaire traiter toutes les exportations en attente. Si l’espace de connecteur possède plusieurs exportations en attente à la taille de page du connecteur, le script de données d’exportation peut être appelé plusieurs fois et éventuellement plusieurs fois pour le même objet.

Le script d’exportation données reçoit les paramètres suivants du connecteur :

Nom | Type de données | Description
--- | --- | ---
ConfigParameters | [KeyedCollection][keyk][string, [ConfigParameter][cp]] | Tableau de paramètres de configuration pour le connecteur.
Informations d’identification | [PSCredential][pscred] | Contient des informations d’identification entrées par l’administrateur sur l’onglet connectivité.
CSEntries | IList[CSEntryChange][csec] | Liste de tous les objets espace de connecteur avec en attente des exportations à traiter au cours de cette étape.
OpenExportConnectionRunStep | [OpenExportConnectionRunStep][oecrs] | Informe le script sur le type d’exportation exécutée (delta ou complète), partition, hiérarchie et la taille de la page attendue.
Types de | [Schéma][schema] | Schéma pour l’espace du connecteur qui est exporté.

Le script de données d’exportation doit retourner un [PutExportEntriesResults] [ peeres] objet au pipeline. Cet objet n’a pas besoin d’inclure des informations de résultat pour chaque connecteur exporté, sauf si une erreur ou une modification de l’attribut de point d’ancrage se produit. Par exemple, pour renvoyer un objet PutExportEntriesResults pour le pipeline :`Write-Output (New-Object Microsoft.MetadirectoryServices.PutExportEntriesResults)`

**Exportation de fin**  
À la fin de l’exportation à l’exécution, le script exporter de fin à l’exécution. Ce script doit effectuer les tâches de nettoyage nécessaires (par exemple, fermer les connexions aux systèmes) et répondre aux pannes.

Le script d’exportation fin reçoit les paramètres suivants du connecteur :

Nom | Type de données | Description
--- | --- | ---
ConfigParameters | [KeyedCollection][keyk][string, [ConfigParameter][cp]] | Tableau de paramètres de configuration pour le connecteur.
Informations d’identification | [PSCredential][pscred] | Contient des informations d’identification entrées par l’administrateur sur l’onglet connectivité.
OpenExportConnectionRunStep | [OpenExportConnectionRunStep][oecrs] | Informe le script sur le type d’exportation exécutée (delta ou complète), partition, hiérarchie et la taille de la page attendue.
CloseExportConnectionRunStep | [CloseExportConnectionRunStep][cecrs] | Informe le script sur la raison pour laquelle que l’exportation a été interrompue.

Le script ne doit pas retourner de sortie pour le pipeline.

#### <a name="password-synchronization"></a>Synchronisation de mot de passe
Connecteurs de Windows PowerShell peuvent servir en tant que cible pour des modifications/réinitialisations de mot de passe.

Le script de mot de passe reçoit les paramètres suivants du connecteur :

Nom | Type de données | Description
--- | --- | ---
ConfigParameters | [KeyedCollection][keyk][string, [ConfigParameter][cp]] | Tableau de paramètres de configuration pour le connecteur.
Informations d’identification | [PSCredential][pscred] | Contient des informations d’identification entrées par l’administrateur sur l’onglet connectivité.
Partition | [Partition][part] | Partition d’annuaire qui se trouve dans le CSEntry.
CSEntry | [CSEntry][cse] | Entrée d’espace de connecteur pour l’objet qui est reçu d’un changement de mot de passe ou de réinitialisation.
Type d’opération | Chaîne | Indique si l’opération est une réinitialisation (**SetPassword**) ou une modification (**ChangePassword**).
PasswordOptions | [PasswordOptions][pwdopt] | Indicateurs qui spécifient le mot de passe prévu réinitialiser le comportement. Ce paramètre est uniquement disponible si le type d’opération est **SetPassword**.
OldPassword | Chaîne | Rempli avec ancien mot de passe l’objet pour que les modifications de mot de passe. Ce paramètre est uniquement disponible si le type d’opération est **ChangePassword**.
NewPassword | Chaîne | Rempli avec nouveau mot de passe l’objet que le script doit définir.

Le script de mot de passe n’est pas attendu pour renvoyer tous les résultats dans le pipeline Windows PowerShell. Si une erreur se produit dans le script de mot de passe, le script doit lever une des exceptions suivantes pour informer le Service de synchronisation sur le problème :

- [PasswordPolicyViolationException] [ pwdex1] – levée si le mot de passe ne respecte pas la stratégie de mot de passe dans le système connecté.
- [PasswordIllFormedException] [ pwdex2] – levée si le mot de passe n’est pas acceptable pour le système connecté.
- [PasswordExtension] [ pwdex3] – levée pour toutes les autres erreurs dans le script de mot de passe.

## <a name="sample-connectors"></a>Connecteurs de l’échantillon
Pour une présentation complète des connecteurs exemple disponible, voir [Windows PowerShell connecteur exemple Connecteur Collection][samp].

## <a name="other-notes"></a>Autres notes

### <a name="additional-configuration-for-impersonation"></a>Configuration supplémentaire pour l’emprunt d’identité
Accorder à l’utilisateur qui est représenté les autorisations suivantes sur le serveur du Service de synchronisation :

Accès en lecture aux clés de Registre suivantes :

- HKEY_USERS\\[SynchronizationServiceServiceAccountSID] \Software\Microsoft\PowerShell
- HKEY_USERS\\[SynchronizationServiceServiceAccountSID] \Environment

Pour déterminer l’identificateur de sécurité (SID) du compte de service Service de synchronisation, exécutez les commandes PowerShell suivantes :

```
$account = New-Object System.Security.Principal.NTAccount "<domain>\<username>"
$account.Translate([System.Security.Principal.SecurityIdentifier]).Value
```

Accès en lecture aux dossiers de système de fichier suivantes :

- %ProgramFiles%\Microsoft forefront Identity Manager\2010\Synchronization Service\Extensions
- %ProgramFiles%\Microsoft forefront Identity Manager\2010\Synchronization Service\ExtensionsCache
- %ProgramFiles%\Microsoft forefront Identity Manager\2010\Synchronization Service\MaData\\{ConnectorName}

Remplacez le nom du connecteur de Windows PowerShell pour l’espace réservé {ConnectorName}.

## <a name="troubleshooting"></a>Résolution des problèmes

-   Pour plus d’informations sur la façon d’activer la journalisation résoudre les problèmes de connecteur, voir [comment activer le suivi ETW des connecteurs](http://go.microsoft.com/fwlink/?LinkId=335731).

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[cpp]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.configparameterpage.aspx
[keyk]: https://msdn.microsoft.com/library/ms132438.aspx
[cp]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.configparameter.aspx
[pscred]: https://msdn.microsoft.com/library/system.management.automation.pscredential.aspx
[schema]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.schema.aspx
[schemaT]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.schematype.aspx
[schemaA]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.schemaattribute.aspx
[dnstyle]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.madistinguishednamestyle.aspx
[exportT]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.maexporttype.aspx
[DataNorm]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.manormalizations.aspx
[oconf]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.maobjectconfirmation.aspx
[dw]: https://msdn.microsoft.com/library/windows/desktop/aa378184.aspx
[part]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.partition.aspx
[hn]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.hierarchynode.aspx
[oicrs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.openimportconnectionrunstep.aspx
[cecrs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.closeexportconnectionrunstep.aspx
[oicres]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.openimportconnectionresults.aspx
[cecrs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.closeexportconnectionrunstep.aspx
[cicres]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.closeimportconnectionresults.aspx
[oecrs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.openexportconnectionrunstep.aspx
[irs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.importrunstep.aspx
[cse]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.csentry.aspx
[csec]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.csentrychange.aspx
[peeres]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.putexportentriesresults.aspx
[pwdopt]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.passwordoptions.aspx
[pwdex1]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.passwordpolicyviolationexception.aspx
[pwdex2]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.passwordillformedexception.aspx
[pwdex3]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.passwordextensionexception.aspx
[samp]: http://go.microsoft.com/fwlink/?LinkId=394291
