<properties
   pageTitle="Référence de l’Architecture de Windows Azure - IaaS : Implémentation d’un réseau de périmètre entre Internet et les Azure | Microsoft Azure"
   description="Comment implémenter une architecture de réseau sécurisée hybride avec accès à Internet dans Azure."
   services="guidance,vpn-gateway,expressroute,load-balancer,virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="christb"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="telmos"/>

# <a name="implementing-a-dmz-between-azure-and-the-internet"></a>Mise en œuvre d’un réseau de périmètre entre Internet et les Azure

[AZURE.INCLUDE [pnp-RA-branding](../../includes/guidance-pnp-header-include.md)]

Cet article décrit les méthodes conseillées pour l’implémentation d’un réseau sécurisé hybride qui étend votre réseau local qui accepte le trafic du réseau internet vers Azure. Cette architecture de référence étend l’architecture décrite dans l’article de la [mise en œuvre d’un réseau de périmètre entre Azure et votre centre de données sur site][implementing-a-secure-hybrid-network-architecture]. Il est recommandé de lire ce document et comprendre cette référence architecture avant la lecture de ce document.

> [AZURE.NOTE] Azure dispose de deux modèles de déploiement différents : [Le Gestionnaire de ressources] [ resource-manager-overview] et classique. Cette architecture de référence utilise un gestionnaire de ressources, Microsoft recommande pour les nouveaux déploiements. 

Scénarios d’utilisation classiques de cette architecture sont les suivantes :

- Les applications hybride dans lequel exécuter des charges de travail partiellement sur site et partiellement dans Azure.

- Infrastructure Azure qui achemine le trafic entrant à partir de locaux et internet.

## <a name="architecture-diagram"></a>Diagramme d’architecture

Le schéma suivant met en évidence les éléments importants de cette architecture :

> Un document Visio qui contient ce diagramme d’architecture est disponible au téléchargement sur le [Centre de téléchargement Microsoft][visio-download]. Ce diagramme est sur la page de zone DMZ – « Public ».

[! [0]][0]

- **Adresse IP publique (PIP).** Il s’agit de l’adresse IP du point d’entrée public. Les utilisateurs externes connectés à Internet peuvent accéder au système par le biais de cette adresse.

- **Matériel de réseau virtuel (à couloirs).**  À couloirs est un terme générique qui décrit une machine virtuelle exécutant des tâches comme autoriser ou refuser l’accès sous la forme d’un pare-feu, optimisation des opérations WAN (y compris la compression de réseau), routage personnalisé ou autres fonctionnalités réseau.

- **Équilibrage de la charge d’Azure.** Toutes les demandes entrantes provenant d’internet passer par ce programme d’équilibrage de charge et sont distribués aux NVAs dans la DMZ publique sous-réseau entrant.

- **DMZ publique entrant le sous-réseau.** Ce sous-réseau accepte les demandes de l’équilibreur de charge Azure. Les demandes entrantes sont transmis à un de le NVAs dans la zone DMZ.

- **Sous-réseau de sortant DMZ publique.** Les demandes qui sont approuvés par l’à couloirs traversent ce sous-réseau à l’équilibreur de charge interne de la couche web.

## <a name="recommendations"></a>Recommandations

Azure offre de nombreuses ressources différentes et des types de ressources, de sorte que cette architecture de référence peut être configuré de différentes façons. Nous avons fourni un modèle Azure Resource Manager pour installer l’architecture de référence qui suit ces recommandations. Si vous choisissez de créer votre propre architecture de référence, vous devez suivre ces recommandations à moins d’avoir une recommandation ne correspondant pas à un besoin spécifique.

### <a name="nva-recommendations"></a>Recommandations à couloirs

Mettre en œuvre un ensemble de NVAs pour le trafic provenant d’internet et l’autre pour le trafic local d’origine. Il s’agit d’un risque de sécurité à n'utiliser qu’un seul ensemble de NVAs pour les deux, car cette conception ne fournit aucun périmètre de sécurité entre les deux ensembles de trafic réseau. Il est des avantages à utiliser cette conception car il réduit la complexité de la vérification des règles de sécurité et plus clairement les règles qui correspondent à chaque demande entrante de réseau. Par exemple, un ensemble de NVAs implémente les règles pour le trafic internet uniquement lors d’un autre ensemble de règles de mise en œuvre de NVAs pour le trafic local uniquement.

Inclure une couche 7 à couloirs pour terminer les connexions des applications au niveau à couloirs et conserve les affinités avec les couches principales. Cela garantit la connectivité symétrique dans lequel le trafic de réponse dans les couches principales renvoie via l’à couloirs.  

### <a name="public-load-balancer-recommendations"></a>Recommandations d’équilibreur de charge publique ###

