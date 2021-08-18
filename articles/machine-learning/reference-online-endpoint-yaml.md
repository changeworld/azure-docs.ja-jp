---
title: マネージド オンライン エンドポイント (プレビュー) YAML リファレンス
titleSuffix: Azure Machine Learning
description: マネージド オンライン エンドポイントとしてモデルをデプロイするために使用される YAML ファイルについての説明
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: rsethur
ms.author: seramasu
ms.date: 05/25/2021
ms.reviewer: laobri
ms.openlocfilehash: 445fc785fd9c7badf1805f5a8bf3dffa9f7bc2e6
ms.sourcegitcommit: 6a3096e92c5ae2540f2b3fe040bd18b70aa257ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/17/2021
ms.locfileid: "112321938"
---
# <a name="managed-online-endpoints-preview-yaml-reference"></a>マネージド オンライン エンドポイント (プレビュー) YAML リファレンス 

Azure Machine Learning 用の Azure CLI 拡張機能 (CLI 2.0) では、YAML ドキュメントを使用して、多くのコマンドを構成することができます。 この記事では、マネージド オンライン エンドポイントを操作するときに使用される YAML ドキュメントについて説明します。

モデルのデプロイの詳細については、「[マネージド オンライン エンドポイントをデプロイする方法](how-to-deploy-managed-online-endpoints.md)」にかんするページを参照してください。

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

