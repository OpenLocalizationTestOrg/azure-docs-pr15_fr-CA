<properties
    pageTitle="Déployer une côté Internet avec équilibrage de charge solution avec IPv6 à l’aide d’un modèle | Microsoft Azure"
    description="Comment déployer la prise en charge de IPv6 pour l’équilibreur de charge Azure et équilibrage de charge les machines virtuelles."
    services="load-balancer"
    documentationCenter="na"
    authors="sdwheeler"
    manager="carmonm"
    editor=""
    tags="azure-resource-manager"
    keywords="IPv6, équilibrage de la charge d’azure, double pile, adresse ip publique, ipv6 natif, mobile, iot"
/>
<tags
    ms.service="load-balancer"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="09/14/2016"
    ms.author="sewhee"
/>

# <a name="deploy-an-internet-facing-load-balancer-solution-with-ipv6-using-a-template"></a>Déployer une solution d’équilibrage de la charge via Internet avec IPv6 à l’aide d’un modèle

> [AZURE.SELECTOR]
- [PowerShell](./load-balancer-ipv6-internet-ps.md)
- [CLI Azure](./load-balancer-ipv6-internet-cli.md)
- [Modèle](./load-balancer-ipv6-internet-template.md)

Un équilibreur de charge Azure est un équilibreur de charge de couche-4 (TCP, UDP). L’équilibreur de charge fournit une haute disponibilité en répartissant le trafic entrant sur les instances de service en bon état dans les services en nuage ou de machines virtuelles dans un jeu d’équilibrage de la charge. Azure équilibreur de charge peut également présenter ces services sur plusieurs ports, plusieurs adresses IP ou les deux.

## <a name="example-deployment-scenario"></a>Exemple de scénario de déploiement

Le diagramme suivant illustre la solution d’équilibrage de la charge en cours de déploiement à l’aide de l’exemple de modèle décrit dans cet article.

![Scénario d’équilibrage de la charge](./media/load-balancer-ipv6-internet-template/lb-ipv6-scenario.png)

Dans ce scénario, vous allez créer les ressources Azure suivantes :

- une interface de réseau virtuel pour chaque ordinateur virtuel avec des adresses IPv4 et IPv6 affectées
- un équilibreur de charge faisant face à Internet IPv4 et une adresse IP publique de IPv6
- deux charge équilibrage de règles pour mapper la VIP public aux points de terminaison privés
- un ensemble de disponibilité qui contient deux ordinateurs virtuels
- deux machines virtuelles (VM)

## <a name="deploying-the-template-using-the-azure-portal"></a>Déploiement du modèle en utilisant le portail Azure

