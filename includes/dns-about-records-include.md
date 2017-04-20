## <a name="about-records"></a>À propos des enregistrements

Chaque enregistrement DNS a un nom et un type. Les enregistrements sont organisés en différents types en fonction des données qu’ils contiennent. Le type le plus courant est un enregistrement « A », qui associe un nom à une adresse IPv4. Un autre type est un enregistrement « MX », qui associe un nom à un serveur de messagerie.

Azure DNS prend en charge tous les common types d’enregistrements DNS, y compris A, AAAA, CNAME, MX, NS, PTR, SOA, SRV et TXT. Notez que :
- Jeux d’enregistrements SOA sont créées automatiquement avec chaque zone, ils ne peuvent pas être créés séparément.
- Enregistrements SPF doivent être créés en utilisant le type d’enregistrement TXT. Pour plus d’informations, consultez [la page suivante](http://tools.ietf.org/html/rfc7208#section-3.1).

Dans Azure, DNS, les enregistrements sont spécifiés à l’aide de noms relatifs. Un nom de domaine « complet » (FQDN) inclut le nom de zone, qu’il n’est pas le cas d’un nom « relatif ». Par exemple, le nom relatif de l’enregistrement « www » dans la zone « contoso.com » donne le nom qualifié complet de l’enregistrement www.contoso.com.

## <a name="about-record-sets"></a>À propos des jeux d’enregistrements

Parfois, vous avez besoin créer plus d’un enregistrement DNS avec un nom donné et le type. Par exemple, supposons que le site web « www.contoso.com » est hébergé sur deux adresses IP différentes. Le site Web de deux différents nécessite un enregistrements, un pour chaque adresse IP. Il s’agit d’un exemple d’un jeu d’enregistrements :

    www.contoso.com.        3600    IN  A   134.170.185.46
    www.contoso.com.        3600    IN  A   134.170.188.221

DNS Azure gère les enregistrements DNS à l’aide de jeux d’enregistrements. Un jeu d’enregistrements est l’ensemble des enregistrements DNS dans une zone et qui ont le même nom et qui sont du même type. La plupart des jeux d’enregistrements contiennent un enregistrement unique, mais les exemples comme celui-ci, dans lesquels un jeu d’enregistrements contient plus d’un enregistrement, ne sont pas rares.

Jeux d’enregistrements SOA et CNAME sont des exceptions. Les normes DNS n’autorisent pas plusieurs enregistrements portant le même nom pour ces types.

La durée de vie, ou TTL, spécifie la durée pendant laquelle chaque enregistrement est mise en cache par les clients avant de nouveau interrogé. Dans cet exemple, la durée de vie est de 3 600 secondes ou 1 heure. La durée de vie est spécifiée pour le jeu d’enregistrements, pas pour chaque enregistrement, la même valeur est utilisée pour tous les enregistrements au sein de ce jeu d’enregistrements.

#### <a name="wildcard-record-sets"></a>Ensembles d’enregistrements génériques

Azure DNS prend en charge les [enregistrements de caractère générique](https://en.wikipedia.org/wiki/Wildcard_DNS_record). Ils sont renvoyés pour une requête avec un nom correspondant (à moins qu’il existe une correspondance plus étroite à partir d’un jeu d’enregistrements non-caractère générique). Jeux d’enregistrements génériques sont pris en charge pour tous les types d’enregistrements sauf NS et SOA.  

Pour créer un jeu d’enregistrements génériques, utilisez le nom de jeu d’enregistrements "\*«. Utiliser un nom avec l’étiquette "\*«, par exemple, »\*.foo ».

#### <a name="cname-record-sets"></a>Jeux d’enregistrements CNAME

Jeux d’enregistrements CNAME ne peut pas coexister avec d’autres jeux d’enregistrements avec le même nom. Par exemple, Impossible de créer un enregistrement CNAME défini avec le nom « www » et un enregistrement A avec le nom « www » en même temps. Étant donné que le sommet de la zone (nom = ‘@’) contient toujours le NS et SOA jeux qui ont été créés lors de la création de la zone d’enregistrements, vous ne pouvez pas créer un enregistrement CNAME définie au sommet de la zone. Ces contraintes proviennent les normes DNS et ne sont pas des limites de DNS d’Azure.
