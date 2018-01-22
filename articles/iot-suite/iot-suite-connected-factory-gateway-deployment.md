---
title: "コネクテッド ファクトリ ゲートウェイのデプロイ - Azure | Microsoft Docs"
description: "Windows または Linux 上にゲートウェイをデプロイして、コネクテッド ファクトリ事前構成済みソリューションに接続できるようにする方法。"
services: 
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/11/2017
ms.author: dobett
ms.openlocfilehash: c9854c68a95c2c1cc584503eb2f0b0dba6091016
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2018
---
# <a name="deploy-an-edge-gateway-for-the-connected-factory-preconfigured-solution-on-windows-or-linux"></a>コネクテッド ファクトリ事前構成済みソリューション用のエッジ ゲートウェイを Windows または Linux 上にデプロイします

"*コネクテッド ファクトリ*" 事前構成済みソリューション用のエッジ ゲートウェイをデプロイするには、次の 2 つのソフトウェア コンポーネントが必要です。

- *OPC Proxy* は、コネクテッド ファクトリとの接続を確立します。 OPC Proxy は、コネクテッド ファクトリ ソリューション ポータルで動作する統合された OPC ブラウザーからのコマンドと制御メッセージを待機します。

- *OPC Publisher* は、既存のオンプレミス OPC UA サーバーに接続し、そこからコネクテッド ファクトリにテレメトリ メッセージを転送します。 [OPC UA 用の OPC クラシック アダプター](https://github.com/OPCFoundation/UA-.NETStandard/blob/master/ComIOP/README.md)を使用して、OPC クラシック デバイスに接続できます。

どちらのコンポーネントもオープン ソースであり、GitHub 上のソースとして、または DockerHub 上の Docker コンテナーとして利用できます。

| GitHub | DockerHub |
| ------ | --------- |
| [OPC Publisher](https://github.com/Azure/iot-edge-opc-publisher) | [OPC Publisher](https://hub.docker.com/r/microsoft/iot-edge-opc-publisher/)   |
| [OPC Proxy](https://github.com/Azure/iot-edge-opc-proxy)         | [OPC Proxy](https://hub.docker.com/r/microsoft/iot-edge-opc-proxy/) |

どちらのコンポーネントも、公開 IP アドレスまたはゲートウェイ ファイアウォールの受信ポートの開放は必要ありません。 OPC Proxy コンポーネントと OPC Publisher コンポーネントでは、送信ポート 443 のみが使用されます。

この記事では、Docker を使用して Windows または Linux 上にエッジ ゲートウェイをデプロイする手順について説明します。 ゲートウェイにより、コネクテッド ファクトリ事前構成済みソリューションに接続できるようになります。 コンポーネントは、コネクテッド ファクトリなしで使用することもできます。

> [!NOTE]
> どちらのコンポーネントも、[Azure IoT Edge](https://github.com/Azure/iot-edge) 内でモジュールとして使用できます。

## <a name="choose-a-gateway-device"></a>ゲートウェイ デバイスを選択する

ゲートウェイ デバイスがまだない場合は、Microsoft のパートナーのいずれかから商用ゲートウェイを購入することをお勧めします。 コネクテッド ファクトリ ソリューションと互換性のあるゲートウェイ デバイスの一覧については、[Azure IoT デバイス カタログ](https://catalog.azureiotsuite.com/?q=opc)をご覧ください。 デバイスに付属する手順書に従って、ゲートウェイを設定します。

または、以下の手順に従って、既存のゲートウェイ デバイスを手動で設定します。

## <a name="install-and-configure-docker"></a>Docker をインストールして構成する

[Docker for Windows](https://www.docker.com/docker-windows) を Windows ベースのゲートウェイ デバイスにインストールするか、パッケージ マネージャーを使用して Linux ベースのゲートウェイ デバイスに Docker をインストールします。

Docker for Windows のセットアップ時に、Docker と共有するホスト コンピューターのドライブを選択します。 次のスクリーンショットは、Docker コンテナーの内部からホスト ドライブへのアクセスを許可するために Windows システムの **D** ドライブを共有することを示しています。

![Docker for Windows のインストール](./media/iot-suite-connected-factory-gateway-deployment/image1.png)

> [!NOTE]
> この手順は、Docker のインストール後に **[設定]** ダイアログから実行することもできます。 Windows システム トレイの **[Docker]** アイコンを右クリックし、**[設定]** を選択します。

Linux を使用している場合は、ファイル システムへのアクセスを有効にするために必要な追加構成はありません。

Windows では Docker と共有したドライブにフォルダーを作成し、Linux ではルート ファイル システムの下にフォルダーを作成します。 このチュートリアルでは、このフォルダーを `<SharedFolder>` と呼びます。

Docker コマンドで `<SharedFolder>` を参照するときは、必ずオペレーティング システムの正しい構文を使用してください。 Windows 用と Linux 用の 2 つの例を次に示します。

- Windows で `D:\shared` フォルダーを `<SharedFolder>` として使用する場合、Docker コマンドの構文は `//d/shared` になります。

- Linux で `/shared` フォルダーを `<SharedFolder>` として使用する場合、Docker コマンドの構文は `/shared` になります。

詳細については、Docker エンジン リファレンスの[ボリュームの使用](https://docs.docker.com/engine/admin/volumes/volumes/)に関する記事を参照してください。

## <a name="configure-the-opc-components"></a>OPC コンポーネントを構成する

OPC コンポーネントをインストールする前に、次の手順を完了して環境を準備してください。

1. ゲートウェイのデプロイを完了するには、コネクテッド ファクトリのデプロイに IoT Hub の**iothubowner** 接続文字列が必要です。 [Azure Portal](http://portal.azure.com/) で、コネクテッド ファクトリ ソリューションのデプロイ時に作成したリソース グループの IoT Hub に移動します。 **[共有アクセス ポリシー]** をクリックして、**iothubowner** 接続文字列にアクセスします。

    ![IoT Hub 接続文字列を見つける](./media/iot-suite-connected-factory-gateway-deployment/image2.png)

    **[接続文字列 -- 主キー]** の値をコピーします。

1. Docker コンテナー間の通信を許可するのには、ユーザー定義のブリッジ ネットワークが必要です。 コンテナー用のブリッジ ネットワークを作成するには、コマンド プロンプトで次のコマンドを実行します。

    ```cmd/sh
    docker network create -d bridge iot_edge
    ```

    **iot_edge** ブリッジ ネットワークが作成されたことを確認するには、次のコマンドを実行します。

    ```cmd/sh
    docker network ls
    ```

    ネットワークの一覧に **Iot_edge** ブリッジ ネットワークが含まれます。

OPC Publisher を実行するには、コマンド プロンプトで次のコマンドを実行します。

```cmd/sh
docker run --rm -it -v <SharedFolder>:/docker -v x509certstores:/root/.dotnet/corefx/cryptography/x509stores --network iot_edge --name publisher -h publisher -p 62222:62222 --add-host <OpcServerHostname>:<IpAddressOfOpcServerHostname> microsoft/iot-edge-opc-publisher:2.1.3 publisher "<IoTHubOwnerConnectionString>" --lf /docker/publisher.log.txt --as true --si 1 --ms 0 --tm true --vc true --di 30
```

- [OPC Publisher GitHub](https://github.com/Azure/iot-edge-opc-publisher) と [Docker 実行リファレンス](https://docs.docker.com/engine/reference/run/)で、以下の項目の詳細を参照できます。

  - コンテナー名の前に指定される Docker コマンド ライン オプション (`microsoft/iot-edge-opc-publisher:2.1.3`)。
  - コンテナー名の後ろに指定される OPC Publisher コマンド ライン パラメーターの意味 (`microsoft/iot-edge-opc-publisher:2.1.3`)。

- `<IoTHubOwnerConnectionString>` は、Azure Portal の **iothubowner** 共有アクセス ポリシーの接続文字列です。 この接続文字列は、前の手順でコピーしています。 この接続文字列は、OPC Publisher の初回の実行時にのみ必要です。 セキュリティ リスクがあるため、以降の実行時は省略する必要があります。

- 使用する `<SharedFolder>`とその構文については、「[Docker をインストールして構成する](#install-and-configure-docker)」セクションで説明されています。 OPC Publisherは、`<SharedFolder>` を使用して OPC Publisher 構成ファイルを読み取り、ログ ファイルに書き込み、コンテナーの外部から両方のファイルを使用できるようにします。

- OPC Publisher は、その構成を **publishednodes.json** ファイルから読み取ります。このファイルは `<SharedFolder>/docker` フォルダーに置く必要があります。 この構成ファイルは、OPC Publisher がサブスクライブする必要がある OPC UA サーバー上の OPC UA ノード データを定義します。

- OPC UA サーバーが OPC Publisher にデータの変更を通知するたびに、新しい値が IoT Hub に送信されます。 バッチ処理の設定によっては、OPC Publisher は、データを蓄積した後で IoT Hub にデータをバッチ送信する場合があります。

- **publishednodes.json** ファイルの完全な構文については、GitHub の [OPC Publisher](https://github.com/Azure/iot-edge-opc-publisher) ページで説明されています。

    次のスニペットは、**publishednodes.json** ファイルの簡単な例を示しています。 この例は、ホスト名 **win10pc** の OPC UA サーバーから **CurrentTime** 値を公開する方法を示しています。

    ```json
    [
      {
        "EndpointUrl": "opc.tcp://win10pc:48010",
        "OpcNodes": [
          {
            "ExpandedNodeId": "nsu=http://opcfoundation.org/UA/;i=2258"
          }
        ]
      }
    ]
    ```

    **Publishednodes.json** ファイルでは、OPC UA サーバーはエンドポイント URL によって指定されます。 前の例のように、IP アドレスの代わりにホスト名ラベル (**win10pc** など) を使用してホスト名を指定する場合は、コンテナーでのネットワーク アドレスの解決で、このホスト名ラベルが IP アドレスに解決できるようにする必要があります。

- Docker は、NetBIOS 名の解決はサポートしません。DNS 名の解決のみをサポートします。 ネットワークに DNS サーバーがない場合は、前のコマンド ラインの例に示されている回避策を使用できます。 前のコマンド ラインの例では、`--add-host` パラメーターを使用して、コンテナーのホスト ファイルにエントリを追加します。 このエントリによって、特定の `<OpcServerHostname>` のホスト名検索が可能になり、IP アドレス `<IpAddressOfOpcServerHostname>` に解決されます。

- OPC UA は、認証と暗号化で X.509 証明書を使用します。 OPC Publisher 証明書を接続先の OPC UA サーバーに配置して、OPC Publisher を信頼できるようにしておく必要があります。 OPC Publisher 証明書ストアは、`<SharedFolder>/CertificateStores` フォルダーに配置されます。 OPC Publisher 証明書は、`CertificateStores` フォルダー内の `trusted/certs` フォルダーで見つけることができます。

  OPC UA サーバーを構成する手順は、使用しているデバイスによって異なります。 これらの手順は、通常は OPC UA サーバーのユーザー マニュアルに記載されています。

OPC Proxy をインストールするには、コマンド プロンプトで次のコマンドを実行します。

```cmd/sh
docker run -it --rm -v <SharedFolder>:/mapped --network iot_edge --name proxy --add-host <OpcServerHostname>:<IpAddressOfOpcServerHostname> microsoft/iot-edge-opc-proxy:1.0.2 -i -c "<IoTHubOwnerConnectionString>" -D /mapped/cs.db
```

インストールは、システムで 1 回のみ実行する必要があります。

次のコマンドを使用して OPC Proxy を実行します。

```cmd/sh
docker run -it --rm -v <SharedFolder>:/mapped --network iot_edge --name proxy --add-host <OpcServerHostname>:<IpAddressOfOpcServerHostname> microsoft/iot-edge-opc-proxy:1.0.2 -D /mapped/cs.db
```

OPC Proxy は、インストール中に接続文字列を保存します。 セキュリティ リスクがあるため、以降の実行では接続文字列を省略する必要があります。

## <a name="enable-your-gateway"></a>ゲートウェイを有効にする

次の手順を完了して、コネクテッド ファクトリ構成済みソリューションで、ゲートウェイを有効にします。

1. 両方のコンポーネントが実行されているときに、コネクテッド ファクトリ ソリューション ポータルの **[独自の OPC UA サーバーを接続する]** ページに移動します。 このページは、ソリューションの管理者のみが使用できます。 パブリッシャー エンドポイント URL (opc.tcp://publisher:62222) を入力し、**[接続]** をクリックします。

1. コネクテッド ファクトリ ポータルと OPC Publisher の間に信頼関係を確立します。 証明書の警告が表示されたら、**[続行]** をクリックします。 次に、OPC Publisher が UA Web クライアントを信頼していないことを示すエラーが表示されます。 このエラーを解決するには、**UA Web クライアント**証明書を `<SharedFolder>/CertificateStores/rejected/certs` フォルダーからゲートウェイの `<SharedFolder>/CertificateStores/trusted/certs` フォルダーにコピーします。 ゲートウェイを再起動する必要はありません。

これでクラウドからゲートウェイに接続できるようになりました。OPC UA サーバーをソリューションに追加する準備ができました。

## <a name="add-your-own-opc-ua-servers"></a>独自の OPC UA サーバーを追加する

独自の OPC UA サーバーをコネクテッド ファクトリの事前構成済みソリューションに追加するには:

1. コネクテッド ファクトリ ソリューション ポータルの **[独自の OPC UA サーバーを接続する]** ページに移動します。 前のセクションの手順に従って、コネクテッド ファクトリ ポータルと OPC UA サーバーの間の信頼関係を確立します。

    ![ソリューション ポータル](./media/iot-suite-connected-factory-gateway-deployment/image4.png)

1. OPC UA サーバーの OPC UA ノード ツリーに移動します。コネクテッド ファクトリに送信する OPC ノードを右クリックし、**[発行]** をクリックします。

1. これでテレメトリがゲートウェイ デバイスから送られてくるようになります。 テレメトリは、コネクテッド ファクトリ ポータルの **[ファクトリの場所]** ビューにある **[新しいファクトリ]** で確認できます。

## <a name="next-steps"></a>次の手順

コネクテッド ファクトリ事前構成済みソリューションのアーキテクチャの詳細については、「[コネクテッド ファクトリ事前構成済みソリューションのチュートリアル](https://docs.microsoft.com/azure/iot-suite/iot-suite-connected-factory-sample-walkthrough)」をご覧ください。

[OPC Publisher のリファレンス実装](https://docs.microsoft.com/azure/iot-suite/iot-suite-connected-factory-publisher)をご覧ください。