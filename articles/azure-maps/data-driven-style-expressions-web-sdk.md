---
title: Expressions de style basé sur les données dans le SDK web Azure Maps | Microsoft Docs
description: Guide pratique pour utiliser des expressions de style basé sur les données dans le SDK web Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 4/4/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: 507af54b8b4c2e7c67538a1a25a040c7ee5fdfd5
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976318"
---
# <a name="data-driven-style-expressions-web-sdk"></a>Expressions de style basé sur les données (SDK web)

Les expressions vous permettent d’appliquer une logique métier à des options de style qui observent les propriétés définies dans chaque forme d’une source de données. Les expressions peuvent également être utilisées pour filtrer des données dans une source de données ou une couche. Les expressions peuvent consister en une logique conditionnelle, comme les instructions if, et peuvent également être utilisées pour manipuler des données avec des opérateurs de chaîne, logiques et mathématiques. 

Les styles basés sur les données peuvent réduire la quantité de code nécessaire pour implémenter une logique métier autour des styles. Quand elles sont utilisées avec des couches, les expressions sont évaluées au moment de la restitution sur un thread distinct qui fournit des performances accrues par rapport à l’évaluation d’une logique métier sur le thread d’interface utilisateur.

La vidéo suivante fournit une vue d’ensemble des styles basés sur les données dans le SDK web Azure Maps.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Data-Driven-Styling-with-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

Les expressions sont représentées sous forme de tableaux JSON. Le premier élément d’une expression dans le tableau est une chaîne qui spécifie le nom de l’opérateur d’expression. Par exemple, « + » ou « case ». Les éléments suivants (le cas échéant) sont les arguments de l’expression. Chaque argument est soit une valeur littérale (une chaîne, le nombre, une valeur booléenne ou `null`), soit un autre tableau d’expressions. Le pseudo-code suivant définit la structure de base d’une expression. 

```javascript
[ 
    expression_operator, 
    argument0, 
    argument1, 
    …
] 
```

Le kit de développement logiciel (SDK) web Azure Maps prend en charge de nombreux types qui peuvent être utilisés seuls ou en combinaison avec d'autres expressions.

