<properties 
   pageTitle="Les Zones DNS et les enregistrements | Microsoft Azure" 
   description="Vue d’ensemble de la prise en charge pour l’hébergement de zones DNS et des enregistrements dans le système DNS de Microsoft Azure." 
   services="dns" 
   documentationCenter="na" 
   authors="jtuliani" 
   manager="carmonm" 
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="10/17/2016"
   ms.author="jtuliani"/>

# <a name="dns-zones-and-records"></a>Enregistrements et zones DNS

Cette page explique les concepts clés de domaines, les zones DNS, les enregistrements DNS et jeux d’enregistrements et comment ils sont pris en charge dans le système DNS d’Azure.

## <a name="domain-names"></a>Noms de domaine
 
Le système de nom de domaine est une hiérarchie de domaines. Démarrage de la hiérarchie du domaine « root », dont le nom est simplement '**.**'.  Ci-dessous proviennent des domaines de niveau supérieur, tel que « com », « net », « org », 'uk' ou « jp ».  Dessous de ces derniers sont des domaines de second niveau, tel que 'org.uk' ou 'co.jp'. Les domaines dans la hiérarchie DNS sont globalement distribués, hébergés par des serveurs de nom DNS dans le monde entier.

Un registre des noms de domaine est une organisation qui vous permet d’acheter un nom de domaine, par exemple, « contoso.com ».  Achat d’un nom de domaine vous donne le droit de contrôler la hiérarchie DNS sous ce nom, par exemple, pour diriger le nom « www.contoso.com » à votre site web. Le Registre peut héberger le domaine dans ses propres serveurs de nom à votre place, ou vous pouvez également spécifier des serveurs de noms secondaires.

DNS Azure fournit une infrastructure de serveur de nom global distribués et à haute disponibilité, que vous pouvez utiliser pour héberger votre domaine. En hébergeant vos domaines dans Azure DNS, vous pouvez gérer vos enregistrements DNS avec les mêmes informations d’identification, API, outils, facturation et la prise en charge en tant que vos autres services Azure.

DNS Azure ne pas prend en charge l’achat de noms de domaine. Si vous souhaitez acheter un nom de domaine, vous devez utiliser un registre des noms de domaine de tiers. Le greffier généralement des frais une petite annuel. Les domaines peuvent ensuite être hébergés dans Azure DNS pour la gestion des enregistrements DNS. Pour plus d’informations, reportez-vous à la section [délégué un domaine DNS d’Azure](dns-domain-delegation.md) .

## <a name="dns-zones"></a>Zones DNS 

Une zone DNS est utilisée pour héberger les enregistrements DNS pour un domaine particulier. Pour démarrer l’hébergement de votre domaine dans le DNS d’Azure, vous devez créer une zone DNS pour ce nom de domaine. Chaque enregistrement DNS de votre domaine est ensuite créé à l’intérieur de cette zone DNS. 

Par exemple, le domaine « contoso.com » peut contenir plusieurs enregistrements DNS, tels que « mail.contoso.com » (pour un serveur de messagerie) et « www.contoso.com » (pour un site web).

Lorsque vous créez une zone DNS dans Azure DNS, le nom de la zone doit être unique dans le groupe de ressources. Le même nom de zone peut être réutilisé dans un groupe de ressources différent ou un autre abonnement Azure. Dans le cas où plusieurs zones partagent le même nom, chaque instance est affecté des adresses de serveur de nom différent. Un seul ensemble d’adresses peut être configuré avec le Registre des noms de domaine.

>[AZURE.NOTE] Vous n’êtes pas obligé de posséder un nom de domaine pour créer une zone DNS portant ce nom de domaine dans DNS d’Azure. Toutefois, vous n’avez pas besoin de posséder le domaine pour configurer les serveurs de noms DNS d’Azure que les serveurs de nom correct pour le nom de domaine avec le Registre des noms de domaine.

Pour plus d’informations, consultez [Delegate un domaine DNS d’Azure](dns-domain-delegation.md).

## <a name="dns-records"></a>Enregistrements DNS

### <a name="record-types"></a>Types d’enregistrements

Chaque enregistrement DNS a un nom et un type. Les enregistrements sont organisés en différents types en fonction des données qu’ils contiennent. Le type le plus courant est un enregistrement « A » qui associe un nom à une adresse IPv4. Un autre type courant est un enregistrement « MX », qui associe un nom à un serveur de messagerie.

Azure DNS prend en charge tous les types d’enregistrements DNS courants : A, AAAA, CNAME, MX, NS, PTR, SOA, SRV et TXT.

### <a name="record-names"></a>Noms d’enregistrement

Dans Azure, DNS, les enregistrements sont spécifiés à l’aide de noms relatifs. Un nom de domaine *pleinement qualifié* (FQDN) inclut le nom de zone n’est pas le cas d’un nom *relatif* . Par exemple, le nom relatif de l’enregistrement « www » dans la zone « contoso.com » donne le nom d’enregistrement complet « www.contoso.com ».

