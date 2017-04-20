Le système de nom de domaine (DNS) est utilisé pour rechercher des éléments sur internet. Par exemple, lorsque vous entrez une adresse dans votre navigateur, ou cliquez sur un lien sur une page web, il utilise le système DNS pour le domaine se traduire par une adresse IP. L’adresse IP est comme une adresse postale, mais il n’est pas convivial très humain. Par exemple, il est beaucoup plus facile de se souvenir d’un nom DNS comme **contoso.com** plutôt que de se souvenir d’une adresse IP telle que 192.168.1.88 ou 2001:0:4137:1f67:24a2:3888:9cce:fea3.

Le système DNS est basé sur des *enregistrements*. Les enregistrements associent un *nom*par exemple, **contoso.com**, avec une adresse IP ou un autre nom DNS. Lorsqu’une application, comme un navigateur web, recherche un nom dans DNS, il trouve l’enregistrement et l’utilise quel que soit il pointe vers l’adresse. Si la valeur qu’il désigne est une adresse IP, le navigateur utilisera cette valeur. Si elle pointe vers un autre nom DNS, l’application doit ensuite faire à nouveau de résolution. Enfin, toute la résolution de nom se termine dans une adresse IP.

Lorsque vous créez un site Web d’Azure, un nom DNS est automatiquement affecté au site. Ce nom prend la forme de ** &lt;yoursitename&gt;. azurewebsites.net**. Lorsque vous ajoutez à votre site Web sous la forme d’un point de terminaison Azure Traffic Manager, votre site Web est accessible par le biais de la ** &lt;yourtrafficmanagerprofile&gt;. trafficmanager.net** domaine.

> [AZURE.NOTE] Lorsque votre site Web est configuré comme un point de terminaison de Traffic Manager, vous pouvez utiliser la **. trafficmanager.net** adresse lors de la création d’enregistrements DNS.

> Vous ne pouvez utiliser les enregistrements CNAME du trafic avec le Gestionnaire de périphériques

Il existe également plusieurs types d’enregistrements, chacun avec ses propres fonctions et les limites, mais pour les sites Web configurés pour comme points de terminaison de Traffic Manager, nous n’y d’un ; Enregistrements *CNAME* .

###<a name="cname-or-alias-record"></a>Enregistrement de nom canonique ou l’Alias

Un enregistrement CNAME associe un nom DNS *spécifique* , tel que **mail.contoso.com** ou **www.contoso.com**, à un autre nom de domaine (canonique). Dans le cas d’Azure des sites Web à l’aide du Gestionnaire de trafic, le nom de domaine canonique est la ** &lt;myapp >. trafficmanager.net** nom de domaine de votre profil de gestionnaire de trafic. Une fois créé, l’enregistrement CNAME crée un alias pour le ** &lt;myapp >. trafficmanager.net** nom de domaine. L’entrée CNAME résout l’adresse IP de votre ** &lt;myapp >. trafficmanager.net** nom de domaine automatiquement, donc si l’adresse IP du site Web change, vous n’avez pas à intervenir.

Une fois le trafic atteint le trafic dans le Gestionnaire de périphériques, il achemine ensuite le trafic sur votre site Web, à l’aide de la méthode, qu'il est configuré pour être d’équilibrage de la charge. Cela est complètement transparent pour les visiteurs de votre site Web. Ne voit que le nom de domaine personnalisé dans leur navigateur.

> [AZURE.NOTE] Certains bureaux de domaine uniquement vous permettent de mapper les sous-domaines lors de l’utilisation d’un enregistrement CNAME, par exemple **www.contoso.com**et non les noms de racine, par exemple, **contoso.com**. Pour plus d’informations sur les enregistrements CNAME, consultez la documentation fournie par le document <a href="http://tools.ietf.org/html/rfc1035">IETF de noms de domaine - implémentation et spécification</a> , <a href="http://en.wikipedia.org/wiki/CNAME_record">l’entrée de Wikipedia sur l’enregistrement CNAME</a>ou votre registraire.
