---
title: CLI (v2) マネージド オンライン デプロイ YAML スキーマ
titleSuffix: Azure Machine Learning
description: CLI (v2) マネージド オンライン デプロイ YAML スキーマのリファレンス ドキュメント。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: lostmygithubaccount
ms.author: copeters
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: bb04bfb69052fab8224b70206b3c4cf680f20769
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131555895"
---
# <a name="cli-v2-managed-online-deployment-yaml-schema"></a>CLI (v2) マネージド オンライン デプロイ YAML スキーマ

ソース JSON スキーマは https://azuremlschemas.azureedge.net/latest/managedOnlineDeployment.schema.json にあります。

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>YAML 構文

| キー | 種類 | 説明 | 使用できる値 | 既定値 |
| --- | ---- | ----------- | -------------- | ------------- |
| `$schema` | string | YAML スキーマ。 Azure Machine Learning 用 VS Code 拡張機能を使用して YAML ファイルを作成する場合は、ファイルの先頭に `$schema` を含めることで、スキーマとリソースの入力候補を呼び出すことができます。 | | |
| `name` | string | **必須。** デプロイの名前。 <br><br> 名前付け規則は[ここ](how-to-manage-quotas.md#azure-machine-learning-managed-online-endpoints-preview)で定義されています。| | |
| `description` | string | デプロイの説明。 | | |
| `tags` | object | デプロイのタグの辞書。 | | |
| `endpoint_name` | string | **必須。** デプロイの作成先となるエンドポイントの名前。 | | |
| `model` | 文字列またはオブジェクト | デプロイに使用するモデル。 この値は、ワークスペース内の既存のバージョン管理されたモデルへの参照またはインライン モデルの仕様のいずれかです。 <br><br> 既存のモデルを参照するには、`azureml:<model-name>:<model-version>` 構文を使用します。 <br><br> モデルをインラインで定義するには、[モデル スキーマ](reference-yaml-model.md#yaml-syntax)に従います。 <br><br> 運用シナリオのベスト プラクティスとして、モデルを個別に作成し、ここで参照します。 <br><br> [カスタム コンテナーのデプロイ](how-to-deploy-custom-container.md) シナリオでは、このフィールドは省略可能です。| | |
| `model_mount_path` | string | カスタム コンテナーにモデルをマウントするためのパス。 [カスタム コンテナーのデプロイ](how-to-deploy-custom-container.md) シナリオにのみ適用されます。 `model` フィールドが指定されている場合は、コンテナー内のこのパスにマウントされます。 | | |
| `code_configuration` | object | スコアリング コード ロジックの構成。 <br><br> [カスタム コンテナーのデプロイ](how-to-deploy-custom-container.md) シナリオでは、このフィールドは省略可能です。 | | |
| `code_configuration.code.local_path` | string | モデルのスコアリングに使用するソース コード ディレクトリへのローカル パス。 | | |
| `code_configuration.scoring_script` | string | ソース コード ディレクトリ内のスコアリング ファイルへの相対パス。 | | |
| `environment` | 文字列またはオブジェクト | **必須。** デプロイに使用する環境。 この値は、ワークスペース内の既存のバージョン管理された環境への参照、またはインライン環境仕様のいずれかになります。 <br><br> 既存の環境を参照するには、`azureml:<environment-name>:<environment-version>` 構文を使用します。 <br><br> 環境をインラインで定義するには、[環境スキーマ](reference-yaml-environment.md#yaml-syntax)に従います。 <br><br> 運用シナリオのベスト プラクティスとして、環境を別途作成し、ここで参照することをお勧めします。 | | |
| `instance_type` | string | **必須。** デプロイに使用する VM サイズ。 サポートされているサイズの一覧については、[マネージド オンライン エンドポイント SKU の一覧](reference-managed-online-endpoints-vm-sku-list.md)に関するページを参照してください。 | | |
| `instance_count` | 整数 | **必須。** デプロイに使用するインスタンスの数。 予想されるワークロードに基づいて値を指定します。 高可用性を確保するために、Microsoft では `3` 以上に設定することを推奨しています。 <br><br> `instance_count` は、`az ml online-deployment update` コマンドを使用して、デプロイの作成後に更新できます。 | | |
| `app_insights_enabled` | boolean | ワークスペースに関連付けられた Azure Application Insights インスタンスとの統合を有効にするかどうかを指定します。 | | `false` |
| `scale_settings` | object | デプロイのスケール設定です。 現在サポートされているスケールの種類は `default` のみであるため、このプロパティを指定する必要はありません。 <br><br> この `default` のスケールの種類を使用すると、1) デプロイの作成後に `instance_count` プロパティを更新してインスタンス数を手動で増減させるか、2) [自動スケーリング ポリシー]()を作成することのいずれかを行うことができます。 | | |
| `scale_settings.type` | string | スケールの種類。 | `default` | `default` |
| `request_settings` | object | デプロイのスコアリング要求の設定。 構成可能なプロパティのセットについては、[RequestSettings](#requestsettings) を参照してください。 | | |
| `liveness_probe` | object | コンテナーの正常性を定期的に監視するための liveness probe の設定。 構成可能なプロパティのセットについては、[ProbeSettings](#probesettings) を参照してください。 | | |
| `readiness_probe` | object | コンテナーがトラフィックを処理する準備ができているかどうかを検証する readiness probe 設定。 構成可能なプロパティのセットについては、[ProbeSettings](#probesettings) を参照してください。 | | |

### <a name="requestsettings"></a>RequestSettings

| キー | 種類 | 説明 | 既定値 |
| --- | ---- | ----------- | ------------- |
| `request_timeout_ms` | 整数 | スコアリングのタイムアウト (ミリ秒)。 | `5000` |
| `max_concurrent_requests_per_instance` | 整数 | デプロイで許可されるインスタンスあたりの最大同時要求数。 <br><br> **この設定は、Microsoft テクニカル サポートまたは Azure ML チームのメンバーの指示がない限り、既定値から変更しないでください。** | `1` |
| `max_queue_wait_ms` | 整数 | 要求がキューに残るミリ秒単位の最大時間。 | `500` |

### <a name="probesettings"></a>ProbeSettings

| キー | 種類 | 説明 | 既定値 |
| --- | ---- | ----------- | ------------- |
| `period` | 整数 | プローブを実行する頻度 (秒単位)。 | `10` |
| `initial_delay` | 整数 | コンテナーの起動後、プローブが開始されるまでの秒数。 最小値は `1` です。 | `10` |
| `timeout` | 整数 | プローブがタイムアウトするまでの秒数。最小値は `1` です。 | `2` |
| `success_threshold` | 整数 | 失敗した後、プローブが成功と見なされるために必要な最小連続成功数。 最小値は `1` です。 | `1` |
| `failure_threshold` | 整数 | プローブが失敗した場合、システムは `failure_threshold` 回の試行を行った後、中断されます。 liveness probe の場合、中断はコンテナーの再起動を意味します。 readiness probe の場合、コンテナーには Unready (準備が未完了) とマークされます。 最小値は `1` です。 | `30` |

## <a name="remarks"></a>Remarks

`az ml online-deployment` コマンドは、Azure Machine Learning マネージド オンライン デプロイメントを管理するために使用できます。

## <a name="examples"></a>例

例は、[GitHub リポジトリの例](https://github.com/Azure/azureml-examples/tree/main/cli/endpoints/online)にあります。 以下にいくつか示します。

## <a name="yaml-basic"></a>YAML: basic

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/endpoints/online/managed/sample/blue-deployment.yml":::

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/endpoints/online/managed/sample/green-deployment.yml":::

## <a name="yaml-system-assigned-identity"></a>YAML: システム割り当て ID

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/endpoints/online/managed/managed-identities/2-sai-deployment.yml":::

## <a name="yaml-user-assigned-identity"></a>YAML: ユーザー割り当て ID

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/endpoints/online/managed/managed-identities/2-uai-deployment.yml":::

## <a name="next-steps"></a>次の手順

- [CLI (v2) をインストールして使用する](how-to-configure-cli.md)
