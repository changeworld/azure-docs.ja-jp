---
title: Team Data Science Process ライフサイクルのデプロイ ステージ
description: データ サイエンス プロジェクトのデプロイ ステージの目標、タスク、成果物
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 1138c95274c769186a9a29aa4d35517e378baeae
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720488"
---
# <a name="deployment-stage-of-the-team-data-science-process-lifecycle"></a>Team Data Science Process ライフサイクルのデプロイ ステージ

この記事では、Team Data Science Process (TDSP) のデプロイ ステージに関連付けられている目標、タスク、成果物のアウトラインを示します。 このプロセスには、データ サイエンス プロジェクトを体系化するために使用できる推奨ライフサイクルが用意されています。 ライフサイクルは、プロジェクトで通常 (多くの場合に繰り返し) 実行される主要なステージのアウトラインを示します。

   1. **ビジネスの把握**
   2. **データの取得と理解**
   3. **モデリング**
   4. **デプロイ**
   5. **顧客による受け入れ**

TDSP ライフサイクルを視覚化すると、次のようになります。 

![TDSP ライフサイクル](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>目標
データ パイプラインのあるモデルを、最終ユーザーによる受け入れに向けて、運用環境または運用環境に似た環境にデプロイします。 

## <a name="how-to-do-it"></a>方法
このステージでは、以下に示す主な課題に取り組みます。

**モデルの運用化**:モデルとパイプラインを、アプリケーションで利用できるように運用環境または運用環境に似た環境にデプロイします。

### <a name="operationalize-a-model"></a>モデルの運用化
うまく機能する一連のモデルが得られたら、他のアプリケーションから利用できるように、それらのモデルを運用できるようにします。 予測は、ビジネス要件に応じてリアルタイムまたはバッチ処理で行われます。 モデルをデプロイするには、オープン API インターフェイスを使用してそれらを公開します。 インターフェイスを使用して、以下のさまざまなアプリケーションでモデルを簡単に使用できるようにすることができます。

   * オンライン Web サイト
   * スプレッドシート 
   * ダッシュボード
   * 基幹業務アプリケーション 
   * バックエンド アプリケーション 

Azure Machine Learning Web サービスによるモデルの運用化の例については、「[Azure Machine Learning Web サービスをデプロイする](../studio/deploy-a-machine-learning-web-service.md)」を参照してください。 ベスト プラクティスは、デプロイする運用モデルとデータ パイプラインにテレメトリと監視を組み込むことです。 これは、その後のシステム状態のレポートとトラブルシューティングに役立ちます。  

## <a name="artifacts"></a>アーティファクト

* システムの正常性と主なメトリックを表示するステータス ダッシュボード
* デプロイの詳細を含む最終的なモデリング レポート
* 最終的なソリューション アーキテクチャ ドキュメント


## <a name="next-steps"></a>次のステップ

TDSP のライフサイクルの各ステップへのリンクを次に示します。

   1. [ビジネスの把握](lifecycle-business-understanding.md)
   2. [データの取得と理解](lifecycle-data.md)
   3. [モデリング](lifecycle-modeling.md)
   4. [デプロイ](lifecycle-deployment.md)
   5. [顧客による受け入れ](lifecycle-acceptance.md)

特定のシナリオのプロセスでのすべての手順を示す完全なチュートリアルが用意されています。 [例を含むチュートリアル](walkthroughs.md)の記事に、各シナリオが、簡潔な説明とリンク付きで示されています。 これらのチュートリアルは、クラウドとオンプレミスのツールおよびサービスをワークフローまたはパイプラインに組み込んで、インテリジェントなアプリケーションを作成する方法について説明しています。 

Azure Machine Learning Studio を使用して TDSP の手順を実行する方法の例については、「[Azure Machine Learning での Team Data Science Process の使用](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)」を参照してください。
