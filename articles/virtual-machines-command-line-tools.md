<properties
    pageTitle="Les commandes CLI Azure dans le mode de gestion de Service | Microsoft Azure"
    description="Commandes d’interface de ligne de commande (CLI) pour Azure dans le mode de gestion de Service pour gérer des déploiements dans le modèle de déploiement classique"
    services="virtual-machines-linux,virtual-machines-windows,mobile-services, cloud-services"
    documentationCenter=""
    authors="dlepow"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="multiple"
    ms.workload="multiple"
    ms.tgt_pltfrm="vm-multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/22/2016"
    ms.author="danlep"/>

# <a name="azure-cli-commands-in-azure-service-management-asm-mode"></a>Commandes CLI Azure en mode de gestion des services Azure (asm)

[AZURE.INCLUDE [learn-about-deployment-models](../includes/learn-about-deployment-models-classic-include.md)]Vous pouvez également [obtenir des informations sur toutes les commandes de modèle de gestionnaire de ressources](virtual-machines/azure-cli-arm-commands.md)et de l’infrastructure du langage commun permet de [migrer des ressources](virtual-machines/virtual-machines-linux-cli-migration-classic-resource-manager.md) à partir de standard pour le modèle de gestionnaire de ressources.

Cet article fournit la syntaxe et les options pour les commandes CLI d’Azure que vous utiliserez couramment pour créer et gérer des ressources Azure dans le modèle de déploiement classique. Pour accéder à ces commandes en exécutant la CLI en mode de gestion des services Azure (asm). Il ne s’agit pas d’une référence complète, et votre version de l’infrastructure du langage commun peut afficher légèrement différentes commandes ou paramètres. 

Pour la démarrer, premier [installer l’interface CLI d’Azure](xplat-cli-install.md) , [Connectez-vous à votre abonnement Azure](xplat-cli-connect.md).

Pour la syntaxe de la commande et les options de ligne de commande, tapez `azure help` ou, pour afficher l’aide pour une commande spécifique, `azure help [command]`. Également trouver des exemples de commandes CLI dans la documentation pour créer et gérer des services Azure spécifiques.

Les paramètres facultatifs sont affichés entre crochets (par exemple, `[parameter]`). Tous les autres paramètres sont obligatoires.

En plus spécifiques à la commande paramètres optionnels décrits ici, il existe trois paramètres facultatifs qui peuvent être utilisés pour afficher la sortie détaillée comme les options de la demande et des codes d’état. Le `-v` paramètre fournit une sortie détaillée et la `-vv` paramètre fournit encore plus d’informations détaillées. Le `--json` option renvoie le résultat au format json brut.

## <a name="setting-asm-mode"></a>Définition du mode asm

Utilisez la commande suivante pour activer les commandes de mode de gestion des services Azure CLI.

    azure config mode asm

>[AZURE.NOTE] Mode du Gestionnaire de ressources Azure et le mode de gestion des services Azure de l’infrastructure du langage commun s’excluent mutuellement. C'est-à-dire que les ressources créées dans un mode ne peuvent être gérés à partir de l’autre mode.

## <a name="manage-your-account-information-and-publish-settings"></a>Gérer vos informations de compte et les paramètres de publication
Une façon de que l’infrastructure CLI peut se connecter à votre compte est en utilisant vos informations d’abonnement Azure. (Voir [se connecter à un abonnement Azure à partir de la CLI Azure](xplat-cli-connect.md) pour d’autres options). Ces informations peuvent être obtenues à partir du portail Azure classique dans un fichier de paramètres de publication comme décrit ici. Vous pouvez importer le fichier de paramètres de publication comme paramètre l’interface CLI de configuration locale permanente utilise pour les opérations ultérieures. Vous devez uniquement importer vos paramètres de publication une fois.

**téléchargement de compte [options]**

Cette commande lance un navigateur pour télécharger votre fichier .publishsettings à partir du portail classique Azure.

    ~$ azure account download
    info:   Executing command account download
    info:   Launching browser to https://windows.azure.com/download/publishprofile.aspx
    help:   Save the downloaded file, then execute the command
    help:   account import <file>
    info:   account download command OK

**compte d’importation [options] &lt;fichier >**


Cette commande importe un fichier de publishsettings ou d’un certificat afin qu’il peut être utilisé par l’outil lors de futures sessions.

    ~$ azure account import publishsettings.publishsettings
    info:   Importing publish settings file publishsettings.publishsettings
    info:   Found subscription: 3-Month Free Trial
    info:   Found subscription: Pay-As-You-Go
    info:   Setting default subscription to: 3-Month Free Trial
    warn:   The 'publishsettings.publishsettings' file contains sensitive information.
    warn:   Remember to delete it now that it has been imported.
    info:   Account publish settings imported successfully

