---
title: Azure Machine Learning デプロイのトラブルシューティング ガイド | Microsoft Docs
description: デプロイとサービス作成のトラブルシューティング ガイド
services: machine-learning
author: aashishb
ms.author: aashishb
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 11/16/2017
ms.openlocfilehash: 7b8d2f4fb52c6d4faed8e813779e3b01d1817355
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/17/2018
---
# <a name="troubleshooting-service-deployment-and-environment-setup"></a>サービスのデプロイと環境のセットアップのトラブルシューティング
次の情報は、モデル管理環境のセットアップ中に発生したエラーの原因の特定に役立ちます。

## <a name="model-management-environment"></a>モデル管理環境
### <a name="contributor-permission-required"></a>共同作成者のアクセス許可が必要
Web サービスのデプロイ用クラスターをセットアップするには、サブスクリプションまたはリソース グループに対する共同作成者のアクセス権が必要です。

### <a name="resource-availability"></a>リソースの可用性
サブスクリプションで、環境リソースをプロビジョニングできるだけのリソースを使用できる必要があります。

### <a name="subscription-caps"></a>サブスクリプションの上限
サブスクリプションに課金の上限があるために、環境リソースをプロビジョニングできない場合があります。 プロビジョニングを有効にするには、その上限を解除します。

### <a name="enable-debug-and-verbose-options"></a>デバッグと詳細オプションを有効にする
setup コマンドで `--debug` フラグと `--verbose` フラグを使用して、環境のプロビジョニング中にデバッグとトレース情報を表示します。

```
az ml env setup -l <location> -n <name> -c --debug --verbose 
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
- Windows マシン上で azure-cli-ml の pip インストールが "`cannot find the path specified`" というエラーで失敗した場合、長いパスのサポートを有効にする必要があります。 https://blogs.msdn.microsoft.com/jeremykuhne/2016/07/30/net-4-6-2-and-long-paths-on-windows-10/ をご覧ください。 
- `env setup` コマンドが `LocationNotAvailableForResourceType` で失敗する場合は、使用している機械学習リソースの場所 (リージョン) が間違っている可能性があります。 `-l` パラメーターに指定されている場所が `eastus2`、`westcentralus`、または `australiaeast` であることを確認してください。
- `env setup` コマンドが `Resource quota limit exceeded` で失敗する場合は、サブスクリプションで使用できるコアが十分にあること、およびリソースが他のプロセスによって使い尽くされていないことを確認してください。
- `env setup` コマンドが `Invalid environment name. Name must only contain lowercase alphanumeric characters` で失敗する場合は、サービス名に大文字、記号、または アンダースコア (_) (例: *my_environment*)が 含まれていないことを確認してください。
- `service create` コマンドが `Service Name: [service_name] is invalid. The name of a service must consist of lower case alphanumeric characters (etc.)` で失敗する場合は、サービス名の長さが 3 ～ 32 文字であること、先頭と末尾が小文字の英数字であること、および大文字、記号 (ハイフン (-) とピリオド (. ) 以外)、アンダースコア (_) (例: *my_webservice*) が含まれていないことを確認してください。
- Web サービスの呼び出し時に `502 Bad Gateway` エラーが発生する場合は再試行してください。 これは、通常、コンテナーがクラスターにまだデプロイされていないことを意味します。
- サービスの作成時に `CrashLoopBackOff` エラーが発生する場合は、ログを確認してください。 通常、これは **init** 関数に依存関係がない結果です。
