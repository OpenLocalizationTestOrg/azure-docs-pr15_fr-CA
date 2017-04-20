<properties
   pageTitle="Recommandé de conventions d’attribution de noms pour les ressources Azure | Conseils | Microsoft Azure"
   description="Conventions d’appellation recommandées pour les ressources d’Azure. Le nom des machines virtuelles, comptes de stockage, les réseaux, les réseaux virtuels, sous-réseaux et autres entités Azure"
   services=""
   documentationCenter="na"
   authors="bennage"
   manager="marksou"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/27/2016"
   ms.author="christb"/>
   
# <a name="recommended-naming-conventions-for-azure-resources"></a>Conventions d’appellation pour Azure ressources recommandées

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

Le choix d’un nom de ressource de Microsoft Azure est important parce que :

- Il est difficile de modifier un nom.
- Les noms doivent répondre aux exigences de leur type de ressource spécifique.

Conventions d’affectation de noms cohérentes rendre les ressources plus faciles à localiser. Ils peuvent également indiquer le rôle d’une ressource dans une solution.

Cet article est un résumé des règles d’affectation de noms et restrictions des ressources Azure et un ensemble de recommandations pour les conventions d’affectation de noms de base.  Vous pouvez utiliser ces recommandations comme point de départ pour vos propres conventions spécifiques à vos besoins.  

La clé du succès avec les conventions d’affectation de noms est mise en place et leur suite au sein de vos applications et les organisations. 

## <a name="naming-subscriptions"></a>Abonnements d’attribution de noms

Lorsque vous nommez des abonnements Azure, les noms détaillés permettent de comprendre le contexte et l’objectif de chaque abonnement clair.  Lorsque vous travaillez dans un environnement comportant un grand nombre de souscriptions, suivant une convention d’affectation de noms partagée peut améliorer la clarté.

Un modèle recommandé pour les abonnements d’attribution de noms est la suivante :

`<Company> <Department (optional)> <Product Line (optional)> <Environment>`

- Société sera généralement le même pour chaque abonnement. Cependant, certaines sociétés peuvent avoir des sociétés enfant au sein de la structure organisationnelle. Ces sociétés peuvent être gérées par un groupe informatique central. Dans ces cas, ils pourraient être différenciées en ayant à la fois le nom de la société mère (*Contoso*) et le nom de société enfant (*Vent du Nord*).

- Département est un nom au sein de l’organisation où un groupe de personnes. Cet élément dans l’espace de noms comme facultatifs.

- Gamme de produits est un nom spécifique d’un produit ou d’une fonction qui est effectuée à partir du service.
Cela est généralement facultative pour les applications et les services interne. Toutefois, il est fortement recommandé d’utiliser pour les services Web public nécessitant une separation facile et identification (par exemple une séparation claire des enregistrements de facturation).

- L’environnement est le nom qui décrit le cycle de vie du déploiement des applications ou des services, tels que le développement, assurance qualité ou nomenclature de production.

| Société | Département | Ligne de produit ou de Service | Environnement | Nom complet  |
----------| ---------- | ----------------------- | ----------- | ---------- |
| Contoso | SocialGaming | AwesomeService | Production | Production de AwesomeService de SocialGaming de Contoso |
| Contoso | SocialGaming | AwesomeService | Dév. | Développement de AwesomeService de SocialGaming de Contoso |
| Contoso | IL | InternalApps | Production | INFORMATIQUE de Contoso InternalApps Production |
| Contoso | IL | InternalApps | Dév. | Contoso informatique développement de InternalApps |

<!-- TODO; include more information about organizing subscriptions for application deployment, pods, etc. -->

## <a name="use-affixes-to-avoid-ambiguity"></a>Permet d’éviter toute ambiguïté Affixes

Lorsque vous nommez des ressources dans Azure, il est recommandé d’utiliser des préfixes ou des suffixes courants pour identifier le type et le contexte de la ressource.  Lors de toutes les informations sur le type, les métadonnées, contexte, est disponible par programmation, application communes affixes simplifie l’identification visuelle.  Lors de l’incorporation des affixes dans votre convention d’affectation de noms, il est important d’indiquer clairement si l’apposer est au début du nom (préfixe) ou à la fin (suffixe).  

Par exemple, voici deux noms possibles pour un service d’hébergement d’un moteur de calcul :

