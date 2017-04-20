<properties
    pageTitle="Configurer le protocole LDAP sécurisé (LDAPS) dans Azure AD Domain Services | Microsoft Azure"
    description="Configuration de LDAP sécurisé (LDAPS) pour un domaine géré de Services de domaine Active Directory de Azure"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/21/2016"
    ms.author="maheshu"/>

# <a name="configure-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>Configuration de LDAP sécurisé (LDAPS) pour un domaine géré de Services de domaine Active Directory de Azure
Cet article explique comment vous pouvez activer le protocole Lightweight Directory Access sécurisé (LDAPS) pour votre domaine géré les Services de domaine Active Directory de Azure. LDAP sécurisé est également connu sous le nom « LDAP Lightweight Directory Access Protocol () sur SSL Secure Sockets Layer () / Transport Layer Security (TLS) ».

## <a name="before-you-begin"></a>Avant de commencer
Pour effectuer les tâches répertoriées dans cet article, vous devez :

1. Un **abonnement Azure**valide.

2. Un **répertoire d’Azure AD** - soit synchronisé avec un répertoire local ou un répertoire de nuage uniquement.

3. **Les Services de domaine Active Directory de Azure** doit être activée pour le répertoire Active Directory Azure. Si vous n’avez pas fait, suivez toutes les tâches décrites dans le [guide Mise en route](./active-directory-ds-getting-started.md).

