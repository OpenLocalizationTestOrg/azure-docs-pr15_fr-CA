<properties
    pageTitle="Avant de déployer POC de pile Azure | Microsoft Azure"
    description="Permet d’afficher les conditions d’environnement et de matériel pour Azure pile POC (administrateur de service)."
    services="azure-stack"
    documentationCenter=""
    authors="ErikjeMS"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/12/2016"
    ms.author="erikje"/>

# <a name="azure-stack-deployment-prerequisites"></a>Conditions préalables au déploiement pile Azure

Avant de déployer Azure POC de pile ([De démonstration](azure-stack-poc.md)), assurez-vous que votre ordinateur remplit les conditions suivantes.
Les spécifications de déploiement technique Preview 2 pour cette phase sont les mêmes que celles requises pour Technical Preview 1. Par conséquent, vous pouvez utiliser le même matériel que celui utilisé pour l’aperçu unique précédent.

## <a name="hardware"></a>Matériel

| Composant | Minimum  | Recommandé |
|---|---|---|
| Disques durs : système d’exploitation | 1 disque de système d’exploitation avec un minimum de 200 Go disponible pour la partition système (SSD ou disque dur) | 1 disque de système d’exploitation avec un minimum de 200 Go disponible pour la partition système (SSD ou disque dur) |
| Disques : les données générales du POC de pile Azure | 4 disques. Chaque disque offre un minimum de 140 Go de capacité (SSD ou disque dur). Tous les disques disponibles seront utilisés. | 4 disques. Chaque disque offre un minimum de 250 Go de capacité (SSD ou disque dur). Tous les disques disponibles seront utilisés.|
| Calculer : processeur | Dual-Socket : cœurs physiques 12 (total)  | Double Socket : (total) 16 cœurs physiques |
| Calculer : mémoire | 96 GO DE RAM  | 128 GO DE RAM |
| Calculer : BIOS | Hyper-V activé (avec prise en charge de lattes)  | Hyper-V activé (avec prise en charge de lattes) |
| Réseau : carte réseau | Windows Server 2012 R2 Certification requis pour la carte réseau ; aucune des fonctionnalités spécialisées requises | Windows Server 2012 R2 Certification requis pour la carte réseau ; aucune des fonctionnalités spécialisées requises |
| Logo de certification de matériel | [Certifié pour Windows Server 2012 R2](http://windowsservercatalog.com/results.aspx?&chtext=&cstext=&csttext=&chbtext=&bCatID=1333&cpID=0&avc=79&ava=0&avq=0&OR=1&PGS=25&ready=0) |[Certifié pour Windows Server 2012 R2](http://windowsservercatalog.com/results.aspx?&chtext=&cstext=&csttext=&chbtext=&bCatID=1333&cpID=0&avc=79&ava=0&avq=0&OR=1&PGS=25&ready=0)|

**Configuration de lecteur de disque de données :** Tous les lecteurs de données doivent être de même type (tous les SAS ou tous de type SATA) et de capacité. Disques durs SAS sont utilisés, les disques doivent être apposées via un chemin d’accès unique (aucun MPIO, prise en charge des chemins d’accès multiples n’est fourni).

**Options de configuration des adaptateurs HBA**
 
- (Par défaut) Adaptateur HBA simple
- RAID HBA – carte doit être configuré en mode de « passer par »
- Adaptateur de bus hôte RAID – disques doivent être configurés en tant que disque unique, RAID-0

**Combinaisons de type de média et bus pris en charge**

-   DISQUES DURS SATA

-   DISQUE DUR SAS

-   DISQUE DUR RAID

-   RAID SSD (si le type de média est non spécifié/inconnu\*)

-   LES SSD SATA + DISQUE DUR SATA

-   SAS SSD + DISQUE DUR SAS

\*Contrôleurs RAID sans fonctionnalité pass-through ne reconnaissent pas le type de média. Ces contrôleurs de marque à la fois les disques durs et SSD comme non spécifié. Dans ce cas, le SSD servira au lieu de la mise en cache des périphériques de stockage permanent. Par conséquent, vous pouvez déployer le contrôle du concept Microsoft Azure pile sur ces SSDs.

**Exemple de HBA**: LSI 9207-8i, LSI-9300-8i ou LSI-9265-8i en mode Pass-Through

Exemples de configurations OEM sont disponibles.

## <a name="operating-system"></a>Système d'exploitation

| | **Configuration requise**  |
|---|---|
| **Version du système d’exploitation** | Windows Server 2012 R2 ou une version ultérieure. La version du système d’exploitation n’est pas critique avant le début du déploiement, comme vous allez démarrer l’ordinateur hôte dans le disque dur virtuel qui est inclus dans le zip d’installation de pile d’Azure. Le système d’exploitation et tous les correctifs requis sont déjà intégrés dans l’image. N’utilisez pas les touches pour activer des instances Windows Server utilisées dans le contrôle du concept.|

## <a name="deployment-requirements-check-tool"></a>Outil de vérifier les exigences de déploiement

Après avoir installé le système d’exploitation, vous pouvez utiliser le [Vérificateur de déploiement pour Azure pile technique Preview 2](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) pour confirmer que votre matériel répond à toutes les exigences.



## <a name="microsoft-azure-active-directory-accounts"></a>Comptes Active Directory de Microsoft Azure

Le déploiement de la VT de pile Microsoft Azure doit être connecté à Azure. Par conséquent, vous devez préparer un compte Active Directory de Microsoft Azure avant d’exécuter le script PowerShell de déploiement. Ce compte devienne l’administrateur Global pour le locataire Azure Active Directory. Elle servira à provisionner et déléguer les applications et les identités de service pour tous les services Azure pile qui interagissent avec Azure Active Directory et des API graphiques. Elle servira également en tant que propriétaire de l’abonnement de fournisseur par défaut (que vous pouvez modifier ultérieurement). Vous pouvez vous connecter à un portail d’administration de votre système d’Azure pile à l’aide de ce compte.

1. Créer un compte Azure qui est l’administrateur de l’annuaire pour au moins un Azure à Active Directory. Si vous avez déjà un, vous pouvez l’utiliser. Dans le cas contraire, vous pouvez en créer une gratuitement à [http://azure.microsoft.com/en-us/pricing/free-trial/](http://azure.microsoft.com/pricing/free-trial/) (en Chine, visitez le site <http://go.microsoft.com/fwlink/?LinkID=717821> à la place.)

    Enregistrez ces informations d’identification pour une utilisation à l’étape 6 de la commande [exécuter le script de déploiement PowerShell](azure-stack-run-powershell-script.md#run-the-powershell-deployment-script). Ce compte *d’administrateur de service* peut configurer et gérer des nuages de ressource, les comptes d’utilisateurs, plans de clients, quotas et prix. Dans le portail, ils peuvent créer des site Web nuages, nuages privés de machine virtuelle, créer des plans et gérer les abonnements de l’utilisateur.

2. [Créer](azure-stack-add-new-user-aad.md) au moins un compte de sorte que vous pouvez vous connecter à la démonstration de pile Azure comme un client.

  	| **Compte Azure d’Active Directory**  | **Pris en charge ?** |
  	|---|---| 
  	| Compte de travail ou à l’école avec abonnement d’Azure Public valide  | Oui |
  	| Microsoft Account avec abonnement d’Azure Public valide  | N° |
  	| Compte de travail ou à l’école avec abonnement d’Azure Chine valide  | Oui |
  	| Compte de travail ou à l’école avec nous gouvernement Azure abonnement valide  | Oui |


## <a name="network"></a>Réseau

### <a name="switch"></a>Commutateur

Un port disponible sur un commutateur pour l’ordinateur du contact.  

L’ordinateur de démonstration de pile Azure prend en charge la connexion à un port d’accès commuté ou d’un port trunk. Aucune des fonctionnalités spécialisées ne sont requis sur le commutateur. Si vous utilisez un port trunk ou si vous avez besoin configurer un ID de réseau VLAN, vous devez fournir l’ID de VLAN sous la forme d’un paramètre de déploiement. Vous pouvez voir des exemples dans la [liste des paramètres de déploiement](azure-stack-run-powershell-script.md).

### <a name="subnet"></a>Sous-réseau

Ne pas connecter l’ordinateur du contact pour les sous-réseaux suivants :
- 192.168.200.0/24
- 192.168.100.0/27
- 192.168.101.0/26
- 192.168.102.0/24
- 192.168.103.0/25
- 192.168.104.0/25

Ces sous-réseaux est réservées pour les réseaux internes au sein de l’environnement de VT de pile Microsoft Azure.

### <a name="ipv4ipv6"></a>IPv4/IPv6

IPv4 n’est pris en charge. Vous ne pouvez pas créer des réseaux IPv6.

### <a name="dhcp"></a>DHCP

Assurez-vous que le serveur DHCP est disponible sur le réseau que la carte réseau se connecte à. Si DHCP n’est pas disponible, vous devez préparer un réseau de IPv4 statique supplémentaire en plus de celui utilisé par l’hôte. Vous devez fournir cette adresse IP et la passerelle sous la forme d’un paramètre de déploiement. Vous pouvez voir des exemples dans la [liste des paramètres de déploiement](azure-stack-run-powershell-script.md).

### <a name="internet-access"></a>Accès à Internet

Pile Azure requiert l’accès à Internet, soit directement, soit via un proxy transparent. Pile Azure ne supporte pas la configuration d’un proxy web pour activer l’accès à Internet. L’adresse IP d’hôte et de la nouvelle période d’enquête affectée à la BGPNAT01-MAS (par DHCP ou l’adresse IP statique) doivent être en mesure d’accéder à Internet. Les ports 80 et 443 sont utilisés dans les domaines graph.windows.net et login.windows.net.

### <a name="telemetry"></a>Télémétrie

Pour prendre en charge les flux de données de télémétrie, le port 443 (HTTPS) doit être ouvert dans votre réseau. Le point de terminaison client est https://vortex-win.data.microsoft.com.


## <a name="next-steps"></a>Étapes suivantes

[Télécharger le package de déploiement d’Azure pile POC](https://azure.microsoft.com/overview/azure-stack/try/?v=try)

[Déployer la pile Azure POC](azure-stack-run-powershell-script.md)
