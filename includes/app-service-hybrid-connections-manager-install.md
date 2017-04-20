
1. De la lame **les connexions hybride** , cliquez sur la connexion hybride que vous venez de créer, puis cliquez sur **Configuration de l’écouteur**.
    
    ![Cliquez sur Configuration de l’écouteur](./media/app-service-hybrid-connections-manager-install/D04ClickListenerSetup.png)
    
4. La lame de **Propriétés de la connexion hybride** s’ouvre. Sous **Local hybride Connection Manager**, choisissez **télécharger et configurer manuellement**, enregistrer le package téléchargé HybridConnectionManager.msi et copier la chaîne de connexion de passerelle.
    
    ![Cliquez ici pour installer](./media/app-service-hybrid-connections-manager-install/D05ClickToInstallHCM.png)
    
5. À partir d’une invite de commandes administrateur, tapez la commande suivante pour démarrer le programme d’installation :

        start HybridConnectionManager.msi
 
7. Une fois le programme d’installation s’exécute, cliquez sur **pas maintenant**, puis naviguez jusqu’au dossier %ProgramFiles%\Microsoft\HybridConnectionManager, exécutez HCMConfigWizard.exe et cliquez sur **Oui** dans la boîte de dialogue **Contrôle de compte d’utilisateur** .
        
7. Collez la chaîne de connexion hybride copiés précédemment et cliquez sur **OK**. 
    
    ![L’installation de](./media/app-service-hybrid-connections-manager-install/D08aHCMInstallManual.png)
    
8. Une fois l’installation terminée, cliquez sur **Fermer**.
    
    ![Cliquez sur Fermer](./media/app-service-hybrid-connections-manager-install/D09HCMInstallComplete.png)
    
    Sur la lame de **connexions d’hybrides** , la colonne **état** indique maintenant **connecté**. 
    
    ![État connecté](./media/app-service-hybrid-connections-manager-install/D10HCStatusConnected.png)