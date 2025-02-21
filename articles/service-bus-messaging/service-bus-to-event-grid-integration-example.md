---
title: "Didacticiel : Exemples d'intégration d'Azure Service Bus à Event Grid"
description: 'Didacticiel : Cet article fournit des exemples d’intégration de la messagerie Service Bus et d’Event Grid.'
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: f99766cb-8f4b-4baf-b061-4b1e2ae570e4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: tutorial
ms.date: 11/05/2019
ms.author: spelluru
ms.openlocfilehash: d3f523bbc0236536734f21870474492a61532ada
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73719027"
---
# <a name="tutorial-respond-to-azure-service-bus-events-received-via-azure-event-grid-by-using-azure-functions-and-azure-logic-apps"></a>Didacticiel : Répondre aux événements Azure Service Bus reçus via Azure Event Grid à l’aide d’Azure Functions et d’Azure Logic Apps
Dans ce tutoriel, vous allez apprendre à répondre aux événements Azure Service Bus qui sont reçus via Azure Event Grid à l’aide d’Azure Functions et d’Azure Logic Apps. Vous allez effectuer les étapes suivantes :
 
- Créer une fonction Azure de test pour déboguer et voir le flux initial d’événements à partir d’Event Grid.
- Créer une fonction Azure pour recevoir et traiter les messages Azure Service Bus en fonction des événements Event Grid.
- Créer une application logique pour répondre aux événements Event Grid.

Après avoir créé la rubrique Service Bus, Event Grid, Azure Functions et les artefacts Logic Apps, vous effectuez les actions suivantes : 

1. Envoyer des messages à une rubrique Service Bus. 
2. Vérifier que les abonnements à la rubrique ont reçu ces messages.
3. Vérifier que la fonction ou l’application logique abonnée à l’événement l’a reçu. 

## <a name="create-a-service-bus-namespace"></a>Création d’un espace de noms Service Bus
Suivez les instructions de ce tutoriel : [Démarrage rapide : utiliser le portail Azure pour créer une rubrique Service Bus et des abonnements à cette rubrique](service-bus-quickstart-topics-subscriptions-portal.md) pour effectuer les tâches suivantes :

- Créer un espace de noms Service Bus **premium**. 
- Obtenir la chaîne de connexion. 
- Créer une rubrique Service Bus.
- Créer deux abonnements à la rubrique. 

## <a name="prepare-a-sample-application-to-send-messages"></a>Préparer un exemple d’application pour envoyer des messages
Vous pouvez utiliser la méthode de votre choix pour envoyer un message à votre rubrique Service Bus. L’exemple de code figurant à la fin de cette procédure suppose que vous utilisez Visual Studio 2017.