Pour maintenir la disponibilité et l’évolutivité, déployer la DMZ publique entrant NVAs dans un [jeu de disponibilité] [ availability-set] et d’utiliser un [équilibreur de charge face à l’internet] [ load-balancer] pour distribuer les requêtes internet sur les NVAs dans l’ensemble de la disponibilité.  

Configurer l’équilibreur de charge pour accepter les requêtes uniquement sur les ports nécessaires pour le trafic internet. Par exemple, limiter les demandes HTTP entrantes vers le port 80 et des requêtes HTTPS entrantes vers le port 443.

## <a name="scalability-considerations"></a>Évolutivité

Conception de votre infrastructure avec un équilibreur de charge devant le sous-réseau de zone DMZ publique entrant dès le début d’ouvert sur internet. Même si votre initialement architecture nécessite une à couloirs unique, il sera plus facile à mettre à l’échelle à plusieurs NVAs à l’avenir si internet face à équilibrage de la charge est déjà déployée.

## <a name="availability-considerations"></a>Considérations relatives à la disponibilité

Internet face équilibreur de charge nécessite chaque à couloirs dans la DMZ publique sous-réseau entrant pour implémenter une [sonde de santé][lb-probe]. Une sonde de santé qui ne répond pas sur ce point de terminaison est considéré comme indisponible et l’équilibreur de charge orientera les demandes d’autres NVAs dans le même jeu de disponibilité. Notez que si tous les NVAs ne répondent pas, votre application ne pourra pas pourquoi il est important d’avoir configuré pour DevOps alerte lorsque le nombre d’instances d’à couloirs sains tombe en dessous d’un seuil de surveillance.

## <a name="manageability-considerations"></a>Considérations relatives à la facilité de gestion

Limiter les fonctionnalités de surveillance et de gestion pour du entrant public DMZ NVA répondre aux demandes à partir de la zone de passer dans le sous-réseau de gestion uniquement. Comme expliqué dans [mise en œuvre d’un réseau de périmètre entre Azure et votre centre de données local] [ implementing-a-secure-hybrid-network-architecture] document, définissez un itinéraire réseau unique à partir du réseau local par l’intermédiaire de la passerelle vers la zone de lien dans le sous-réseau de gestion pour restreindre l’accès.

Si la connectivité de la passerelle du réseau local vers Azure est en panne, vous pouvez toujours atteindre la zone passer en déployant un PIP, en ajoutant à la zone passer et l’accès distant dans à partir d’internet.

## <a name="security-considerations"></a>Considérations sur la sécurité

Cette architecture de référence implémente plusieurs niveaux de sécurité :

- Internet face équilibreur de charge dirige les requêtes aux NVAs dans le sous-réseau DMZ entrant public uniquement et uniquement sur les ports nécessaires à l’application.

- Les règles NSG pour le sous-réseau de zone DMZ publique entrant et sortant empêchent la compromission en bloquant des requêtes qui se trouvent en dehors des règles NSG les NVAs.

- La configuration de routage NAT pour les NVAs dirige les requêtes entrantes sur le port 80 et le port 443 pour l’équilibrage de charge du niveau web, mais il ignore les demandes sur tous les autres ports.

Notez que vous devez consigner toutes les demandes entrantes sur tous les ports. L’audit régulièrement les journaux, en accordant une attention aux demandes non comprises dans les paramètres attendus comme il peuvent indiquer des tentatives d’intrusion.

### <a name="using-nsgs-to-blockpass-traffic-between-application-tiers"></a>À l’aide de NSGs à bloc/passe le trafic entre les niveaux de l’application

Chacune des couches web, business et données de restreindre le trafic entre eux à l’aide de NSGs. La couche d’entreprise utilise un NSG pour bloquer tout le trafic qui ne provient pas de la couche web et la couche de données utilise un NSG à bloquer tout le trafic qui ne provient pas du niveau de l’entreprise. Si vous avez besoin pour développer les règles NSG pour permettre un accès plus large à ces niveaux, peser ces exigences contre les risques de sécurité. Chaque nouvelle voie d’accès entrant représente une opportunité de dommages de fuites ou à l’application de données accidentelle ou intentionnelle.

## <a name="solution-deployment"></a>Déploiement de la solution

Un déploiement d’une architecture de référence qui implémente ces recommandations est disponible sur Github. Cette architecture de référence comprend un réseau virtuel (VNet), le groupe de sécurité réseau (NSG), équilibrage de la charge et deux machines virtuelles (VM).

L’architecture de référence peut être déployée avec Windows ou Linux VM en suivant les instructions ci-dessous : 

