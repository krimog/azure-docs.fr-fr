---
title: Exemples de notebooks Jupyter
titleSuffix: Azure Machine Learning
description: Recherchez et utilisez des exemples de notebooks Jupyter pour explorer le kit SDK Azure Machine Learning pour Python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.date: 11/04/2019
ms.custom: seodec18
ms.openlocfilehash: 16066ceeb7196bb181d144cc9529d6db627ed828
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73582529"
---
# <a name="explore-azure-machine-learning-with-jupyter-notebooks"></a>Explorer Azure Machine Learning avec des notebooks Jupyter

L’[exemple de dépôt Azure Machine Learning Notebooks](https://github.com/azure/machinelearningnotebooks) contient les derniers exemples du Kit de développement logiciel (SDK) Python Azure Machine Learning. Ces notebooks Jupyter sont conçus pour vous aider à explorer le SDK et servent de modèles pour vos propres projets Machine Learning.

Cet article vous montre comment accéder au dépôt dans les environnements suivants :

- [Machine virtuelle Notebook Azure Machine Learning](#notebookvm)
- [Utiliser son propre serveur de notebooks](#byo)
- [Data Science Virtual Machine](#dsvm) (Machine virtuelle Science des données)

> [!NOTE]
> Une fois que vous avez cloné le dépôt, vous voyez que des notebooks de tutoriel se trouvent dans le dossier **tutorials**, et que des notebooks relatifs à certaines fonctionnalités se trouvent dans le dossier **how-to-use-azureml**.

<a name="notebookvm"></a>
## <a name="get-samples-on-azure-machine-learning-notebook-vm"></a>Recevoir des exemples sur une machine virtuelle Notebook Azure Machine Learning

Pour bien démarrer avec les exemples, le plus simple est de suivre le [Tutoriel : Configurer l’environnement et l’espace de travail](tutorial-1st-experiment-sdk-setup.md). Une fois que vous l’aurez suivi, vous disposerez d’un serveur de notebook dédié dans lequel auront été préchargés le SDK et l’exemple de dépôt. Aucune installation ni aucun téléchargement ne sont nécessaires.

<a name="byo"></a>

## <a name="get-samples-on-your-notebook-server"></a>Recevoir des exemples sur votre serveur de notebook

Si vous souhaitez utiliser votre propre serveur de notebooks pour le développement local, effectuez les étapes suivantes :

[!INCLUDE [aml-your-server](../../../includes/aml-your-server.md)]

Ces instructions permettent d’installer les packages de base du SDK qui sont nécessaires pour le guide de démarrage rapide et les notebooks du tutoriel. Les autres exemples de notebooks peuvent nécessiter l’installation de composants supplémentaires. Pour plus d’informations, consultez [Installer le kit SDK Azure Machine Learning pour Python](https://docs.microsoft.com/python/api/overview/azure/ml/install).

<a name="dsvm"></a>
## <a name="get-samples-on-dsvm"></a>Recevoir des exemples sur DSVM

La machine virtuelle DSVM (Data Science Virtual Machine) est une image de machine virtuelle personnalisée spécialement conçue pour la science des données. Lorsque vous [créez une machine virtuelle DSVM](how-to-configure-environment.md#dsvm), le SDK et le serveur de notebooks sont installés et configurés automatiquement. Toutefois, vous devrez créer un espace de travail et cloner l’exemple de dépôt.

[!INCLUDE [aml-dsvm-server](../../../includes/aml-dsvm-server.md)]

## <a name="next-steps"></a>Étapes suivantes

Explorez les [exemples de notebooks](https://aka.ms/aml-notebooks) pour découvrir ce que permet de faire Azure Machine Learning, ou essayez ces tutoriels :

- [Entraîner un modèle de classification d’images avec MNIST](tutorial-train-models-with-aml.md)

- [Préparer des données et utiliser le machine learning automatisé pour entraîner un modèle de régression avec le jeu de données NYC Taxi](tutorial-auto-train-models.md)