1. Clonez [le référentiel azure-service-bus GitHub](https://github.com/Azure/azure-service-bus/).
2. Dans Visual Studio, accédez au dossier *\samples\DotNet\Microsoft.ServiceBus.Messaging\ServiceBusEventGridIntegration*, puis ouvrez le fichier *SBEventGridIntegration.sln*.
3. Accédez au projet **MessageSender**, puis sélectionnez **Program.cs**.
4. Renseignez le nom de votre rubrique Service Bus et la chaîne de connexion que vous avez obtenue à l’étape précédente :

    ```CSharp
    const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
    const string TopicName = "YOUR TOPIC NAME";
    ```
5. Générez et exécutez le programme pour envoyer des messages de test à la rubrique Service Bus. 

## <a name="set-up-a-test-function-on-azure"></a>Configurer une fonction de test sur Azure 
Avant d’étudier le scénario dans son intégralité, configurez au moins une petite fonction de test, que vous pouvez utiliser pour déboguer et voir les événements qui sont transmis. Suivez les instructions fournies dans l’article [Créer votre première fonction à l’aide du Portail Azure](../azure-functions/functions-create-first-azure-function.md) pour effectuer les tâches suivantes : 

1. Créer une application de fonction.
2. Créer une fonction déclenchée via HTTP. 

Effectuez ensuite les étapes suivantes : 


# <a name="azure-functions-v2tabv2"></a>[Azure Functions v2](#tab/v2)

1. Développez **Fonctions** dans l’arborescence, puis sélectionnez votre fonction. Remplacez le code de la fonction par le code suivant : 

    ```CSharp
    #r "Newtonsoft.Json"
    
    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;
    
    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");
        var content = req.Body;
        string jsonContent = await new StreamReader(content).ReadToEndAsync();
        log.LogInformation($"Received Event with payload: {jsonContent}");
    
        IEnumerable<string> headerValues;
        headerValues = req.Headers.GetCommaSeparatedValues("Aeg-Event-Type");
    
        if (headerValues.Count() != 0)
        {
            var validationHeaderValue = headerValues.FirstOrDefault();
            if(validationHeaderValue == "SubscriptionValidation")
            {
                var events = JsonConvert.DeserializeObject<GridEvent[]>(jsonContent);
                var code = events[0].Data["validationCode"];
                log.LogInformation("Validation code: {code}");
                return (ActionResult) new OkObjectResult(new { validationResponse = code });
            }
        }
    
        return jsonContent == null
            ? new BadRequestObjectResult("Please pass a name on the query string or in the request body")
            : (ActionResult)new OkObjectResult($"Hello, {jsonContent}");
    }
    
    public class GridEvent
    {
        public string Id { get; set; }
        public string EventType { get; set; }
        public string Subject { get; set; }
        public DateTime EventTime { get; set; }
        public Dictionary<string, string> Data { get; set; }
        public string Topic { get; set; }
    }
    
    ```
2. Sélectionnez **Enregistrer et exécuter**.

    ![Sortie d’application de fonction](./media/service-bus-to-event-grid-integration-example/function-run-output.png)
3. Sélectionnez **Obtenir l’URL de la fonction** et notez l’URL. 

    ![Obtenir l’URL de la fonction](./media/service-bus-to-event-grid-integration-example/get-function-url.png)

# <a name="azure-functions-v1tabv1"></a>[Azure Functions v1](#tab/v1)

1. Configurez la fonction pour utiliser la version **V1** : 
    1. Sélectionnez votre application de fonction dans l’arborescence, puis **Paramètres Function App**. 

        ![Paramètres Function App]()./media/service-bus-to-event-grid-integration-example/function-app-settings.png)
    2. Sélectionnez **~1** pour **Version du runtime**. 
2. Développez **Fonctions** dans l’arborescence, puis sélectionnez votre fonction. Remplacez le code de la fonction par le code suivant : 

    ```CSharp
    #r "Newtonsoft.Json"
    using System.Net;
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;
    
    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {
        log.Info("C# HTTP trigger function processed a request.");
        // parse query parameter
        var content = req.Content;
    
        string jsonContent = await content.ReadAsStringAsync(); 
        log.Info($"Received Event with payload: {jsonContent}");
    
        IEnumerable<string> headerValues;
        if (req.Headers.TryGetValues("Aeg-Event-Type", out headerValues))
        {
            var validationHeaderValue = headerValues.FirstOrDefault();
            if(validationHeaderValue == "SubscriptionValidation")
            {
            var events = JsonConvert.DeserializeObject<GridEvent[]>(jsonContent);
                 var code = events[0].Data["validationCode"];
                 return req.CreateResponse(HttpStatusCode.OK,
                 new { validationResponse = code });
            }
        }
    
        return jsonContent == null
        ? req.CreateResponse(HttpStatusCode.BadRequest, "Pass a name on the query string or in the request body")
        : req.CreateResponse(HttpStatusCode.OK, "Hello " + jsonContent);
    }
    
    public class GridEvent
    {
        public string Id { get; set; }
        public string EventType { get; set; }
        public string Subject { get; set; }
        public DateTime EventTime { get; set; }
        public Dictionary<string, string> Data { get; set; }
        public string Topic { get; set; }
    }
    ```
