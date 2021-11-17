---
title: Azure Spring Cloud でヒープ ダンプ、スレッド ダンプ、および JFR を手動でキャプチャする方法
description: ヒープ ダンプやスレッド ダンプを手動でキャプチャする方法、または JFR を開始する方法について説明します。
author: YinglueZhang-MS
ms.author: yinglzh
ms.service: spring-cloud
ms.topic: how-to
ms.date: 10/31/2021
ms.custom: devx-track-java
ms.openlocfilehash: 5f34dce5e177650b11858cabf770b7d63dc9b593
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2021
ms.locfileid: "132000579"
---
# <a name="how-to-manually-capture-heap-dump-thread-dump-and-jfr-in-azure-spring-cloud"></a>Azure Spring Cloud でヒープ ダンプ、スレッド ダンプ、および JFR を手動でキャプチャする方法

**この記事の適用対象:** ✔️ Java ❌ C#

効果的なトラブルシューティング。お客様が運用環境の問題をタイムリーに修正し、ビジネスが常にオンラインであるようにするうえで不可欠です。 現在、Azure Spring Cloud では、アプリケーション ログのストリーミング/クエリ、豊富なメトリック生成、アラート、分散トレースなどを提供し、この分野でお客様を支援します。 しかし、長い要求の待ち時間、JVM ヒープ リーク、または高い CPU 使用率について知らせるアラートが発生したときに、それらを末端で解決するソリューションはありません。 そのため、ヒープ ダンプの生成、スレッド ダンプの生成、または JFR の開始を手動で行えるようにしました。

## <a name="prerequisites"></a>前提条件
この演習を完了するには、以下が必要です。

* デプロイされた Azure Spring Cloud サービス インスタンス。 [Azure CLI を使用したアプリのデプロイに関するクイックスタート](./quickstart.md)に従って作業を開始してください。
* サービス インスタンスで既に作成してある少なくとも 1 つのアプリケーション。
* 生成された診断ファイルを保存するために、少なくとも 1 つの[永続ストレージがアプリにバインド](how-to-built-in-persistent-storage.md)されている必要があります。

**マウント パスの下のパスを使用する場合は、サブパスが既に作成されていることを確認してください。**

## <a name="generate-a-heap-dump"></a>ヒープ ダンプを生成する
Azure Spring Cloud でアプリのヒープ ダンプを生成します。
```heap dump command
   az spring-cloud app deployment generate-heap-dump -g <resource-group-name> -s <service-instance-name> --app <app-name> --deployment <deployment-name> --app-instance <app-instance name> --file-path <your-target-file-path-in-your-persistent-storage-mount-path>
```

## <a name="generate-a-thread-dump"></a>スレッド ダンプを生成する
Azure Spring Cloud でアプリのスレッド ダンプを生成します。
```thread dump command
   az spring-cloud app deployment generate-thread-dump -g <resource-group-name> -s <service-instance-name> --app <app-name> --deployment <deployment-name> --app-instance <app-instance name> --file-path <your-target-file-path-in-your-persistent-storage-mount-path>
```

## <a name="start-jfr"></a>JFR を開始する
Azure Spring Cloud でアプリの JFR を開始します。
```JFR command
   az spring-cloud app deployment start-JFR -g <resource-group-name> -s <service-instance-name> --app <app-name> --deployment <deployment-name> --app-instance <app-instance name> --file-path <your-target-file-path-in-your-persistent-storage-mount-path> --duration <duration-of-JFR>
```
期間の既定値は、60 秒です。
## <a name="get-your-diagnostic-files"></a>診断ファイルを取得する
永続ストレージのターゲット ファイル パスに移動し、ダンプ/JFR を見つけます。 これらのファイルは、ローカル コンピューターにダウンロードできます。 生成されたファイルの名前は、ヒープ ダンプの場合は '{appInstance}\_heapdump\_{timeStamp}.hprof'、スレッド ダンプの場合は '{appInstance}\_threaddump\_{timeStamp}.txt'、JFR の場合は '{appInstance}\_JFR\_{timeStamp}.jfr' のようになります。