> [AZURE.NOTE] Le fichier publishsettings peut contenir des détails (c'est-à-dire, les nom d’abonnement et les ID) sur plus d’un abonnement. Lorsque vous importez le fichier publishsettings, le premier abonnement est utilisé comme description par défaut. Pour utiliser un autre abonnement, exécutez la commande suivante :
<code>~$ azure config set subscription &lt;other-subscription-id&gt;</code>

**compte Effacer [options]**

Cette commande supprime la publishsettings stockée qui ont été importés. Utilisez cette commande si vous avez terminé à l’aide de l’outil sur cet ordinateur et voulez vous assurer que l’outil ne peut pas être utilisé avec votre compte lors de futures sessions.

    ~$ azure account clear
    Clearing account info.
    info:   OK

**liste des comptes [options]**

Répertorier les abonnements importés

    ~$ azure account list
    info:    Executing command account list
    data:    Name                                    Id
           Current
    data:    --------------------------------------  -------------------------------
    -----  -------
    data:    Forums Subscription                     8679c8be-3b05-49d9-b8fb  true
    data:    Evangelism Team Subscription            9e672699-1055-41ae-9c36  false
    data:    MSOpenTech-Prod                         c13e6a92-706e-4cf5-94b6  false

**compte défini [options] &lt;abonnement&gt;**

Définir l’abonnement en cours

###<a name="commands-to-manage-your-affinity-groups"></a>Commandes pour gérer vos groupes d’affinité

**liste de groupe de l’affinité du compte [options]**

Cette commande répertorie les groupes d’affinité Azure.

Groupes d’affinité peut être définies lorsqu’un groupe d’ordinateurs virtuels s’étend sur plusieurs machines physiques. L’affinité du groupe indique que les machines physiques doivent être plus proche que possible, afin de réduire la latence du réseau.

    ~$ azure account affinity-group list
    + Fetching affinity groups
    data:   Name                                  Label   Location
    data:   ------------------------------------  ------  --------
    data:   535EBAED-BF8B-4B18-A2E9-8755FB9D733F  opentec  West US
    info:   account affinity-group list command OK

**affinité du groupe compte créer [options] &lt;nom&gt;**

Cette commande crée un groupe d’affinité

    ~$ azure account affinity-group create opentec -l "West US"
    info:    Executing command account affinity-group create
    + Creating affinity group
    info:    account affinity-group create command OK

**afficher les affinités-groupe de comptes [options] &lt;nom&gt;**

Cette commande affiche les détails du groupe d’affinité

    ~$ azure account affinity-group show opentec
    info:    Executing command account affinity-group show
    + Getting affinity groups
    data:    $ xmlns "http://schemas.microsoft.com/windowsazure"
    data:    $ xmlns:i "http://www.w3.org/2001/XMLSchema-instance"
    data:    Name "opentec"
    data:    Label "b3BlbnRlYw=="
    data:    Description $ i:nil "true"
    data:    Location "West US"
    data:    HostedServices ""
    data:    StorageServices ""
    data:    Capabilities Capability 0 "PersistentVMRole"
    data:    Capabilities Capability 1 "HighMemory"
    info:    account affinity-group show command OK

**compte de groupe d’affinité supprimer [options] &lt;nom&gt;**

Cette commande supprime le groupe d’affinité spécifié

    ~$ azure account affinity-group delete opentec
    info:    Executing command account affinity-group delete
    Delete affinity group opentec? [y/n] y
    + Deleting affinity group
    info:    account affinity-group delete command OK

###<a name="commands-to-manage-your-account-environment"></a>Commandes permettant de gérer votre environnement de compte

**liste des comptes env [options]**

Liste des environnements de compte

    C:\windows\system32>azure account env list
    info:    Executing command account env list
    data:    Name
    data:    ---------------
    data:    AzureCloud
    data:    AzureChinaCloud
    info:    account env list command OK

**compte env afficher [options] [environment]**

Afficher les détails de l’environnement compte

    ~$ azure account env show
    info:    Executing command account env show
    Environment name: AzureCloud
    data:    Environment publishingProfile  http://go.microsoft.com/fwlink/?LinkId=2544
    data:    Environment portal  http://go.microsoft.com/fwlink/?LinkId=2544
    info:    account env show command OK

**compte env ajouter [options] [environment]**

Cette commande ajoute un environnement pour le compte

**compte env définie [options] [environment]**

Cette commande définit le compte de l’environnement

**compte env supprimer [options] [environment]**

Cette commande supprime l’environnement spécifié à partir du compte

## <a name="commands-to-manage-your-classic-virtual-machines"></a>Commandes pour gérer vos ordinateurs virtuels classiques
Le diagramme suivant illustre comment classique Azure ordinateurs virtuels sont hébergés dans l’environnement de déploiement de production d’un service cloud Azure.

![Azure diagramme technique](./media/virtual-machines-command-line-tools/architecturediagram.jpg)

**Créer nouveau** crée le lecteur dans le stockage blob (c'est-à-dire e:\ dans le diagramme) ; **joindre** d’attache un disque déjà créé mais non attaché à une machine virtuelle.

**machine virtuelle créer [options] &lt;nom dns > &lt;image > &lt;nom d’utilisateur > [mot_de_passe]**

Cette commande crée un ordinateur virtuel Azure. Par défaut, chaque machine virtuelle (vm) est créé dans son propre service de cloud. Vous pouvez spécifier qu’un ordinateur virtuel doit être ajouté à un service cloud existant à l’aide de l’option - c, comme indiqué ici.

La machine virtuelle, Créer commande, tels que le portail classique Azure, crée uniquement des ordinateurs virtuels dans l’environnement de déploiement de production. Il n’y a pas d’option pour créer un ordinateur virtuel dans l’environnement de déploiement intermédiaire d’un service cloud. Si votre abonnement n’a pas un compte de stockage Azure existante, la commande crée un.

Vous pouvez spécifier un emplacement via le paramètre de l’emplacement, ou vous pouvez spécifier un groupe d’affinité via le paramètre de l’affinité du groupe. Si aucun n’est fourni, vous êtes invité à fournir un dans la liste des emplacements valides.

Le mot de passe fourni doit être 8-123 caractères et répondent aux exigences de complexité de mot de passe du système d’exploitation que vous utilisez pour cet ordinateur virtuel.

Si vous envisagez d’utiliser le protocole SSH pour gérer un ordinateur virtuel de Linux déployé (comme c’est généralement le cas), vous devez activer le protocole SSH via l’option -e lorsque vous créez l’ordinateur virtuel. Il n’est pas possible d’activer SSH, une fois que l’ordinateur virtuel a été créé.

Machines virtuelles Windows peut activer RDP ultérieurement en ajoutant le port 3389 sous la forme d’un point de terminaison.

Les paramètres facultatifs suivants sont pris en charge pour cette commande :

**- c,-se connecter** créer l’ordinateur virtuel à l’intérieur d’un déploiement déjà créé dans un service d’hébergement. Si - vmname n’est pas utilisée avec cette option, le nom de la nouvelle machine virtuelle est généré automatiquement.<br />
**-n, le nom de la machine virtuelle :** Spécifiez le nom de l’ordinateur virtuel. Ce paramètre prend le nom de service d’hébergement par défaut. Si - vmname n’est pas spécifié, le nom de la nouvelle machine virtuelle est généré en tant que &lt;-nom du service >&lt;id >, où &lt;id > est le nombre de 1, plus le service des machines virtuelles existantes. Par exemple, si vous utilisez cette commande pour ajouter un ordinateur virtuel à un service d’hébergement MyService disposant d’un ordinateur virtuel existant, le nouvel ordinateur virtuel est nommé MyService2.<br />
**-u,--- url de blob** Spécifiez l’URL de stockage blob cible à partir duquel créer la disquette système de machine virtuelle. <br />
**-z, la machine virtuelle : taille** Spécifiez la taille de la machine virtuelle. Les valeurs valides sont : « ExtraSmall », « Small », « Medium », « Large », « ExtraLarge », « A5 », « A6 », « A7 », « A8 », « A9 », « A10 », « A11 », « Basic_A0 », « Basic_A1 », « Basic_A2 », « Basic_A3 », « Basic_A4 », « Standard_D1 », « Standard_D2 », « Standard_D3 », « Standard_D4 », « Standard_D11 », « Standard_D12 », « Standard_D13 », « Standard_D14 », « Standard_DS1 », « Standard_DS2 », « Standard_DS3 », « Standard_DS4 », « Standard_DS11 », « Standard_DS12 », « Standard_DS13 », « Standard_DS14 », « Standard_G1 », « Standard_G2 » , « Standard_G3 », « Standard_G4 », « Standard_G55 ». La valeur par défaut est « Small ». <br />
**-r** Ajoute la connectivité RDP à une machine virtuelle Windows. <br />
**-e,--ssh** Ajoute la connectivité SSH à une machine virtuelle Windows. <br />
**-t,--ssh-cert** Spécifie le certificat SSH. <br />
**-s** L’abonnement <br />
**-o,--Communauté** L’image spécifiée est une image de la Communauté <br />
**-w** Le nom du réseau virtuel <br/>
**-l,--emplacement** indique l’emplacement (par exemple, « Nord centrale nous »). <br />
**-un,--affinité-groupe** Spécifie le groupe d’affinité.<br />
**-w,--nom de réseau virtuel** Spécifiez le réseau virtuel sur laquelle ajouter la nouvelle machine virtuelle. Les réseaux virtuels peuvent être paramétrées et gérées à partir du portail classique Azure.<br />
**-b,--les noms de sous-réseaux** Spécifie le nom du sous-réseau à affecter à l’ordinateur virtuel.

Dans cet exemple, MSFT__Win2K8R2SP1-120514-1520-141205-01-en-us-30GB est une image fournie par la plate-forme. Pour plus d’informations sur les images de système d’exploitation, consultez la liste d’images de machine virtuelle.

    ~$ azure vm create my-vm-name MSFT__Windows-Server-2008-R2-SP1.11-29-2011 username --location "West US" -r
    info:   Executing command vm create
    Enter VM 'my-vm-name' password: ************
    info:   vm create command OK

**Création de machine virtuelle &lt;nom dns > &lt;rôle-fichier >**

Cette commande crée un ordinateur virtuel Azure à partir d’un fichier de rôle JSON.

    ~$ azure vm create-from my-vm example.json
    info:   OK

**liste de machine virtuelle [options]**

Cette commande répertorie les ordinateurs virtuels Azure. L’option--json Spécifie que les résultats sont renvoyés au format raw de JSON.

    ~$ azure vm list
    info:   Executing command vm list
    data:   DNS Name                          VM Name      Status
    data:   --------------------------------  -----------  ---------
    data:   my-vm-name.cloudapp-preview.net        my-vm        ReadyRole
    info:   vm list command OK

**liste des emplacements de mémoire virtuelle [options]**

Cette commande répertorie tous les emplacements disponibles compte Azure.

    ~$ azure vm location list
    info:   Executing command vm location list
    data:   Name                   Display Name
    data:   ---------------------  ------------
    data:   Azure Preview  West US
    info:   account location list command OK

**machine virtuelle afficher [options] &lt;nom >**

Cette commande affiche les détails sur une machine virtuelle Azure. L’option--json Spécifie que les résultats sont renvoyés au format raw de JSON.

    ~$ azure vm show my-vm
    info:   Executing command vm show
    data:   {
    data:       InstanceSize: 'Small',
    data:       InstanceStatus: 'ReadyRole',
    data:       DataDisks: [],
    data:       IPAddress: '10.26.192.206',
    data:       DNSName: 'my-vm.cloudapp.net',
    data:       InstanceStateDetails: {},
    data:       VMName: 'my-vm',
    data:       Network: {
    data:           Endpoints: [
    data:               {
    data:                   Protocol: 'tcp',
    data:                   Vip: '65.52.250.250',
    data:                   Port: '63238' ,
    data:                   LocalPort: '3389',
    data:                   Name: 'RemoteDesktop'
    data:               }
    data:           ]
    data:       },
    data:       Image: 'MSFT__Windows-Server-2008-R2-SP1.11-29-2011',
    data:       OSVersion: 'WA-GUEST-OS-1.18_201203-01'
    data:   }
    info:   vm show command OK

**machine virtuelle supprimer [options] &lt;nom >**

Cette commande supprime une machine virtuelle Azure. Par défaut, cette commande ne supprime pas le blob Azure à partir de laquelle le disque du système d’exploitation et le disque de données sont créés. Pour supprimer l’objet blob et l’ordinateur virtuel sur lequel il est basé, spécifiez l’option -b.

    ~$ azure vm delete my-vm
    info:   Executing command vm delete
    info:   vm delete command OK

**ordinateur virtuel démarre [options] &lt;nom >**

Cette commande démarre une machine virtuelle Azure.

    ~$ azure vm start my-vm
    info:   Executing command vm start
    info:   vm start command OK

**redémarrage de la machine virtuelle [options] &lt;nom >**

Cette commande redémarre une machine virtuelle Azure.

    ~$ azure vm restart my-vm
    info:   Executing command vm restart
    info:   vm restart command OK

**arrêt de l’ordinateur virtuel [options] &lt;nom >**

Cette commande arrête une machine virtuelle Azure. Vous pouvez utiliser l’option-p pour spécifier que la ressource de calcul ne sont pas libérées lors de l’arrêt.

```
~$ azure vm shutdown my-vm
info:   Executing command vm shutdown
info:   vm shutdown command OK  
```

**capture de la machine virtuelle &lt;nom de la machine virtuelle > &lt;nom de l’image cible >**

Cette commande capture une image Azure VM.

Une image de machine virtuelle peut être capturée uniquement si l’état de l’ordinateur virtuel est **arrêté**. Arrêtez l’ordinateur virtuel avant de continuer.

    ~$ azure.cmd vm capture my-vm mycaptureimagename --delete
    info:   Executing command vm capture
    + Fetching VMs
    + Capturing VM
    info:   vm capture command OK

**exportation de machine virtuelle [options] &lt;nom de machine virtuelle-> &lt;-chemin d’accès >**

Cette commande exporte une image Azure VM vers un fichier

    ~$ azure vm export "myvm" "C:\"
    info:    Executing command vm export
    + Getting virtual machines
    + Exporting the VM
    info:   vm export command OK

##  <a name="commands-to-manage-your-azure-virtual-machine-endpoints"></a>Commandes pour gérer vos points de terminaison Azure VM
Le diagramme suivant illustre l’architecture d’un déploiement typique de plusieurs instances d’un ordinateur virtuel classique. Dans cet exemple, le port 3389 est ouvert sur chaque machine virtuelle (pour un accès RDP). Il existe également une adresse IP interne (par exemple, 168.55.11.1) sur chaque ordinateur virtuel qui est utilisé par l’équilibrage de charge pour acheminer le trafic vers l’ordinateur virtuel. Cette adresse IP interne peut également être utilisée pour la communication entre des machines virtuelles.

![azurenetworkdiagram](./media/virtual-machines-command-line-tools/networkdiagram.jpg)

Les demandes externes vers des machines virtuelles passent par un équilibreur de charge. De ce fait, demandes ne peut pas être spécifiés par rapport à un ordinateur virtuel spécifique sur les déploiements avec plusieurs machines virtuelles. Pour les déploiements avec plusieurs machines virtuelles, le mappage de port doit être configuré entre les machines virtuelles (vm-port) et l’équilibrage de la charge (lb-port).

**créer de la machine virtuelle de point de terminaison &lt;-nom de la machine virtuelle > &lt;lb-port > [vm-port]**

Cette commande crée un point de terminaison de machine virtuelle. Vous pouvez également utiliser -u ou--activer-direct-server-return pour spécifier si serveur direct revenir sur ce point de terminaison, désactivée par défaut pour activer.

    ~$ azure vm endpoint create my-vm 8888 8888
    azure vm endpoint create my-vm 8888 8888
    info:   Executing command vm endpoint create
    + Fetching VM
    + Reading network configuration
    + Updating network configuration
    info:   vm endpoint create command OK

**vm point de terminaison créer-multiples [options] &lt;-nom de la machine virtuelle > &lt;lb-port > [ :&lt;vm-port > [ :&lt;protocole > [ :&lt;activer-direct-server-return > [ :&lt;nom-entité-lb > [ :&lt;sonde-protocole > [ :&lt;sonde-port > [ :&lt;sonde-chemin d’accès > [ :&lt;nom interne lb >]]] {1 -*}**

Créer plusieurs points de terminaison de machine virtuelle.

**point de terminaison VM supprimer [options] &lt;nom de la machine virtuelle > &lt;nom de point de terminaison >**

Cette commande supprime un point de terminaison de machine virtuelle.

    ~$ azure vm endpoint delete my-vm http
    azure vm endpoint delete my-vm http
    info:   Executing command vm endpoint delete
    + Fetching VM
    + Reading network configuration
    + Updating network configuration
    info:   vm endpoint delete command OK

**liste de point de terminaison de machine virtuelle &lt;nom de la machine virtuelle >**

Cette commande répertorie tous les points de terminaison de machine virtuelle. L’option--json Spécifie que les résultats sont renvoyés au format raw de JSON.

    ~$ azure vm endpoint list my-linux-vm
    data:   Name  External Port  Local Port
    data:   ----  -------------  ----------
    data:   ssh   22             22

**mise à jour du point de terminaison de machine virtuelle [options] &lt;nom de machine virtuelle-> &lt;nom de point de terminaison >**

Cette commande met à jour la machine virtuelle d’un point de terminaison avec de nouvelles valeurs à l’aide de ces options.

    -n, --endpoint-name <name>          the new endpoint name
    -lo, --lb-port <port>                the new load balancer port
    -t, --vm-port <port>                the new local port
    -o, --endpoint-protocol <protocol>  the new transport layer protocol for port (tcp or udp)

**point de terminaison VM afficher [options] &lt;nom de la machine virtuelle >**

Cette commande affiche les détails des points de terminaison sur une machine virtuelle

    ~$ azure vm endpoint show "mycouchvm"
    info:    Executing command vm endpoint show
    + Getting virtual machines
    data:    Network Endpoints 0 LoadBalancedEndpointSetName "CouchDB_EP-5984"
    data:    Network Endpoints 0 LocalPort "5984"
    data:    Network Endpoints 0 Name "CouchDB_EP"
    data:    Network Endpoints 0 Port "5984"
    data:    Network Endpoints 0 Protocol "tcp"
    data:    Network Endpoints 0 Vip "168.61.9.97"
    data:    Network Endpoints 1 LoadBalancedEndpointSetName "CouchEP_2-2020"
    data:    Network Endpoints 1 LocalPort "2020"
    data:    Network Endpoints 1 Name "CouchEP_2"
    data:    Network Endpoints 1 Port "2020"
    data:    Network Endpoints 1 Protocol "tcp"
    data:    Network Endpoints 1 Vip "168.61.9.97"
    data:    Network Endpoints 2 LocalPort "3389"
    data:    Network Endpoints 2 Name "RemoteDesktop"
    data:    Network Endpoints 2 Port "3389"
    data:    Network Endpoints 2 Protocol "tcp"
    data:    Network Endpoints 2 Vip "168.61.9.97"
    info:    vm endpoint show command OK

## <a name="commands-to-manage-your-azure-virtual-machine-images"></a>Commandes pour gérer vos images Azure VM

Images d’ordinateur virtuel sont des captures de déjà configuré les ordinateurs virtuels peuvent être répliqués comme requis.

**liste d’images de machine virtuelle [options]**

Cette commande Obtient une liste d’images d’ordinateur virtuel. Il existe trois types d’images : les images créées par Microsoft, qui sont précédés de « MSFT », créer des images créées par des tiers, qui sont préfixés avec le nom du fournisseur et vous les images. Pour créer des images, vous pouvez capturer un ordinateur virtuel existant ou créer une image d’un .vhd personnalisé chargé pour le stockage des objets blob. Pour plus d’informations sur l’utilisation d’un .vhd personnalisé, reportez-vous à la section Créer image vm.
L’option--json Spécifie que les résultats sont renvoyés au format raw de JSON.

    ~$ azure vm image list
    data:   Name                                                                   Category   OS
    data:   ---------------------------------------------------------------------  ---------  -------
    data:   CANONICAL__Canonical-Ubuntu-12-04-20120519-2012-05-19-en-us-30GB.vhd   Canonical  Linux
    data:   MSFT__Windows-Server-2008-R2-SP1.11-29-2011                            Microsoft  Windows
    data:   MSFT__Windows-Server-2008-R2-SP1-with-SQL-Server-2012-Eval.11-29-2011  Microsoft  Windows
    data:   MSFT__Windows-Server-8-Beta.en-us.30GB.2012-03-22                      Microsoft  Windows
    data:   MSFT__Windows-Server-8-Beta.2-17-2012                                  Microsoft  Windows
    data:   MSFT__Windows-Server-2008-R2-SP1.en-us.30GB.2012-3-22                  Microsoft  Windows
    data:   OpenLogic__OpenLogic-CentOS-62-20120509-en-us-30GB.vhd                 OpenLogic  Linux
    data:   SUSE__SUSE-Linux-Enterprise-Server-11SP2-20120521-en-us-30GB.vhd       SUSE       Linux
    data:   SUSE__OpenSUSE64121-03192012-en-us-15GB.vhd                            SUSE       Linux
    data:   WIN2K8-R2-WINRM                                                        User       Windows
    info:   vm image list command OK

**Afficher image de machine virtuelle [options] &lt;nom >**

Cette commande affiche les détails d’une image de machine virtuelle.

    ~$ azure vm image show MSFT__Windows-Server-2008-R2-SP1.11-29-2011
    + Fetching VM image
    info:   Executing command vm image show
    data:   {
    data:       Label: 'Windows Server 2008 R2 SP1, Nov 2011',
    data:       Name: 'MSFT__Windows-Server-2008-R2-SP1.11-29-2011',
    data:       Description: 'Microsoft Windows Server 2008 R2 SP1',
    data:       @: { xmlns: 'http://schemas.microsoft.com/windowsazure', xmlns:i: 'http://www.w3.org/2001/XMLSchema-instance' },
    data:       Category: 'Microsoft',
    data:       OS: 'Windows',
    data:       Eula: 'http://www.microsoft.com',
    data:       LogicalSizeInGB: '30'
    data:   }
    info:   vm image show command OK

**suppression de l’image virtuelle [options] &lt;nom >**

Cette commande supprime une image d’ordinateur virtuel.

    ~$ azure vm image delete my-vm-image
    info:   Executing command vm image delete
    info:   VM image deleted: my-vm-image
    info:   vm image delete command OK

**créer des images de machine virtuelle &lt;nom > [-chemin d’accès source]**

Cette commande crée une image de machine virtuelle. Téléchargement de vos fichiers .vhd personnalisés pour le stockage blob, et ensuite l’image de l’ordinateur virtuel est créé à partir de là. Vous utilisez ensuite cette image de machine virtuelle pour créer un ordinateur virtuel. Les paramètres d’emplacement et de système d’exploitation sont nécessaires.

>[AZURE.NOTE]Actuellement cette commande prend uniquement en charge le téléchargement de fichiers .vhd fixes. Pour télécharger un fichier .vhd dynamique, utilisez les [utilitaires disque dur virtuel d’Azure pour aller](https://github.com/Microsoft/azure-vhd-utils-for-go).

Certains systèmes imposent des limites de descripteur de fichier de chaque processus. Si cette limite est dépassée, l’outil affiche une erreur de limite de descripteur de fichier. Vous pouvez exécuter la commande à l’aide de la p - &lt;nombre > paramètre afin de réduire le nombre maximal de téléchargements parallèles. Le nombre de téléchargements parallèles par défaut est 96.

    ~$ azure vm image create mytestimage ./Sample.vhd -o windows -l "West US"
    info:   Executing command vm image create
    + Retrieving storage accounts
    info:   VHD size : 13 MB
    info:   Uploading 13312.5 KB
    Requested:100.0% Completed:100.0% Running: 105 Time:    8s Speed:  1721 KB/s
    info:   http://myaccount.blob.core.azure.com/vm-images/Sample.vhd is uploaded successfully
    info:   vm image create command OK

## <a name="commands-to-manage-your-azure-virtual-machine-data-disks"></a>Commandes pour gérer vos disques de données Azure VM

Disques de données sont des fichiers .vhd dans le stockage blob qui peut être utilisé par un ordinateur virtuel. Pour plus d’informations sur le mode de déploiement des disques de données pour le stockage blob, consultez le diagramme technique Azure illustré précédemment.

Les commandes permettant de joindre des disques de données (disque vm azure joindre et azure vm disque joindre à nouveau) attribuer un numéro d’unité logique (LUN) sur le disque de données, comme requis par le protocole SCSI. Le premier disque de données attaché à une machine virtuelle est affecté la LUN 0, la suivante est affectée LUN 1 et ainsi de suite.

Lorsque vous détachez un disque de données avec le disque vm azure détacher de commande, utilisez la &lt;lun&gt; paramètre pour indiquer le disque pour la détacher.

>[AZURE.NOTE] Vous devez toujours détacher les disques de données dans l’ordre inverse, commençant par la LUN au numéro le plus élevé qui a été affecté. La couche SCSI de Linux ne gère pas le détachement d’un LUN inférieur pendant une LUN élevé est encore attachée. Par exemple, vous ne devez pas détacher LUN 0 si LUN 1 est toujours attaché.

**afficher de disque VM [options] &lt;nom >**

Cette commande affiche les détails sur un disque Azure.

    ~$ azure vm disk show anucentos-anucentos-0-20120524070008
    info:   Executing command vm disk show
    data:   AttachedTo DeploymentName "mycentos"
    data:   AttachedTo HostedServiceName "myanucentos"
    data:   AttachedTo RoleName "myanucentos"
    data:   OS "Linux"
    data:   Location "Azure Preview"
    data:   LogicalDiskSizeInGB "30"
    data:   MediaLink "http://mystorageaccount.blob.core.azure-preview.com/vhd-store/mycentos-cb39b8223b01f95c.vhd"
    data:   Name "mycentos-mycentos-0-20120524070008"
    data:   SourceImageName "OpenLogic__OpenLogic-CentOS-62-20120509-en-us-30GB.vhd"
    info:   vm disk show command OK

**liste des disques VM [options] [nom de la machine virtuelle]**

Cette commande répertorie les disques Azure, ou des disques connectés à un ordinateur virtuel spécifié. Si elle est exécutée avec un paramètre de nom d’ordinateur virtuel, il retourne tous les disques attachés à l’ordinateur virtuel. LUN 1 est créé avec la machine virtuelle, et tous les autres disques répertoriés sont joints séparément.

    ~$ azure vm disk list mycentos
    info:   Executing command vm disk list
    data:   Lun  Size(GB)  Blob-Name
    data:   ---  --------  --------------------------------
    data:   1    30        mycentos-cb39b8223b01f95c.vhd
    data:   2    10        mycentos-e3f0d717950bb78d.vhd
    info:   vm disk list command OK

L’exécution de cette commande sans un paramètre de nom d’ordinateur virtuel renvoie tous les disques.

    ~$ azure vm disk list
    data:   Name                                        OS
    data:   ------------------------------------------  -------
    data:   mycentos-mycentos-0-20120524070008          Linux
    data:   mycentos-mycentos-2-20120525055052
    data:   mywindows-winvm-20120522223119              Windows
    info:   vm disk list command OK

**suppression de disque VM [options] &lt;nom >**

Cette commande supprime un disque Azure à partir d’un référentiel personnel. Le disque doit être détaché de l’ordinateur virtuel avant d’être supprimé.

    ~$ azure vm disk delete mycentos-mycentos-2-20120525055052
    info:   Executing command vm disk delete
    info:   Disk deleted: mycentos-mycentos-2-20120525055052
    info:   vm disk delete command OK

**création du disque de l’ordinateur virtuel &lt;nom > [-chemin d’accès source]**

Cette commande télécharge et enregistre un disque Azure. ---url de blob,--emplacement ou--affinité du groupe doit être spécifié. Si vous utilisez cette commande avec [-chemin d’accès source], le fichier .vhd spécifié est chargé et une image est créée. Vous pouvez ensuite joindre cette image à une machine virtuelle à l’aide de disque vm joindre.

Certains systèmes imposent des limites de descripteur de fichier de chaque processus. Si cette limite est dépassée, l’outil affiche une erreur de limite de descripteur de fichier. Vous pouvez exécuter la commande à l’aide de la p - &lt;nombre > paramètre afin de réduire le nombre maximal de téléchargements parallèles. Le nombre de téléchargements parallèles par défaut est 96.

    ~$ azure vm disk create my-data-disk ~/test.vhd --location "West US"
    info:   Executing command vm disk create
    info:   VHD size : 10 MB
    info:   Uploading 10240.5 KB
    Requested:100.0% Completed:100.0% Running:  81 Time:   11s Speed:   952 KB/s
    info:   http://account.blob.core.azure.com/disks/test.vhd is uploaded successfully
    info:   vm disk create command OK

**téléchargement de disque VM [options] &lt;source-path > &lt;-url de blob > &lt;clé de compte de stockage >**

Cette commande vous permet de télécharger un disque vm

    ~$ azure vm disk upload "http://sourcestorage.blob.core.windows.net/vhds/sample.vhd" "http://destinationstorage.blob.core.windows.net/vhds/sample.vhd" "DESTINATIONSTORAGEACCOUNTKEY"
    info:   Executing command vm disk upload
    info:   Uploading 12351.5 KB
    info:   vm disk upload command OK

**disque VM joindre &lt;nom de la machine virtuelle > &lt;nom de l’image disque >**

Cette commande attache un disque existant dans le stockage blob à un ordinateur virtuel existant déployé dans un service cloud.

    ~$ azure vm disk attach my-vm my-vm-my-vm-2-201242418259
    info:   Executing command vm disk attach
    info:   vm disk attach command OK

**machine virtuelle disque joindre-nouvelle &lt;-nom de la machine virtuelle > &lt;taille dans Go > [url de blob]**

Cette commande attache un disque de données à une machine virtuelle Azure. Dans cet exemple, la taille du nouveau disque, en gigaoctets, doit être jointe est 20. Vous pouvez éventuellement utiliser une URL de blob comme dernier argument pour spécifier explicitement le blob de cible à créer. Si vous ne spécifiez pas une URL de blob, un objet blob est généré automatiquement.

    ~$ azure vm disk attach-new nick-test36 20 http://nghinazz.blob.core.azure-preview.com/vhds/vmdisk1.vhd
    info:   Executing command vm disk attach-new
    info:   vm disk attach-new command OK  

**détacher du disque VM &lt;nom de machine virtuelle-> &lt;lun >**

Cette commande détache d’un disque de données connecté à un ordinateur virtuel Azure. &lt;LUN > identifie le disque pour être détaché. Pour obtenir une liste des disques associés à un disque avant de vous détacher, utilisez liste des disques vm &lt;nom de la machine virtuelle >.

    ~$ azure vm disk detach my-vm 2
    info:   Executing command vm disk detach
    info:   vm disk detach command OK

## <a name="commands-to-manage-your-azure-cloud-services"></a>Commandes pour gérer vos services Azure cloud

Les services en nuage Azure sont des applications et des services hébergés sur les rôles web et worker. Les commandes suivantes peuvent servir à gérer des services cloud Azure.

**Création de service [options] &lt;ServiceName% >**

Cette commande crée un service cloud

    ~$ azure service create newservicemsopentech
    info:    Executing command service create
    + Getting locations
    help:    Location:
      1) East Asia
      2) Southeast Asia
      3) North Europe
      4) West Europe
      5) East US
      6) West US
      : 6
    + Creating cloud service
    data:    Cloud service name newservicemsopentech
    info:    service create command OK

**service Afficher [options] &lt;ServiceName% >**

Cette commande affiche les détails d’un service cloud Azure

    ~$ azure service show newservicemsopentech
    info:    Executing command service show
    + Getting cloud service
    data:    Name newservicemsopentech
    data:    Url https://management.core.windows.net/9e672699-1055-41ae-9c36-e85152f2e352/services/hostedservices/newservicemsopentech
    data:    Properties location West US
    data:    Properties label newservicemsopentech
    data:    Properties status Created
    data:    Properties dateCreated
    data:    Properties dateLastModified
    info:    service show command OK

**liste de service [options]**

Cette commande répertorie les services en nuage Azure.

    ~$ azure service list
    info:   Executing command service list
    data:   Name         Status
    data:   -----------  -------
    data:   service1     Created
    data:   service2     Created
    info:   service list command OK

**suppression du service [options] &lt;nom >**

Cette commande supprime un service cloud Azure.

    ~$ azure service delete myservice
    info:   Executing command service delete myservice
    info:   cloud-service delete command OK

Pour forcer la suppression, utilisez la `-q` paramètre.


## <a name="commands-to-manage-your-azure-certificates"></a>Commandes pour gérer vos certificats Azure

Les certificats de service Azure sont les certificats SSL connectés à votre compte Azure. Pour plus d’informations sur les certificats d’Azure, voir [Gérer les certificats](http://msdn.microsoft.com/library/azure/gg981929.aspx).

**liste de certificats de service [options]**

Cette commande répertorie les certificats Azure.

    ~$ azure service cert list
    info:   Executing command service cert list
    + Fetching cloud services
    + Fetching certificates
    data:   Service   Thumbprint                                Algorithm
    data:   --------  ----------------------------------------  ---------
    data:   myservice  262DBF95B5E61375FA27F1E74AC7D9EAE842916C  sha1
    info:   service cert list command OK

**créer des certificats de service &lt;dns-préfixe > &lt;fichier > [mot_de_passe]**

Cette commande envoie un certificat. Rien dans l’invite de mot de passe pour les certificats qui ne sont pas protégés par un mot de passe.

    ~$ azure service cert create nghinazz ~/publishSet.pfx
    info:   Executing command service cert create
    Cert password:
    + Creating certificate
    info:   service cert create command OK

**suppression du certificat de service [options] &lt;empreinte numérique >**

Cette commande supprime un certificat.

    ~$ azure service cert delete 262DBF95B5E61375FA27F1E74AC7D9EAE842916C
    info:   Executing command service cert delete
    + Deleting certificate
    info:   nghinazz : cert deleted
    info:   service cert delete command OK

## <a name="commands-to-manage-your-web-apps"></a>Commandes pour gérer vos applications web

Une application web Azure est une configuration web accessible par un URI. Les applications Web sont hébergées dans des machines virtuelles, mais vous n’avez pas besoin de réfléchir sur les détails de la création et le déploiement de la machine virtuelle vous-même. Ces détails sont gérés par Azure.

**liste des sites [options]**

Cette commande répertorie vos applications web.

    ~$ azure site list
    info:   Executing command site list
    data:   Name            State    Host names
    data:   --------------  -------  --------------------------------------------------
    data:   mongosite       Running  mongosite.antdf0.antares.windows.net
    data:   myphpsite       Running  myphpsite.antdf0.antares.windows.net
    data:   mydrupalsite36  Running  mydrupalsite36.antdf0.antares.windows.net
    info:   site list command OK

**ensemble de site [options] [nom]**

Cette commande définit les options de configuration pour votre application web [nom]

    ~$ azure site set
    info:    Executing command site set
    Web site name: mydemosite
    + Getting sites
    + Updating site config information
    info:    site set command OK

**site deploymentscript [options]**

Cette commande génère un script de déploiement personnalisé

    ~$ azure site deploymentscript --node
    info:    Executing command site deploymentscript
    info:    Generating deployment script for node.js Web Site
    info:    Generated deployment script files
    info:    site deploymentscript command OK

**site création [options] [nom]**

Cette commande crée une application web et le répertoire local.

    ~$ azure site create mysite
    info:   Executing command site create
    info:   Using location northeuropewebspace
    info:   Creating a new web site
    info:   Created web site at  mysite.antdf0.antares.windows.net
    info:   Initializing repository
    info:   Repository initialized
    info:   site create command OK

> [AZURE.NOTE] Le nom du site doit être unique. Vous ne pouvez pas créer un site avec le même nom DNS sous la forme d’un site existant.

**navigation de site [options] [nom]**

Cette commande ouvre votre application web dans un navigateur.

    ~$ azure site browse mysite
    info:   Executing command site browse
    info:   Launching browser to http://mysite.antdf0.antares-test.windows-int.net
    info:   site browse command OK

**afficher de site [options] [nom]**

Cette commande affiche les détails pour une application web.

    ~$ azure site show mysite
    info:   Executing command site show
    info:   Showing details for site
    data:   Site AdminEnabled true
    data:   Site HostNames mysite.antdf0.antares-test.windows-int.net
    data:   Site Name mysite
    data:   Site Owner 00060000814EDDEE
    data:   Site RepositorySiteName mysite
    data:   Site SelfLink https://s1.api.antdf0.antares.windows.net:454/subscriptions/444e62ff-4c5f-4116-a695-5c803ed584a5/webspaces/northeuropewebspace/sites/mysite
    data:   Site State Running
    data:   Site UsageState Normal
    data:   Site WebSpace northeuropewebspace
    data:   Config AppSettings
    data:   Config ConnectionStrings
    data:   Config DefaultDocuments 0=Default.htm, 1=Default.asp, 2=index.htm, 3=index.html, 4=iisstart.htm, 5=default.aspx, 6=index.php, 7=hostingstart.aspx
    data:   Config DetailedErrorLoggingEnabled false
    data:   Config HttpLoggingEnabled false
    data:   Config Metadata
    data:   Config NetFrameworkVersion v4.0
    data:   Config NumberOfWorkers 1
    data:   Config PhpVersion 5.3
    data:   Config PublishingPassword rJ}[Er2v[Y]q16B6vTD]n$[C2z}Z.pvgLfRcLnAp%ax]xstiLny};o@vmMAote@d
    data:   Config RequestTracingEnabled false
    data:   Repository https://mysite.scm.antdf0.antares-test.windows-int.net/
    info:   site show command OK

