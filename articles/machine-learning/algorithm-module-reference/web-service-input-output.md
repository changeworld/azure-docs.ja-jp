---
title: Web サービスの入出力
description: Azure Machine Learning デザイナー (プレビュー) の Web サービス モジュールについて説明します
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/13/2020
ms.openlocfilehash: be940e3ba693270707e22ffc7b9377dbea1df5f8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "81462023"
---
# <a name="web-service-inputoutput"></a>Web サービスの入出力

この記事では、Azure Machine Learning デザイナー (プレビュー) の **Web サービスの入力**モジュールと **Web サービスの出力**モジュールについて説明します。

**Web サービスの入力**モジュールは、**DataFrameDirectory** タイプでのみ入力ポートに接続できます。 **Web サービスの出力**モジュールは、**DataFrameDirectory** タイプでのみ出力ポートに接続できます。 この 2 つのモジュールは、モジュールツリーの **Web サービス** カテゴリにあります。 

**Web サービスの入力**モジュールは、ユーザー データがパイプラインに入る場所を示すために使用され、**Web サービスの出力**モジュールは、ユーザー データがリアルタイム推論パイプラインに返される場所を示すために使用されます。

## <a name="how-to-use-web-service-inputoutput"></a>Web サービスの入出力を使用する方法

- トレーニング パイプラインからリアルタイム推論パイプラインを作成すると、**Web サービスの入力**モジュールと **Web サービスの出力**モジュールが自動的に追加され、ユーザー データがパイプラインに入る場所とデータが返される場所が示されます。 

    詳細については、「[リアルタイム推論パイプラインの作成](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-deploy#create-a-real-time-inference-pipeline)」を参照してください。

    > [!NOTE]
    > リアルタイム推論パイプラインの自動生成は、ルールベースのベストエフォート プロセスであり、正確さを保証するものではありません。 要件を満たすために、**Web サービスの入力または出力**モジュールを手動で追加または削除することができます。 リアルタイム推論パイプラインに、少なくとも 1 つの **Web サービスの入力**モジュールと 1 つの **Web サービスの出力**モジュールがあるようにしてください。 **Web サービスの入力**モジュールまたは **Web サービスの出力**モジュールが複数ある場合は、それらの名前が一意であることを確認してください。この名前はモジュールの右のパネルに入力できます。

- また、**Web サービスの入力**モジュールおよび **Web サービスの出力**モジュールを未送信のパイプラインに追加することで、リアルタイム推論パイプラインを手動で作成することもできます。

    > [!NOTE]
    >  パイプラインの種類は、初めて送信するときに決定されます。 したがって、リアルタイム推論パイプラインを作成する場合は、初めて送信する前に、**Web サービスの入力**モジュールと **Web サービスの出力**モジュールを必ず追加してください。

   次の例では、リアルタイム推論パイプラインを **Python スクリプトの実行**モジュールから手動で作成する方法を示します。 

   ![例](media/module/web-service-input-output-example.png)
   
   パイプラインを送信し、実行が正常に完了すると、リアルタイム エンドポイントをデプロイできるようになります。
   
   > [!NOTE]
   >  上の例では、**データの手動入力**が Web サービスの入力のためのデータ スキーマを提供し、リアルタイム エンドポイントをデプロイするために必要です。 一般に、データ スキーマを提供するには、**Web サービスの入力**が接続されているポートにモジュールまたはデータセットを常に接続する必要があります。
   
## <a name="next-steps"></a>次のステップ
[リアルタイム エンドポイントのデプロイ](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-deploy#deploy-the-real-time-endpoint)について学習します。
Azure Machine Learning で[使用できる一連のモジュール](module-reference.md)を参照してください。