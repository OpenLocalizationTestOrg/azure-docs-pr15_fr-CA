## <a name="how-to-create-a-vnet-in-the-azure-portal"></a>Comment créer un VNet dans le portail Azure

Pour créer un VNet en fonction du scénario ci-dessus, suivez les étapes ci-dessous.

1. À partir d’un navigateur, accédez à http://manage.windowsazure.com et, si nécessaire, connectez-vous à votre compte Azure.
2. Cliquez sur **Nouveau** > **SERVICES réseau** > **Réseau virtuel** > **Créer de personnalisé** comme illustré dans la figure ci-dessous.

    ![Créer des VNet de portail](./media/virtual-networks-create-vnet-classic-portal-include/vnet-create-portal-figure1.gif)

3. Sur la page de **Détails de réseau virtuel** , tapez le **nom** de la VNet et sélectionnez son **emplacement**puis cliquez sur la flèche dans le coin inférieur droit de la page pour passer à l’étape 2. La figure ci-dessous illustre les paramètres de notre scénario.

    ![Page de détails de réseau virtuel](./media/virtual-networks-create-vnet-classic-portal-include/vnet-create-portal-figure2.png)

4. Dans la page **serveurs DNS et la connectivité VPN** , spécifiez le nom et l’adresse IP à 9 serveurs DNS à utiliser. Si vous ne spécifiez pas un serveur DNS, votre VNet utilise la résolution interne d’attribution de noms fournie par Azure. Pour notre scénario, nous n'allons pas configurer des serveurs DNS.
5. Si vous souhaitez fournir l’accès VPN site-à-point pour votre VNet, activez la case à cocher **configurer un VPN site-à-point** . Si vous ne configurez pas un VPN site-à-point, vous pouvez l’ajouter à votre VNet à tout moment après la création. Pour notre scénario, nous n'allons pas configurer un réseau privé virtuel site-à-point.
6. Si vous souhaitez fournir la connectivité VPN de site à site entre votre VNet et de VNet un autre ou de votre réseau local, activez la case à cocher **configurer un VPN de site à site** et spécifier si vous souhaitez utiliser **ExpressRoute** ou note et le nom du réseau pour vous connecter à. Si vous ne configurez pas un VPN de site à site, vous pouvez l’ajouter à votre VNet à tout moment après la création. Pour notre scénario, nous n'allons pas configurer un VPN de site à site.
7. Cliquez sur la flèche dans le coin inférieur droit de la page pour passer à 3 étape que la figure ci-dessous illustre les paramètres de notre scénario.

    ![Page de connexion VPN et les serveurs DNS](./media/virtual-networks-create-vnet-classic-portal-include/vnet-create-portal-figure3.png)

8. Dans la page des **Espaces d’adressage de réseau virtuel** , sous **IP de début**, cliquez sur *10.0.0.0* pour modifier l’espace d’adressage VNet et tapez l’espace d’adressage que vous souhaitez utiliser en début. Pour notre scénario, tapez *192.168.0.0*. 
9. Sous **CIDR (nombre d’adresses)** , sélectionnez le nombre de bits du masque de sous-réseau. Pour notre scénario, sélectionnez *16 (65536)*.
10. Sous **sous-réseaux**, cliquez sur le *sous-réseau 1* et renommez le sous-réseau si nécessaire. Pour notre scénario, vous devez le renommer en *frontal*.

    >[AZURE.NOTE] Si vous cliquez en dehors de la zone de texte pour un sous-réseau vous ne serez pas en mesure de modifier le nom si le sous-réseau à nouveau. Pour corriger cette erreur, vous devez supprimer le sous-réseau en cliquant sur le bouton X à sa droite, puis ajouter un nouveau sous-réseau comme décrit à l’étape 13 ci-dessous.

11. Sous le premier sous-réseau **IP de début** , spécifiez l’adresse IP de début pour le sous-réseau. Pour notre scénario, tapez *192.168.1.0*.
12. Sous **CIDR (nombre d’adresses)** , sélectionnez le nombre de bits du masque de sous-réseau pour le sous-réseau du premier. Pour notre scénario, sélectionnez *24 (256)*.
13. Cliquez sur **Ajouter le sous-réseau** pour ajouter un nouveau sous-réseau, si nécessaire. Pour notre scénario, ajouter un sous-réseau et répétez les étapes 10 à 12 pour configurer le VNet, comme illustré dans la figure ci-dessous.

    ![Page d’espaces d’adresse de réseau virtuel](./media/virtual-networks-create-vnet-classic-portal-include/vnet-create-portal-figure4.png)

14. Cliquez sur le bouton de coche dans le coin inférieur droit de la page pour créer la VNet. Après quelques secondes votre VNet apparaît dans la liste des VNets disponibles, comme indiqué dans la figure ci-dessous.

    ![Nouveau réseau virtuel](./media/virtual-networks-create-vnet-classic-portal-include/vnet-create-portal-figure5.png)