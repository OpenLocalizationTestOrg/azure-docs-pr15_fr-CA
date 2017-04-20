1. Dans le portail, accédez à **Nouveau**. Tapez « Passerelle de réseau virtuel » dans la recherche. Recherchez de **passerelle de réseau virtuel** dans la recherche de retour et cliquez sur l’entrée. La lame de **passerelle de réseau virtuel créer** s’ouvre.
2. Cliquez sur **créer** en bas de la lame de **passerelle de réseau virtuel** . La lame de **passerelle de réseau virtuel créer** s’ouvre. Renseignez les valeurs de la passerelle réseau virtuel.

    ![Créer les champs de lame de passerelle de réseau virtuel] (./media/vpn-gateway-add-gw-rm-portal-include/createvnetgw300.png "Créer les champs de lame de passerelle de réseau virtuel")

3. **Nom**: nom de la passerelle. Ce n’est pas identique à la dénomination d’un sous-réseau de la passerelle. C’est le nom de l’objet passerelle que vous créez.

4. **Type de passerelle**: sélectionnez **VPN**. Les passerelles VPN utilisent le type de passerelle de réseau virtuel **VPN**. 

5. **Type de VPN**: sélectionnez le type VPN qui est spécifié pour votre configuration. La plupart des configurations nécessitent un type VPN basée sur un itinéraire.

6. **Référence**: sélectionnez la référence SKU de passerelle dans la liste déroulante. Les références répertoriées dans la liste déroulante varient selon le type VPN que vous sélectionnez.

7. **Emplacement**: ajuster le champ **emplacement** pour pointer vers l’emplacement où se trouve votre réseau virtuel.
 
8. Sélectionnez le réseau virtuel auquel vous souhaitez ajouter cette passerelle. Cliquez sur le **réseau virtuel** pour ouvrir la **fenêtre Choisir un réseau virtuel** de lame. Sélectionnez le VNet. Si vous ne voyez pas votre VNet, assurez-vous que le champ **d’emplacement** pointe vers la région dans lequel se trouve votre réseau virtuel.

9. Choisissez une adresse IP publique. Cliquez sur l' **adresse IP publique** pour ouvrir la lame de **l’adresse IP publique de choisir** . Cliquez sur **+ Créer nouveau** pour ouvrir la **créer lame d’adresse IP publique**. Entrez un nom pour votre adresse IP publique. Cette blade crée un objet d’adresse IP publique à laquelle une adresse IP publique sera attribuée dynamiquement.<br>Cliquez sur **OK** pour enregistrer vos modifications dans cette blade.

10. **Abonnement**: Vérifiez que l’abonnement approprié est sélectionné.

11. **Groupe de ressources**: ce paramètre est déterminé par le réseau virtuel que vous sélectionnez. 

12. Ajuster l' **emplacement** une fois que vous avez spécifié les paramètres précédents.

13. Vérifiez les paramètres. Vous pouvez sélectionner le **code confidentiel pour le tableau de bord** en bas de la lame si vous souhaitez que votre passerelle s’affiche sur le tableau de bord.

14. Cliquez sur **créer** pour commencer la création de la passerelle. Les paramètres seront validées et vous verrez la « passerelle virtuelle de déploiement réseau » de mosaïque sur le tableau de bord. Création d’une passerelle peut prendre jusqu'à 45 minutes. Vous devrez peut-être actualiser votre page de portail pour afficher l’état terminé.

    ![Déploiement virtuel passerelle de réseau] (./media/vpn-gateway-add-gw-rm-portal-include/deployvnetgw150.png "Déploiement virtuel passerelle de réseau")

11. Après la création de la passerelle, vous pouvez afficher l’adresse IP qui a été affectée en examinant le réseau virtuel du portail. La passerelle s’affiche sous la forme d’un périphérique connecté. Vous pouvez cliquer sur le périphérique connecté (votre passerelle réseau virtuel) pour afficher plus d’informations.