- SvcCalculationEngine (préfixe)
- CalculationEngineSvc (suffixe)

Affixes peuvent faire référence aux différents aspects qui décrivent les ressources. Le tableau suivant montre quelques exemples utilisés en général.

| Aspect | Exemple | Notes |
| ------ | ------- | ----- |
| Environnement | développement, production, assurance qualité | Identifie l’environnement de la ressource |
| Emplacement | UW (nous ouest), ue (nous East) | Identifie la zone dans laquelle la ressource est déployée. |
| Instance | 01, 02 | Pour les ressources qui ont plus d’une instance nommée (serveurs web, etc.). |
| Produit ou Service | service | Identifie le produit, une application ou un service qui prend en charge de la ressource |
| Rôle | SQL, web, messagerie | Identifie le rôle de la ressource associée |

Lors du développement d’une convention d’affectation de noms spécifique pour votre société ou des projets, il est important de choisir un ensemble commun d’affixes ainsi que leur position (préfixe ou suffixe).

## <a name="naming-rules-and-restrictions"></a>Règles d’affectation de noms et Restrictions

Chaque type de ressource ou un service dans Azure applique un ensemble de nommer les restrictions et la portée ; toute convention de nommage ou le modèle doit respecter les règles de nommage requis et l’étendue.  Par exemple, alors que le nom d’un ordinateur virtuel correspond à un nom DNS (et par conséquent doit être unique dans l’ensemble de Azure), le nom d’un VNET portée est limitée au groupe de ressources où elle est créée.

En règle générale, évitez les caractères spéciaux (`-` ou `_`) comme le premier ou le dernier caractère dans n’importe quel nom. Ces caractères entraîne la plupart des règles de validation échoue.

| Catégorie | Service ou l’entité | Champ d’application | Longueur | Casse | Caractères valides | Modèle suggéré | Exemple |
| ------------- | ----------------- | ----- | ------ | ------ | ---------------- | ----------------- | ------- |
| Groupe de ressources | Groupe de ressources | Global | 1-64 | Casse | Alphanumérique, le trait de soulignement et le tiret, | `<service short name>-<environment>-rg` | `profx-prod-rg` |
| Groupe de ressources | Ensemble de disponibilité | Groupe de ressources | 1-80 | Casse | Alphanumérique, le trait de soulignement et le tiret, | `<service-short-name>-<context>-as` | `profx-sql-as` |
| Général | Balise | Entité associée | 512 (nom), 256 (valeur) | Casse | Alphanumérique | `"key" : "value"` | `"department" : "Central IT"` |
| Calculer | Machine virtuelle | Groupe de ressources | 1-15 | Casse | Alphanumérique, le trait de soulignement et le tiret, | `<name>-<role>-<instance>` | `profx-sql-001` |
| Stockage | Nom du compte de stockage (données) | Global | 3-24 | Bas de casse | Alphanumérique | `<service short name><type><number>` | `profxdata001` |
| Stockage | Nom du compte de stockage (disques) | Global | 3-24 | Bas de casse | Alphanumérique | `<vm name without dashes>st<number>` | `profxsql001st0` |
| Stockage | Nom du conteneur | Compte de stockage | 3-63 |   Bas de casse | Alphanumérique et tiret | `<context>` | `logs` |
| Stockage | Nom de l’objet BLOB | Conteneur | 1-1024 | Distinction majuscules/minuscules | Tout caractère de l’URL | `<variable based on blob usage>` | `<variable based on blob usage>` |
| Stockage | Nom de la file d’attente | Compte de stockage | 3-63 | Bas de casse | Alphanumérique et tiret | `<service short name>-<context>-<num>` | `awesomeservice-messages-001` |
| Stockage | Nom de la table | Compte de stockage | 3-63 |Casse | Alphanumérique | `<service short name>-<context>` | `awesomeservice-logs` |
| Stockage | Nom de fichier | Compte de stockage | 3-63 | Bas de casse | Alphanumérique | `<variable based on blob usage>` | `<variable based on blob usage>` |
| Mise en réseau | Réseau virtuel (VNet) | Groupe de ressources | 2-64 | Non-respect de la casse | Alphanumérique, des tirets, des traits de soulignement et période | `<service short name>-[section]-vnet` | `profx-vnet` |
| Mise en réseau | Sous-réseau | VNet du parent | 2-80 | Non-respect de la casse | Alphanumérique, le trait de soulignement, tiret et période | `<role>-subnet` | `gateway-subnet` |
| Mise en réseau | Interface réseau | Groupe de ressources | 1-80 | Non-respect de la casse | Alphanumérique, des tirets, des traits de soulignement et période | `<vmname>-<num>nic` | `profx-sql1-1nic` |
| Mise en réseau | Groupe de sécurité réseau | Groupe de ressources | 1-80 | Non-respect de la casse | Alphanumérique, des tirets, des traits de soulignement et période | `<service short name>-<context>-nsg` | `profx-app-nsg` |
| Mise en réseau | Règle du groupe de sécurité réseau | Groupe de ressources | 1-80 | Non-respect de la casse | Alphanumérique, des tirets, des traits de soulignement et période | `<descriptive context>` | `sql-allow` |
| Mise en réseau | Adresse IP publique | Groupe de ressources | 1-80 | Non-respect de la casse | Alphanumérique, des tirets, des traits de soulignement et période | `<vm or service name>-pip` | `profx-sql1-pip` |
| Mise en réseau | L’équilibreur de charge | Groupe de ressources | 1-80 | Non-respect de la casse | Alphanumérique, des tirets, des traits de soulignement et période | `<service or role>-lb` | `profx-lb` |
| Mise en réseau | Charger la configuration de règles équilibrées | L’équilibreur de charge | 1-80 | Non-respect de la casse | Alphanumérique, des tirets, des traits de soulignement et période | `descriptive context` | `http` |

