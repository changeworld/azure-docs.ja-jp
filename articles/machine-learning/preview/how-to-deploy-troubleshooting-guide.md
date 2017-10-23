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
ms.date: 10/05/2017
ms.openlocfilehash: 066a6a223692055c7855abc63667e345ee8dc2d4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="model-management-troubleshooting"></a>モデル管理のトラブルシューティング
次の情報は、デプロイ中または Web サービスの呼び出し時に発生するエラーの原因を特定するときに役立ちます。
 
## <a name="1-service-logs"></a>1.サービス ログ
サービス CLI の `logs` オプションは、Docker と Kubernetes のログ データを提供します。

```
az ml service logs realtime -i <web service id>
```

その他のログ設定には、`--help` (または `-h`) オプションを使用します。

```
az ml service logs realtime -h
```

## <a name="2-debug-and-verbose-options"></a>2.デバッグおよび詳細オプション
サービスをデプロイするときにデバッグ ログを表示するには、`--debug` フラグを使用します。

```
az ml service create realtime -m <modelfile>.pkl -f score.py -n <service name> -r python --debug
```

サービスをデプロイするときにさらに詳細な情報を表示するには、`--verbose` フラグを使用します。

```
az ml service create realtime -m <modelfile>.pkl -f score.py -n <service name> -r python --verbose
```

## <a name="3-app-insights"></a>3.App Insights
要求レベルのログ記録を有効にするには、Web サービスを作成するときに `-l` フラグを true に設定します。 要求のログは、Azure の環境内の App Insights インスタンスに書き込まれます。 このインスタンスを検索するには、`az ml env setup` コマンドを使用するときに指定した環境名を使用します。

- サービスを作成するときは、`-l` を true に設定します。
- Azure Portal で Application Insights を開きます。 実際の環境名を使用して Application Insights インスタンスを見つけます。
- Application Insights を開いたら、上部メニューの [検索] をクリックして結果を表示します。
- または `Analytics` > `Exceptions` > `exceptions take | 10` にアクセスします。


## <a name="4-error-handling-in-script"></a>4.スクリプトのエラー処理
`scoring.py` スクリプトの **run** 関数で Web サービス出力の一部としてエラー メッセージを返すために、例外処理を使用します。

Python の例:
```
    try:
        <code to load model and score>
   except Exception as e:
        return(str(e))
```

## <a name="5-other-common-problems"></a>5.その他の一般的な問題
- `env setup` コマンドが失敗する場合、サブスクリプションで使用できる十分なコア数があることを確認します。
- Web サービス名にアンダースコア ( _ ) を使用しないでください (*my_webservice* など)。
- Web サービスの呼び出し時に **502 無効なゲートウェイ** エラーを受け取る場合は再試行します。 これは、通常、コンテナーがクラスターにまだデプロイされていないことを意味します。
- サービスを作成するときに **CrashLoopBackOff** エラーを受け取る場合は、ログを確認します。 通常、これは **init** 関数に依存関係がない結果です。