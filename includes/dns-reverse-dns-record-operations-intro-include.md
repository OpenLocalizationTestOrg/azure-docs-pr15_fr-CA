## <a name="what-is-reverse-dns"></a>Nouveautés de DNS inversée ?

Les enregistrements DNS conventionnelles activer un mappage à partir d’un nom DNS (par exemple, « www.contoso.com ») à une adresse IP (par exemple, 64.4.6.100).  DNS inverse permet la traduction d’une adresse IP (64.4.6.100) avec un nom (« www.contoso.com »).

Enregistrement DNS inverse est utilisées dans de nombreuses situations. Par exemple, les enregistrements DNS inversées sont largement utilisés dans la lutte contre le courrier non sollicité en vérifiant l’expéditeur d’un message électronique.  Le serveur de messagerie de réception va récupérer l’enregistrement DNS inversée de l’adresse IP du serveur de l’expéditeur et vérifier si cet hôte est autorisé à envoyer des messages électroniques à partir du domaine d’origine. (Notez toutefois que [services d’Azure Compute ne gèrent pas l’envoi de messages électroniques à des domaines externes](https://blogs.msdn.microsoft.com/mast/2016/04/04/sending-e-mail-from-azure-compute-resource-to-external-domains/).)

## <a name="how-reverse-dns-works"></a>Comment inversée DNS fonctionne

Les enregistrements DNS inversées sont hébergés dans les zones DNS spéciaux, appelés zones de « ARPA ».  Ces zones constituent une hiérarchie DNS distincte en parallèle avec la hiérarchie classique hébergeant des domaines, par exemple, « contoso.com ».

Par exemple, l’enregistrement DNS « www.contoso.com » est implémentée à l’aide d’un enregistrement DNS « A » avec le nom « www » dans la zone « contoso.com ».  Cet enregistrement A pointe vers l’adresse IP correspondante, dans ce cas 64.4.6.100.  La recherche inversée est implémentée séparément, à l’aide d’un enregistrement « PTR » nommé « 100 » dans la zone « 6.4.64.in-addr.arpa » (Notez que les adresses IP sont inversés dans les zones de l’ARPA.)  Cet enregistrement PTR, s’il a été configuré correctement, pointe au nom « www.contoso.com ».

Lorsqu’une organisation est affectée d’un bloc d’adresse IP, ils acquièrent également le droit de gérer l’arpa parent correspondant. Les zones de l’ARPA correspondant pour les blocs d’adresses IP utilisées par Azure sont hébergés et gérés par Microsoft. Votre fournisseur de services Internet peut héberger ARPA pour vos propres adresses IP pour vous, ou peut vous permettre d’héberger la zone ARPA dans un service DNS de votre choix, tels que DNS d’Azure.

>[AZURE.NOTE] Les recherches DNS directes et inversées sont implémentées dans les hiérarchies DNS distincts, en parallèle. La recherche inversée pour « www.contoso.com » **pas** hébergés dans la zone « contoso.com », au lieu de cela, il est hébergé dans la zone de l’ARPA pour le bloc d’adresse IP correspondante.

Pour plus d’informations sur DNS inversée, consultez [Recherche DNS inversée](http://en.wikipedia.org/wiki/Reverse_DNS_lookup).

## <a name="azure-support-for-reverse-dns"></a>Prise en charge Azure pour DNS inversée

Azure prend en charge deux scénarios distincts concernant la recherche DNS inversée :

1. Hébergement de l’arpa parent correspondant à votre bloc d’adresse IP.
2. Qui vous permet de configurer l’enregistrement DNS inversée pour l’adresse IP attribuée à votre service Azure.

Pour prendre en charge l’ancien, Azure DNS permet d’héberger vos zones ARPA et de gérer les enregistrements PTR pour chaque recherche DNS inversée.  Le processus de création de la zone d’ARPA, la configuration de la délégation et la configuration des enregistrements PTR est la même que pour les zones DNS standard.  Les seules différences sont que la délégation doit être configurée via votre fournisseur de services Internet au lieu de votre registraire DNS et seulement le type d’enregistrement PTR doit être utilisé.

Pour prendre en charge ce dernier, Azure vous permet de configurer la recherche inversée pour les adresses IP affectées à votre service.  Cette recherche inversée est configurée par Azure comme un enregistrement PTR dans la zone ARPA correspondante.  Ces zones ARPA, correspondant à toutes les plages IP utilisés par Azure, hébergés par Microsoft. **Le reste de cet article décrit ce scénario en détail.**
