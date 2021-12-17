---
title: オンライン エンドポイントのデプロイのトラブルシューティング (プレビュー)
titleSuffix: Azure Machine Learning
description: オンライン エンドポイントのデプロイとスコアリングの一般的なエラーをトラブルシューティングする方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
author: petrodeg
ms.author: petrodeg
ms.reviewer: laobri
ms.date: 11/03/2021
ms.topic: troubleshooting
ms.custom: devplatv2
ms.openlocfilehash: f24e09dfbc2486b131efd754c742a984ab7c0dfc
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132719867"
---
# <a name="troubleshooting-online-endpoints-deployment-and-scoring-preview"></a>オンライン エンドポイントのデプロイとスコアリングのトラブルシューティング (プレビュー)

Azure Machine Learning のオンライン エンドポイントのデプロイとスコアリングの一般的な問題を解決する方法について説明します (プレビュー)。

このドキュメントは、推奨されるトラブルシューティング方法別に構成されています。

1. クラウドにデプロイする前に、[ローカル デプロイ](#deploy-locally)を使用してモデルをローカルでテストおよびデバッグする。
1. 問題のデバッグに[コンテナー ログ](#get-container-logs)を役立てる。
1. 発生する可能性がある[一般的なデプロイ エラー](#common-deployment-errors)とその修正方法について理解する。

「[HTTP 状態コード](#http-status-codes)」セクションでは、REST 要求でエンドポイントをスコアリングする際に呼び出しと予測のエラーを HTTP 状態コードにマップする方法について説明します。

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>前提条件

* **Azure サブスクリプション**。 [無料版または有料版の Azure Machine Learning](https://azure.microsoft.com/free/) をお試しください。
* [Azure CLI](/cli/azure/install-azure-cli)。
* [CLI (v2) (プレビュー) のインストール、設定、および使用](how-to-configure-cli.md)。

## <a name="deploy-locally"></a>ローカルでのデプロイ

ローカル デプロイとは、ローカルの Docker 環境にモデルをデプロイすることです。 ローカル デプロイは、クラウドにデプロイする前にテストやデバッグを行う場合に便利です。

> [!TIP]
> お使いのエンドポイントをローカルでテストおよびデバッグするには、Visual Studio Code を使用します。 詳細については、[Visual Studio Code でオンライン エンドポイントをローカルでデバッグする](how-to-debug-managed-online-endpoints-visual-studio-code.md)方法に関する記事を参照してください。

ローカル デプロイでは、ローカル エンドポイントの作成、更新、および削除がサポートされています。 また、エンドポイントを呼び出してログを取得することもできます。 ローカル デプロイを使用するには、適切な CLI コマンドに `--local` を追加します。

```azurecli
az ml online-deployment create --endpoint-name <endpoint-name> -n <deployment-name> -f <spec_file.yaml> --local
```

ローカル デプロイの一環として、次のステップが実行されます。

- Docker では、新しいコンテナー イメージをビルドするか、ローカルの Docker キャッシュから既存のイメージをプルします。 仕様ファイルの環境部分に一致する既存のイメージがある場合は、それが使用されます。
- Docker では、モデルやコード ファイルなど、マウントされているローカルの成果物を使用して、新しいコンテナーを起動します。

詳細については、「[マネージド オンライン エンドポイントを使用して機械学習モデルをローカルにデプロイおよびスコアリングする (プレビュー)](how-to-deploy-managed-online-endpoints.md#deploy-and-debug-locally-by-using-local-endpoints)」を参照してください。

## <a name="get-container-logs"></a>コンテナー ログを取得する

モデルがデプロイされている VM に直接アクセスすることはできません。 ただし、VM で実行されている一部のコンテナーからログを取得することはできます。 情報の量は、デプロイのプロビジョニング状態によって異なります。 指定したコンテナーが稼働している場合は、コンソール出力が表示されます。それ以外の場合は、後でやり直すよう求めるメッセージが表示されます。

コンテナーからのログ出力を表示するには、次の CLI コマンドを使用します。

```azurecli
az ml online-deployment get-logs -e <endpoint-name> -n <deployment-name> -l 100
```

または

```azurecli
    az ml online-deployment get-logs --endpoint-name <endpoint-name> --name <deployment-name> --lines 100
```

`az configure` を介して `--resource-group` と `--workspace-name` をまだ設定していない場合は、上記のコマンドにこれらのパラメーターを追加します。

これらのパラメーターを設定する方法について確認する場合、また現在の値が既に設定されている場合は、次を実行します。

```azurecli
az ml online-deployment get-logs -h
```

既定では、ログが推論サーバーからプルされます。 ログには推論サーバーのコンソール ログが含まれています。ここには、"score.py" コードの print/log ステートメントが含まれています。

> [!NOTE]
> Python ログを使用する場合は、メッセージがログに発行される、適切なログ レベルの順序を使用してください。 たとえば、INFO を使用します。


`–-container storage-initializer` を渡すことで、ストレージ初期化子コンテナーからログを取得することもできます。 これらのログには、コードとモデル データがコンテナーに正常にダウンロードされたかどうかに関する情報が含まれます。

コマンドに `--help` や `--debug` を追加して、詳細を表示します。 `x-ms-client-request-id` ヘッダーを含めると、トラブルシューティングに役立ちます。

## <a name="common-deployment-errors"></a>一般的なデプロイのエラー

デプロイ操作の状態の一部としてレポートされる一般的なデプロイ エラーの一覧を次に示します。

* [OutOfQuota](#error-outofquota)
* [OutOfCapacity](#error-outofcapacity)
* [BadArgument](#error-badargument)
* [ResourceNotReady](#error-resourcenotready)
* [ResourceNotFound](#error-resourcenotfound)
* [OperationCancelled](#error-operationcancelled)
* [InternalServerError](#error-internalservererror)

### <a name="error-outofquota"></a>ERROR: OutOfQuota

以下に示すのは、Azure サービスを使用するときにクォータが不足している可能性がある一般的なリソースの一覧です。

* [CPU](#cpu-quota)
* [ロールの割り当て](#role-assignment-quota)
* [エンドポイント](#endpoint-quota)
* [Kubernetes](#kubernetes-quota)
* [その他](#other-quota)

#### <a name="cpu-quota"></a>CPU クォータ

モデルをデプロイする前に、十分なコンピューティング クォータを用意する必要があります。 このクォータでは、サブスクリプションごと、ワークスペースごと、SKU ごと、リージョンごとに使用可能な仮想コアの量を定義します。 SKU の種類に基づいて、デプロイするたびに使用可能なクォータが減算され、削除するたびに加算されます。

考えられる軽減策は、使用されていない削除可能なデプロイが含まれていないかを確認することです。 または、[クォータの引き上げ要求](how-to-manage-quotas.md#request-quota-increases)を送信できます。

#### <a name="role-assignment-quota"></a>ロールの割り当てクォータ

このサブスクリプションで使用されていないロールの割り当てを削除してみてください。 Azure portal の [アクセス制御] メニューで、すべてのロールの割り当てを確認できます。

#### <a name="endpoint-quota"></a>エンドポイント クォータ

このサブスクリプションで使用されていないエンドポイントをいくつか削除してみてください。

#### <a name="kubernetes-quota"></a>Kubernetes クォータ

要求された CPU またはメモリを満たすことができませんでした。 要求またはクラスターを調整してください。

#### <a name="other-quota"></a>その他のクォータ

デプロイの一部として提供される `score.py` を実行するために、Azure では `score.py` に必要なすべてのリソースを含むコンテナーを作成し、そのコンテナーでスコアリング スクリプトを実行します。

コンテナーを起動できなかった場合、これはスコアリングを実行できなかったことを意味します。 コンテナーには、`instance_type` でサポートできるよりも多くのリソースが必要である可能性があります。 その場合は、オンライン デプロイの `instance_type` を更新することを検討してください。

エラーの正確な原因を取得するには、次を実行します。 

```azurecli
az ml online-deployment get-logs -e <endpoint-name> -n <deployment-name> -l 100
```

### <a name="error-outofcapacity"></a>ERROR: OutOfCapacity

Azure Machine Learning の容量不足のため、指定された VM サイズをプロビジョニングできませんでした。 後で再試行するか、別のリージョンにデプロイしてみてください。

### <a name="error-badargument"></a>ERROR: BadArgument

以下に示すのは、このエラーが発生する可能性がある理由の一覧です。

* [リソース要求が制限を超えている](#resource-requests-greater-than-limits)
* [リソースをダウンロードできない](#unable-to-download-resources)

#### <a name="resource-requests-greater-than-limits"></a>制限を超えているリソース要求

リソースに対する要求は、制限以下でなければなりません。 制限を設定しない場合は、Azure Machine Learning ワークスペースにコンピューティングをアタッチするときに既定値が設定されます。 制限は、Azure portal または `az ml compute show` コマンドを使用して確認できます。

#### <a name="unable-to-download-resources"></a>リソースをダウンロードできない

コンピューティング リソースをプロビジョニングした後、Azure では、デプロイの作成中に、ワークスペースのプライベート Azure Container Registry (ACR) からユーザー コンテナー イメージをプルし、ユーザー モデルとコード成果物をワークスペース ストレージ アカウントからユーザー コンテナーにマウントしようとします。

まず、ACR へのアクセスに関するアクセス許可の問題があるかどうかを確認してください。

BLOB をプルするために、Azure では[マネージド ID](../active-directory/managed-identities-azure-resources/overview.md) を使用してストレージ アカウントにアクセスします。

  - SystemAssigned を使用して関連付けられたエンドポイントを作成した場合は、Azure のロールベースのアクセス制御 (RBAC) のアクセス許可が自動的に付与され、それ以上のアクセス許可は必要ありません。

  - UserAssigned を使用して関連付けられているエンドポイントを作成した場合は、ユーザーのマネージド ID に、ワークスペース ストレージ アカウントに対するストレージ BLOB データ閲覧者のアクセス許可が必要です。

このプロセス中に、操作が失敗したステージに応じて、いくつかの異なる問題が発生する可能性があります。

* [ユーザー コンテナー イメージをダウンロードできません](#unable-to-download-user-container-image)
* [ユーザー モデルおよびコード成果物をダウンロードできません](#unable-to-download-user-model-or-code-artifacts)

これらのエラーの詳細を表示するには、次を実行します。

```azurecli
az ml online-deployment get-logs -n <endpoint-name> --deployment <deployment-name> --l 100
``` 

#### <a name="unable-to-download-user-container-image"></a>ユーザー コンテナー イメージをダウンロードできません

ユーザー コンテナーが見つからなかった可能性があります。

ワークスペース ACR でコンテナー イメージを使用できることを確認します。

たとえば、イメージが `testacr.azurecr.io/azureml/azureml_92a029f831ce58d2ed011c3c42d35acb:latest` の場合は、`az acr repository show-tags -n testacr --repository azureml/azureml_92a029f831ce58d2ed011c3c42d35acb --orderby time_desc --output table` を使用してリポジトリを確認します。

#### <a name="unable-to-download-user-model-or-code-artifacts"></a>ユーザー モデルおよびコード成果物をダウンロードできません

ユーザー モデルまたはコード成果物が見つからない可能性があります。

モデルとコード成果物が、デプロイと同じワークスペースに登録されていることを確認します。 `show` コマンドを使用して、ワークスペース内のモデルまたはコード成果物の詳細を表示します。 

- 次に例を示します。 
  
  ```azurecli
  az ml model show --name <model-name>
  az ml code show --name <code-name> --version <version>
  ```
 
  また、BLOB がワークスペース ストレージ アカウント内に存在するかどうかを確認することもできます。

- たとえば、BLOB が `https://foobar.blob.core.windows.net/210212154504-1517266419/WebUpload/210212154504-1517266419/GaussianNB.pkl` の場合、次のコマンドを使用して、それが存在するかどうかを確認できます。

  `az storage blob exists --account-name foobar --container-name 210212154504-1517266419 --name WebUpload/210212154504-1517266419/GaussianNB.pkl --subscription <sub-name>`

### <a name="error-resourcenotready"></a>ERROR: ResourceNotReady

デプロイの一部として提供される `score.py` を実行するために、Azure では `score.py` に必要なすべてのリソースを含むコンテナーを作成し、そのコンテナーでスコアリング スクリプトを実行します。 このシナリオのエラーは、実行中にこのコンテナーがクラッシュしたため、スコアリングを実行できないことを意味します。 このエラーは、次の場合に発生します。

- `score.py` にエラーがある。 `get-logs` を使用すると、一般的な問題を診断できます。
    - インポートされたパッケージは、conda 環境には存在しません。
    - 構文エラーです。
    - `init()` メソッドのエラー。
- `get-logs` によってログが生成されていない場合は、通常、コンテナーの起動に失敗したということを意味します。 この問題をデバッグするには、代わりに[ローカルにデプロイ](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/machine-learning/how-to-troubleshoot-online-endpoints.md#deploy-locally)してみてください。
- readiness probe または liveness probe が正しく設定されていない。
- 依存関係が不足しているなど、コンテナーの環境設定にエラーがある。

### <a name="error-resourcenotfound"></a>ERROR: ResourceNotFound

このエラーは Azure Resource Manager が必要なリソースを見つけられない場合に発生します。 たとえば、ストレージ アカウントが参照されているが、指定されたパスで見つからない場合、このエラーが発生します。 正確なパスまたは名前のスペルによって提供された可能性のあるリソースを再確認してください。

詳細については、「[リソースが見つからないエラーを解決する](../azure-resource-manager/troubleshooting/error-not-found.md)」を参照してください。 

### <a name="error-operationcancelled"></a>ERROR: OperationCancelled

Azure の操作には、優先度レベルが割り当てられており、高いものから順に実行されます。 このエラーは、操作が優先度の高い別の操作によって上書きされた場合に発生します。 操作を再試行すると、キャンセルせずに操作を実行することが許可される場合があります。

### <a name="error-internalservererror"></a>ERROR: InternalServerError

Microsoft では信頼性の高い、安定したサービスを提供するために最善を尽くしていますが、計画どおりに進まない場合があります。 このエラーが発生した場合、Microsoft 側で問題が発生しているため、当社で修正する必要があります。 すべての関連情報を記載のうえ、[カスタマー サポート チケット](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)を送信していただけましたら、こちらで問題に対処します。 

## <a name="autoscaling-issues"></a>自動スケールの問題

自動スケールで問題が発生した場合は、「[Azure 自動スケーリングのトラブルシューティング](../azure-monitor/autoscale/autoscale-troubleshoot.md)」を参照してください。

## <a name="http-status-codes"></a>HTTP 状態コード

REST 要求でオンライン エンドポイントにアクセスしたときに返される状態コードは、[HTTP の状態コード](https://aka.ms/http-status-codes)の標準に準拠しています。 エンドポイントの呼び出しと予測エラーが HTTP 状態コードにマップされる方法の詳細を以下に示します。

| status code| 理由の文字列 |  このコードが返される理由 |
| --- | --- | --- |
| 200 | OK | 待ち時間の範囲内で、モデルが正常に実行されました。 |
| 401 | 権限がありません | スコアリングなどの要求されたアクションを実行するためのアクセス許可がないか、トークンの有効期限が切れています。 |
| 404 | 見つかりません | URL が正しくありません。 |
| 408 | 要求タイムアウト | モデルの実行にかかる時間が、モデル デプロイ構成の `request_settings` の `request_timeout_ms` で指定したタイムアウトよりも長くなっています。|
| 424 | モデル エラー | モデル コンテナーから 200 以外の応答が返された場合、Azure は 424 を返します。 詳細については、`ms-azureml-model-error-statuscode` と `ms-azureml-model-error-reason` の応答ヘッダーをご確認ください。 |
| 429 | レート制限 | 1 秒あたり 100 を超える要求をエンドポイントに送信しようとしました。 |
| 429 | 保留中の要求が多すぎます | モデルで処理できる数よりも多くの要求が送られてきています。 常に 2 つの * `max_concurrent_requests_per_instance` * `instance_count` 要求が許可されます。 追加の要求は拒否されます。 これらの設定は、モデル デプロイ構成の `request_settings` および `scale_settings` で確認できます。 自動スケーリングを使用している場合、モデルはシステムでスケールアップできるよりも高速に要求を取得します。 自動スケーリングでは、[エクスポネンシャル バックオフ](https://aka.ms/exponential-backoff)を使用して要求の再送信を試行できます。 これにより、システムに調整時間が与えられます。 |
| 500 | 内部サーバー エラー | Azure ML でプロビジョニングされたインフラストラクチャが失敗しています。 |

## <a name="next-steps"></a>次のステップ

- [マネージド オンライン エンドポイントを使用して機械学習モデルをデプロイおよびスコアリングする (プレビュー)](how-to-deploy-managed-online-endpoints.md)
- [オンライン エンドポイントの安全なロールアウト (プレビュー)](how-to-safely-rollout-managed-endpoints.md)
- [マネージド オンライン エンドポイント (プレビュー) YAML リファレンス](reference-yaml-endpoint-managed-online.md)