4. Sélectionnez **Enregistrer et exécuter**.

    ![Sortie d’application de fonction](./media/service-bus-to-event-grid-integration-example/function-run-output.png)
4. Sélectionnez **Obtenir l’URL de la fonction** et notez l’URL. 

    ![Obtenir l’URL de la fonction](./media/service-bus-to-event-grid-integration-example/get-function-url.png)

---

## <a name="connect-the-function-and-namespace-via-event-grid"></a>Connecter la fonction et l’espace de noms via Event Grid
Dans cette section, vous allez relier la fonction et l’espace de noms Service Bus à l’aide du portail Azure. 

Pour créer un abonnement Azure Event Grid, effectuez les étapes suivantes :

1. Dans le portail Azure, accédez à votre espace de noms puis, dans le volet gauche, sélectionnez **Événements**. La fenêtre de votre espace de noms s’ouvre, avec deux abonnements Event Grid affichés dans le volet droit. 
    
    ![Service Bus - page d’événements](./media/service-bus-to-event-grid-integration-example/service-bus-events-page.png)
2. Sélectionnez **+ Abonnement aux événements** dans la barre d’outils. 
3. Dans la page **Créer un abonnement aux événements**, effectuez les étapes suivantes :
    1. Entrez un **nom** pour l’abonnement. 
    2. Sélectionnez **Webhook** pour **Type de point de terminaison**. 

        ![Service Bus - abonnement Event Grid](./media/service-bus-to-event-grid-integration-example/event-grid-subscription-page.png)
    3. Choisissez **Sélectionner un point de terminaison**, collez l’URL de fonction, puis sélectionnez **Confirmer la sélection**. 

        ![Fonction - sélectionner le point de terminaison](./media/service-bus-to-event-grid-integration-example/function-select-endpoint.png)
    4. Basculez vers l’onglet **Filtres**, entrez le nom du **premier abonnement** à la rubrique Service Bus que vous avez créé plus tôt, puis sélectionnez le bouton **Créer**. 

        ![Filtre d’abonnement aux événements](./media/service-bus-to-event-grid-integration-example/event-subscription-filter.png)
4. Vérifiez que vous voyez bien l’abonnement aux événements dans la liste.

    ![Abonnement aux événements dans la liste](./media/service-bus-to-event-grid-integration-example/event-subscription-in-list.png)

## <a name="send-messages-to-the-service-bus-topic"></a>Envoyer des messages à la rubrique Service Bus
1. Exécutez l’application .NET en C#, qui envoie des messages à la rubrique Service Bus. 

    ![Sortie d’application console](./media/service-bus-to-event-grid-integration-example/console-app-output.png)
1. Dans la page de votre application Azure Function, développez **Fonctions**, développez votre **fonction**, puis sélectionnez **Monitor** (Superviser). 

    ![Superviser la fonction](./media/service-bus-to-event-grid-integration-example/function-monitor.png)

## <a name="receive-messages-by-using-azure-functions"></a>Recevoir des messages à l’aide d’Azure Functions
Dans la section précédente, vous avez étudié un scénario de test et de débogage simple, et vérifié que les événements sont transmis. 

Dans cette section, vous allez apprendre comment recevoir et traiter des messages après avoir reçu un événement.

### <a name="publish-a-function-from-visual-studio"></a>Publier une fonction à partir de Visual Studio
1. Dans la même solution Visual Studio (**SBEventGridIntegration**) que vous avez ouverte, sélectionnez **ReceiveMessagesOnEvent.cs** dans le projet **SBEventGridIntegration**. 
2. Entrez votre chaîne de connexion Service Bus dans le code suivant :

    ```Csharp
    const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
    ```
3. Téléchargez le **profil de publication** pour la fonction :
    1. Sélectionnez votre application de fonction. 
    2. Sélectionnez l’onglet **Vue d’ensemble** s’il ne l’est pas déjà. 
    3. Sélectionnez **Obtenir le profil de publication** dans la barre d’outils. 

        ![Obtenir le profil de publication pour la fonction](./media/service-bus-to-event-grid-integration-example/function-download-publish-profile.png)
    4. Enregistrez le fichier dans le dossier de votre projet. 
