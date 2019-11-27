---
title: Azure Container インスタンスで LUIS コンテナーをデプロイする
titleSuffix: Azure Cognitive Services
description: LUIS コンテナーを Azure Container インスタンスにデプロイし、Web ブラウザーでテストします。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: aecbb9bb94fc251ee0142b611c54d16304793e50
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2019
ms.locfileid: "73901815"
---
# <a name="deploy-the-language-understanding-luis-container-to-azure-container-instances"></a>Language Understanding (LUIS) コンテナーを Azure Container インスタンスにデプロイする

Cognitive Services [LUIS](luis-container-howto.md) コンテナーを Azure [Container インスタンス](https://docs.microsoft.com/azure/container-instances/)にデプロイする方法について説明します。 この手順では、Anomaly Detector リソースの作成方法を実演します。 次に、関連するコンテナー イメージのプルについて説明します。 最後に、ブラウザーからこの 2 つのオーケストレーションを行う機能を取り上げます。 コンテナーを使用することで、開発者の関心をインフラストラクチャの管理から切り離し、アプリケーション開発に専念させることができます。

[!INCLUDE [Prerequisites](../containers/includes/container-prerequisites.md)]

[!INCLUDE [Create LUIS resource](includes/create-luis-resource.md)]

[!INCLUDE [Create LUIS Container instance resource](../containers/includes/create-container-instances-resource.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Next steps](../containers/includes/containers-next-steps.md)]
