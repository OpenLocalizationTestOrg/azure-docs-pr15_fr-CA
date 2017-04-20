<BR> 
## <a name="faq---hosting-your-arpa-zone-in-azure-dns"></a>Forum aux questions - hébergeant votre zone ARPA dans Azure DNS

### <a name="can-i-host-arpa-zones-for-my-isp-assigned-ip-blocks-on-azure-dns"></a>Puis-je héberger les zones ARPA pour mon blocs IP affectée par le fournisseur de services Internet sur Azure DNS ?
Oui. Les zones ARPA pour vos propres plages IP dans Azure DNS d’hébergement est entièrement pris en charge.

[Créez](dns-getstarted-create-dnszone.md)simplement la zone DNS d’Azure, puis travailler avec votre fournisseur de services Internet pour [déléguer la zone](dns-domain-delegation.md).  Vous pouvez ensuite gérer les enregistrements PTR pour chaque recherche inversée de la même façon que d’autres types d’enregistrement.

Vous pouvez également [Importer une zone de recherche inversée existante à l’aide de la CLI d’Azure](dns-import-export.md).

### <a name="how-much-does-hosting-my-arpa-zone-cost"></a>Combien mes frais de zone ARPA d’hébergement ?
Bloc dans Azure DNS hébergeant la zone ARPA pour votre IP affectée par le fournisseur de services Internet est facturée au [taux standard de DNS d’Azure](https://azure.microsoft.com/pricing/details/dns/).

### <a name="can-i-host-arpa-zones-for-both-ipv4-and-ipv6-addresses-in-azure-dns"></a>Puis-je héberger les zones ARPA pour les adresses IPv4 et IPv6 dans Azure DNS ?
Oui.