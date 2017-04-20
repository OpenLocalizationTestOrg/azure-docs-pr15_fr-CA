1. Recherchez votre passerelle de réseau virtuel et cliquez sur **tous les paramètres** pour ouvrir la lame de **paramètres** .

2. Sur la lame de **paramètres** , cliquez sur **connexions**, puis cliquez sur **Ajouter** dans la partie supérieure de la lame pour ouvrir la blade **d’Ajouter une connexion** .

    ![Créer la connexion de Site à Site](./media/vpn-gateway-add-site-to-site-connection-rm-portal-include/addconnection250.png)

3. Sur la blade **d’Ajouter une connexion** , le **nom de** votre connexion. 

4. Pour le **type de connexion**, sélectionnez **Site-to-site(IPSec)**.

5. Pour une **passerelle de réseau virtuel**, la valeur est fixe parce que vous vous connectez à partir de cette passerelle.

6. Pour la **passerelle du réseau Local**, cliquez sur **Choisir une passerelle du réseau local** et sélectionnez la passerelle du réseau local que vous souhaitez utiliser. 

7. Pour la **Clé partagée**, la valeur doit correspondre à la valeur que vous utilisez pour votre périphérique VPN local. Si votre périphérique VPN sur votre réseau local ne fournit pas une clé partagée, vous pouvez en créer un et il entrée ici et sur votre périphérique local. L’important est que les deux correspondent.

8. Les valeurs restantes pour **l’abonnement**, **Groupe de ressources**et **l’emplacement** sont fixes.

9. Cliquez sur **OK** pour créer la connexion. Vous voyez clignoter sur l’écran *Création d’une connexion* .

10. Lorsque la connexion est terminée, vous le verrez apparaissent dans la lame de **connexions** pour votre passerelle.

    ![Créer la connexion de Site à Site](./media/vpn-gateway-add-site-to-site-connection-rm-portal-include/connectionstatus450.png)

