---
title: Orchestrations durables - Azure Functions
description: Introduction à la fonctionnalité d’orchestration pour Azure Durable Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: overview
ms.date: 09/08/2019
ms.author: azfuncdf
ms.openlocfilehash: 4e11070f4e766f83b0e7ead7757c675de3fef33f
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73614768"
---
# <a name="durable-orchestrations"></a>Orchestrations durables

Durable Functions est une extension d’[Azure Functions](../functions-overview.md). Vous pouvez utiliser une *fonction orchestrator* pour orchestrer l’exécution d’autres fonctions Durable Functions dans une application de fonction. Les fonctions orchestrator présentent les caractéristiques suivantes :

* Les fonctions orchestrator définissent les workflows de fonction à l’aide de code procédural. Elles ne nécessitent aucun concepteur ou schéma déclaratif.
* Les fonctions orchestrator peuvent appeler d’autres fonctions durables de façon synchrone et asynchrone. La sortie des fonctions appelées peut être enregistrée de façon fiable dans des variables locales.
* Les fonctions orchestrator sont durables et fiables. La progression de l’exécution fait automatiquement l’objet d’un point de contrôle quand la fonction « attend » ou « est suspendue ». L’état local n’est jamais perdu quand le processus est recyclé ou que la machine virtuelle redémarre.
* L’exécution des fonctions orchestrator peut être longue. La durée de vie totale d’une *instance d’orchestration* peut se calculer en secondes, en jours, en mois ou être sans fin.

Cet article vous donne une vue d’ensemble des fonctions orchestrator et explique comment elles peuvent vous aider à résoudre diverses difficultés que vous rencontrez lors du développement d’applications. Si vous n’êtes pas déjà familiarisé avec les types de fonctions disponibles dans une application Durable Functions, lisez d’abord l’article [Types de fonctions durables](durable-functions-types-features-overview.md).

## <a name="orchestration-identity"></a>Identité d’orchestration

Chaque *instance* d’une orchestration dispose d’un identificateur d’instance (également appelé *ID d’instance*). Par défaut, chaque ID d’instance est un GUID généré automatiquement. Toutefois, les ID d’instance peuvent également être toute valeur de chaîne générée par l’utilisateur. Chaque ID d’instance d’orchestration doit être unique dans un [hub de tâches](durable-functions-task-hubs.md).

Voici quelques règles concernant les ID d’instance :

* Les ID d’instance doivent comprendre entre 1 et 256 caractères.
* Les ID d’instance ne doivent pas commencer par `@`.
* Les ID d’instance ne doivent pas contenir de caractères `/`, `\`, `#` ou `?`.
* Les ID d’instance ne doivent pas contenir de caractères de contrôle.

> [!NOTE]
> Il est généralement recommandé d’utiliser autant que possible des ID d’instance générés automatiquement. Les ID d’instance générés par l’utilisateur sont destinés aux scénarios dans lesquels il existe un mappage un-à-un entre une instance d’orchestration et une entité externe propre à l’application, comme un bon de commande ou un document.