<!-- TODO fill in the rest of these resources
| Networking | Azure Application Gateway | Resource Group | 1-80 | Case-insensitive | Alphanumeric, dash, underscore, and period | `<service or role>-aag` | `profx-aag`
| Networking | Azure Application Gateway Connection | Azure Application Gateway | 1-80 | Case-insensitive | Alphanumeric, dash, underscore, and period | `` | TODO
| Networking | Traffic Manager Profile | Resource Group | 1-80 | Case-insensitive | Alphanumeric, dash, underscore, and period | TODO | TODO
-->

## <a name="organizing-resources-with-tags"></a>Organisation des ressources avec des balises

Le Gestionnaire de ressources Azure prend en charge le repérage des entités avec des chaînes de texte arbitraire pour identifier le contexte et simplifier l’automation.  Par exemple, la balise `"sqlVersion: "sql2014ee"` a pu identifier les ordinateurs virtuels dans un déploiement 2014 Enterprise Edition de SQL Server en cours d’exécution pour l’exécution d’un script automatisé à leur encontre.  Balises doivent être utilisés pour compléter et améliorer le contexte sur le côté des conventions d’affectation de noms choisi.

> [AZURE.TIP]Un autre avantage des balises est que les balises span des groupes de ressources, ce qui vous permet de lier et d’établir une corrélation entre les entités dans des environnements disparates.

Chaque ressource ou un groupe de ressources peut avoir un maximum de **15** balises. Le nom de balise est limité à 512 caractères, et la valeur de la balise est limitée à 256 caractères.

Pour plus d’informations sur les ressources de balisage, consultez [en utilisant des balises pour organiser vos ressources Azure](../resource-group-using-tags.md).

Les cas d’usage de balisage courants sont les suivants :

- **Facturation**; Regrouper les ressources et en les associant à des frais de facturation ou de sauvegarder des codes.
- **Identification de contexte de Service**; Identifier les groupes de ressources entre des groupes de ressources pour les opérations courantes et de regroupement
- **Contrôle d’accès et le contexte de sécurité**; Identification du rôle d’administration basée sur le porte-documents, système, service, application, instance, etc..

> [AZURE.TIP]La balise de début - balise souvent.  Pour disposer d’une base de balisage de modèle en place de meilleures et évoluent dans le temps, plutôt que de devoir mettre après le fait.  

Un exemple de certaines des approches courantes marquage :