Un enregistrement *d’apex* est un enregistrement DNS à la racine (ou le *sommet*) d’une zone DNS. Par exemple, dans la zone DNS « contoso.com », un enregistrement d’apex a également le nom qualifié complet « contoso.com » (cela est parfois appelé un domaine *naked* ).  Par convention, le nom relatif '@' est utilisé pour créer des enregistrements d’apex.

### <a name="record-sets"></a>Jeux d’enregistrements
Parfois, vous avez besoin créer plus d’un enregistrement DNS avec un nom donné et le type. Par exemple, supposons que le site web « www.contoso.com » est hébergé sur deux adresses IP différentes. Le site Web de deux différents nécessite un enregistrements, un pour chaque adresse IP. Il s’agit d’un exemple d’un jeu d’enregistrements :

    www.contoso.com.        3600    IN  A   134.170.185.46
    www.contoso.com.        3600    IN  A   134.170.188.221

DNS Azure gère à l’aide *d’enregistrement définit*les enregistrements DNS. Un jeu d’enregistrements (également connu sous le nom un *ressource de* jeu d’enregistrements) est l’ensemble des enregistrements DNS dans une zone et qui ont le même nom et qui sont du même type. La plupart des jeux d’enregistrements contiennent un enregistrement unique, mais les exemples comme celui-ci, dans lesquels un jeu d’enregistrements contient plus d’un enregistrement, ne sont pas rares.

Par exemple, supposons que vous avez déjà créé un enregistrement de « www » dans la zone « contoso.com », qui pointe vers l’adresse IP adresse '134.170.185.46' (le premier enregistrement ci-dessus).  Pour créer le deuxième enregistrement vous souhaitez ajouter cet enregistrement à un jeu d’enregistrements existant, au lieu de créer un nouveau jeu d’enregistrements.

Les types d’enregistrements SOA et CNAME sont des exceptions. Les normes DNS n’autorisent pas plusieurs enregistrements portant le même nom pour ces types, par conséquent, ces jeux d’enregistrements ne peut contenir un seul enregistrement.

### <a name="time-to-live"></a>Durée de vie

La durée de vie, ou TTL, spécifie la durée pendant laquelle chaque enregistrement est mise en cache par les clients avant de nouveau interrogé. Dans l’exemple ci-dessus, la durée de vie est de 3 600 secondes ou 1 heure.

Dans le système DNS Azure, la durée de vie est spécifiée pour le jeu d’enregistrements, pas pour chaque enregistrement, donc la même valeur est utilisée pour tous les enregistrements au sein de ce jeu d’enregistrements.  Vous pouvez spécifier n’importe quelle valeur de durée de vie entre 1 et 2 147 483 647 secondes.

### <a name="wildcard-records"></a>Enregistrements de génériques