| Type d’expressions | Description |
|---------------------|-------------|
| [Expression d'agrégation](#aggregate-expression) | Expression définissant un calcul traité sur un jeu de données et pouvant être utilisée avec l'option `clusterProperties` d'une `DataSource`. |
| [Expressions booléennes](#boolean-expressions) | Les expressions booléennes fournissent un ensemble d’expressions d’opérateurs booléens pour l’évaluation de comparaisons booléennes. |
| [Expressions de couleur](#color-expressions) | Les expressions de couleur simplifient la création et la manipulation de valeurs de couleurs. |
| [Expressions conditionnelles](#conditional-expressions) | Les expressions conditionnelles fournissent des opérations de logique qui ressemblent à des instructions if. |
| [Expressions de données](#data-expressions) | Permet d’accéder aux données de propriété dans une fonctionnalité. |
| [Expressions interpolate et step](#interpolate-and-step-expressions) | Les expressions interpolate et step peuvent être utilisées pour calculer des valeurs le long d’une courbe interpolée ou d’une fonction d’étape. |
| [Expressions spécifiques à une couche](#layer-specific-expressions) | Expressions spéciale qui ne sont applicables qu’à une couche unique. |
| [Expressions mathématiques](#math-expressions) | Fournit des opérateurs mathématiques pour effectuer des calculs basés sur les données dans le framework de l’expression. |
| [Expressions d’opérateur de chaîne](#string-operator-expressions) | Les expressions d’opérateur de chaîne effectuent des opérations de conversion sur des chaînes, comme une concaténation et une conversion de la casse. |
| [Expressions du type](#type-expressions) | Les expressions du type fournissent des outils permettant de tester et de convertir différents types de données comme des chaînes, des nombres et des valeurs booléennes. |
| [Expressions de liaison de variable](#variable-binding-expressions) | Les expressions de liaison de variable permettent de stocker les résultats d’un calcul dans une variable et de les référencer plusieurs fois ailleurs dans une expression sans avoir à recalculer la valeur stockée. |
| [Expression zoom](#zoom-expression) | Récupère le niveau de zoom actuel de la carte au moment de la restitution. |

Tous les exemples de ce document utilisent la fonctionnalité suivante pour illustrer diverses façons d’utiliser les différents types d’expressions. 

```javascript
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.13284, 47.63699]
    },
    "properties": { 
        "id": 123,
        "entityType": "restaurant",
        "revenue": 12345,
        "subTitle": "Building 40", 
        "temperature": 72,
        "title": "Cafeteria", 
        "zoneColor": "red"
    }
}
```

## <a name="data-expressions"></a>Expressions de données

Les expressions de données permettent d’accéder aux données de propriété dans une fonctionnalité. 

| Expression | Type de retour | Description |
|------------|-------------|-------------|
| `['at', number, array]` | objet | Récupère un élément à partir d’un tableau. |
| `['geometry-type']` | string | Obtient le type de géométrie de la fonctionnalité : Point, MultiPoint, LineString, MultiLineString, Polygon, MultiPolygon. |
| `['get', string]` | value | Obtient la valeur de propriété à partir des propriétés de la fonctionnalité actuelle. Retourne la valeur null si la propriété demandée est manquante. |
| `['get', string, object]` | value | Obtient la valeur de propriété à partir des propriétés de l’objet fourni. Retourne la valeur null si la propriété demandée est manquante. |
| `['has', string]` | boolean | Détermine si les propriétés d’une fonctionnalité ont la propriété spécifiée. |
| `['has', string, object]` | boolean | Détermine si les propriétés de l’objet ont la propriété spécifiée. |
| `['id']` | value | Obtient l’ID de la fonctionnalité, le cas échéant. |
| `['length', string | array]` | number | Obtient la longueur d’une chaîne ou d’un tableau. |

**Exemples**

Les propriétés d’une fonctionnalité sont accessibles directement dans une expression en utilisant une expression `get`. L’exemple suivant utilise la valeur « zoneColor » de la fonctionnalité pour spécifier la propriété color d’une couche de bulles. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: ['get', 'zoneColor'] //Get the zoneColor value.
});
```

L’exemple ci-dessus fonctionnera correctement si toutes les fonctionnalités de point disposent de la propriété `zoneColor`, mais si ce n’est pas le cas, la couleur se repliera probablement sur « black » (noir). Pour modifier la couleur de secours, une expression `case` peut être utilisée en association avec l’expression `has` pour vérifier si la propriété existe. Si ce n’est pas le cas, une couleur de secours est retournée à la place.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'case', //Use a conditional case expression.

        ['has', 'zoneColor'],   //Check to see if feature has a "zoneColor" property
        ['get', 'zoneColor'],   //If it does, use it.

        'blue'  //If it doesn't, default to blue.
    ]
});
```

Les couches de bulles et de symboles affichent par défaut les coordonnées de toutes formes d’une source de données. Cela peut être destiné à mettre en évidence les sommets d’un polygone ou une ligne. L’option `filter` de la couche peut être utilisée pour limiter le type de géométrie des fonctionnalités qu’elle restitue à l’aide d’une expression `['geometry-type']` dans une expression booléenne. L’exemple suivant limite une couche de bulles afin que seules les fonctionnalités `Point` soient restituées.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['==', ['geometry-type'], 'Point']
});
```

L’exemple suivant permettra la restitution des fonctionnalités `Point` et `MultiPoint`. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]
});
```

De même, le contour des polygones s’affichera dans les couches de lignes. Pour désactiver ce comportement dans une couche de lignes, ajoutez un filtre qui autorise uniquement les fonctionnalités `LineString` et `MultiLineString`.  

## <a name="math-expressions"></a>Expressions mathématiques

Les expressions mathématiques fournissent des opérateurs mathématiques pour effectuer des calculs basés sur les données dans le framework de l’expression.

| Expression | Type de retour | Description |
|------------|-------------|-------------|
| `['+', number, number, …]` | number | Calcule la somme des nombres spécifiés. |
| `['-', number]` | number | Retranche 0 du nombre spécifié. |
| `['-', number, number]` | number | Retranche les premiers nombres du deuxième nombre. |
| `['*', number, number, …]` | number | Multiplie les nombres spécifiés entre eux. |
| `['/', number, number]` | number | Divise le premier nombre par le deuxième nombre. |
| `['%', number, number]` | number | Calcule le reste de la division du premier nombre par le deuxième. |
| `['^', number, number]` | number | Calcule la valeur du premier nombre élevé à la puissance du deuxième nombre. |
| `['abs', number]` | number | Calcule la valeur absolue du nombre spécifié. |
| `['acos', number]` | number | Calcule l’arc cosinus du nombre spécifié. |
| `['asin', number]` | number | Calcule l’arc sinus du nombre spécifié. |
| `['atan', number]` | number | Calcule l’arc tangente du nombre spécifié. |
| `['ceil', number]` | number | Arrondit le nombre à l’entier supérieur suivant. |
| `['cos', number]` | number | Calcule le cosinus du nombre spécifié. |
| `['e']` | number | Retourne la constante mathématique `e`. |
| `['floor', number]` | number | Arrondit le nombre à l’entier inférieur précédent. |
| `['ln', number]` | number | Calcule le logarithme naturel du nombre spécifié. |
| `['ln2']` | number | Retourne la constante mathématique `ln(2)`. |
| `['log10', number]` | number | Calcule le logarithme en base dix du nombre spécifié. |
| `['log2', number]` | number | Calcule le logarithme en base deux du nombre spécifié. |
| `['max', number, number, …]` | number | Calcule le nombre maximal dans l’ensemble spécifié de nombres. |
| `['min', number, number, …]` | number | Calcule le nombre minimal dans l’ensemble spécifié de nombres. |
| `['pi']` | number | Retourne la constante mathématique `PI`. |
| `['round', number]` | number | Arrondit le nombre à l’entier le plus proche. Les valeurs médianes sont arrondies à la valeur la plus éloignée de zéro. Par exemple, `['round', -1.5]` a pour résultat -2. |
| `['sin', number]` | number | Calcule le sinus du nombre spécifié. |
| `['sqrt', number]` | number | Calcule la racine carrée du nombre spécifié. |
| `['tan', number]` | number | Calcule la tangente du nombre spécifié. |

## <a name="aggregate-expression"></a>Expression d'agrégation

Une expression d'agrégation définit un calcul traité sur un jeu de données et peut être utilisée avec l'option `clusterProperties` d'une `DataSource`. Le résultat de ces expressions doit être un nombre ou une valeur booléenne. 

Une expression d'agrégation accepte trois valeurs : une valeur d'opérateur, une valeur initiale et une expression permettant d'extraire une propriété à partir de chaque élément d'une donnée afin d'y appliquer l'opération d'agrégation. Le format de cette expression est le suivant :

```javascript
[operator: string, initialValue: boolean | number, mapExpression: Expression]
```

- operator : fonction d'expression qui est ensuite appliquée à toutes les valeurs calculées par `mapExpression` pour chaque point du cluster. Opérateurs pris en charge ; 
    - Pour les nombres : `+`, `*`, `max`, `min`
    - Pour les valeurs booléennes : `all`, `any`
- initialValue : valeur initiale à partir de laquelle la première valeur calculée est agrégée.
- mapExpression : expression appliquée à chaque point du jeu de données.

**Exemples**

Si tous les éléments d'un jeu de données possèdent une propriété `revenue` correspondant à un nombre. Le revenu total de tous les points d'un cluster créé à partir du jeu de données peut être calculé à l'aide de l'expression d'agrégation suivante : `['+', 0, ['get', 'revenue']]`

## <a name="boolean-expressions"></a>Expressions booléennes

Les expressions booléennes fournissent un ensemble d’expressions d’opérateurs booléens pour l’évaluation de comparaisons booléennes.

Lors de la comparaison de valeurs, la comparaison est strictement typée. Les valeurs de types différents sont toujours considérées comme inégales. Les cas où les types sont reconnus différents au moment de l’analyse sont considérés comme non valides et génèrent une erreur d’analyse. 

| Expression | Type de retour | Description |
|------------|-------------|-------------|
| `['! ', boolean]` | boolean | Négation logique. Retourne `true` si l’entrée est `false`, et `false` si l’entrée est `true`. |
| `['!= ', value, value]` | boolean | Retourne `true` si les valeurs d’entrée ne sont pas égales ; `false` dans le cas contraire. |
| `['<', value, value]` | boolean | Retourne `true` si la première entrée est strictement inférieure à la deuxième ; `false` dans le cas contraire. Les arguments doivent obligatoirement être tous les deux des chaînes ou des nombres. |
| `['<=', value, value]` | boolean | Retourne `true` si la première entrée est inférieure ou égale à la deuxième ; `false` dans le cas contraire. Les arguments doivent obligatoirement être tous les deux des chaînes ou des nombres. |
| `['==', value, value]` | boolean | Retourne `true` si les valeurs d’entrée sont égales ; `false` dans le cas contraire. Les arguments doivent obligatoirement être tous les deux des chaînes ou des nombres. |
| `['>', value, value]` | boolean | Retourne `true` si la première entrée est strictement supérieure à la deuxième ; `false` dans le cas contraire. Les arguments doivent obligatoirement être tous les deux des chaînes ou des nombres. |
| `['>=' value, value]` | boolean | Retourne `true` si la première entrée est supérieure ou égale à la deuxième ; `false` dans le cas contraire. Les arguments doivent obligatoirement être tous les deux des chaînes ou des nombres. |
| `['all', boolean, boolean, …]` | boolean | Retourne `true` si toutes les entrées ont la valeur `true` ; retourne `false` dans le cas contraire. |
| `['any', boolean, boolean, …]` | boolean | Retourne `true` si l’une des entrées a la valeur `true` ; retourne `false` si ce n’est pas le cas. |

## <a name="conditional-expressions"></a>Expressions conditionnelles

Les expressions conditionnelles fournissent des opérations de logique qui ressemblent à des instructions if.

Les expressions suivantes effectuent des opérations de logique conditionnelle sur les données d’entrée. Par exemple, l’expression `case` fournit la logique « if/then/else » alors que l’expression `match` est comme une « instruction switch ». 

### <a name="case-expression"></a>Expression case

Une expression `case` est un type d’expression conditionnelle qui fournit une logique semblable à l’instruction if (if/then/else). Ce type d’expression parcourt une liste de conditions booléennes et retourne la valeur de sortie de la première condition booléenne qui est vraie.

Le pseudo-code suivant définit la structure de l’expression `case`. 

```javascript
[
    'case',
    condition1: boolean, 
    output1: value,
    condition2: boolean, 
    output2: value,
    ...,
    fallback: value
]
```

**Exemple**

L’exemple suivant parcourt différentes conditions booléennes jusqu’à ce qu’il en trouve une qui a pour résultat `true`, puis il retourne cette valeur associée. Si aucune condition booléenne n’a pour résultat `true`, une valeur de secours est retournée. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'case',

        //Check to see if the first boolean expression is true, and if it is, return its assigned result.
        ['has', 'zoneColor'],
        ['get', 'zoneColor'],

        //Check to see if the second boolean expression is true, and if it is, return its assigned result.
        ['all', ['has', ' temperature '], ['>', ['get', 'temperature'], 100]],
        'red',

        //Specify a default value to return.
        'green'
    ]
});
```

### <a name="match-expression"></a>Expression match

Une expression `match` est un type d’expression conditionnelle qui fournit une logique semblable à l’instruction switch. L’entrée peut être n’importe quelle expression, par exemple `['get', 'entityType']`, qui retourne une chaîne ou un nombre. Chaque étiquette doit être une valeur littérale unique ou un tableau de valeurs littérales dont les valeurs doivent être toutes des chaînes ou toutes des nombres. L’entrée est une correspondance si l’une des valeurs du tableau correspond. Chaque étiquette doit être unique. Si le type d’entrée ne correspond au type des étiquettes, le résultat est la valeur de secours.

Le pseudo-code suivant définit la structure de l’expression `match`. 

```javascript
[
    'match',
    input: number | string,
    label1: number | string | (number | string)[], 
    output1: value,
    label2: number | string | (number | string)[], 
    output2: value,
    ...,
    fallback: value
]
```

**Exemples**

L’exemple suivant examine la propriété `entityType` d’une fonctionnalité Point dans une couche de bulles afin de trouver une correspondance. Si une correspondance est trouvée, la valeur spécifiée est retournée ; sinon, il retourne la valeur de secours.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'match',

        //Get the property to match.
        ['get', 'entityType'],

        //List the values to match and the result to return for each match.
        'restaurant', 'red',
        'park', 'green',

        //Specify a default value to return if no match is found.
        'black'
    ]
});
```

L’exemple suivant utilise un tableau pour lister un ensemble d’étiquettes qui doivent toutes retourner la même valeur. Cette procédure est beaucoup plus efficace que de lister chaque étiquette individuellement. Dans ce cas, si la propriété `entityType` a la valeur « restaurant » ou « grocery_store », la couleur « red » (rouge) est retournée.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'match',

        //Get the property to match.
        ['get', 'entityType'],

        //List the values to match and the result to return for each match.
        ['restaurant', 'grocery_store'], 'red',

        'park', 'green',

        //Specify a default value to return if no match is found.
        'black'
    ]
});
```

L’exemple suivant utilise une expression de correspondance pour appliquer un filtre de type « in array » ou « array contains ». Dans ce cas, en filtrant les données qui ont une valeur d’ID figurant dans une liste d’ID autorisés. Lorsque vous utilisez des expressions avec des filtres, le résultat doit être une valeur booléenne.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: [
        'match',  

        //Get the property to match.
        ['get', 'id'],  

         //List of values to match.
        [24, 53, 98], 

        //If there is a match, return true.
        true,
    
        //Otherwise return false.
        false
    ]
});
```

### <a name="coalesce-expression"></a>Expression coalesce

Une expression `coalesce` parcourt un ensemble d’expressions jusqu’à ce que la première valeur non null soit obtenue, puis retourne cette valeur. 

Le pseudo-code suivant définit la structure de l’expression `coalesce`. 

```javascript
[
    'coalesce', 
    value1, 
    value2, 
    …
]
```

**Exemple**

L’exemple suivant utilise une expression `coalesce` pour définir l’option `textField` d’une couche de symboles. Si la propriété `title` est manquante dans la fonctionnalité ou qu’elle est définie sur `null`, l’expression tente alors de rechercher la propriété `subtitle` ; si elle est manquante ou `null`, elle utilise une chaîne vide. 

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'coalesce',

            //Try getting the title property.
            ['get', 'title'],

            //If there is no title, try getting the subtitle. 
            ['get', 'subtitle'],

            //Default to an empty string.
            ''
        ]
    }
});
```

## <a name="type-expressions"></a>Expressions du type

Les expressions du type fournissent des outils permettant de tester et de convertir différents types de données comme des chaînes, des nombres et des valeurs booléennes.

| Expression | Type de retour | Description |
|------------|-------------|-------------|
| `['literal', array]`<br/><br/>`['literal', object]` | tableau\|objet | Retourne une valeur littérale d’objet ou de tableau. Utilisez cette expression pour empêcher qu’un tableau ou un objet soit évalué en tant qu’expression. Cela est nécessaire quand un tableau ou un objet doit être retourné par une expression. |
| `['to-boolean', value]` | boolean | Convertit la valeur d’entrée en une valeur booléenne. Le résultat est `false` quand l’entrée est une chaîne vide, `0`, `false`, `null` ou `NaN` ; sinon, il prend la valeur `true`. |
| `['to-color', value]`<br/><br/>`['to-color', value1, value2…]` | color | Convertit la valeur d’entrée en une couleur. Si plusieurs valeurs sont fournies, chacune est évaluée dans l’ordre jusqu’à ce que la première conversion réussie soit obtenue. Si aucune des entrées ne peut être convertie, l’expression est une erreur. |
| `['to-number', value]`<br/><br/>`['to-number', value1, value2, …]` | number | Convertit la valeur d’entrée en un nombre, si cela est possible. Si l’entrée est `null` ou `false`, le résultat est 0. Si l’entrée est `true`, le résultat est 1. Si l’entrée est une chaîne, elle est convertie en un nombre à l’aide de la fonction de chaîne [ToNumber](https://tc39.github.io/ecma262/#sec-tonumber-applied-to-the-string-type) de la spécification de langage ECMAScript. Si plusieurs valeurs sont fournies, chacune est évaluée dans l’ordre jusqu’à ce que la première conversion réussie soit obtenue. Si aucune des entrées ne peut être convertie, l’expression est une erreur. |
| `['to-string', value]` | string | Convertit la valeur d’entrée en une chaîne. Si l’entrée est `null`, le résultat est `""`. Si l’entrée est une valeur booléenne, le résultat est `"true"` ou `"false"`. Si l’entrée est un nombre, elle est convertie en une chaîne à l’aide de la fonction de nombre [ToString](https://tc39.github.io/ecma262/#sec-tostring-applied-to-the-number-type) de la spécification de langage ECMAScript. Si l’entrée est une couleur, elle est convertie en une chaîne de couleur RVBA CSS `"rgba(r,g,b,a)"`. Sinon, l’entrée est convertie en une chaîne à l’aide de la fonction [JSON.stringify](https://tc39.github.io/ecma262/#sec-json.stringify) de la spécification de langage ECMAScript. |
| `['typeof', value]` | string | Retourne une chaîne décrivant le type de la valeur donnée. |

> [!TIP]
> Si un message d’erreur semblable à `Expression name must be a string, but found number instead. If you wanted a literal array, use ["literal", [...]].` s’affiche dans la console du navigateur, cela signifie qu’il existe, quelque part dans votre code, une expression comportant un tableau qui n’a pas de chaîne pour sa première valeur. Si vous voulez que l’expression retourne un tableau, wrappez le tableau avec l’expression `literal`. L’exemple suivant définit l’option d’icône `offset` d’une couche de symboles, qui doit être un tableau contenant deux nombres, à l’aide d’une expression `match` permettant de choisir entre deux valeurs de décalage en fonction de la valeur de la propriété `entityType` de la fonctionnalité de point.
>
> ```javascript
> var layer = new atlas.layer.SymbolLayer(datasource, null, {
>     iconOptions: {
>         offset: [
>             'match',
>
>             //Get the entityType value.
>             ['get', 'entityType'],
>
>             //If there is no title, try getting the subtitle. 
>             'restaurant', ['literal', [0, -10]],
>
>             //Default to value.
>             ['literal', [0, 0]]
>         ]
>     }
> });
> ```

## <a name="color-expressions"></a>Expressions de couleur

Les expressions de couleur simplifient la création et la manipulation de valeurs de couleurs.

| Expression | Type de retour | Description |
|------------|-------------|-------------|
| `['rgb', number, number, number]` | color | Crée une valeur de couleur à partir des composants *red*, *green* et *blue* dont les valeurs doivent être comprises entre `0` et `255`, et d’un composant alpha ayant la valeur `1`. Si l’un des composants est hors limites, l’expression est une erreur. |
| `['rgba', number, number, number, number]` | color | Crée une valeur de couleur à partir des composants *red*, *green*, *blue* dont les valeurs doivent être comprises entre `0` et `255`, et d’un composant alpha dont la valeur est comprise entre `0` et `1`. Si l’un des composants est hors limites, l’expression est une erreur. |
| `['to-rgba']` | \[nombre, nombre, nombre, nombre\] | Retourne un tableau à quatre éléments contenant les composants *red*, *green*, *blue* et *alpha* de la couleur d’entrée, dans cet ordre. |

**Exemple**

L’exemple suivant crée une valeur de couleur RVB qui a une valeur *red* égale à `255` et des valeurs *green* et *blue* calculées en multipliant `2.5` par la valeur de la propriété `temperature`. Quand la température change, la couleur est remplacée par différentes nuances de rouge (*red*).

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'rgb', //Create a RGB color value.

        255,    //Set red value to 255.

        ['*', 2.5, ['get', 'temperature']], //Multiple the temperature by 2.5 and set the green value.

        ['*', 2.5, ['get', 'temperature']]  //Multiple the temperature by 2.5 and set the blue value.
    ]
});
```

