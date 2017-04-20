## <a name="scenario"></a>Scénario

Pour mieux illustrer comment créer NSGs, ce document utilisera le scénario ci-dessous.

![Scénario de VNet](./media/virtual-networks-create-nsg-scenario-include/figure1.png)

Dans ce scénario, vous allez créer un NSG pour chaque sous-réseau du réseau virtuel **TestVNet** , comme indiqué ci-dessous : 

- **NSG-frontal**. Le front-end NSG seront appliquées au sous-réseau *frontal* et contiennent deux règles :  
    - **règle de rdp**. Cette règle autorise le trafic RDP du sous-réseau *frontal* .
    - **règle de web**. Cette règle autorise le trafic du sous-réseau *frontal* HTTP.
- **NSG-back-end**. Le back-end NSG seront appliquées au sous-réseau *back-end* et contiennent deux règles : 
    - **règle de sql**. Cette règle autorise le trafic SQL uniquement à partir du sous-réseau *frontal* .
    - **règle de web**. Cette règle refuse liés à internet tout le trafic du sous-réseau *back-end* .

La combinaison de ces règles de créer un scénario de type DMZ, où le sous-réseau back-end peut recevoir uniquement le trafic entrant pour SQL à partir du sous-réseau du front-end et n’a pas accès à Internet, alors que le sous-réseau de front-end peut communiquer avec Internet et recevoir les requêtes HTTP entrantes.
 
