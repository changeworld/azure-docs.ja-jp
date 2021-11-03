---
title: Conversational Language Understanding ジョブの送信方法
titleSuffix: Azure Cognitive Services
description: Conversation Language Understanding の要求を送信する方法について説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-clu, ignite-fall-2021
ms.openlocfilehash: e1f53678ef99fb5477700628e14127e5572da816
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131090021"
---
# <a name="deploy-and-test-model"></a>モデルのデプロイとテスト

データセットで[モデルをトレーニング](./train-model.md)したら、モデルをデプロイする準備が整います。 モデルをデプロイすると、予測のためにクエリを実行できるようになります。 

## <a name="deploy-model"></a>モデルのデプロイ

モデルのデプロイとは、モデルをホストし、エンドポイントを介して予測できるようにすることです。 プロジェクトごとにデプロイできるモデルは 1 つだけです。別のモデルをデプロイすると、以前にデプロイしたモデルが上書きされます。

モデルをデプロイすると、ポータルでモデルを直接テストしたり、関連付けられている API を呼び出してテストしたりすることができます。

モデルを選択し、[Deploy model]\(モデルのデプロイ\) ページの [Deploy model]\(モデルのデプロイ\) をクリックするだけです。 

:::image type="content" source="../media/deploy-model.png" alt-text="Language Studio のモデルのデプロイのページを示すスクリーンショット。" lightbox="../media/deploy-model.png":::

**オーケストレーション ワークフロー プロジェクトのデプロイ**

オーケストレーション ワークフロー プロジェクトをデプロイするときに、小さなウィンドウが表示され、そこでデプロイを確認し、接続済みサービスのパラメーターを構成します。

1 つ以上の LUIS アプリケーションに接続している場合は、デプロイ名と、スロットとバージョンのどちらのデプロイの種類を使用するかを指定します。        
* デプロイの種類がスロットの場合は、運用スロットとステージングスロットのどちらかを選択する必要があります。
* デプロイの種類がバージョンの場合は、発行済みのバージョンを指定する必要があります。

カスタム質問と回答および CLU 接続、またはリンクされていない意図には、構成は必要ありません。

LUIS プロジェクトは、オーケストレーションのデプロイ中に構成されたスロットに **発行する必要があります**。また、カスタムの質問と回答 KB も運用スロットに発行する必要があります。

:::image type="content" source="../media/deploy-connected-services.png" alt-text="オーケストレーション ワークフロー プロジェクトのデプロイ画面を示すスクリーンショット。" lightbox="../media/deploy-connected-services.png":::

## <a name="send-a-conversational-language-understanding-request"></a>Conversational Language Understanding 要求を送信する

モデルがデプロイされると、デプロイされたモデルを予測に使用できるようになります。 [Test model]\(モデルのテスト\) ページの外部では、指定されたカスタムエンドポイントへの API 要求を使用して、デプロイ済みモデルの呼び出しを開始できます。 このエンドポイント要求は、モデル内で定義されている意図とエンティティの予測を取得します。

エンドポイントの完全な URL を取得するには、 **[Deploy model]\(モデルのデプロイ\)** ページに移動し、デプロイしたモデルを選択して、 [Get prediction URL]\(予測 URL の取得\) をクリックします。

:::image type="content" source="../media/prediction-url.png" alt-text="予測の要求と URL を示すスクリーンショット" lightbox="../media/prediction-url.png":::

## <a name="api-response-for-a-conversations-project"></a>会話プロジェクトの API 応答

会話プロジェクトでは、プロジェクト内に存在する意図とエンティティの両方の予測が得られます。 
- 意図とエンティティには、プロジェクト内の特定の要素のモデルの予測がどの程度信頼できるかの確実性に関する、0.0 から 1.0 の信頼スコアが含まれます。 
- 上位スコアの意図は、独自のパラメーター内に含まれています。
- 予測されたエンティティのみが応答に表示されます。
- エンティティによって、次が示されます。
    - 抽出されたエンティティのテキスト
    - オフセット値で示される開始位置
    - 長さの値で表されるエンティティ テキストの長さ

## <a name="api-response-for-an-orchestration-workflow-project"></a>オーケストレーション ワークフロー プロジェクト の API 応答

オーケストレーション ワークフロー プロジェクトは、上位スコアの意図と接続済みサービスの応答とともに返されます。
- この目的で、targetKind パラメーターを使用して、オーケストレーターの最上位の意図 (会話、LUIS、または QnA Maker) によって返された応答の種類を特定できます。
- 接続済みサービスの応答を result パラメーターで取得します。 

要求内では、オーケストレーターがそのサービスにルーティングするときに、各接続済みサービスに対して追加のパラメーターを指定できます。
- プロジェクト パラメーター内では、必要に応じて、接続済みサービスに対して別のクエリを指定できます。 別のクエリを指定しない場合は、元のクエリが使用されます。
- *direct target* パラメーターを使用すると、オーケストレーターによるルーティングの決定をバイパスし、特定の接続済みの意図を直接ターゲットにして、応答を強制することができます。

## <a name="next-steps"></a>次のステップ

* [Conversational Language Understanding の概要](../overview.md)
