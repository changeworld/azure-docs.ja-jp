---
title: クラウド リージョン全体での機能の利用可否
titleSuffix: Azure Machine Learning
description: この記事では、パブリック クラウドと、Azure Government、Azure Germany、Azure China 21Vianet の各リージョンで、各機能を利用できるかどうかをリスト形式で示します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.reviewer: larryfr
ms.author: andzha
author: Anurzeuii
ms.date: 10/21/2021
ms.custom: references_regions
ms.openlocfilehash: 828c093eba73ad1b0042d63ed52b34ba47032acd
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131558176"
---
# <a name="azure-machine-learning-feature-availability-across-clouds-regions"></a>Azure Machine Learning ソブリン クラウドの同等性

Azure Government、Azure Germany、Azure China 21Vianet の各リージョンで、Azure Machine Learning のどの機能が利用できるかを説明します。 

世界の Azure リージョンには、パブリック クラウドのリージョンに加えて、特定の国・地域を対象とするいくつかのリージョンがあります。 たとえば、Azure Government と Azure China 21Vianet リージョンがあります。 Azure Machine Learning は、パブリック クラウド リージョンに加えて、次のリージョンでデプロイされています。

* Azure Government リージョンの **US-Arizona** および **US-Virginia**
* Azure China 21Vianet リージョンの **China-East-2**

このドキュメントの残りの部分では、これらのリージョンで Azure Machine Learning のどの機能が利用できるかを説明し、これらの機能の使用に関するリージョンごとの情報を提供します。
## <a name="azure-government"></a>Azure Government 

