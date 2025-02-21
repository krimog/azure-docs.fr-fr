---
title: Intégration de partenaire d’imagerie
description: Décrit l’intégration de partenaire d’imagerie
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 788ffd9e7036996f6ac1bc7fcbc33137aca40ee2
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132019"
---
# <a name="imagery-partner-integration"></a>Intégration de partenaire d’imagerie

Cet article explique comment utiliser le composant Azure FarmBeats Translator pour envoyer des données d’imagerie à FarmBeats. Les données d’imagerie agricole peuvent provenir de diverses sources, notamment de caméras multispectrales, de satellites et de drones. Les partenaires d’imagerie agricole peuvent s’intégrer à FarmBeats afin de fournir aux clients des cartes personnalisées pour leurs fermes.

Une fois les données disponibles, elles peuvent être visualisées à l’aide de FarmBeats Accelerator. Par ailleurs, les entreprises agricoles et intégrateurs systèmes clients peuvent les utiliser pour la fusion de données et la création de modèles ML/IA (machine learning/intelligence artificielle).

FarmBeats offre les possibilités suivantes :

- Définir des types, une source et un format de fichier d’image personnalisés à l’aide des API Extended Type
- Ingérer des données d’imagerie de diverses sources avec les API Scene et SceneFile.

Les informations qui suivent expliquent comment récupérer tout type d’imagerie dans le système FarmBeats.

Quand vous sélectionnez la section Drone Imagery, une fenêtre indépendante s’ouvre avec une image haute résolution de l’orthophotographie du drone. Vous pouvez accéder au logiciel du partenaire, qui vous permet de planifier les vols de drone et de récupérer les données brutes. Vous l’utiliserez également pour la planification des trajectoires et l’assemblage des images orthophotographiques.

Les partenaires de drone doivent permettre aux clients de lier leur compte client à leur instance FarmBeats sur Azure.

Vous devez utiliser les informations d’identification suivantes dans le logiciel du partenaire de drone pour la liaison à FarmBeats :

- Point de terminaison d’API
- ID client
- ID client
- Clé secrète client

## <a name="api-development"></a>Développement d’API

