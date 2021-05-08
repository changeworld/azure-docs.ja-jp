---
title: Microsoft OPC Publisher のデプロイ
description: このチュートリアルでは、スタンドアロン モードで OPC Publisher をデプロイする方法について説明します。
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: c82d15541459b5b482e427fc707b92755aa02c6c
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104787231"
---
# <a name="tutorial-deploy-the-opc-publisher"></a>チュートリアル: OPC Publisher をデプロイする

OPC Publisher は、オープンで開発された完全にサポートされている Microsoft 製品であり、産業用資産と Microsoft Azure クラウドの間のギャップを埋めます。 このために、OPC UA 対応の資産または産業用接続ソフトウェアに接続し、IEC62541 OPC UA PubSub 標準形式 (バージョン 2.6 以降) など、さまざまな形式でテレメトリ データを [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) に発行します。

これは、[Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) でモジュールとして、またはプレーン Docker でコンテナーとして実行されます。 [.NET クロスプラットフォーム ランタイム](https://docs.microsoft.com/dotnet/core/introduction)を利用するので、Linux と Windows 10 でもネイティブに実行されます。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * OPC Publisher をデプロイする
> * OPC Publisher の最新リリース バージョンをコンテナーとして実行する
> * Azure portal でコンテナーの作成オプションを指定する

Azure サブスクリプションを持っていない場合は、無料試用版アカウントを作成してください。

## <a name="prerequisites"></a>前提条件

- IoT ハブが作成されている必要があります
- IoT Edge デバイスが作成されている必要があります

## <a name="deploy-the-opc-publisher-from-the-azure-marketplace"></a>Azure Marketplace から OPC Publisher をデプロイする

1. 使用する Azure サブスクリプションを選択します。 使用可能な Azure サブスクリプションがない場合は、作成する必要があります。
2. OPC Publisher からのデータ送信先となる IoT ハブを選択します。 使用可能な IoT ハブがない場合は、作成する必要があります。
3. OPC Publisher が実行される IoT Edge デバイスを選択します (または、作成する新しい IoT Edge デバイスの名前を入力します)。
4. [作成] をクリックします。 選択した IoT Edge デバイスの [デバイスのモジュールを設定してください] ページが開きます。
5. [OPCPublisher] をクリックして OPC Publisher の [IoT Edge モジュールの更新] ページを開き、[コンテナーの作成オプション] を選択します。
6. OPC Publisher の使用状況に基づいてコンテナーの作成オプションを追加で指定します。次のセクションを参照してください。


### <a name="accessing-the-microsoft-container-registry-docker-containers-for-opc-publisher-manually"></a>OPC Publisher の Microsoft Container Registry Docker コンテナーに手動でアクセスする

OPC Publisher の最新リリース バージョンは、次の方法を利用して手動で実行できます。

```
docker run mcr.microsoft.com/iotedge/opc-publisher:latest <name>
```

ここでは、"name" はコンテナーの名前です。

## <a name="specifying-container-create-options-in-the-azure-portal"></a>Azure portal でコンテナーの作成オプションを指定する
Azure portal を使用して OPC Publisher をデプロイする際、OPC Publisher の [IoT Edge モジュールの更新] ページでコンテナーの作成オプションを指定できます。 これらの作成オプションは JSON 形式にする必要があります。 OPC Publisher のコマンド ライン引数は、次の例のように、Cmd キーを使用して指定できます。
```
"Cmd": [
    "--pf=./pn.json",
    "--aa"
],
```

OPC Publisher の典型的な IoT Edge モジュールのコンテナーの作成オプションは次のとおりです。
```
{
    "Hostname": "opcpublisher",
    "Cmd": [
        "--pf=./pn.json",
        "--aa"
    ],
    "HostConfig": {
        "Binds": [
            "/iiotedge:/appdata"
        ]
    }
}
```

これらのオプションが指定された OPC Publisher は構成ファイル `./pn.json` を読み取ります。 OPC Publisher の作業ディレクトリは、起動時に `/appdata` に設定されるため、OPC Publisher はその Docker コンテナー内の `/appdata/pn.json` ファイルを読み取ります。 OPC Publisher のログ ファイルは `/appdata` に書き込まれ、(OPC UA 証明書に使用される) `CertificateStores` ディレクトリもこのディレクトリ内に作成されます。 これらのファイルを IoT Edge ホスト ファイル システムで使用できるようにするには、コンテナーの構成にバインド マウント ボリュームが必要です。 `/iiotedge:/appdata` バインドによって、ディレクトリ `/appdata` がホスト ディレクトリ `/iiotedge` (存在しない場合は IoT Edge ランタイムによって作成されます) にマップされます。
**このバインド マウント ボリュームがない場合は、コンテナーが再起動されたときに OPC Publisher の構成ファイルがすべて失われます。**

ネットワーク上で DNS サーバーが構成されていない場合にホスト名を使用して OPC UA サーバーに接続できるようにするには、`HostConfig` セクションに `ExtraHosts` エントリを追加します。

```
"HostConfig": {
    "ExtraHosts": [
        "opctestsvr:192.168.178.26"
    ]
}
```

## <a name="next-steps"></a>次のステップ 
OPC Publisher Edge モジュールをデプロイしたので、次の手順ではそれを構成します。

> [!div class="nextstepaction"]
> [OPC Publisher の構成](tutorial-publisher-configure-opc-publisher.md)