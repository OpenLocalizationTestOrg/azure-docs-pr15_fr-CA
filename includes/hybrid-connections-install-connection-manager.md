
Le Gestionnaire de connexion hybride permet à votre ordinateur local pour vous connecter à Azure et relayer le trafic TCP. Vous devez installer le gestionnaire sur un ordinateur local qui peut se connecter à la votre instance de SQL Server.

1. La connexion que vous venez de créer doit avoir un **statut** **de premesis d’installation incomplète**. Cliquez sur cette connexion, puis cliquez sur **le programme d’installation sur site**.

    ![Programme d’installation sur site](./media/hybrid-connections-install-connection-manager/5-1.png)

2. Cliquez sur **installer et configurer**.

    Cette procédure installe une instance personnalisée du Gestionnaire de connexion, qui est déjà préconfiguré pour fonctionner avec la connexion hybride que vous venez de créer.

3. Remplissez le reste des étapes d’installation de Connection Manager.

    Une fois l’installation terminée, l’état de connexion hybride devient **1 Instance connecté**. Vous devrez peut-être actualiser le navigateur et attendez quelques minutes. 

Le programme d’installation de connexion hybride est maintenant terminée.