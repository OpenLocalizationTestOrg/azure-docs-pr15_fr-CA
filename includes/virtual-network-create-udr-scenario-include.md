## <a name="scenario"></a>Scénario

Pour mieux illustrer comment créer UDRs, ce document utilisera le scénario ci-dessous.

![DESCRIPTION DE L’IMAGE](./media/virtual-network-create-udr-scenario-include/figure1.png)

Dans ce scénario vous créerez un UDR pour le *sous-réseau de fin avant* et un autre UDR pour le *sous-réseau de fin de sauvegarde* , comme décrit ci-dessous : 

- **UDR-frontal**. Le front-end UDR seront appliquées au sous-réseau *frontal* et contiennent une gamme :  
    - **RouteToBackend**. Cet itinéraire envoie tout le trafic sur le sous-réseau de back-end pour l’ordinateur virtuel **FW1** .
- **UDR-back-end**. Le back-end UDR seront appliquées au sous-réseau *back-end* et contenir une gamme : 
    - **RouteToFrontend**. Cet itinéraire envoie tout le trafic sur le sous-réseau de front-end pour l’ordinateur virtuel **FW1** .

La combinaison de ces itinéraires permet de garantir que tout trafic à destination d’un sous-réseau à un autre est acheminé vers l’ordinateur virtuel **FW1** , qui est utilisé en tant qu’appliance virtuelle. Vous devez également activer le transfert IP pour cet ordinateur virtuel, pour vérifier qu’il peut recevoir le trafic destiné aux autres machines virtuelles.
