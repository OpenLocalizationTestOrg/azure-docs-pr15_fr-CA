<properties
    pageTitle="Procédure pas à pas d’exemple Infrastructure | Microsoft Azure"
    description="Obtenir des informations sur les instructions clés de conception et d’implémentation pour le déploiement d’une infrastructure d’exemple dans Azure."
    documentationCenter=""
    services="virtual-machines-windows"
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/08/2016"
    ms.author="iainfou"/>

# <a name="example-azure-infrastructure-walkthrough"></a>Procédure pas à pas d’exemple infrastructure Azure

[AZURE.INCLUDE [virtual-machines-windows-infrastructure-guidelines-intro](../../includes/virtual-machines-windows-infrastructure-guidelines-intro.md)] 

Cet article explique de créer une infrastructure d’application exemple. Nous détaillent la conception d’une infrastructure pour un magasin en ligne simple qui rassemble toutes les directives et les décisions portant sur les conventions d’affectation de noms, disponibilité des jeux, des réseaux virtuels et équilibreurs de charge et réellement déployer vos machines virtuelles (VM).


## <a name="example-workload"></a>Charge de travail exemple

Adventure Works Cycles souhaite créer une application de banque en ligne dans Azure qui se compose de :

- Deux serveurs IIS exécutant le client front-end dans une couche web
- Deux serveurs IIS traitement des données et des commandes dans une couche application
- Deux instances de Microsoft SQL Server avec les groupes de disponibilité AlwaysOn (deux serveurs SQL et un témoin de nœud majoritaire) pour le stockage des données sur les produits et les commandes dans un niveau de base de données
- Deux contrôleurs de domaine Active Directory pour les comptes clients et des fournisseurs dans un niveau d’authentification
- Tous les serveurs se trouvent dans deux sous-réseaux :
    - un sous-réseau frontal pour les serveurs web 
    - un sous-réseau de back-end pour les serveurs d’applications, cluster SQL et les contrôleurs de domaine

![Diagramme de différents niveaux de l’infrastructure d’application](./media/virtual-machines-common-infrastructure-service-guidelines/example-tiers.png)

Entrant sécurisé du trafic web doit être équilibré en charge entre les serveurs web comme clients parcourir le magasin en ligne. Ordre de traitement du trafic dans le formulaire de HTTP demande à partir du web, les serveurs doivent être équilibrées entre les serveurs d’application. En outre, l’infrastructure doit être conçue pour une haute disponibilité.

La conception résultante doit comporter :

- Un abonnement Azure et un compte
- Un groupe de ressources unique
- Comptes de stockage
- Un réseau virtuel avec deux sous-réseaux
- Disponibilité définit pour les ordinateurs virtuels avec un rôle similaire
- Ordinateurs virtuels

Tous les ci-dessus suivent ces conventions d’affectation de noms :

- Adventure Works Cycles utilise **[charge de travail informatique]-[emplacement]-[ressource Azure]** en tant que préfixe
    - Pour cet exemple, «**azos**» (banque en ligne d’Azure) est le nom de la charge de travail informatique et «**utiliser**» (Extrême-Orient US 2) est l’emplacement
- Comptes de stockage utilisent adventureazosusesa**[description]**
    - « aventure » a été ajouté au préfixe pour fournir l’unicité et les noms de compte de stockage ne prennent pas en charge l’utilisation des traits d’union.
- Les réseaux virtuels utilisent des AZOS-utilisation-VN**[numéro]**
- Jeux de disponibilité utilisent azos-utilisez-sous-**[rôle]**
- Les noms de machine virtuelle utilisent azos-utilisez-vm -**[vmname]**


## <a name="azure-subscriptions-and-accounts"></a>Comptes et abonnements azure

Adventure Works Cycles est à l’aide de leur abonnement entreprise, nommée Adventure Works entreprise abonnement, afin de fournir de facturation pour cette charge de travail informatique.


## <a name="storage-accounts"></a>Comptes de stockage

Adventure Works Cycles déterminé qu’ils nécessaire deux comptes de stockage :

- **adventureazosusesawebapp** pour le stockage standard des serveurs web, serveurs d’applications et contrôleurs de domaine et leurs disques de données.
- **adventureazosusesasql** pour le stockage de la prime d’ordinateurs virtuels SQL Server et leurs disques de données.


## <a name="virtual-network-and-subnets"></a>Sous-réseaux et des réseaux virtuels

Parce que le réseau virtuel ne doit pas une connectivité permanente au réseau local Cycles de travail Adventure, elle a décidé sur un réseau virtuel nuage uniquement.

Ils créé un réseau virtuel nuage uniquement avec les paramètres suivants à l’aide du portail Azure :

- Nom : AZOS-utilisation-VN01
- Emplacement : Américain d’Extrême-Orient 2
- Espace d’adressage de réseau virtuel : 10.0.0.0/8
- Premier sous-réseau :
    - Nom : site Web frontal
    - Espace d’adressage : 10.0.1.0/24
- Deuxième sous-réseau :
    - Nom : back-end
    - Espace d’adressage : 10.0.2.0/24


## <a name="availability-sets"></a>Jeux de disponibilité

Pour garantir une haute disponibilité de tous les quatre niveaux de leur magasin en ligne, Adventure Works Cycles a décidé de quatre jeux de disponibilité :

- **Utilisez azos comme web** pour les serveurs web
- **azos utilisé en tant qu’application** pour les serveurs d’applications
- **Utilisez azos comme sql** pour les serveurs SQL
- **azos-utilisation-sous-dc** pour les contrôleurs de domaine


## <a name="virtual-machines"></a>Ordinateurs virtuels

Adventure Works Cycles a décidé, les noms suivants pour leurs machines virtuelles d’Azure :

- **azos-utilisation-vm-web01** pour le premier serveur web
- **azos-utilisation-vm-web02** pour le second serveur web
- **azos-utilisation-vm-app01** pour le premier serveur d’application
- **azos-utilisation-vm-app02** pour le deuxième serveur d’applications
- **azos-utilisation-vm-sql01** pour le premier serveur SQL Server dans le cluster
- **azos-utilisation-vm-sql02** pour le second serveur SQL Server dans le cluster
- **azos-utilisation-vm-dc01** du premier contrôleur de domaine
- **azos-utilisation-vm-dc02** sur le deuxième contrôleur de domaine

Voici la configuration obtenue.

![Infrastructure d’application finale déployée dans Azure](./media/virtual-machines-common-infrastructure-service-guidelines/example-config.png)

Cette configuration comprend :

- Un réseau virtuel nuage uniquement avec deux sous-réseaux (frontaux et back-end)
- Deux comptes de stockage
- Quatre jeux de disponibilité, un pour chaque niveau de la banque en ligne
- Les ordinateurs virtuels pour les quatre niveaux
- Un jeu d’équilibrage de la charge externes pour le trafic web basée sur le protocole HTTPS à partir d’Internet vers les serveurs web
- Un jeu pour le trafic web crypté à partir des serveurs web pour les serveurs d’applications d’équilibrage interne
- Un groupe de ressources unique


## <a name="next-steps"></a>Étapes suivantes

[AZURE.INCLUDE [virtual-machines-windows-infrastructure-guidelines-next-steps](../../includes/virtual-machines-windows-infrastructure-guidelines-next-steps.md)] 