| Nom de la balise | Clé | Exemple | Commentaire |
| -------- | --- | ------- | ------- |
| Nomenclature / internes refacturation ID | billTo  | `IT-Chargeback-1234` | Une e/s interne ou un code de facturation |
| Opérateur ou directement responsables individuels (DRI) | managedBy | `joe@contoso.com`  | Alias ou adresse e-mail |
| Nom du projet | nom du projet | `myproject`  | Nom de la ligne du projet ou du produit |
| Version du projet | version du projet | `3.4`  | Version de la ligne du projet ou du produit |
| Environnement | environnement | `<Production, Staging, QA >` | Identificateur de l’environnement | 
| Couche | couche | `Front End, Back End, Data` | Identification de la couche ou rôle/contexte |
| Profilage des données | dataProfile | `Public, Confidential, Restricted, Internal` | Sensibilité des données stockées dans la ressource |
 
## <a name="tips-and-tricks"></a>Conseils et astuces

Certains types de ressources peuvent nécessiter des soins supplémentaires sur les noms et les conventions.

### <a name="virtual-machines"></a>Ordinateurs virtuels

En particulier dans les grandes topologies, dénomination soigneusement les machines virtuelles rationalise identifiant le rôle et le but de chaque ordinateur et l’activation des scripts plus prévisibles.

> [AZURE.WARNING]Chaque ordinateur virtuel dans Azure dispose d’un nom de ressource Azure et un nom d’hôte de système d’exploitation.  
> Si le nom de la ressource et le nom de l’hôte sont différents, la gestion des ordinateurs virtuels peuvent être problématique et doivent être évitées.
> Par exemple, si un ordinateur virtuel est créé à partir d’un .vhd déjà contient un système d’exploitation configuré avec un nom d’hôte.

