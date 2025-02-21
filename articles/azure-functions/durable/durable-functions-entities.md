---
title: Entités durables - Azure Functions
description: Découvrez ce que sont les entités durables et comment les utiliser dans l’extension Durable Functions pour Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: overview
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 62ca71e1b42e000f7528a2963793f9bf40663bf3
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818515"
---
# <a name="entity-functions"></a>Fonctions d’entité

Les fonctions d’entité définissent les opérations pour la lecture et la mise à jour de petits éléments d’état, connus sous le nom *d’entités durables*. Comme les fonctions d’orchestrateur, les fonctions d’entité sont des fonctions ayant un type spécial de déclencheur, *déclencheur d’entité*. Contrairement aux fonctions orchestrator, les fonctions d’entité gèrent l’état d’une entité explicitement, plutôt que de représenter implicitement l’état via le flux de contrôle.
Les entités offrent un moyen d’effectuer un scale-out des applications, en distribuant le travail entre plusieurs entités, chacune avec un état de taille modeste.

> [!NOTE]
> Les fonctions d’entité et les fonctionnalités associées sont uniquement disponibles dans Durable Functions 2.0 et versions ultérieures.

## <a name="general-concepts"></a>Concepts généraux

Les entités se comportent un peu comme de minuscules services qui communiquent par le biais de messages. Chaque entité a une identité unique et un état interne (s’il existe). À l’instar des services ou des objets, les entités effectuent des opérations quand elles y sont invitées. Quand elle s’exécute, une opération peut mettre à jour l’état interne de l’entité. Elle peut également appeler des services externes et attendre une réponse. Les entités communiquent avec d’autres entités, orchestrations et clients à l’aide de messages qui sont envoyés implicitement par le biais de files d’attente fiables. 

Pour éviter les conflits, toutes les opérations effectuées sur une entité unique sont assurées de s’exécuter en série, autrement dit l’une après l’autre. 

### <a name="entity-id"></a>L’ID d’entité
Les entités sont accessibles par le biais d’un identificateur unique, l’*ID d’entité*. Un ID d’entité est simplement une paire de chaînes qui identifie de façon unique une instance d’entité. Elle comprend :

* un **nom d’entité** : un nom qui identifie le type d’entité (par exemple, « Compteur »). Ce nom doit correspondre au nom de la fonction d’entité qui implémente l’entité. Il ne respecte pas la casse.
* une **clé d’entité** : une chaîne qui identifie de façon unique l’entité parmi toutes les autres entités du même nom (par exemple, un GUID).

Par exemple, une fonction d’entité *Compteur* peut être utilisée pour calculer les points dans un jeu en ligne. Chaque instance du jeu aura un ID d’entité unique, tel que `@Counter@Game1`, `@Counter@Game2`, et ainsi de suite. Toutes les opérations qui ciblent une entité particulière nécessitent de spécifier un ID d’entité comme paramètre.

### <a name="entity-operations"></a>Opérations d’entité ###

Pour appeler une opération sur une entité, vous devez spécifier :

* L’*ID d’entité* de l’entité cible.
* Le *nom de l’opération*, une chaîne qui spécifie l’opération à effectuer. Par exemple, l’entité Counter peut prendre en charge les opérations « add », « get » ou « reset ».
* L’*entrée d’opération*, qui est un paramètre d’entrée facultatif pour l’opération. Par exemple, l’opération « add » peut prendre une valeur entière comme entrée.

Les opérations peuvent retourner une valeur de résultat ou un résultat d’erreur (par exemple une erreur JavaScript ou une exception .NET). Ce résultat ou cette erreur peut être observé par les orchestrations qui ont appelé l’opération.

Une opération d’entité peut également créer, lire, mettre à jour et supprimer l’état de l’entité. L’état de l’entité est toujours persistant dans le stockage.

## <a name="defining-entities"></a>Définition d’entités

Nous proposons actuellement deux API distinctes pour définir des entités.