> [!NOTE]
> マネージド オンライン エンドポイント用に詳細に指定されたサンプル YAML があるので、[ご参考](https://azuremlschemas.azureedge.net/latest/managedOnlineEndpoint.template.yaml)ください

## <a name="schema"></a>スキーマ

| Key | 説明 |
| --- | --- |
| $schema    | \[__省略可能__\] YAML スキーマ。 上の例のスキーマをブラウザーで表示すると、YAML ファイルで使用可能なすべてのオプションを確認できます。|
| name       | エンドポイントの名前。 Azure リージョン レベルで一意である必要があります。|
| traffic | エンドポイントから各デプロイに転送するトラフィックの割合 (%)。 トラフィックの値は合計して 100 になる必要があります。 |
| auth_mode | キー ベース認証には `key` を、Azure Machine Learning トークンベース認証には `aml_token` を使用します。 `key` には有効期限がありませんが、`aml_token` には有効期限があります。 `az ml endpoint list-keys` コマンドを使用して最新のトークンを取得してください。 |
| ID | システム割り当てマネージド ID とユーザー割り当てマネージド ID を構成するために使用されます。 |
| app_insights_enabled | `True` によって、Azure Machine Learning ワークスペースに関連付けられている Azure AppInsights との統合を有効にします。 既定では `False` です。
| tags | エンドポイントに関連付けられる Azure タグのディクショナリ。 |
| description | エンドポイントの説明。 |
| ターゲット (target) | このキーが定義されていない場合、エンドポイントはマネージド オンライン エンドポイントとしてデプロイされます。 AKS を使用するには、このキーの値を、登録されているコンピューティング先の名前 (例: `target:azureml:my-aks`) に設定します。 
| deployments | エンドポイントに作成されるデプロイの一覧を含みます。 このケースでは、`blue` という名前のデプロイが 1 つあるだけです。 |

### <a name="attributes-of-the-deployments-key"></a>`deployments` キーの属性
 
| Key | 説明 |
| --- | --- |
| name  | デプロイの名前。 |
| model | `model: azureml:my-model:1` 形式の登録されているモデル バージョンの名前。 モデルのプロパティには、インラインで `name`、`version`、`local_path` を指定できます。 モデル ファイルは自動的にアップロードされて登録されます。 インラインで指定することの欠点は、モデル ファイルを更新する場合に、バージョンを手動でインクリメントする必要がある点です。|
| code_configuration.code.local_path | モデルのスコアリングに使用されるすべての Python ソース コードを格納するディレクトリ。 入れ子になったディレクトリまたはパッケージがサポートされます。 |
| code_configuration.scoring_script | 上記のスコアリング ディレクトリ内の Python ファイル。 この Python コードには、`init()` 関数と `run()` 関数が含まれている必要があります。 `init()` 関数は、モデルの作成後または更新後に呼び出されます (この関数を使用して、モデルをメモリにキャッシュするなどの操作を実行できます)。 `run()` 関数は、実際のスコアリングまたは予測を実行するためにエンドポイントが呼び出されるたびに呼び出されます。 |
| 環境 | モデルとコードをホスティングする Azure Machine Learning 環境の詳細を含みます。 運用環境ではベスト プラクティスとして、モデルと環境を別々に登録し、登録済みの名前とバージョンを YAML で指定することをお勧めします。 たとえば、「 `environment: azureml:my-env:1` 」のように入力します。 |
| instance_type | デプロイ インスタンスのホストとなる VM の SKU。 詳細については、[マネージド オンライン エンドポイントでサポートされる VM SKU](reference-managed-online-endpoints-vm-sku-list.md) に関するページを参照してください。|
| scale_settings.scale_type | 現在、この値は `manual` である必要があります。 エンドポイントとデプロイの作成後にスケールアップまたはスケールダウンするには、YAML で `instance_count` を更新し、`az ml endpoint update -n $ENDPOINT_NAME --file <yaml filepath>` コマンドを実行します。 |
| scale_settings.instance_count | デプロイ内のインスタンスの数。 想定されるワークロードに基づく値を指定します。 高可用性を確保するために、Microsoft では `3` 以上に設定することを推奨しています。 |
| scale_settings.min_instances | 常に存在するインスタンスの最小数。 |
| scale_settings.max_instances | デプロイがスケールアウトできる最大のインスタンス数です。 クォータは、予約済みの max_instance になります。 |
| request_settings.request_timeout_ms | スコアリングのタイムアウト (ミリ秒)。 マネージド オンライン エンドポイントの既定値は 5000 です。 |
| request_settings.max_concurrent_requests_per_instance | デプロイごとに許可されるノードあたりの最大同時要求数。 既定値は 1 です。 __この設定は、マイクロソフト テクニカル サポートまたはマイクロソフトの Azure Machine Learning チームのメンバーの指示がない限り、既定値の 1 してください。__ |
| request_settings.max_queue_wait_ms | 要求がキューに残る最大時間 (ミリ秒単位)。 既定値は 500 です。 |
| liveness_probe | liveness probe は、コンテナーの正常性を定期的に監視します。 |
| liveness_probe.period | liveness probe を実行する頻度 (秒単位)。 既定値は 10 秒です。 最大値は 1 です。 |
| liveness_probe.initial_delay | コンテナーの起動後、liveness probe が開始するまでの秒数。 既定値は 10 です。 |
| liveness_probe.timeout | liveness probe がタイムアウトするまでの秒数。既定値は 2 秒です。 最大値は 1 です。 |
| liveness_probe.failure_threshold | システムは、failure_threshold 回数だけ試し、この後、中止されます。 既定値は 30 です。 最大値は 1 です。 |
| liveness_probe.success_threshold | 失敗した後、liveness probe が成功と見なされるための最小連続成功数。 既定値は 1 です。 最大値は 1 です。 |
| readiness_probe | readiness probe は、コンテナーがトラフィックを処理する準備ができている場合に検証します。 プロパティと既定値は、liveness probe と同じです。 |
| tags | デプロイに関連付ける Azure タグのディクショナリ。 |
| description | デプロイの説明。 |

## <a name="next-steps"></a>次の手順

- [マネージド オンライン エンドポイントを使用してモデルをデプロイする](how-to-deploy-managed-online-endpoints.md)方法について学習する
- [マネージド オンライン エンドポイントのデプロイとスコアリングのトラブルシューティング (プレビュー)](how-to-troubleshoot-managed-online-endpoints.md)