**supprimer le site [options] [nom]**

Cette commande supprime une application web.

    ~$ azure site delete mysite
    info:   Executing command site delete
    info:   Deleting site mysite
    info:   Site mysite has been deleted
    info:   site delete command OK

 **échange de site [options] [nom]**

Cette commande remplace les deux connecteurs d’application web.

Cette commande prend en charge l’option supplémentaire suivante :

**- q ou **--mode silencieux ** : ne pas demander de confirmation. Utilisez cette option dans les scripts automatisés.


**début du site [options] [nom]**

Cette commande démarre une application web.

    ~$ azure site start mysite
    info:   Executing command site start
    info:   Starting site mysite
    info:   Site mysite has been started
    info:   site start command OK

**arrêt du site [options] [nom]**

Cette commande arrête une application web.

    ~$ azure site stop mysite
    info:   Executing command site stop
    info:   Stopping site mysite
    info:   Site mysite has been stopped
    info:   site stop command OK

**redémarrage du site [options] [nom]**

Cette commande arrête et démarre une application web spécifiée.

Cette commande prend en charge l’option supplémentaire suivante :

**--emplacement** &lt;emplacement > : le nom de l’emplacement à redémarrer.


**liste d’emplacement de site [options]**

Cette commande répertorie des emplacements de votre application web.

    ~$ azure site location list
    info:    Executing command site location list
    + Getting locations
    data:    Name
    data:    ----------------
    data:    West Europe
    data:    West US
    data:    North Central US
    data:    North Europe
    data:    East Asia
    data:    East US
    info:    site location list command OK