Une **syntaxe basée sur la fonction**, où les entités sont représentées en tant que fonctions et les opérations sont distribuées de manière explicite par l’application. Cette syntaxe fonctionne bien pour les entités avec un état simple, peu d’opérations ou un ensemble dynamique d’opérations (comme dans les frameworks d’application). Toutefois, sa gestion peut être fastidieuse, car elle n’intercepte pas les erreurs de type au moment de la compilation.

Une **syntaxe basée sur la classe**, où les entités et les opérations sont représentées par des classes et des méthodes. Cette syntaxe produit un code plus facile à lire et permet d’appeler des opérations de façon sécurisée. La syntaxe basée sur la classe n’est qu’une fine couche par-dessus la syntaxe basée sur la fonction ; ainsi, les deux variantes peuvent être utilisées de manière interchangeable dans la même application.

### <a name="example-function-based-syntax---c"></a>Exemple : Syntaxe basée sur les fonctions - C#

Le code suivant est un exemple d’entité *Counter* simple implémentée en tant que fonction durable. Cette fonction définit trois opérations (`add`, `reset` et `get`), chacune d’elles opérant sur un état d’entier.

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            ctx.SetState(ctx.GetState<int>() + ctx.GetInput<int>());
            break;
        case "reset":
            ctx.SetState(0);
            break;
        case "get":
            ctx.Return(ctx.GetState<int>());
            break;
    }
}
```

Pour plus d’informations sur la syntaxe basée sur la fonction et sur son utilisation, consultez [Syntaxe basée sur la fonction](durable-functions-dotnet-entities.md#function-based-syntax).

### <a name="example-class-based-syntax---c"></a>Exemple : Syntaxe basée sur la classe - C#

L’exemple suivant est une implémentation équivalente de l’entité `Counter` à l’aide de classes et de méthodes.

```csharp
[JsonObject(MemberSerialization.OptIn)]
public class Counter
{
    [JsonProperty("value")]
    public int CurrentValue { get; set; }

    public void Add(int amount) => this.CurrentValue += amount;

    public void Reset() => this.CurrentValue = 0;

    public int Get() => this.CurrentValue;

