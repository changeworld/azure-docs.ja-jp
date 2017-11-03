---
title: "Azure Machine Learning デプロイのトラブルシューティング ガイド | Microsoft Docs"
description: "デプロイとサービス作成のトラブルシューティング ガイド"
services: machine-learning
author: raymondl
ms.author: raymondl
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 10/09/2017
ms.openlocfilehash: b9287c7151c96aaccbcda81c111cfe36ead5ab38
ms.sourcegitcommit: 1131386137462a8a959abb0f8822d1b329a4e474
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/13/2017
---
# <a name="troubleshooting-service-deployment-and-environment-setup"></a>サービスのデプロイと環境のセットアップのトラブルシューティング
次の情報は、モデル管理環境のセットアップ中に発生したエラーの原因の特定に役立ちます。

## <a name="model-management-environment"></a>モデル管理環境
### <a name="owner-permission-required"></a>所有者アクセス許可が必要
Machine Learning のコンピューティングを登録するには、Compute に登録するには、Azure サブスクリプションに対する所有者アクセス許可を持っている必要があります。

Web サービスをデプロイするためのクラスターをセットアップする場合も、所有者アクセス許可が必要です。

### <a name="resource-availability"></a>リソースの可用性
サブスクリプションで、環境リソースをプロビジョニングできるだけのリソースを使用できる必要があります。

### <a name="subscription-caps"></a>サブスクリプションの上限
サブスクリプションに課金の上限があるために、環境リソースをプロビジョニングできない場合があります。 プロビジョニングを有効にするには、その上限を解除します。

### <a name="enable-debug-and-verbose-options"></a>デバッグと詳細オプションを有効にする
setup コマンドで `--debug` フラグと `--verbose` フラグを使用して、環境のプロビジョニング中にデバッグとトレース情報を表示します。

```
az ml env setup -l <loation> -n <name> -c --debug --verbose 
```

## <a name="service-deployment"></a>サービスのデプロイ
次の情報は、デプロイ中または Web サービスの呼び出し時に発生するエラーの原因を特定するときに役立ちます。

### <a name="service-logs"></a>サービス ログ
サービス CLI の `logs` オプションは、Docker と Kubernetes のログ データを提供します。

```
az ml service logs realtime -i <web service id>
```

その他のログ設定には、`--help` (または `-h`) オプションを使用します。

```
az ml service logs realtime -h
```

### <a name="debug-and-verbose-options"></a>デバッグおよび詳細オプション
サービスをデプロイするときにデバッグ ログを表示するには、`--debug` フラグを使用します。

```
az ml service create realtime -m <modelfile>.pkl -f score.py -n <service name> -r python --debug
```

サービスをデプロイするときにさらに詳細な情報を表示するには、`--verbose` フラグを使用します。

```
az ml service create realtime -m <modelfile>.pkl -f score.py -n <service name> -r python --verbose
```

### <a name="enable-request-logging-in-app-insights"></a>App Insights でログ要求を有効にする
要求レベルのログ記録を有効にするには、Web サービスを作成するときに `-l` フラグを true に設定します。 要求のログは、Azure の環境内の App Insights インスタンスに書き込まれます。 このインスタンスを検索するには、`az ml env setup` コマンドを使用するときに指定した環境名を使用します。

- サービスを作成するときは、`-l` を true に設定します。
- Azure Portal で Application Insights を開きます。 実際の環境名を使用して Application Insights インスタンスを見つけます。
- Application Insights を開いたら、上部メニューの [検索] をクリックして結果を表示します。
- または `Analytics` > `Exceptions` > `exceptions take | 10` にアクセスします。


### <a name="add-error-handling-in-scoring-script"></a>スコア付けスクリプトにエラー処理を追加する
`scoring.py` スクリプトの **run** 関数で Web サービス出力の一部としてエラー メッセージを返すために、例外処理を使用します。

Python の例:
```
    try:
        <code to load model and score>
   except Exception as e:
        return(str(e))
```

## <a name="other-common-problems"></a>その他の一般的な問題
- `env setup` コマンドが失敗する場合、サブスクリプションで使用できる十分なコア数があることを確認します。
- Web サービス名にアンダースコア ( _ ) を使用しないでください (*my_webservice* など)。
- Web サービスの呼び出し時に **502 無効なゲートウェイ** エラーを受け取る場合は再試行します。 これは、通常、コンテナーがクラスターにまだデプロイされていないことを意味します。
- サービスを作成するときに **CrashLoopBackOff** エラーを受け取る場合は、ログを確認します。 通常、これは **init** 関数に依存関係がない結果です。
