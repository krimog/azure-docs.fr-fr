---
title: 'Démarrage rapide : Envoyer une requête de recherche à l’API REST Recherche d’entités Bing en Java'
titleSuffix: Azure Cognitive Services
description: Utilisez ce démarrage rapide pour envoyer une requête à l’API REST Recherche d’entités Bing à l’aide de Java et recevoir une réponse JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 07/24/2019
ms.author: aahi
ms.openlocfilehash: 1f7d9620198042adc7f96e7c4aaa269f73870df0
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68478996"
---
# <a name="quickstart-send-a-search-request-to-the-bing-entity-search-rest-api-using-java"></a>Démarrage rapide : Envoyer une requête de recherche à l’API REST Recherche d’entités Bing en Java

Utilisez ce guide de démarrage rapide pour effectuer votre premier appel à l’API Recherche d’entités Bing et voir la réponse JSON. Cette application simple en Java envoie une requête de recherche d’actualités à l’API et affiche ensuite la réponse.

Alors que cette application est écrite en Java, l’API est un service web RESTful compatible avec la plupart des langages de programmation.

## <a name="prerequisites"></a>Prérequis

* Le [JDK (Java Development Kit)](https://www.oracle.com/technetwork/java/javase/downloads/)
* La [bibliothèque Gson](https://github.com/google/gson)


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Créer et initialiser un projet

1. Créez un projet Java dans votre éditeur ou IDE favori, puis importez les bibliothèques suivantes.

   ```java
   import java.io.*;
   import java.net.*;
   import java.util.*;
   import javax.net.ssl.HttpsURLConnection;
   import com.google.gson.Gson;
   import com.google.gson.GsonBuilder;
   import com.google.gson.JsonObject;
   import com.google.gson.JsonParser;
   import com.google.gson.Gson;
   import com.google.gson.GsonBuilder;
   import com.google.gson.JsonObject;
   import com.google.gson.JsonParser;
   ```

2. Dans une nouvelle classe, créez des variables pour le point de terminaison d’API, votre clé d’abonnement et une requête de recherche.

   ```java
   public class EntitySearch {

      static String subscriptionKey = "ENTER KEY HERE";
    
        static String host = "https://api.cognitive.microsoft.com";
        static String path = "/bing/v7.0/entities";
    
        static String mkt = "en-US";
        static String query = "italian restaurant near me";
   //...
    
   ```

## <a name="construct-a-search-request-string"></a>Construire une chaîne de requête de recherche

1. Créez une fonction nommée `search()` qui retourne un JSON `String`. Encodez par URL votre requête de recherche et ajoutez-la à une chaîne de paramètres avec `&q=`. Ajoutez votre marché à la chaîne avec `?mkt=`.
 
2. Créez un objet URL avec vos chaînes d’hôte, de chemin et de paramètres.
    
    ```java
    //...
    public static String search () throws Exception {
        String encoded_query = URLEncoder.encode (query, "UTF-8");
        String params = "?mkt=" + mkt + "&q=" + encoded_query;
        URL url = new URL (host + path + params);
    //...
    ```
      
## <a name="send-a-search-request-and-receive-a-response"></a>Envoyer une requête de recherche et recevoir une réponse

1. Dans la fonction `search()` créée ci-dessus, créez un objet `HttpsURLConnection` avec `url.openCOnnection()`. Définissez la méthode de requête sur `GET` et ajoutez votre clé d’abonnement à l’en-tête `Ocp-Apim-Subscription-Key`.

    ```java
    //...
    HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
    connection.setRequestMethod("GET");
    connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);
    connection.setDoOutput(true);
    //...
    ```

2. Créez un `StringBuilder`. Utilisez un nouveau `InputStreamReader` comme paramètre lors de l’instanciation de `BufferedReader` pour lire la réponse de l’API.  
    
    ```java
    //...
    StringBuilder response = new StringBuilder ();
    BufferedReader in = new BufferedReader(
        new InputStreamReader(connection.getInputStream()));
    //...
    ```

3. Créez un objet `String` pour stocker la réponse de `BufferedReader`. Itérez en son sein et ajoutez chaque ligne à la chaîne. Ensuite, fermez le lecteur, puis retournez la réponse. 
    
    ```java
    String line;
    
    while ((line = in.readLine()) != null) {
      response.append(line);
    }
    in.close();
    
    return response.toString();
    ```

## <a name="format-the-json-response"></a>Mettre en forme la réponse JSON

1. Créez une fonction nommée `prettify` pour mettre en forme la réponse JSON. Créez un `JsonParser` et appelez `parse()` sur le texte json, puis stockez-le en tant qu’objet JSON. 

2. Utilisez la bibliothèque Gson pour créer un `GsonBuilder()` et utilisez `setPrettyPrinting().create()` pour mettre en forme le json. Ensuite, retournez-le.    
  
   ```java
   //...
   public static String prettify (String json_text) {
    JsonParser parser = new JsonParser();
    JsonObject json = parser.parse(json_text).getAsJsonObject();
    Gson gson = new GsonBuilder().setPrettyPrinting().create();
    return gson.toJson(json);
   }
   //...
   ```

## <a name="call-the-search-function"></a>Appeler la fonction de recherche

1. À partir de la méthode main de votre projet, appelez `search()` et utilisez `prettify()` pour mettre en forme le texte.
    
    ```java
        public static void main(String[] args) {
            try {
                String response = search ();
                System.out.println (prettify (response));
            }
            catch (Exception e) {
                System.out.println (e);
            }
        }
    ```

## <a name="example-json-response"></a>Exemple de réponse JSON

Une réponse correcte est retournée au format JSON, comme dans l’exemple suivant : 

```json
{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "italian restaurant near me",
    "askUserForLocation": true
  },
  "places": {
    "value": [
      {
        "_type": "LocalBusiness",
        "webSearchUrl": "https://www.bing.com/search?q=sinful+bakery&filters=local...",
        "name": "Liberty's Delightful Sinful Bakery & Cafe",
        "url": "https://www.contoso.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98112",
          "addressCountry": "US",
          "neighborhood": "Madison Park"
        },
        "telephone": "(800) 555-1212"
      },

      . . .
      {
        "_type": "Restaurant",
        "webSearchUrl": "https://www.bing.com/search?q=Pickles+and+Preserves...",
        "name": "Munson's Pickles and Preserves Farm",
        "url": "https://www.princi.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness",
            "Restaurant"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98101",
          "addressCountry": "US",
          "neighborhood": "Capitol Hill"
        },
        "telephone": "(800) 555-1212"
      },
      
      . . .
    ]
  }
}
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer une application web monopage](../tutorial-bing-entities-search-single-page-app.md)

* [Qu’est-ce que l’API Recherche d’entités Bing ?](../overview.md )
* [Informations de référence sur l’API Recherche d’entités Bing](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference)
