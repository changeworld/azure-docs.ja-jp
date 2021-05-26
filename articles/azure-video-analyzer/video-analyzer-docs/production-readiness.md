---
title: 運用環境の準備状況とベスト プラクティス
description: この記事では、運用環境で Azure Video Analyzer モジュールを構成してデプロイする方法について説明します。
ms.topic: reference
ms.date: 04/26/2021
ms.openlocfilehash: af353c6845259f09edf4f1cb6ee4282f0fae6ba9
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/25/2021
ms.locfileid: "110387848"
---
# <a name="production-readiness-and-best-practices"></a>運用環境の準備状況とベスト プラクティス

この記事では、運用環境で Azure Video Analyzer エッジ モジュールとクラウド サービスを構成してデプロイする方法について説明します。 また、IoT Edge ソリューションの準備について、記事「[IoT Edge ソリューションを運用環境にデプロイするための準備を行う](../../iot-edge/production-checklist.md)」を確認する必要があります。

> [!NOTE]
> セキュリティに関連する側面については、組織の IT 部門に問い合わせてください。

## <a name="creating-the-video-analyzer-account"></a>Video Analyzer アカウントの作成
Video Analyzer アカウントを[作成](create-video-analyzer-account.md)する場合、次のようにすることをお勧めします。
1. サブスクリプション所有者は、Video Analyzer に必要なすべてのリソースを作成するためのリソース グループを作成する必要があります。
1. その後、所有者はそのリソース グループに対する[共同作成者](../../role-based-access-control/built-in-roles.md#contributor)と[ユーザー アクセス管理者](../../role-based-access-control/built-in-roles.md#user-access-administrator)のロールをユーザーに付与する必要があります。
1. 次に、ユーザーは関連するリソース、つまり、ストレージ アカウント、ユーザー割り当てマネージド ID、Video Analyzer アカウントを、そのリソース グループの下に作成できます。

## <a name="running-the-module-as-a-local-user"></a>ローカル ユーザーとしてのモジュールの実行

Video Analyzer エッジ モジュールを IoT Edge デバイスにデプロイすると、既定では昇格された特権で実行されます。 これは、次のモジュール (`sudo iotedge logs {name-of-module}`) のログを使用して確認できます。

```
!! production readiness: user accounts – Warning
       LOCAL_USER_ID and LOCAL_GROUP_ID environment variables are not set. The program will run as root!
       For optimum security, make sure to set LOCAL_USER_ID and LOCAL_GROUP_ID environment variables to a non-root user and group.
```

以下のセクションでは、上記の警告に対処する方法について説明します。

### <a name="creating-and-using-a-local-user-account"></a>ローカル ユーザー アカウントの作成と使用

Video Analyzer エッジ モジュールは、可能な限り少ない特権を持つローカル ユーザー アカウントで実行でき、またそのようにする必要があります。 たとえば、次のコマンドは、Linux VM でローカル ユーザー アカウントを作成する方法を示しています。

```
sudo groupadd -g 1010 localedgegroup
sudo useradd --home-dir /home/localedgeuser --uid 1010 --gid 1010 localedgeuser
```

次に、配置マニフェストで、LOCAL_USER_ID と LOCAL_GROUP_ID 環境変数をその非ルート ユーザーとグループに設定できます。

```
"avaedge": {
"version": "1.0",
…
"env": {
    "LOCAL_USER_ID": 
    {
        "value": "1010"
    },
    "LOCAL_GROUP_ID": {
        "value": "1010"
    }
}
},
…
```

### <a name="granting-permissions-to-device-storage"></a>デバイスのストレージへのアクセス許可を付与する

Video Analyzer エッジ モジュールでは、次の場合にローカル ファイル システムにファイルを書き込める必要があります。

- モジュール ツインの [`applicationDataDirectory`](module-twin-configuration-schema.md) プロパティを使用する場合。ここでは、構成データを格納するローカル ファイル システムのディレクトリを指定する必要があります。
- パイプラインを使用してビデオをクラウドに記録します。モジュールで、エッジ デバイス上のディレクトリをキャッシュとして使用する必要があります (詳細については、記事「[継続的なビデオ記録](continuous-video-recording.md)」を参照してください)。
- [ローカル ファイルに記録する](event-based-video-recording-concept.md)場合。記録されるビデオのファイル パスを指定します。

上記のいずれかを使用する場合は、前述のユーザー アカウントに、関連するディレクトリへのアクセス権があることを確認する必要があります。 たとえば、`applicationDataDirectory` などを検討してみてください。 エッジ デバイス上にディレクトリを作成し、デバイス ストレージをモジュール ストレージにリンクすることができます。

```
sudo mkdir /var/lib/videoanalyzer
sudo chown -R localedgeuser:localedgegroup /var/lib/videoanalyzer
```

次に、配置マニフェストのエッジ モジュールの作成オプションで、上記のディレクトリ ("/var/lib/videoanalyzer") をモジュール内のディレクトリ ("/var/lib/videoanalyzer" など) にマッピングする `binds` 設定を追加できます。 また、`applicationDataDirectory` の値として後者のディレクトリを使用します。

```
        "modules": {
          "avaedge": {
            "version": "1.1",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/media/video-analyzer:1",
              "createOptions": "{ \"HostConfig\": { \"LogConfig\": { \"Type\": \"\", \"Config\": { \"max-size\": \"10m\", \"max-file\": \"10\" } }, \"Binds\": [ \"/var/media/:/var/media/\", \"/var/lib/videoanalyzer/:/var/lib/videoanalyzer\" ], \"IpcMode\": \"host\", \"ShmSize\": 1536870912 } }"
            },
            "env": {
              "LOCAL_USER_ID": {
                "value": "1010"
              },
              "LOCAL_GROUP_ID": {
                "value": "1010"
              }
            }
          },
          …
        },
        
    …
    
    "avaedge": {
       "properties.desired": {
          "applicationDataDirectory": "/var/lib/videoanalyzer",
          "ProvisioningToken": "{your-token}",
          "diagnosticsEventsOutputName": "diagnostics",
          "operationalEventsOutputName": "operational",
          "logLevel": "information",
          "LogCategories": "Application,Events",
          "allowUnsecuredEndpoints": true,
          "telemetryOptOut": false
          "allowUnsecuredEndpoints": false
    }
}
```

クイックスタートやチュートリアル (「[継続的なビデオ記録](use-continuous-video-recording.md)」など) のサンプル パイプラインを見るとわかるように、メディア キャッシュ ディレクトリ (`localMediaCachePath`) は `applicationDataDirectory` の下のサブディレクトリを使用します。 キャッシュには一時的なデータが含まれているため、これが推奨される方法です。

また、トラフィックのセキュリティを確保するために TLS 暗号化を使用する運用環境に対して推奨されているように、`allowedUnsecuredEndpoints` は `true` に設定されています。

### <a name="naming-video-or-files"></a>ビデオまたはファイルの名前付け

パイプラインを使用すると、ビデオをクラウドに記録したり、エッジ デバイス上の MP4 ファイルとして記録したりすることができます。 これらは、[継続的なビデオ記録](use-continuous-video-recording.md)、または[イベントベースのビデオ記録](record-event-based-live-video.md)を行うことで生成できます。

クラウドに記録する場合、ビデオ リソースに付ける名前の構造は "<anytext>-${System.TopologyName}-${System.PipelineName}" のようにすることをお勧めします。 特定のライブ パイプラインは 1 つの RTSP 対応 IP カメラにのみ接続できます。また、そのカメラからの入力は 1 つのビデオ リソースに記録する必要があります。 たとえば、次のように `VideoName` を Video Sink 上に設定できます。

```
"VideoName": "sampleVideo-${System.TopologyName}-${System.PipelineName}"
```
置換パターンは、 **${variableName}** のように、`$` 記号の後に中かっこを続けて指定します。

イベント ベースの記録を使用してエッジ デバイス上の MP4 ファイルに記録する場合は、次のコマンドを使用できます。

```
"fileNamePattern": "sampleFilesFromEVR-${System.TopologyName}-${System.PipelineName}-${fileSinkOutputName}-${System.Runtime.DateTime}"
```

> [!Note]
> 上記の例では、変数 **fileSinkOutputName** は、ライブ パイプラインを作成するときに定義するサンプル変数名です。 これは、システム変数では **ありません**。 **DateTime** を使用することで、イベントごとに一意の MP4 ファイル名を付けることができます。

#### <a name="system-variables"></a>システム変数

使用できるシステム定義変数には、次のものがあります。

| システム変数        | 説明                                                  | 例              |
| :--------------------- | :----------------------------------------------------------- | :------------------- |
| System.Runtime.DateTime        | ISO8601 ファイルに準拠した形式の UTC 日付時刻 (基本表現 YYYYYMMDDThhmmss)。 | 20200222T173200Z     |
| System.Runtime.PreciseDateTime | ミリ秒付きの ISO8601 ファイルに準拠した形式の UTC 日付時刻 (基本表現 YYYYMMDDThhmmss.sss)。 | 20200222T173200.123Z |
| System.TopologyName    | ユーザーが指定した実行中のパイプライン トポロジの名前。          | IngestAndRecord      |
| System.PipelineName    | ユーザーが指定した実行中のライブ パイプラインの名前。          | camera001            |

> [!Tip]
> System.Runtime.PreciseDateTime と System.Runtime.DateTime は、クラウド内のビデオに名前を付けるときには使用できません。

### <a name="tips-about-maintaining-your-edge-device"></a>エッジ デバイスの維持に関するヒント

> [!Note]
> 以下のヒントにはすべてが列挙されているわけではありませんが、よく知られている問題について確認するのに役立ちます。

IoT Edge デバイスとして使用している Linux VM は、定期的に管理されていない場合、応答しなくなる可能性があります。 キャッシュをクリーンな状態にし、不要なパッケージを排除し、未使用のコンテナーを VM から削除することが不可欠です。 これを行うため、ここではエッジ VM で使用できる一連の推奨コマンドを紹介します。

- `sudo apt-get clean`

apt-get clean コマンドは、/var/cache に残されている取得済みパッケージ ファイルのローカル リポジトリをクリアします。 クリーンアップするディレクトリは /var/cache/apt/archives/ と /var/cache/apt/archives/partial/ です。 /Var/cache/apt/archives に残されるファイルは、ロック ファイルと partial サブディレクトリのみです。 apt-get clean コマンドは、通常、定期的な予定メンテナンスの一環として、必要に応じてディスク領域をクリアするために使用されます。 詳細については、[apt-get を使用したクリーンアップ](https://www.networkworld.com/article/3453032/cleaning-up-with-apt-get.html)に関するページを参照してください。

- `sudo apt-get autoclean`

apt-get autoclean オプションは、apt-get clean のように、取得済みパッケージ ファイルのローカル リポジトリをクリアします。ただし、ダウンロードできなくなり、役に立たないファイルのみが削除されます。 これは、キャッシュが大きくなりすぎないようにするために役立ちます。

- `sudo apt-get autoremove`

autoremove オプションは、他のパッケージで必要になったために自動的にインストールされたものの、そのパッケージが削除されたために不要になったパッケージを削除します

- `sudo docker image ls` – エッジ システム上の Docker イメージの一覧を提供します

- `sudo docker system prune`

Docker は、イメージ、コンテナー、ボリューム、ネットワークなど、使用されていないオブジェクトをクリーンアップするための控えめなアプローチ ("ガベージ コレクション" と呼ばれることもあります) を採用しています。これらのオブジェクトは、通常、Docker に明示的に指示しない限り削除されません。 これにより、Docker は追加のディスク領域を使用する可能性があります。 オブジェクトの種類ごとに、Docker には prune コマンドが用意されています。 また、`docker system prune` を使用して、複数の種類のオブジェクトを一度にクリーンアップすることもできます。 詳細については、[未使用の Docker オブジェクトを排除する方法](https://docs.docker.com/config/pruning/)に関するページを参照してください。

- `sudo docker rmi REPOSITORY:TAG`

エッジ モジュールで更新が行われても、Docker に古いバージョンのエッジ モジュールがまだ存在している可能性があります。 このような場合は、`docker rmi` コマンドを使用して、イメージ バージョン タグで識別される特定のイメージを削除することをお勧めします。

## <a name="next-steps"></a>次の手順

[クイックスタート: 概要 - Azure Video Analyzer](get-started-detect-motion-emit-events.md)
