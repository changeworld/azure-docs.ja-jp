---
title: 運用環境でのモデルのデプロイ - Team Data Science Process
description: ビジネス意思決定を行う上で有効な役割を果たすために、実稼働環境にモデルを展開する方法を説明します。
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/30/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: ed48ab9f85d28bec926120fea87a5ccf8bb6f9af
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/28/2019
ms.locfileid: "75526772"
---
# <a name="deploy-models-to-production-to-play-an-active-role-in-making-business-decisions"></a>ビジネス意思決定を行う上で有効な役割を果たすために、実稼働環境にモデルを展開します

運用環境へのデプロイによって、モデルがビジネスのアクティブな役割を担うことが可能になります。 デプロイされたモデルからの予測は、ビジネス上の決定に使用できます。

## <a name="production-platforms"></a>運用環境プラットフォーム

モデルを運用環境にデプロイするには、さまざまな方法とプラットフォームがあります。 いくつかの選択肢を次に示します。

- [Azure Machine Learning を使用するモデルのデプロイ先](../how-to-deploy-and-where.md)
- [SQL server でのモデルのデプロイ](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

>[!NOTE]
>デプロイの前に、モデル スコアリングの待機時間が、運用環境で使用できるほど十分に低いことを保証する必要があります。
>

>[!NOTE]
>Azure Machine Learning Studio を使用したデプロイについては、「[Azure Machine Learning Web サービスをデプロイする](../studio/deploy-a-machine-learning-web-service.md)」をご覧ください。
>

## <a name="ab-testing"></a>A/B テスト

複数のモデルが運用環境にある場合、[A/B テスト](https://en.wikipedia.org/wiki/A/B_testing)を実行してモデルのパフォーマンスを比較することが役立つ可能性があります。 
 
## <a name="next-steps"></a>次のステップ

また、**特定のシナリオ**のプロセスにおけるすべての手順を説明するチュートリアルも用意されています。 これらは、[サンプル チュートリアル](walkthroughs.md)の記事で簡単な説明と共にリンク付きで紹介されています。 チュートリアルでは、クラウド、オンプレミスのツール、サービスをワークフローまたはパイプラインに組み込んでインテリジェント アプリケーションを作成する方法を説明しています。 