L’ID d’instance d’une orchestration est un paramètre obligatoire pour la plupart des [opérations de gestion des instances](durable-functions-instance-management.md). Il est également important pour les diagnostics, comme la [recherche dans les données de suivi d’orchestration](durable-functions-diagnostics.md#application-insights) dans Application Insights à des fins de résolution des problèmes ou d’analyse. C’est pourquoi il est recommandé d’enregistrer les ID d’instance générés dans un emplacement externe (par exemple, une base de données ou dans des journaux d’application) où ils peuvent être facilement référencés ultérieurement.

## <a name="reliability"></a>Fiabilité

Les fonctions d’orchestrateur conservent de façon fiable leur état d’exécution à l’aide du modèle de conception [approvisionnement d’événements](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing). Au lieu de stocker directement l’état actuel d’une orchestration, le framework Durable Task utilise un magasin d’ajout uniquement pour enregistrer toute la série d’actions effectuées par l’orchestration de fonction. Un magasin d’ajout uniquement présente de nombreux avantages par rapport au « vidage » de l’état d’exécution complet. Ces avantages incluent l’amélioration des performances, de l’extensibilité et de la réactivité. Vous bénéficiez aussi de la cohérence finale des données transactionnelles, ainsi que de pistes d’audit et d’un historique complets. Les pistes d’audit permettent des actions de compensation fiables.

Durable Functions utilise l’approvisionnement d’événements en toute transparence. En coulisse, l’opérateur `await` (C#) ou `yield` (JavaScript) d’une fonction d’orchestrateur cède le contrôle du thread orchestrateur au répartiteur Durable Task Framework. Le répartiteur valide ensuite dans le stockage toutes les actions que la fonction d’orchestrateur a planifiées (par exemple, l’appel d’une ou plusieurs fonctions enfant ou la planification d’un minuteur durable). L’action de validation transparente s’ajoute à l’historique d’exécution de l’instance d’orchestration. L’historique est stocké dans une table de stockage. L’action de validation ajoute ensuite des messages à une file d’attente pour planifier le travail réel. À ce stade, la fonction d’orchestrateur peut être déchargée de la mémoire.

Lorsqu’une fonction d’orchestration reçoit plus de tâches à effectuer (par exemple, un message de réponse est reçu ou un minuteur durable expire), l’orchestrateur sort à nouveau de veille et réexécute toute la fonction depuis le début afin de reconstruire l’état local. Si, au cours de la réexécution, le code tente d’appeler une fonction (ou effectue toute autre tâche asynchrone), l’infrastructure Durable Task Framework consulte l’historique d’exécution de l’orchestration en cours. Si elle constate que la [fonction d’activité](durable-functions-types-features-overview.md#activity-functions) a déjà été exécutée et a produit un résultat, elle réexécute le résultat de cette fonction, et le code d’orchestrateur continue de s’exécuter. La réexécution se poursuit jusqu’à ce que le code de la fonction s’achève ou jusqu’à ce qu’il ait planifié une nouvelle tâche asynchrone.

> [!NOTE]
> Pour que le modèle de relecture fonctionne correctement et de manière fiable, le code de fonction orchestrator doit être *déterministe*. Pour plus d’informations sur les restrictions de code pour les fonctions orchestrator, consultez la rubrique [Contraintes du code des fonctions](durable-functions-code-constraints.md).

> [!NOTE]
> Si une fonction orchestrator émet des messages de journal, le comportement de relecture peut provoquer l’émission de messages de journal en double. Pour en savoir plus sur la raison pour laquelle ce comportement se produit et sur la façon de contourner ce problème, consultez la rubrique [Journalisation](durable-functions-diagnostics.md#logging).

## <a name="orchestration-history"></a>Historique d’orchestration

Le comportement de l’approvisionnement en événements du framework Durable Task est étroitement lié au code de fonction orchestrator que vous écrivez. Supposons que vous disposiez d’une fonction orchestrator de chaînage d’activités, comme la fonction orchestrator C# suivante :

```csharp
[FunctionName("E1_HelloSequence")]
public static async Task<List<string>> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var outputs = new List<string>();

    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Tokyo"));
    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Seattle"));
    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "London"));

    // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
    return outputs;
}
```

Si vous codez en JavaScript, votre fonction orchestrator de chaînage d’activités peut se présenter comme dans l’exemple de code suivant :

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const output = [];
    output.push(yield context.df.callActivity("E1_SayHello", "Tokyo"));
    output.push(yield context.df.callActivity("E1_SayHello", "Seattle"));
    output.push(yield context.df.callActivity("E1_SayHello", "London"));

    // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
    return output;
});
```

À chaque instruction `await` (C#) ou `yield` (JavaScript), le framework Durable Task crée un point de contrôle de l’état d’exécution de la fonction dans un back-end de stockage durable (généralement le Stockage Table Azure). Cet état est appelé *historique d’orchestration*.

### <a name="history-table"></a>Table d’historique

En règle générale, Durable Task Framework réalise les tâches suivantes à chaque point de contrôle :

1. Il enregistre l’historique d’exécution dans des tables de stockage Azure.
2. Il empile les messages pour les fonctions que l’orchestrateur souhaite appeler.
3. Il empile les messages pour l’orchestrateur lui-même &mdash; par exemple, les messages d’un minuteur durable.

Une fois le point de contrôle terminé, la fonction d’orchestrateur peut être supprimée de la mémoire jusqu'à ce qu’elle ait de nouvelles tâches à réaliser.

> [!NOTE]
> Le stockage Azure ne fournit aucune garantie transactionnelle entre l’enregistrement des données dans le stockage de tables et les files d’attente. Pour gérer les erreurs, le fournisseur de stockage Fonctions durables utilise les modèles de *cohérence éventuelle*. Ces modèles évitent la perte de données en cas d’incident ou de perte de connectivité au cours d’un point de contrôle.

Une fois que vous avez terminé, l’historique de la fonction présentée précédemment ressemble au tableau suivant dans le Stockage Table Azure (présentation raccourcie à des fins d’illustration) :

| PartitionKey (InstanceId)                     | Type d’événement             | Timestamp               | Entrée | Nom             | Résultat                                                    | Statut |
|----------------------------------|-----------------------|----------|--------------------------|-------|------------------|-----------------------------------------------------------|
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:32.362Z |       |                  |                                                           |                     |
| eaee885b | ExecutionStarted      | 2017-05-05T18:45:28.852Z | null  | E1_HelloSequence |                                                           |                     |
| eaee885b | TaskScheduled         | 2017-05-05T18:45:32.670Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:32.670Z |       |                  |                                                           |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:34.232Z |       |                  |                                                           |                     |
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.201Z |       |                  | """Hello Tokyo!"""                                        |                     |
| eaee885b | TaskScheduled         | 2017-05-05T18:45:34.435Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:34.435Z |       |                  |                                                           |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:34.857Z |       |                  |                                                           |                     |
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.763Z |       |                  | """Hello Seattle!"""                                      |                     |
| eaee885b | TaskScheduled         | 2017-05-05T18:45:34.857Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:34.857Z |       |                  |                                                           |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:35.032Z |       |                  |                                                           |                     |
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.919Z |       |                  | """Hello London!"""                                       |                     |
| eaee885b | ExecutionCompleted    | 2017-05-05T18:45:35.044Z |       |                  | "[""Hello Tokyo!"",""Hello Seattle!"",""Hello London!""]" | Completed           |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:35.044Z |       |                  |                                                           |                     |

Quelques remarques sur les valeurs de colonne :

* **PartitionKey** : contient l’ID d’instance de l’orchestration.
* **EventType** : représente le type de l’événement. qui peut être l’un des suivants :
  * **OrchestrationStarted** : la fonction orchestrator a repris après une expression await ou s’exécute pour la première fois. La colonne `Timestamp` indique la valeur déterministe pour les API `CurrentUtcDateTime` (.NET) `currentUtcDateTime` (JavaScript).
  * **ExecutionStarted** : la fonction orchestrator a commencé à s’exécuter pour la première fois. Cet événement contient également l’entrée de la fonction dans la colonne `Input`.
  * **TaskScheduled** : une fonction d’activité a été planifiée. Le nom de la fonction d’activité est indiqué dans la colonne `Name`.
  * **TaskCompleted** : une fonction d’activité s’est terminée. Le résultat de la fonction se trouve dans la colonne `Result`.
  * **TimerCreated** : un minuteur durable a été créé. La colonne `FireAt` contient l’heure UTC planifiée à laquelle le minuteur expire.
  * **TimerFired** : un minuteur durable s’est déclenché.
  * **EventRaised** : un événement externe a été envoyé à l’instance d’orchestration. La colonne `Name` indique le nom de l’événement et la colonne `Input` indique la charge utile de l’événement.
  * **OrchestratorCompleted** : la fonction orchestrator a attendu.
  * **ContinueAsNew** : la fonction orchestrator s’est terminée et a redémarré avec un nouvel état. La colonne `Result` contient la valeur, qui est utilisée comme entrée dans l’instance redémarrée.
  * **ExecutionCompleted** : la fonction orchestrator s’est exécutée entièrement (ou a échoué). Les sorties de la fonction ou les détails de l’erreur sont stockés dans la colonne `Result`.
* **Timestamp** : horodatage UTC de l’événement d’historique.
* **Name** : nom de la fonction qui a été appelée.
* **Entrée**: entrée au format JSON de la fonction.
* **Result** : sortie de la fonction ; autrement dit, sa valeur renvoyée.

> [!WARNING]
> Même si cette table est utile en tant qu’outil de débogage, vous ne devez pas en dépendre. Elle peut changer à mesure que l’extension Fonctions durables évolue.

Chaque fois que la fonction reprend après une expression `await` (C#) ou `yield` (JavaScript), Durable Task Framework réexécute la fonction orchestrator depuis le début. À chaque réexécution, il consulte l’historique d’exécution pour déterminer si l’opération asynchrone en cours a eu lieu.  Si l’opération a eu lieu, le framework réexécute immédiatement la sortie de cette opération et passe à l’expression `await` (C#) ou `yield` (JavaScript) suivante. Ce processus se poursuit jusqu’à ce que tout l’historique ait été réexécuté. Une fois l’historique actuel relu, les variables locales sont restaurées à leurs valeurs précédentes.

## <a name="features-and-patterns"></a>Fonctionnalités et modèles

Les sections suivantes décrivent les fonctionnalités et les modèles de fonctions orchestrator.

### <a name="sub-orchestrations"></a>Orchestrations secondaires

Les fonctions orchestrator peuvent appeler des fonctions d’activité, mais aussi d’autres fonctions orchestrator. Par exemple, vous pouvez créer une orchestration plus grande à partir d’une bibliothèque de fonctions d’orchestrateur. Vous pouvez aussi exécuter en parallèle plusieurs instances d’une fonction d’orchestrateur.

Pour obtenir plus d’informations et des exemples, consultez l’article [Orchestrations secondaires](durable-functions-sub-orchestrations.md).

### <a name="durable-timers"></a>Minuteurs durables

Les orchestrations peuvent planifier des *minuteurs durables* pour implémenter des retards ou configurer le traitement des délais d’expiration sur les actions asynchrones. Utilisez les minuteurs durables dans les fonctions de l’orchestrateur à la place de `Thread.Sleep` et `Task.Delay` (C#) ou de `setTimeout()` et `setInterval()` (JavaScript).

Pour obtenir plus d’informations et des exemples, consultez l’article [Minuteurs durables](durable-functions-timers.md).

### <a name="external-events"></a>Événements externes

Les fonctions d’orchestrateur peuvent attendre des événements externes pour mettre à jour une instance d’orchestration. Cette fonctionnalité Durable Functions est souvent utile pour gérer l’interaction humaine ou d’autres rappels externes.

Pour obtenir plus d’informations et des exemples, consultez l’article [Événements externes](durable-functions-external-events.md).

### <a name="error-handling"></a>Gestion des erreurs

Les fonctions orchestrator peuvent utiliser les fonctionnalités de gestion des erreurs du langage de programmation. Les modèles existants comme `try`/`catch` sont pris en charge dans le code d’orchestration.

Les fonctions orchestrator peuvent également ajouter des stratégies de nouvelles tentatives à l’activité ou aux fonctions orchestrator secondaires qu’elles appellent. Si une fonction d’activité ou une fonction orchestrator secondaire échoue avec une exception, la stratégie de nouvelles tentatives spécifiée peut automatiquement retarder et retenter l’exécution autant de fois que vous l’aurez spécifié.

> [!NOTE]
> S’il existe une exception non prise en charge dans une fonction orchestrator, l’instance d’orchestration se termine dans l’état `Failed`. Une instance d’orchestration ne peut pas être retentée une fois qu’elle a échoué.

Pour obtenir plus d’informations et des exemples, consultez l’article [Gestion des erreurs](durable-functions-error-handling.md).

### <a name="critical-sections-durable-functions-2x"></a>Sections critiques (Durable Functions 2. x)

Les instances d’orchestration étant à thread unique, il n’est pas nécessaire de se soucier des conditions de concurrence *dans* une orchestration. Toutefois, des conditions de concurrence sont possibles quand les orchestrations interagissent avec des systèmes externes. Pour atténuer les conditions de concurrence en cas d’interaction avec des systèmes externes, les fonctions orchestrator peuvent définir des *sections critiques* à l’aide d’une méthode `LockAsync` dans .NET.

L’exemple de code suivant illustre une fonction orchestrator qui définit une section critique. Elle entre la section critique à l’aide de la méthode `LockAsync`. Cette méthode nécessite de passer une ou plusieurs références à une [entité durable](durable-functions-entities.md), ce qui gère durablement l’état du verrou. Une seule instance de cette orchestration peut exécuter le code de la section critique à la fois.

```csharp
[FunctionName("Synchronize")]
public static async Task Synchronize(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var lockId = new EntityId("LockEntity", "MyLockIdentifier");
    using (await context.LockAsync(lockId))
    {
        // critical section - only one orchestration can enter at a time
    }
}
```

La méthode `LockAsync` acquiert un ou plusieurs verrous durables et retourne un `IDisposable` qui met fin à la section critique quand elle est supprimée. Ce résultat `IDisposable` peut être utilisé avec un bloc `using` pour obtenir une représentation syntaxique de la section critique. Quand une fonction orchestrator entre dans une section critique, une seule instance peut exécuter ce bloc de code. Toutes les autres instances qui tentent d’entrer dans la section critique sont bloquées jusqu’à ce que l’instance précédente quitte la section critique.

La fonctionnalité de section critique est également utile pour coordonner les changements apportés à des entités durables. Pour plus d’informations sur les sections critiques, consultez la rubrique [« Coordination d’entités » (entités durables)](durable-functions-entities.md#entity-coordination).

> [!NOTE]
> Les sections critiques sont disponibles dans Durable Functions 2.0 et les versions ultérieures. Actuellement, seules les orchestrations .NET implémentent cette fonctionnalité.

### <a name="calling-http-endpoints-durable-functions-2x"></a>Appel de points de terminaison HTTP (Durable Functions 2.x)

Les fonctions orchestrator ne sont pas autorisées à effectuer des E/S, comme décrit dans [Contraintes du code des fonctions orchestrator](durable-functions-code-constraints.md). La solution de contournement classique de cette limitation consiste à wrapper dans une fonction d’activité tout code qui doit effectuer des E/S. Les orchestrations qui interagissent avec des systèmes externes utilisent fréquemment des fonctions d’activité pour effectuer des appels HTTP et retourner le résultat à l’orchestration.

Pour simplifier ce modèle courant, les fonctions orchestrator peuvent utiliser la méthode `CallHttpAsync` dans .NET pour appeler directement des API HTTP. En plus de prendre en charge les modèles de requête/réponse de base, `CallHttpAsync` prend en charge la gestion automatique des modèles d’interrogation HTTP 202 asynchrones courants, ainsi que l’authentification avec des services externes à l’aide d’[identités managées](../../active-directory/managed-identities-azure-resources/overview.md).

```csharp
[FunctionName("CheckSiteAvailable")]
public static async Task CheckSiteAvailable(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    Uri url = context.GetInput<Uri>();

    // Makes an HTTP GET request to the specified endpoint
    DurableHttpResponse response = 
        await context.CallHttpAsync(HttpMethod.Get, url);

    if (response.StatusCode >= 400)
    {
        // handling of error codes goes here
    }
}
```

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const url = context.df.getInput();
    var res = yield context.df.callHttp("GET", url);
    if (res.statusCode >= 400) {
        // handling of error codes goes here
    }
});
```

Pour obtenir plus d’informations et des exemples détaillés, consultez l’article [Fonctionnalités HTTP](durable-functions-http-features.md).

> [!NOTE]
> L’appel des points de terminaison HTTP directement à partir de fonctions orchestrator est disponible dans Durable Functions 2.0 et les versions ultérieures.

### <a name="passing-multiple-parameters"></a>Transmission de plusieurs paramètres

Il n’est pas possible de passer directement plusieurs paramètres à une fonction d’activité. Il est recommandé de passer un tableau d’objets ou d’utiliser des objets [ValueTuples](https://docs.microsoft.com/dotnet/csharp/tuples) dans .NET.

Dans l’exemple suivant, de nouvelles fonctionnalités des objets [ValueTuples](https://docs.microsoft.com/dotnet/csharp/tuples) sont ajoutées avec [C# 7](https://docs.microsoft.com/dotnet/csharp/whats-new/csharp-7#tuples) :

```csharp
[FunctionName("GetCourseRecommendations")]
public static async Task<object> RunOrchestrator(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string major = "ComputerScience";
    int universityYear = context.GetInput<int>();

    object courseRecommendations = await context.CallActivityAsync<object>(
        "CourseRecommendations",
        (major, universityYear));
    return courseRecommendations;
}

[FunctionName("CourseRecommendations")]
public static async Task<object> Mapper([ActivityTrigger] IDurableActivityContext inputs)
{
    // parse input for student's major and year in university
    (string Major, int UniversityYear) studentInfo = inputs.GetInput<(string, int)>();

    // retrieve and return course recommendations by major and university year
    return new
    {
        major = studentInfo.Major,
        universityYear = studentInfo.UniversityYear,
        recommendedCourses = new []
        {
            "Introduction to .NET Programming",
            "Introduction to Linux",
            "Becoming an Entrepreneur"
        }
    };
}
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Contraintes du code d’orchestrateur](durable-functions-code-constraints.md)
