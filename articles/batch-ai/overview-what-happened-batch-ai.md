---
title: Azure Batch AI の現状 | Microsoft Docs
description: Azure Batch AI と Azure Machine Learning service のコンピューティング オプションの現状について説明します。
ms.service: batch-ai
services: batch-ai
ms.topic: overview
ms.author: jmartens
author: j-martens
ms.date: 2/28/2019
ms.openlocfilehash: edd6a7e5f385d766d51631d77f5889233af2469a
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57194504"
---
# <a name="whats-happening-to-azure-batch-ai"></a>Azure Batch AI の現状

**Azure Batch AI サービスは 3 月に提供が停止されます。** Batch AI の大規模トレーニングとスコアリング機能は、[Azure Machine Learning service](../machine-learning/service/overview-what-is-azure-ml.md) (2018 年 12 月 4 日に一般公開) で利用できるようになりました。

他の多くの機械学習機能と同様に、Azure Machine Learning service には、機械学習モデルのトレーニング、デプロイ、スコアリングのためのクラウドベースのマネージド コンピューティング ターゲットが含まれています。 このコンピューティング ターゲットは、[Azure Machine Learning コンピューティング](../machine-learning/service/how-to-set-up-training-targets.md#amlcompute)と呼ばれています。 [今すぐ移行して使ってみましょう](#migrate)。 Azure Machine Learning service との対話には、[Python SDK](../machine-learning/service/quickstart-create-workspace-with-python.md)、コマンドライン インターフェイス、および [Azure portal](../machine-learning/service/quickstart-get-started.md) を使用できます。

## <a name="support-timeline"></a>サポートのタイムライン

今回は、以前と同じように既存の Azure Batch AI サブスクリプションを使用できます。 ただし、**新しいサブスクリプション**は作成できず、新しい投資は行われていません。

2019 年 3 月 31 日以降、既存の Batch AI サブスクリプションは機能しなくなります。&nbsp;&nbsp;

## <a name="compare-to-azure-machine-learning"></a>Azure Machine Learning との比較
機械学習モデルのトレーニング、デプロイ、自動化、管理を行うためのクラウド サービスであり、これらの操作のすべてをクラウドによって提供される幅広い規模で行うことができます。 [Azure Machine Learning service の概要についてはこちら](../machine-learning/service/overview-what-is-azure-ml.md)をご覧ください。
 

一般的なモデル開発ライフサイクルは、データの準備、トレーニングと実験、およびデプロイ フェーズで構成されます。 このエンド ツー エンドのサイクルは、Machine Learning パイプラインを使用して調整できます。

![フロー図](./media/overview-what-happened-batch-ai/lifecycle.png)


[サービスの動作方法とその主要な概念の詳細についてはこちら](../machine-learning/service/concept-azure-machine-learning-architecture.md)をご覧ください。 モデル トレーニング ワークフローの概念の多くは、Batch AI の既存の概念に似ています。 

具体的には、次の対応をご覧ください。
 
|Batch AI サービス|  Azure Machine Learning サービス|
|:--:|:---:|
|ワークスペース|ワークスペース|
|クラスター|   `AmlCompute` 型のコンピューティング|
|ファイル サーバー|データストア|
|実験|実験|
|[ジョブ]|実行 (入れ子の実行も可能)|
 
同じ表をさらに視覚化した別のビューを次に示します。
 
### <a name="batch-ai-hierarchy"></a>Batch AI の階層
![フロー図](./media/overview-what-happened-batch-ai/batchai-heirarchy.png) 
 
### <a name="azure-machine-learning-service-hierarchy"></a>Azure Machine Learning service の階層
![フロー図](./media/overview-what-happened-batch-ai/azure-machine-learning-service-heirarchy.png) 

## <a name="platform-capabilities"></a>プラットフォームの機能
Azure Machine Learning service には、エンド ツー エンドのトレーニングから、Azure リソースを管理する必要なく AI 開発に使用できるデプロイ スタックまで、優れた新機能のセットが含まれます。 次の表では、2 つのサービスのトレーニング機能のサポートを比較します。

|機能|Batch AI サービス|Azure Machine Learning サービス|
|-------|:-------:|:-------:|
|VM サイズの選択 |CPU/GPU    |CPU/GPU。 推論用に FPGA もサポートされいます|
|AI 対応のクラスター (ドライバー、Docker など)|  はい |はい|
|ノードの準備| はい|    いいえ |
|OS ファミリの選択   |部分的    |いいえ |
|専用 VM と LowPriority VM  |はい    |はい|
|自動スケーリング   |はい    |はい (既定)|
|自動スケーリングの待機時間  |いいえ  |はい|
|SSH    |はい|   はい|
|クラスター レベルのマウント |はい (ファイル共有、BBLOB、NFS、カスタム)   |はい (データストアのマウントまたはダウンロード)|
|分散トレーニング|  はい |はい|
|ジョブ実行モード|    VM またはコンテナー|    コンテナー|
|カスタム コンテナー イメージ|    はい |はい|
|任意のツールキット    |はい    |はい (Python スクリプトを実行)|
|JobPreparation|    はい |まだ、いいえ|
|ジョブ レベルのマウント |はい (ファイル共有、BBLOB、NFS、カスタム)   |はい (ファイル共有、BBLOB)|
|ジョブ監視     |GetJob 経由|    実行履歴 を使用 (より詳細な情報、さらにメトリックをプッシュするカスタム ランタイム)|
|ジョブ ログとファイル/モデルの取得 |   ListFiles API および Storage API 経由  |Artifact サービス経由|
 |Tensorboard のサポート   |いいえ |    はい|
|VM ファミリ レベルのクォータ |はい    |はい (以前の容量を引き継ぎ)|
 
前の表に加えて、Azure Machine Learning service には従来は BatchAI でサポートされていなかった機能があります。

|機能|Batch AI サービス|Azure Machine Learning サービス|
|-------|:-------:|:-------:|
|環境の準備    |いいえ  |はい (Conda の準備と ACR へのアップロード)|
|ハイパーパラメーターの調整  |いいえ |    はい|
|モデル管理   |いいえ  |はい|
|運用化/デプロイ| いいえ   |AKS と ACI 経由|
|データの準備   |いいえ  |はい|
|コンピューティング先    |Azure VM  |ローカル、BatchAI (AmlCompute として)、DataBricks、HDInsight|
|自動化された機械学習 |いいえ |    はい|
|パイプライン  |いいえ  |はい|
|バッチ スコアリング  |はい    |はい|
|ポータル/CLI のサポート|    はい |はい|


## <a name="programming-interfaces"></a>プログラミング インターフェイス

次の表では、各サービスで使用できるさまざまなプログラミング インターフェイスを示します。
    
|機能|BatchAI サービス|Azure Machine Learning サービス|
|-------|:-------:|:-------:|
|SDK    |Java、C#、Python、Nodejs   |Python (一般的なフレームワークに対する実行構成ベースと推定ベースの両方)|
|CLI    |はい    |まだ、いいえ|
|Azure ポータル   |はい    |はい (ジョブ送信を除く)|
|REST API   |はい    |はい、ただしマイクロサービス間に分散|


プレビューの BatchAI から GA の Azure Machine Learning service にアップグレードすると、Estimator やデータストアなどの使いやすい概念によるより優れたエクスペリエンスが提供されます。 また、GA レベルの Azure サービスの SLA とカスタマー サポートが保証されます。

Azure Machine Learning service では、機械学習の自動化、ハイパーパラメーターの調整、ML パイプラインなどの新しい機能も提供され、ほとんどの大規模な AI ワークロードで役に立ちます。 別のサービスに切り替えずにトレーニング済みモデルをデプロイする機能は、データの準備 (Data Prep SDK を使用) から、運用化とモデルの監視までの、完全なデータ サイエンス ループを作成するのに役立ちます。

<a name="migrate"></a>
## <a name="migrate"></a>移行

移行方法と、使用するコードが Azure Machine Learning service のコードにどのようにマッピングされるかについては、[Azure Machine Learning service への移行](how-to-migrate.md)に関する記事を参照してください。

## <a name="get-support"></a>サポートを受ける

Batch AI は 3 月 31 日に廃止が予定されており、サポートを通して例外を上げることによりホワイトリストに登録されている場合を除き、サービスに対して新しいサブスクリプションを登録することは既にできなくなっています。  Azure Machine Learning service への移行に関して質問やフィードバックがある場合は、[Azure Batch AI Training Preview](mailto:AzureBatchAITrainingPreview@service.microsoft.com) でお問い合わせください。

Azure Machine Learning service は一般提供のサービスです。 これは、コミットされた SLA があり、さまざまなサポート プランから選択できることを意味します。

基になっているコンピューティングの料金が変更されただけなので、Batch AI サービスと Azure Machine Learning service のどちらでも、Azure インフラストラクチャを使用する料金は変わらないはずです。 詳しくは、[料金計算ツール](https://azure.microsoft.com/pricing/details/machine-learning-service/)に関するページをご覧ください。

Azure portal で 2 つのサービスを利用できるリージョンについては、[こちら](https://azure.microsoft.com/global-infrastructure/services/?products=batch-ai,machine-learning-service&regions=all)をご覧ください。


## <a name="next-steps"></a>次の手順

+ [移行方法](how-to-migrate.md)と、使用するコードが Azure Machine Learning service のコードにどのようにマッピングされるかについて学習します。

+ 「[Azure Machine Learning サービスの概要](../machine-learning/service/overview-what-is-azure-ml.md)」を読みます。

+ Azure Machine Learning service を使用して[モデルのトレーニング用のコンピューティング ターゲットを構成](../machine-learning/service/how-to-set-up-training-targets.md)します。

+ [Azure のロードマップ](https://azure.microsoft.com/updates/)で、他の Azure サービスの更新情報を確認します。
