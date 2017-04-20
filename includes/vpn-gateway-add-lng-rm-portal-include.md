1. Dans le portail, à partir de **toutes les ressources**, cliquez sur **+ Ajouter**. Dans la **tout** lame zone Rechercher, tapez la **passerelle du réseau Local**, puis cliquez sur pour effectuer une recherche. Ceci renverra une liste. Cliquez sur la **passerelle du réseau Local** pour ouvrir la lame, puis cliquez sur **créer** pour ouvrir la lame de la **passerelle du réseau local créer** .

    ![créer la passerelle du réseau local](./media/vpn-gateway-add-lng-rm-portal-include/addlng250.png)

2. Sur la **lame de passerelle réseau local créer**, spécifiez un **nom** pour votre objet de passerelle du réseau local.
 
3. Spécifiez une **adresse IP** publique valide pour le périphérique VPN ou passerelle réseau virtuel auquel vous voulez vous connecter.<br>Si ce réseau local représente un emplacement local, il s’agit de l’adresse IP publique du périphérique VPN que vous souhaitez vous connecter. Il ne peut pas être derrière le NAT et doit être accessible par Azure.<br>Si ce réseau local représente un autre VNet, vous spécifierez l’adresse IP publique qui a été affectée à la passerelle de réseau virtuel pour ce VNet.<br>

4. **Espace d’adressage** fait référence aux plages d’adresses pour le réseau qui représente de ce réseau local. Vous pouvez ajouter plusieurs plages d’adresses espace. Assurez-vous que les plages que vous spécifiez ici ne se chevauchent pas avec des plages des autres réseaux que vous souhaitez vous connecter.
 
5. Pour l' **abonnement**, vérifiez que l’abonnement approprié s’affiche.

6. Pour le **Groupe de ressources**, sélectionnez le groupe de ressources que vous souhaitez utiliser. Vous pouvez soit créer un nouveau groupe de ressources, ou sélectionnez-en un que vous avez déjà créé.

7. Pour l' **emplacement**, sélectionnez l’emplacement qui sera créé dans cet objet. Vous pouvez également sélectionner le même emplacement que votre VNet se trouve dans, mais vous n’êtes pas obligé de le faire.

8. Cliquez sur **créer** pour créer la passerelle du réseau local.
