---
title: マネージド オンライン エンドポイントのデプロイのトラブルシューティング (プレビュー)
titleSuffix: Azure Machine Learning
description: マネージド オンライン エンドポイントのデプロイとスコアリングに関する一般的なエラーをトラブルシューティングする方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: petrodeg
ms.author: petrodeg
ms.reviewer: laobri
ms.date: 05/13/2021
ms.topic: troubleshooting
ms.custom: devplatv2
ms.openlocfilehash: 9f50fd0ff60f819717dbcb1e3878b4caf56c563b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121729786"
---
# <a name="troubleshooting-managed-online-endpoints-deployment-and-scoring-preview"></a>マネージド オンライン エンドポイントのデプロイとスコアリングのトラブルシューティング (プレビュー)

Azure Machine Learning のマネージド オンライン エンドポイントのデプロイとスコアリングに関する一般的な問題を解決する方法について説明します (プレビュー)。

このドキュメントは、推奨されるトラブルシューティング方法別に構成されています。

1. クラウドにデプロイする前に、[ローカル デプロイ](#deploy-locally)を使用してモデルをローカルでテストおよびデバッグする。
1. 問題のデバッグに[コンテナー ログ](#get-container-logs)を役立てる。
1. 発生する可能性がある[一般的なデプロイ エラー](#common-deployment-errors)とその修正方法について理解する。

「[HTTP 状態コード](#http-status-codes)」セクションでは、REST 要求でエンドポイントをスコアリングする際に呼び出しと予測のエラーを HTTP 状態コードにマップする方法について説明します。

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>前提条件

* **Azure サブスクリプション**。 [無料版または有料版の Azure Machine Learning](https://azure.microsoft.com/free/) をお試しください。
* [Azure CLI](/cli/azure/install-azure-cli)。
* [2.0 CLI (プレビュー) のインストール、設定、および使用](how-to-configure-cli.md)。

## <a name="deploy-locally"></a>ローカルでのデプロイ

ローカル デプロイとは、ローカルの Docker 環境にモデルをデプロイすることです。 ローカル デプロイは、クラウドにデプロイする前にテストやデバッグを行う場合に便利です。

ローカル デプロイでは、ローカル エンドポイントの作成、更新、および削除がサポートされています。 また、エンドポイントを呼び出してログを取得することもできます。 ローカル デプロイを使用するには、適切な CLI コマンドに `--local` を追加します。

```azurecli
az ml endpoint create -n <endpoint-name> -f <spec_file.yaml> --local
```
ローカル デプロイの一環として、次のステップが実行されます。

- Docker では、新しいコンテナー イメージをビルドするか、ローカルの Docker キャッシュから既存のイメージをプルします。 仕様ファイルの環境部分に一致する既存のイメージがある場合は、それが使用されます。
- Docker では、モデルやコード ファイルなど、マウントされているローカルの成果物を使用して、新しいコンテナーを起動します。

詳細については、「[マネージド オンライン エンドポイントを使用して機械学習モデルをローカルにデプロイおよびスコアリングする (プレビュー)](how-to-deploy-managed-online-endpoints.md#deploy-and-debug-locally-by-using-local-endpoints)」を参照してください。

## <a name="get-container-logs"></a>コンテナー ログを取得する

モデルがデプロイされている VM に直接アクセスすることはできません。 ただし、VM で実行されている一部のコンテナーからログを取得することはできます。 情報の量は、デプロイのプロビジョニング状態によって異なります。 指定したコンテナーが稼働している場合は、コンソール出力が表示されます。それ以外の場合は、後でやり直すよう求めるメッセージが表示されます。

コンテナーからのログ出力を表示するには、次の CLI コマンドを使用します。

```azurecli
az ml endpoint get-logs -n <endpoint-name> -d <deployment-name> -l 100
```

または

```azurecli
    az ml endpoint get-logs --name <endpoint-name> --deployment <deployment-name> --lines 100
```

`az configure` を介して `--resource-group` と `--workspace-name` をまだ設定していない場合は、上記のコマンドにこれらのパラメーターを追加します。

これらのパラメーターを設定する方法について確認する場合、また現在の値が既に設定されている場合は、次を実行します。

```azurecli
az ml endpoint get-logs -h
```

既定では、ログが推論サーバーからプルされます。 ログには推論サーバーのコンソール ログが含まれています。ここには、"score.py" コードの print/log ステートメントが含まれています。

> [!NOTE]
> Python ログを使用する場合は、メッセージがログに発行される、適切なログ レベルの順序を使用してください。 たとえば、INFO を使用します。


`–-container storage-initializer` を渡すことで、ストレージ初期化子コンテナーからログを取得することもできます。 これらのログには、コードとモデル データがコンテナーに正常にダウンロードされたかどうかに関する情報が含まれます。

コマンドに `--help` や `--debug` を追加して、詳細を表示します。 `x-ms-client-request-id` ヘッダーを含めると、トラブルシューティングに役立ちます。

## <a name="common-deployment-errors"></a>一般的なデプロイのエラー

デプロイ操作の状態の一部としてレポートされる一般的なデプロイ エラーの一覧を次に示します。

### <a name="err_1100-not-enough-quota"></a>ERR_1100: Not enough quota (ERR_1100: 十分なクォータがありません)

モデルをデプロイする前に、十分なコンピューティング クォータを用意する必要があります。 このクォータでは、サブスクリプションごと、ワークスペースごと、SKU ごと、リージョンごとに使用可能な仮想コアの量を定義します。 SKU の種類に基づいて、デプロイするたびに使用可能なクォータが減算され、削除するたびに加算されます。

考えられる軽減策は、使用されていない削除可能なデプロイが含まれていないかを確認することです。 または、[クォータの引き上げ要求](./how-to-manage-quotas.md)を送信できます。

### <a name="err_1101-out-of-capacity"></a>ERR_1101: Out of capacity (容量不足です)

Azure Machine Learning の容量不足のため、指定された VM サイズをプロビジョニングできませんでした。 後で再試行するか、別のリージョンにデプロイしてみてください。

### <a name="err_1200-unable-to-download-user-container-image"></a>ERR_1200: Unable to download user container image (ERR_1200: ユーザー コンテナー イメージをダウンロードできません)

コンピューティングをプロビジョニングした後、デプロイの作成時に、Azure ではワークスペースのプライベート Azure コンテナー レジストリ (ACR) からユーザー コンテナー イメージをプルしようとします。 2 つの問題が発生する可能性があります。

- ユーザー コンテナー イメージが見つかりません。

  ワークスペース ACR でコンテナー イメージを使用できることを確認します。
たとえば、イメージが `testacr.azurecr.io/azureml/azureml_92a029f831ce58d2ed011c3c42d35acb:latest` の場合は、`az acr repository show-tags -n testacr --repository azureml/azureml_92a029f831ce58d2ed011c3c42d35acb --orderby time_desc --output table` を使用してリポジトリを確認します。

- ACR にアクセスする際にアクセス許可の問題が発生しています。

  イメージをプルする際、Azure では[マネージド ID](../active-directory/managed-identities-azure-resources/overview.md) を使用して ACR にアクセスします。 

  - SystemAssigned を使用して関連付けられたエンドポイントを作成した場合は、Azure のロールベースのアクセス制御 (RBAC) のアクセス許可が自動的に付与され、それ以上のアクセス許可は必要ありません。
  - UserAssigned を使用して関連付けられたエンドポイントを作成した場合は、ユーザーのマネージド ID に、ワークスペース ACR に対する AcrPull アクセス許可が含まれている必要があります。

このエラーに関する詳細を表示するには、次を実行します。

```azurecli
az ml endpoint get-logs -n <endpoint-name> --deployment <deployment-name> --tail 100
```

### <a name="err_1300-unable-to-download-user-modelcode-artifacts"></a>ERR_1300: Unable to download user model\code artifacts (ERR_1300: ユーザー モデルおよびコード成果物をダウンロードできません)

コンピューティング リソースをプロビジョニングした後、デプロイの作成時に、Azure ではユーザー モデルとコード成果物をワークスペース ストレージ アカウントからユーザー コンテナーにマウントしようとします。

- ユーザー モデルとコード成果物が見つかりません。

  - モデルとコード成果物が、デプロイと同じワークスペースに登録されていることを確認します。 `show` コマンドを使用して、ワークスペース内のモデルまたはコード成果物の詳細を表示します。 次に例を示します。 
  
    ```azurecli
    az ml model show --name <model-name>
    az ml code show --name <code-name> --version <version>
    ```

  - また、BLOB がワークスペース ストレージ アカウント内に存在するかどうかを確認することもできます。

    たとえば、BLOB が `https://foobar.blob.core.windows.net/210212154504-1517266419/WebUpload/210212154504-1517266419/GaussianNB.pkl` の場合、`az storage blob exists --account-name foobar --container-name 210212154504-1517266419 --name WebUpload/210212154504-1517266419/GaussianNB.pkl --subscription <sub-name>` コマンドを使用して、それが存在するかどうかを確認できます。

- ACR にアクセスする際にアクセス許可の問題が発生しています。

  BLOB をプルするために、Azure では[マネージド ID](../active-directory/managed-identities-azure-resources/overview.md) を使用してストレージ アカウントにアクセスします。

  - SystemAssigned を使用して関連付けられたエンドポイントを作成した場合は、Azure のロールベースのアクセス制御 (RBAC) のアクセス許可が自動的に付与され、それ以上のアクセス許可は必要ありません。

  - UserAssigned を使用して関連付けられているエンドポイントを作成した場合は、ユーザーのマネージド ID に、ワークスペース ストレージ アカウントに対するストレージ BLOB データ閲覧者のアクセス許可が必要です。

このエラーに関する詳細を表示するには、次を実行します。

```azurecli
az ml endpoint get-logs -n <endpoint-name> --deployment <deployment-name> --lines 100
```

### <a name="err_1350-unable-to-download-user-model-not-enough-space-on-the-disk"></a>ERR_1350: Unable to download user model, not enough space on the disk (ユーザー モデルをダウンロードできません。ディスクに十分な領域がありません)

この問題は、モデルのサイズが使用可能なディスク領域よりも大きい場合に発生します。 もっと大きいディスク領域がある SKU を試してください。

### <a name="err_2100-unable-to-start-user-container"></a>ERR_2100: Unable to start user container (ERR_2100: ユーザー コンテナーを起動できません)

デプロイの一部として提供される `score.py` を実行するために、Azure では `score.py` に必要なすべてのリソースを含むコンテナーを作成し、そのコンテナーでスコアリング スクリプトを実行します。

このエラーは、このコンテナーを起動できなかったため、スコアリングを実行できなかったことを意味します。 コンテナーには、`instance_type` でサポートできるよりも多くのリソースが必要である可能性があります。 その場合は、オンライン デプロイの `instance_type` を更新することを検討してください。

エラーの正確な原因を取得するには、次を実行します。 

```azurecli
az ml endpoint get-logs
```

### <a name="err_2200-user-container-has-crashedterminated"></a>ERR_2200: User container has crashed\terminated (ERR_2200: ユーザー コンテナーがクラッシュまたは終了しました)

デプロイの一部として提供される `score.py` を実行するために、Azure では `score.py` に必要なすべてのリソースを含むコンテナーを作成し、そのコンテナーでスコアリング スクリプトを実行します。  このシナリオのエラーは、実行中にこのコンテナーがクラッシュしたため、スコアリングを実行できなかったことを意味します。 このエラーは、次の場合に発生します。

- `score.py` にエラーがある。 `get--logs` を使用すると、一般的な問題を診断できます。
    - インポートされたパッケージは、conda 環境には存在しません
    - 構文エラー
    - `init()` メソッドのエラー
- readiness probe または liveness probe が正しく設定されていない。
- 依存関係が不足しているなど、コンテナーの環境設定にエラーがある。

### <a name="err_5000-internal-error"></a>ERR_5000: Internal error (ERR_5000: 内部エラー)

Microsoft では信頼性の高い、安定したサービスを提供するために最善を尽くしていますが、計画どおりに進まない場合があります。 このエラーが発生した場合、Microsoft 側で問題が発生しているため、当社で修正する必要があります。 すべての関連情報を記載のうえ、[カスタマー サポート チケット](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)を送信していただけましたら、こちらで問題に対処します。  

## <a name="http-status-codes"></a>HTTP 状態コード

REST 要求でマネージド オンライン エンドポイントにアクセスしたときに返される状態コードは、[HTTP 状態コード](https://aka.ms/http-status-codes)の標準に準拠しています。 マネージド エンドポイントの呼び出しと予測に関するエラーを、HTTP 状態コードにマップする方法の詳細を以下に示します。

| status code| 理由の文字列 |  このコードが返される理由 |
| --- | --- | --- |
| 200 | OK | 待ち時間の範囲内で、モデルが正常に実行されました。 |
| 401 | 権限がありません | スコアリングなどの要求されたアクションを実行するためのアクセス許可がないか、トークンの有効期限が切れています。 |
| 404 | 見つかりません | URL が正しくありません。 |
| 408 | 要求タイムアウト | モデルの実行にかかる時間が、モデル デプロイ構成の `request_settings` の `request_timeout_ms` で指定したタイムアウトよりも長くなっています。|
| 413 | ペイロードが大きすぎます | 要求ペイロードが 1.5 メガバイトを超えています。 |
| 424 | モデル エラー、元のコード =`<original code>` | モデル コンテナーから 200 以外の応答が返された場合、Azure は 424 を返します。 |
| 424 | 応答ペイロードが大きすぎます | コンテナーが 1.5 メガバイトを超えるペイロードを返した場合、Azure は 424 を返します。 |
| 429 | レート制限 | 1 秒あたり 100 を超える要求をエンドポイントに送信しようとしました。 |
| 429 | 保留中の要求が多すぎます | モデルで処理できる数よりも多くの要求が送られてきています。 いつでも 2 *`max_concurrent_requests_per_instance`* `instance_count` 個の要求が許可されます。 追加の要求は拒否されます。 これらの設定は、モデル デプロイ構成の `request_settings` および `scale_settings` で確認できます。 自動スケーリングを使用している場合、モデルはシステムでスケールアップできるよりも高速に要求を取得します。 自動スケーリングでは、[エクスポネンシャル バックオフ](https://aka.ms/exponential-backoff)を使用して要求の再送信を試行できます。 これにより、システムに調整時間が与えられます。 |
| 500 | 内部サーバー エラー | Azure ML でプロビジョニングされたインフラストラクチャが失敗しています。 |

## <a name="next-steps"></a>次のステップ

- [マネージド オンライン エンドポイントを使用して機械学習モデルをデプロイおよびスコアリングする (プレビュー)](how-to-deploy-managed-online-endpoints.md)
- [オンライン エンドポイントの安全なロールアウト (プレビュー)](how-to-safely-rollout-managed-endpoints.md)
- [マネージド オンライン エンドポイント (プレビュー) YAML リファレンス](reference-yaml-endpoint-managed-online.md)