###<a name="commands-to-manage-your-web-app-application-settings"></a>Commandes pour gérer les paramètres de votre application web app

**liste des sites appsetting [options] [nom]**

Cette commande répertorie le paramètre application ajouté à l’application web.

    ~$ azure site appsetting list
    info:    Executing command site appsetting list
    Web site name: mydemosite
    + Getting sites
    + Getting site config information
    data:    Name  Value
    data:    ----  -----
    data:    test  value
    info:    site appsetting list command OK

**site appsetting ajouter [options] &lt;keyvaluepair > [nom]**

Cette commande ajoute un paramètre d’application à votre application web sous la forme d’une paire clé / valeur.

    ~$ azure site appsetting add test=value
    info:    Executing command site appsetting add
    Web site name: mydemosite
    + Getting sites
    + Getting site config information
    + Updating site config information
    info:    site appsetting add command OK

**site appsetting supprimer [options] &lt;clé > [nom]**

Cette commande supprime le paramètre de l’application spécifiée à partir de l’application web.

    ~$ azure site appsetting delete test
    info:    Executing command site appsetting delete
    Web site name: mydemosite
    + Getting sites
    + Getting site config information
    Delete application setting test? [y/n] y
    + Updating site config information
    info:    site appsetting delete command OK

**site appsetting afficher [options] &lt;clé > [nom]**

Cette commande affiche des informations détaillées sur le paramétrage de l’application spécifiée

    ~$ azure site appsetting show test
    info:    Executing command site appsetting show
    Web site name: mydemosite
    + Getting sites
    + Getting site config information
    data:    Value:  value
    info:    site appsetting show command OK

###<a name="commands-to-manage-your-web-app-certificates"></a>Commandes permettant de gérer des certificats de votre application web

**liste de certificats de site [options] [nom]**

Cette commande affiche une liste des certificats d’application web.

    ~$ azure site cert list
    info:    Executing command site cert list
    Web site name: mydemosite
    + Getting sites
    + Getting site information
    data:    Subject                       Expiration Date                    Thumbprint
    data:    ----------------------------  -----------------------------------------
    ----------------  ----------------------------------------
    data:    *.msopentech.com              Fri Nov 28 2014 09:49:57 GMT-0800 (Pacific Standard Time)  A40E82D3DC0286D1F58650E570ECF8224F69A148
    data:    msopentech.azurewebsites.net  Fri Jun 19 2015 11:57:32 GMT-0700 (Pacific Daylight Time)  CE1CD6538852BF7A5DC32001C2E26A29B541F0E8
    info:    site cert list command OK

**certificat de site ajouter [options] &lt;chemin d’accès de certificat > [nom]**

**supprimer le cert site [options] &lt;empreinte > [nom]**

**site d’afficher de cert [options] &lt;empreinte > [nom]**

Cette commande affiche les détails du certificat

    ~$ azure site cert show CE1CD65852B38DC32001C2E0E8F7A526A29B541F
    info:    Executing command site cert show
    Web site name: mydemosite
    + Getting sites
    + Getting site information
    data:    Certificate hostNames 0=msopentech.azurewebsites.net
    data:    Certificate expirationDate
    data:    Certificate friendlyName msopentech.azurewebsites.net
    data:    Certificate issueDate
    data:    Certificate issuer CN=MSIT Machine Auth CA 2, DC=redmond, DC=corp, DC=microsoft, DC=com
    data:    Certificate subjectName msopentech.azurewebsites.net
    data:    Certificate thumbprint CE1CD65852B38DC32001C2E0E8F7A526A29B541F
    info:    site cert show command OK

###<a name="commands-to-manage-your-web-app-connection-strings"></a>Commandes pour gérer les chaînes de connexion de votre application web

**liste des sites de connectionstring [options] [nom]**

**site connectionstring ajouter [options] &lt;connectionname > &lt;valeur > &lt;type > [nom]**

**site connectionstring supprimer [options] &lt;connectionname > [nom]**

**site connectionstring afficher [options] &lt;connectionname > [nom]**

###<a name="commands-to-manage-your-web-app-default-documents"></a>Commandes pour gérer les documents par défaut que votre application web

**liste des sites defaultdocument [options] [nom]**

**site defaultdocument ajouter [options] &lt;document > [nom]**

**site defaultdocument supprimer [options] &lt;document > [nom]**

###<a name="commands-to-manage-your-web-app-deployments"></a>Commandes pour gérer le déploiement de vos applications web

**liste de déploiement de site [options] [nom]**

**déploiement de site afficher [options] &lt;commitId > [nom]**

**Redéploiement de déploiement de site [options] &lt;commitId > [nom]**

**déploiement de site github [options] [nom]**

**ensemble d’utilisateurs site déploiement [options] [nom_utilisateur] [test]**

###<a name="commands-to-manage-your-web-app-domains"></a>Commandes pour gérer vos domaines d’application web

**liste de domaine de site [options] [nom]**

**Ajouter un domaine de site [options] &lt;dn > [nom]**

**la suppression du domaine site [options] &lt;dn > [nom]**

###<a name="commands-to-manage-your-web-app-handler-mappings"></a>Commandes pour gérer vos mappages de gestionnaire d’application web

**liste de gestionnaires de site [options] [nom]**

**Gestionnaire de site ajouter [options] &lt;extension > &lt;processeur > [nom]**

**suppression de gestionnaire de site [options] &lt;extension > [nom]**

###<a name="commands-to-manage-your-web-jobs"></a>Commandes pour gérer vos projets Web

**liste de tâches de site [options] [nom]**

Cette commande répertorie toutes les tâches web dans une application web.

Cette commande prend en charge les options supplémentaires suivantes :

+ **: type de la tâche** &lt;de type travail > : facultatif. Le type de la webjob. Valeur valide est « déclenchement » ou « continue ». Webjobs de tous les types de retour par défaut.
+ **--emplacement** &lt;emplacement > : le nom de l’emplacement à redémarrer.

**site d’afficher de tâche [options] &lt;jobName > &lt;fonction > [nom]**

Cette commande affiche les détails d’un projet web spécifique.

Cette commande prend en charge les options supplémentaires suivantes :

+ **: nom de la tâche** &lt;nom de la tâche > : obligatoire. Le nom de la webjob.
+ **: type de la tâche** &lt;de type travail > : obligatoire. Le type de la webjob. Valeur valide est « déclenchement » ou « continue ».
+ **--emplacement** &lt;emplacement > : le nom de l’emplacement à redémarrer.

**supprimer la tâche site [options] &lt;jobName > &lt;fonction > [nom]**

Cette commande supprime la tâche web spécifiée.

Cette commande prend en charge les options supplémentaires suivantes :

+ **: nom de la tâche** &lt;nom de la tâche > requis. Le nom de la webjob.
+ **: type de la tâche** &lt;de type travail > requis. Le type de la webjob. Valeur valide est « déclenchement » ou « continue ».
+ **-q** ou **--silencieux**: ne pas demander de confirmation. Utilisez cette option dans les scripts automatisés.
+ **--emplacement** &lt;emplacement > : le nom de l’emplacement à redémarrer.

**téléchargement de travail de site [options] &lt;jobName > &lt;fonction > <jobFile> [nom]**

Cette commande supprime la tâche web spécifiée.

Cette commande prend en charge les options supplémentaires suivantes :

+ **: nom de la tâche** &lt;nom de la tâche > : obligatoire. Le nom de la webjob.
+ **: type de la tâche** &lt;de type travail > : obligatoire. Le type de la webjob. Valeur valide est « déclenchement » ou « continue ».
+ **--fichier de travail** &lt;-fichier de travail > : obligatoire. Le fichier de travail.
+ **--emplacement** &lt;emplacement > : le nom de l’emplacement à redémarrer.

**site de début de la tâche [options] &lt;jobName > &lt;fonction > [nom]**

Cette commande démarre la tâche web spécifiée.

Cette commande prend en charge les options supplémentaires suivantes :

+ **: nom de la tâche** &lt;nom de la tâche > : obligatoire. Le nom de la webjob.
+ **: type de la tâche** &lt;de type travail > : obligatoire. Le type de la webjob. Valeur valide est « déclenchement » ou « continue ».
+ **--emplacement** &lt;emplacement > : le nom de l’emplacement à redémarrer.

**fin de la tâche site [options] &lt;jobName > &lt;fonction > [nom]**

Cette commande arrête le projet de site web spécifié. Seuls les travaux continues peuvent être arrêtés.

Cette commande prend en charge les options supplémentaires suivantes :

+ **: nom de la tâche** &lt;nom de la tâche > : obligatoire. Le nom de la webjob.
+ **--emplacement** &lt;emplacement > : le nom de l’emplacement à redémarrer.

###<a name="commands-to-manage-your-web-jobs-history"></a>Commandes permettant de gérer l’historique de vos projets Web

**liste de l’historique du travail site [options] [jobName] [nom]**

Cette commande affiche l’historique des exécutions de la tâche web spécifiée.

Cette commande prend en charge les options supplémentaires suivantes :

+ **: nom de la tâche** &lt;nom de la tâche > : obligatoire. Le nom de la webjob.
+ **--emplacement** &lt;emplacement > : le nom de l’emplacement à redémarrer.

**afficher l’historique de travail site [options] [jobName] [runId] [nom]**

Cette commande Obtient les détails du travail exécuté pour le projet de site web spécifié.

Cette commande prend en charge les options supplémentaires suivantes :

+ **: nom de la tâche** &lt;nom de la tâche > : obligatoire. Le nom de la webjob.
+ **: id d’exécution** &lt;id d’exécution > : facultatif. Id de l’historique d’exécution. Si non spécifié, afficher la dernière exécution.
+ **--emplacement** &lt;emplacement > : le nom de l’emplacement à redémarrer.

###<a name="commands-to-manage-your-web-app-diagnostics"></a>Commandes pour gérer vos diagnostics d’application web

**journal du site télécharger [options] [nom]**

Télécharger un fichier .zip qui contient les tests de diagnostic de votre application web.

    ~$ azure site log download
    info:    Executing command site log download
    Web site name: mydemosite
    + Getting sites
    + Getting site information
    + Downloading diagnostic log to diagnostics.zip
    info:    site log download command OK

**dernières lignes du journal site [options] [nom]**

Cette commande connecte votre terminal pour le service de diffusion en continu de journal.

    ~$ azure site log tail
    info:    Executing command site log tail
    Web site name: mydemosite
    + Getting sites
    + Getting site information
    2013-11-19T17:24:17  Welcome, you are now connected to log-streaming service.

**journal de site définie [options] [nom]**

Cette commande configure les options de diagnostics pour votre application web.

    ~$ azure site log set -a
    info:    Executing command site log set
    + Getting output options
    help:    Output:
      1) file
      2) storage
      : 1
    Web site name: mydemosite
    + Getting locations
    + Getting sites
    + Getting site information
    + Getting diagnostic settings
    + Updating diagnostic settings
    info:    site log set command OK

###<a name="commands-to-manage-your-web-app-repositories"></a>Commandes pour gérer les référentiels de votre application web

**branche du référentiel site [options] &lt;branche > [nom]**

**supprimer le site référentiel [options] [nom]**

**synchronisation de référentiel de site [options] [nom]**

###<a name="commands-to-manage-your-web-app-scaling"></a>Commandes pour gérer votre application web échelle