## <a name="string-operator-expressions"></a>Expressions d’opérateur de chaîne

Les expressions d’opérateur de chaîne effectuent des opérations de conversion sur des chaînes, comme une concaténation et une conversion de la casse. 

| Expression | Type de retour | Description |
|------------|-------------|-------------|
| `['concat', string, string, …]` | string | Concatène plusieurs chaînes. Chaque valeur doit être une chaîne. Utilisez l’expression du type `to-string` pour convertir d’autres types de valeurs en chaîne, si nécessaire. |
| `['downcase', string]` | string | Convertit la chaîne spécifiée en minuscules. |
| `['upcase', string]` | string | Convertit la chaîne spécifiée en majuscules. |

**Exemple**

L’exemple suivant convertit la propriété `temperature` de la fonctionnalité de point en chaîne, puis les concatène « °F » à la fin de celle-ci.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: ['concat', ['to-string', ['get', 'temperature']], '°F'],

        //Some additional style options.
        offset: [0, -1.5],
        size: 12,
        color: 'white'
    }
});
```

L’expression ci-dessus affiche une épingle sur la carte avec le texte « 64 °F » superposé, comme illustré dans l’image ci-dessous.

<center>

![Exemple d’expression d’opérateur de chaîne](media/how-to-expressions/string-operator-expression.png) </center>

## <a name="interpolate-and-step-expressions"></a>Expressions interpolate et step

Les expressions interpolate et step peuvent être utilisées pour calculer des valeurs le long d’une courbe interpolée ou d’une fonction d’étape. Ces expressions prennent une expression qui retourne une valeur numérique comme entrée ; par exemple, `['get',  'temperature']`. La valeur d’entrée est évaluée par rapport à des paires de valeurs d’entrée et de sortie, appelées « arrêts » (stops), pour déterminer la valeur qui convient le mieux à la fonction de courbe ou d’étape. Les valeurs d’entrée de chaque arrêt doivent être un nombre et être dans l’ordre croissant. Les valeurs de sortie doivent être un nombre et un tableau de nombres, ou une couleur.

### <a name="interpolate-expression"></a>Expression interpolate

Une expression `interpolate` peut être utilisée pour calculer un ensemble régulier et continu de valeurs en effectuant des interpolations entre des valeurs d’arrêt. Une expression `interpolate` qui retourne des valeurs de couleur produit un dégradé de couleurs dans lequel les valeurs de résultats sont sélectionnées.

Il existe trois types de méthodes d’interpolation qui peuvent être utilisées dans une expression `interpolate` :
 
* `['linear']` : effectue une interpolation de manière linéaire entre la paire d’arrêts.
* `['exponential', base]` : effectue une interpolation de façon exponentielle entre les arrêts. La valeur `base` contrôle le taux auquel la sortie augmente. Les valeurs élevées font davantage augmenter la sortie vers l’extrémité supérieure de la plage. Une valeur `base` proche de 1 produit une sortie qui augmente de façon plus linéaire.
* `['cubic-bezier', x1, y1, x2, y2]` : effectue une interpolation à l’aide d’une [courbe de Bézier cubique](https://developer.mozilla.org/docs/Web/CSS/timing-function) définie par les points de contrôle donnés.

Voici un exemple de ce à quoi ressemblent ces différents types d’interpolations. 

| Linéaire  | Exponentielle | De Bézier cubique |
|---------|-------------|--------------|
| ![Graphique d’interpolation linéaire](media/how-to-expressions/linear-interpolation.png) | ![Graphique d’interpolation exponentielle](media/how-to-expressions/exponential-interpolation.png) | ![Graphique d’interpolation de Bézier cubique](media/how-to-expressions/bezier-curve-interpolation.png) |

Le pseudo-code suivant définit la structure de l’expression `interpolate`. 

```javascript
[
    'interpolate',
    interpolation: ['linear'] | ['exponential', base] | ['cubic-bezier', x1, y1, x2, y2],
    input: number,
    stopInput1: number, 
    stopOutput1: value1,
    stopInput2: number, 
    stopOutput2: value2, 
    ...
]
```

**Exemple**

L’exemple suivant utilise une expression `linear interpolate` pour définir la propriété `color` d’une couche de bulles sur la propriété `temperature` de la fonctionnalité de point. Si la valeur `temperature` est inférieure à 60, « blue » est retourné ; si elle est comprise entre 60 et 70, « yellow » est retourné ; si elle est comprise entre 70 et 80, « orange » est retourné ; si elle est supérieure ou égale à 80, « red » est retourné.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'interpolate',
        ['linear'],
        ['get', 'temperature'],
        50,        
        'blue',
        60,
        'yellow',
        70,
        'orange',
        80,
        'red'
    ]
});
```

