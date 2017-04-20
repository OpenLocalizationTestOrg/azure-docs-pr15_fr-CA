<properties
   pageTitle="Infrastructure de mise à jour de Red Hat (RHUI) | Microsoft Azure"
   description="En savoir plus sur Red Hat mise à jour Infrastructure (RHUI) pour les instances de Red Hat Enterprise Linux à la demande de Microsoft Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="BorisB2015"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="10/14/2016"
   ms.author="borisb"/>

# <a name="red-hat-update-infrastructure-rhui-for-on-demand-red-hat-enterprise-linux-vms-in-azure"></a>Infrastructure de mise à jour de Red Hat (RHUI) pour la demande de Red Hat Enterprise Linux VM dans Azure

Les ordinateurs virtuels créés à partir des images Red Hat Enterprise Linux (RHEL) à la demande disponibles dans Azure Marketplace sont inscrits pour accéder à la Red Hat mise à jour Infrastructure (RHUI) déployés dans Azure.  Les instances RHEL à la demande ont accès à un référentiel d’yum régionaux et en mesure de recevoir des mises à jour incrémentielles.

La liste des référentiels yum, qui est gérée par RHUI, est configurée dans votre instance RHEL au cours de la mise en service. Vous n’avez pas besoin d’effectuer une configuration supplémentaire - exécuter `yum update` une fois votre instance RHEL est prêt obtenir les dernières mises à jour.

> [AZURE.NOTE] Infrastructure de RHUI Azure a été récemment mis à jour (septembre 2016) et nécessite des modifications dans la configuration de vos instances RHEL existants pour un accès ininterrompu à la RHUI d’Azure. Reportez-vous à la section de mise à jour de RHUI Azure Infrastructure pour plus de détails.


