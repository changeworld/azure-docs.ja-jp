---
title: Azure Spring Cloud での高度なトラブルシューティングのための JVM オプションの診断設定
description: JVM 構成でヒープ ダンプ、JFR、および GC ログを設定するためのベストプラクティスをいくつか挙げます。
author: YinglueZhang-MS
ms.author: yinglzh
ms.service: spring-cloud
ms.topic: how-to
ms.date: 10/31/2021
ms.custom: devx-track-java
ms.openlocfilehash: fd29b9b7356bc16132d46f125d3f3cf2792c1654
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2021
ms.locfileid: "132000567"
---
# <a name="tutorial-diagnostic-settings-of-jvm-options-for-advanced-troubleshooting-in-azure-spring-cloud"></a>チュートリアル: Azure Spring Cloud での高度なトラブルシューティングのための JVM オプションの診断設定

**この記事の適用対象:** ✔️ Java ❌ C#

ヒープダンプ、JFR ログ、および GC ログに関連する JVM ベースのアプリケーション スタートアップ パラメーターがいくつかあります。 Azure Spring Cloud では、JVM オプションを使用して JVM を構成できます。 この記事では、お客様にとって役に立つ可能性のある例をいくつか紹介します。

## <a name="prerequisites"></a>前提条件
この演習を完了するには、以下が必要です。

* デプロイされた Azure Spring Cloud サービス インスタンス。 [Azure CLI を使用したアプリのデプロイに関するクイックスタート](./quickstart.md)に従って作業を開始してください。
* サービス インスタンスで既に作成してある少なくとも 1 つのアプリケーション。
* 生成された診断ファイルを保存するために、少なくとも 1 つの[永続ストレージがアプリにバインド](how-to-built-in-persistent-storage.md)されている必要があります。

ユーザーは、[展開ドキュメント](https://docs.microsoft.com/cli/azure/spring-cloud/app/deployment?view=azure-cli-latest)に従って、JVM ベースのアプリケーション スタートアップ パラメーターを構成できます。 次のセクションでは、いくつかの例を示します。 **必要な作業は、JVM オプションでパラメーターを追加することです。**

## <a name="generate-a-heap-dump-when-out-of-memory"></a>メモリが不足したときにヒープ ダンプを生成する
```heap dump when OOM
   -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=<PATH_TO_HEAP_DUMP_FOLDER> 
```
ファイルの特定の名前を指定することもできます。 ただし、フォルダー パスのみを指定することを強くお勧めします。 ファイル名を指定すると、最初の OOM に 1 つのヒープ ダンプしか生成されません。 hprof 形式のファイルを扱えないため、ユーザーは後続の OOM のヒープ ダンプを取得できません。 ユーザーがフォルダーへのパスのみを指定した場合、自動生成される名前ですべての OOM のヒープ ダンプを取得できます。

以下で指定するすべてのパス ユーザーは、お客様のアプリに既にバインドされているユーザーの永続ストレージのマウント パスの下に配置されている必要があります。

## <a name="gc-logs"></a>GC ログ
ここでは、GC ログに関連する JVM オプションの使用方法の例を示します。 詳細については、公式の JVM ドキュメントを参照してください。
```GC Logs
   -XX:+PrintGCDetails -Xloggc:<PATH_TO_GC_LOG_FILE>
```

## <a name="jfr-on-exit"></a>終了時の JFR
ここでは、JFR に関連する JVM オプションの使用方法の例を示します。 詳細については、公式の JVM ドキュメントを参照してください。
```JFR on exit
   -XX:StartFlightRecording=dumponexit=true,dumponexitpath=<PATH_TO_JFR_FILE>
```

## <a name="path-of-generated-file"></a>生成されたファイルのパス。
生成されたファイルのターゲット パスがアプリの永続ストレージに存在していることを確認する必要があることに注意してください。 そうすることで、ストレージ内のファイルを取得できます。
たとえば、ユーザーが Azure Spring Cloud に永続ストレージを最初に作成するときに、json ファイルが提供されます。
```json
    {
       "customPersistentDisks": [
          {
              "storageName": "<storage-resource-name>",
              "customPersistentDiskProperties": {
                  "type": "AzureFileVolume",
                  "shareName": "<azure-file-share-name>",
                  "mountPath": "<unique-mount-path e.g. /test/Path>",
                  "mountOptions": [
                      "uid=0",
                      "gid=0"
                   ],
                   "readOnly": false 
                }
          },
          {
              "storageName": "<storage-resource-name>",
              "customPersistentDiskProperties": {
                  "type": "AzureFileVolume",
                  "shareName": "<azure-file-share-name>",
                  "mountPath": "<unique-mount-path e.g. /test/anotherPath>",
                  "readOnly": true
              }
          }
       ]
    }
```
または、以下のコマンドを使用して永続ストレージを追加することもできます。
    
```
   az spring-cloud app append-persistent-storage --persistent-storage-type AzureFileVolume --share-name <azure-file-share-name> --mount-path <unique-mount-path e.g. /test/Path> --storage-name <storage-resource-name> -n <app-name> -g <resource-group-name> -s <spring-instance-name>
```
   
   お客様が上記の独自の Azure ファイルの下にダンプを作成する場合は、<PATH_TO_HEAP_DUMP_FOLDER> を "/test/Path" として指定してください。 マウント パスの下のパスを使用する場合は、サブパスが既に作成されていることを確認してください。