L’image suivante montre comment les couleurs sont choisies pour l’expression ci-dessus.
 
<center>

![Exemple d’expression interpolate](media/how-to-expressions/interpolate-expression-example.png) </center>

### <a name="step-expression"></a>Expression step

Une expression `step` peut être utilisée pour calculer des valeurs de résultat discrètes échelonnées en évaluant une [fonction constante par morceaux](http://mathworld.wolfram.com/PiecewiseConstantFunction.html) définie par des arrêts. 

Le pseudo-code suivant définit la structure de l’expression `step`. 

```javascript
[
    'step',
    input: number,
    output0: value0,
    stop1: number, 
    output1: value1,
    stop2: number, 
    output2: value2, 
    ...
]
```

Les expressions step retournent la valeur de sortie de l’arrêt juste avant la valeur d’entrée, ou la première valeur d’entrée si l’entrée est inférieure au premier arrêt. 

**Exemple**

L’exemple suivant utilise une expression `step` pour définir la propriété `color` d’une couche de bulles sur la propriété `temperature` de la fonctionnalité de point. Si la valeur `temperature` est inférieure à 60, « blue » est retourné ; si elle est comprise entre 60 et 70, « yellow » est retourné ; si elle est comprise entre 70 et 80, « orange » est retourné ; si elle est supérieure ou égale à 80, « red » est retourné.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'step',
        ['get', 'temperature'],
        'blue',
        60,
        'yellow',
        70,
        'orange',
        80,
        'red'
    ]
});
```

L’image suivante montre comment les couleurs sont choisies pour l’expression ci-dessus.
 
<center>

![ step](media/how-to-expressions/step-expression-example.png)
</center>

## <a name="layer-specific-expressions"></a>Expressions spécifiques à une couche

Expressions spéciales qui s’appliquent uniquement à des couches spécifiques.

### <a name="heat-map-density-expression"></a>Expression de densité de carte thermique

Une expression de densité de carte thermique, qui est définie sous la forme `['heatmap-density']`, récupère la valeur de densité de carte thermique pour chaque pixel d’une couche de carte thermique. Cette valeur est un nombre compris entre `0` et `1`, et est utilisé en combinaison avec une expression `interpolation` ou `step` pour définir le dégradé de couleurs utilisé pour mettre en couleur de la carte thermique. Cette expression peut uniquement être utilisée dans l’[option color](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest#color) de la couche de carte thermique.

> [!TIP]
> La couleur de l’index 0 dans une expression d’interpolation ou la couleur par défaut d’une étape définit la couleur des zones dans lesquelles il n’existe aucune donnée et peut être utilisée pour définir une couleur d’arrière-plan. La plupart des utilisateurs préfèrent définir cette valeur sur une couleur noire transparente ou semi-transparente. 

**Exemple**

Cet exemple utilise une expression d’interpolation linéaire afin de créer un dégradé de couleurs léger pour afficher la carte thermique. 

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    color: [
        'interpolate',
        ['linear'],
        ['heatmap-density'],
        0, 'transparent',
        0.01, 'purple',
        0.5, '#fb00fb',
        1, '#00c3ff'
    ]
});
```