| 特徴量 | パブリック クラウドの状態  | 米国バージニア | 米国アリゾナ| 
|----------------------------------------------------------------------------|:----------------------:|:--------------------:|:-------------:|
| **[自動機械学習](concept-automated-ml.md)** | | | |
| ノートブックで実験を作成して実行する                                    | GA                   | YES                | YES         |
| Studio Web エクスペリエンスで実験を作成および実行する                        | パブリック プレビュー       | YES                | YES         |
| 業界トップ レベルの予測機能                                  | GA                   | YES                | YES         |
| ディープ ラーニングとその他の高度な学習機能のサポート                      | GA                   | YES                | YES         |
| 大規模なデータのサポート (最大 100 GB)                                          | パブリック プレビュー       | YES                | YES         |
| Azure Databricks の統合                                              | GA                   | NO                 | NO          |
| SQL、CosmosDB、および HDInsight の統合                                   | GA                   | YES                | YES         |
| **[Machine Learning パイプライン](concept-ml-pipelines.md)** |   |  | | 
| Azure ML SDK を使用してパイプラインを作成、実行、発行する                   | GA                   | YES                | YES         |
| Azure ML SDK を使用してパイプライン エンドポイントを作成する                           | GA                   | YES                | YES         |
| Azure ML SDK を使用したパイプラインのスケジュールされた実行の作成、編集、削除 | GA                   | はい*               | はい*        |
| Studio でパイプラインの実行の詳細を表示する                                        | GA                   | YES                | YES         |
| Azure ML デザイナーでパイプラインを作成、実行、視覚化、および発行する          | GA      | YES                | YES         |
| ML パイプラインとの Azure Databricks 統合                             | GA                   | NO                 | NO          |
| Azure ML デザイナーでのパイプライン エンドポイントの作成                             | GA      | YES                | YES         |
| **[統合されたノートブック](how-to-run-jupyter-notebooks.md)** |   |  | | 
| ワークスペース ノートブックとファイル共有                                        | GA                   | YES                | YES         |
| R および Python のサポート                                                       | GA                   | YES                | YES         |
| 仮想ネットワークのサポート                                                    | パブリック プレビュー       | NO                 | NO          |
| **[コンピューティング インスタンス](concept-compute-instance.md)** |   |  | | 
| 統合されたノートブックのマネージド コンピューティング インスタンス                         | GA                   | YES                | YES         |
| Jupyter、JupyterLab の統合                                            | GA                   | YES                | YES         |
| Virtual Network (VNet) のサポート                                             | パブリック プレビュー       | YES                | YES         |
| **SDK のサポート** |  |  | | 
| [Python SDK サポート](/python/api/overview/azure/ml/)                                                         | GA                   | YES                | YES         |
| **[Security](concept-enterprise-security.md)** |   | | | 
| トレーニングのための Virtual Network (VNet) サポート                                | GA                   | YES                | YES         |
| 推論のための Virtual Network (VNet) のサポート                               | GA                   | YES                | YES         |
| エンドポイント認証のスコア付け                                            | パブリック プレビュー       | YES                | YES         |
| ワークプレース プライベート エンドポイント                                                 | GA  |  GA  |  GA |
| VNet の背後にある ACI                                                            | パブリック プレビュー       | NO                 | NO          |
| VNet の背後にある ACR                                                            | GA       | YES                 | YES          |
| AKS クラスターのプライベート IP                                                  | パブリック プレビュー       | NO                 | NO          |
| **Compute** |   | | |
| [ワークスペース間のクォータ管理](how-to-manage-quotas.md)                                         | GA                   | YES                | YES         |
| **[機械学習用のデータ](concept-data.md)** |   | | |
| SDK からのデータセットとデータストアの作成、表示、または編集                  | GA                   | YES                | YES         |
| UI からのデータセットとデータストアの作成、表示、または編集                   | GA                   | YES                | YES         |
| SDK からのデータセット ドリフト モニターの表示、編集、または削除                   | パブリック プレビュー       | YES                | YES         |
| UI からのデータセット ドリフト モニターの表示、編集、または削除                    | パブリック プレビュー       | YES                | YES         |
| **Machine Learning のライフサイクル** |   | | |
| [データ プロファイル](how-to-deploy-profile-model.md)                                                            | GA                   | YES                | PARTIAL     |
| [Azure ML CLI 1.0](reference-azure-machine-learning-cli.md)     | GA                   | YES                | YES         |
| [FPGA ベースの Hardware Accelerated Models](how-to-deploy-fpga-web-service.md)                                     | GA                   | NO                 | NO          |
| [Visual Studio Code の統合](how-to-setup-vs-code.md)                                             | パブリック プレビュー       | NO                 | NO          |
| [イベント グリッドの統合](how-to-use-event-grid.md)                                                     | パブリック プレビュー       | NO                 | NO          |
| [Azure Stream Analytics と Azure Machine Learning を統合する](../stream-analytics/machine-learning-udf.md)               | パブリック プレビュー       | NO                 | NO          |
| **[画像](how-to-create-image-labeling-projects.md)と [テキスト](how-to-create-text-labeling-projects.md)にラベルを付ける** |   | | |
| プロジェクト管理ポータルのラベル付け                                        | GA                   | YES                | YES         |
| ラベラー ポータル                                                            | GA                   | YES                | YES         |
| プライベート ワークフォースを使用したラベル付け                                          | GA                   | YES                | YES         |
| ML によるラベル付け (イメージ分類とオブジェクトの検出)           | パブリック プレビュー       | YES                | YES         |
| **[信頼できる ML](concept-responsible-ml.md)** |   | | |
| UI での説明可能性                                                       | パブリック プレビュー       | NO                 | NO          |
| 差分プライバシー SmartNoise ツールキット                                    | OSS                  | NO                 | NO          |
| データシートを実装するための Azure Machine Learning のカスタム タグ              | GA                   | NO                 | NO          |
| 公平性 AzureML 統合                                               | パブリック プレビュー       | NO                 | NO          |
| SDK の解釈可能性                                                      | GA                   | YES                | YES         |
| **トレーニング** |   | | |
| [実験ログ ストリーミング](how-to-track-monitor-analyze-runs.md)                                              | GA                   | YES                | YES         |
| [強化学習](how-to-use-reinforcement-learning.md)                                                     | パブリック プレビュー       | NO                 | NO          |
| [実験 UI](how-to-track-monitor-analyze-runs.md)                                                         | パブリック プレビュー                   | YES                | YES         |
| [.NET 統合 ML.NET 1.0](/dotnet/machine-learning/tutorials/object-detection-model-builder)                                                | GA                   | YES                | YES         |
| **推論** |   | | |
| [バッチ推論](tutorial-pipeline-batch-scoring-classification.md)                                                          | GA                   | YES                | YES         |
| [FPGA での Data Box Edge](how-to-deploy-fpga-web-service.md#deploy-to-a-local-edge-server)                                                    | パブリック プレビュー       | NO                 | NO          |
| **その他** |   | | |
| [Open Datasets](../open-datasets/samples.md)                                                              | パブリック プレビュー       | YES                | YES         |
| [カスタム Cognitive Search](how-to-deploy-model-cognitive-search.md)                                                    | パブリック プレビュー       | YES                | YES         |


### <a name="azure-government-scenarios"></a>Azure Government シナリオ

| シナリオ                                                    | 米国バージニア | 米国アリゾナ| 制限事項  |
|----------------------------------------------------------------------------|:----------------------:|:--------------------:|-------------|
| **全般的なセキュリティ設定** |   | | |
| インターネット アクセス (受信および送信) と特定の VNet を無効または制御する | PARTIAL| PARTIAL   |  | 
| 関連付けられているすべてのリソース/サービスの配置  | YES | YES |  |
| 保存時および転送中の暗号化。                                                 | YES | YES |  |
| コンピューティング リソースへのルート アクセスと SSH アクセス。                                          | YES | YES |  |
| エンドポイントの保護、修正プログラムの適用、ログ記録など、デプロイされたシステム (インスタンス、エンドポイントなど) のセキュリティを維持する |  PARTIAL|  PARTIAL |VNet の内側にある ACI は現在使用できません |                                  
| ACI/AKS 統合の制御 (無効化/制限/制限)                    | PARTIAL| PARTIAL |VNet の内側にある ACI は現在使用できません|
| Azure ロールベースのアクセス制御 (Azure RBAC) - カスタム ロールの作成                           | YES | YES |  |
| ML サービスによって使用される ACR イメージへのアクセスの制御 (Azure によって提供または維持される、またはカスタム)  |PARTIAL|  PARTIAL |  |
| **一般的な Machine Learning サービスの使用** |  | | |
| モデルを構築し、そのモデルをトレーニングし、エンドポイントとしてホストし、webapp を介して使用するための開発環境を用意する機能     | YES | YES |  |
| ADLS (Data Lake Storage) からデータをプルする機能                                 |YES | YES |  |
| Azure Blob Storage からデータをプルする機能                                       |YES | YES |  |



### <a name="other-azure-government-limitations"></a>Azure Government のその他の制約

* Azure Machine Learning のコンピューティング インスタンスでは、24 時間以上続くトークンを更新する機能は Azure Government では使用できません。
* 米国アリゾナ リージョンでは、モデル プロファイルは 4 CPU をサポートしません。   
* サンプル ノートブックは、パブリック データへのアクセスが必要な場合、Azure Government で機能しないことがあります。
* IP アドレス: [必要なパブリック インターネット アクセス](how-to-secure-training-vnet.md#required-public-internet-access)命令で使用される CLI コマンドからは、IP 範囲は返されません。 代わりに [Azure Government の Azure IP 範囲とサービス タグ](https://www.microsoft.com/download/details.aspx?id=57063)を使用してください。
* スケジュールされたパイプラインの場合は、BLOB ベースのトリガー メカニズムも用意されています。 このメカニズムは CMK ワークスペースではサポートされていません。 CMK ワークスペースで BLOB を利用したトリガーを有効にするには、追加の設定が必要です。 詳細については、「[ロジック アプリから Machine Learning パイプラインの実行をトリガーする](how-to-trigger-published-pipeline.md)」を参照してください。
* ファイアウォール: Azure Government リージョンを使用するときは、次のホストをファイアウォールの設定に追加します。

    * アリゾナの場合: `usgovarizona.api.ml.azure.us`
    * バージニアの場合: `usgovvirginia.api.ml.azure.us`
    * 両方の場合: `graph.windows.net` 


## <a name="azure-china-21vianet"></a>Azure China 21Vianet 

| 特徴量                                       | パブリック クラウドの状態 | CH-East-2 | CH-North-3 |
|----------------------------------------------------------------------------|:------------------:|:--------------------:|:-------------:|
| **自動機械学習** |    | | |
| ノートブックで実験を作成して実行する                                    | GA               | YES       | 該当なし        |
| Studio Web エクスペリエンスで実験を作成および実行する                        | パブリック プレビュー   | YES       | 該当なし        |
| 業界トップ レベルの予測機能                                  | GA               | YES       | 該当なし        |
| ディープ ラーニングとその他の高度な学習機能のサポート                      | GA               | YES       | 該当なし        |
| 大規模なデータのサポート (最大 100 GB)                                          | パブリック プレビュー   | YES       | 該当なし        |
| Azure Databricks の統合                                              | GA               | NO        | 該当なし        |
| SQL、CosmosDB、および HDInsight の統合                                   | GA               | YES       | 該当なし        |
| **Machine Learning パイプライン** |    | | |
| Azure ML SDK を使用してパイプラインを作成、実行、発行する                   | GA               | YES       | 該当なし        |
| Azure ML SDK を使用してパイプライン エンドポイントを作成する                           | GA               | YES       | 該当なし        |
| Azure ML SDK を使用したパイプラインのスケジュールされた実行の作成、編集、削除 | GA               | YES       | 該当なし        |
| Studio でパイプラインの実行の詳細を表示する                                        | GA               | YES       | 該当なし        |
| Azure ML デザイナーでパイプラインを作成、実行、視覚化、および発行する          | GA  | YES       | 該当なし        |
| ML パイプラインとの Azure Databricks 統合                             | GA               | NO        | 該当なし        |
| Azure ML デザイナーでのパイプライン エンドポイントの作成                             | GA   | YES       | 該当なし        |
| **統合されたノートブック** |   | | |
| ワークスペース ノートブックとファイル共有                                        | GA               | YES       | 該当なし        |
| R および Python のサポート                                                       | GA               | YES       | 該当なし        |
| 仮想ネットワークのサポート                                                    | パブリック プレビュー   | NO        | 該当なし        |
| **コンピューティング インスタンス** |    | | |
| 統合されたノートブックのマネージド コンピューティング インスタンス                         | GA               | YES        | 該当なし        |
| Jupyter、JupyterLab の統合                                            | GA               | YES       | 該当なし        |
| Virtual Network (VNet) のサポート                                             | パブリック プレビュー   | YES       | 該当なし        |
| **SDK のサポート** |    | | |
| Python SDK サポート                                                         | GA               | YES       | 該当なし        |
| **Security** |   | | |
| トレーニングのための Virtual Network (VNet) サポート                                | GA               | YES       | 該当なし        |
| 推論のための Virtual Network (VNet) のサポート                               | GA               | YES       | 該当なし        |
| エンドポイント認証のスコア付け                                            | パブリック プレビュー   | YES       | 該当なし        |
| ワークプレース プライベート エンドポイント                                                 | GA               | NO        | 該当なし        |
| VNet の背後にある ACI                                                            | パブリック プレビュー   | NO        | 該当なし        |
| VNet の背後にある ACR                                                            | GA   | YES       | 該当なし        |
| AKS クラスターのプライベート IP                                                  | パブリック プレビュー   | NO        | 該当なし        |
| **Compute** |   | | |
| ワークスペース間のクォータ管理                                         | GA               | YES       | 該当なし        |
| **機械学習用のデータ** | | | |
| SDK からのデータセットとデータストアの作成、表示、または編集                  | GA               | YES       | 該当なし        |
| UI からのデータセットとデータストアの作成、表示、または編集                   | GA               | YES       | 該当なし        |
| SDK からのデータセット ドリフト モニターの表示、編集、または削除                   | パブリック プレビュー   | YES       | 該当なし        |
| UI からのデータセット ドリフト モニターの表示、編集、または削除                    | パブリック プレビュー   | YES       | 該当なし        |
| **Machine Learning のライフサイクル** |    | | |
| データ プロファイル                                                            | GA               | PARTIAL   | 該当なし        |
| Machine Learning と Azure ML CLI 用の Azure DevOps 拡張機能         | GA               | YES       | 該当なし        |
| FPGA ベースの Hardware Accelerated Models                                     | GA               | NO        | 該当なし        |
| Visual Studio Code の統合                                             | パブリック プレビュー   | NO        | 該当なし        |
| Event Grid の統合                                                     | パブリック プレビュー   | YES       | 該当なし        |
| Azure Stream Analytics と Azure Machine Learning の統合               | パブリック プレビュー   | NO        | 該当なし        |
| **ラベル** |    | | |
| プロジェクト管理ポータルのラベル付け                                        | GA               | YES       | 該当なし        |
| ラベラー ポータル                                                            | GA               | YES       | 該当なし        |
| プライベート ワークフォースを使用したラベル付け                                          | GA               | YES       | 該当なし        |
| ML によるラベル付け (イメージ分類とオブジェクトの検出)           | パブリック プレビュー   | YES       | 該当なし        |
| **信頼できる ML** |    | | |
| UI での説明可能性                                                       | パブリック プレビュー   | NO        | 該当なし        |
| 差分プライバシー SmartNoise ツールキット                                    | OSS              | NO        | 該当なし        |
| データシートを実装するための Azure Machine Learning のカスタム タグ              | GA               | NO        | 該当なし        |
| 公平性 AzureML 統合                                               | パブリック プレビュー   | NO        | 該当なし        |
| SDK の解釈可能性                                                      | GA               | YES       | 該当なし        |
| **トレーニング** |    | | |
| 実験ログ ストリーミング                                              | GA               | YES       | 該当なし        |
| 強化学習                                                     | パブリック プレビュー   | NO        | 該当なし        |
| 実験 UI                                                         | GA               | YES       | 該当なし        |
| .NET 統合 ML.NET 1.0                                                | GA               | YES       | 該当なし        |
| **推論** |   | | |
| バッチ推論                                                          | GA               | YES       | 該当なし        |
| FPGA での Data Box Edge                                                    | パブリック プレビュー   | NO        | 該当なし        |
| **その他** |    | | |
| Open Datasets                                                              | パブリック プレビュー   | YES       | 該当なし        |
| カスタム Cognitive Search                                                    | パブリック プレビュー   | YES       | 該当なし        |



### <a name="other-azure-china-limitations"></a>Azure China のその他の制約

* Azure China では、特に GPU SKU の VM SKU が制限されています。 NCv3 ファミリ (V100) のみが含まれています。
* REST API エンドポイントがグローバル Azure とは異なります。 次の表を使用して、Azure China リージョンの REST API エンドポイントを見つけてください。

    | REST エンドポイント                 | グローバル Azure                                 | China-Government                           |
    |------------------|--------------------------------------------|--------------------------------------------|
    | 管理プレーン | `https://management.azure.com/`              | `https://management.chinacloudapi.cn/`       |
    | データ プレーン       | `https://{location}.experiments.azureml.net` | `https://{location}.experiments.ml.azure.cn` |
    | Azure Active Directory              | `https://login.microsoftonline.com`          | `https://login.chinacloudapi.cn`             |

* サンプル ノートブックは、パブリック データへのアクセスが必要な場合に機能しないことがあります。
* IP アドレスの範囲: [必要なパブリック インターネット アクセス](how-to-secure-training-vnet.md#required-public-internet-access)命令で使用される CLI コマンドからは、IP 範囲は返されません。 代わりに [Azure China の Azure IP 範囲とサービス タグ](https://www.microsoft.com//download/details.aspx?id=57062)を使用してください。
* Azure Machine Learning コンピューティング インスタンスのプレビューは、プライベート エンドポイントが有効になっているワークスペースでは現在サポートされていませんが、すべての AML リージョンにサービス展開する次回のデプロイでは CI がサポートされるようになります。

## <a name="next-steps"></a>次のステップ

Azure Machine Learning を使用できるリージョンの詳細については、[リージョン別の製品](https://azure.microsoft.com/global-infrastructure/services/)に関するページを参照してください。