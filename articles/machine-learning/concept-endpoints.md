---
title: エンドポイント (プレビュー) とは
titleSuffix: Azure Machine Learning
description: Azure Machine Learning エンドポイント (プレビュー) を使用して機械学習のデプロイを簡略化する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.topic: conceptual
ms.author: seramasu
author: rsethur
ms.reviewer: laobri
ms.custom: devplatv2, ignite-fall-2021
ms.date: 10/21/2021
ms.openlocfilehash: b832daad3ca667df404617bdb4efc96e54b5c5ea
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132719943"
---
# <a name="what-are-azure-machine-learning-endpoints-preview"></a>Azure Machine Learning エンドポイント (プレビュー) とは 

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

Azure Machine Learning エンドポイント (プレビュー) を使用して、リアルタイムとバッチ、両方の推論のデプロイについてモデル デプロイを効率化します。 エンドポイントは、複数のコンピューティングの種類にわたってモデル デプロイを起動および管理するための統一インターフェイスを提供します。

この記事では、次の内容について説明します。
> [!div class="checklist"]
> * エンドポイント
> * デプロイメント
> * マネージド オンライン エンドポイント
> * Kubernetes オンライン エンドポイント
> * バッチ推論エンドポイント

## <a name="what-are-endpoints-and-deployments-preview"></a>エンドポイントとデプロイ (プレビュー) とは

機械学習モデルをトレーニングした後、他のユーザーがそのモデルを使用して推論を実行できるようにするために、それをデプロイする必要があります。 Azure Machine Learning では、**エンドポイント** (プレビュー) と **デプロイ** (プレビュー) を使用して、そうすることができます。

**エンドポイント** は、トレーニング済みモデルの推論 (スコアリング) 出力を受信するためにクライアントが呼び出すことができる HTTPS エンドポイントです。 次の機能を提供します。 
- "キーとトークン" ベースの認証を使用する認証 
- SSL ターミネーション 
- 安定したスコアリング URI (endpoint-name.region.inference.ml.azure.com)


**デプロイ** は、実際の推論を実行するモデルをホストするのに必要なリソースのセットです。 

1 つのエンドポイントに複数のデプロイを含めることができます。 エンドポイントとデプロイは、Azure portal に表示される、独立した Azure Resource Manager リソースです。