4. Un **certificat à utiliser pour activer le protocole LDAP sécurisé**.
    - **Recommandé** - obtenir un certificat de votre autorité de certification d’entreprise ou une autorité de certification publique. Cette option de configuration est plus sécurisée.
    - Alternativement, vous pouvez également choisir de [créer un certificat auto-signé](#task-1---obtain-a-certificate-for-secure-ldap) comme indiqué plus loin dans cet article.

<br>

### <a name="requirements-for-the-secure-ldap-certificate"></a>Configuration requise pour le certificat LDAP sécurisé
Acquérir un certificat valide par les indications suivantes, avant d’activer le protocole LDAP sécurisé. Vous rencontrez des erreurs si vous essayez d’activer le protocole LDAP sécurisé pour votre domaine géré avec un certificat incorrect/non valide.

1. **Émetteur de confiance** - le certificat doit être délivré par une autorité approuvée par les ordinateurs qui doivent se connecter au domaine à l’aide de LDAP sécurisé. Cette autorité peut être autorité de certification d’entreprise de votre organisation ou une autorité de certification publique approuvée par ces ordinateurs.

2. **Durée de vie** - le certificat doit être valide pour au moins les 3 à 6 prochains mois. Accès sécurisé de LDAP à votre domaine géré est interrompu lors de l’expiration du certificat.

3. **Nom du sujet** : le nom du sujet du certificat doit être un caractère générique pour votre domaine géré. Par exemple, si votre domaine est nommé « contoso100.com », nom du sujet du certificat doit être ' *. contoso100.com'. Définissez le nom DNS (autre nom du sujet) à ce nom générique.

3. **Utilisation de la clé** - le certificat doit être configuré pour l’exemple suivant utilise - signatures numériques et le chiffrement de la clé.

4. **Rôle du certificat** : le certificat doit être valide pour l’authentification de serveur SSL.

<br>

## <a name="task-1---obtain-a-certificate-for-secure-ldap"></a>Tâche 1 - obtenir un certificat pour le protocole LDAP sécurisé
La première tâche, vous devez obtenir un certificat utilisé pour l’accès LDAP sécurisé vers le domaine géré. Vous disposez de deux options :

- Obtenir un certificat auprès d’une autorité de certification. L’autorité peut être une autorité de certification ou de certification d’entreprise de votre organisation.

- Créer un certificat auto-signé.


### <a name="option-a-recommended---obtain-a-secure-ldap-certificate-from-a-certification-authority"></a>Option une (recommandé),-obtenir un certificat LDAP sécurisé à partir d’une autorité de certification
Si votre organisation déploie une infrastructure à clé publique (PKI) enterprise, vous devez obtenir un certificat auprès de l’autorité de certification d’entreprise (CA) pour votre organisation. Si votre organisation obtient ses certificats auprès d’une autorité de certification publique, vous devez obtenir le certificat LDAP sécurisé à partir de cette autorité de certification publique.

Lorsque vous demandez un certificat, veillez à respecter les exigences décrites dans la [spécification pour le certificat LDAP sécurisé](#requirements-for-the-secure-ldap-certificate).

> [AZURE.NOTE] Les ordinateurs clients qui doivent se connecter au domaine géré à l’aide de LDAP sécurisé doivent approuver l’émetteur du certificat LDAPS.


### <a name="option-b---create-a-self-signed-certificate-for-secure-ldap"></a>Option B - permet de créer un certificat auto-signé pour le protocole LDAP sécurisé
Vous pouvez choisir de créer un certificat auto-signé pour sécuriser LDAP, si :

- certificats de votre organisation ne sont pas émis par une autorité de certification d’entreprise ou
- Vous ne prévoyez pas d’utiliser un certificat auprès d’une autorité de certification publique.

**Créer un certificat auto-signé à l’aide de PowerShell**

Sur votre ordinateur Windows, ouvrez une nouvelle fenêtre PowerShell en tant **qu’administrateur** et tapez les commandes suivantes, pour créer un nouveau certificat auto-signé.

    $lifetime=Get-Date

    New-SelfSignedCertificate -Subject *.contoso100.com -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment -Type SSLServerAuthentication -DnsName *.contoso100.com

Dans l’exemple précédent, remplacez « contoso100.com » avec le nom de domaine DNS de votre domaine géré les Services de domaine Active Directory de Azure.

![Sélectionnez répertoire de publicité Azure](./media/active-directory-domain-services-admin-guide/secure-ldap-powershell-create-self-signed-cert.png)

Le certificat auto-signé nouvellement créé est placé dans le magasin de certificats de l’ordinateur local.


## <a name="task-2---export-the-secure-ldap-certificate-to-a-pfx-file"></a>Tâche 2 : exporter le protocole LDAP sécurisé de certificats à un. Fichier PFX
Avant de commencer cette tâche, assurez-vous que vous avez obtenu le certificat LDAP sécurisé à partir de votre autorité de certification d’entreprise ou une autorité de certification publique ou avez créé un certificat auto-signé.

Procédez comme suit pour exporter le certificat LDAPS à un. Fichiers PFX.

1. Appuyez sur le bouton **Démarrer** et tapez **R**. Dans la boîte de dialogue **exécuter** , tapez **mmc** et cliquez sur **OK**.

    ![Lancement de la console MMC](./media/active-directory-domain-services-admin-guide/secure-ldap-start-run.png)

2. À l’invite du **Contrôle de compte d’utilisateur** , cliquez sur **Oui** pour lancer la console MMC (Microsoft Management Console) en tant qu’administrateur.

3. Dans le menu **fichier** , cliquez sur **Add/Remove Snap-in...**.

    ![Ajouter le composant logiciel enfichable à la console MMC](./media/active-directory-domain-services-admin-guide/secure-ldap-add-snapin.png)

4. Dans la boîte de dialogue **Ajouter ou supprimer des composants** , sélectionnez le composant logiciel enfichable **certificats** , cliquez sur le **Ajouter >** bouton.

    ![Ajouter le composant logiciel enfichable Certificats à une console MMC](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-snapin.png)

5. Dans l’Assistant **composant logiciel enfichable Certificats** , sélectionnez **compte d’ordinateur** et cliquez sur **suivant**.

    ![Ajouter le composant logiciel enfichable Certificats pour un compte d’ordinateur](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-computer-account.png)

6. Dans la page **Sélectionner un ordinateur** , sélectionnez **ordinateur Local : (l’ordinateur cette console s’exécute)** et cliquez sur **Terminer**.

    ![Ajouter enfichable Certificats - sélectionner un ordinateur](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-local-computer.png)

7. Dans la boîte de dialogue **Ajouter ou supprimer des composants** , cliquez sur **OK** pour ajouter le composant logiciel enfichable Certificats à la console MMC.

    ![Ajouter le composant logiciel enfichable Certificats à la console MMC - effectué](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-snapin-done.png)

8. Dans la fenêtre de la console MMC, développez **Racine de la Console**. Vous devez voir le composant logiciel enfichable Certificats chargé. Cliquez sur **certificats (ordinateur Local)** pour le développer. Cliquez pour développer le nœud **personnel** , suivi par le nœud **certificats** .

    ![Magasin de certificats de personnels ouverts](./media/active-directory-domain-services-admin-guide/secure-ldap-open-personal-store.png)

9. Vous devez voir le certificat auto-signé, que nous avons créé. Vous pouvez examiner les propriétés du certificat pour garantir l’empreinte numérique correspond à celle signalée sur windows PowerShell, lors de la création du certificat.

10. Sélectionnez le certificat auto-signé et le **clic droit**. Dans le menu contextuel, sélectionnez **Toutes les tâches** et sélectionnez **Exporter**.

    ![Certificat d’exportation](./media/active-directory-domain-services-admin-guide/secure-ldap-export-cert.png)

11. Dans l' **Assistant Exportation de certificat**, cliquez sur **suivant**.

    ![Assistant Exportation de certificat](./media/active-directory-domain-services-admin-guide/secure-ldap-export-cert-wizard.png)

12. Dans la page **Exportation de clé privée** , sélectionnez **Oui, exporter la clé privée**, puis cliquez sur **suivant**.

    ![Exporter la clé privée.](./media/active-directory-domain-services-admin-guide/secure-ldap-export-private-key.png)

    > [AZURE.WARNING] Vous devez exporter la clé privée avec le certificat. Si vous fournissez un PFX qui ne contient-elle pas la clé privée pour le certificat, l’activation du service LDAP sécurisé pour votre domaine géré échoue.

13. Dans la page **Format de fichier d’exportation** , sélectionnez **d’échange d’informations personnelles - PKCS #12 (. PFX)** en tant que format de fichier pour le certificat exporté.

    ![Format de fichier de certificat d’exportation](./media/active-directory-domain-services-admin-guide/secure-ldap-export-to-pfx.png)

    > [AZURE.NOTE] Uniquement le. Format de fichier PFX est pris en charge. Ne pas exporter le certificat à la. Format de fichier de région d’exécution limitée.

14. Dans la page **sécurité** , sélectionnez l’option de **mot de passe** et d’un type dans un mot de passe pour protéger le. Fichiers PFX. N’oubliez pas ce mot de passe dans la mesure où il sera nécessaire dans la tâche suivante. Cliquez sur **suivant** pour continuer.

    ![Mot de passe pour l’exportation du certificat ](./media/active-directory-domain-services-admin-guide/secure-ldap-export-select-password.png)

    > [AZURE.NOTE] Prenez note de ce mot de passe. Vous avez besoin lors de l’activation du service LDAP sécurisé pour ce domaine géré dans la [tâche 3 - activer le protocole LDAP sécurisé pour le domaine géré](#task-3---enable-secure-ldap-for-the-managed-domain)

15. Dans la page **fichier à exporter** , spécifiez le nom de fichier et l’emplacement où vous souhaitez exporter le certificat.

    ![Chemin d’accès pour l’exportation du certificat](./media/active-directory-domain-services-admin-guide/secure-ldap-export-select-path.png)

16. Sur la page suivante, cliquez sur **Terminer** pour exporter le certificat vers un fichier PFX. Vous devriez voir le message de confirmation lorsque le certificat a été exporté.

    ![Certificat d’exportation effectuée](./media/active-directory-domain-services-admin-guide/secure-ldap-exported-as-pfx.png)


## <a name="task-3---enable-secure-ldap-for-the-managed-domain"></a>Tâche 3 - activer le LDAP sécurisé pour le domaine géré
Pour activer le protocole LDAP sécurisé, effectuez les étapes de configuration suivantes :

1. Accédez au **[portail de classique Azure](https://manage.windowsazure.com)**.

2. Sélectionnez le nœud **Active Directory** dans le volet gauche.

3. Sélectionnez le répertoire Azure AD (également appelé « clients »), pour lequel vous avez activé les Services de domaine Active Directory de Azure.

    ![Sélectionnez répertoire de publicité Azure](./media/active-directory-domain-services-getting-started/select-aad-directory.png)

4. Cliquez sur l’onglet **configurer** .

    ![Configurer l’onglet du répertoire](./media/active-directory-domain-services-getting-started/configure-tab.png)

5. Faites défiler jusqu'à la section intitulée des **services de domaine**. Vous devriez voir une option intitulée **LDAP sécurisé (LDAPS)** , comme illustré dans la capture d’écran suivante :

    ![Section de configuration des Services de domaine](./media/active-directory-domain-services-admin-guide/secure-ldap-start.png)

6. Cliquez sur le bouton **certificat de configurer...** pour afficher la boîte de dialogue **Configurer un certificat pour le protocole LDAP sécurisé** .

    ![Configurer un certificat pour le protocole LDAP sécurisé](./media/active-directory-domain-services-admin-guide/secure-ldap-configure-cert-page.png)

7. Cliquez sur l’icône de dossier ci-dessous **PFX fichier par certificat** pour spécifier le fichier PFX qui contient le certificat que vous souhaitez utiliser pour l’accès LDAP sécurisé vers le domaine géré. Permet également d’entrer le mot de passe que vous avez spécifié lors de l’exportation du certificat vers le fichier PFX. Puis, cliquez sur le bouton terminé en bas.

    ![Spécifier le mot de passe et sécurisé des fichiers LDAP PFX](./media/active-directory-domain-services-admin-guide/secure-ldap-specify-pfx.png)

8. La section de l’onglet **configurer** **les services de domaine** doit obtenir grisée et se trouve dans le **en attente...** état pendant quelques minutes. Au cours de cette période, le certificat LDAPS est vérifié pour précision et LDAP sécurisé est configuré pour votre domaine géré.

    ![Sécuriser LDAP - état d’attente](./media/active-directory-domain-services-admin-guide/secure-ldap-pending-state.png)

    > [AZURE.NOTE] Il prend environ 10 à 15 minutes pour activer le protocole LDAP sécurisé pour votre domaine géré. Si le certificat LDAP sécurisé fourni ne correspond pas les critères requis, LDAP sécurisé n’est pas activée pour votre répertoire et vous voyez une défaillance. Par exemple, le nom de domaine est incorrect, le certificat est expiré ou expire bientôt etc..

9. Lorsque le protocole LDAP sécurisé est activée pour votre domaine géré, le **en attente...** message doit disparaître. Vous devez voir l’empreinte numérique du certificat s’affiche.

    ![LDAP sécurisé activé](./media/active-directory-domain-services-admin-guide/secure-ldap-enabled.png)

<br>


## <a name="task-4---enable-secure-ldap-access-over-the-internet"></a>Tâche 4 : activer l’accès LDAP sécurisé sur internet
**Tâche facultative** - si vous ne prévoyez pas d’accéder au domaine géré à l’aide du protocole LDAPS via internet, ignorez cette tâche de configuration.

Avant de commencer cette tâche, assurez-vous que vous avez terminé les étapes décrites dans la [tâche 3](#task-3---enable-secure-ldap-for-the-managed-domain).

1. Vous devriez voir une option permettant d' **Activer SECURE LDAP ACCESS sur INTERNET** , dans la section de la page **configurer** **les services de domaine** . Dans la mesure où l’accès internet pour le domaine géré sur le protocole LDAP sécurisé est désactivé par défaut, cette option est définie sur **non** par défaut.

    ![LDAP sécurisé activé](./media/active-directory-domain-services-admin-guide/secure-ldap-enabled2.png)

2. Activer/désactiver **Activer l’accès LDAP sécurisé via l’INTERNET** à **Oui**. Cliquez sur le bouton **Enregistrer** dans le panneau inférieur.
    ![LDAP sécurisé activé](./media/active-directory-domain-services-admin-guide/secure-ldap-enable-internet-access.png)

3. La section de l’onglet **configurer** **les services de domaine** doit obtenir grisée et se trouve dans le **en attente...** état pendant quelques minutes. Après un certain temps, l’accès à internet pour votre domaine géré sur le protocole LDAP sécurisé est activé.

    ![Sécuriser LDAP - état d’attente](./media/active-directory-domain-services-admin-guide/secure-ldap-enable-internet-access-pending-state.png)

    > [AZURE.NOTE] Il prend environ 10 minutes pour permettre l’accès à internet via le protocole LDAP sécurisé pour votre domaine géré.

4. Lors de l’accès LDAP sécurisé à votre domaine géré via internet est activé, le **en attente...** message doit disparaître. Vous devez voir l’adresse IP externe qui peut être utilisé pour accéder à votre répertoire sur LDAPS dans le champ **Adresse IP externe pour LDAPS accès**.

    ![LDAP sécurisé activé](./media/active-directory-domain-services-admin-guide/secure-ldap-internet-access-enabled.png)

<br>

## <a name="task-5---configure-dns-to-access-the-managed-domain-from-the-internet"></a>Tâche 5 : configuration de DNS pour accéder au domaine de managé à partir d’internet
**Tâche facultative** - si vous ne prévoyez pas d’accéder au domaine géré à l’aide du protocole LDAPS via internet, ignorez cette tâche de configuration.

Avant de commencer cette tâche, assurez-vous que vous avez terminé les étapes décrites dans la [tâche 4](#task-4---enable-secure-ldap-access-over-the-internet).

Une fois que vous avez activé l’accès LDAP sécurisé sur internet pour votre domaine géré, vous devez mettre à jour DNS afin que les ordinateurs clients peuvent rechercher ce domaine géré. À la fin de la tâche 4, une adresse IP externe est affichée sur l’onglet **configurer** dans **l’Adresse IP externe pour LDAPS accès**.

Configurez votre fournisseur DNS externe afin que le nom DNS du domaine géré (par exemple, ' contoso100.com') pointe vers cette adresse IP externe. Dans notre exemple, nous devons créer l’entrée DNS suivante :

    contoso100.com  -> 52.165.38.113

Voilà, vous êtes maintenant prêt à vous connecter au domaine géré à l’aide de LDAP sécurisé via internet.

> [AZURE.WARNING] N’oubliez pas que les ordinateurs clients doivent approuver l’émetteur du certificat LDAPS pour pouvoir vous connecter correctement au domaine géré à l’aide du protocole LDAPS. Si vous utilisez une autorité de certification d’entreprise ou une autorité de certification publique approuvée, vous n’avez pas besoin de rien à faire dans la mesure où ces émetteurs de certificats de confiance des ordinateurs clients. Si vous utilisez un certificat auto-signé, vous devez installer la partie publique du certificat auto-signé dans le magasin de certificats de confiance sur l’ordinateur client.

<br>

## <a name="related-content"></a>Contenu associé

- [Administrer un domaine géré de Services de domaine Active Directory de Azure](active-directory-ds-admin-guide-administer-domain.md)
