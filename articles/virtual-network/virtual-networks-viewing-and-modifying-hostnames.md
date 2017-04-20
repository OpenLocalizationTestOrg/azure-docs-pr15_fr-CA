<properties 
   pageTitle="Affichage et modification des noms d’hôtes | Microsoft Azure"
   description="Comment faire pour afficher et modifier les noms d’hôte pour les machines virtuelles Azure, web et rôles de travailleur pour la résolution de nom"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/27/2016"
   ms.author="jdial" />

# <a name="viewing-and-modifying-hostnames"></a>Affichage et modification des noms d’hôte

Pour permettre à vos instances de rôles être référencé par nom d’hôte, vous devez définir la valeur pour le nom d’hôte dans le fichier de configuration de service pour chaque rôle. Que faire, vous devez ajouter le nom d’hôte de votre choix à l’attribut **vmName** illustre l’élément **Role** . La valeur de l’attribut **vmName** est utilisée comme base pour le nom d’hôte de chaque instance du rôle. Par exemple, si **vmName** est *webrole* et il y a trois instances de ce rôle, les noms d’hôte des instances seront *webrole0*, *webrole1*et *webrole2*. Il est inutile de spécifier un nom d’hôte pour les machines virtuelles dans le fichier de configuration, car le nom d’hôte pour une machine virtuelle est rempli en fonction du nom de l’ordinateur virtuel. Pour plus d’informations sur la configuration d’un service Microsoft Azure, consultez [Schéma de Configuration de Service Azure (.cscfg fichier)](https://msdn.microsoft.com/library/azure/ee758710.aspx)

## <a name="viewing-hostnames"></a>Affichage des noms d’hôte

Vous pouvez afficher les noms d’hôtes des ordinateurs virtuels et les instances de rôles dans un service cloud à l’aide d’un des outils ci-dessous.

### <a name="azure-portal"></a>Azure Portal

Vous pouvez utiliser le [portail Azure](http://portal.azure.com) pour afficher les noms d’hôte pour les ordinateurs virtuels sur la lame de vue d’ensemble pour une machine virtuelle. Gardez à l’esprit que la lame indique une valeur pour le **nom** et le **Nom de l’hôte**. Bien qu’ils soient initialement la même, la modification du nom d’hôte ne changera pas le nom de l’ordinateur virtuel ou d’une instance de rôle.

Instances de rôle peuvent également être consultées dans le portail Azure, mais lorsque vous mettez les instances dans un service cloud, le nom d’hôte n’est pas affiché. Vous verrez un nom pour chaque instance, mais que ce nom ne représente pas le nom d’hôte.

### <a name="service-configuration-file"></a>Fichier de configuration de service

Vous pouvez télécharger le fichier de configuration de service pour un service déployé depuis la blade de **configuration** du service dans le portail Azure. Vous pouvez ensuite rechercher l’attribut **vmName** pour l’élément de **nom de rôle** voir le nom de l’hôte. Gardez à l’esprit que ce nom d’hôte est utilisé comme base pour le nom d’hôte de chaque instance du rôle. Par exemple, si **vmName** est *webrole* et il y a trois instances de ce rôle, les noms d’hôte des instances seront *webrole0*, *webrole1*et *webrole2*.

### <a name="remote-desktop"></a>Bureau à distance

Une fois que le Bureau à distance (Windows), la communication à distance de Windows PowerShell (Windows) ou les connexions SSH (Linux et Windows) pour vos ordinateurs virtuels ou les instances de rôles, vous pouvez afficher le nom d’hôte à partir d’une connexion Bureau à distance de différentes manières :

- Tapez le nom d’hôte à l’invite de commande ou d’un terminal SSH.

- Tapez ipconfig/tout à la commande invite (Windows uniquement).

- Permet d’afficher le nom de l’ordinateur dans les paramètres système (Windows uniquement).

### <a name="azure-service-management-rest-api"></a>Gestion des services Azure API REST

À partir d’un client reste, suivez ces instructions :

1. Assurez-vous que vous disposez d’un certificat client pour se connecter au portail Azure. Pour obtenir un certificat client, suivez les étapes présentées dans [Comment : télécharger et importer les paramètres de publication et d’abonnement](https://msdn.microsoft.com/library/dn385850.aspx). 

1. Définir une entrée d’en-tête x-ms-version avec une valeur de 2013-11-01.

1. Envoyer une demande dans le format suivant : https://management.core.windows.net/\<subscrition-id\>/services/hostedservices/\<nom-service\>?embed-détail = true

1. Recherchez l’élément de **nom d’hôte** de chaque élément de **RoleInstance** .

>[AZURE.WARNING] Vous pouvez également afficher le suffixe de domaine interne pour votre service de nuage à partir de la réponse d’appel reste en vérifiant l’élément **InternalDnsSuffix** , ou en exécutant ipconfig/à partir d’une invite de commande dans une session Bureau à distance (Windows) ou en cours d’exécution /etc/resolv.conf cat à partir d’un terminal SSH (Linux).

## <a name="modifying-a-hostname"></a>Modification d’un nom d’hôte

Vous pouvez modifier le nom d’hôte pour tout ordinateur virtuel ou d’une instance de rôle en téléchargeant un fichier de configuration du service modifiée ou en renommant l’ordinateur à partir d’une session Bureau à distance.

## <a name="next-steps"></a>Étapes suivantes

[Résolution de nom (DNS)](virtual-networks-name-resolution-for-vms-and-role-instances.md)

[Schéma de Configuration de Service Azure (.cscfg)](https://msdn.microsoft.com/library/windowsazure/ee758710.aspx)

[Schéma de Configuration Azure réseau virtuel](http://go.microsoft.com/fwlink/?LinkId=248093)

[Spécifiez les paramètres DNS à l’aide de fichiers de configuration réseau](virtual-networks-specifying-a-dns-settings-in-a-virtual-network-configuration-file.md)
