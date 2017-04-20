<properties 
   pageTitle="Dépanner un périphérique déployé de StorSimple | Microsoft Azure"
   description="Décrit comment diagnostiquer et résoudre les erreurs qui se produisent sur un dispositif de StorSimple qui est actuellement déployé et opérationnels."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="05/16/2016"
   ms.author="v-sharos" />

# <a name="troubleshoot-an-operational-storsimple-device"></a>Résoudre les problèmes liés à un dispositif opérationnel de la StorSimple

## <a name="overview"></a>Vue d’ensemble

Cet article fournit des conseils de dépannage pour la résolution des problèmes de configuration que vous pouvez rencontrer après votre périphérique StorSimple est déployé et opérationnel. Il décrit les problèmes courants, les causes et les étapes à suivre pour vous aider à résoudre les problèmes que vous pouvez rencontrer lorsque vous exécutez Microsoft Azure StorSimple. Ces informations s’appliquent à la fois le périphérique physique sur site de StorSimple et le périphérique virtuel StorSimple.

À la fin de cet article, que vous trouverez une liste des codes d’erreur que vous pouvez rencontrer lors de l’opération de Microsoft Azure StorSimple, ainsi que les étapes vous pouvez prendre pour résoudre les erreurs. 

## <a name="setup-wizard-process-for-operational-devices"></a>Processus de l’Assistant d’installation de périphériques opérationnels

Vous utilisez l’Assistant d’installation ([Invoke-HcsSetupWizard][1]) pour vérifier la configuration du périphérique et prendre des mesures correctives si nécessaire.

Lorsque vous exécutez l’Assistant d’installation sur un périphérique précédemment configuré et opérationnels, le flux de processus est différent. Vous pouvez modifier uniquement les entrées suivantes :

- Adresse IP, masque de sous-réseau et passerelle
- Serveur DNS principal
- Serveur NTP principal
- Configuration du serveur proxy web en option

L’Assistant d’installation n’effectue pas les opérations relatives à l’enregistrement des collection et dispositif de mot de passe.

## <a name="errors-that-occur-during-subsequent-runs-of-the-setup-wizard"></a>Erreurs qui se produisent lors des exécutions suivantes de l’Assistant Installation

Le tableau suivant décrit les erreurs que vous pouvez rencontrer lorsque vous exécutez l’Assistant de configuration sur un dispositif opérationnel, les causes possibles de l’erreur et les mesures recommandées pour les résoudre. 

| N° | Message d’erreur ou une condition | Causes possibles | Action recommandée |
|:--- |:-------------------------- |:--------------- |:------------------ |
|  1  | Erreur 350032 : Ce périphérique a déjà été désactivé. | Vous obtiendrez cette erreur si vous exécutez l’Assistant d’installation sur un périphérique qui est désactivé. | [Contacter le support technique Microsoft](storsimple-contact-microsoft-support.md) pour les étapes suivantes. Un périphérique désactivé ne peut pas être mis en service. Une réinitialisation de la fabrique peut être nécessaire avant que le périphérique peut être activé à nouveau. |
|  2  | Invoke-HcsSetupWizard : ERROR_INVALID_FUNCTION (Exception à partir de HRESULT : 0 x 80070001) | Échec de la mise à jour du serveur DNS. Les paramètres DNS sont des paramètres globaux et sont appliqués à toutes les interfaces réseau activée. | Activer l’interface et appliquer de nouveau les paramètres DNS. Cela peut perturber le réseau pour les autres interfaces activées car ces paramètres sont globaux. |
|  3  | Le périphérique semble être en ligne dans le Gestionnaire de StorSimple service portal, mais lorsque vous essayez de terminer la configuration minimale et enregistrer la configuration, l’opération échoue. | Lors de l’installation initiale, le proxy web n’a pas configuré, même s’il était un serveur de proxy réel en place. | Utilisez l' [applet de commande Test-HcsmConnection] [ 2] pour localiser l’erreur. [Contacter le support technique Microsoft](storsimple-contact-microsoft-support.md) si vous ne parvenez pas à résoudre le problème. |
|  4  | Invoke-HcsSetupWizard : La valeur ne tombe pas dans la plage attendue. | Un masque de sous-réseau incorrect génère cette erreur. Les causes possibles sont : <ul><li> Le masque de sous-réseau est manquant ou vide.</li><li>Le format de préfixe Ipv6 est incorrect.</li><li>L’interface est activé pour le cloud, mais la passerelle est manquant ou incorrect.</li></ul>Notez que les données 0 sont automatiquement activé pour le cloud si configuré par le biais de l’Assistant d’installation. | Pour déterminer le problème, utilisez le sous-réseau 0.0.0.0 ou 256.256.256.256, puis regardez dans la sortie. Entrez les valeurs correctes pour le masque de sous-réseau, la passerelle et le préfixe Ipv6, en fonction des besoins. |
 
## <a name="error-codes"></a>Codes d’erreur

Les erreurs sont répertoriées dans l’ordre numérique.

|Numéro d’erreur|Texte d’erreur ou de la description|Action de l’utilisateur|
|:---|:---|:---|
|10502|Une erreur s’est produite lors de l’accès à votre compte de stockage.|Attendez quelques minutes et essayez à nouveau. Si l’erreur persiste, veuillez contacter le support technique Microsoft pour les étapes suivantes.|
|40017|L’opération de sauvegarde a échoué : un volume spécifié dans la stratégie de sauvegarde n’a pas été trouvé sur le périphérique.|Recommencez la sauvegarde opération, si l’erreur persiste, contactez le Support Microsoft. pour les étapes suivantes.|
|40018|L’opération de sauvegarde a échoué car aucun des volumes spécifiés dans la stratégie de sauvegarde n’a été trouvé sur le périphérique. |Recommencez la sauvegarde opération, si l’erreur persiste, contactez le Support Microsoft. pour les étapes suivantes.|
|390061|Le système est occupé ou non disponible.|Attendez quelques minutes et essayez à nouveau. Si l’erreur persiste, veuillez contacter le support technique Microsoft pour les étapes suivantes.|
|390143|Une erreur s’est produite avec le code d’erreur 390143. (Erreur inconnue).|Si l’erreur persiste, veuillez contacter le Support Microsoft pour les étapes suivantes.|

## <a name="next-steps"></a>Étapes suivantes

Si vous ne parvenez pas à résoudre le problème, [contactez le support technique de Microsoft](storsimple-contact-microsoft-support.md) pour obtenir de l’aide. 


[1]: https://technet.microsoft.com/en-us/%5Clibrary/Dn688135(v=WPS.630).aspx
[2]: https://technet.microsoft.com/en-us/%5Clibrary/Dn715782(v=WPS.630).aspx