    [FunctionName(nameof(Counter))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<Counter>();
}
```

L’état de cette entité est un objet de type `Counter`, qui contient un champ qui stocke la valeur actuelle du compteur. Pour rendre cet objet persistant dans le stockage, il est sérialisé et désérialisé par la bibliothèque [Json.NET](https://www.newtonsoft.com/json). 

Pour plus d’informations sur la syntaxe basée sur la classe et sur son utilisation, consultez [Définition des classes d’entités](durable-functions-dotnet-entities.md#defining-entity-classes).

### <a name="example-javascript-entity"></a>Exemple : Entité JavaScript

Les entités durables sont disponibles dans JavaScript à partir de la version **1.3.0** du package NPM `durable-functions`. Le code suivant est l’entité *Counter* implémentée en tant que fonction durable écrite en JavaScript.

**function.json**
```json
{
  "bindings": [
    {
      "name": "context",
      "type": "entityTrigger",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

**index.js**
```javascript
const df = require("durable-functions");

module.exports = df.entity(function(context) {
    const currentValue = context.df.getState(() => 0);
    switch (context.df.operationName) {
        case "add":
            const amount = context.df.getInput();
            context.df.setState(currentValue + amount);
            break;
        case "reset":
            context.df.setState(0);
            break;
        case "get":
            context.df.return(currentValue);
            break;
    }
});
```

## <a name="accessing-entities"></a>Accès aux entités

Les entités sont accessibles à l’aide d’une communication unidirectionnelle ou bidirectionnelle. Nous utilisons la terminologie suivante pour distinguer : 

* L’**appel** d’une entité signifie que nous utilisons une communication bidirectionnelle (aller-retour) : nous envoyons un message d’opération à l’entité, puis nous attendons le message de réponse avant de continuer. Le message de réponse peut fournir une valeur de résultat ou un résultat d’erreur (par exemple une erreur JavaScript ou une exception .NET). Ce résultat ou cette erreur est ensuite observé par l’appelant.
* La **signalisation** d’une entité signifie que nous utilisons une communication unidirectionnelle (déclenchement et oubli) : nous envoyons un message d’opération, mais n’attendons pas de réponse. Bien que la remise finale du message soit garantie, l’expéditeur ne sait pas quand et ne peut pas observer de valeur de résultat ou d’erreur.

Les entités sont accessibles à partir de fonctions clientes, de fonctions orchestrator ou de fonctions d’entité. Toutes les formes de communication ne sont pas prises en charge par tous les contextes :

* À partir de clients, vous pouvez *signaler* des entités et vous pouvez *lire* l’état de l’entité.
* À partir d’orchestrations, vous pouvez *signaler* des entités et vous pouvez *appeler* des entités.
* À partir d’entités, vous pouvez *signaler* des entités.

Vous trouverez ci-dessous quelques exemples illustrant les différentes façons d’accéder aux entités.

> [!NOTE]
> Par souci de simplicité, les exemples ci-dessous illustrent la syntaxe faiblement typée pour l’accès aux entités. En général, nous recommandons d’[accéder aux entités par le biais d’interfaces](durable-functions-dotnet-entities.md#accessing-entities-through-interfaces), car cela procure davantage de contrôle de type.

### <a name="example-client-signals-an-entity"></a>Exemple : le client signale une entité

Pour accéder aux entités à partir d’une fonction Azure ordinaire, également connue sous le nom de *fonction cliente*, utilisez la [liaison de sortie du client d’entité](durable-functions-bindings.md#entity-client). L’exemple suivant montre une fonction déclenchée par une file d’attente qui *signale* une entité à l’aide de cette liaison.

```csharp
[FunctionName("AddFromQueue")]
public static Task Run(
    [QueueTrigger("durable-function-trigger")] string input,
    [DurableClient] IDurableEntityClient client)
{
    // Entity operation input comes from the queue message content.
    var entityId = new EntityId(nameof(Counter), "myCounter");
    int amount = int.Parse(input);
    return client.SignalEntityAsync(entityId, "Add", amount);
}
```

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    await context.df.signalEntity(entityId, "add", 1);
};
```

Le terme *signal* signifie que l’appel de l’API d’entité est unidirectionnel et asynchrone. Il n’est pas possible pour une fonction *cliente* de savoir quand l’entité a traité l’opération. De plus, la fonction cliente ne peut pas observer les valeurs de résultat ou les exceptions. 

### <a name="example-client-reads-an-entity-state"></a>Exemple : le client lit un état d’entité

Les fonctions clientes peuvent également interroger l’état d’une entité, comme illustré dans l’exemple suivant :

```csharp
[FunctionName("QueryCounter")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function)] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client)
{
    var entityId = new EntityId(nameof(Counter), "myCounter");
    JObject state = await client.ReadEntityStateAsync<JObject>(entityId);
    return req.CreateResponse(HttpStatusCode.OK, state);
}
```

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    return context.df.readEntityState(entityId);
};
```

Les requêtes d’état d’entité sont envoyées au magasin de suivi durable et retournent l’état *persistant* le plus récent de l’entité . Cet état est toujours un état « validé » ; autrement dit, il ne s’agit jamais d’un état intermédiaire temporaire adopté au milieu de l’exécution d’une opération. Toutefois, il est possible que cet état soit obsolète par rapport à l’état en mémoire de l’entité. Seules les orchestrations peuvent lire l’état en mémoire d’une entité, comme décrit dans la section suivante.

### <a name="example-orchestration-signals-and-calls-an-entity"></a>Exemple : l’orchestration signale et appelle une entité

Les fonctions orchestrator peuvent accéder à des entités à l’aide d’API sur la [liaison du déclencheur d’orchestration](durable-functions-bindings.md#orchestration-trigger). L’exemple de code suivant montre une fonction orchestrator qui *appelle* et *signale* une entité *Counter* (Compteur).

```csharp
[FunctionName("CounterOrchestration")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var entityId = new EntityId(nameof(Counter), "myCounter");

    // Two-way call to the entity which returns a value - awaits the response
    int currentValue = await context.CallEntityAsync<int>(entityId, "Get");
    if (currentValue < 10)
    {
        // One-way signal to the entity which updates the value - does not await a response
        context.SignalEntity(entityId, "Add", 1);
    }
}
```

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    const entityId = new df.EntityId("Counter", "myCounter");

    // Two-way call to the entity which returns a value - awaits the response
    currentValue = yield context.df.callEntity(entityId, "get");
    if (currentValue < 10) {
        // One-way signal to the entity which updates the value - does not await a response
        yield context.df.signalEntity(entityId, "add", 1);
    }
});
```

Seules les orchestrations peuvent appeler des entités et obtenir une réponse, qui peut être une valeur de retour ou une exception. Les fonctions clientes utilisant la[liaison cliente](durable-functions-bindings.md#entity-client) peuvent uniquement *signaler* des entités.

> [!NOTE]
> Appeler une entité à partir d’une fonction orchestrator revient à appeler une [fonction d’activité](durable-functions-types-features-overview.md#activity-functions) à partir d’une fonction orchestrator. La principale différence réside dans le fait que les fonctions d’entité sont des objets durables avec une adresse (l’*ID d’entité*) et qu’elles prennent en charge la spécification d’un nom d’opération. D’autre part, les fonctions d’activité sont sans état et n’intègrent pas le concept d’opérations.

### <a name="example-entity-signals-an-entity"></a>Exemple : l’entité signale une entité

Une fonction d’entité peut envoyer des signaux à d’autres entités (ou même à elle-même !) pendant qu’elle exécute une opération.
Par exemple, nous pouvons modifier l’exemple d’entité Counter ci-dessus pour qu’elle envoie un signal « milestone-reached » à une entité de supervision quand le compteur atteint la valeur 100 :

```csharp
   case "add":
        var currentValue = ctx.GetState<int>();
        var amount = ctx.GetInput<int>();
        if (currentValue < 100 && currentValue + amount >= 100)
        {
            ctx.SignalEntity(new EntityId("MonitorEntity", ""), "milestone-reached", ctx.EntityKey);
        }

        ctx.SetState(currentValue + amount);
        break;
```

```javascript
    case "add":
        const amount = context.df.getInput();
        if (currentValue < 100 && currentValue + amount >= 100) {
            const entityId = new df.EntityId("MonitorEntity", "");
            context.df.signalEntity(entityId, "milestone-reached", context.df.instanceId);
        }
        context.df.setState(currentValue + amount);
        break;
```

## <a name="entity-coordination"></a>Coordination d’entités

Il peut arriver que vous deviez coordonner des opérations entre plusieurs entités. Par exemple, dans une application bancaire, vous pouvez avoir des entités représentant des comptes bancaires individuels. Lors du transfert de fonds d’un compte vers un autre, vous devez vérifier que le compte _source_ dispose des fonds suffisants et que les mises à jour des comptes _source_ et de _destination_ sont effectuées de façon cohérente au niveau transactionnel.

### <a name="example-transfer-funds-c"></a>Exemple : Transférer des fonds (C#)

L’exemple de code suivant transfère des fonds entre deux entités de _compte_ à l’aide d’une fonction orchestrator. La coordination de mises à jour d’entités nécessite l’utilisation de la méthode `LockAsync` pour créer une _section critique_ dans l’orchestration :

> [!NOTE]
> Par souci de simplicité, cet exemple réutilise l’entité `Counter` définie précédemment. Toutefois, dans une application réelle, il serait préférable de définir plutôt une entité `BankAccount` plus détaillée.

```csharp
// This is a method called by an orchestrator function
public static async Task<bool> TransferFundsAsync(
    string sourceId,
    string destinationId,
    int transferAmount,
    IDurableOrchestrationContext context)
{
    var sourceEntity = new EntityId(nameof(Counter), sourceId);
    var destinationEntity = new EntityId(nameof(Counter), destinationId);

    // Create a critical section to avoid race conditions.
    // No operations can be performed on either the source or
    // destination accounts until the locks are released.
    using (await context.LockAsync(sourceEntity, destinationEntity))
    {
        ICounter sourceProxy = 
            context.CreateEntityProxy<ICounter>(sourceEntity);
        ICounter destinationProxy =
            context.CreateEntityProxy<ICounter>(destinationEntity);

        int sourceBalance = await sourceProxy.Get();

        if (sourceBalance >= transferAmount)
        {
            await sourceProxy.Add(-transferAmount);
            await destinationProxy.Add(transferAmount);

            // the transfer succeeded
            return true;
        }
        else
        {
            // the transfer failed due to insufficient funds
            return false;
        }
    }
}
```

Dans .NET, `LockAsync` retourne un `IDisposable` qui met fin à la section critique quand elle est supprimée. Ce résultat `IDisposable` peut être utilisé avec un bloc `using` pour obtenir une représentation syntaxique de la section critique.

Dans l’exemple précédent, une fonction orchestrator a transféré des fonds d’une entité _source_ vers une entité de _destination_. La méthode `LockAsync` a verrouillé les entités de compte _source_ et de _destination_. Ce verrouillage garantissait qu’aucun autre client ne pouvait interroger ou modifier l’état de l’un des comptes tant que la logique d’orchestration n’a pas quitté la _section critique_ à la fin de l’instruction `using`. Ce comportement empêche que le compte _source_ soit à découvert.

> [!NOTE] 
> Quand une orchestration se termine (soit normalement, soit avec une erreur), toutes les sections critiques en cours sont arrêtées de façon implicite et tous les verrous sont libérés.

### <a name="critical-section-behavior"></a>Comportement de la section critique

La méthode `LockAsync` crée une _section critique_ dans une orchestration. Ces _sections critiques_ empêchent d’autres orchestrations d’apporter des changements faisant double emploi à un ensemble d’entités spécifié. En interne, l’API `LockAsync` envoie des opérations de « verrou » aux entités et retourne un résultat quand elle reçoit un message de réponse « verrou acquis » de chacune de ces mêmes entités. Les opérations *verrouiller* et *déverrouiller* sont toutes deux des opérations intégrées prises en charge par toutes les entités.

Aucune opération d’autres clients n’est autorisée sur une entité pendant qu’elle est verrouillée. Ce comportement garantit qu’une seule instance d’orchestration peut verrouiller une entité à la fois. Si un appelant tente d’appeler une opération sur une entité alors qu’elle est verrouillée par une orchestration, cette opération est placée dans une *file d’attente des opérations en attente*. Aucune opération en attente n’est traitée tant que l’orchestration maintenant le verrou n’a pas libéré ce dernier.

> [!NOTE] 
> Cela diffère légèrement des primitives de synchronisation utilisées dans la plupart des langages de programmation, comme l’instruction `lock` en C#. Par exemple, en C#, l’instruction `lock` doit être utilisée par tous les threads afin de garantir une synchronisation correcte entre plusieurs threads. Toutefois, les entités n’exigent pas que tous les appelants _verrouillent_ explicitement une entité. Si un appelant verrouille une entité, toutes les autres opérations sur cette entité sont bloquées et placées en file d’attente derrière ce verrou.

Les verrous sur les entités sont durables. Par conséquent, ils persistent même si le processus en cours d’exécution est recyclé. Les verrous sont conservés en interne dans le cadre de l’état durable d’une entité.

Contrairement aux transactions, les sections critiques n’annulent pas automatiquement les modifications en cas d’erreur. Au lieu de cela, toute gestion des erreurs (restauration, nouvelle tentative ou autre) doit être explicitement codée, par exemple en interceptant les erreurs ou les exceptions. Ce choix de conception est intentionnel. L’annulation automatique de tous les effets d’une orchestration est en général difficile, voire impossible, car les orchestrations peuvent exécuter des activités et effectuer des appels à des services externes qui ne peuvent pas être restaurés. De plus, les tentatives de restauration peuvent elles-mêmes échouer et nécessiter une gestion supplémentaire des erreurs.

### <a name="critical-section-rules"></a>Règles relatives aux sections critiques

Contrairement aux primitives de verrouillage de bas niveau dans la plupart des langages de programmation, les sections critiques sont **garanties contre le blocage**. Pour éviter les blocages, nous appliquons les restrictions suivantes : 

* Les sections critiques ne peuvent pas être imbriquées.
* Les sections critiques ne peuvent pas créer de sous-orchestrations.
* Les sections critiques peuvent appeler uniquement les entités qu’elles ont verrouillées.
* Les sections critiques ne peuvent pas appeler la même entité avec plusieurs appels parallèles.
* Les sections critiques peuvent signaler uniquement les entités qui n’ont pas été verrouillées.

Toute violation de ces règles provoque une erreur d’exécution (par exemple `LockingRulesViolationException` dans .NET) qui comprend un message expliquant quelle règle a été rompue.

## <a name="comparison-with-virtual-actors"></a>Comparaison avec les acteurs virtuels

La plupart des fonctionnalités des entités durables s’inspirent du [modèle d’acteur](https://en.wikipedia.org/wiki/Actor_model). Si vous êtes déjà familiarisé avec les acteurs, il se peut que vous reconnaissiez un grand nombre des concepts décrits dans cet article. Les entités durables sont particulièrement similaires aux [acteurs virtuels](https://research.microsoft.com/projects/orleans/), ou *grains*, tels que popularisés par le [projet Orleans](http://dotnet.github.io/orleans/). Par exemple :

* Les entités durables sont adressables via un *ID d’entité*.
* Les opérations sur les entités durables s’exécutent en série, une à la fois, afin d’éviter les conditions de concurrence.
* Les entités durables sont créées implicitement quand elles sont appelées ou signalées.
* Lorsque vous n'exécutez pas d’opérations, les entités durables sont déchargées silencieusement de la mémoire.

Il existe cependant des différences importantes, qui sont à noter :

* Les entités durables donnent la priorité à la *durabilité* sur la *latence*, et peuvent par conséquent ne pas convenir aux applications avec des besoins de latence stricts.
* Les entités durables n’ont pas de délai d’expiration intégré pour les messages. Dans Orleans, tous les messages expirent après une durée configurable (30 secondes par défaut).
* Les messages envoyés entre entités sont remis de façon fiable et ordonnée. Dans Orleans, la livraison fiable ou chronologique est prise en charge pour le contenu envoyé par le biais de flux, mais n’est pas garantie pour tous les messages entre les grains.
* Les modèles de demande/réponse dans les entités sont limités aux orchestrations. À partir de l’intérieur des entités, seule la messagerie unidirectionnelle (également appelée « signalisation ») est autorisée, comme dans le modèle d’acteur d’origine, et contrairement aux grains dans Orleans. 
* Les entités durables ne subissent pas de blocage. Dans Orleans, des blocages peuvent se produire (et ne sont pas résolus tant que les messages n’ont pas expiré).
* Les entités durables, qui peuvent être utilisées conjointement avec des orchestrations durables, prennent en charge les mécanismes de verrouillage distribués. 


## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Lire le Guide du développeur relatif aux entités durables dans .NET](durable-functions-dotnet-entities.md)

> [!div class="nextstepaction"]
> [En savoir plus sur les hubs de tâches](durable-functions-task-hubs.md)
