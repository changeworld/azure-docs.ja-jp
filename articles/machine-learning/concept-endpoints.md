---
title: エンドポイント (プレビュー) とは
titleSuffix: Azure Machine Learning
description: Azure Machine Learning エンドポイント (プレビュー) を使用して機械学習のデプロイを簡略化する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: seramasu
author: rsethur
ms.reviewer: laobri
ms.custom: devplatv2
ms.date: 06/17/2021
ms.openlocfilehash: 8ce9241e11bd9aa259c8b7a1bf3114be677a01bd
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2021
ms.locfileid: "114447654"
---
# <a name="what-are-azure-machine-learning-endpoints-preview"></a>Azure Machine Learning エンドポイント (プレビュー) とは 

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

Azure Machine Learning エンドポイント (プレビュー) を使用して、リアルタイムとバッチ、両方の推論のデプロイについてモデル デプロイを効率化します。 エンドポイントは、複数のコンピューティングの種類にわたってモデル デプロイを起動および管理するための統一インターフェイスを提供します。

この記事では、次の内容について説明します。
> [!div class="checklist"]
> * エンドポイント
> * デプロイメント
> * マネージド オンライン エンドポイント
> * Azure Kubernetes Service (AKS) オンライン エンドポイント
> * バッチ推論エンドポイント

## <a name="what-are-endpoints-and-deployments-preview"></a>エンドポイントとデプロイ (プレビュー) とは

機械学習モデルをトレーニングした後、他のユーザーがそのモデルを使用して推論を実行できるようにするために、それをデプロイする必要があります。 Azure Machine Learning では、**エンドポイント** (プレビュー) と **デプロイ** (プレビュー) を使用して、そうすることができます。

:::image type="content" source="media/concept-endpoints/endpoint-concept.png" alt-text="2 つのデプロイへのエンドポイント分割トラフィックを示す図":::

**エンドポイント** は、トレーニング済みモデルの推論 (スコアリング) 出力を受信するためにクライアントが呼び出すことができる HTTPS エンドポイントです。 次の機能を提供します。 
- "キーとトークン" ベースの認証を使用する認証 
- SSL ターミネーション 
- デプロイ間のトラフィック割り当て 
- 安定したスコアリング URI (endpoint-name.region.inference.ml.azure.com)


**デプロイ** は、実際の推論を実行するモデルをホストするコンピューティング リソースのセットです。 その構成要素を次に示します。 
- モデルの詳細 (コード、モデル、環境) 
- コンピューティング リソースとスケールの設定 
- 詳細設定 (要求とプローブの設定など)

1 つのエンドポイントに複数のデプロイを含めることができます。 エンドポイントとデプロイは、Azure portal に表示される、独立した ARM リソースです。