4. Dans Visual Studio, cliquez avec le bouton droit sur **SBEventGridIntegration** et sélectionnez **Publier**. 
5. Sélectionnez **Démarrer** dans la page **Publier**. 
6. Dans la page **Choisir une cible de publication**, effectuez les étapes suivantes, sélectionnez **Importer le profil**. 

    ![Visual Studio - bouton Importer le profil](./media/service-bus-to-event-grid-integration-example/visual-studio-import-profile-button.png)
7. Sélectionnez le **fichier de profil de publication** que vous avez téléchargé précédemment. 
8. Sélectionnez **Publier** dans la page **Publier**. 

    ![Visual Studio - Publier](./media/service-bus-to-event-grid-integration-example/select-publish.png)
9. Vérifiez que vous voyez bien la nouvelle fonction Azure **ReceiveMessagesOnEvent**. Actualisez la page si nécessaire. 

    ![Vérifier que la nouvelle fonction est créée](./media/service-bus-to-event-grid-integration-example/function-receive-messages.png)
10. Obtenez l’URL de la nouvelle fonction et notez-la. 

### <a name="event-grid-subscription"></a>Abonnement Event Grid

1. Supprimez l’abonnement Event Grid existant :
    1. Dans la page **Espace de noms Service Bus**, sélectionnez **Événements** dans le menu de gauche. 
    2. Sélectionnez l’abonnement aux événements existant. 
    3. Dans la page **Abonnement aux événements**, sélectionnez **Supprimer**.
