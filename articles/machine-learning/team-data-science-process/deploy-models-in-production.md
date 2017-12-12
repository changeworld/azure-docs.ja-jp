---
title: "運用環境へモデルのデプロイ - Azure Machine Learning | Microsoft Docs"
description: "ビジネス意思決定を行う上で有効な役割を果たすために、実稼働環境にモデルを展開する方法を説明します。"
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/30/2017
ms.author: bradsev;
ms.openlocfilehash: c7be9eda2d6f37951eb120250861cf4a39b0141b
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/01/2017
---
# <a name="deploy-models-in-production"></a>運用環境でのモデルのデプロイ

運用環境へのデプロイによって、モデルがビジネスのアクティブな役割を担うことが可能になります。 デプロイされたモデルからの予測は、ビジネス上の決定に使用できます。

## <a name="production-platforms"></a>運用環境プラットフォーム
モデルを運用環境にデプロイするには、さまざまな方法とプラットフォームがあります。 いくつかの選択肢を次に示します。


- [Azure Machine Learning でのモデルのデプロイ](https://docs.microsoft.com/azure/machine-learning/preview/model-management-overview)
- [SQL server でのモデルのデプロイ](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)


>[!NOTE]
>デプロイの前に、モデル スコアリングの待機時間が、運用環境で使用できるほど十分に低いことを保証する必要があります。
>


>[!NOTE]
>Azure Machine Learning Studio を使用したデプロイについては、「[Azure Machine Learning Web サービスをデプロイする](../studio/publish-a-machine-learning-web-service.md)」をご覧ください。
>

## <a name="ab-testing"></a>A/B テスト
複数のモデルが運用環境にある場合、[A/B テスト](https://en.wikipedia.org/wiki/A/B_testing)を実行してモデルのパフォーマンスを比較することが役立つ可能性があります。 

 
## <a name="next-steps"></a>次のステップ

また、**特定のシナリオ**のプロセスにおけるすべての手順を説明するチュートリアルも用意されています。 これらは、[サンプル チュートリアル](walkthroughs.md)の記事で簡単な説明と共にリンク付きで紹介されています。 チュートリアルでは、クラウド、オンプレミスのツール、サービスをワークフローまたはパイプラインに組み込んでインテリジェント アプリケーションを作成する方法を説明しています。 
 


