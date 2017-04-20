<properties
   pageTitle="Importation et exportation d’un fichier de zone du domaine DNS Azure à l’aide de la CLI | Microsoft Azure"
   description="Découvrez comment importer et exporter un fichier de zone DNS vers Azure DNS en utilisant l’infrastructure du langage commun Azure"
   services="dns"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/16/2016"
   ms.author="sewhee"/>

# <a name="import-and-export-a-dns-zone-file-using-the-azure-cli"></a>Importer et exporter un fichier de zone DNS à l’aide de la CLI d’Azure


Cet article vous guidera tout au long de l’importer et exporter des fichiers de zone DNS pour DNS Azure à l’aide de la CLI d’Azure.

## <a name="introduction-to-dns-zone-migration"></a>Introduction à la migration de zone DNS

Un fichier de zone DNS est un fichier texte qui contient les détails de chaque enregistrement de nom de domaine (DNS) dans la zone. Il respecte un format standard, ce qui la rend appropriée pour le transfert des enregistrements DNS entre les systèmes DNS. À l’aide d’un fichier de zone est rapide, fiable et pratique permet de transférer une zone DNS dans ou en dehors d’Azure DNS.

Azure DNS prend en charge l’importation et l’exportation des fichiers de zone à l’aide de l’interface de ligne de commande (CLI) Azure. L’infrastructure du langage commun Azure est un outil de ligne de commande multiplateforme utilisé pour la gestion des services Azure. Il est disponible pour les plates-formes Windows, Mac et Linux à partir de la [page de téléchargements Azure](https://azure.microsoft.com/downloads/). Prise en charge de plusieurs plates-formes est particulièrement importante pour l’importation et l’exportation des fichiers de zone, dans la mesure où le logiciel de serveur de nom plus courant, [lier](https://www.isc.org/downloads/bind/), est généralement exécuté sous Linux.

## <a name="obtain-your-existing-dns-zone-file"></a>Obtenir le fichier de zone DNS existant

Avant d’importer un fichier de zone DNS dans Azure DNS, vous devez obtenir une copie du fichier de zone. La source de ce fichier dépend de la zone DNS est actuellement hébergeant.

- Si votre zone DNS est hébergée par un service partenaire (par exemple un registraire de domaine, fournisseur d’hébergement dédié DNS ou fournisseur de nuage de remplacement), ce service doit fournir la possibilité de télécharger le fichier de zone DNS.

- Si votre zone DNS est hébergée sur DNS Windows, le dossier par défaut pour les fichiers de zone est **%systemroot%\system32\dns**. Le chemin d’accès complet pour chaque fichier de zone s’affiche également sous l’onglet **Général** de la console de gestion du service DNS.

- Si votre zone DNS est hébergée à l’aide de liaison, l’emplacement du fichier de zone pour chaque zone est spécifié dans le fichier du configuration BIND **named.conf**.

**Utilisation des fichiers de zone à partir de GoDaddy**<BR>
Les fichiers de zone téléchargés à partir de GoDaddy ont un format légèrement non standard. Vous devez corriger ce problème avant d’importer ces fichiers de zone dans Azure DNS. Dans la section RData d’un chaque enregistrement DNS, les noms DNS sont spécifiés en tant que noms qualifiés complets, mais ne sont pas un arrêt «. » Cela signifie qu’elles sont interprétées par d’autres systèmes DNS en tant que noms relatifs. Vous devez modifier le fichier pour l’ajouter à la fin de la zone «. » pour leurs noms avant de les importer dans Azure DNS.

## <a name="import-a-dns-zone-file-into-azure-dns"></a>Importer un fichier de zone DNS dans Azure DNS


Importation d’un fichier de zone crée une nouvelle zone dans Azure DNS s’il n’existe pas déjà. Si la zone existe déjà, les jeux d’enregistrements dans le fichier de zone doivent être fusionnées avec les jeux d’enregistrements existants.

### <a name="merge-behavior"></a>Comportement de fusion

- Par défaut, les jeux d’enregistrements nouveaux et existants sont fusionnés. Enregistrements identiques au sein d’un jeu d’enregistrements fusionné sont dédupliquées.

- Vous pouvez également, en spécifiant le `--force` option, le processus d’importation remplace existants avec nouveaux jeux d’enregistrements de jeux d’enregistrements. Des ensembles d’enregistrements existants qui n’ont pas d’enregistrement correspondant dans le fichier de zone importées ne seront pas supprimés.

- Lors de la fusion de jeux d’enregistrements, la durée de vie de jeux d’enregistrements préexistants est utilisée. Lors de la `--force` est utilisée, la durée de vie du nouveau jeu d’enregistrements est utilisée.

- Début de paramètres de l’autorité (SOA) (à l’exception de `host`) sont toujours pris à partir du fichier de zone importés, que `--force` est utilisé. De même, pour l’enregistrement de serveur de nom défini au sommet de la zone, la durée de vie est toujours prise à partir du fichier de zone importés.

- Un enregistrement CNAME importé ne remplace pas un enregistrement CNAME existant portant le même nom, sauf si la `--force` paramètre est spécifié.

- Lorsqu’un conflit se produit entre un enregistrement CNAME et un autre enregistrement portant le même nom mais un type différent (quelle que soit la qui est existant ou nouveau), l’enregistrement existant est conservé. Cette valeur est indépendante de l’utilisation de `--force`.

### <a name="additional-information-about-importing"></a>Informations supplémentaires sur l’importation

Les notes suivantes fournissent des détails techniques supplémentaires sur la zone processus d’importation.

- Le `$TTL` la directive est facultative, et il est pris en charge. Lorsque le n° `$TTL` la directive est donnée, les enregistrements sans une durée de vie explicite seront importé ensemble pour une durée de vie de 3 600 secondes par défaut. Lorsque deux enregistrements du même jeu d’enregistrements spécifient TTLs différents, c’est la valeur inférieure qui est utilisée.

- Le `$ORIGIN` la directive est facultative, et il est pris en charge. Lorsque le n° `$ORIGIN` est défini, la valeur par défaut est le nom de zone spécifié sur la ligne de commande (ainsi que l’arrêt «. »).

- La `$INCLUDE` et `$GENERATE` directives ne sont pas prises en charge.

- Ces types d’enregistrements sont prises en charge : A, AAAA, CNAME, MX, NS, SOA, SRV et TXT.

- L’enregistrement SOA est créé automatiquement par le système DNS d’Azure lorsqu’une zone est créée. Lorsque vous importez un fichier de zone, tous les paramètres SOA sont prises à partir de la zone fichier *à l’exception de* la `host` paramètre. Ce paramètre utilise la valeur fournie par le système DNS d’Azure. C’est pourquoi ce paramètre doit faire référence au serveur de noms principal fourni par le système DNS d’Azure.

- L’enregistrement de serveur de nom défini au sommet de la zone est également créé automatiquement par Azure DNS lorsque la zone est créée. Seulement la durée de vie de ce jeu d’enregistrements est importée. Ces enregistrements contiennent les noms de serveur de nom fournis par le système DNS d’Azure. Les données d’enregistrement ne sont pas remplacées par les valeurs contenues dans le fichier de zone importés.

- Au cours de la présentation publique, Azure DNS prend en charge les uniquement les enregistrements TXT une chaîne unique. Les enregistrements TXT multichaîne seront concaténées et tronquées à 255 caractères.

### <a name="cli-format-and-values"></a>Valeurs et format de l’infrastructure du langage commun


Le format de la commande CLI d’Azure pour importer une zone DNS est :<BR>`azure network dns zone import [options] <resource group> <zone name> <zone file name>`

Valeurs :

- `<resource group>`est le nom du groupe de ressources pour la zone DNS d’Azure.
- `<zone name>`est le nom de la zone.
- `<zone file name>`est le chemin du fichier de zone à importer.

Si une zone portant ce nom n’existe pas dans le groupe de ressources, il sera créé pour vous. Si la zone existe déjà, les jeux d’enregistrements importés seront fusionnées avec les jeux d’enregistrements existants. Pour remplacer les jeux d’enregistrements existants, utilisez la `--force` option.

Pour vérifier le format d’un fichier de zone sans réellement important, utilisez le `--parse-only` option.

### <a name="step-1-import-a-zone-file"></a>Étape 1. Importer un fichier de zone

Pour importer un fichier de zone pour la zone **contoso.com**.

1. Connectez-vous à votre abonnement Azure à l’aide de la CLI d’Azure.

        azure login

2. Sélectionnez l’abonnement dans lequel vous souhaitez créer votre nouvelle zone DNS.

        azure account set <subscription name>

3. Azure DNS étant un service Gestionnaire de ressources uniquement Azure, le CLI Azure doit être basculé en mode de Gestionnaire des ressources.

        azure config mode arm

4. Avant d’utiliser le service DNS d’Azure, vous devez inscrire votre abonnement pour utiliser le fournisseur de ressources Microsoft.Network. (Il s’agit d’une opération unique pour chaque abonnement.)

        azure provider register Microsoft.Network

5. Si vous n’avez pas encore, vous devez également créer un groupe de ressources du Gestionnaire de ressources.

        azure group create myresourcegroup westeurope

6. Pour importer le fichier **contoso.com.txt** la zone **contoso.com** dans une zone DNS dans le groupe de ressources **myresourcegroup**, exécutez la commande `azure network dns zone import`.<BR>Cette commande charge le fichier de zone et l’analyser. La commande exécute une série de commandes sur le service DNS d’Azure pour créer la zone et l’enregistrement se définit dans la zone. La commande signale également la progression dans la fenêtre de la console, ainsi que les éventuels avertissements ou erreurs. Dans la mesure où les jeux d’enregistrements sont créés dans la série, il peut prendre quelques minutes pour importer un fichier de zone volumineux.

        azure network dns zone import myresourcegroup contoso.com contoso.com.txt



### <a name="step-2-verify-the-zone"></a>Étape 2. Vérifiez que la zone

Pour vérifier la zone DNS après avoir importé le fichier, vous pouvez utiliser l’une des méthodes suivantes :

- Vous pouvez répertorier les enregistrements à l’aide de la commande CLI d’Azure suivante.

        azure network dns record-set list myresourcegroup contoso.com

- Vous pouvez répertorier les enregistrements à l’aide de l’applet de commande PowerShell `Get-AzureRmDnsRecordSet`.

- Vous pouvez utiliser `nslookup` pour vérifier la résolution de nom pour les enregistrements. Étant donné que la zone n’est pas déléguée encore, vous devez spécifier de manière explicite les serveurs de noms DNS d’Azure corrects. L’exemple ci-dessous montre comment récupérer les noms de serveur de nom attribuées à la zone. IT montre également comment interroger l’enregistrement « www » à l’aide de `nslookup`.

        C:\>azure network dns record-set show myresourcegroup contoso.com @ NS
        info:Executing command network dns record-set show
        + Looking up the DNS Record Set "@" of type "NS"
        data:Id: /subscriptions/…/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/NS/@
        data:Name: @
        data:Type: Microsoft.Network/dnszones/NS
        data:Location: global
        data:TTL : 3600
        data:NS records
        data:Name server domain name : ns1-01.azure-dns.com
        data:Name server domain name : ns2-01.azure-dns.net
        data:Name server domain name : ns3-01.azure-dns.org
        data:Name server domain name : ns4-01.azure-dns.info
        data:
        info:network dns record-set show command OK

        C:\> nslookup www.contoso.com ns1-01.azure-dns.com

        Server: ns1-01.azure-dns.com
        Address:  40.90.4.1

        Name:www.contoso.com
        Addresses:  134.170.185.46
        134.170.188.221

### <a name="step-3-update-dns-delegation"></a>Étape 3. Mettre à jour la délégation DNS

Après avoir vérifié que la zone a été importée correctement, vous devez mettre à jour la délégation DNS pour pointer vers les serveurs de noms DNS d’Azure. Pour plus d’informations, voir l’article [mettre à jour de la délégation DNS](dns-domain-delegation.md).

## <a name="export-a-dns-zone-file-from-azure-dns"></a>Exporter un fichier de zone DNS à partir de DNS d’Azure

Le format de la commande CLI d’Azure pour importer une zone DNS est :<BR>`azure network dns zone export [options] <resource group> <zone name> <zone file name>`

Valeurs :

- `<resource group>`est le nom du groupe de ressources pour la zone DNS d’Azure.
- `<zone name>`est le nom de la zone.
- `<zone file name>`est le chemin du fichier de zone à exporter.

Comme avec l’importation de la zone, vous tout d’abord besoin pour vous connecter, choisissez votre abonnement et configurer l’infrastructure du langage commun Azure pour utiliser le mode du Gestionnaire de ressources.

### <a name="to-export-a-zone-file"></a>Pour exporter un fichier de zone


1. Connectez-vous à votre abonnement Azure à l’aide de la CLI d’Azure.

        azure login

2. Sélectionnez l’abonnement dans lequel vous souhaitez créer votre nouvelle zone DNS.

        azure account set <subscription name>

3. Azure DNS est un service de gestionnaire de ressources uniquement Azure. L’infrastructure du langage commun Azure doit être basculé en mode de Gestionnaire des ressources.

        azure config mode arm

4. Pour exporter l’existant Azure DNS zone **contoso.com** dans la ressource groupe **myresourcegroup** dans le fichier **contoso.com.txt** (dans le dossier en cours), vous devez exécuter `azure network dns zone export`. Cette commande appelle le service DNS d’Azure pour énumérer des ensembles d’enregistrements dans la zone et d’exporter les résultats vers un fichier de zone compatible avec BIND.

        azure network dns zone export myresourcegroup contoso.com contoso.com.txt