2. Suivez les instructions de la section [Connecter la fonction et l’espace de noms via Event Grid](#connect-the-function-and-namespace-via-event-grid) pour créer un abonnement Event Grid à l’aide de la nouvelle URL de fonction.
3. Suivez les instructions de la section [Envoyer des messages à la rubrique Service Bus](#send-messages-to-the-service-bus-topic) pour envoyer des messages à la rubrique et superviser la fonction. 

## <a name="receive-messages-by-using-logic-apps"></a>Recevoir des messages à l’aide de Logic Apps
Connectez une application logique avec Azure Service Bus et Azure Event Grid en procédant comme suit :

1. Créez une application logique dans le portail Azure.
    1. Sélectionnez **+ Créer une ressource**, **Intégration**, puis **Application logique**. 
    2. Dans la page **Application logique - Créer**, entrez un **nom** pour l’application logique.
    3. Sélectionnez votre **abonnement**Azure. 
    4. Sélectionnez **Utiliser l’existant** pour **Groupe de ressources**, puis le groupe de ressources que vous avez utilisé pour d’autres ressources (telles que la fonction Azure ou l’espace de noms Service Bus) créées précédemment. 
    5. Sélectionnez l’**emplacement** de l’application logique. 
    6. Sélectionnez **Créer** pour créer l’application logique. 
2. Dans la page **Concepteur Logic Apps**, sélectionnez **Application logique vide** sous **Modèles**. 
3. Dans le concepteur, procédez comme suit :
    1. Recherchez **Event Grid**. 
    2. Sélectionnez **Quand un événement de ressource se produit (préversion) - Azure Event Grid**. 

        ![Concepteur Logic Apps - sélectionner le déclencheur Event Grid](./media/service-bus-to-event-grid-integration-example/logic-apps-event-grid-trigger.png)
4. Sélectionnez **Se connecter**, entrez vos informations d’identification Azure, puis sélectionnez **Autoriser l’accès**. 
5. Dans la page **Quand un événement de ressource se produit**, effectuez les étapes suivantes :
    1. Sélectionnez votre abonnement Azure. 
    2. Pour **Type de ressource**, sélectionnez **Microsoft.ServiceBus.Namespaces**. 
    3. Pour **Nom de la ressource**, sélectionnez votre espace de noms Service Bus. 
    4. Sélectionnez **Ajouter un nouveau paramètre**, puis **Filtre de suffixe**. 
    5. Pour **Filtre de suffixe**, entrez le nom de votre deuxième abonnement à la rubrique Service Bus. 
        ![Concepteur Logic Apps - Configurer un événement](./media/service-bus-to-event-grid-integration-example/logic-app-configure-event.png)
6. Sélectionnez **+ Nouvelle étape** dans le concepteur et procédez comme suit :
    1. Recherchez **Service Bus**.
    2. Sélectionnez **Service Bus** dans la liste. 
    3. Recherchez **Obtenir les messages** dans la liste **Actions**. 
    4. Sélectionnez **Obtenir des messages à partir d’un abonnement à une rubrique (Peek-lock)** . 

        ![Concepteur Logic Apps - action Obtenir les messages](./media/service-bus-to-event-grid-integration-example/service-bus-get-messages-step.png)
    5. Attribuez un **nom à cette connexion**. Par exemple :  **Obtenir des messages à partir de l’abonnement à la rubrique**, puis sélectionnez l’espace de noms Service Bus. 

        ![Concepteur Logic Apps - sélectionner l’espace de noms Service Bus](./media/service-bus-to-event-grid-integration-example/logic-apps-select-namespace.png) 
    6. Sélectionnez **RootManageSharedAccessKey**.

        ![Concepteur Logic Apps - sélectionner la clé d’accès partagé](./media/service-bus-to-event-grid-integration-example/logic-app-shared-access-key.png) 
    7. Sélectionnez **Create** (Créer). 
    8. Sélectionnez votre rubrique et votre abonnement. 
    
        ![Concepteur Logic Apps - sélectionner votre rubrique Service Bus et votre abonnement](./media/service-bus-to-event-grid-integration-example/logic-app-select-topic-subscription.png)
7. Sélectionnez **+ Nouvelle étape** et procédez comme suit : 
    1. Sélectionnez **Service Bus**.
    2. Sélectionnez **Compléter le message dans un abonnement à une rubrique**. 
    3. Sélectionnez votre **rubrique** Service Bus.
    4. Sélectionnez le deuxième **abonnement** à la rubrique.
    5. Pour **Jeton de verrouillage du message**, sélectionnez **Jeton de verrouillage** à partir du **contenu dynamique**. 

        ![Concepteur Logic Apps - sélectionner votre rubrique Service Bus et votre abonnement](./media/service-bus-to-event-grid-integration-example/logic-app-complete-message.png)
8. Sélectionnez **Enregistrer** dans la barre d’outils du concepteur Logic Apps pour enregistrer l’application logique. 
9. Suivez les instructions de la section [Envoyer des messages à la rubrique Service Bus](#send-messages-to-the-service-bus-topic) pour envoyer des messages à la rubrique. 
10. Basculez vers la page **Vue d’ensemble** de votre application logique. Vous voyez que l’application logique s’exécute dans **Historique des exécutions** pour les messages envoyés.

    ![Concepteur Logic Apps - l’application logique s’exécute](./media/service-bus-to-event-grid-integration-example/logic-app-runs.png)

## <a name="next-steps"></a>Étapes suivantes

* Apprenez-en plus sur [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/).
* Apprenez-en plus sur [Azure Functions](https://docs.microsoft.com/azure/azure-functions/).
* Apprenez-en plus sur [la fonctionnalité Logic Apps d’Azure App Service](https://docs.microsoft.com/azure/logic-apps/).
* En savoir plus sur [Azure Service Bus](https://docs.microsoft.com/azure/service-bus/).


[2]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid2.png
[3]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid3.png
[7]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid7.png
[8]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid8.png
[9]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid9.png
[10]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid10.png
[11]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid11.png
[12]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12.png
[12-1]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12-1.png
[12-2]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12-2.png
[13]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid13.png
[14]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid14.png
[15]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid15.png
[16]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid16.png
[17]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid17.png
[18]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid18.png
[20]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal.png
[21]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal2.png