1. Cliquez avec le bouton droit de la souris sur le bouton ci-dessous, puis sélectionnez soit « ouvrir le lien dans un nouvel onglet » ou « Ouvrir le lien dans une nouvelle fenêtre » :  
[![Déployer vers Azure](./media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-hybrid-network-secure-vnet-dmz%2FvirtualNetwork.azuredeploy.json)

2. Une fois le lien ouvert dans Azure portal, vous devez entrer des valeurs pour certains paramètres : 
    - Le nom du **groupe de ressources** est déjà défini dans le fichier de paramètres : cliquez sur **Créer** et saisissez `ra-public-dmz-network-rg` dans la zone de texte.
    - Sélectionnez la zone à partir de la zone de liste déroulante **emplacement** .
    - Ne modifiez pas l' **Uri racine de modèle** ou les zones de texte du **Paramètre racine Uri** .
    - Sélectionnez le **Type de système d’exploitation** dans la liste déroulante zone, **windows** ou **linux**.
    - Passez en revue les termes et conditions, puis cliquez sur la case à cocher **que j’accepte les termes et conditions susmentionnées** .
    - Cliquez sur le bouton **achat** .

3. Attendez que le déploiement à effectuer.

4. Cliquez avec le bouton droit de la souris sur le bouton ci-dessous, puis sélectionnez soit « ouvrir le lien dans un nouvel onglet » ou « Ouvrir le lien dans une nouvelle fenêtre » :  
[![Déployer vers Azure](./media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-hybrid-network-secure-vnet-dmz%2Fworkload.azuredeploy.json)

5. Une fois le lien ouvert dans Azure portal, vous devez entrer des valeurs pour certains paramètres : 
    - Le nom du **groupe de ressources** est déjà défini dans le fichier de paramètres : cliquez sur **Créer** et saisissez `ra-public-dmz-wl-rg` dans la zone de texte.
    - Sélectionnez la zone à partir de la zone de liste déroulante **emplacement** .
    - Ne modifiez pas l' **Uri racine de modèle** ou les zones de texte du **Paramètre racine Uri** .
    - Passez en revue les termes et conditions, puis cliquez sur la case à cocher **que j’accepte les termes et conditions susmentionnées** .
    - Cliquez sur le bouton **achat** .

6. Attendez que le déploiement à effectuer.

7. Cliquez avec le bouton droit de la souris sur le bouton ci-dessous, puis sélectionnez soit « ouvrir le lien dans un nouvel onglet » ou « Ouvrir le lien dans une nouvelle fenêtre » :  
[![Déployer vers Azure](./media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-hybrid-network-secure-vnet-dmz%2Fsecurity.azuredeploy.json)

8. Une fois le lien ouvert dans Azure portal, vous devez entrer des valeurs pour certains paramètres : 
    - Le nom du **groupe de ressources** est déjà défini dans le fichier de paramètres, donc **Utiliser existant** et tapez `ra-public-dmz-network-rg` dans la zone de texte.
    - Sélectionnez la zone à partir de la zone de liste déroulante **emplacement** .
    - Ne modifiez pas l' **Uri racine de modèle** ou les zones de texte du **Paramètre racine Uri** .
    - Passez en revue les termes et conditions, puis cliquez sur la case à cocher **que j’accepte les termes et conditions susmentionnées** .
    - Cliquez sur le bouton **achat** .

9. Attendez que le déploiement à effectuer.

10. Les fichiers de paramètres incluent les nom d’utilisateur codé en dur l’administrateur et le mot de passe pour tous les ordinateurs virtuels, et il est fortement recommandé que vous modifiez immédiatement à la fois. Pour chaque ordinateur virtuel dans le déploiement, sélectionnez-le dans le portail Azure, puis sur **Réinitialiser le mot de passe** dans la lame **de prise en charge + de résolution des problèmes** . Sélectionnez **Réinitialiser le mot de passe** dans la zone de liste déroulante **en Mode** , puis sélectionnez un nouveau **nom d’utilisateur** et un **mot de passe**. Cliquez sur le bouton de **mise à jour** pour rendre persistantes.


<!-- links -->

[availability-set]: ../virtual-machines/virtual-machines-windows-manage-availability.md
[guidance-vpn-gateway]: ./guidance-hybrid-network-vpn.md
[implementing-a-multi-tier-architecture-on-Azure]: ./guidance-compute-3-tier-vm.md
[implementing-a-secure-hybrid-network-architecture]: ./guidance-iaas-ra-secure-vnet-hybrid.md
[iptables]: https://help.ubuntu.com/community/IptablesHowTo
[lb-probe]: ../load-balancer/load-balancer-custom-probe-overview.md
[load-balancer]: ../load-balancer/load-balancer-internet-overview.md
[network-security-group]: ../virtual-network/virtual-networks-nsg.md
[ra-vpn]: ./guidance-hybrid-network-vpn.md
[ra-expressroute]: ./guidance-hybrid-network-expressroute.md
[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[vpn-failover]: ./guidance-hybrid-network-expressroute-vpn-failover.md
[0]: ./media/blueprints/hybrid-network-secure-vnet-dmz.png "Architecture de réseau hybride sécurisée"