## <a name="rhui-azure-infrastructure-update"></a>Mise à jour RHUI Infrastructure Azure
À partir de septembre 2016, Azure a un nouvel ensemble de serveurs de l’Infrastructure de mise à jour de Red Hat (RHUI). Ces serveurs sont déployés avec [Azure Traffic Manager]( https://azure.microsoft.com/services/traffic-manager/) afin qu’un seul point de terminaison (rhui-1.micrsoft.com) peut être utilisé par n’importe quel ordinateur virtuel quelle que soit la région. Ils utilisent également un certificat SSL est chaîné à une autorité de certification (racine de Baltimore) bien connu. Rendre cette mise à jour automatique est dangereux pour des clients qui ont des listes ACL ou des tables de routage personnalisées pour les serveurs de mise à jour RHUI, afin que cette mise à jour est « opt-in ». Étapes manuelles pour intégration vers ces nouveaux serveurs sont disponibles sur cette page et un script complet pour l’intégration de manière automatique (lors de la vérification des étapes individuelles). Les nouvelles images RHEL PAYG sur le marché d’Azure (versions datées de septembre 2016 ou version ultérieures) pointent automatiquement sur les nouveaux serveurs Azure RHUI et ne nécessitent pas d’opération supplémentaire.

### <a name="the-new-azure-rhui-infrastructure-onboarding-timeline"></a>La nouvelle chronologie d’arrivant d’infrastructure Azure RHUI

| Date | Remarque |
| --- | --- |
|22 septembre 2016 | Les serveurs RHUI et les instructions d’installation disponibles pour utilisation. Machines virtuelles déployées à l’aide de la nouvelle (datée de 2016 septembre) les images marketplace RHEL PAYG utilisent automatiquement les nouveaux serveurs RHUI, mais les machines virtuelles existantes sont « opt-in »
|1 novembre 2016 | Des images de machine virtuelle de retenue à la source de RHEL héritées (qui utilisent les anciens serveurs Azure RHUI) seront supprimés de la galerie d’Azure Marketplace
|16 janvier 2017 | Les anciens serveurs Azure RHUI va être désactivés. Mettre à jour tous vos ordinateurs virtuels de RHEL de retenue à la source affecté par cette fois-ci pour maintenir l’accès aux RHUI d’Azure

### <a name="the-ips-for-the-new-rhui-content-delivery-servers-are"></a>Sont des adresses IP pour les nouveaux serveurs de diffusion de contenu RHUI

```
# Azure Global
13.91.47.76
40.85.190.91
52.187.75.218
52.174.163.213

# Azure US Government
13.72.186.193
```

### <a name="manual-update-procedure-to-use-the-new-azure-rhui-servers"></a>Procédure de mise à jour manuelle pour utiliser les nouveaux serveurs Azure RHUI

Télécharger (via curl) de la signature de clé publique

```
curl -o RPM-GPG-KEY-microsoft-azure-release https://download.microsoft.com/download/9/D/9/9d945f05-541d-494f-9977-289b3ce8e774/microsoft-sign-public.asc 
```

Vérifiez la clé de téléchargement

```
gpg --list-packets --verbose < RPM-GPG-KEY-microsoft-azure-release
```

Vérifiez la sortie, vérifiez les `keyid` et `user ID packet`:

```
Version: GnuPG v1.4.7 (GNU/Linux)
:public key packet:
        version 4, algo 1, created 1446074508, expires 0
        pkey[0]: [2048 bits]
        pkey[1]: [17 bits]
        keyid: EB3E94ADBE1229CF
:user ID packet: "Microsoft (Release signing) <gpgsecurity@microsoft.com>"
:signature packet: algo 1, keyid EB3E94ADBE1229CF
        version 4, created 1446074508, md5len 0, sigclass 0x13
        digest algo 2, begin of digest 1a 9b
        hashed subpkt 2 len 4 (sig created 2015-10-28)
        hashed subpkt 27 len 1 (key flags: 03)
        hashed subpkt 11 len 5 (pref-sym-algos: 9 8 7 3 2)
        hashed subpkt 21 len 3 (pref-hash-algos: 2 8 3)
        hashed subpkt 22 len 2 (pref-zip-algos: 2 1)
        hashed subpkt 30 len 1 (features: 01)
        hashed subpkt 23 len 1 (key server preferences: 80)
        subpkt 16 len 8 (issuer key ID EB3E94ADBE1229CF)
        data: [2047 bits]
```

Installer la clé publique

```
sudo install -o root -g root -m 644 RPM-GPG-KEY-microsoft-azure-release /etc/pki/rpm-gpg
sudo rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-microsoft-azure-release
```

Télécharger, vérifier et installer le Client tr/min

Téléchargement : Pour RHEL 6

```
curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel6/rhui-azure-rhel6-2.0-2.noarch.rpm 
```

Pour RHEL 7

```
curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel7/rhui-azure-rhel7-2.0-2.noarch.rpm  
```

Vérifiez que :

```
rpm -Kv azureclient.rpm
```

Vérifiez dans la sortie qu’une signature du package a est OK

```
azureclient.rpm:
    Header V3 RSA/SHA256 Signature, key ID be1229cf: OK
    Header SHA1 digest: OK (927a3b548146c95a3f6c1a5d5ae52258a8859ab3)
    V3 RSA/SHA256 Signature, key ID be1229cf: OK
    MD5 digest: OK (c04ff605f82f4be8c96020bf5c23b86c)
```

Installer le fichier RPM

```
sudo rpm -U azureclient.rpm
```

Une fois terminée, vérifiez que vous pouvez accéder à l’écran Azure RHUI la machine virtuelle

### <a name="all-in-one-script-for-automating-the-above-task"></a>Script de tout-en-un pour l’automatisation de la tâche précédente
Si nécessaire, utilisez le script suivant pour automatiser la tâche de mise à jour des ordinateurs virtuels affectés sur les nouveaux serveurs Azure RHUI.

```
# Download key
curl -o RPM-GPG-KEY-microsoft-azure-release https://download.microsoft.com/download/9/D/9/9d945f05-541d-494f-9977-289b3ce8e774/microsoft-sign-public.asc 

# Validate key
if ! gpg --list-packets --verbose < RPM-GPG-KEY-microsoft-azure-release | grep -q "keyid: EB3E94ADBE1229CF"; then
    echo "Keyfile azure.asc NOT valid. Exiting."
    exit 1
fi

# Install Key
sudo install -o root -g root -m 644 RPM-GPG-KEY-microsoft-azure-release /etc/pki/rpm-gpg
sudo rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-microsoft-azure-release

# Download RPM package
if grep -q "release 7" /etc/redhat-release; then
    ver=7
elif  grep -q "release 6" /etc/redhat-release; then
    ver=6
else
    echo "Version not supported, exiting"
    exit 1
fi

url=https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel$ver/rhui-azure-rhel$ver-2.0-2.noarch.rpm
curl -o azureclient.rpm "$url"

# Verify package
if ! rpm -Kv azureclient.rpm | grep -q "key ID be1229cf: OK"; then
    echo "RPM failed validation ($url)"
    exit 1
fi

# Install package
sudo rpm -U azureclient.rpm
```

## <a name="rhui-overview"></a>Vue d’ensemble RHUI
[Infrastructure de mise à jour de Red Hat](https://access.redhat.com/products/red-hat-update-infrastructure) offre une solution hautement évolutive pour gérer le contenu de référentiel yum pour les instances de nuage de Red Hat Enterprise Linux qui sont hébergés par les fournisseurs de nuage de Red Hat certifié. Selon le projet en amont de la pâte à papier, RHUI permet à nuage de localement mise en miroir du contenu des référentiels sur le Red Hat hébergé, créer des référentiels personnalisés avec leurs propres informations et rendre ces référentiels disponibles pour un grand groupe d’utilisateurs finaux via un système de remise contenu avec équilibrage de charge.

## <a name="regions-where-rhui-is-available"></a>Régions où RHUI est disponible
RHUI est disponible dans toutes les régions où les images de la demande RHEL sont disponibles. Elle comprend actuellement publics de toutes les régions répertoriées sur la page de [tableau de bord état Azure](https://azure.microsoft.com/status/) et les régions d’Azure américains. Accès RHUI pour les machines virtuelles mis en service à partir d’images à la demande RHEL est incluse dans leur prix. Met à jour la disponibilité de nuage de régionales et nationales supplémentaires en disponibilité à la demande RHEL à l’avenir.

> [AZURE.NOTE] Accès aux RHUI d’Azure hébergé est limité aux ordinateurs virtuels dans [des plages IP de Datacenter Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653).

## <a name="get-updates-from-another-update-repository"></a>Obtenir des mises à jour à partir d’un autre référentiel de mise à jour

Si vous avez besoin obtenir les mises à jour à partir d’un référentiel de mise à jour différente (au lieu de RHUI Azure hébergé) vous devez annuler l’inscription de vos instances de RHUI et de les inscrire à nouveau avec l’infrastructure de mise à jour de votre choix (par exemple, Red Hat Satellite ou Red Hat client Portal CDN). Vous aurez besoin des abonnements de Red Hat appropriés pour ces services et l’inscription pour [Red Hat nuage accès dans Azure](https://access.redhat.com/ecosystem/partners/ccsp/microsoft-azure).

Pour annuler l’inscription de RHUI et le réenregistrer à votre suivi d’infrastructure de mise à jour les étapes ci-dessous.

1.  Modifiez /etc/yum.repos.d/rh-cloud.repo et tous les `enabled=1` à `enabled=0`. Par exemple :

        # sed -i 's/enabled=1/enabled=0/g' /etc/yum.repos.d/rh-cloud.repo

2.  Modifier /etc/yum/pluginconf.d/rhnplugin.conf et `enabled=0` à `enabled=1`.
3.  Puis l’enregistrer avec l’infrastructure de votre choix, notamment le portail client Red Hat. Suivez le guide de la solution Red Hat sur la [façon d’enregistrer et de s’abonner à un système au portail client Red Hat](https://access.redhat.com/solutions/253273).

> [AZURE.NOTE] Accès à la RHUI Azure hébergé est inclus dans le prix d’image de RHEL paiement (retenues à la source). Annuler l’inscription d’une machine virtuelle RHEL de retenue à la source à partir de la RHUI Azure hébergé ne convertit pas la machine virtuelle en type de mettre votre-propriétaire-licence (BYOL) VM, et donc vous pouvez être encourir des frais doubles si vous vous inscrivez à la machine virtuelle de même avec une autre source de mises à jour. 
> 
> Si vous souhaitez toujours utiliser une infrastructure de mise à jour autres que Azure hébergé de RHUI envisager la création et le déploiement de vos propres images (de type BYOL), comme décrit dans l’article de [Création et la machine virtuelle télécharger Red Hat pour Azure](virtual-machines-linux-redhat-create-upload-vhd.md) .

## <a name="next-steps"></a>Étapes suivantes
Pour créer un ordinateur virtuel de Red Hat Enterprise Linux à partir d’exploiter et d’image de paiement à l’utilisation de Azure Marketplace Azure hébergé le RHUI atteindre la [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/redhat/). Vous ne pourrez pas utiliser `yum update` de votre instance RHEL sans aucun paramétrage supplémentaire.