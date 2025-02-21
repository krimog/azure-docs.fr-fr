---
title: Surveiller les performances des applications web Java sur Linux - Azure | Microsoft Docs
description: Surveillance étendue des performances des applications de votre site web Java avec le plug-in collectd pour Application Insights.
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: 40c68f45-197a-4624-bf89-541eb7323002
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: mbullwin
ms.openlocfilehash: c6e947dfed3169f346f43ab08225056815e8b487
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67061194"
---
# <a name="collectd-linux-performance-metrics-in-application-insights"></a>collectd : métriques de performances Linux dans Application Insights


Pour explorer les métriques de performances d’un système Linux dans [Application Insights](../../azure-monitor/app/app-insights-overview.md), installez [collectd](https://collectd.org/) et son plug-in Application Insights. Cette solution open source rassemble diverses statistiques concernant le système et le réseau.

De manière générale, vous utilisez collectd lorsque vous avez déjà [instrumenté votre service web Java avec Application Insights][java]. Il vous donne davantage de données pour vous aider à améliorer les performances de votre application ou à diagnostiquer les problèmes. 

## <a name="get-your-instrumentation-key"></a>Récupérer votre clé d’instrumentation
Dans le [Portail Microsoft Azure](https://portal.azure.com), ouvrez la ressource [Application Insights](../../azure-monitor/app/app-insights-overview.md) dans laquelle vous souhaitez afficher les données. (Ou [créez une ressource](../../azure-monitor/app/create-new-resource.md ).)

Effectuez une copie de la clé d’instrumentation, qui identifie la ressource.

![Parcourez tous les éléments, ouvrez votre ressource, puis, dans la liste déroulante « Essentials », sélectionnez et copiez la clé d’instrumentation.](./media/java-collectd/instrumentation-key-001.png)

## <a name="install-collectd-and-the-plug-in"></a>Installer le plug-in et collectd
Sur vos ordinateurs serveurs Linux :

1. Installez [collectd](https://collectd.org/) version 5.4.0 ou ultérieure.
2. Téléchargez le [plug-in d'écriture collectd Application Insights](https://aka.ms/aijavasdk). Notez le numéro de version.
3. Copiez le fichier JAR du plug-in dans `/usr/share/collectd/java`.
4. Modifiez `/etc/collectd/collectd.conf`:
   * Vérifiez que [le plug-in Java](https://collectd.org/wiki/index.php/Plugin:Java) est activé.
   * Mettez à jour l’élément JVMArg pour java.class.path afin d’inclure le fichier JAR suivant. Mettez à jour le numéro de version afin qu’il corresponde à celui que vous avez téléchargé :
   * `/usr/share/collectd/java/applicationinsights-collectd-1.0.5.jar`
   * Ajoutez cet extrait de code à l’aide de la clé d’instrumentation provenant de votre ressource :

```XML

     LoadPlugin "com.microsoft.applicationinsights.collectd.ApplicationInsightsWriter"
     <Plugin ApplicationInsightsWriter>
        InstrumentationKey "Your key"
     </Plugin>
```

Voici une partie d’un exemple de fichier de configuration :

```XML

    ...
    # collectd plugins
    LoadPlugin cpu
    LoadPlugin disk
    LoadPlugin load
    ...

    # Enable Java Plugin
    LoadPlugin "java"

    # Configure Java Plugin
    <Plugin "java">
      JVMArg "-verbose:jni"
      JVMArg "-Djava.class.path=/usr/share/collectd/java/applicationinsights-collectd-1.0.5.jar:/usr/share/collectd/java/collectd-api.jar"

      # Enabling Application Insights plugin
      LoadPlugin "com.microsoft.applicationinsights.collectd.ApplicationInsightsWriter"

      # Configuring Application Insights plugin
      <Plugin ApplicationInsightsWriter>
        InstrumentationKey "12345678-1234-1234-1234-123456781234"
      </Plugin>

      # Other plugin configurations ...
      ...
    </Plugin>
    ...
```

Configurez d’autres [plug-ins collectd](https://collectd.org/wiki/index.php/Table_of_Plugins)permettant de collecter des données de différentes sources.

Redémarrez collectd selon les instructions de son [manuel](https://collectd.org/wiki/index.php/First_steps).

## <a name="view-the-data-in-application-insights"></a>Visualiser les données dans Application Insights
Dans votre ressource Application Insights, ouvrez [Métriques et ajoutez des graphiques][metrics] en sélectionnant les indicateurs de performance que vous souhaitez voir à partir de la catégorie Personnalisé.

Par défaut, les métriques sont agrégés sur toutes les machines hôtes à partir desquelles ils ont été collectés. Pour visualiser les métriques par hôte, dans le panneau des détails du graphique, activez le regroupement, puis choisissez un groupement de type CollectD-Host.

## <a name="to-exclude-upload-of-specific-statistics"></a>Pour exclure le chargement de statistiques spécifiques
Par défaut, le plug-in Application Insights envoie toutes les données collectées par tous les plug-ins de lecture collectd activés. 

Pour exclure des données provenant de plug-ins ou de sources de données spécifiques :

* Modifiez le fichier de configuration. 
* Dans `<Plugin ApplicationInsightsWriter>`, ajoutez les directives suivantes :

| Directive | Résultat |
| --- | --- |
| `Exclude disk` |Exclusion de toutes les données collectées par le plug-in `disk` . |
| `Exclude disk:read,write` |Exclusion des sources nommées `read` et `write` du plug-in `disk`. |

Séparez les directives par un saut de ligne.

## <a name="problems"></a>Des problèmes ?
*Je ne vois pas les données dans le portail*

* Ouvrez [Rechercher][diagnostic] pour vérifier si les événements bruts sont arrivés. Ils mettent parfois du temps à apparaître dans Metrics Explorer.
* Vous devrez peut-être [définir des exceptions de pare-feu pour les données sortantes](../../azure-monitor/app/ip-addresses.md)
* Activez le suivi dans le plug-in Application Insights. Ajoutez la ligne ci-après dans `<Plugin ApplicationInsightsWriter>`:
  * `SDKLogger true`
* Ouvrez un terminal et démarrez collectd en mode détaillé pour vérifier si des problèmes ont été signalés :
  * `sudo collectd -f`

## <a name="known-issue"></a>Problème connu

Le plug-in d’écriture Application Insights n’est pas compatible avec certains plug-ins de lecture. Certains plug-ins envoient parfois « NaN » alors que le plug-in Application Insights s’attend à recevoir un nombre à virgule flottante.

Symptôme : le journal collectd affiche des erreurs incluant « AI: ... SyntaxError: Jeton inattendu N ».

Solution de contournement : exclure les données collectées par les plug-ins d’écriture à l’origine du problème. 

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[apiexceptions]: ../../azure-monitor/app/api-custom-events-metrics.md#track-exception
[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: java-get-started.md
[javalogs]: java-trace-logs.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md