Azure DNS prend en charge les [enregistrements de caractère générique](https://en.wikipedia.org/wiki/Wildcard_DNS_record). Les enregistrements de génériques sont renvoyés en réponse à une requête avec un nom correspondant (à moins qu’il existe une correspondance plus étroite à partir d’un jeu d’enregistrements non-caractère générique). Jeux d’enregistrements génériques sont pris en charge pour tous les types d’enregistrements sauf NS et SOA.  

Pour créer un jeu d’enregistrements génériques, utilisez le nom de jeu d’enregistrements '\*'. Sinon, vous pouvez également utiliser un nom avec '\*'comme la plus à gauche étiquette, par exemple, »\*.foo ».

### <a name="cname-records"></a>Enregistrements CNAME

Jeux d’enregistrements CNAME ne peut pas coexister avec d’autres jeux d’enregistrements avec le même nom. Par exemple, Impossible de créer un enregistrement CNAME défini avec le nom « www » et un enregistrement A avec le nom « www » en même temps.

Étant donné que le sommet de la zone (nom = ‘@’) contient toujours le NS et SOA jeux qui ont été créés lors de la création de la zone d’enregistrements, vous ne pouvez pas créer un enregistrement CNAME définie au sommet de la zone.

Ces contraintes proviennent les normes DNS et ne sont pas des limites de DNS d’Azure.

### <a name="ns-records"></a>Enregistrements NS

Un jeu d’enregistrements NS est créé automatiquement au sommet de chaque zone (nom = ‘@’), et est automatiquement supprimée lorsque la zone est supprimée (il ne peut pas être supprimée séparément).  Vous pouvez modifier la durée de vie de ce jeu d’enregistrements, mais vous ne pouvez pas modifier les enregistrements, qui sont préconfigurés pour faire référence aux serveurs de noms DNS d’Azure affectés à la zone.

Vous pouvez créer et supprimer les autres enregistrements NS de la zone, pas au sommet de la zone.  Cela vous permet de configurer les zones enfant (voir [délégation des sous-domaines dans Azure DNS](dns-domain-delegation.md#delegating-sub-domains-in-azure-dns).)

### <a name="soa-records"></a>Enregistrements SOA

Un jeu d’enregistrements SOA est créé automatiquement au sommet de chaque zone (nom = ‘@’), et est automatiquement supprimée lorsque la zone est supprimée.  Les enregistrements SOA ne peut pas être créés ou supprimés séparément. 

Vous pouvez modifier toutes les propriétés de l’enregistrement SOA, à l’exception de la propriété 'host', qui est préconfigurée pour faire référence au nom du serveur de nom principal fourni par le système DNS d’Azure.

### <a name="spf-records"></a>Enregistrements SPF

Enregistrements SPF (Sender Policy Framework) de l’expéditeur sont utilisées pour spécifier les serveurs de messagerie sont autorisés à envoyer du courrier électronique au nom d’un nom de domaine donné.  La configuration correcte des enregistrements SPF est importante empêcher les destinataires de votre e-mail comme « indésirables » de marquage.

La RFC DNS a initialement introduit un nouveau type d’enregistrement « SPF » pour prendre en charge ce scénario. Pour prendre en charge les serveurs de noms plus anciens, ils autorisé également l’utilisation du type d’enregistrement TXT pour spécifier les enregistrements SPF.  Cette ambiguïté a conduit à une confusion, ce qui a été résolue par [RFC 7208](http://tools.ietf.org/html/rfc7208#section-3.1).  Cela a déclaré que les enregistrements SPF doivent être créés uniquement en utilisant le type d’enregistrement TXT et déconseillée le type d’enregistrement SPF. 

**Enregistrements SPF sont pris en charge par le serveur DNS d’Azure et doivent être créés en utilisant le type d’enregistrement TXT.** Le type d’enregistrement SPF obsolète n’est pas pris en charge. Lors de [l’importation d’un serveur DNS de la zone fichier](dns-import-export.md), tous les enregistrements SPF en utilisant le type d’enregistrement SPF sont convertis au type d’enregistrement TXT. 

### <a name="srv-records"></a>Enregistrements SRV

Pour spécifier des emplacements de serveur, les [enregistrements SRV](https://en.wikipedia.org/wiki/SRV_record) sont utilisés par différents services. Lorsque vous spécifiez un enregistrement SRV dans les DNS Azure :

- Le *service* et le *protocole* doivent être spécifiés en tant que partie du nom du jeu d’enregistrements, préfixé avec des traits de soulignement.  Par exemple, «\_sip. \_tcp.name'.  Pour un enregistrement au sommet de la zone, il est inutile de spécifier '@' dans le nom de l’enregistrement, utilisez simplement le service et le protocole, par exemple, «\_sip. \_tcp ».
- La *priorité*, le *poids*, le *port*et le *cible* sont spécifiés comme paramètres de chaque enregistrement dans le jeu d’enregistrements. 

## <a name="tags-and-metadata"></a>Métadonnées et balises

### <a name="tags"></a>Balises
Les balises sont une liste de paires nom-valeur et utilisés par le Gestionnaire de ressources Azure aux ressources de l’étiquette.  Azure Resource Manager utilise des balises pour activer des vues filtrées de votre nomenclature Azure et vous permet également de définir une stratégie dans laquelle les balises sont requis. Pour plus d’informations sur les balises, voir [en utilisant des balises pour organiser vos ressources Azure](../resource-group-using-tags.md).

DNS Azure, prend en charge à l’aide de balises d’Azure le Gestionnaire de ressources sur des ressources de zone DNS.  Il ne gère pas les balises de jeux d’enregistrements DNS.

### <a name="metadata"></a>Métadonnées

Comme alternative aux balises de jeu d’enregistrements, Azure DNS prend en charge les annoter des jeux d’enregistrements à l’aide de 'metadata'.  Similaires aux balises, métadonnées vous permet d’associer des paires nom-valeur à chaque jeu d’enregistrements.  Cela peut être utile, par exemple pour indiquer l’objectif de chaque jeu d’enregistrements.  Contrairement aux balises, les métadonnées ne peut pas être utilisée pour fournir une vue filtrée de votre nomenclature Azure et ne peuvent pas être spécifiées dans une stratégie de gestionnaire de ressources Azure.

## <a name="limits"></a>Limites

Les limites par défaut suivantes s’appliquent lors de l’utilisation de DNS d’Azure :

[AZURE.INCLUDE [dns-limits](../../includes/dns-limits.md)] 

## <a name="next-steps"></a>Étapes suivantes

- Pour commencer à utiliser DNS d’Azure, découvrez comment [créer une zone DNS](dns-getstarted-create-dnszone-portal.md) et de [créer des enregistrements DNS](dns-getstarted-create-recordset-portal.md).
- Pour migrer une zone DNS, apprenez comment [Importer et le fichier de zone DNS](dns-import-export.md).
