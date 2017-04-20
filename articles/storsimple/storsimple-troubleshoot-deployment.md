<properties 
   pageTitle="Résoudre les problèmes de déploiement StorSimple | Microsoft Azure"
   description="Décrit comment diagnostiquer et résoudre les erreurs qui se produisent lors du premier déploiement de StorSimple."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="08/18/2016"
   ms.author="alkohli" />

# <a name="troubleshoot-storsimple-device-deployment-issues"></a>Résoudre les problèmes de déploiement de périphérique StorSimple

## <a name="overview"></a>Vue d’ensemble

Cet article fournit des conseils de dépannage pour votre déploiement de Microsoft Azure StorSimple. Il décrit les problèmes courants, les causes et les étapes recommandées pour vous aider à résoudre les problèmes que vous pouvez rencontrer lors de la configuration StorSimple. Ces informations s’appliquent à la fois le périphérique physique sur site de StorSimple et le périphérique virtuel StorSimple.

> [AZURE.NOTE] Problèmes liés à la configuration de périphériques que vous pouvez rencontrer peuvent se produire lorsque vous déployez le périphérique pour la première fois, ou ils peuvent se produire ultérieurement, lorsque le périphérique est opérationnel. Cet article se concentre sur la résolution des problèmes de déploiement de la première fois. Pour résoudre les problèmes liés à un dispositif opérationnel, accédez à [résoudre les problèmes de périphérique opérationnel](storsimple-troubleshoot-operational-device.md).

Cet article décrit les outils de résolution des problèmes de déploiement de StorSimple également et fournit un exemple pas à pas de dépannage.

## <a name="first-time-deployment-issues"></a>Problèmes de déploiement de la première fois

Si vous rencontrez un problème lors du déploiement de votre périphérique pour la première fois, considérez les points suivants :

- Si vous dépannez un périphérique physique, assurez-vous que le matériel a été installé et configuré comme décrit dans [installer votre périphérique StorSimple 8100](storsimple-8100-hardware-installation.md) ou [votre périphérique StorSimple 8600](storsimple-8600-hardware-installation.md).
- Vérifiez les conditions préalables pour le déploiement. Assurez-vous que vous disposez de toutes les informations décrites dans l' [aide-mémoire de configuration de déploiement](storsimple-deployment-walkthrough.md#deployment-configuration-checklist).
- Passez en revue les Notes de version StorSimple pour voir si le problème est décrit. Les notes de version comprennent des solutions de contournement concernant les problèmes d’installation connus. 

Lors du déploiement du périphérique, les plus courants problèmes que les utilisateurs face se produisent lorsqu’ils exécutent l’Assistant d’installation et lorsqu’ils s’inscrivent le périphérique par le biais de Windows PowerShell pour StorSimple. (Vous utilisez Windows PowerShell pour StorSimple pour inscrire et configurer votre périphérique StorSimple. Pour plus d’informations sur l’inscription de périphérique, consultez [étape 3 : configurer et inscrire votre périphérique par le biais de Windows PowerShell pour StorSimple](storsimple-deployment-walkthrough.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple)).

Les sections suivantes peuvent vous aider à résoudre les problèmes que vous rencontrez lorsque vous configurez le périphérique StorSimple pour la première fois.

## <a name="first-time-setup-wizard-process"></a>Processus de l’Assistant Configuration initiale

Les étapes suivantes résument le processus de l’Assistant d’installation. Pour plus d’informations de configuration détaillées, voir [déploiement votre périphérique de StorSimple local](storsimple-deployment-walkthrough.md).