**mode d’échelle site [options] &lt;mode > [nom]**

**groupement de serveurs site instances [options] &lt;instances > [nom]**


## <a name="commands-to-manage-azure-mobile-services"></a>Commandes pour gérer les Services de Mobile Azure

Les Services mobiles Azure regroupe un ensemble de services Azure qui activent les fonctionnalités de back-end pour vos applications. Commandes de Services mobiles sont réparties dans les catégories suivantes :

+ [Commandes pour gérer les instances de service mobile](#Mobile_Services)
+ [Commandes permettant de gérer la configuration du service mobile](#Mobile_Configuration)
+ [Commandes pour gérer les tables du service mobile](#Mobile_Tables)
+ [Commandes pour gérer des scripts du service mobile](#Mobile_Scripts)
+ [Commandes permettant de gérer des tâches planifiées](#Mobile_Jobs)
+ [Commandes de mise à l’échelle d’un service mobile](#Mobile_Scale)

Les options suivantes s’appliquent à la plupart des commandes de Services mobiles :

+ **-h** ou **--aide**: affichage des informations sur l’utilisation de sortie.
+ **-s `<id>` ** ou **: abonnement `<id>` **: utiliser un abonnement spécifique, spécifiée en tant que `<id>`.
+ **-v** ou **--commentaires**: écrire la sortie détaillée.
+ **--json**: sortie d’écrire le JSON.

### <a name="Mobile_Services"></a>Commandes pour gérer les instances de service mobile

**emplacements mobiles [options]**

Cette commande répertorie les emplacements géographiques pris en charge par les Services mobiles.

    ~$ azure mobile locations
    info:    Executing command mobile locations
    info:    East US (default)
    info:    West US
    info:    North Europe

**Création de Mobile [options] [ServiceName%] [sqlAdminUsername] [sqlAdminPassword]**

Cette commande crée un service mobile et une de base de données SQL server.

    ~$ azure mobile create todolist your_login_name Secure$Password
    info:    Executing command mobile create
    + Creating mobile service
    info:    Overall application state: Healthy
    info:    Mobile service (todolist) state: ProvisionConfigured
    info:    SQL database (todolist_db) state: Provisioned
    info:    SQL server (e96ean1c6v) state: ProvisionConfigured
    info:    mobile create command OK

Cette commande prend en charge les options supplémentaires suivantes :

+ **- r `<sqlServer>` ** ou **--SQL Server `<sqlServer>` **: utiliser un serveur de base de données SQL, spécifié en tant que `<sqlServer>`.
+ **-d `<sqlDb>` ** ou **: sqlDb `<sqlDb>` **: utiliser SQL base de données existante, spécifiée en tant que `<sqlDb>`.
+ **-l `<location>` ** ou **--emplacement `<location>` **: créer le service dans un emplacement spécifique, spécifié en tant que `<location>`. Exécutez azure emplacements mobiles pour obtenir la liste des emplacements disponibles.
+ **--sqlLocation `<location>` **: créer le serveur SQL dans un spécifique `<location>`; par défaut, l’emplacement du service mobile.

**supprimer mobile [options] [ServiceName%]**

Cette commande supprime un service mobile et son de la base de données SQL server.

    ~$ azure mobile delete todolist -a -q
    info:    Executing command mobile delete
    data:    Mobile service todolist
    data:    SQL database todolistAwrhcL60azo1C401
    data:    SQL server fh1kvbc7la
    + Deleting mobile service
    info:    Deleted mobile service
    + Deleting SQL server
    info:    Deleted SQL server
    + Deleting mobile application
    info:    Deleted mobile application
    info:    mobile delete command OK

Cette commande prend en charge les options supplémentaires suivantes :

+ **-d** ou **--deleteData**: supprimer toutes les données de ce service mobile à partir de la base de données.
+ **-** ou **--deleteAll**: supprimer la base de données de SQL et le serveur.
+ **-q** ou **--silencieux**: ne pas demander de confirmation. Utilisez cette option dans les scripts automatisés.

**liste mobile [options]**

Cette commande répertorie vos services mobiles.

    ~$ azure mobile list
    info:    Executing command mobile list
    data:    Name          State  URL
    data:    ------------  -----  --------------------------------------
    data:    todolist      Ready  https://todolist.azure-mobile.net/
    data:    mymobileapp   Ready  https://mymobileapp.azure-mobile.net/
    info:    mobile list command OK

**Afficher portable [options] [ServiceName%]**

Cette commande affiche des détails sur un service mobile.

    ~$ azure mobile show todolist
    info:    Executing command mobile show
    + Getting information
    info:    Mobile application
    data:    status Healthy
    data:    Mobile service name todolist
    data:    Mobile service status ProvisionConfigured
    data:    SQL database name todolistAwrhcL60azo1C401
    data:    SQL database status Linked
    data:    SQL server name fh1kvbc7la
    data:    SQL server status Linked
    info:    Mobile service
    data:    name todolist
    data:    state Ready
    data:    applicationUrl https://todolist.azure-mobile.net/
    data:    applicationKey XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    data:    masterKey XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    data:    webspace WESTUSWEBSPACE
    data:    region West US
    data:    tables TodoItem
    info:    mobile show command OK

**redémarrage mobile [options] [ServiceName%]**

Cette commande redémarre une instance de service mobile.

    ~$ azure mobile restart todolist
    info:    Executing command mobile restart
    + Restarting mobile service
    info:    Service was restarted.
    info:    mobile restart command OK

**journal mobile [options] [ServiceName%]**

Cette commande renvoie les journaux de service mobile, filtrage de tous les types de journal mais `error`.

    ~$ azure mobile log todolist -t error
    info:    Executing command mobile log
    data:
    data:    timeCreated 2013-01-07T16:04:43.351Z
    data:    type error
    data:    source /scheduler/TestingLogs.js
    data:    message This is an error.
    data:
    info:    mobile log command OK

Cette commande prend en charge les options supplémentaires suivantes :

+ **- r `<query>` ** ou **: requête `<query>` **: exécute la requête du journal spécifié.
+ **-t `<type>` ** ou **: type de `<type>` **: filtrer les journaux retournés par entrée de `<type>`, qui peut être `information`, `warning`, ou `error`.
+ **-k `<skip>` ** ou **--Ignorer `<skip>` **: ignore le nombre de lignes spécifié par `<skip>`.
+ **-p `<top>` ** ou **--haut `<top>` **: renvoie un nombre déterminé de lignes spécifié par `<top>`.

> [AZURE.NOTE] La **--requête** paramètre est prioritaire sur **--type**, **--Ignorer**, et **--haut**.

**Mobile recover [options] [unhealthyservicename] [healthyservicename]**

Cette commande récupère un service mobile défectueux en la déplaçant vers un service mobile sain dans une région différente.

Cette commande prend en charge l’option supplémentaire suivante :

**-q** ou **--silencieux**: supprimer l’invite de confirmation de la restauration.

**clé mobile Régénérer [options] [ServiceName%] [type]**

Cette commande régénère la clé d’application de service mobile.

    ~$ azure mobile key regenerate todolist application
    info:    Executing command mobile key regenerate
    info:    New application key is SmLorAWVfslMcOKWSsuJvuzdJkfUpt40
    info:    mobile key regenerate command OK

Types de clés sont `master` et `application`.

> [AZURE.NOTE] Lorsque vous régénérez les clés, les clients qui utilisent l’ancienne clé peuvent être impossible d’accéder à votre service mobile. Lorsque vous régénérez la clé de l’application, vous devez mettre à jour votre application avec la nouvelle valeur de clé.

**jeu de clés mobile [options] [ServiceName%] [type] [valeur]**

Cette commande définit la clé de service mobile à une valeur spécifique.


### <a name="Mobile_Configuration"></a>Commandes permettant de gérer la configuration du service mobile

**liste de configuration mobile [options] [ServiceName%]**

Cette commande répertorie les options de configuration pour un service mobile.

    ~$ azure mobile config list todolist
    info:    Executing command mobile config list
    + Getting mobile service configuration
    data:    dynamicSchemaEnabled true
    data:    microsoftAccountClientSecret Not configured
    data:    microsoftAccountClientId Not configured
    data:    microsoftAccountPackageSID Not configured
    data:    facebookClientId Not configured
    data:    facebookClientSecret Not configured
    data:    twitterClientId Not configured
    data:    twitterClientSecret Not configured
    data:    googleClientId Not configured
    data:    googleClientSecret Not configured
    data:    apnsMode none
    data:    apnsPassword Not configured
    data:    apnsCertifcate Not configured
    info:    mobile config list command OK

**configuration mobile obtenir [options] [ServiceName%] [clé]**

Cette commande permet d’obtenir une option de configuration spécifique pour un service mobile, schéma dynamique dans ce cas.

    ~$ azure mobile config get todolist dynamicSchemaEnabled
    info:    Executing command mobile config get
    data:    dynamicSchemaEnabled true
    info:    mobile config get command OK

**jeu de configuration mobile [options] [ServiceName%] [clé] [valeur]**

Cette commande définit une option de configuration spécifique pour un service mobile, schéma dynamique dans ce cas.

    ~$ azure mobile config set todolist dynamicSchemaEnabled false
    info:    Executing command mobile config set
    info:    mobile config set command OK


### <a name="Mobile_Tables"></a>Commandes pour gérer les tables du service mobile

**liste de table mobile [options] [ServiceName%]**

Cette commande répertorie toutes les tables de votre service mobile.

    ~$azure mobile table list todolist
    info:    Executing command mobile table list
    data:    Name      Indexes  Rows
    data:    --------  -------  ----
    data:    Channel   1        0
    data:    TodoItem  1        0
    info:    mobile table list command OK

**table mobile afficher [options] [ServiceName%] [tablename]**

Cette commande affiche retourne des détails concernant une table spécifique.

    ~$azure mobile table show todolist
    info:    Executing command mobile table show
    + Getting table information
    info:    Table statistics:
    data:    Number of records 5
    info:    Table operations:
    data:    Operation  Script       Permissions
    data:    ---------  -----------  -----------
    data:    insert     1900 bytes   user
    data:    read       Not defined  user
    data:    update     Not defined  user
    data:    delete     Not defined  user
    info:    Table columns:
    data:    Name  Type           Indexed
    data:    ----  -------------  -------
    data:    id    bigint(MSSQL)  Yes
    data:    text      string
    data:    complete  boolean
    info:    mobile table show command OK

**table mobile créer [options] [ServiceName%] [tablename]**

Cette commande crée une table.

    ~$azure mobile table create todolist Channels
    info:    Executing command mobile table create
    + Creating table
    info:    mobile table create command OK

Cette commande prend en charge l’option supplémentaire suivante :

+ **-p `&lt;permissions>` ** ou **: autorisations `&lt;permissions>` **: liste délimitée par des virgules de `<operation>` = `<permission>` paires, où `<operation>` est `insert`, `read`, `update`, ou `delete` et `&lt;permissions>` est `public`, `application` (par défaut), `user`, ou `admin`.

**lire les données mobiles [options] [ServiceName%] [tablename] [requête]**

Cette commande lit les données d’une table.

    ~$azure mobile data read todolist TodoItem
    info:    Executing command mobile data read
    data:    id  text     complete
    data:    --  -------  --------
    data:    1   item #1  false
    data:    2   item #2  true
    data:    3   item #3  false
    data:    4   item #4  true
    info:    mobile data read command OK

Cette commande prend en charge les options supplémentaires suivantes :

+ **-k `<skip>` ** ou **--Ignorer `<skip>` **: ignore le nombre de lignes spécifié par `<skip>`.
+ **-t `<top>` ** ou **--haut `<top>` **: renvoie un nombre déterminé de lignes spécifié par `<top>`.
+ **-l** ou **--liste**: renvoie les données dans un format de liste.

**mise à jour de la table mobile [options] [ServiceName%] [tablename]**

Cette commande modifie des autorisations de suppression sur une table uniquement aux administrateurs.

    ~$azure mobile table update todolist Channels -p delete=admin
    info:    Executing command mobile table update
    + Updating permissions
    info:    Updated permissions
    info:    mobile table update command OK

Cette commande prend en charge les options supplémentaires suivantes :

+ **-p `&lt;permissions>` ** ou **: autorisations `&lt;permissions>` **: liste délimitée par des virgules de `<operation>` = `<permission>` paires, où `<operation>` est `insert`, `read`, `update`, ou `delete` et `&lt;permissions>` est `public`, `application` (par défaut), `user`, ou `admin`.
+ **--Supprimer une colonne `<columns>` **: liste délimitée de colonnes à supprimer, en tant que `<columns>`.
+ **-q** ou **--silencieux**: supprime les colonnes sans demander de confirmation.
+ **--addIndex `<columns>` **: liste délimitée de colonnes à inclure dans l’index.
+ **--deleteIndex `<columns>` **: la liste délimitée des colonnes à exclure de l’index.

**table mobile supprimer [options] [ServiceName%] [tablename]**

Cette commande supprime une table.

    ~$azure mobile table delete todolist Channels
    info:    Executing command mobile table delete
    Do you really want to delete the table (yes/no): yes
    + Deleting table
    info:    mobile table delete command OK

Spécifiez le paramètre - q pour supprimer la table sans confirmation. Cela, pour éviter un blocage des scripts d’automatisation.

**[options] [ServiceName%] [tablename] de tronquer des données mobiles**

Cette commande supprime toutes les lignes de données de la table.

    ~$azure mobile data truncate todolist TodoItem
    info:    Executing command mobile data truncate
    info:    There are 7 data rows in the table.
    Do you really want to delete all data from the table? (y/n): y
    info:    Deleted 7 rows.
    info:    mobile data truncate command OK


### <a name="Mobile_Scripts"></a>Commandes pour gérer des scripts

Commandes de cette section sont utilisés pour gérer les scripts de serveur qui appartiennent à un service mobile. Pour plus d’informations, voir [utilisation des scripts de serveur dans les Services mobiles](https://github.com/Azure/azure-mobile-services/blob/master/docs/mobile-services-how-to-use-server-scripts.md).

**liste de script mobile [options] [ServiceName%]**

Cette commande répertorie les scripts enregistrés, y compris les scripts de table et le planificateur.

    ~$azure mobile script list todolist
    info:    Executing command mobile script list
    + Getting script information
    info:    Table scripts
    data:    Name                   Size
    data:    ---------------------  ----
    data:    table/TodoItem.delete  256
    data:    table/Devices.insert   1660
    error:   Unable to get shared scripts
    info:    Scheduler scripts
    data:    Name                 Status     Interval   Last run   Next run
    data:    -------------------  ---------  ---------  ---------  ---------
    data:    scheduler/undefined  undefined  undefined  undefined  undefined
    data:    scheduler/undefined  undefined  undefined  undefined  undefined
    info:    mobile script list command OK

**téléchargement du script de Mobile [options] [ServiceName%] [nom_script]**

Cette commande télécharge le script inséré à partir de la table TodoItem dans un fichier nommé `todoitem.insert.js` dans les `table` sous-dossier.

    ~$azure mobile script download todolist table/todoitem.insert.js
    info:    Executing command mobile script download
    info:    Saved script to ./table/todoitem.insert.js
    info:    mobile script download command OK

Cette commande prend en charge les options supplémentaires suivantes :

+ **-p `<path>` ** ou **: chemin d’accès `<path>` **: l’emplacement du fichier dans lequel enregistrer le script, où le répertoire de travail en cours est la valeur par défaut.
+ **-f `<file>` ** ou **--fichier de `<file>` **: le nom du fichier dans lequel enregistrer le script.
+ **-o** ou **: substituer**: remplacer un fichier existant.
+ **-c** ou **--console**: écrire le script dans la console au lieu d’un fichier.

**téléchargement du script mobile [options] [ServiceName%] [nom_script]**

Cette commande transfère un script nommé `todoitem.insert.js` de la `table` sous-dossier.

    ~$azure mobile script upload todolist table/todoitem.insert.js
    info:    Executing command mobile script upload
    info:    mobile script upload command OK

Le nom du fichier doit être composé à partir des noms de table et le fonctionnement. Il doit se trouver dans le sous-dossier de table par rapport à l’emplacement où la commande est exécutée. Vous pouvez également utiliser le **-f `<file>` ** ou **--fichier de `<file>` ** paramètre pour spécifier un autre nom de fichier et un chemin d’accès au fichier qui contient le script pour enregistrer.


**suppression de script mobile [options] [ServiceName%] [nom_script]**

Cette commande supprime le script Insérer existant à partir de la table TodoItem.

    ~$azure mobile script delete todolist table/todoitem.insert.js
    info:    Executing command mobile script delete
    info:    mobile script delete command OK

### <a name="Mobile_Jobs"></a>Commandes permettant de gérer des tâches planifiées

Commandes de cette section sont utilisés pour gérer des tâches planifiées qui appartiennent à un service mobile. Pour plus d’informations, voir [planifier des tâches](http://msdn.microsoft.com/library/windowsazure/jj860528.aspx).

**liste des tâches mobiles [options] [ServiceName%]**

Cette commande répertorie les tâches planifiées.

    ~$azure mobile job list todolist
    info:    Executing command mobile job list
    info:    Scheduled jobs
    data:    Job name    Script name           Status    Interval     Last run              Next run
    data:    ----------  --------------------  --------  -----------  --------------------  --------------------
    data:    getUpdates  scheduler/getUpdates  enabled   15 [minute]  2013-01-14T16:15:00Z  2013-01-14T16:30:00Z
    info:    You can manipulate scheduled job scripts using the 'azure mobile script' command.
    info:    mobile job list command OK

**travail mobile créer [options] [ServiceName%] [jobname]**

Cette commande crée un projet nommé `getUpdates` qui est planifiée toutes les heures.

    ~$azure mobile job create -i 1 -u hour todolist getUpdates
    info:    Executing command mobile job create
    info:    Job was created in disabled state. You can enable the job using the 'azure mobile job update' command.
    info:    You can manipulate the scheduled job script using the 'azure mobile script' command.
    info:    mobile job create command OK

Cette commande prend en charge les options supplémentaires suivantes :

+ **-i `<number>` ** ou **: intervalle de `<number>` **: l’intervalle de la tâche, sous la forme d’un entier. La valeur par défaut est `15`.
+ **-u `<unit>` ** ou **: intervalUnit `<unit>` **: l’unité de l' _intervalle_, qui peut être une des valeurs suivantes :
    + **minute** (par défaut)
    + **heure**
    + **jour**
    + **mois**
    + **Aucun** (les travaux à la demande)
+ **-t `<time>`** **--startTime `<time>` ** L’heure de début de la première exécution du script, au format ISO. La valeur par défaut est `now`.

> [AZURE.NOTE] Nouvelles tâches sont créées dans un état désactivé parce qu’un script doit toujours être téléchargé. Utilisez la commande **téléchargement de script mobile** à télécharger un script et la commande de **mise à jour de travail mobile** pour activer la tâche.

**mise à jour de travail mobile [options] [ServiceName%] [jobname]**

La commande suivante active le désactivé `getUpdates` travail.

    ~$azure mobile job update -a enabled todolist getUpdates
    info:    Executing command mobile job update
    info:    mobile job update command OK

Cette commande prend en charge les options supplémentaires suivantes :

+ **-i `<number>` ** ou **: intervalle de `<number>` **: l’intervalle de la tâche, sous la forme d’un entier. La valeur par défaut est `15`.
+ **-u `<unit>` ** ou **: intervalUnit `<unit>` **: l’unité de l' _intervalle_, qui peut être une des valeurs suivantes :
    + **minute** (par défaut)
    + **heure**
    + **jour**
    + **mois**
    + **Aucun** (les travaux à la demande)
+ **-t `<time>`** **--startTime `<time>` ** L’heure de début de la première exécution du script, au format ISO. La valeur par défaut est `now`.
+ **- `<status>` ** ou **--état `<status>` **: l’état du travail, qui peut être `enabled` ou `disabled`.

**suppression du travail mobiles [options] [ServiceName%] [jobname]**

Cette commande supprime la tâche planifiée getUpdates à partir du serveur de la liste des tâches.

    ~$azure mobile job delete todolist getUpdates
    info:    Executing command mobile job delete
    info:    mobile job delete command OK

> [AZURE.NOTE] Suppression d’un travail supprime également le script chargé.

### <a name="Mobile_Scale"></a>Commandes de mise à l’échelle d’un service mobile

Commandes de cette section sont utilisés à l’échelle d’un service mobile. Pour plus d’informations, reportez-vous à la section [mise à l’échelle d’un service mobile](http://msdn.microsoft.com/library/windowsazure/jj193178.aspx).

**une échelle mobile afficher [options] [ServiceName%]**

Cette commande affiche les informations d’échelle, y compris le mode de calcul en cours et le nombre d’instances.

    ~$azure mobile scale show todolist
    info:    Executing command mobile scale show
    data:    webspace WESTUSWEBSPACE
    data:    computeMode Free
    data:    numberOfInstances 1
    info:    mobile scale show command OK

**une échelle mobile modifier [options] [ServiceName%]**

Cette commande modifie l’échelle du service mobile gratuites au mode de prime.

    ~$azure mobile scale change -c Reserved -i 1 todolist
    info:    Executing command mobile scale change
    + Rescaling the mobile service
    info:    mobile scale change command OK

Cette commande prend en charge les options supplémentaires suivantes :

+ **- c `<mode>` ** ou **: computeMode `<mode>` **: le mode de calcul doit être `Free` ou `Reserved`.
+ **-i `<count>` ** ou **: numberOfInstances `<count>` **: le nombre d’instances utilisés lors de l’exécution en mode réservé.

> [AZURE.NOTE] Lorsque vous définissez le mode de calcul `Reserved`, tous vos services mobiles dans la même région s’exécuter en mode de prime.


###<a name="commands-to-enable-preview-features-for-your-mobile-service"></a>Commandes pour activer les fonctionnalités de votre Service Mobile

**liste d’aperçu mobile [options] [ServiceName%]**

Cette commande affiche les fonctionnalités disponibles sur le service spécifié et indique si elles sont activées.

    ~$ azure mobile preview list mysite
    info:    Executing command mobile preview list
    + Getting preview features
    data:    Preview feature  Enabled
    data:    ---------------  -------
    data:    SourceControl    No
    data:    Users            No
    info:    You can enable preview features using the 'azure mobile preview enable' command.
    info:    mobile preview list command OK

**Activer l’aperçu mobile [options] [ServiceName%] [NomFonctionnalité]**

Cette commande active la fonctionnalité d’aperçu spécifié pour un service mobile. Une fois activé, fonctionnalités ne peut pas être désactivées pour un service mobile.

###<a name="commands-to-manage-your-mobile-service-apis"></a>Commandes pour gérer votre service mobile API

**liste des api mobile [options] [ServiceName%]**

Cette commande affiche une liste mobile service API personnalisé que vous avez créés pour votre service mobile.

    ~$ azure mobile api list mysite
    info:    Executing command mobile api list
    + Retrieving list of APIs
    info:    APIs
    data:    Name                  Get          Put          Post         Patch        Delete
    data:    --------------------  -----------  -----------  -----------  -----------  -----------
    data:    myCustomRetrieveAPI   application  application  application  application  application
    info:    You can manipulate API scripts using the 'azure mobile script' command.
    info:    mobile api list command OK

**api mobile créer [options] [ServiceName%] [apiname]**

Crée une API personnalisé du service mobile

    ~$ azure mobile api create mysite myCustomRetrieveAPI
    info:    Executing command mobile api create
    + Creating custom API: 'myCustomRetrieveAPI'
    info:    API was created successfully. You can modify the API using the 'azure mobile script' command.
    info:    mobile api create command OK

Cette commande prend en charge l’option supplémentaire suivante :

**-p** ou **--autorisations** &lt;autorisations > : une liste délimitée par des virgules de &lt;méthode > =&lt;autorisation > paires.

**mise à jour des api mobile [options] [ServiceName%] [apiname]**

Cette commande met à jour l’API personnalisé du service mobile spécifié.

Cette commande prend en charge l’option supplémentaire suivante :

Cette commande prend en charge les options supplémentaires suivantes :

+ **-p** ou **--autorisations** &lt;autorisations > : une liste délimitée par des virgules de &lt;méthode > =&lt;autorisation > paires.
+ **-f** ou **--force**: remplace les modifications personnalisées apportées au fichier de métadonnées d’autorisations.

**api mobile supprimer [options] [ServiceName%] [apiname]**

    ~$ azure mobile api delete mysite myCustomRetrieveAPI
    info:    Executing command mobile api delete
    + Deleting API: 'myCustomRetrieveAPI'
    info:    mobile api delete command OK

Cette commande supprime l’API personnalisé du service mobile spécifié.

###<a name="commands-to-manage-your-mobile-application-app-settings"></a>Commandes pour gérer les paramètres d’application de votre application mobile

**liste de Mobile appsetting [options] [ServiceName%]**

Cette commande affiche les paramètres d’application application mobile pour le service spécifié.

    ~$ azure mobile appsetting list mysite
    info:    Executing command mobile appsetting list
    + Retrieving app settings
    data:    Name               Value
    data:    -----------------  -----
    data:    enablebetacontent  true
    info:    mobile appsetting list command OK

**Mobile appsetting ajouter [options] [ServiceName%] [nom] [valeur]**

Cette commande ajoute un paramètre d’application personnalisé pour votre service mobile.

    ~$ azure mobile appsetting add mysite enablebetacontent true
    info:    Executing command mobile appsetting add
    + Retrieving app settings
    + Adding app setting
    info:    mobile appsetting add command OK

**Mobile appsetting supprimer [options] [ServiceName%] [nom]**

Cette commande supprime le paramétrage de l’application spécifiée pour votre service mobile.

    ~$ azure mobile appsetting delete mysite enablebetacontent
    info:    Executing command mobile appsetting delete
    + Retrieving app settings
    + Removing app setting 'enablebetacontent'
    info:    mobile appsetting delete command OK

**Mobile appsetting afficher [options] [ServiceName%] [nom]**

Cette commande supprime le paramétrage de l’application spécifiée pour votre service mobile.

    ~$ azure mobile appsetting show mysite enablebetacontent
    info:    Executing command mobile appsetting show
    + Retrieving app settings
    info:    enablebetacontent: true
    info:    mobile appsetting show command OK

## <a name="manage-tool-local-settings"></a>Gérer les paramètres locaux de l’outil

Votre ID d’abonnement et le nom du compte de stockage par défaut sont des paramètres locaux.

**liste de configuration [options]**

Cette commande affiche les paramètres de configuration.

    ~$ azure config list
    info:   Displaying config settings
    data:   Setting                Value
    data:   ---------------------  ------------------------------------
    data:   subscription           32-digit-subscription-key
    data:   defaultStorageAccount  name

**jeu de configuration [options] &lt;nom de&gt;,&lt;valeur&gt;**

Cette commande permet de modifier un paramètre de configuration.

    ~$ azure config set defaultStorageAccount myname
    info:   Setting 'defaultStorageAccount' to value 'myname'
    info:   Changes saved.

## <a name="commands-to-manage-service-bus"></a>Commandes pour gérer le Service de Bus

Utilisez ces commandes pour gérer votre compte du Bus des services

**SB, espace de noms à cocher [options] &lt;nom >**

Vérifiez qu’un espace de noms de bus de service est disponible et juridiques.

**créer de l’espace de noms SB &lt;nom > &lt;emplacement >**

Crée un espace de noms du Bus de Service.

    ~$ azure sb namespace create mysbnamespacea-test "West US"
    info:    Executing command sb namespace create
    + Creating namespace mysbnamespacea-test in region West US
    data:    Name: mysbnamespacea-test
    data:    Region: West US
    data:    DefaultKey: fBu8nQ9svPIesFfMFVhCFD+/sY0rRbifWMoRpYy0Ynk=
    data:    Status: Activating
    data:    CreatedAt: 2013-11-14T16:23:29.32Z
    data:    AcsManagementEndpoint: https://mysbnamespacea-test-sb.accesscontrol.windows.net/
    data:    ServiceBusEndpoint: https://mysbnamespacea-test.servicebus.windows.net/

    data:    ConnectionString: Endpoint=sb://mysbnamespacea-test.servicebus.windows.
    net/;SharedSecretIssuer=owner;SharedSecretValue=fBu8nQ9svPIesFfMFVhCFD+/sY0rRbif
    WMoRpYy0Ynk=
    data:    SubscriptionId: 8679c8be3b0549d9b8fb4bd232a48931
    data:    Enabled: true
    data:    _: [object Object]
    info:    sb namespace create command OK


**supprimer de l’espace de noms SB &lt;nom >**

Supprimer un espace de noms.

    ~$ azure sb namespace delete mysbnamespacea-test
    info:    Executing command sb namespace delete
    Delete namespace mysbnamespacea-test? [y/n] y
    + Deleting namespace mysbnamespacea-test
    info:    sb namespace delete command OK

**liste d’espace de noms SB**

Liste de tous les espaces de noms créés pour votre compte.

    ~$ azure sb namespace list
    info:    Executing command sb namespace list
    + Getting namespaces
    data:    Name                 Region   Status
    data:    -------------------  -------  ------
    data:    mysbnamespacea-test  West US  Active
    info:    sb namespace list command OK


**liste d’emplacement d’espace de noms SB**

Afficher une liste de tous les emplacements de l’espace de noms disponibles.

    ~$ azure sb namespace location list
    info:    Executing command sb namespace location list
    + Getting locations
    data:    Name              Code
    data:    ----------------  ----------------
    data:    East Asia         East Asia
    data:    West Europe       West Europe
    data:    North Europe      North Europe
    data:    East US           East US
    data:    Southeast Asia    Southeast Asia
    data:    North Central US  North Central US
    data:    West US           West US
    data:    South Central US  South Central US
    info:    sb namespace location list command OK

**afficher d’espace de noms SB &lt;nom >**

Afficher des détails sur un espace de noms spécifique.

    ~$ azure sb namespace show mysbnamespacea-test
    info:    Executing command sb namespace show
    + Getting namespace
    data:    Name: mysbnamespacea-test
    data:    Region: West US
    data:    DefaultKey: fBu8nQ9svPIesFfMFVhCFD+/sY0rRbifWMoRpYy0Ynk=
    data:    Status: Active
    data:    CreatedAt: 2013-11-14T16:23:29.32Z
    data:    AcsManagementEndpoint: https://mysbnamespacea-test-sb.accesscontrol.windows.net/
    data:    ServiceBusEndpoint: https://mysbnamespacea-test.servicebus.windows.net/

    data:    ConnectionString: Endpoint=sb://mysbnamespacea-test.servicebus.windows.
    net/;SharedSecretIssuer=owner;SharedSecretValue=fBu8nQ9svPIesFfMFVhCFD+/sY0rRbif
    WMoRpYy0Ynk=
    data:    SubscriptionId: 8679c8be3b0549d9b8fb4bd232a48931
    data:    Enabled: true
    data:    UpdatedAt: 2013-11-14T16:25:37.85Z
    info:    sb namespace show command OK

**vérification de l’espace de noms SB &lt;nom >**

Vérifiez si l’espace de noms est disponible.

## <a name="commands-to-manage-your-storage-objects"></a>Commandes pour gérer les objets de stockage

###<a name="commands-to-manage-your-storage-accounts"></a>Commandes pour gérer vos comptes de stockage

**liste des comptes de stockage [options]**

Cette commande affiche les comptes de stockage de votre abonnement.

    ~$ azure storage account list
    info:    Executing command storage account list
    + Getting storage accounts
    data:    Name             Label  Location
    data:    ---------------  -----  --------
    data:    mybasestorage           West US
    info:    storage account list command OK

**afficher de compte de stockage [options]<name>**

Cette commande affiche des informations sur le compte de stockage spécifié, notamment les propriétés URI et compte.

**compte de stockage créer [options]<name>**

Cette commande crée un compte de stockage basé sur les options fournies.

    ~$ azure storage account create mybasestorage --label PrimaryStorage --location "West US"
    info:    Executing command storage account create
    + Creating storage account
    info:    storage account create command OK

Cette commande prend en charge les options supplémentaires suivantes :

+ **-e** ou **: étiquette** &lt;étiquette > : l’étiquette pour le compte de stockage.
+ **-d** ou **--description** &lt;description > : le compte de stockage de description.
+ **-l** ou **--emplacement** &lt;nom > : la région géographique dans laquelle créer le compte de stockage.
+ **-un** ou **--groupe d’affinité** &lt;nom > : le groupe d’affinité à laquelle associer le compte de stockage. 
+ **: type**: indique le type de compte à créer : un stockage Standard avec option de redondance (LRS/ZRS/GRS/RAGRS) ou de stockage Premium (PLRS).

**jeu de compte de stockage [options]<name>**

Cette commande met à jour le compte de stockage spécifié.

    ~$ azure storage account set mybasestorage --kind Storage --sku-name GRS
    info:    Executing command storage account set
    + Updating storage account
    info:    storage account set command OK

Cette commande prend en charge les options supplémentaires suivantes :

+ **-e** ou **: étiquette** &lt;étiquette > : l’étiquette pour le compte de stockage.
+ **-d** ou **--description** &lt;description > : le compte de stockage de description.
+ **-l** ou **--emplacement** &lt;nom > : la région géographique dans laquelle créer le compte de stockage.
+ **: type**: indique le nouveau type de compte : soit stockage Standard avec option de redondance (LRS/ZRS/GRS/RAGRS) ou de stockage Premium (PLRS).

**suppression de compte de stockage [options]<name>**

Cette commande supprime le compte de stockage spécifié.

Cette commande prend en charge l’option supplémentaire suivante :

**-q** ou **--silencieux**: ne pas demander de confirmation. Utilisez cette option dans les scripts automatisés.

###<a name="commands-to-manage-your-storage-account-keys"></a>Commandes pour gérer vos clés de compte de stockage

**liste des clés de compte de stockage [options]<name>**

Cette commande répertorie les clés primaires et secondaires pour le compte de stockage spécifié.

**clés de compte de stockage renouvellement [options]<name>**

###<a name="commands-to-manage-your-storage-container"></a>Commandes permettant de gérer votre conteneur de stockage

**liste de conteneurs de stockage [options] [prefix]**

Cette commande affiche la liste de conteneurs de stockage pour un compte de stockage spécifié. Le compte de stockage est spécifié par la chaîne de connexion ou de la clé de nom de compte et le compte de stockage.

Cette commande prend en charge les options supplémentaires suivantes :

+ **-p** ou **-préfixe** &lt;préfixe > : le préfixe de nom de conteneur de stockage.
+ **-un** ou **--nom de compte** &lt;nom de compte > : le nom de compte de stockage.
+ **-k** ou **--clé de compte** &lt;accountKey > : la clé de compte de stockage.
+ **-c** ou **: chaîne de connexion** &lt;connectionString > : la chaîne de connexion de stockage.
+ **--debug**: exécute la commande de stockage en mode débogage.

**afficher de conteneur de stockage [options] [conteneur]**
**conteneur de stockage créer [options] (conteneur)**

Cette commande crée un conteneur de stockage pour le compte de stockage spécifié. Le compte de stockage est spécifié par la chaîne de connexion ou de la clé de nom de compte et le compte de stockage.

Cette commande prend en charge les options supplémentaires suivantes :

+ **--conteneur** &lt;conteneur > : le nom du conteneur de stockage à créer.
+ **-p** ou **-préfixe** &lt;préfixe > : le préfixe de nom de conteneur de stockage.
+ **-un** ou **--nom de compte** &lt;nom de compte > : le nom de compte de stockage
+ **-k** ou **--clé de compte** &lt;accountKey > : la clé de compte de stockage
+ **-c** ou **: chaîne de connexion** &lt;connectionString > : la chaîne de connexion de stockage
+ **--debug**: exécute la commande de stockage en mode débogage.

**suppression de conteneur de stockage [options] (conteneur)**

Cette commande supprime le conteneur de stockage spécifié. Le compte de stockage est spécifié par la chaîne de connexion ou de la clé de nom de compte et le compte de stockage.

Cette commande prend en charge les options supplémentaires suivantes :

+ **--conteneur** &lt;conteneur > : le nom du conteneur de stockage à créer.
+ **-p** ou **-préfixe** &lt;préfixe > : le préfixe de nom de conteneur de stockage.
+ **-un** ou **--nom de compte** &lt;nom de compte > : le nom de compte de stockage.
+ **-k** ou **--clé de compte** &lt;accountKey > : la clé de compte de stockage.
+ **-c** ou **: chaîne de connexion** &lt;connectionString > : la chaîne de connexion de stockage.
+ **--debug**: exécute la commande de stockage en mode débogage.

**ensemble de conteneur de stockage [options] (conteneur)**

Cette commande définit la liste de contrôle d’accès pour le conteneur de stockage. Le compte de stockage est spécifié par la chaîne de connexion ou de la clé de nom de compte et le compte de stockage.

Cette commande prend en charge les options supplémentaires suivantes :

+ **--conteneur** &lt;conteneur > : le nom du conteneur de stockage à créer.
+ **-p** ou **-préfixe** &lt;préfixe > : le préfixe de nom de conteneur de stockage.
+ **-un** ou **--nom de compte** &lt;nom de compte > : le nom de compte de stockage.
+ **-k** ou **--clé de compte** &lt;accountKey > : la clé de compte de stockage.
+ **-c** ou **: chaîne de connexion** &lt;connectionString > : la chaîne de connexion de stockage.
+ **--debug**: exécute la commande de stockage en mode débogage.

###<a name="commands-to-manage-your-storage-blob"></a>Commandes pour gérer le blob de stockage

**liste de stockage de blob [options] [conteneur] [prefix]**

Cette commande renvoie une liste des blobs de stockage dans le conteneur de stockage spécifié.

Cette commande prend en charge les options supplémentaires suivantes :

+ **--conteneur** &lt;conteneur > : le nom du conteneur de stockage à créer.
+ **-p** ou **-préfixe** &lt;préfixe > : le préfixe de nom de conteneur de stockage.
+ **-un** ou **--nom de compte** &lt;nom de compte > : le nom de compte de stockage.
+ **-k** ou **--clé de compte** &lt;accountKey > : la clé de compte de stockage.
+ **-c** ou **: chaîne de connexion** &lt;connectionString > : la chaîne de connexion de stockage.
+ **--debug**: exécute la commande de stockage en mode débogage.

**blob de stockage afficher [options] [conteneur] [blob]**

Cette commande affiche les détails de l’objet blob de stockage spécifié.

Cette commande prend en charge les options supplémentaires suivantes :

+ **--conteneur** &lt;conteneur > : le nom du conteneur de stockage à créer.
+ **-p** ou **-préfixe** &lt;préfixe > : le préfixe de nom de conteneur de stockage.
+ **-un** ou **--nom de compte** &lt;nom de compte > : le nom de compte de stockage.
+ **-k** ou **--clé de compte** &lt;accountKey > : la clé de compte de stockage.
+ **-c** ou **: chaîne de connexion** &lt;connectionString > : la chaîne de connexion de stockage.
+ **--debug**: exécute la commande de stockage en mode debug.

**blob de stockage supprimer [options] [conteneur] [blob]**

Cette commande prend en charge les options supplémentaires suivantes :

+ **--conteneur** &lt;conteneur > : le nom du conteneur de stockage à créer.
+ **-b** ou **--blob** &lt;blobName > : le nom de l’objet blob de stockage à supprimer.
+ **-q** ou **--silencieux**: supprimer le blob de stockage spécifié sans confirmation.
+ **-un** ou **--nom de compte** &lt;nom de compte > : le nom de compte de stockage.
+ **-k** ou **--clé de compte** &lt;accountKey > : la clé de compte de stockage.
+ **-c** ou **: chaîne de connexion** &lt;connectionString > : la chaîne de connexion de stockage.
+ **--debug**: exécute la commande de stockage en mode debug.

**téléchargement de blob de stockage [options] [fichiers] [conteneur] [blob]**

Cette commande télécharger le fichier spécifié dans le blob de stockage specified\.

Cette commande prend en charge les options supplémentaires suivantes :

+ **--conteneur** &lt;conteneur > : le nom du conteneur de stockage à créer.
+ **-b** ou **--blob** &lt;blobName > : le nom de l’objet blob de stockage à télécharger.
+ **-t** ou **--blobtype** &lt;blobtype > : le type de stockage blob : Page ou bloc.
+ **-p** ou **--Propriétés** &lt;Propriétés > : les propriétés de blob de stockage pour le fichier téléchargé. Les propriétés sont la clé = valeur paire s et séparés par un point-virgule ( ;). Les propriétés disponibles sont contentType, valeur contentEncoding, contentLanguage et cacheControl.
+ **-m** ou **métadonnées--** &lt;métadonnées > : les métadonnées de blob de stockage pour le fichier téléchargé. Métadonnées sont la clé de paires = valeur séparé par un d avec un point-virgule ( ;).
+ **--concurrenttaskcount** &lt;concurrenttaskcount > : le nombre maximal de demandes de téléchargement simultanées.
+ **-q** ou **--silencieux**: remplacer le blob de stockage spécifié sans confirmation.
+ **-un** ou **--nom de compte** &lt;nom de compte > : le nom de compte de stockage.
+ **-k** ou **--clé de compte** &lt;accountKey > : la clé de compte de stockage.
+ **-c** ou **: chaîne de connexion** &lt;connectionString > : la chaîne de connexion de stockage.
+ **--debug**: exécute la commande de stockage en mode debug.

**téléchargement de blob de stockage [options] [conteneur] [blob] [destination]**

Cette commande télécharge le blob de stockage spécifié.

Cette commande prend en charge les options supplémentaires suivantes :

+ **--conteneur** &lt;conteneur > : le nom du conteneur de stockage à créer.
+ **-b** ou **--blob** &lt;blobName > : le nom du stockage blob.
+ **-d** ou **--destination** [destination] : le chemin de fichier ou un répertoire de destination transfert.
+ **-m** ou **--checkmd5**: la vérification md5sum pour le fichier téléchargé.
+ **--concurrenttaskcount** &lt;concurrenttaskcount > le nombre maximal de demandes de téléchargement simultanées
+ **-q** ou **--silencieux**: remplacer le fichier de destination sans confirmation.
+ **-un** ou **--nom de compte** &lt;nom de compte > : le nom de compte de stockage.
+ **-k** ou **--clé de compte** &lt;accountKey > : la clé de compte de stockage.
+ **-c** ou **: chaîne de connexion** &lt;connectionString > : la chaîne de connexion de stockage.
+ **--debug**: exécute la commande de stockage en mode debug.

## <a name="commands-to-manage-sql-databases"></a>Commandes pour gérer les bases de données SQL

Utilisez ces commandes pour gérer vos bases de données SQL Azure

###<a name="commands-to-manage-sql-servers"></a>Commandes pour gérer les serveurs SQL.

Utilisez ces commandes pour gérer vos serveurs SQL

**SQL server créer &lt;administratorLogin > &lt;administratorPassword > &lt;emplacement >**

Créer un serveur de base de données

    ~$ azure sql server create test T3stte$t "West US"
    info:    Executing command sql server create
    + Creating SQL Server
    data:    Server Name i1qwc540ts
    info:    sql server create command OK

**afficher de serveur SQL &lt;nom >**

Afficher les détails du serveur.

    ~$ azure sql server show xclfgcndfg
    info:    Executing command sql server show
    + Getting SQL server
    data:    SQL Server Name xclfgcndfg
    data:    SQL Server AdministratorLogin msopentechforums
    data:    SQL Server Location West US
    data:    SQL Server FullyQualifiedDomainName xclfgcndfg.database.windows.net
    info:    sql server show command OK

**liste des serveurs SQL**

Obtenir la liste des serveurs.

    ~$ azure sql server list
    info:    Executing command sql server list
    + Getting SQL server
    data:    Name        Location
    data:    ----------  --------
    data:    xclfgcndfg  West US
    info:    sql server list command OK

**suppression du serveur SQL &lt;nom >**

Supprime un serveur

    ~$ azure sql server delete i1qwc540ts
    info:    Executing command sql server delete
    Delete server i1qwc540ts? [y/n] y
    + Removing SQL Server
    info:    sql server delete command OK

###<a name="commands-to-manage-sql-databases"></a>Commandes pour gérer les bases de données SQL

Utilisez ces commandes pour gérer vos bases de données SQL.

**base de données SQL créer [options] &lt;NomServeur > &lt;databaseName > &lt;administratorPassword >**

Crée une instance de base de données

    ~$ azure sql db create fr8aelne00 newdb test
    info:    Executing command sql db create
    Administrator password: ********
    + Creating SQL Server Database
    info:    sql db create command OK

**base de données SQL afficher [options] &lt;NomServeur > &lt;databaseName > &lt;administratorPassword >**

Afficher les détails de la base de données.

    C:\windows\system32>azure sql db show fr8aelne00 newdb test
    info:    Executing command sql db show
    Administrator password: ********
    + Getting SQL server databases
    data:    Database _ ContentRootElement=m:properties, id=https://fr8aelne00.datab
    ase.windows.net/v1/ManagementService.svc/Server2('fr8aelne00')/Databases(4), ter
    m=Microsoft.SqlServer.Management.Server.Domain.Database, scheme=http://schemas.m
    icrosoft.com/ado/2007/08/dataservices/scheme, link=[rel=edit, title=Database, hr
    ef=Databases(4), rel=http://schemas.microsoft.com/ado/2007/08/dataservices/relat
    ed/Server, type=application/atom+xml;type=entry, title=Server, href=Databases(4)
    /Server, rel=http://schemas.microsoft.com/ado/2007/08/dataservices/related/Servi
    ceObjective, type=application/atom+xml;type=entry, title=ServiceObjective, href=
    Databases(4)/ServiceObjective, rel=http://schemas.microsoft.com/ado/2007/08/data
    services/related/DatabaseMetrics, type=application/atom+xml;type=entry, title=Da
    tabaseMetrics, href=Databases(4)/DatabaseMetrics, rel=http://schemas.microsoft.c
    om/ado/2007/08/dataservices/related/DatabaseCopies, type=application/atom+xml;ty
    pe=feed, title=DatabaseCopies, href=Databases(4)/DatabaseCopies], title=, update
    d=2013-11-18T19:48:27Z, name=
    data:    Database Id 4
    data:    Database Name newdb
    data:    Database ServiceObjectiveId 910b4fcb-8a29-4c3e-958f-f7ba794388b2
    data:    Database AssignedServiceObjectiveId 910b4fcb-8a29-4c3e-958f-f7ba794388b2
    data:    Database ServiceObjectiveAssignmentState 1
    data:    Database ServiceObjectiveAssignmentStateDescription Complete
    data:    Database ServiceObjectiveAssignmentErrorCode
    data:    Database ServiceObjectiveAssignmentErrorDescription
    data:    Database ServiceObjectiveAssignmentSuccessDate
    data:    Database Edition Web
    data:    Database MaxSizeGB 1
    data:    Database MaxSizeBytes 1073741824
    data:    Database CollationName SQL_Latin1_General_CP1_CI_AS
    data:    Database CreationDate
    data:    Database RecoveryPeriodStartDate
    data:    Database IsSystemObject
    data:    Database Status 1
    data:    Database IsFederationRoot
    data:    Database SizeMB -1
    data:    Database IsRecursiveTriggersOn
    data:    Database IsReadOnly
    data:    Database IsFederationMember
    data:    Database IsQueryStoreOn
    data:    Database IsQueryStoreReadOnly
    data:    Database QueryStoreMaxSizeMB
    data:    Database QueryStoreFlushPeriodSeconds
    data:    Database QueryStoreIntervalLengthMinutes
    data:    Database QueryStoreClearAll
    data:    Database QueryStoreStaleQueryThresholdDays
    info:    sql db show command OK

**liste de base de données SQL [options] &lt;NomServeur > &lt;administratorPassword >**

Répertorier les bases de données.

    ~$ azure sql db list fr8aelne00 test
    info:    Executing command sql db list
    Administrator password: ********
    + Getting SQL server databases
    data:    Name    Edition  Collation                     MaxSizeInGB
    data:    ------  -------  ----------------------------  -----------
    data:    master  Web      SQL_Latin1_General_CP1_CI_AS  5
    info:    sql db list command OK

**base de données SQL supprimer [options] &lt;NomServeur > &lt;databaseName > &lt;administratorPassword >**

Supprime une base de données.

    ~$ azure sql db delete fr8aelne00 newdb test
    info:    Executing command sql db delete
    Administrator password: ********
    Delete database newdb? [y/n] y
    + Getting SQL server databases
    + Removing database
    info:    sql db delete command OK

###<a name="commands-to-manage-your-sql-server-firewall-rules"></a>Commandes pour gérer vos règles de pare-feu de SQL Server

Utilisez ces commandes pour gérer les règles de pare-feu de SQL Server

**SQL firewallrule créer [options] &lt;NomServeur > &lt;NomRègle > &lt;startIPAddress > &lt;endIPAddress >**

Créer une règle de pare-feu pour un SQL Server.

    ~$ azure sql firewallrule create fr8aelne00 allowed 131.107.0.0 131.107.255.255
    info:    Executing command sql firewallrule create
    + Creating Firewall Rule
    info:    sql firewallrule create command OK

**SQL firewallrule afficher [options] &lt;NomServeur > &lt;ruleName >**

Afficher les détails de la règle de pare-feu.

    ~$ azure sql firewallrule show fr8aelne00 allowed
    info:    Executing command sql firewallrule show
    + Getting firewall rule
    data:    Firewall rule Name allowed
    data:    Firewall rule Type Microsoft.SqlAzure.FirewallRule
    data:    Firewall rule State Normal
    data:    Firewall rule SelfLink https://management.core.windows.net/9e672699-105
    5-41ae-9c36-e85152f2e352/services/sqlservers/servers/fr8aelne00/firewallrules/allowed
    data:    Firewall rule ParentLink https://management.core.windows.net/9e672699-1
    055-41ae-9c36-e85152f2e352/services/sqlservers/servers/fr8aelne00
    data:    Firewall rule StartIPAddress 131.107.0.0
    data:    Firewall rule EndIPAddress 131.107.255.255
    info:    sql firewallrule show command OK

**liste de firewallrule SQL [options] &lt;nom_serveur >**

Liste des règles de pare-feu.

    ~$ azure sql firewallrule list fr8aelne00
    info:    Executing command sql firewallrule list
    \data:    Name     Start IP address  End IP address
    data:    -------  ----------------  ---------------
    data:    allowed  131.107.0.0       131.107.255.255
    +
    info:    sql firewallrule list command OK

**SQL firewallrule supprimer [options] &lt;NomServeur > &lt;ruleName >**

Cette commande supprime une règle de pare-feu.

    ~$ azure sql firewallrule delete fr8aelne00 allowed
    info:    Executing command sql firewallrule delete
    Delete rule allowed? [y/n] y
    + Removing firewall rule
    info:    sql firewallrule delete command OK

## <a name="commands-to-manage-your-virtual-networks"></a>Commandes pour gérer vos réseaux virtuels

Utilisez ces commandes pour gérer vos réseaux virtuels

**réseau vnet créer [options] &lt;emplacement >**

Créer un réseau virtuel.

    ~$ azure network vnet create vnet1 --location "West US" -v
    info:    Executing command network vnet create
    info:    Using default address space start IP: 10.0.0.0
    info:    Using default address space cidr: 8
    info:    Using default subnet start IP: 10.0.0.0
    info:    Using default subnet cidr: 11
    verbose: Address Space [Starting IP/CIDR (Max VM Count)]: 10.0.0.0/8 (16777216)
    verbose: Subnet [Starting IP/CIDR (Max VM Count)]: 10.0.0.0/11 (2097152)
    verbose: Fetching Network Configuration
    verbose: Fetching or creating affinity group
    verbose: Fetching Affinity Groups
    verbose: Fetching Locations
    verbose: Creating new affinity group AG1
    info:    Using affinity group AG1
    verbose: Updating Network Configuration
    info:    network vnet create command OK

**réseau de vnet afficher &lt;nom >**

Afficher les détails d’un réseau virtuel.

    ~$ azure network vnet show vnet1
    info:    Executing command network vnet show
    + Fetching Virtual Networks
    data:    Name "vnet1"
    data:    Id "25786fbe-08e8-4e7e-b1de-b98b7e586c7a"
    data:    AffinityGroup "AG1"
    data:    State "Created"
    data:    AddressSpace AddressPrefixes 0 "10.0.0.0/8"
    data:    Subnets 0 Name "subnet-1"
    data:    Subnets 0 AddressPrefix "10.0.0.0/11"
    info:    network vnet show command OK

**liste de vnet de réseau**

Liste de tous les réseaux virtuels existants.

    ~$ azure network vnet list
    info:    Executing command network vnet list
    + Fetching Virtual Networks
    data:    Name        Status   AffinityGroup
    data:    ----------  -------  -------------
    data:    vnet1      Created  AG1
    data:    vnet2      Created  AG1
    data:    vnet3      Created  AG1
    data:    vnet4      Created  AG1
    info:    network vnet list command OK


**supprimer les vnet de réseau &lt;nom >**

Supprime le réseau virtuel spécifié.

    ~$ azure network vnet delete opentechvn1
    info:    Executing command network vnet delete
    + Fetching Network Configuration
    Delete the virtual network opentechvn1 ?  (y/n) y
    + Deleting the virtual network opentechvn1
    info:    network vnet delete command OK

**exportation de réseau [chemin]**

Pour la configuration de réseau avancées, vous pouvez exporter la configuration de votre réseau local. La configuration réseau exporté comprend des paramètres de serveur DNS, les paramètres de réseau virtuel, les paramètres de site de réseau local et d’autres paramètres.

**importation de réseau [chemin]**

Importer une configuration de réseau local.

**réseau serveurDNS enregistrer [options] &lt;dnsIP >**

Inscrire un serveur DNS que vous prévoyez d’utiliser pour la résolution de nom de configuration de votre réseau.

    ~$ azure network dnsserver register 98.138.253.109 --dns-id FrontEndDnsServer
    info:    Executing command network dnsserver register
    + Fetching Network Configuration
    + Updating Network Configuration
    info:    network dnsserver register command OK

**liste du serveurDNS réseau**

Liste de tous les serveurs DNS inscrits dans la configuration de votre réseau.

    ~$ azure network dnsserver list
    info:    Executing command network dnsserver list
    + Fetching Network Configuration
    data:    DNS Server ID         DNS Server IP
    data:    --------------------  --------------
    data:    DNS-bb39b4ac34d66a86  44.55.22.11
    data:    FrontEndDnsServer     98.138.253.109
    info:    network dnsserver list command OK

**Annuler l’inscription de réseau serveurDNS [options] &lt;dnsIP >**

Supprime une entrée de serveur DNS à partir de la configuration du réseau.

    ~$ azure network dnsserver unregister 77.88.99.11
    info:    Executing command network dnsserver unregister
    + Fetching Network Configuration
    Delete the DNS server entry dns-4 ( 77.88.99.11 ) %s ? (y/n) y
    + Deleting the DNS server entry dns-4 ( 77.88.99.11 )
    info:    network dnsserver unregister command OK