- [Conventions d’affectation de noms pour les machines virtuelles de Windows Server](https://support.microsoft.com/en-us/kb/188997)

<!-- TODO - recommendations on naming VMs. -->

### <a name="storage-accounts-and-storage-entities"></a>Comptes de stockage et d’entités de stockage

Il existe deux principaux exemples d’utilisation pour les comptes de stockage - sauvegarde des disques pour les machines virtuelles et de stocker des données dans le BLOB, les files d’attente et les tables.  Comptes de stockage utilisés pour les disques de l’ordinateur virtuel doivent suivre la convention d’affectation de noms de les associer avec le nom de la machine virtuelle du parent (et avec le besoin potentiel de plusieurs comptes de stockage pour les éditions de machine virtuelle haut de gamme, s’appliquent également un suffixe de numéro).

> [AZURE.TIP]Comptes de stockage - si c’est le cas pour les données ou les disques - doivent suivre une convention d’affectation de noms qui permet à plusieurs comptes de stockage à être exploitées (c'est-à-dire toujours à l’aide d’un suffixe numérique).

Il est possible de configurer un nom de domaine personnalisé pour accéder aux données blob dans votre compte de stockage Azure.
Le point de terminaison par défaut pour le service d’objet Blob est `https://mystorage.blob.core.windows.net`.

Mais si vous mappez un domaine personnalisé (par exemple www.contoso.com) au point de terminaison blob pour votre compte de stockage, vous pouvez également accéder à blob de données dans votre compte de stockage à l’aide de ce domaine. Par exemple, avec un nom de domaine personnalisé, `http://mystorage.blob.core.windows.net/mycontainer/myblob` Impossible d’y accéder en tant que `http://www.contoso.com/mycontainer/myblob`.

Pour plus d’informations sur la configuration de cette fonctionnalité, reportez-vous à [configurer un nom de domaine personnalisé pour votre point de terminaison du stockage Blob](../storage/storage-custom-domain-name.md).

Pour plus d’informations sur la dénomination des BLOB, les conteneurs et les tables :

- [Dénomination et faisant référence à des conteneurs, des objets BLOB et métadonnées](https://msdn.microsoft.com/library/dd135715.aspx)
- [Noms des files d’attente et de métadonnées](https://msdn.microsoft.com/library/dd179349.aspx)
- [Nommer des Tables](https://msdn.microsoft.com/library/azure/dd179338.aspx)

Un nom d’objet blob peut contenir n’importe quelle combinaison de caractères, mais les caractères URL réservés doivent être échappés correctement. Évitez les noms d’objet blob qui se terminent par un point (.), une barre oblique (/), ou d’une séquence ou d’une combinaison des deux. Par convention, la barre oblique sert de séparateur de répertoire **virtuel** . N’utilisez pas une barre oblique (\) un nom d’objet blob. Le client API peut lui permettre, mais alors pas correctement de hachage, et les signatures ne correspondront pas.

Il n’est pas possible de modifier le nom d’un compte de stockage ou un conteneur après que qu’il a été créé.
Si vous souhaitez utiliser un nouveau nom, vous devez le supprimer et créer un nouveau.

> [AZURE.TIP] Il est recommandé que vous établissez une convention d’affectation de noms pour tous les types et les comptes de stockage avant de vous lancer dans le développement d’un nouveau service ou une application.

## <a name="example---deploying-an-n-tier-service"></a>Exemple : déploiement d’un service à n niveaux

Dans cet exemple, nous définissons une configuration n-couche service, IIS serveurs frontaux (hébergés dans les ordinateurs virtuels de Windows Server), avec SQL Server (hébergé dans deux Windows Server VM), consistant en un cluster ElasticSearch (hébergé dans les ordinateurs virtuels de Linux 6) et les comptes de stockage associée, réseaux virtuels, ressource de groupe et l’équilibrage de charge.

Nous allons commencer par définir les conventions contextuelles pour cette application :

| Entité | Convention | Description  |
| ------ | ---------- | ------------ |  
| Nom du service | `profx` | Le nom court de l’application ou le service en cours de déploiement |
| Environnement | `prod` | C’est pour le déploiement de production (et non de l’assurance qualité, tests, etc..) |

À partir de cette base nous pouvons ensuite mapper les conventions pour chacun des types de ressources :

| Type de ressource | Base de la convention | Exemple |
| ------------- | --------------- | ------- |
| Abonnement | `<Company> <Department (optional)> <Product Line (optional)> <Environment>` | `Contoso IT InternalApps Profx Production` |
| Groupe de ressources | `servicename-rg` | `profx-rg` |
| Réseau virtuel | `servicename-vnet` | `profx-vnet` |
| Sous-réseau | `role-subnet` | `sql-vnet` |
| L’équilibreur de charge | `servicename-lb` | `profx-lb` |
| Machine virtuelle | `servicename-role[number]` | `profx-sql0` |
| Compte de stockage | `<vmnamenodashes>st<num>` | `profxsql0st0` |

Comme indiqué dans la suite du diagramme :

![diagramme de topologie d’application] (media/guidance-naming-conventions/guidance-naming-convention-example.png "Application exemple de topologie")

## <a name="sample---azure-cli-script-for-deploying-the-sample-above"></a>Exemple - script CLI Azure pour le déploiement de l’exemple ci-dessus

```bash
#!/bin/sh

#####################################################################
# Sample script using the Azure CLI to build out an application 
# demonstrating naming conventions.  
#
# Note; this script is not intended for production deployment, as it does 
# not create availability sets, configure network security rules, etc.
#####################################################################

# Set up variables to build out the naming conventions for deploying
# the cluster  
LOCATION=eastus2
APP_NAME=profx
ENVIRONMENT=prod
USERNAME=testuser
PASSWORD="testpass"

# Set up the tags to associate with items in the application
TAG_BILLTO="InternalApp-ProFX-12345"
TAGS="billTo=${TAG_BILLTO}"

# Explicitly set the subscription to avoid confusion as to which subscription
# is active/default
SUBSCRIPTION=3e9c25fc-55b3-4837-9bba-02b6eb204331

# Set up the names of things using recommended conventions
RESOURCE_GROUP="${APP_NAME}-${ENVIRONMENT}-rg"
VNET_NAME="${APP_NAME}-vnet"

# Set up the postfix variables attached to most CLI commands
POSTFIX="--resource-group ${RESOURCE_GROUP} --location ${LOCATION} --subscription ${SUBSCRIPTION}"

##########################################################################################
# Set up the VM conventions for Linux and Windows images

# For Windows, get the list of URN's via 
# azure vm image list ${LOCATION} MicrosoftWindowsServer WindowsServer 2012-R2-Datacenter
WINDOWS_BASE_IMAGE=MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20160126

# For Linux, get the list or URN's via 
# azure vm image list ${LOCATION} canonical ubuntuserver
LINUX_BASE_IMAGE=canonical:ubuntuserver:16.04.0-DAILY-LTS:16.04.201602130

#########################################################################################
## Define functions 
create_vm ()
{
    vm_name=$1
    vnet_name=$2
    subnet_name=$3
    os_type=$4
    vhd_path=$5
    vm_size=$6
    diagnostics_storage=$7

    # Create the network interface card for this VM
    azure network nic create --name "${vm_name}-0nic" --subnet-name ${subnet_name} --subnet-vnet-name ${vnet_name} \
        --tags="${TAGS}" ${POSTFIX}

    # Create the storage account for this vm's disks (premium locally redundant storage -> PLRS)
    # Note the ${var//-/} syntax to remove dashes from the vm name
    storage_account_name=${vm_name//-/}st01
    azure storage account create --type=PLRS --tags "${TAGS}" ${POSTFIX} "${storage_account_name}"

    # Map the name of the diagnostics storage account to a blob URI for boot diagnostics
    # This is (currently) required when deploying with a named premium storage account 
    diag_blob="https://${diagnostics_storage}.blob.core.windows.net/"

    # Create the VM
    azure vm create --name ${vm_name} --nic-name "${vm_name}-0nic" --os-type ${os_type} \
        --image-urn ${vhd_path} --vm-size ${vm_size} --vnet-name ${vnet_name} --vnet-subnet-name ${subnet_name} \
        --storage-account-name "${storage_account_name}" --storage-account-container-name vhds --os-disk-vhd "${vm_name}-osdisk.vhd" \
        --admin-username "${USERNAME}" --admin-password "${PASSWORD}" \
        --boot-diagnostics-storage-uri "${diag_blob}" \
        --tags="${TAGS}" ${POSTFIX} 
}

###################################################################################################
# Create resources

# Step 1 - create the enclosing resource group
azure group create --name "${RESOURCE_GROUP}" --location "${LOCATION}" --tags "${TAGS}" --subscription "${SUBSCRIPTION}"

# Step 2 - create the network security groups

# Step 3 - create the networks (VNet and subnets)
azure network vnet create --name "${VNET_NAME}" --address-prefixes="10.0.0.0/8" --tags "${TAGS}" ${POSTFIX}
# TODO - does subnet support tagging?
azure network vnet subnet create --name gateway-subnet --vnet-name "${VNET_NAME}" --address-prefix="10.0.1.0/24" --resource-group "${RESOURCE_GROUP}" --subscription ${SUBSCRIPTION}
azure network vnet subnet create --name es-master-subnet --vnet-name "${VNET_NAME}" --address-prefix="10.0.2.0/24" --resource-group "${RESOURCE_GROUP}" --subscription ${SUBSCRIPTION}
azure network vnet subnet create --name es-data-subnet --vnet-name "${VNET_NAME}" --address-prefix="10.0.3.0/24" --resource-group "${RESOURCE_GROUP}" --subscription ${SUBSCRIPTION}
azure network vnet subnet create --name sql-subnet --vnet-name "${VNET_NAME}" --address-prefix="10.0.4.0/24" --resource-group "${RESOURCE_GROUP}" --subscription ${SUBSCRIPTION}

# Step 4 - define the load balancer and network security rules
azure network lb create --name "${APP_NAME}-lb" ${POSTFIX}
# In a production deployment script, we'd create load balancer rules and 
# network security groups here

# Step 5 - create a diagnostics storage account
diagnostics_storage_account=${APP_NAME//-/}diag
azure storage account create --type=LRS --tags "${TAGS}" ${POSTFIX} "${diagnostics_storage_account}"

# Step 6.1 - Create the gateway VMs
for i in `seq 1 4`;
do
    create_vm "${APP_NAME}-gw${i}" "${APP_NAME}-vnet" "gateway-subnet" "Windows" "${WINDOWS_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}" 
done    

# Step 6.2 - Create the ElasticSearch master and data VMs
for i in `seq 1 3`;
do
    create_vm "${APP_NAME}-es-master${i}" "${APP_NAME}-vnet" "es-master-subnet" "Linux" "${LINUX_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}"
done
for i in `seq 1 3`;
do
    create_vm "${APP_NAME}-es-data${i}" "${APP_NAME}-vnet" "es-data-subnet" "Linux" "${LINUX_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}"
done

# Step 6.3 - Create the SQL VMs
create_vm "${APP_NAME}-sql0" "${APP_NAME}-vnet" "sql-subnet" "Windows" "${WINDOWS_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}"
create_vm "${APP_NAME}-sql1" "${APP_NAME}-vnet" "sql-subnet" "Windows" "${WINDOWS_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}"
```
