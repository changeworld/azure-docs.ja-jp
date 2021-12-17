---
title: 'Web サービスの入出力: コンポーネント リファレンス'
description: Azure Machine Learning デザイナーの Web サービス コンポーネントを使用して入力と出力を管理する方法について説明します。
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/13/2020
ms.openlocfilehash: 67741a786f60b45e2e0cc7f728aa136397220899
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131565696"
---
# <a name="web-service-input-and-web-service-output-components"></a>[Web Service Input]\(Web サービスの入力\) コンポーネントと [Web Service Output]\(Web サービスの出力\) モジュール

この記事では、Azure Machine Learning デザイナーの Web サービスの入力コンポーネントと Web サービスの出力コンポーネントについて説明します。

Web サービスの入力コンポーネントは、**DataFrameDirectory** タイプでのみ入力ポートに接続できます。 Web サービスの出力コンポーネントは、**DataFrameDirectory** タイプでのみ出力ポートに接続できます。 2 つのコンポーネントは、コンポーネント ツリーの **[Web サービス]** カテゴリ下で見つけることができます。 

Web サービスの入力コンポーネントでは、ユーザー データがパイプラインに入る場所が指示されます。 Web サービスの出力コンポーネントでは、リアルタイム推論パイプライン内でユーザー データが返される場所が指示されます。

## <a name="how-to-use-web-service-input-and-output"></a>Web サービスの入出力を使用する方法

トレーニング パイプラインから[リアルタイム推論パイプラインを作成する](../tutorial-designer-automobile-price-deploy.md#create-a-real-time-inference-pipeline)と、Web サービスの入力モジュールと Web サービスの出力コンポーネントが自動的に追加され、ユーザー データがパイプラインに入る場所とデータが返される場所が示されます。 

> [!NOTE]
> リアルタイム推論パイプラインの自動生成は、ルールベースのベストエフォート プロセスです。 正確性の保証はありません。 

要件を満たすために、Web サービスの入力および Web サービスの出力コンポーネントを手動で追加または削除することができます。 リアルタイム推論パイプラインには、少なくとも 1 つの Web サービスの入力コンポーネントと 1 つの Web サービスの出力コンポーネントがあるようにしてください。 Web サービスの入力コンポーネントまたは Web サービスの出力コンポーネントが複数ある場合は、それらの名前が一意であることを確認してください。 コンポーネントの右パネルに名前を入力できます。

また、Web サービスの入力コンポーネントおよび Web サービスの出力コンポーネントを未送信のパイプラインに追加することで、リアルタイム推論パイプラインを手動で作成することもできます。

> [!NOTE]
> パイプラインの種類は、初めて送信するときに決定されます。 初めて送信する前に、Web サービスの入力コンポーネントと Web サービスの出力コンポーネントを必ず追加してください。

次の例は、リアルタイム推論パイプラインを Python スクリプトの実行コンポーネントから手動で作成する方法を示します。 

![例](media/module/web-service-input-output-example.png)
   
パイプラインを送信して、実行が正常に完了すると、リアルタイム エンドポイントをデプロイできます。
   
> [!NOTE]
>  上の例では、**データの手動入力** は Web サービスの入力のためのデータ スキーマを提供しており、リアルタイム エンドポイントをデプロイするために必要です。 一般に、データ スキーマを提供するには、**Web サービスの入力** が接続されているポートにコンポーネントまたはデータセットを常に接続する必要があります。
   
## <a name="next-steps"></a>次のステップ
[リアルタイム エンドポイントのデプロイ](../tutorial-designer-automobile-price-deploy.md#deploy-the-real-time-endpoint)について学習します。

Azure Machine Learning で[使用できる一連のコンポーネント](component-reference.md)をご覧ください。