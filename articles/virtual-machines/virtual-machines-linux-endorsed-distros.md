<properties
    pageTitle="Revêtus de distributions de Linux | Microsoft Azure"
    description="Obtenir des informations sur Linux a visé l’Azure distributions, y compris les instructions pour Ubuntu, OpenLogic, Oracle et SUSE."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="szarkos"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management,azure-resource-manager"
    />

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/17/2016"
    ms.author="szark"/>



#<a name="linux-on-azure-endorsed-distributions"></a>Linux sur les Distributions d’Azure par l’éditeur

> [AZURE.NOTE] Si vous disposez d’un certain temps, veuillez nous aider à améliorer la documentation Azure VM de Linux en prenant cette [enquête rapide](https://aka.ms/linuxdocsurvey) de vos expériences. Chaque réponse nous permet de vous aident à rendre votre travail.

Les images de Linux dans la galerie d’Azure ou du marché sont fournies par un certain nombre de partenaires, et nous travaillons en collaboration avec les diverses communautés de Linux pour ajouter davantage de types de la liste de Distribution de visé. En attendant, pour les distributions n’est pas disponibles à partir de la galerie vous pouvez toujours mettre-Your-propriétaire-Linux en suivant les instructions de [cette page](virtual-machines-linux-classic-create-upload-vhd.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


## <a name="supported-distributions--versions"></a>Les Versions et les versions prises en charge ##

Le tableau suivant répertorie les versions qui sont pris en charge sur Azure et les distributions de Linux. Reportez-vous également à la [prise en charge des images de Linux dans Microsoft Azure](https://support.microsoft.com/en-us/kb/2941892) pour des informations plus détaillées.

Les pilotes Linux Integration Services (LIS) Hyper-V et Azure sont les modules de noyau Microsoft contribue directement au noyau Linux en amont.  Les pilotes LIS soit intégrées du noyau de la distribution par défaut, ou pour les anciennes RHEL CentOS basées sur/distributions sont disponibles en tant que téléchargement séparé [ici](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409).  Consultez [cet article](virtual-machines-linux-create-upload-generic.md#linux-kernel-requirements) pour plus d’informations sur les pilotes LIS.

L’Agent de Linux Azure est déjà préinstallé sur les images de la galerie d’Azure et sont en général disponible à partir du référentiel de la distribution package.  Vous trouverez le code source sur [GitHub](https://github.com/azure/walinuxagent).

Distribution|Version|Pilotes|Agent
---|---|---|---
CentOS par OpenLogic | CentOS 6.3 + 7.0 + | CentOS 6.3 : [téléchargement de LIS](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409)<p>CentOS 6.4 + : Dans le noyau | : Du package [mis en pension de OpenLogic](http://olcentgbl.trafficmanager.net/openlogic/6/openlogic/x86_64/RPMS/) sous « WALinuxAgent » <br/>Code de source : [GitHub](https://github.com/Azure/WALinuxAgent)
[CoreOS](https://coreos.com/docs/running-coreos/cloud-providers/azure/) | 494.4.0+ | Dans le noyau | Code de source : [GitHub](https://github.com/coreos/coreos-overlay/tree/master/app-emulation/wa-linux-agent)
Debian | 7.9 Debian +, 8.2 + | Dans le noyau | : Du package mis en pension sous « waagent » <br/>Code de source : [GitHub](https://github.com/Azure/WALinuxAgent)
Oracle Linux | 6.4 +, 7.0 + | Dans le noyau | : Du package mis en pension sous « WALinuxAgent » <br/>Code de source : [GitHub](http://go.microsoft.com/fwlink/p/?LinkID=250998)
Red Hat Enterprise Linux | RHEL 6.7 +, 7.1 + | Dans le noyau|: Du package mis en pension sous « WALinuxAgent » <br/>Code de source : [GitHub](https://github.com/Azure/WALinuxAgent)
SUSE Linux Enterprise | SLES 11 SP4, SLES 12 SP1 + et <p> SLES pour SAP 11 SP3 + | Dans le noyau | : Du package Mis en pension [Cloud : outils](https://build.opensuse.org/project/show/Cloud:Tools) sous « python-azure-agent » <br/>Code de source : [GitHub](http://go.microsoft.com/fwlink/p/?LinkID=250998)
openSUSE | openSUSE 13.2 + | Dans le noyau | : Du package Mis en pension [Cloud : outils](https://build.opensuse.org/project/show/Cloud:Tools) sous « python-azure-agent » <br/>Code de source : [GitHub](https://github.com/Azure/WALinuxAgent)
Ubuntu|Ubuntu 12.04, 14.04, 16.04, 16.10 | Dans le noyau | : Du package mis en pension sous « walinuxagent » <br/>Code de source : [GitHub](https://github.com/Azure/WALinuxAgent)


## <a name="partners"></a>Partenaires

### <a name="openlogic"></a>OpenLogic
[http://www.openlogic.com/Azure](http://www.openlogic.com/azure)

OpenLogic est des leaders des solutions en open source enterprise pour le cloud et le centre de données. OpenLogic permet à des centaines de leaders d’entreprise sur un large éventail de secteurs d’activité à acquérir en toute sécurité, prennent en charge et contrôler les logiciels open source. OpenLogic offre de support technique de qualité commerciale et l’indemnisation pour les 600 packages open source soutenu par la Communauté Expert OpenLogic, y compris la prise en charge de niveau entreprise pour CentOS ainsi que le partenaire de lancement pour fournir des images basées sur CentOS sur Azure.

### <a name="coreos"></a>CoreOS
[https://CoreOS.com/docs/Running-CoreOS/cloud-Providers/Azure/](https://coreos.com/docs/running-coreos/cloud-providers/azure/)

À partir du site Web de CoreOS :

*CoreOS est conçu pour la sécurité, la cohérence et de fiabilité. Au lieu d’installer des packages via yum ou apt, CoreOS utilise des conteneurs de Linux pour gérer vos services à un niveau supérieur d’abstraction. Code d’un service unique et toutes les dépendances sont empaquetées dans un conteneur qui peut être exécuté sur un ou plusieurs ordinateurs de CoreOS.*


### <a name="credativ"></a>Credativ
[http://www.credativ.co.uk/credativ-blog/Debian-images-Microsoft-Azure](http://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure)

Credativ est un consultant indépendant et des services spécialisés dans le développement et la mise en oeuvre des solutions professionnelles à l’aide d’un logiciel gratuit. En tant qu’Open Source les meilleurs spécialistes mondiaux, Credative a la reconnaissance internationale avec nombreux services informatiques à l’aide de leur prise en charge. En collaboration avec Microsoft, Credativ prépare actuellement images Debian correspondantes pour 8 Debian (Jessica) et Debian avant 7 (Wheezy), qui sont spécialement conçus pour s’exécuter sur Azure et peuvent être facilement gérés par la plate-forme. Credativ prendra également en charge la maintenance à long terme et la mise à jour des images Debian pour Azure par l’intermédiaire de ses centres de Support Open Source.

### <a name="oracle"></a>Oracle
[http://www.Oracle.com/technetwork/topics/cloud/FAQ-1963009.HTML](http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)

Stratégie d’Oracle est de proposer une large gamme de solutions pour les nuages privés et publics, tout en offrant aux clients de choix et flexibilité dans le déploiement de logiciels Oracle dans les nuages d’Oracle, ainsi que des autres nuages.  Partenariat d’Oracle avec Microsoft permet aux clients de déployer le logiciel Oracle dans les nuages privés et publics de Microsoft en toute confiance de certification et prise en charge d’Oracle.  Engagement et investissement dans les solutions de cloud public et privé de Oracle Oracle n’est pas modifiée.

### <a name="red-hat"></a>Red Hat
[http://www.redhat.com/en/partners/Strategic-Alliance/Microsoft](http://www.redhat.com/en/partners/strategic-alliance/microsoft)

Le leader mondial des solutions en open source, Red Hat vous aide à plus de 90 % des sociétés du classement Fortune 500 résoudre les défis posés aux entreprises, en aligner leurs et stratégies d’entreprise et les préparer pour l’avenir de la technologie. Red Hat effectue cette opération en fournissant des solutions sécurisées à travers un modèle commercial ouvert et un modèle d’abonnement abordable et prévisibles.

### <a name="suse"></a>SUSE
[http://www.SuSE.com/SuSE-Linux-Enterprise-Server-on-Azure](http://www.suse.com/suse-linux-enterprise-server-on-azure)

SUSE Linux Enterprise Server sur Azure est une plateforme éprouvée qui offre une plus grande fiabilité et sécurité pour le cloud computing. Plate-forme de Linux de SUSE polyvalent s’intègre parfaitement avec les services cloud Azure pour fournir un environnement de cloud facilement gérable. Et avec des applications certifiées 9,200 de plus de plus de 1 800 éditeurs de logiciels indépendants pour SUSE Linux Enterprise Server, SUSE permet de s’assurer que les charges de travail pris en charge dans le centre de données peuvent être déployés en toute confiance sur Azure.

### <a name="canonical"></a>Canonique
[http://www.Ubuntu.com/cloud/Azure](http://www.ubuntu.com/cloud/azure)

Ingénierie canonique et gouvernance de communauté ouverte de lecteur succès du Ubuntu de client, serveur et informatique en nuage, y compris les services de cloud personnelles des consommateurs. Vision du canoniques d’une plate-forme unifiée libre sous Ubuntu, à partir de téléphone vers le cloud, avec une gamme d’interfaces cohérents pour le téléphone, Tablet PC, TV et du bureau, rend Ubuntu le premier choix pour les établissements différents à partir des fournisseurs de nuage public et les décideurs de l’électronique grand public, un préféré parmi des spécialistes des technologies.

Les développeurs et les ingénieurs centres dans le monde entier, Canonical est idéalement placé pour un partenariat avec les fabricants de matériel, les fournisseurs de contenu et développeurs de logiciels pour fournir des solutions d’Ubuntu sur le marché, à partir de PC, serveurs et périphériques de poche.