Azure Machine Learning では、エンドポイントとデプロイの概念を使用して、異なる種類のエンドポイント ([**オンライン エンドポイント**](#what-are-online-endpoints-preview)と [**バッチ エンドポイント**](#what-are-batch-endpoints-preview)) を実装します。

### <a name="multiple-developer-interfaces"></a>複数の開発者インターフェイス

複数の開発者ツールを使用して、バッチおよびオンライン エンドポイントを作成、管理します。
- Azure CLI
- ARM と REST API
- Azure Machine Learning スタジオ Web ポータル
- Azure portal (IT および管理者)
- Azure CLI インターフェイスと REST および ARM インターフェイスを使用した、CI/CD MLOps パイプラインのサポート

## <a name="what-are-online-endpoints-preview"></a>オンライン エンドポイント (プレビュー) とは

**オンライン エンドポイント** (プレビュー) は、オンライン (リアルタイム) の推論に使用されるエンドポイントです。 **バッチ エンドポイント** と比較すると、**オンライン エンドポイント** には、クライアントからデータを受信する準備が整い、リアルタイムで応答を返信できる **デプロイ** が含まれています。

### <a name="online-endpoints-requirements"></a>オンライン エンドポイントの要件

オンライン エンドポイントを作成するには、以下を指定する必要があります。
- モデル ファイル (または自分のワークスペース内の登録済みモデルを指定) 
- スコアリング スクリプト - スコアリングおよび推論を実行するために必要なコード
- 環境 - Conda 依存関係がある Docker イメージ、または dockerfile 
- コンピューティング インスタンスとスケールの設定 

[CLI](how-to-deploy-managed-online-endpoints.md) および[スタジオ Web ポータル](how-to-use-managed-online-endpoint-studio.md)からオンライン エンドポイントをデプロイする方法について学習してください。

### <a name="test-and-deploy-locally-for-faster-debugging"></a>迅速なデバッグのためにローカルでテストおよびデプロイする

エンドポイントをテストするために、クラウドにはデプロイせず、ローカルにデプロイします。 Azure Machine Learning によって、Azure ML イメージを模倣するローカル Docker イメージが作成されます。 Azure Machine Learning は、ローカルでデプロイを構築して実行し、迅速な反復処理用にイメージをキャッシュします。

### <a name="native-bluegreen-deployment"></a>ネイティブ ブルーグリーン デプロイ 

1 つのエンドポイントに複数のデプロイを含めることができることを思い出してください。 オンライン エンドポイントでは、負荷分散を実行して、各デプロイに任意の割合のトラフィックを割り当てることができます。

トラフィック割り当てを使用すると、異なるインスタンス間で要求を分散することによって、ブルーグリーン デプロイの安全なロールアウトを実施できます。

:::image type="content" source="media/concept-endpoints/traffic-allocation.png" alt-text="デプロイ間のトラフィック割り当てを設定するためのスライダー インターフェイスを示すスクリーンショット":::

[オンライン エンドポイントに安全にロールアウト](how-to-safely-rollout-managed-endpoints.md)する方法について学習してください。

### <a name="application-insights-integration"></a>Application Insights の統合

すべてのオンライン エンドポイントは、SLA の監視と問題の診断のために、Application Insights と統合されています。 

ただし、[マネージド オンライン エンドポイント](#managed-online-endpoints-vs-aks-online-endpoints-preview)には、Azure ログと Azure メトリックとの追加設定なしの統合も含まれています。

### <a name="security"></a>セキュリティ

- 認証: キーと Azure ML トークン
- マネージド ID: ユーザー割り当てとシステム割り当て (マネージド オンライン エンドポイントのみ)
- エンドポイント呼び出しのための既定の SSL


## <a name="managed-online-endpoints-vs-aks-online-endpoints-preview"></a>マネージド オンライン エンドポイントと AKS オンライン エンドポイント (プレビュー)

オンライン エンドポイントには、2 つの種類があります。**マネージド オンライン エンドポイント** (プレビュー) と **AKS オンライン エンドポイント** (プレビュー) です。 次の表は、主な違いの一部を示しています。

|  | マネージド オンライン エンドポイント | AKS オンライン エンドポイント |
|-|-|-|
| **推奨されるユーザー** | マネージド モデル デプロイおよび拡張された MLOps エクスペリエンスを必要とするユーザー | Azure Kubernetes Service (AKS) を使用し、インフラストラクチャの要件を自己管理できるユーザー |
| **インフラストラクチャの管理** | マネージド コンピューティング プロビジョニング、スケーリング、ホスト OS イメージの更新、およびセキュリティ強化 | ユーザーの責任での対応 |
| **コンピューティングの種類** | マネージド (AmlCompute) | AKS |
| **追加設定なしの監視** | [Azure 監視](how-to-monitor-online-endpoints.md) <br> (待ち時間やスループットなど、主要なメトリックが含まれます) | サポートされていない |
| **追加設定なしのログ** | [エンドポイント レベルでの Azure ログと Log Analytics](how-to-deploy-managed-online-endpoints.md#optional-integrate-with-log-analytics) | クラスター レベルでの手動セットアップ |
| **Application Insights** | サポートされています | サポートされています |
| **管理対象 ID** | [サポートされています](tutorial-deploy-managed-endpoints-using-system-managed-identity.md) | サポートされていません |
| **仮想ネットワーク (VNET)** | サポートされていません (パブリック プレビュー) | クラスター レベルで手動で構成する |
| **コストを表示する** | [エンドポイントとデプロイのレベル](how-to-view-online-endpoints-costs.md) | クラスター レベル |

### <a name="managed-online-endpoints"></a>マネージド オンライン エンドポイント

マネージド オンライン エンドポイントは、デプロイ プロセスを効率化するために役立ちます。 マネージド オンライン エンドポイントには、AKS オンライン エンドポイントと比べて、以下の利点があります。

- マネージド インフラストラクチャ
    - コンピューティングを自動的にプロビジョニングし、モデルをホストします (VM の種類とスケールの設定のみ指定する必要があります) 
    - 基になるホスト OS イメージに対して更新プログラムとパッチを自動的に実行します
    - システム障害が発生した場合のノードの自動復旧

:::image type="content" source="media/concept-endpoints/log-analytics-and-azure-monitor.png" alt-text="エンドポイントの待ち時間の Azure Monitor グラフを示すスクリーンショット":::

- 監視とログ
    - [Azure Monitor とのネイティブ統合](how-to-monitor-online-endpoints.md)を使用して、モデルの可用性、パフォーマンス、および SLA を監視します。
    - ログと、Azure Log Analytics とのネイティブ統合を使用して、デプロイをデバッグします。

- マネージド ID
    -  [マネージド ID を使用して、スコアリング スクリプトからセキュリティで保護されたリソースにアクセス](tutorial-deploy-managed-endpoints-using-system-managed-identity.md)します

:::image type="content" source="media/concept-endpoints/endpoint-deployment-costs.png" alt-text="エンドポイントとデプロイのコスト グラフのスクリーンショット":::

- コストを表示する 
    - マネージド オンライン エンドポイントを使用すると、[エンドポイントとデプロイのレベルでコストを監視](how-to-view-online-endpoints-costs.md)できます。

ステップバイステップのチュートリアルについては、[マネージド オンライン エンドポイントをデプロイする方法](how-to-deploy-managed-online-endpoints.md)に関するページを参照してください。

## <a name="what-are-batch-endpoints-preview"></a>バッチ エンドポイント (プレビュー) とは

**バッチ エンドポイント** (プレビュー) は、一定期間に大量のデータに対してバッチ推論を実行するために使用されるエンドポイントです。  **バッチ エンドポイント** は、データへのポインターを受け取り、ジョブを非同期に実行して、複数のコンピューティング クラスターでデータを並列に処理します。 バッチ エンドポイントは、さらに詳細な分析のために、出力をデータ ストアに格納します。

[Azure CLI を使用してバッチ エンドポイントをデプロイおよび使用する](how-to-use-batch-endpoint.md)方法について学習してください。

### <a name="no-code-mlflow-model-deployments"></a>コードなしの MLflow モデルのデプロイ

スコアリング スクリプトと実行環境が自動的に作成されるようにするには、[MLflow モデル](how-to-use-mlflow.md)用のコードなしのバッチ エンドポイント作成エクスペリエンスを使用します。  

MLflow モデルを使用するバッチ エンドポイントの場合は、以下を指定する必要があります。
- モデル ファイル (または自分のワークスペース内の登録済みモデルを指定)
- コンピューティング ターゲット

ただし、MLflow モデルをデプロイして **いない** 場合は、追加の要件を指定する必要があります。
- スコアリング スクリプト - スコアリングおよび推論を実行するために必要なコード
- 環境 - Conda 依存関係がある Docker イメージ


### <a name="managed-cost-with-autoscaling-compute"></a>自動スケーリング コンピューティングによって管理されるコスト

バッチ エンドポイントを呼び出すと、非同期バッチ推論ジョブがトリガーされます。 コンピューティング リソースは、ジョブの開始時に自動的にプロビジョニングされ、ジョブの完了時に自動的に割り当て解除されます。 そのため、コンピューティングを使用しているときにのみ料金を支払うことになります。

実行を高速化したり、コストを削減したりするために、個々のバッチ推論ジョブの[コンピューティング リソースの設定](how-to-use-batch-endpoint.md#overwrite-settings) (インスタンス数など) と詳細設定 (ミニ バッチ サイズ、エラーしきい値など) をオーバーライドできます。

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