Les API incluent une documentation technique Swagger. Consultez [Swagger](https://aka.ms/FarmBeatsDatahubSwagger) pour plus d’informations sur les API et les requêtes/réponses correspondantes.

## <a name="authentication"></a>Authentication

FarmBeats s’appuie sur l’authentification [Active Directory](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization) de Microsoft Azure. Azure App Service offre une prise en charge intégrée de l’authentification et de l’autorisation. 

Pour plus d’informations, consultez [Azure Active Directory](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization).   

Le hub de données FarmBeats utilise l’authentification de porteur, qui nécessite les informations d’identification suivantes :

- ID client
- Clé secrète client
- ID client

À l’aide des informations d’identification ci-dessus, l’appelant peut demander un jeton d’accès, qui doit être envoyé dans les requêtes d’API suivantes (dans la section d’en-tête) comme suit :

```
headers = {"Authorization": "Bearer " + access_token, …} 
```

Voici un exemple de code Python qui récupère le jeton d’accès. Vous pouvez ensuite utiliser le jeton pour les appels d’API suivants à FarmBeats :   
import azure 

```python
from azure.common.credentials import ServicePrincipalCredentials 
import adal 
#FarmBeats API Endpoint 
ENDPOINT = "https://<yourdatahub>.azurewebsites.net"   
CLIENT_ID = "<Your Client ID>"   
CLIENT_SECRET = "<Your Client Secret>"   
TENANT_ID = "<Your Tenant ID>" 
AUTHORITY_HOST = 'https://login.microsoftonline.com' 
AUTHORITY = AUTHORITY_HOST + '/' + TENANT_ID 
#Authenticating with the credentials 
context = adal.AuthenticationContext(AUTHORITY) 
token_response = context.acquire_token_with_client_credentials(ENDPOINT, CLIENT_ID, CLIENT_SECRET) 
#Should get an access token here 
access_token = token_response.get('accessToken') 
```

## <a name="http-request-headers"></a>En-têtes de requête HTTP

Voici les en-têtes de requête les plus courants qui doivent être spécifiés lors d’un appel d’API au hub de données FarmBeats :

**En-tête** | **Description et exemple**
--- | ---
Content-Type  | Le format de la requête (Content-Type: application/<format>) pour l’API du hub de données FarmBeats est JSON. Content-Type: application/json
Authorization | Spécifie le jeton d’accès requis pour effectuer un appel d’API. Autorisation : Bearer <jeton-accès>
Acceptation  | Format de la réponse. Pour les API du hub de données FarmBeats, le format est JSON. Accept: application/json


## <a name="api-requests"></a>Requêtes d’API

Pour effectuer une requête d’API REST, vous devez associer la méthode HTTP (GET/POST/PUT), l’URL du service d’API, l’URI de la ressource (pour l’interrogation, l’envoi de données, la mise à jour ou la suppression) et un ou plusieurs en-têtes de requête HTTP.

Si vous le souhaitez, vous pouvez inclure des paramètres de requête à des appels GET pour filtrer les données dans les réponses, en limiter la taille et les trier.

L’exemple de requête ci-dessous porte sur l’obtention de la liste des appareils :

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H
"Content-Type: application/json" -H
"Authorization: Bearer <Access-Token>”
```

La plupart des appels GET, POST et PUT nécessitent un corps de requête JSON.

L’exemple de requête ci-dessous porte sur la création d’un appareil (qui comporte un code JSON d’entrée avec le corps de la requête).


```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  
"accept: application/json" -H  
"Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d
"{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  
\"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

## <a name="data-format"></a>Format de données

JSON (JavaScript Object Notation) est un format de données courant, indépendant du langage, qui fournit une représentation textuelle simple de structures de données arbitraires. Pour plus d’informations, consultez [JSON.org](https://JSON.org).

## <a name="ingesting-imagery-into-farmbeats"></a>Ingestion de données d’imagerie dans FarmBeats

Une fois que le partenaire dispose d’informations d’identification pour se connecter au hub de données FarmBeats, il effectue les opérations suivantes dans le composant Translator :

1.  Il crée un type étendu pour les champs suivants en fonction du type d’imagerie qui sera chargé :

    - Source de la scène : par exemple, <drone_nom_partenaire>
    - Type de scène : Par exemple, <drone>
    - Type de fichier de scène : Par exemple, <chlorophyll index>
    - Type de contenu de fichier de scène : par exemple, <image/tiff>

2.  Il appelle l’API Farms pour récupérer la liste des fermes dans le système Azure FarmBeats.
3.  Il donne au client la possibilité de choisir une ferme unique dans la liste des fermes.

    Le système partenaire doit afficher la ferme dans le logiciel partenaire. Il permet ainsi de planifier les trajectoires, de procéder au vol du drone et de collecter les images.

4.  Il appelle l’API Scene et entre les informations requises pour créer une scène avec un SceneID unique.
5.  Il reçoit une URL SAS d’objet blob pour charger les images requises dans le hub de données FarmBeats dans le contexte de la ferme choisie, dans le système FarmBeats.

Voici un flux détaillé sur les appels d’API :

### <a name="step-1-extendedtype"></a>Étape 1 : ExtendedType

Dans l’API ExtendedType, vérifiez si le type et la source du fichier sont disponibles sur FarmBeats. Pour cela, vous pouvez faire un appel GET sur l’API /ExtendedType.

Les valeurs définies par le système sont les suivantes :

```json
{
  "items": [
    {
      "id": "ae2aa527-7d27-42e2-8fcf-841937c651d7",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-10-25T09:47:42.5910344Z",
      "key": "SceneFileType",
      "value": [
        "evi",
        "ndvi",
        "ndwi",
        "tci",
        "soil-moisture",
        "sensor-placement",
        "sentinel-b01",
        "sentinel-b02",
        "sentinel-b03",
        "sentinel-b04",
        "sentinel-b05",
        "sentinel-b06",
        "sentinel-b07",
        "sentinel-b08",
        "sentinel-b8a",
        "sentinel-b09",
        "sentinel-b10",
        "sentinel-b11",
        "sentinel-b12",
        "cloud-mask",
        "farm-mask",
        "image/tiff",
        "orthomosaic"
      ],
      "description": "List of scene file types available in system. User can add more values."
    },
    {
      "id": "b7824d2e-3e43-46d3-8fd4-ec7ba8918d84",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-10-25T09:45:44.8914691Z",
      "key": "SceneFileContentType",
      "value": [
        "image/tiff",
        "image/png",
        "image/jpeg",
        "text/csv",
        "text/plain",
        "text/tab-separated-values",
        "application/json",
        "application/octet-stream",
        "orthomosaic"
      ],
      "description": "List of scene file content types available in system. User can add more values."
    },
    {
      "id": "21d2924d-8b3e-4ce4-a7f8-318e85c0f7f2",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-10-25T09:45:37.5096445Z",
      "key": "SceneSource",
      "value": [
        "sentinel-l1c",
        "sentinel-l2a",
        "farmbeats-model",
        "dji",
        "SlantRange-3P",
        "DJI"
      ],
      "description": "List of scene sources available in system. User can add more values."
    },
    {
      "id": "6e09ca69-cacf-4b53-b63a-53c659aae4a4",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-10-25T09:45:34.7483899Z",
      "key": "SceneType",
      "value": [
        "base-bands",
        "sensor-placement",
        "soil-moisture",
        "evi",
        "ndwi",
        "ndvi",
        "drone",
        "orthomosaic-drone"
      ],
      "description": "List of scene types available in system. User can add more values."
    },
    {
      "id": "55d8436b-3a86-4bea-87ab-e1b51226525f",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-06-17T07:32:30.1135716Z",
      "key": "SensorMeasureType",
      "value": [
        "AmbientTemperature",
        "CO2",
        "Depth",
        "ElectricalConductivity",
        "LeafWetness",
        "Length",
        "LiquidLevel",
        "Nitrate",
        "O2",
        "PH",
        "Phosphate",
        "PointInTime",
        "Potassium",
        "Pressure",
        "RainGauge",
        "RelativeHumidity",
        "Salinity",
        "SoilMoisture",
        "SoilTemperature",
        "SolarRadiation",
        "State",
        "TimeDuration",
        "UVRadiation",
        "UVIndex",
        "Volume",
        "WindDirection",
        "WindRun",
        "WindSpeed",
        "Evapotranspiration",
        "PAR"
      ],
      "description": "List of sensor measure types available in system. User can add more values."
    },
    {
      "id": "0dfd6e6b-df58-428f-9ab8-a0674bfdcbe5",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-06-17T07:32:30.1135716Z",
      "key": "SensorMeasureUnit",
      "value": [
        "NoUnit",
        "Celsius",
        "Fahrenheit",
        "Kelvin",
        "Rankine",
        "Pascal",
        "Mercury",
        "PSI",
        "MilliMeter",
        "CentiMeter",
        "Meter",
        "Inch",
        "Feet",
        "Mile",
        "KiloMeter",
        "MilesPerHour",
        "MilesPerSecond",
        "KMPerHour",
        "KMPerSecond",
        "MetersPerHour",
        "MetersPerSecond",
        "Degree",
        "WattsPerSquareMeter",
        "KiloWattsPerSquareMeter",
        "MilliWattsPerSquareCentiMeter",
        "MilliJoulesPerSquareCentiMeter",
        "VolumetricWaterContent",
        "Percentage",
        "PartsPerMillion",
        "MicroMol",
        "MicroMolesPerLiter",
        "SiemensPerSquareMeterPerMole",
        "MilliSiemensPerCentiMeter",
        "Centibar",
        "DeciSiemensPerMeter",
        "KiloPascal",
        "VolumetricIonContent",
        "Liter",
        "MilliLiter",
        "Seconds",
        "UnixTimestamp",
        "MicroMolPerMeterSquaredPerSecond",
        "InchesPerHour"
      ],
      "description": "List of sensor measure units available in system. User can add more values."
    }
  ]
}
```

Il s’agit d’une configuration unique, et la portée de ce nouveau type de scène est limitée à l’abonnement dans lequel le projet FarmBeats est déployé.

Exemple : Pour ajouter la valeur de SceneSource « SlantRange », vous effectuez un PUT sur l’ID de /ExtendedType avec la clé « SceneSource ». Charge utile d’entrée :

```json
{

      "key": "SceneSource",
      "value": [
        "sentinel-l1c",
        "sentinel-l2a",
        "farmbeats-model",
        "dji",
        "SlantRange-3P"
      ],
      "description": "List of scene sources available in system. User can add more values."
}

```

Le champ vert est le nouvel ajout aux valeurs de source de scène définies par le système.

### <a name="step-2-get-farmdetails"></a>Étape 2 : Obtenir les informations sur la ferme

Les scènes (fichiers .tiff ou. csv) sont associées au contexte d’une ferme. Vous devez obtenir les informations sur la ferme en effectuant un GET sur l’API /Farm. L’API retourne la liste des fermes disponibles dans FarmBeats, et vous pouvez sélectionner la ferme pour laquelle vous souhaitez ingérer les données.

Obtenir la réponse /Farm :

```json
{
  "id": "07f3e09c-89aa-4619-8d50-e57fb083d8f9",
  "createdAt": "2019-11-01T13:55:41.8804663Z",
  "lastModifiedAt": "2019-11-01T13:55:41.8804663Z",
  "geometry": {
    "type": "Polygon",
    "coordinates": [
      [
        [
          78.34252251428694,
          17.402556352862675
        ],
        [
          78.34278000635095,
          17.407920852463022
        ],
        [
          78.34883106989963,
          17.40878079576528
        ],
        [
          78.3498181228054,
          17.403539173730607
        ],
        [
          78.34805859369493,
          17.39977166504127
        ],
        [
          78.34252251428694,
          17.402556352862675
        ]
      ]
    ]
  },
  "name": "Samplefarm",
  "properties": {
    "crops": "Corn",
    "address": "Sample street"
  }
}
 ```

### <a name="step-3-create-ascene-id-post-call"></a>Étape 3 : Créer un ID /scene (appel POST)

Créez une scène (fichier .tiff ou. csv) avec les informations fournies en indiquant la date, la séquence et l’ID de ferme auxquels la scène sera associée. Les métadonnées associées à la scène peuvent être définies dans les propriétés, y compris la durée et le type de mesure.

Ceci crée un SceneID, qui sera associé à la ferme. Une fois le SceneID créé, l’utilisateur peut s’en servir pour créer un fichier (tiff ou. csv) et stocker le contenu du fichier.

Exemple de charge utile d’entrée pour l’appel POST sur l’API /Scene

```json
{
  "sceneId": "a0505928-c480-491b-ba31-d38285a28c1d",
  "type": "newtype",
  "contentType": "image/tiff",
  "name": "test scene file",
  "description": "test scene file description",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```

Réponse de l’API :

```json
{
  "id": "a0505928-c480-491b-ba31-d38285a28c1d",
  "createdAt": "2019-10-04T16:19:12.4838584Z",
  "lastModifiedAt": "2019-10-04T16:19:12.4838584Z",
  "type": "new type",
  "source": "SlantRange-3P",
  "farmId": "d41a33e7-b73e-480e-9279-0fcb3207332b",
  "date": "2019-10-04T16:13:39.064Z",
  "sequence": 5,
  "name": "test scene",
  "description": "test scene description",
  "properties": {}
}

```

**Créer /SceneFile**

L’ID de scène retourné à l’étape 3 est l’entrée pour le SceneFile. Le SceneFile retourne un jeton d’URL SAS valide pendant 24 heures.

Si l’utilisateur a besoin d’une méthode programmatique pour charger un flux d’images, le SDK de stockage d’objets blob peut être utilisé pour définir une méthode avec l’ID, l’emplacement et l’URL du Scenefile.

Exemple de charge utile d’entrée pour l’appel POST sur l’API /Scenefile :

```json
{
  "sceneId": "a0505928-c480-491b-ba31-d38285a28c1d",
  "type": "newtype",
  "contentType": "image/tiff",
  "name": "test scene file",
  "description": "test scene file description",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```
Réponse de l’API :

```json
{
  "uploadSASUrl": "https://storagej2lho.blob.core.windows.net/farm-scene/2019/a0505928-c480-491b-ba31-d38285a28c1d/e91139a7-4ebd-4e2f-b17c-c677822dc840?sv=2018-03-28&sr=b&sig=%2F1426JkDcIFE5g3d%2BjOevCVMIn%2FJo9YKwBn3La5zL8Y%3D&se=2019-10-05T16%3A23%3A57Z&sp=w",
  "id": "e91139a7-4ebd-4e2f-b17c-c677822dc840",
  "createdAt": "2019-10-04T16:23:57.1192916Z",
  "lastModifiedAt": "2019-10-04T16:23:57.1192916Z",
  "blobUrl": "https://storagej2lho.blob.core.windows.net/farm-scene/2019/a0505928-c480-491b-ba31-d38285a28c1d/e91139a7-4ebd-4e2f-b17c-c677822dc840",
  "sceneId": "a0505928-c480-491b-ba31-d38285a28c1d",
  "type": "newtype",
  "contentType": "image/tiff",
  "name": "test scene file",
  "description": "test scene file description",
  "properties": {}
}


```

L’appel POST à l’API /SceneFile retourne une URL de chargement SAS qui peut être utilisée pour charger le fichier csv ou tiff à l’aide du client/de la bibliothèque de Stockage Blob Azure.


## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’intégration basée sur l’API REST, consultez [API REST](references-for-farmbeats.md#rest-api).