En plus de l’utilisation d’un dégradé léger pour mettre en couleur une carte thermique, les couleurs peuvent être spécifiées dans un ensemble de plages à l’aide d’une expression `step`. L’utilisation d’une expression `step` pour coloriser la carte thermique décompose visuellement la densité en plages. Elle ressemble ainsi davantage à une carte de style de contours ou de radars.  

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    color: [
        'step',
        ['heatmap-density'],
        'transparent',
        0.01, 'navy',
        0.25, 'navy',
        0.5, 'green',
        0.75, 'yellow',
        1, 'red'
    ]
});
```

Pour plus d’informations, consultez la documentation [Ajouter une couche de carte thermique](map-add-heat-map-layer.md).

### <a name="line-progress-expression"></a>Expression de progression des lignes

Une expression de progression des lignes, qui est définie sous la forme `['line-progress']`, récupère la progression le long d’une ligne avec dégradé dans une couche de lignes. Cette valeur est un nombre compris entre 0 et 1, et est utilisée en combinaison avec une expression `interpolation` ou `step`. Cette expression peut uniquement être utilisée avec l’[option strokeGradient]( https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest#strokegradient) de la couche de lignes. 

> [!NOTE]
> L’option `strokeGradient` de la couche de lignes exige que l’option `lineMetrics` de la source de données soit définie sur `true`.

**Exemple**

L’exemple suivant utilise l’expression `['line-progress']` pour appliquer un dégradé de couleurs au trait d’une ligne.

```javascript
var layer = new atlas.layer.LineLayer(datasource, null, {
    strokeGradient: [
        'interpolate',
        ['linear'],
        ['line-progress'],
        0, "blue",
        0.1, "royalblue",
        0.3, "cyan",
        0.5, "lime",
        0.7, "yellow",
        1, "red"
    ]
});
```

[Consulter un exemple en direct](map-add-line-layer.md#line-stroke-gradient)

### <a name="text-field-format-expression"></a>Expression de format de champ de texte

L’expression de format de champ de texte peut être utilisée avec l’option `textField` de la propriété `textOptions` des couches de symboles afin de fournir une mise en forme mixte du texte. Cette expression permet de spécifier un ensemble de chaînes d’entrée et d’options de mise en forme. Les options suivantes peuvent être spécifiées pour chaque chaîne d’entrée de cette expression.

 * `'font-scale'` : spécifie le facteur d’échelle pour la taille de police. Si elle est spécifiée, cette valeur remplace la propriété `size` des `textOptions` pour la chaîne individuelle.
 * `'text-font'` : spécifie une ou plusieurs familles de polices qui doivent être utilisés pour cette chaîne. Si elle est spécifiée, cette valeur remplace la propriété `font` des `textOptions` pour la chaîne individuelle.

Le pseudo-code suivant définit la structure de l’expression de format de champ de texte. 

```javascript
[
    'format', 
    input1: string, 
    options1: { 
        'font-scale': number, 
        'text-font': string[] 
    },
    input2: string, 
    options2: { 
        'font-scale': number, 
        'text-font': string[] 
    },
    …
]
```

**Exemple**

L’exemple suivant met en forme le champ de texte en ajoutant une police gras et en effectuant un scale-up de la taille de police de la propriété `title` de la fonctionnalité. Cet exemple ajoute également la propriété `subtitle` de la fonctionnalité sur une nouvelle ligne, avec un scale-down de la taille de police.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'format',

            //Bold the title property and scale its font size up.
            ['get', 'title'],
            {
                'text-font': ['literal', ['StandardFont-Bold']],
                'font-scale': 1.25
            },

            '\n', {},   //Add a new line without any formatting.

            //Scale the font size down of the subtitle property. 
            ['get', 'subtitle'],
            { 'font-scale': 0.75 }
        ]
    }
});
```