1. Exécutez l’applet de commande [Invoke-HcsSetupWizard](https://technet.microsoft.com/library/dn688135.aspx) pour démarrer l’Assistant d’installation qui vous guidera dans les étapes restantes. 
2. Configuration du réseau : l’Assistant d’installation vous permet de configurer les paramètres réseau de l’interface réseau 0 des données sur le périphérique StorSimple. Ces paramètres sont les suivants :
  - Virtuel IP (VIP), le masque de sous-réseau et la passerelle – l’applet de commande [Set-HcsNetInterface](https://technet.microsoft.com/library/dn688161.aspx) est exécutée en arrière-plan. Il configure l’adresse IP, masque de sous-réseau et passerelle pour l’interface réseau 0 de données sur le périphérique StorSimple.
  - Serveur DNS principal – l’applet de commande [Set-HcsDnsClientServerAddress](https://technet.microsoft.com/library/dn688172.aspx) est exécutée en arrière-plan. Il configure les paramètres DNS de la solution StorSimple.
  - Serveur NTP à l’applet de commande [Set-HcsNtpClientServerAddress](https://technet.microsoft.com/library/dn688138.aspx) est exécutée en arrière-plan. Il configure les paramètres du serveur NTP pour votre solution de StorSimple.
  - Proxy web en option – l’applet de commande [Set-HcsWebProxy](https://technet.microsoft.com/library/dn688154.aspx) est exécutée en arrière-plan. Il définit et permet la configuration de proxy web de la solution StorSimple.
3. Définir les mots de passe : l’étape suivante consiste à configurer les administrateur de périphérique et les mots de passe de gestionnaire d’instantanés StorSimple. Si vous exécutez la mise à jour 1, puis vous pas devront définir le mot de passe StorSimple Gestionnaire de snapshots.
  - Le mot de passe administrateur est utilisé pour vous connecter à votre périphérique. Le mot de passe de périphérique par défaut est le **mot de passe1**.
  - Le mot de passe du Gestionnaire de snapshots StorSimple est requis lorsque vous configurez un périphérique pour utiliser le Gestionnaire de snapshots de StorSimple. Vous devez d’abord définir le mot de passe dans l’Assistant d’installation, puis vous pouvez définir et modifier à partir du service Gestionnaire de StorSimple. Ce mot de passe authentifie le périphérique avec le Gestionnaire de capture instantanée StorSimple.
 
    > [AZURE.IMPORTANT] Les mots de passe sont recueillies avant l’enregistrement, mais appliquées uniquement après que vous inscrivez correctement l’appareil. En cas de panne d’appliquer un mot de passe, vous êtes invité à fournir le mot de passe à nouveau, jusqu'à ce que les mots de passe requis (répondant aux exigences de complexité) sont collectées.

4. Inscrire l’appareil : la dernière étape consiste à enregistrer le périphérique avec le service de gestionnaire de StorSimple en cours d’exécution dans Microsoft Azure. L’inscription vous devez [obtenir la clé d’inscription du service](storsimple-manage-service.md#get-the-service-registration-key) à partir du portail classique Azure et lui fournit dans l’Assistant d’installation. Une fois que le périphérique est correctement enregistré, une clé de cryptage de données de service est fournie pour vous. Veillez à conserver cette clé de cryptage dans un endroit sûr, car il vous sera nécessaire pour inscrire tous les périphériques suivants auprès du service.

## <a name="common-errors-during-device-deployment"></a>Erreurs courantes lors du déploiement du périphérique

Les tableaux suivants répertorient les erreurs courantes que vous pouvez rencontrer lorsque vous :

- Configurer les paramètres réseau requis.
- Configurer les paramètres de proxy web en option.
- Configurer l’administrateur de périphérique et les mots de passe de gestionnaire d’instantanés StorSimple. 
- Inscrire l’appareil. 

## <a name="errors-during-the-required-network-settings"></a>Erreurs pendant les paramètres réseau requis

| N°| Message d’erreur | Causes possibles | Action recommandée |
| ---| ------------- | --------------- | ------------------ |
| 1  | Invoke-HcsSetupWizard : Cette commande ne peut être exécutée sur le contrôleur actif. | Configuration a été effectuée sur le contrôleur passif.| Exécutez la commande suivante à partir du contrôleur actif. Pour plus d’informations, reportez-vous à la section [identifier un contrôleur active sur le périphérique](storsimple-controller-replacement.md#identify-the-active-controller-on-your-device).|
| 2 | Invoke-HcsSetupWizard : Dispositif n’est pas prêt. | Il existe des problèmes liés à la connectivité de réseau de données 0.| Vérifiez la connectivité réseau physique de données 0.|
| 3 | Invoke-HcsSetupWizard : Il existe un conflit d’adresse IP avec un autre système sur le réseau (Exception à partir de HRESULT : 0x80070263). | L’adresse IP fourni pour données 0 était déjà en cours d’utilisation par un autre système. | Fournissez une nouvelle adresse IP qui n’est pas en cours d’utilisation.|
| 4 | Invoke-HcsSetupWizard : Une ressource de cluster a échoué. (Exception à partir de HRESULT : 0x800713AE). | VIP en double. L’adresse IP fourni est déjà en cours d’utilisation.| Fournissez une nouvelle adresse IP qui n’est pas en cours d’utilisation.|
| 5 | Invoke-HcsSetupWizard : Adresse IPv4 non valide. | L’adresse IP est fournie dans un format incorrect.| Vérifiez le format et fournir de nouveau votre adresse IP. Pour plus d’informations, reportez-vous à [Adressage Ipv4][1]. |
| 6 | Invoke-HcsSetupWizard : Adresse IPv6 non valide. | L’adresse IP est fournie dans un format incorrect.| Vérifiez le format et fournir de nouveau votre adresse IP. Pour plus d’informations, reportez-vous à la section [Ipv6 Addressing][2].|
| 7 | Invoke-HcsSetupWizard : Aucun point de terminaison plus ne sont disponibles dans le mappeur de point de terminaison. (Exception à partir de HRESULT : 0x800706D9) | La fonctionnalité de cluster ne fonctionne pas. | [Contacter le support technique Microsoft](storsimple-contact-microsoft-support.md) pour les étapes suivantes.

## <a name="errors-during-the-optional-web-proxy-settings"></a>Erreurs pendant les paramètres de proxy web en option

| N°| Message d’erreur | Causes possibles | Action recommandée |
| ---| ------------- | --------------- | ------------------ |
| 1  | Appel de code non-HcsSetupWizard : Paramètre non valide (Exception à partir de HRESULT : 0 x 80070057) | L’un des paramètres fournis pour les paramètres de proxy n’est pas valide.| L’URI n’est pas fourni dans le format correct. Utilisez le format suivant : http://*<IP address or FQDN of the web proxy server>*:*<TCP port number>* |
| 2 | Invoke-HcsSetupWizard : Serveur RPC n’est pas disponible (Exception à partir de HRESULT : 0x800706ba) | La cause est une des opérations suivantes :<ol><li>Le cluster n’est pas disponible.</li><li>Le contrôleur passif ne peut pas communiquer avec le contrôleur actif, et la commande est exécutée à partir de contrôleur passif.</li></ol> | Selon la cause :<ol><li>[Contacter le support technique Microsoft](storsimple-contact-microsoft-support.md) pour vous assurer que le cluster est disponible.</li><li>Exécutez la commande à partir du contrôleur actif. Si vous souhaitez exécuter la commande à partir du contrôleur passif, vous devez vous assurer que le contrôleur passif peut communiquer avec le contrôleur actif. Vous devez contacter le [Support technique de Microsoft](storsimple-contact-microsoft-support.md) si cette connexion est interrompue.</li></ol> |
| 3 | Invoke-HcsSetupWizard : Échec de l’appel RPC (Exception à partir de HRESULT : 0x800706be) | Cluster est arrêté. | [Contacter le support technique Microsoft](storsimple-contact-microsoft-support.md) pour vous assurer que le cluster est disponible.|
| 4 | Invoke-HcsSetupWizard : Ne pas trouvée la ressource de Cluster (Exception à partir de HRESULT : 0x8007138f) | La ressource de cluster n’est pas trouvée. Cela peut se produire lors de l’installation n’était pas correcte. | Vous devrez peut-être réinitialiser le périphérique aux paramètres d’usine par défaut. [Contacter le support technique Microsoft](storsimple-contact-microsoft-support.md) pour créer une ressource de cluster.|
| 5 | Invoke-HcsSetupWizard : Pas en ligne la ressource de Cluster (Exception à partir de HRESULT : 0x8007138c)| Les ressources de cluster ne sont pas en ligne. | [Contacter le support technique Microsoft](storsimple-contact-microsoft-support.md) pour les étapes suivantes.|

## <a name="errors-related-to-device-administrator-and-storsimple-snapshot-manager-passwords"></a>Erreurs liées à l’administrateur de périphérique et les mots de passe de gestionnaire de snapshots de StorSimple

Le mot de passe administrateur de périphérique par défaut est le **mot de passe1**. Ce mot de passe expire après la première ouverture de session ; Par conséquent, vous devez utiliser l’Assistant d’installation pour le modifier. Vous devez fournir un nouveau mot de passe administrateur périphérique lorsque vous vous inscrivez le périphérique pour la première fois. 

Si vous utilisez le logiciel de gestionnaire de snapshots de StorSimple en cours d’exécution sur l’hôte Windows Server à gérer le périphérique, vous devez également fournir un mot de passe du Gestionnaire de snapshots StorSimple pendant l’inscription de la première fois. 

Assurez-vous que vos mots de passe satisfont aux exigences suivantes :

- Le mot de passe administrateur doit être compris entre 8 et 15 caractères.
- Votre mot de passe du Gestionnaire de snapshots StorSimple doit comporter 14 ou 15 caractères.
- Les mots de passe doivent contenir 3 suivant 4 types de caractères : minuscules, majuscules, numériques et spéciaux. 
- Votre mot de passe ne peut pas être la même que les dernière 24 mots de passe.

En outre, gardez à l’esprit que les mots de passe expirent chaque année et peuvent être modifiées qu’après que vous inscrivez correctement l’appareil. Si l’enregistrement échoue pour une raison quelconque, les mots de passe ne changera pas. Pour plus d’informations sur l’administrateur de périphérique et les mots de passe de gestionnaire de snapshots StorSimple, accédez à [utiliser le service de gestionnaire de StorSimple modifier votre mot de passe StorSimple](storsimple-change-passwords.md).

Vous pouvez rencontrer un ou plusieurs des erreurs suivantes lors de la configuration de l’administrateur de périphérique et les mots de passe de gestionnaire d’instantanés StorSimple.

| N°| Message d’erreur | Action recommandée |
| ---| ------------- | ------------------ | 
| 1  | Le mot de passe dépasse la longueur maximale. |Utilisez un mot de passe qui répond à ces exigences :<ul><li>Le mot de passe administrateur doit être compris entre 8 et 15 caractères.</li><li>Votre mot de passe du Gestionnaire de snapshots StorSimple doit comporter 14 ou 15 caractères.</li></ul> | 
| 2 | Le mot de passe ne répond pas à la longueur requise. | Utilisez un mot de passe qui répond à ces exigences :<ul><li>Le mot de passe administrateur doit être compris entre 8 et 15 caractères.</li><li>Votre mot de passe du Gestionnaire de snapshots StorSimple doit comporter 14 ou 15 caractères.</lu></ul> |
| 3 | Le mot de passe doit contenir des caractères en minuscules. | Les mots de passe doivent contenir 3 suivant 4 types de caractères : minuscules, majuscules, numériques et spéciaux. Assurez-vous que votre mot de passe répond à ces exigences. |
| 4 | Le mot de passe doit contenir des caractères numériques. | Les mots de passe doivent contenir 3 suivant 4 types de caractères : minuscules, majuscules, numériques et spéciaux. Assurez-vous que votre mot de passe répond à ces exigences. |
| 5 | Le mot de passe doit contenir des caractères spéciaux. | Les mots de passe doivent contenir 3 suivant 4 types de caractères : minuscules, majuscules, numériques et spéciaux. Assurez-vous que votre mot de passe répond à ces exigences. |
| 6 | Le mot de passe doit contenir 3 des 4 caractères suivants : majuscules, minuscules, numériques et spéciaux. | Votre mot de passe ne contient-elle pas de types de caractères requis. Assurez-vous que votre mot de passe répond à ces exigences. |
| 7 | Paramètre ne correspond pas à la confirmation. | Assurez-vous que votre mot de passe répond à toutes les exigences et que vous l’avez entrée correctement. |
| 8 | Votre mot de passe ne peut pas correspondre à la valeur par défaut. | Le mot de passe par défaut est le *mot de passe1*. Vous devez modifier ce mot de passe une fois que vous ouvrez une session pour la première fois. |
| 9 | Le mot de passe que vous avez entré ne correspond pas le mot de passe de périphérique. Retapez le mot de passe. | Le mot de passe puis tapez-le de nouveau. |

Les mots de passe sont collectés avant que le périphérique est enregistré, mais sont appliquées uniquement après l’enregistrement est effectué. Le flux de travail de récupération de mot de passe nécessite le périphérique d’être enregistré. 

> [AZURE.IMPORTANT] En général, en cas d’échec d’une tentative d’appliquer un mot de passe, puis le logiciel à plusieurs reprises tente de récupérer le mot de passe jusqu'à ce que l’opération a réussi. Dans de rares cas, le mot de passe ne peut pas être appliquée. Dans ce cas, vous pouvez inscrire l’appareil et continuer, cependant les mots de passe ne seront pas modifiés. Vous ne recevrez aucune indication qui le mot de passe n’a pas changé, le mot de passe administrateur ou le mot de passe du Gestionnaire d’instantanés StorSimple. Si cette situation se produit, nous vous conseillons de modifier les mots de passe.

Vous pouvez réinitialiser les mots de passe dans le portail classique Azure via le service Gestionnaire de StorSimple. Pour plus d’informations, consultez : 

- [Modifier le mot de passe administrateur](storsimple-change-passwords.md#change-the-device-administrator-password).
- [Modifier le mot de passe du Gestionnaire d’instantanés StorSimple](storsimple-change-passwords.md#change-the-storsimple-snapshot-manager-password).

## <a name="errors-during-device-registration"></a>Erreurs lors de l’inscription de périphérique

Le service de gestionnaire de StorSimple en cours d’exécution dans Microsoft Azure vous permet d’inscrire l’appareil. Vous pouvez rencontrer un ou plusieurs des problèmes suivants lors de l’enregistrement du périphérique.

| N°| Message d’erreur | Causes possibles | Action recommandée |
| ---| ------------- | --------------- | ------------------ |
| 1  | Erreur 350027 : Impossible d’inscrire le périphérique avec le Gestionnaire de StorSimple. |  | Attendez quelques minutes et essayez à nouveau l’opération. Si le problème persiste, [contactez le support technique de Microsoft](storsimple-contact-microsoft-support.md).|
| 2  | Erreur 350013 : Une erreur s’est produite lors de l’inscription de l’appareil. Cela peut provenir de clé d’enregistrement de service incorrecte. | | Veuillez enregistrer à nouveau le périphérique avec la clé d’inscription de service correct. Pour plus d’informations, consultez [obtenir la clé d’enregistrement service.](storsimple-manage-service.md#get-the-service-registration-key) |
| 3 | Erreur 350063 : Échec de l’authentification pour le service de gestionnaire de StorSimple passé, mais l’enregistrement. Veuillez recommencer l’opération après un certain temps. | Cette erreur indique que l’authentification ACS a réussi mais échec de l’appel de Registre effectuée sur le service. Cela peut résulter d’un problème réseau sporadique. | Si le problème persiste, veuillez [contacter le support technique de Microsoft](storsimple-contact-microsoft-support.md). |
| 4 | Erreur 350049 : Le service n’a pas pu être atteint lors de l’inscription. | Lorsque l’appel est effectué au service, une exception de web est reçue. Dans certains cas, il peut obtenir fixe avec une nouvelle tentative de l’opération plus tard. | Veuillez vérifier votre adresse IP et le nom DNS et recommencez l’opération. Si le problème persiste, [contactez le Support Microsoft.](storsimple-contact-microsoft-support.md) | 
| 5 | Erreur 350031 : Le périphérique a déjà été inscrit. | | Aucune action n’est nécessaire. |
| 6 | Erreur 350016 : Échec de l’inscription de l’appareil. | |Vérifiez que la clé d’enregistrement est correcte. |
| 7 | Invoke-HcsSetupWizard : Une erreur s’est produite lors de l’enregistrement de votre appareil ; Cela peut être dû à des adresse IP ou nom DNS. Veuillez vérifier vos paramètres réseau et réessayez. Si le problème persiste, [contactez le support technique de Microsoft](storsimple-contact-microsoft-support.md). (Erreur 350050) | Assurez-vous que votre appareil peut ping sur le réseau externe. Si vous n’avez pas de connexion réseau externe, l’inscription peut échouer avec cette erreur. Cette erreur peut être une combinaison d’une ou plusieurs des opérations suivantes :<ul><li>IP incorrecte</li><li>Sous-réseau incorrect</li><li>Passerelle incorrecte</li><li>Paramètres DNS incorrects</li></ul> | Consultez les étapes de l' [exemple de dépannage étape par étape](#step-by-step-storsimple-troubleshooting-example). |
| 8 | Invoke-HcsSetupWizard : Échec de l’opération actuelle en raison d’une erreur interne du service [0x1FBE2]. Recommencez l’opération ultérieurement. Si le problème persiste, veuillez contacter le Support Microsoft. | Il s’agit d’une erreur générique est levée pour les erreurs d’invisible tous les utilisateurs du service ou de l’agent. La raison la plus courante est peut-être que l’authentification ACS a échoué. Des causes possibles de l’échec sont qu’il existe des problèmes avec la configuration du serveur NTP et sur le périphérique n’est pas défini correctement. | L’heure correcte (s’il existe des problèmes) et recommencez l’opération d’enregistrement. Si vous utilisez la commande Set-HcsSystem - fuseau horaire pour régler le fuseau horaire, majuscule chaque dans le fuseau horaire (par exemple « Pacifique »).  Si ce problème persiste, [contacter le Support de Microsoft](storsimple-contact-microsoft-support.md) pour les étapes suivantes. |
| 9 | Avertissement : Impossible d’activer le périphérique. Votre administrateur de périphérique et les mots de passe de gestionnaire de snapshots StorSimple n’ont pas été modifiés. | En cas d’échec de l’enregistrement, l’administrateur du périphérique et Gestionnaire de snapshots StorSimple de mots de passe ne sont pas modifiés. |

## <a name="tools-for-troubleshooting-storsimple-deployments"></a>Outils de résolution des déploiements de StorSimple

StorSimple comprend plusieurs outils que vous pouvez utiliser pour résoudre les problèmes de votre solution de StorSimple. Celles-ci comprennent :

- Prise en charge des packages et des journaux de périphérique 
- Applets de commande spécialement conçues pour la résolution des problèmes 

## <a name="support-packages-and-device-logs-available-for-troubleshooting"></a>Prise en charge des packages et des journaux de périphérique disponible pour la résolution des problèmes

Une prise en charge contient tous les journaux pertinents qui peuvent aider l’équipe de Support de Microsoft sur la résolution des problèmes de périphériques. Vous pouvez utiliser Windows PowerShell pour StorSimple pour générer un package chiffré de prise en charge que vous pouvez ensuite partager avec le personnel du support technique.

### <a name="to-view-the-logs-or-the-contents-of-the-support-package"></a>Pour afficher les journaux ou le contenu du package de prise en charge

1. Utiliser Windows PowerShell pour StorSimple pour générer un package de prise en charge, comme décrit dans [créer et gérer un package de prise en charge](storsimple-create-manage-support-package.md).

2. Téléchargez le [script de décryptage](https://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) localement sur votre ordinateur client.

3. Utilisez cette [procédure pas à pas](storsimple-create-manage-support-package.md#edit-a-support-package) pour ouvrir et décrypter le package de prise en charge.

4. Journaux de lot déchiffrée de prise en charge sont au format d’etw/etvx. Vous pouvez effectuer les opérations suivantes pour afficher ces fichiers dans l’Observateur d’événements Windows :
  1. Exécutez la commande **eventvwr** sur votre client Windows. Démarre l’Observateur d’événements.
  2. Dans le volet **Actions** , cliquez sur **Ouvrir le journal enregistré** et pointez sur les fichiers journaux au format d’etvx/etw (le package de prise en charge). Vous pouvez maintenant afficher le fichier. Après avoir ouvert le fichier, vous pouvez avec le bouton droit et enregistrez le fichier sous forme de texte.
   
    > [AZURE.IMPORTANT] Vous pouvez également utiliser l’applet de commande **Get-intercepteur** d’ouvrir ces fichiers dans Windows PowerShell. Pour plus d’informations, reportez-vous à la section [Get-intercepteur](https://technet.microsoft.com/library/hh849682.aspx) dans la documentation de référence d’applet de commande Windows PowerShell.

5. Ouverture des journaux dans l’Observateur d’événements, recherchez les fichiers journaux suivants qui contiennent des problèmes liés à la configuration du périphérique :

  - hcs_pfconfig journal opérationnel
  - hcs_pfconfig/Config

6. Dans les fichiers journaux, rechercher des chaînes liées pour les applets de commande appelée par l’Assistant d’installation. Pour obtenir la liste de ces applets de commande, reportez-vous à la section [processus de l’Assistant première fois le programme d’installation](#first-time-setup-wizard-process) . 

7. Si vous n’êtes pas en mesure de déterminer la cause du problème, vous pouvez [contacter le support technique de Microsoft](storsimple-contact-microsoft-support.md) pour les étapes suivantes. Suivez les étapes dans [créer une demande de support](storsimple-contact-microsoft-support.md#create-a-support-request) lorsque vous contactez le Support Microsoft pour obtenir de l’aide.

## <a name="cmdlets-available-for-troubleshooting"></a>Applets de commande disponible pour le dépannage

Utiliser les applets de commande Windows PowerShell suivante pour détecter les erreurs de connectivité.

- `Get-NetAdapter`: Cette applet de commande à utiliser pour détecter l’état des interfaces réseau. 

- `Test-Connection`: À utiliser cette applet de commande pour vérifier la connectivité réseau à l’intérieur et à l’extérieur du réseau.

- `Test-HcsmConnection`: À utiliser cette applet de commande pour vérifier la connectivité d’un périphérique enregistré avec succès.

Si vous exécutez 1 de mise à jour de votre périphérique StorSimple, les applets de commande de diagnostics suivants sont également disponibles.

- `Sync-HcsTime`: À utiliser cette applet de commande pour afficher l’heure du périphérique et forcer une synchronisation avec le serveur NTP.

- `Enable-HcsPing`et `Disable-HcsPing`: ces applets de commande permet d’autoriser les hôtes ping sur les interfaces réseau sur le périphérique StorSimple. Par défaut, les interfaces de réseau StorSimple ne répondent pas aux requêtes ping.

- `Trace-HcsRoute`: Cette applet de commande à utiliser comme un outil de traçage d’itinéraire. Il envoie des paquets à chaque routeur sur le chemin d’une destination finale pendant une période de temps, puis calcule les résultats en fonction des paquets renvoyés depuis chaque tronçon. Dans la mesure où `Trace-HcsRoute` indique le degré de perte de paquets sur n’importe quel routeur ou d’un lien, vous pouvez déterminer quels routeurs ou liens peuvent être à l’origine des problèmes de réseau. 

- `Get-HcsRoutingTable`: À utiliser cette applet de commande pour afficher la table de routage IP locale.

## <a name="troubleshoot-with-the-get-netadapter-cmdlet"></a>Résoudre les problèmes avec l’applet de commande Get-NetAdapter

Lorsque vous configurez les interfaces réseau d’un déploiement de la première fois, l’état du matériel n’est pas disponible dans le service de gestionnaire de StorSimple l’interface utilisateur, car le périphérique n’est pas encore inscrit avec le service. En outre, la page de l’état du matériel reflète ne peut-être pas toujours correctement l’état du périphérique, en particulier si des problèmes affectent la synchronisation du service. Dans ces situations, vous pouvez utiliser la `Get-NetAdapter` applet de commande pour déterminer la santé et l’état des interfaces réseau.

### <a name="to-see-a-list-of-all-the-network-adapters-on-your-device"></a>Pour afficher la liste de toutes les cartes réseau sur votre périphérique.

1. Démarrage de Windows PowerShell pour StorSimple et tapez `Get-NetAdapter`. 

2. Utilisez le résultat de la `Get-NetAdapter` applet de commande et les consignes ci-dessous pour connaître l’état de l’interface réseau.
  - Si l’interface est en bon état et activé, l’état **ifIndex** est affiché comme étant en **service**.
  - Si l’interface est sain, mais n’est pas physiquement connecté (par un câble réseau), **ifIndex** est affiché comme **désactivé**.
  - Si l’interface est correct mais pas activé, l’état **ifIndex** est affiché en tant que **NotPresent**.
  - Si l’interface n’existe pas, il n’apparaît pas dans cette liste. Le service de gestionnaire de StorSimple l’interface utilisateur affiche toujours cette interface en état d’échec.

Pour plus d’informations sur l’utilisation de cette applet de commande, consultez [GetNetAdapter](https://technet.microsoft.com/library/jj130867.aspx) dans la référence d’applet de commande Windows PowerShell. 

Les sections suivantes présentent des exemples de sortie de la `Get-NetAdapter` applet de commande. 

 Dans ces exemples, contrôleur 0 était le contrôleur passif et a été configuré comme suit :

- DONNÉES 0 données 1, données 2 et données 3 réseau interfaces existaient sur le périphérique.
- DONNÉES 4 et 5 de données cartes d’interface réseau n’étaient pas présents ; Par conséquent, ils ne sont pas répertoriés dans la sortie.
- DONNÉES 0 a été activées.

Contrôleur 1 était le contrôleur actif et il a été configuré comme suit :

- DONNÉES 0, données 1, données 2, données 3, 4 de données et réseau de données 5 interfaces existaient sur le périphérique.
- DONNÉES 0 a été activées.

**Sortie d’échantillon : contrôleur 0**

Voici la sortie de contrôleur 0 (le contrôleur passif). DONNÉES 1, données 2 et données 3 ne sont pas connectés. DONNÉES 4 et 5 de données ne sont pas répertoriées car ils ne sont pas présents sur le périphérique. 

     Controller0>Get-NetAdapter
     Name                 InterfaceDescription                        ifIndex  Status
     ------               --------------------                        -------  ----------
     DATA3                Mellanox ConnectX-3 Ethernet Adapter #2     17       NotPresent
     DATA2                Mellanox ConnectX-3 Ethernet Adapter        14       NotPresent
     Ethernet 2           HCS VNIC                                    13       Up
     DATA1                Intel(R) 82574L Gigabit Network Co...#2     16       NotPresent
     DATA0                Intel(R) 82574L Gigabit Network Conn...     15       Up


**Sortie d’échantillon : contrôleur 1**

Voici la sortie de contrôleur de 1 (le contrôleur actif). Uniquement les données 0 interface réseau sur le périphérique est configuré et opérationnel.

     Controller1>Get-NetAdapter
     Name                 InterfaceDescription                        ifIndex  Status
     ------               --------------------                        -------  ----------
     DATA3                Mellanox ConnectX-3 Ethernet Adapter        18       NotPresent
     DATA2                Mellanox ConnectX-3 Ethernet Adapter #2     19       NotPresent
     DATA1                Intel(R) 82574L Gigabit Network Co...#2     16       NotPresent
     DATA0                Intel(R) 82574L Gigabit Network Conn...     15       Up
     Ethernet 2           HCS VNIC                                    13       Up
     DATA5                Intel(R) Gigabit ET Dual Port Server...     14       NotPresent
     DATA4                Intel(R) Gigabit ET Dual Port Serv...#2     17       NotPresent

 
## <a name="troubleshoot-with-the-test-connection-cmdlet"></a>Résoudre les problèmes avec l’applet de commande Test-connexion

Vous pouvez utiliser la `Test-Connection` applet de commande pour déterminer si votre périphérique StorSimple peut se connecter au réseau externe. Si tous les paramètres de mise en réseau, y compris le serveur DNS, sont correctement configurés dans l’Assistant d’installation, vous pouvez utiliser la `Test-Connection` applet de commande ping sur une adresse connue en dehors du réseau, par exemple outlook.com. 

Vous devez activer la commande ping résoudre les problèmes de connectivité dans cette applet de commande si la commande ping est désactivée.

Consultez les exemples de sortie de la `Test-Connection` applet de commande. 

> [AZURE.NOTE] Dans le premier exemple, le périphérique est configuré avec un serveur DNS incorrect. Dans le deuxième exemple, le serveur DNS est correct.
 
**Sortie d’échantillon – DNS incorrect**

Dans l’exemple suivant, il n’y a aucune sortie pour les adresses IPV4 et IPV6, ce qui indique que le serveur DNS n’est pas résolu. Cela signifie que s’il n’y a pas de connectivité au réseau externe et un serveur DNS correct doit être fourni. 

     Source        Destination     IPV4Address      IPV6Address
     ------        -----------     -----------      -----------
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com

**Sortie d’échantillon – DNS correct**

Dans l’exemple suivant, le serveur DNS renvoie l’adresse IPV4, indiquant que le serveur DNS est configuré correctement. Cela permet de confirmer qu’il existe une connectivité au réseau externe. 

     Source        Destination     IPV4Address      IPV6Address
     ------        -----------     -----------      -----------
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194

## <a name="troubleshoot-with-the-test-hcsmconnection-cmdlet"></a>Résoudre les problèmes avec l’applet de commande Test-HcsmConnection

Utilisez le `Test-HcsmConnection` applet de commande pour un périphérique qui est déjà connecté et enregistré avec votre service de gestionnaire de StorSimple. Cette applet de commande permet de vérifier la connectivité entre un périphérique enregistré et le service de gestionnaire de StorSimple correspondant. Vous pouvez exécuter cette commande sur Windows PowerShell pour StorSimple. 

### <a name="to-run-the-test-hcsmconnection-cmdlet"></a>Pour exécuter l’applet de commande Test-HcsmConnection

1. Assurez-vous que le périphérique est enregistré.

2. Vérifiez l’état du périphérique. Si le périphérique est désactivé, en mode de maintenance, ou en mode hors connexion, vous pouvez voir les erreurs suivantes : 

   - ErrorCode.CiSDeviceDecommissioned – indique que le périphérique est désactivé.
   - ErrorCode.DeviceNotReady – indique que le périphérique est en mode maintenance.
   - ErrorCode.DeviceNotReady – indique que le périphérique n’est pas en ligne.

3. Vérifiez que le service Gestionnaire de StorSimple est en cours d’exécution (utilisez l’applet de commande [Get-ClusterResource](https://technet.microsoft.com/library/ee461004.aspx) ). Si le service n’est pas en cours d’exécution, vous pouvez voir les erreurs suivantes :

   - ErrorCode.CiSApplianceAgentNotOnline
   - ErrorCode.CisPowershellScriptHcsError – cela indique qu’une exception est survenue lors de l’exécution de Get-ClusterResource.

4. Vérifier le jeton du Service de contrôle d’accès (ACS). Si elle lève une exception de web, il peut être le résultat d’un problème de passerelle, une authentification de proxy manquant, un serveur DNS incorrect ou un échec d’authentification. Vous pouvez voir les erreurs suivantes :

   - ErrorCode.CiSApplianceGateway – cela indique une exception HttpStatusCode.BadGateway : le service de résolution de nom n’a pas pu résoudre le nom d’hôte. 
   - ErrorCode.CiSApplianceProxy – cela indique une exception HttpStatusCode.ProxyAuthenticationRequired (code d’état HTTP 407) : le client n’a pas pu s’authentifier avec le serveur proxy. 
   - ErrorCode.CiSApplianceDNSError – cela indique une exception WebExceptionStatus.NameResolutionFailure : le service de résolution de nom n’a pas pu résoudre le nom d’hôte.
   - ErrorCode.CiSApplianceACSError – indique que le service a renvoyé une erreur d’authentification, mais il existe une connectivité.
   
    Si elle ne lève pas une exception web, recherchez ErrorCode.CiSApplianceFailure. Cela indique que l’application a échoué.

5. Vérifiez la connectivité de service cloud. Si le service lève une exception de web, vous pouvez voir les erreurs suivantes :

  - ErrorCode.CiSApplianceGateway – cela indique une exception HttpStatusCode.BadGateway : un serveur proxy intermédiaire a reçu une demande incorrecte à partir d’un autre proxy ou du serveur d’origine.
  - ErrorCode.CiSApplianceProxy – cela indique une exception HttpStatusCode.ProxyAuthenticationRequired (code d’état HTTP 407) : le client n’a pas pu s’authentifier avec le serveur proxy. 
  - ErrorCode.CiSApplianceDNSError – cela indique une exception WebExceptionStatus.NameResolutionFailure : le service de résolution de nom n’a pas pu résoudre le nom d’hôte.
  - ErrorCode.CiSApplianceACSError – indique que le service a renvoyé une erreur d’authentification, mais il existe une connectivité.
  
    Si elle ne lève pas une exception web, recherchez ErrorCode.CiSApplianceSaasServiceError. Cela indique un problème avec le service Gestionnaire de StorSimple.
 
6. Vérifiez la connectivité du Bus des services Azure. ErrorCode.CiSApplianceServiceBusError indique que le périphérique ne peut pas se connecter au Bus de Service.
 
Les fichiers journaux CiSCommandletLog0Curr.errlog et CiSAgentsvc0Curr.errlog ont plus d’informations, telles que les détails de l’exception. 

Consultez la documentation pour plus d’informations sur l’utilisation de l’applet de commande, accédez au [Test-HcsmConnection](https://technet.microsoft.com/library/dn715782.aspx) dans le Windows PowerShell.

> [AZURE.IMPORTANT] Vous pouvez exécuter cette applet de commande pour les actifs et passifs. 
 
Consultez les exemples de sortie de la `Test-HcsmConnection` applet de commande. 

**Exemple de sortie – périphérique correctement enregistré qui exécutent la version de StorSimple (juillet 2014)**

Le premier exemple est un périphérique qui est enregistrée avec succès avec le service Gestionnaire de StorSimple et n’a aucun problème de connectivité. 

     Controller1>Test-HcsmConnection -verbose
     Checking device state  ... Success.
     Device registered successfully
     Checking device authentication.  ... This operation will take few minutes to complete....
     Checking device authentication.  ... Success.
     Checking connectivity from device to StorSimple Manager service.  ... This operation will take few minutes to complete....
     Checking connectivity from device to StorSimple Manager service.  ... Success.
     Checking connectivity from StorSimple Manager service to StorSimple device. .... Success.
     Controller1>

**Exemple de sortie – périphérique correctement enregistré en cours d’exécution StorSimple mise à jour 1**

Si vous exécutez 1 de mise à jour de votre périphérique StorSimple, vous devrez pas l’exécuter avec le commutateur de commentaires.

      Controller1>Test-HcsmConnection
       
      Checking device registration state  ... Success
      Device registered successfully
       
      Checking primary NTP server [time.windows.com] ... Success
       
      Checking web proxy  ... NOT SET
       
      Checking primary IPv4 DNS server [10.222.118.154] ... Success
      Checking primary IPv6 DNS server  ... NOT SET
      Checking secondary IPv4 DNS server [10.222.120.24] ... Success
      Checking secondary IPv6 DNS server  ... NOT SET
       
      Checking device online  ... Success
 
      Checking device authentication  ... This will take a few minutes.
      Checking device authentication  ... Success
       
      Checking connectivity from device to service  ... This will take a few minutes.
       
      Checking connectivity from device to service  ... Success
       
      Checking connectivity from service to device  ... Success
       
      Checking connectivity to Microsoft Update servers  ... Success
      Controller1>

**Exemple de sortie – périphérique hors connexion qui exécutent la version de StorSimple (juillet 2014)**

Cet exemple est à partir d’un périphérique qui a un état **hors connexion** dans Azure portal classique.

     Checking device state: Success 
     Device is registered successfully 
     Checking connectivity from device to SaaS.. Failure

Le périphérique n’a pas pu connecter à l’aide de la configuration actuelle du proxy web. Cela peut être un problème avec la configuration du proxy web ou d’un problème de connectivité réseau. Dans ce cas, vous devez vous assurer que vos paramètres de proxy web sont corrects et que vos serveurs de proxy web sont en ligne et accessibles. 

## <a name="troubleshoot-with-the-sync-hcstime-cmdlet"></a>Résoudre les problèmes avec l’applet de commande Sync-HcsTime

Cette applet de commande permet d’afficher l’heure du périphérique. Si l’heure du périphérique possède un décalage avec le serveur NTP, vous pouvez ensuite utiliser cette applet de commande pour synchroniser le temps à force avec votre serveur NTP. Si le décalage entre l’appareil et le serveur NTP est supérieur à 5 minutes, un avertissement s’affiche. Si l’offset dépasse 15 minutes, le périphérique mis hors connexion. Vous pouvez toujours utiliser cette applet de commande pour forcer une synchronisation. Toutefois, si le décalage est supérieur à 15 heures, puis vous pas sera en mesure de force-synchronisation de l’heure et un message d’erreur seront affichera.

**Exemple de sortie – synchronisation forcée à l’aide de HcsTime de la synchronisation**
 
     Controller0>Sync-HcsTime
     The current device time is 4/24/2015 4:05:40 PM UTC.
 
     Time difference between NTP server and appliance is 00.0824069 seconds. Do you want to resync time with NTP server?
     [Y] Yes [N] No (Default is "Y"): Y
     Controller0>

## <a name="troubleshoot-with-the-enable-hcsping-and-disable-hcsping-cmdlets"></a>Résoudre les problèmes avec les applets de commande HcsPing-activer et désactiver-HcsPing

Utilisez ces applets de commande pour vous assurer que les interfaces réseau sur votre périphérique de répondent aux requêtes ping ICMP. Par défaut, les interfaces de réseau StorSimple ne répondent pas aux requêtes ping. À l’aide de cette applet de commande est le moyen le plus simple de savoir si votre périphérique est en ligne et accessibles.  

**Exemple de sortie – HcsPing-activer et désactiver-HcsPing**

     Controller0>
     Controller0>Enable-HcsPing
     Successfully enabled ping.
     Controller0>
     Controller0>
     Controller0>Disable-HcsPing
     Successfully disabled ping.
     Controller0>

## <a name="troubleshoot-with-the-trace-hcsroute-cmdlet"></a>Résoudre les problèmes avec l’applet de commande Trace-HcsRoute

Utiliser cette applet de commande comme un outil de traçage d’itinéraire. Il envoie des paquets à chaque routeur sur le chemin d’une destination finale pendant une période de temps, puis calcule les résultats en fonction des paquets renvoyés depuis chaque tronçon. Parce que l’applet de commande indique le degré de perte de paquets sur n’importe quel routeur ou lien, vous pouvez déterminer quels routeurs ou liens peuvent être à l’origine des problèmes de réseau.

**Exemple de sortie indiquant comment suivre l’itinéraire d’un paquet avec Trace-HcsRoute**

     Controller0>Trace-HcsRoute -Target 10.126.174.25
     
     Tracing route to contoso.com [10.126.174.25]
     over a maximum of 30 hops:
       0  HCSNode0 [10.126.173.90]
       1  contoso.com [10.126.174.25]
      
     Computing statistics for 25 seconds...
                 Source to Here   This Node/Link
     Hop  RTT    Lost/Sent = Pct  Lost/Sent = Pct  Address
       0                                           HCSNode0 [10.126.173.90]
                                     0/ 100 =  0%   |
       1    0ms     0/ 100 =  0%     0/ 100 =  0%  contoso.com
      [10.126.174.25]
      
     Trace complete.

## <a name="troubleshoot-with-the-get-hcsroutingtable-cmdlet"></a>Résoudre les problèmes avec l’applet de commande Get-HcsRoutingTable

Cette applet de commande permet d’afficher la table de routage de votre périphérique StorSimple. Une table de routage est un ensemble de règles qui peuvent aider à déterminer où seront redirigés les paquets de données via un réseau IP (Internet Protocol). 

La table de routage indique les interfaces et la passerelle qui achemine les données vers les réseaux spécifiés. Il donne également le métrique de routage qui est le preneur de décisions pour le chemin emprunté pour atteindre une destination particulière. La plus faible le métrique de routage, plus la préférence est élevée. 

Par exemple, si vous avez 2 interfaces réseau, données 2 et données 3, connecté à Internet. Si la métrique de routage de données 2 et données 3 est respectivement de 15 et 261, 2 de données avec le métrique de routage inférieur est l’interface par défaut utilisé pour accéder à Internet.

Si vous exécutez 1 de mise à jour de votre périphérique StorSimple, votre interface de réseau 0 de données a la préférence la plus élevée pour le trafic de nuage. Cela signifie que même s’il existe d’autres interfaces compatibles sur le nuage, le trafic de nuage serait routé par l’intermédiaire de données 0. 

Si vous exécutez le `Get-HcsRoutingTable` applet de commande sans spécifier de paramètres (comme le montre l’exemple suivant), l’applet de commande affiche les tables de routage IPv4 et IPv6. Vous pouvez également spécifier `Get-HcsRoutingTable -IPv4` ou `Get-HcsRoutingTable -IPv6` pour obtenir une table de routage pertinente.

      Controller0>
      Controller0>Get-HcsRoutingTable
      ===========================================================================
      Interface List
       14...00 50 cc 79 63 40 ......Intel(R) 82574L Gigabit Network Connection
       12...02 9a 0a 5b 98 1f ......Microsoft Failover Cluster Virtual Adapter
       13...28 18 78 bc 4b 85 ......HCS VNIC
        1...........................Software Loopback Interface 1
       21...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter #2
       22...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter #3
      ===========================================================================
       
      IPv4 Route Table
      ===========================================================================
      Active Routes:
      Network Destination        Netmask          Gateway       Interface  Metric
                0.0.0.0          0.0.0.0  192.168.111.100  192.168.111.101     15
              127.0.0.0        255.0.0.0         On-link         127.0.0.1    306
              127.0.0.1  255.255.255.255         On-link         127.0.0.1    306
        127.255.255.255  255.255.255.255         On-link         127.0.0.1    306
            169.254.0.0      255.255.0.0         On-link     169.254.1.235    261
          169.254.1.235  255.255.255.255         On-link     169.254.1.235    261
        169.254.255.255  255.255.255.255         On-link     169.254.1.235    261
          192.168.111.0    255.255.255.0         On-link   192.168.111.101    266
        192.168.111.101  255.255.255.255         On-link   192.168.111.101    266
        192.168.111.255  255.255.255.255         On-link   192.168.111.101    266
              224.0.0.0        240.0.0.0         On-link         127.0.0.1    306
              224.0.0.0        240.0.0.0         On-link     169.254.1.235    261
              224.0.0.0        240.0.0.0         On-link   192.168.111.101    266
        255.255.255.255  255.255.255.255         On-link         127.0.0.1    306
        255.255.255.255  255.255.255.255         On-link     169.254.1.235    261
        255.255.255.255  255.255.255.255         On-link   192.168.111.101    266
      ===========================================================================
      Persistent Routes:
        Network Address          Netmask  Gateway Address  Metric
                0.0.0.0          0.0.0.0  192.168.111.100       5
      ===========================================================================
       
      IPv6 Route Table
      ===========================================================================
      Active Routes:
       If Metric Network Destination      Gateway
        1    306 ::1/128                  On-link
       13    276 fd99:4c5b:5525:d80b::/64 On-link
       13    276 fd99:4c5b:5525:d80b::1/128
                                          On-link
       13    276 fd99:4c5b:5525:d80b::3/128
                                          On-link
       13    276 fe80::/64                On-link
       12    261 fe80::/64                On-link
       13    276 fe80::17a:4eba:7c80:727f/128
                                          On-link
       12    261 fe80::fc97:1a53:e81a:3454/128
                                          On-link
        1    306 ff00::/8                 On-link
       13    276 ff00::/8                 On-link
       12    261 ff00::/8                 On-link
       14    266 ff00::/8                 On-link
      ===========================================================================
      Persistent Routes:
        None
       
      Controller0>
 
## <a name="step-by-step-storsimple-troubleshooting-example"></a>Exemple de dépannage pas à pas StorSimple

L’exemple suivant montre étape par étape de la résolution des problèmes d’un déploiement StorSimple. Dans le scénario de l’exemple, l’enregistrement de dispositifs échoue avec un message d’erreur indiquant que les paramètres de réseau ou le nom DNS est incorrect.

Le message d’erreur renvoyé est :

     Invoke-HcsSetupWizard: An error has occurred while registering the device. This could be due to incorrect IP address or DNS name. Please check your network settings and try again. If the problems persist, contact Microsoft Support.
     +CategoryInfo: Not specified
     +FullyQualifiedErrorID: CiSClientCommunicationErros, Microsoft.HCS.Management.PowerShell.Cmdlets.InvokeHcsSetupWizardCommand

L’erreur peut être provoquée par une des opérations suivantes :

- Installation de matériel incorrect
- Interfaces de réseau défectueux
- Adresse IP incorrecte, le masque de sous-réseau, passerelle, serveur DNS principal ou proxy web
- Clé d’enregistrement incorrecte
- Paramètres de pare-feu incorrects

### <a name="to-locate-and-fix-the-device-registration-problem"></a>Pour localiser et résoudre le problème d’inscription de périphérique

1. Vérifiez la configuration de vos périphériques : sur le contrôleur actif, exécutez `Invoke-HcsSetupWizard`.

     > [AZURE.NOTE] L’Assistant d’installation doit s’exécuter sur le contrôleur actif. Pour vérifier que vous êtes connecté au contrôleur actif, examinez la bannière présentée dans la console série. La bannière indique si vous êtes connecté à un contrôleur de 0 ou 1, et si le contrôleur est actif ou passif. Pour plus d’informations, accédez à [identifier un contrôleur active sur le périphérique](storsimple-controller-replacement.md#identify-the-active-controller-on-your-device).
 
2. Assurez-vous que le périphérique est correctement câblé : Vérifiez le câblage sur le fond de panier du périphérique de réseau. Le câblage est spécifique au modèle de périphérique. Pour plus d’informations, accédez à [installer votre périphérique StorSimple 8100](storsimple-8100-hardware-installation.md) ou [installer votre périphérique StorSimple 8600](storsimple-8600-hardware-installation.md).

     > [AZURE.NOTE] Si vous utilisez des ports de réseau 10 Gigabit Ethernet, vous devez utiliser les cartes de QSFP-SFP fournis et les câbles SFP. Pour plus d’informations, consultez la [liste des câbles, commutateurs et émetteurs/récepteurs recommandés par le fournisseur OEM pour les ports de Mellanox](http://www.mellanox.com/page/cables?mtag=cable_overview).
 
3. Vérifiez l’état de l’interface réseau :

   - L’applet de commande Get-NetAdapter permet de détecter l’état des interfaces réseau de données 0. 
   - Si le lien ne fonctionne pas, le statut **ifindex** indique que l’interface est en panne. Vous devez alors vérifier la connexion réseau du port pour la solution matérielle-logicielle et le commutateur. Vous devez également éliminer des câbles défectueux. 
   - Si vous pensez que les données 0 port du contrôleur actif a échoué, vous pouvez le vérifier en se connectant aux données 0 port contrôleur 1. Pour confirmer cela, déconnectez le câble réseau à l’arrière du périphérique de contrôleur 0, connectez le câble au contrôleur 1 et puis exécutez à nouveau l’applet de commande Get-NetAdapter. 
   Si les données 0 port sur un contrôleur tombe en panne, [contactez le support technique de Microsoft](storsimple-contact-microsoft-support.md) pour les étapes suivantes. Vous devrez peut-être remplacer le contrôleur sur votre système.
 
4. Vérifiez la connectivité au commutateur :
   - Assurez-vous que les interfaces de réseau 0 de données sur le contrôleur 0 et contrôleur 1 dans votre boîtier principal sont sur le même sous-réseau. 
   - Vérifiez le concentrateur ou le routeur. En règle générale, vous devez vous connecter deux contrôleurs au même concentrateur ou routeur. 
   - Assurez-vous que les commutateurs que vous utilisez pour la connexion disposent données 0 pour les deux contrôleurs sur le même vLAN.
   
5. Éliminer les erreurs de l’utilisateur :

  - Exécutez l’Assistant de configuration (exécution **Invoke-HcsSetupWizard**) et entrez les valeurs à nouveau afin de vous assurer qu’il n’y a pas d’erreurs. 
  - Vérifier l’enregistrement de la clé utilisée. La même clé d’inscription peut être utilisée pour connecter plusieurs périphériques à un service de gestionnaire de StorSimple. Utilisez la procédure dans [l’obtention de la clé d’inscription de service](storsimple-manage-service.md#get-the-service-registration-key) pour vous assurer que vous utilisez la clé d’enregistrement correct.

    > [AZURE.IMPORTANT] Si vous avez plusieurs services en cours d’exécution, vous devez vous assurer que la clé d’enregistrement pour le service approprié est utilisée pour inscrire l’appareil. Si vous avez enregistré un périphérique avec un mauvais service Gestionnaire de StorSimple, vous devrez [contacter le support technique de Microsoft](storsimple-contact-microsoft-support.md) pour les étapes suivantes. Vous devrez peut-être effectuer une réinitialisation de l’usine du périphérique (ce qui peut entraîner la perte de données) vers, puis connectez-le au service prévu.

6. Utilisez l’applet de commande Test-connexion pour vérifier que vous êtes connecté au réseau externe. Pour plus d’informations, accédez à la [résolution des problèmes avec l’applet de commande Test-connexion](#troubleshoot-with-the-test-connection-cmdlet).

7. Contrôle d’interférence de pare-feu. Si vous avez vérifié que les paramètres IP (VIP), sous-réseau, la passerelle et DNS virtuels sont toutes correctes, vous voyez toujours des problèmes de connectivité, puis il est possible que votre pare-feu bloque les communications entre votre périphérique et le réseau externe. Vous devez vous assurer que les ports 80 et 443 sont disponibles sur le périphérique StorSimple pour la communication sortante. Pour plus d’informations, voir [Configuration requise pour votre dispositif de StorSimple de réseau](storsimple-system-requirements.md#networking-requirements-for-your-storsimple-device).

8. Consultez les journaux. Atteindre [les packages de support technique et des journaux de périphérique disponible pour le dépannage](#support-packages-and-device-logs-available-for-troubleshooting).

9. Si les étapes précédentes ne résolvent pas le problème, [contactez le support technique de Microsoft](storsimple-contact-microsoft-support.md) pour obtenir de l’aide.

## <a name="next-steps"></a>Étapes suivantes
[Découvrez comment résoudre les problèmes liés à un périphérique opérationnel](storsimple-troubleshoot-operational-device.md).

<!--Link references-->

[1]: https://technet.microsoft.com/library/dd379547(v=ws.10).aspx
[2]: https://technet.microsoft.com/library/dd392266(v=ws.10).aspx 
