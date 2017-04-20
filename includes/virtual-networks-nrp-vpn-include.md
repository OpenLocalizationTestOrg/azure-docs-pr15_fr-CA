## <a name="vpn-gateway"></a>Passerelle VPN 
Une ressource de passerelle VPN vous permet de créer une connexion sécurisée entre leur centre de données sur site et l’Azure. Une ressource de passerelle VPN peut être configurée de trois manières différentes :
 
- **Pointez sur Site** – vous pouvez accéder en toute sécurité à vos ressources Azure hébergés dans un VNET à l’aide d’un client VPN à partir de n’importe quel ordinateur. 
- **Connexion de plusieurs sites** , vous pouvez connecter en toute sécurité à partir de vos centres de données sur site aux ressources en cours d’exécution dans un VNET. 
- **VNET à VNET** , vous pouvez vous connecter en toute sécurité sur VNETS d’Azure dans la même région ou entre régions pour générer des charges de travail avec redondance géographique.

Propriétés de passerelle VPN de clé sont les suivants :
 
- **Type de passerelle** - routé de manière dynamique ou une passerelle routée statique. 
- **Préfixe du Pool d’adresses VPN Client** – les adresses IP à affecter aux clients qui se connectent à un point de la configuration du site.