Cette couche affiche la fonctionnalité de point, comme illustré dans l’image ci-dessous :
 
<center>

![Image de la fonctionnalité Point avec un champ de texte mis en forme](media/how-to-expressions/text-field-format-expression.png) </center>

### <a name="number-format-expression"></a>Expression number-format

L’expression `number-format` peut uniquement être utilisée avec l’option `textField` d’une couche de symboles. Cette expression convertit le nombre fourni en une chaîne mise en forme. Elle wrappe la fonction [Number.toLocalString](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString) de JavaScript et prend en charge l’ensemble suivant d’options.

 * `locale` : spécifiez cette option pour convertir des nombres en chaînes d’une manière conforme à la langue spécifiée. Passez une [balise de langue BCP 47](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Intl#Locale_identification_and_negotiation) dans cette option.
 * `currency` : permet de convertir le nombre en une chaîne représentant une devise. Les valeurs possibles sont les [codes de devise ISO 4217](https://en.wikipedia.org/wiki/ISO_4217), comme « USD » pour le dollar américain, « EUR » pour l’euro ou « CNY » pour le RMB chinois.
 * `'min-fraction-digits'` : spécifie le nombre minimal de décimales à inclure dans la version de type chaîne du nombre.
 * `'max-fraction-digits'` : spécifie le nombre maximal de décimales à inclure dans la version de type chaîne du nombre.

Le pseudo-code suivant définit la structure de l’expression de format de champ de texte. 

```javascript
[
    'number-format', 
    input: number, 
    options: {
        locale: string, 
        currency: string, 
        'min-fraction-digits': number, 
        'max-fraction-digits': number
    }
]
```

**Exemple**

L’exemple suivant utilise une expression `number-format` pour modifier la façon dont la propriété `revenue` de la fonctionnalité de point est restituée dans l’option `textField` d’une couche de symboles de sorte qu’elle affiche une valeur en dollars américains.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'number-format', 
            ['get', 'revenue'], 
            { ‘currency’: 'USD' }
        ],

        offset: [0, 0.75]
    }
});
```

Cette couche affiche la fonctionnalité de point, comme illustré dans l’image ci-dessous :

<center>

![Exemple d’expression number-format](media/how-to-expressions/number-format-expression.png) </center>

## <a name="zoom-expression"></a>Expression zoom

Une expression `zoom`, qui est définie sous la forme `['zoom']`, est utilisée pour récupérer le niveau de zoom actuel de la carte au moment de la restitution. Cette expression retourne un nombre compris entre la plage de niveau de zoom minimale et maximale de la carte. L’utilisation de cette expression permet la modification dynamique des styles alors que le niveau de zoom de la carte est changé. L’expression `zoom` peut uniquement être utilisée avec des expressions `interpolate` et `step`.

**Exemple**

Par défaut, les rayons des points de données restitués dans la couche de carte thermique ont un rayon de pixels fixe pour tous les niveaux de zoom. Alors que la carte fait l’objet d’un zoom, les données sont agrégées et la couche de carte thermique change d’aspect. Une expression `zoom` peut être utilisée pour mettre à l’échelle le rayon de chaque niveau de zoom de sorte que chaque point de données couvre la même zone physique de la carte. Cette opération fait paraître la couche de carte thermique plus statique et plus cohérente. Chaque niveau de zoom de la carte a deux fois plus de pixels verticalement et horizontalement que le niveau de zoom précédent. La mise à l’échelle du rayon de sorte qu’il soit multiplié par deux avec chaque niveau de zoom crée une carte thermique qui paraît cohérente sur tous les niveaux de zoom. Vous pouvez effectuer cette opération à l’aide de l’expression `zoom` avec une expression `base 2 exponential interpolation`, comme indiqué ci-dessous. 

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    radius: [
        'interpolate',
        ['exponential', 2],
        ['zoom'],
        
        //For zoom level 1 set the radius to 2 pixels.
        10, 2,

        //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 10,000 pixels.
        19, 10000
    ]
};
```

