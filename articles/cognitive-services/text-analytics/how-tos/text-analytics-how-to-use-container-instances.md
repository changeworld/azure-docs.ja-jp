---
title: Azure Container Instances の実行 - Text Analytics
titleSuffix: Azure Cognitive Services
description: Text Analytics コンテナーを Azure Container Instances にデプロイし、それを Web ブラウザーでテストします。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: 7ae8fbd404c9c2b650e4eed30c219e8ffafe55f7
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383521"
---
# <a name="deploy-a-text-analytics-container-to-azure-container-instances"></a>Text Analytics コンテナーを Azure Container Instances にデプロイする

Cognitive Services の [Text Analytics][install-and-run-containers] コンテナーを Azure [Container Instances][container-instances] にデプロイする方法について学習します。 この手順では、Text Analytics リソースの作成、関連付けられる感情分析イメージの作成、およびこの 2 つのオーケストレーションをブラウザーから実行する機能について例示します。 コンテナーを使用することで、開発者の関心をインフラストラクチャの管理から切り離し、アプリケーション開発に専念させることができます。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプションを使用してください。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances](../../containers/includes/create-container-instances-resource.md)]

#### <a name="key-phrase-extractiontabkeyphrase"></a>[キー フレーズ抽出](#tab/keyphrase)

[!INCLUDE [Verify the Key Phrase Extraction container instance](../includes/verify-key-phrase-extraction-container.md)]

#### <a name="language-detectiontablanguage"></a>[言語検出](#tab/language)

[!INCLUDE [Verify the Language Detection container instance](../includes/verify-language-detection-container.md)]

#### <a name="sentiment-analysistabsentiment"></a>[感情分析](#tab/sentiment)

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

***

## <a name="next-steps"></a>次の手順 

* さらに [Cognitive Services コンテナー](../../cognitive-services-container-support.md)を使用する
* [Text Analytics 接続済みサービス](../vs-text-connected-service.md)を使用する

[install-and-run-containers]: ./text-analytics-how-to-install-containers.md
[container-instances]: https://docs.microsoft.com/azure/container-instances