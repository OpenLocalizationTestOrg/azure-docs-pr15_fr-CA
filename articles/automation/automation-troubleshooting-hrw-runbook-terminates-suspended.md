<properties
   pageTitle="Travailleur de procédure opérationnelle hybride : Un travail de procédure opérationnelle se termine avec l’état suspendu | Microsoft Azure"
   description="Causes de problèmes et résolutions pour travail de procédure opérationnelle hybride d’erreur de fin de la tâche."
   services="automation"
   documentationCenter=""
   authors="mgoedtel"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/17/2016"
   ms.author="magoedte" />

# <a name="hybrid-runbook-worker-a-runbook-job-terminates-with-a-status-of-suspended"></a>Travailleur de procédure opérationnelle hybride : Un travail de procédure opérationnelle se termine avec l’état suspendu

## <a name="summary"></a>Résumé

Votre procédure opérationnelle est interrompue peu après la tentative d’exécuter trois fois. Il existe des conditions susceptibles d’interrompre la procédure opérationnelle de se terminer et le message d’erreur lié n’inclut pas toutes les informations supplémentaires indiquant pourquoi. Cet article fournit des étapes de dépannage pour les problèmes liés aux erreurs d’exécution de procédure opérationnelle travailleur de procédure opérationnelle hybride.

Si votre problème Azure n’est pas abordée dans cet article, consultez les forums Azure sur [MSDN et le débordement de la pile](https://azure.microsoft.com/support/forums/). Vous pouvez valider votre problème sur ces forums ou [ @AzureSupport sur Twitter](https://twitter.com/AzureSupport). Également, vous pouvez archiver une demande de prise en charge Azure en sélectionnant **obtenir de l’aide** sur le site de [support d’Azure](https://azure.microsoft.com/support/options/) .

## <a name="symptom"></a>Symptôme

Exécution de procédure opérationnelle échoue et l’erreur renvoyée est, « l’action de travail 'Activer' ne peut pas être exécutée, car le processus s’est arrêté de façon inattendue. L’action de travail a été tentée 3 fois. »


## <a name="cause"></a>Cause

Il existe plusieurs causes possibles de l’erreur : 

  1. Le travailleur hybride est derrière un proxy ou un pare-feu
  2. L’ordinateur qu'exécutant le travailleur hybride a moins que les [exigences](automation-hybrid-runbook-worker.md#hybrid-runbook-worker-requirements) configuration matérielle minimale 
  3. Les procédures opérationnelles ne peut pas s’authentifier auprès des ressources locales


## <a name="cause-1-hybrid-runbook-worker-is-behind-proxy-or-firewall"></a>Cause 1 : Travail de procédure opérationnelle hybride est derrière un proxy ou un pare-feu

L’ordinateur que fonctionne sur le travailleur de procédure opérationnelle hybride est derrière un pare-feu ou un serveur proxy et un accès réseau sortant ne peut pas autorisé ou configuré correctement.

### <a name="solution"></a>Solution

Vérifiez que l’ordinateur dispose d’un accès sortant à *. cloudapp.net sur les ports 443, 9354 et 30199-30000. 

## <a name="cause-2-computer-has-less-than-minimum-hardware-requirements"></a>Cause 2 : Ordinateur possède la configuration matérielle minimale inférieure à

Ordinateurs qui exécutent le travail de procédure opérationnelle hybride doivent présenter la configuration matérielle minimale requise avant de désigner qu’il héberge cette fonctionnalité : Dans le cas contraire, en fonction de l’utilisation des ressources d’autres processus d’arrière-plan et les conflits causés par des procédures opérationnelles lors de l’exécution, l’ordinateur va devenir surutilisées et entraîner des retards de tâche de procédure opérationnelle ou des délais d’attente. 

### <a name="solution"></a>Solution 

Vérifiez tout d’abord l’ordinateur désigné pour exécuter la fonction de travail de procédure opérationnelle hybride répond à la configuration matérielle minimale requise.  Si c’est le cas, surveiller l’utilisation des ressources UC et mémoire pour identifier toute corrélation entre les performances du processus de travail de procédure opérationnelle hybride et Windows.  S’il existe de pression de processeur ou de mémoire, cela peut indiquer la nécessité de mettre à niveau ou ajoutez des processeurs supplémentaires ou augmentez la mémoire pour adresser le goulot d’étranglement et de résoudre l’erreur. Vous pouvez également sélectionner une ressource de calcul différentes qui peut prendre en charge la configuration minimale requise et l’échelle lorsque la charge de travail indique une augmentation est nécessaire.         

## <a name="cause-3-runbooks-cannot-authenticate-with-local-resources"></a>Cause 3 : Les procédures opérationnelles ne peut pas s’authentifier auprès des ressources locales

### <a name="solution"></a>Solution

Vérifiez le journal des événements pour un événement correspondant avec description de *Processus Win32 s’est terminé avec le code [4294967295]* **Microsoft-SMA** .  La cause de cette erreur est vous n’avez pas configuré l’authentification dans les procédures opérationnelles ou spécifié d’exécuter en tant qu’informations d’identification pour le groupe de travail hybrides.  Vérifiez [les autorisations de procédure opérationnelle](automation-hybrid-runbook-worker.md#runbook-permissions) pour confirmer que vous avez correctement configuré l’authentification pour les procédures opérationnelles.  


 