[Consulter un exemple en direct](map-add-heat-map-layer.md#consistent-zoomable-heat-map)

## <a name="variable-binding-expressions"></a>Expressions de liaison de variable

Les expressions de liaison de variable stockent les résultats d’un calcul dans une variable afin qu’ils puissent être référencés plusieurs fois ailleurs dans une expression sans avoir à les recalculer. Il s’agit d’une optimisation utile des expressions qui impliquent de nombreux calculs.

| Expression | Type de retour | Description |
|--------------|---------------|--------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'let',<br/>&nbsp;&nbsp;&nbsp;&nbsp;name1: string,<br/>&nbsp;&nbsp;&nbsp;&nbsp;value1: any,<br/>&nbsp;&nbsp;&nbsp;&nbsp;name2: string,<br/>&nbsp;&nbsp;&nbsp;&nbsp;value2: any,<br/>&nbsp;&nbsp;&nbsp;&nbsp;…<br/>&nbsp;&nbsp;&nbsp;&nbsp;childExpression<br/>\] | | Stocke une ou plusieurs valeurs sous forme de variables pour une utilisation par l’expression `var` dans l’expression enfant qui retourne le résultat. |
| `['var', name: string]` | any | Fait référence à une variable qui a été créée à l’aide de l’expression `let`. |