Cet article fait référence à un modèle qui est publié dans la galerie de [Modèles de démarrage rapide d’Azure](https://azure.microsoft.com/documentation/templates/201-load-balancer-ipv6-create/) . Vous pouvez télécharger le modèle de la galerie ou lancer le déploiement dans Azure directement à partir de la galerie. Cet article suppose que vous avez téléchargé le modèle sur votre ordinateur local.

1. Ouvrez le portail Azure et connectez-vous avec un compte qui dispose des autorisations nécessaires pour créer des ordinateurs virtuels et les ressources réseau au sein d’un abonnement Azure. En outre, sauf si vous utilisez des ressources existantes, le compte nécessite une autorisation pour créer un groupe de ressources et d’un compte de stockage.

2. Cliquez sur « + Nouveau » dans le menu puis du type « modèle » dans la zone de recherche. Sélectionnez « Déploiement du modèle » résultats de la recherche.

    ![livres-ipv6-portail-étape 2](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step2.png)

3. Dans le tout lame, cliquez sur « Déploiement modèle. »

    ![livres-ipv6-portail-étape 3](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step3.png)

4. Cliquez sur « Créer ».

    ![livres-ipv6-portail à l’étape4.](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step4.png)

5. Cliquez sur « Modifier le modèle ». Supprimer le contenu existant et copier/coller dans tout le contenu du fichier de modèle (pour inclure le début et la fin de {}), puis cliquez sur « Enregistrer ».

    > [AZURE.NOTE] Si vous utilisez Microsoft Internet Explorer, lorsque vous collez vous recevez une boîte de dialogue vous invitant à autoriser l’accès dans le Presse-papiers de Windows. Cliquez sur « Autoriser l’accès ».

    ![livres-ipv6-portail-step5](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step5.png)

6. Cliquez sur « Modifier les paramètres ». De la lame de paramètres, spécifiez les valeurs par les conseils de la section de paramètres de modèle, puis cliquez sur « Enregistrer » pour fermer la lame de paramètres. De la lame personnalisé déploiement, activez votre abonnement, un groupe de ressources existant ou créez-en un. Si vous créez un groupe de ressources, puis sélectionnez un emplacement pour le groupe de ressources. Ensuite, cliquez sur **conditions**, puis cliquez sur **acheter** pour les conditions juridiques. Azure commence à déployer les ressources. Il faut plusieurs minutes pour déployer toutes les ressources.

    ![livres-ipv6-portail-step6](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step6.png)

    Pour plus d’informations sur ces paramètres, reportez-vous à la section [variables et paramètres de modèle](#template-parameters-and-variables) plus loin dans cet article.

7. Pour afficher les ressources créées par le modèle, cliquez sur Parcourir, faites défiler la liste jusqu'à voir « Groupes de ressources », puis cliquez dessus.

    ![livres-ipv6-portail-step7](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step7.png)

8. La lame de groupes de ressources, cliquez sur le nom du groupe de ressources que vous avez spécifié à l’étape 6. Vous consultez une liste de toutes les ressources qui ont été déployés. Si tout va bien, il doit indiquer « Réussi » sous « Dernier déploiement. » Dans le cas contraire, vérifiez que le compte que vous utilisez dispose des autorisations pour créer les ressources nécessaires.

    ![livres-ipv6-portail-step8](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step8.png)

    > [AZURE.NOTE] Si vous accédez à vos groupes de ressources immédiatement après la fin de l’étape 6, « Déploiement de la dernière » affiche le statut de « Déploiement » tandis que les ressources sont déployées.

9. Cliquez sur « myIPv6PublicIP » dans la liste des ressources. Vous voyez qu’il dispose d’une adresse IPv6 sous adresse IP et que son nom DNS est la valeur spécifiée pour le paramètre dnsNameforIPv6LbIP à l’étape 6. Cette ressource est le public IPv6 adresse et nom d’hôte qui est accessible aux clients Internet.

    ![livres-ipv6-portail-step9](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step9.png)

## <a name="validate-connectivity"></a>Validation de la connexion

Lorsque le modèle a été déployé avec succès, vous pouvez valider la connectivité en effectuant les tâches suivantes :

1. Connectez-vous au portail Azure et se connecter à chacun des ordinateurs virtuels créés par le déploiement du modèle. Si vous avez déployé un serveur Windows VM, exécutez ipconfig/à partir d’une invite de commande. Vous voyez que les ordinateurs virtuels possèdent des adresses IPv4 et IPv6. Si vous avez déployé des machines virtuelles de Linux, vous devez configurer le système d’exploitation Linux pour recevoir les adresses IPv6 dynamiques en suivant les instructions de votre distribution Linux.
2. À partir d’un client connecté à l’Internet IPv6, établir une connexion avec l’adresse IPv6 publique de l’équilibreur de charge. Pour confirmer que l’équilibreur de charge est l’équilibrage entre les deux ordinateurs virtuels, vous pouvez installer un serveur web comme Microsoft Internet Information Services (IIS) sur chacun des ordinateurs virtuels. La page web par défaut sur chaque serveur peut contenir le texte « Server0 » ou « Serveur1 » pour identifier de manière unique. Ensuite, ouvrez un navigateur Internet sur un client connecté à l’Internet IPv6 et naviguez vers le nom d’hôte que vous avez spécifiée pour le paramètre dnsNameforIPv6LbIP de l’équilibreur de charge pour vérifier la connectivité IPv6 de bout en bout à chaque machine virtuelle. Si vous ne voyez que la page web à partir d’un seul serveur, vous devrez vider votre cache de navigateur. Ouvrir plusieurs sessions de navigation privées. Vous devez voir une réponse à partir de chaque serveur.
3. À partir d’un client connecté à l’Internet IPv4, établir une connexion avec l’adresse IPv4 publique de l’équilibreur de charge. Pour vérifier que l’équilibrage de charge est de deux ordinateurs virtuels d’équilibrage de la charge, vous pouvez tester à l’aide de IIS, comme indiqué à l’étape 2.
4. À partir de chaque ordinateur virtuel, initialiser une connexion sortante vers un périphérique IPv6 ou IPv4-connexion Internet. Dans les deux cas, l’adresse IP source vu par le périphérique de destination est l’adresse IPv4 ou IPv6 publique de l’équilibreur de charge.

>[AZURE.NOTE]
ICMP pour IPv4 et IPv6 est bloqué dans le réseau d’Azure. Par conséquent, les outils ICMP tels que ping toujours échouent. Pour tester la connectivité, utilisez une alternative TCP comme TCPing ou l’applet de commande PowerShell Test-NetConnection. Notez que les adresses IP indiquées dans le diagramme sont des exemples de valeurs que vous pouvez consulter. Dans la mesure où les adresses IPv6 sont attribués de façon dynamique, les adresses que vous recevez seront différent et peuvent varier selon les pays. En outre, il est courant pour l’adresse IPv6 publique de l’équilibreur de charge pour démarrer avec un préfixe différent que les adresses IPv6 privés dans le pool principal.

## <a name="template-parameters-and-variables"></a>Les variables et les paramètres de modèle

Un modèle de gestionnaire de ressources Azure contient plusieurs variables et les paramètres que vous pouvez personnaliser selon vos besoins. Les variables sont utilisées pour des valeurs fixes que vous ne souhaitez pas un utilisateur à modifier. Les paramètres sont utilisés pour les valeurs que vous souhaitez qu’un utilisateur à fournir lors du déploiement du modèle. L’exemple de modèle est configuré pour le scénario décrit dans cet article. Vous pouvez le personnaliser pour les besoins de votre environnement.

L’exemple de modèle utilisé dans cet article inclut les variables et les paramètres suivants :

| Paramètre / Variable | Notes |
|-----------|-------|
| adminUsername | Spécifiez le nom du compte administrateur utilisé pour vous connecter aux ordinateurs virtuels avec. |
| adminPassword | Spécifiez le mot de passe utilisé pour vous connecter aux ordinateurs virtuels avec le compte d’administrateur. |
| dnsNameforIPv4LbIP | Spécifiez le nom d’hôte DNS à affecter en tant que le nom public de l’équilibreur de charge. Ce nom est résolu en adresse IPv4 publique de l’équilibreur charge. Le nom doit être en minuscules et correspond à l’expression régulière : ^ [a-z][a-z0-9-]{1,61}[a-z0-9]$. |
| dnsNameforIPv6LbIP | Spécifiez le nom d’hôte DNS à affecter en tant que le nom public de l’équilibreur de charge. Ce nom est résolu en adresse IPv6 publique de l’équilibreur charge. Le nom doit être en minuscules et correspond à l’expression régulière : ^ [a-z][a-z0-9-]{1,61}[a-z0-9]$. Cela peut être le même nom que l’adresse IPv4. Lorsqu’un client envoie une requête DNS pour ce nom d’Azure renverra à la fois les enregistrements A et AAAA lorsque le nom est partagé. |
| vmNamePrefix | Permet de spécifier le préfixe de nom d’ordinateur virtuel. Le modèle ajoute un numéro (0, 1, etc.) pour le nom lors de la création d’ordinateurs virtuels. |
| nicNamePrefix | Permet de spécifier le préfixe de nom d’interface réseau. Le modèle ajoute un numéro (0, 1, etc.) pour le nom lors de la création d’interfaces réseau. |
| storageAccountName | Entrez le nom d’un compte de stockage existant ou spécifiez le nom d’un nouveau qui sera créé par le modèle. |
| availabilitySetName | Entrez ensuite le nom de la disponibilité défini pour être utilisé avec les ordinateurs virtuels |
| addressPrefix | Le préfixe d’adresse permet de définir la plage d’adresses du réseau virtuel |
| subnetName | Le nom du sous-réseau dans créé pour la VNet |
| subnetPrefix | Le préfixe d’adresse permet de définir la plage d’adresses du sous-réseau |
| vnetName | Spécifiez le nom de la VNet utilisée par les ordinateurs virtuels. |
| ipv4PrivateIPAddressType | La méthode de répartition utilisée pour l’adresse IP privée (statique ou dynamique) |
| ipv6PrivateIPAddressType | La méthode de répartition utilisée pour l’adresse IP privée (dynamique). IPv6 prend uniquement en charge l’allocation dynamique. |
| numberOfInstances | Le nombre d’instances d’équilibrage de charge déployées par le modèle |
| ipv4PublicIPAddressName | Spécifiez le nom DNS que vous souhaitez utiliser pour communiquer avec l’adresse IPv4 publique de l’équilibreur de charge. |
| ipv4PublicIPAddressType | La méthode de répartition utilisée pour l’adresse IP publique (statique ou dynamique) |
| Ipv6PublicIPAddressName | Spécifiez le nom DNS que vous souhaitez utiliser pour communiquer avec l’adresse IPv6 publique de l’équilibreur de charge. |
| ipv6PublicIPAddressType | La méthode de répartition utilisée pour l’adresse IP publique (dynamique). IPv6 prend uniquement en charge l’allocation dynamique. |
| lbName | Spécifiez le nom de l’équilibreur de charge. Ce nom est affiché dans le portail ou utilisé pour faire référence à celui-ci avec une commande CLI ou PowerShell. |

Les autres variables dans le modèle contiennent des valeurs dérivées qui sont affectés lorsque Azure crée les ressources. Ne modifiez pas ces variables.