Azure Machine Learning では、エンドポイントとデプロイの概念を使用して、異なる種類のエンドポイント ([オンライン エンドポイント](#what-are-online-endpoints-preview)と[バッチ エンドポイント](#what-are-batch-endpoints-preview)) を実装します。

### <a name="multiple-developer-interfaces"></a>複数の開発者インターフェイス

複数の開発者ツールを使用して、バッチおよびオンライン エンドポイントを作成、管理します。
- Azure CLI
- Azure Resource Manager/REST API
- Azure Machine Learning スタジオ Web ポータル
- Azure portal (IT および管理者)
- Azure CLI インターフェイスと REST および ARM インターフェイスを使用した、CI/CD MLOps パイプラインのサポート

## <a name="what-are-online-endpoints-preview"></a>オンライン エンドポイント (プレビュー) とは

**オンライン エンドポイント** (プレビュー) は、オンライン (リアルタイム) の推論に使用されるエンドポイントです。 **バッチ エンドポイント** と比較すると、**オンライン エンドポイント** には、クライアントからデータを受信する準備が整い、リアルタイムで応答を返信できる **デプロイ** が含まれています。

次の図は、"blue" と "green" の 2 つのデプロイを持つオンライン エンドポイントを示しています。 Blue デプロイでは、CPU SKU を持つ VM が使用され、モデルの v1 が実行されます。 緑のデプロイでは、GPU SKU を持つ VM が使用され、モデルの v2 が使用されます。 エンドポイントは、着信トラフィックの 90% を青色のデプロイにルーティングするように構成されていますが、緑は残りの 10% を受け取ります。

:::image type="content" source="media/concept-endpoints/endpoint-concept.png" alt-text="2 つのデプロイへのエンドポイント分割トラフィックを示す図":::

### <a name="online-endpoints-requirements"></a>オンライン エンドポイントの要件

オンライン エンドポイントを作成するには、次の要素を指定する必要があります。
- モデル ファイル (または自分のワークスペース内の登録済みモデルを指定) 
- スコアリング スクリプト - スコアリングおよび推論を実行するために必要なコード
- 環境 - Conda 依存関係がある Docker イメージ、または dockerfile 
- コンピューティング インスタンスとスケールの設定 

[CLI](how-to-deploy-managed-online-endpoints.md) および[スタジオ Web ポータル](how-to-use-managed-online-endpoint-studio.md)からオンライン エンドポイントをデプロイする方法について学習してください。

### <a name="test-and-deploy-locally-for-faster-debugging"></a>迅速なデバッグのためにローカルでテストおよびデプロイする

エンドポイントをテストするために、クラウドにはデプロイせず、ローカルにデプロイします。 Azure Machine Learning によって、Azure ML イメージを模倣するローカル Docker イメージが作成されます。 Azure Machine Learning は、ローカルでデプロイを構築して実行し、迅速な反復処理用にイメージをキャッシュします。

### <a name="native-bluegreen-deployment"></a>ネイティブ ブルーグリーン デプロイ 

1 つのエンドポイントに複数のデプロイを含めることができることを思い出してください。 オンライン エンドポイントでは、負荷分散を実行して、各デプロイに任意の割合のトラフィックを与えられます。

トラフィック割り当てを使用すると、異なるインスタンス間で要求を分散することによって、ブルーグリーン デプロイの安全なロールアウトを実施できます。

> [!TIP]
> 要求では、`azureml-model-deployment` の HTTP ヘッダーを含めることによって、構成されたトラフィックの負荷分散をバイパスできます。 ヘッダーの値を、要求のルーティング先のデプロイの名前に設定します。

:::image type="content" source="media/concept-endpoints/traffic-allocation.png" alt-text="デプロイ間のトラフィック割り当てを設定するためのスライダー インターフェイスを示すスクリーンショット":::

[オンライン エンドポイントに安全にロールアウト](how-to-safely-rollout-managed-endpoints.md)する方法について学習してください。

### <a name="application-insights-integration"></a>Application Insights の統合

すべてのオンライン エンドポイントは、SLA の監視と問題の診断のために、Application Insights と統合されています。 

ただし、[マネージド オンライン エンドポイント](#managed-online-endpoints-vs-kubernetes-online-endpoints-preview)には、Azure ログと Azure メトリックとの追加設定なしの統合も含まれています。

### <a name="security"></a>セキュリティ

- 認証: キーと Azure ML トークン
- マネージド ID: ユーザー割り当てとシステム割り当て (マネージド オンライン エンドポイントのみ)
- エンドポイント呼び出しのための既定の SSL

### <a name="autoscaling"></a>自動スケール

自動スケールでは、アプリケーションの負荷を処理するために適切な量のリソースが自動的に実行されます。 マネージド エンドポイントは、[Azure Monitor 自動スケーリング](../azure-monitor/autoscale/autoscale-overview.md)機能との統合によって、自動スケールをサポートします。 メトリックベースのスケーリング (たとえば、CPU 使用率 >70%)、スケジュールに基づくスケーリング (たとえば、営業時間のピーク時のルールのスケーリング)、またはその組み合わせを構成できます。

:::image type="content" source="media/concept-endpoints/concept-autoscale.png" alt-text="ルールに応じて、最小インスタンスと最大インスタンスの間で自動スケールが柔軟に提供することを示すスクリーンショット":::

### <a name="visual-studio-code-debugging"></a>Visual Studio Code のデバッグ

Visual Studio Code を使用すると、エンドポイントを対話的にデバッグできます。

:::image type="content" source="media/concept-endpoints/visual-studio-code-full.png" alt-text="VSCode でのエンドポイント デバッグのスクリーンショット。" lightbox="media/concept-endpoints/visual-studio-code-full.png" :::

## <a name="managed-online-endpoints-vs-kubernetes-online-endpoints-preview"></a>マネージド オンライン エンドポイントと Kubernetes オンライン エンドポイント (プレビュー)

オンライン エンドポイントには、2 つの種類があります。**マネージド オンライン エンドポイント** (プレビュー) と **Kubernetes オンライン エンドポイント** (プレビュー) です。 次の表は、主な違いの一部を示しています。

|  | マネージド オンライン エンドポイント | Kubernetes オンライン エンドポイント |
|-|-|-|
| **推奨されるユーザー** | マネージド モデル デプロイおよび拡張された MLOps エクスペリエンスを必要とするユーザー | Kubernetes を使用し、インフラストラクチャの要件を自己管理できるユーザー |
| **インフラストラクチャの管理** | マネージド コンピューティング プロビジョニング、スケーリング、ホスト OS イメージの更新、およびセキュリティ強化 | ユーザーの責任での対応 |
| **コンピューティングの種類** | マネージド (AmlCompute) | Kubernetes クラスター (Kubernetes) |
| **追加設定なしの監視** | [Azure 監視](how-to-monitor-online-endpoints.md) <br> (待ち時間やスループットなど、主要なメトリックが含まれます) | サポートされていない |
| **追加設定なしのログ** | [エンドポイント レベルでの Azure ログと Log Analytics](how-to-deploy-managed-online-endpoints.md#optional-integrate-with-log-analytics) | サポートされています |
| **Application Insights** | サポートされています | サポートされています |
| **管理対象 ID** | [サポートされています](how-to-access-resources-from-endpoints-managed-identities.md) | サポートされています |
| **仮想ネットワーク (VNET)** | まだサポートされていません (現在取り組み中です) | サポートされています |
| **コストを表示する** | [エンドポイントとデプロイのレベル](how-to-view-online-endpoints-costs.md) | クラスター レベル |

### <a name="managed-online-endpoints"></a>マネージド オンライン エンドポイント

マネージド オンライン エンドポイントは、デプロイ プロセスを効率化するために役立ちます。 マネージド オンライン エンドポイントには、Kubernetes オンライン エンドポイントと比べて、以下の利点があります。

- マネージド インフラストラクチャ
    - コンピューティングを自動的にプロビジョニングし、モデルをホストします (VM の種類とスケールの設定のみ指定する必要があります) 
    - 基になるホスト OS イメージを自動的に更新しパッチを適用する
    - システム障害が発生した場合のノードの自動回復

- 監視とログ
    - [Azure Monitor とのネイティブ統合](how-to-monitor-online-endpoints.md)を使用して、モデルの可用性、パフォーマンス、および SLA を監視します。
    - ログと、Azure Log Analytics とのネイティブ統合を使用して、デプロイをデバッグします。

    :::image type="content" source="media/concept-endpoints/log-analytics-and-azure-monitor.png" alt-text="エンドポイントの待ち時間の Azure Monitor グラフを示すスクリーンショット":::

- マネージド ID
    -  [マネージド ID を使用して、スコアリング スクリプトからセキュリティで保護されたリソースにアクセス](tutorial-deploy-managed-endpoints-using-system-managed-identity.md)します

- コストを表示する 
    - マネージド オンライン エンドポイントを使用すると、[エンドポイントとデプロイのレベルでコストを監視](how-to-view-online-endpoints-costs.md)できます。
    
    :::image type="content" source="media/concept-endpoints/endpoint-deployment-costs.png" alt-text="エンドポイントとデプロイのコスト グラフのスクリーンショット":::

ステップバイステップのチュートリアルについては、[マネージド オンライン エンドポイントをデプロイする方法](how-to-deploy-managed-online-endpoints.md)に関するページを参照してください。

## <a name="what-are-batch-endpoints-preview"></a>バッチ エンドポイント (プレビュー) とは

**バッチ エンドポイント** (プレビュー) は、一定期間に大量のデータに対してバッチ推論を実行するために使用されるエンドポイントです。  **バッチ エンドポイント** は、データへのポインターを受け取り、ジョブを非同期に実行して、複数のコンピューティング クラスターでデータを並列に処理します。 バッチ エンドポイントは、さらに詳細な分析のために、出力をデータ ストアに格納します。

:::image type="content" source="media/concept-endpoints/batch-endpoint.png" alt-text="1 つのバッチ エンドポイントで複数のデプロイ (そのうちの 1 つがデフォルト) に要求をルーティングできることを示す図。":::

### <a name="batch-deployment-requirements"></a>バッチ デプロイ要件

バッチ デプロイを作成するには、次の要素を指定する必要があります。

- モデル ファイル (または自分のワークスペース内の登録済みモデルを指定)
- Compute
- スコアリング スクリプト - スコアリングおよび推論を実行するために必要なコード
- 環境 - Conda 依存関係がある Docker イメージ

[MLflow モデル](how-to-train-cli.md#model-tracking-with-mlflow)を配置する場合は、両方が自動生成されるため、スコアリング スクリプトと実行環境を提供する必要はありません。

[Azure CLI を使用してバッチ エンドポイントをデプロイおよび使用する](how-to-use-batch-endpoint.md)方法と[スタジオ Web ポータル](how-to-use-batch-endpoints-studio.md)について学習してください。

### <a name="managed-cost-with-autoscaling-compute"></a>自動スケーリング コンピューティングによって管理されるコスト

バッチ エンドポイントを呼び出すと、非同期バッチ推論ジョブがトリガーされます。 コンピューティング リソースは、ジョブの開始時に自動的にプロビジョニングされ、ジョブの完了時に自動的に割り当て解除されます。 そのため、コンピューティングを使用しているときにのみ料金を支払うことになります。

実行を高速化し、コストを削減するために、個々のバッチ推論ジョブの[コンピューティング リソースの設定](how-to-use-batch-endpoint.md#configure-the-output-location-and-overwrite-settings) (インスタンス数など) と詳細設定 (ミニ バッチ サイズ、エラーしきい値など) をオーバーライドできます。

### <a name="flexible-data-sources-and-storage"></a>柔軟なデータ ソースとストレージ

バッチ エンドポイントを呼び出す際には、入力データに対する以下のオプションを使用できます。

- Azure Machine Learning の登録済みデータセット - 詳細については、「[Azure Machine Learning データセットを作成する](how-to-train-with-datasets.md)」を参照してください
- クラウド データ - パブリック データ URI またはデータストア内のデータ パス。 詳細については、「[Azure Machine Learning スタジオを使用してデータに接続する](how-to-connect-data-ui.md)」を参照してください
- ローカルに格納されているデータ

ストレージ出力の場所として任意のデータストアおよびパスを指定します。 既定では、バッチ エンドポイントは、ジョブ名 (システムによって生成される GUID) によって構成される、ワークスペースの既定の BLOB ストアに出力を格納します。

### <a name="security"></a>セキュリティ

- 認証: Azure Active Directory トークン
- エンドポイント呼び出しのための既定の SSL

## <a name="next-steps"></a>次の手順

- [Azure CLI を使用してマネージド オンライン エンドポイントをデプロイする方法](how-to-deploy-managed-online-endpoints.md)
- [Azure CLI を使用してバッチ エンドポイントをデプロイする方法](how-to-use-batch-endpoint.md)
- [スタジオを使用してマネージド オンライン エンドポイントを使用する方法](how-to-use-managed-online-endpoint-studio.md)
- [REST を使用してモデルをデプロイする (プレビュー)](how-to-deploy-with-rest.md)
- [マネージド オンライン エンドポイントを監視する方法](how-to-monitor-online-endpoints.md)
- [オンライン エンドポイントのコストを表示する方法](how-to-view-online-endpoints-costs.md)
- [Azure Machine Learning を使用するリソースのクォータの管理と引き上げ](how-to-manage-quotas.md#azure-machine-learning-managed-online-endpoints-preview)