**Exemple**

Cet exemple utilise une expression qui calcule le chiffre d’affaires par comparaison avec un rapport de température, puis utilise une expression `case` pour évaluer différentes opérations booléennes sur cette valeur. L’expression `let` est utilisée pour stocker le chiffre d’affaires par comparaison avec un rapport de température afin qu’il doive être calculé une seule fois, et l’expression `var` fait référence à cette variable aussi souvent que nécessaire sans avoir à la recalculer.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        //Divide the point features `revenue` property by the `temperature` property and store it in a variable called `ratio`.
        'let', 'ratio', ['/', ['get', 'revenue'], ['get', 'temperature']],
        //Evaluate the child expression in which the stored variable will be used.
        [
            'case',

            //Check to see if the ratio is less than 100, return 'red'.
            ['<', ['var', 'ratio'], 100],
            'red',

            //Check to see if the ratio is less than 200, return 'green'.
            ['<', ['var', 'ratio'], 200],
            'green',

            //Return `blue` for values greater or equal to 200.
            'blue'
        ]
    ]
});
```

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir plus d’exemples de code qui implémentent des expressions, consultez les articles suivants :

> [!div class="nextstepaction"] 
> [Ajouter une couche de symboles](map-add-pin.md)

> [!div class="nextstepaction"] 
> [Ajouter une couche de bulles](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Ajouter une couche de lignes](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Ajouter une couche de polygones](map-add-shape.md)

> [!div class="nextstepaction"] 
> [Ajouter une couche de carte thermique](map-add-heat-map-layer.md)

En savoir plus sur les options de couche qui prennent en charge des expressions :

> [!div class="nextstepaction"] 
> [BubbleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [HeatMapLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [PolygonLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest) 
