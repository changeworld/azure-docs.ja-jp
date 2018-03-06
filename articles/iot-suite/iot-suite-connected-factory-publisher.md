---
title: "Azure IoT Suite コネクテッド ファクトリ OPC Publisher の使用 | Microsoft Docs"
description: "コネクテッド ファクトリ OPC Publisher のリファレンス実装の構築方法とデプロイ方法。"
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
ms.date: 11/14/2017
ms.author: dobett
ms.openlocfilehash: 934f4deb3139c136c871ab0125ba45267e1d1b05
ms.sourcegitcommit: 2c2dfe04a0af718cf058445467fa407ead5f45a4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/22/2018
---
# <a name="opc-publisher-for-azure-iot-edge"></a>OPC Publisher for Azure IoT Edge

この記事では、OPC Publisher のリファレンス実装を使用する方法について説明します。 このリファレンス実装は、Azure IoT Edge を使用して次のことを行う方法を示します。

- 既存の OPC UA サーバーに接続する。
- OPC UA サーバーから取得した、JSON でエンコードされた利用統計情報を、JSON ペイロードを使用して、OPC UA *Pub/Sub* 形式で Azure IoT Hub に発行する。 Azure IoT Edge でサポートされているトランスポート プロトコルのいずれかを使用できます。

このリファレンス アプリケーションには次のものが含まれます。

- ご利用のネットワーク上にある既存の OPC UA サーバーに接続する OPC UA *クライアント*。
- 発行されたものを管理するために使用できるポート 62222 でリッスンする OPC UA *サーバー*。

このアプリケーションは、.NET Core を使用して実装され、.NET Core でサポートされているプラットフォーム上で実行できます。

このパブリッシャーは、エンドポイントに接続を確立するときの再試行ロジックを実装します。 パブリッシャーは、指定したキープ アライブ要求の数の範囲内でエンドポイントが応答することを予期します。 この再試行ロジックによってパブリッシャーは、OPC UA サーバーの電源障害などの状況を検出できます。

OPC UA サーバーに対する明確な発行間隔ごとに、パブリッシャーは、この発行間隔ですべてのノードを更新する別のサブスクリプションを作成します。

ネットワーク負荷を減らすために、このパブリッシャーは IoT Hub に送信するデータのバッチ処理をサポートします。 バッチは、構成したパッケージ サイズに達した場合にのみ IoT Hub に送信されます。

このアプリケーションは OPC Foundations の OPC UA リファレンス スタックを使用するため、ライセンス制限が適用されます。 OPC UA のドキュメントとライセンス条項については、http://opcfoundation.github.io/UA-.NETStandardLibrary/ をご覧ください。

OPC Publisher のソース コードは、GitHub リポジトリの [OPC Publisher for Azure IoT Edge](https://github.com/Azure/iot-edge-opc-publisher) で確認できます。

## <a name="prerequisites"></a>前提条件

ご利用のオペレーティング システムのアプリケーションを構築するには、[.NET Core SDK 1.1.](https://docs.microsoft.com/dotnet/core/sdk) が 必要です。

## <a name="build-the-application"></a>アプリケーションのビルド

### <a name="as-native-windows-application"></a>Windows ネイティブ アプリケーションとして

Visual Studio 2017 で `OpcPublisher.sln` プロジェクトを開き、F7 キーを押してソリューションを構築します。

### <a name="as-docker-container"></a>Docker コンテナーとして

Windows Docker コンテナーとしてアプリケーションを構築するには、`Dockerfile.Windows` 構成ファイルを使用します。

Linux Docker コンテナーとしてアプリケーションを構築するには、`Dockerfile` 構成ファイルを使用します。

開発用マシンのリポジトリのルートから、コンソール ウィンドウで次のコマンドを入力します。

`docker build -f <docker-configfile-to-use> -t <your-container-name> .`

`docker build` の `-f` オプションは省略可能で、既定では `Dockerfile` 構成ファイルを使用するようになっています。

Docker を使用すると、Git リポジトリから直接構築することもできます。 次のコマンドを使用して、Linux Docker コンテナーを構築できます。

`docker build -t <your-container-name> .https://github.com/Azure/iot-edge-opc-publisher`

## <a name="configure-the-opc-ua-nodes-to-publish"></a>発行を行う OPC UA ノードの構成

Azure IoT Hub に発行される値を持つ OPC UA ノードを構成するには、JSON 形式の構成ファイルを作成します。 この構成ファイルの既定の名前は `publishednodes.json` です。 このアプリケーションは、OPC UA サーバーのメソッドである **PublishNode** または **UnpublishNode** を使用するときに、この構成ファイルを更新して保存します。

構成ファイルの構文は次のとおりです。

```json
[
    {
        // example for an EnpointUrl is: opc.tcp://win10iot:51210/UA/SampleServer
        "EndpointUrl": "opc.tcp://<your_opcua_server>:<your_opcua_server_port>/<your_opcua_server_path>",
        "OpcNodes": [
            // Publisher will request the server at EndpointUrl to sample the node with the OPC sampling interval specified on command line (or the default value: OPC publishing interval)
            // and the subscription will publish the node value with the OPC publishing interval specified on command line (or the default value: server revised publishing interval).
            {
                // The identifier specifies the NamespaceUri and the node identifier in XML notation as specified in Part 6 of the OPC UA specification in the XML Mapping section.
                "ExpandedNodeId": "nsu=http://opcfoundation.org/UA/;i=2258"
            },
            // Publisher will request the server at EndpointUrl to sample the node with the OPC sampling interval specified on command line (or the default value: OPC publishing interval)
            // and the subscription will publish the node value with an OPC publishing interval of 4 seconds.
            // Publisher will use for each dinstinct publishing interval (of nodes on the same EndpointUrl) a separate subscription. All nodes without a publishing interval setting,
            // will be on the same subscription and the OPC UA stack will publish with the lowest sampling interval of a node.
            {
                "ExpandedNodeId": "nsu=http://opcfoundation.org/UA/;i=2258",
                "OpcPublishingInterval": 4000
            },
            // Publisher will request the server at EndpointUrl to sample the node with the given sampling interval of 1 second
            // and the subscription will publish the node value with the OPC publishing interval specified on command line (or the default value: server revised interval).
            // If the OPC publishing interval is set to a lower value, Publisher will adjust the OPC publishing interval of the subscription to the OPC sampling interval value.
            {
                "ExpandedNodeId": "nsu=http://opcfoundation.org/UA/;i=2258",
                // the OPC sampling interval to use for this node.
                "OpcSamplingInterval": 1000
            }
        ]
    },

    // the format below is only supported for backward compatibility. you need to ensure that the
    // OPC UA server on the configured EndpointUrl has the namespaceindex you expect with your configuration.
    // please use the ExpandedNodeId syntax instead.
    {
        "EndpointUrl": "opc.tcp://<your_opcua_server>:<your_opcua_server_port>/<your_opcua_server_path>",
        "NodeId": {
            "Identifier": "ns=0;i=2258"
        }
    }
    // please consult the OPC UA specification for details on how OPC monitored node sampling interval and OPC subscription publishing interval settings are handled by the OPC UA stack.
    // the publishing interval of the data to Azure IoT Hub is controlled by the command line settings (or the default: publish data to IoT Hub at least each 1 second).
]
```

## <a name="run-the-application"></a>アプリケーションの実行

### <a name="command-line-options"></a>コマンド ライン オプション

アプリケーションの詳細な使用状況を表示するには、コマンド ライン オプションの `--help` を使用します。 次の例では、コマンドの構造を示します。

```cmd/sh
OpcPublisher.exe <applicationname> [<IoT Hubconnectionstring>] [<options>]
```

`applicationname` は、使用する OPC UA アプリケーションの名前です。 このパラメーターは必須です。 このアプリケーション名は、IoT Hub デバイス レジストリにパブリッシャーを登録するためにも使用されます。

`IoT Hubconnectionstring` は、IoT Hub 所有者の接続文字列です。 このパラメーターは省略可能です。

次のオプションがサポートされています。

```cmd/sh
--pf, --publishfile=VALUE
  the filename to configure the nodes to publish.
    Default: './publishednodes.json'
--sd, --shopfloordomain=VALUE
  the domain of the shopfloor. if specified this
    domain is appended (delimited by a ':' to the '
    ApplicationURI' property when telemetry is
    sent to IoT Hub.
    The value must follow the syntactical rules of a
    DNS hostname.
    Default: not set
--sw, --sessionconnectwait=VALUE
  specify the wait time in seconds publisher is
    trying to connect to disconnected endpoints and
    starts monitoring unmonitored items
    Min: 10
    Default: 10
--vc, --verboseconsole=VALUE
  the output of publisher is shown on the console.
    Default: False
--ih, --IoT Hubprotocol=VALUE
  the protocol to use for communication with Azure
    IoT Hub (allowed values: Amqp, Http1, Amqp_
    WebSocket_Only, Amqp_Tcp_Only, Mqtt, Mqtt_
    WebSocket_Only, Mqtt_Tcp_Only).
    Default: Mqtt
--ms, --IoT Hubmessagesize=VALUE
  the max size of a message which can be send to
    IoT Hub. when telemetry of this size is available
    it will be sent.
    0 will enforce immediate send when telemetry is
    available
    Min: 0
    Max: 256 * 1024
    Default: 4096
--si, --IoT Hubsendinterval=VALUE
  the interval in seconds when telemetry should be
    send to IoT Hub. If 0, then only the
    IoT Hubmessagesize parameter controls when
    telemetry is sent.
    Default: '1'
--lf, --logfile=VALUE
  the filename of the logfile to use.
    Default: './Logs/<applicationname>.log.txt'
--pn, --portnum=VALUE
  the server port of the publisher OPC server
    endpoint.
    Default: 62222
--pa, --path=VALUE
  the endpoint URL path part of the publisher OPC
    server endpoint.
    Default: '/UA/Publisher'
--lr, --ldsreginterval=VALUE
  the LDS(-ME) registration interval in ms. If 0,
    then the registration is disabled.
    Default: 0
--ot, --operationtimeout=VALUE
  the operation timeout of the publisher OPC UA
    client in ms.
    Default: 120000
--oi, --opcsamplinginterval=VALUE
  the publisher is using this as default value in
    milliseconds to request the servers to sample
    the nodes with this interval
    this value might be revised by the OPC UA
    servers to a supported sampling interval.
    please check the OPC UA specification for
    details how this is handled by the OPC UA stack.
    a negative value will set the sampling interval
    to the publishing interval of the subscription
    this node is on.
    0 will configure the OPC UA server to sample in
    the highest possible resolution and should be
    taken with care.
    Default: 1000
--op, --opcpublishinginterval=VALUE
  the publisher is using this as default value in
    milliseconds for the publishing interval setting
    of the subscriptions established to the OPC UA
    servers.
    please check the OPC UA specification for
    details how this is handled by the OPC UA stack.
    a value less than or equal zero will let the
    server revise the publishing interval.
    Default: 0
--ct, --createsessiontimeout=VALUE
  specify the timeout in seconds used when creating
    a session to an endpoint. On unsuccessful
    connection attemps a backoff up to 5 times the
    specified timeout value is used.
    Min: 1
    Default: 10
--ki, --keepaliveinterval=VALUE
  specify the interval in seconds the publisher is
    sending keep alive messages to the OPC servers
    on the endpoints it is connected to.
    Min: 2
    Default: 2
--kt, --keepalivethreshold=VALUE
  specify the number of keep alive packets a server
    can miss, before the session is disconneced
    Min: 1
    Default: 5
--st, --opcstacktracemask=VALUE
  the trace mask for the OPC stack. See github OPC .
    NET stack for definitions.
    To enable IoT Hub telemetry tracing set it to 711.
    Default: 285  (645)
--as, --autotrustservercerts=VALUE
  the publisher trusts all servers it is
    establishing a connection to.
    Default: False
--tm, --trustmyself=VALUE
  the publisher certificate is put into the trusted
    certificate store automatically.
    Default: True
--fd, --fetchdisplayname=VALUE
  enable to read the display name of a published
    node from the server. this will increase the
    runtime.
    Default: False
--at, --appcertstoretype=VALUE
  the own application cert store type.
    (allowed values: Directory, X509Store)
    Default: 'X509Store'
--ap, --appcertstorepath=VALUE
  the path where the own application cert should be
    stored
    Default (depends on store type):
    X509Store: 'CurrentUser\UA_MachineDefault'
    Directory: 'CertificateStores/own'
--tt, --trustedcertstoretype=VALUE
  the trusted cert store type.
    (allowed values: Directory, X509Store)
    Default: Directory
--tp, --trustedcertstorepath=VALUE
  the path of the trusted cert store
    Default (depends on store type):
    X509Store: 'CurrentUser\UA_MachineDefault'
    Directory: 'CertificateStores/trusted'
--rt, --rejectedcertstoretype=VALUE
  the rejected cert store type.
    (allowed values: Directory, X509Store)
    Default: Directory
--rp, --rejectedcertstorepath=VALUE
  the path of the rejected cert store
    Default (depends on store type):
    X509Store: 'CurrentUser\UA_MachineDefault'
    Directory: 'CertificateStores/rejected'
--it, --issuercertstoretype=VALUE
  the trusted issuer cert store type.
    (allowed values: Directory, X509Store)
    Default: Directory
--ip, --issuercertstorepath=VALUE
  the path of the trusted issuer cert store
    Default (depends on store type):
    X509Store: 'CurrentUser\UA_MachineDefault'
    Directory: 'CertificateStores/issuers'
--dt, --devicecertstoretype=VALUE
  the IoT Hub device cert store type.
    (allowed values: Directory, X509Store)
    Default: X509Store
--dp, --devicecertstorepath=VALUE
  the path of the iot device cert store
    Default Default (depends on store type):
    X509Store: 'My'
    Directory: 'CertificateStores/IoT Hub'
-h, --help
  show this message and exit
```

次の環境変数を使用してアプリケーションを制御できます。
- `_HUB_CS` は、IoT Hub 所有者の接続文字列を設定します
- `_GW_LOGP` は、使用するログ ファイルのファイル名を設定します
- `_TPC_SP` は、信頼できるステーションの証明書を格納するパスを設定します
- `_GW_PNFP` は、発行する構成ファイルのファイル名を設定します

コマンドライン引数は、環境変数の設定を変更します。

通常、アプリケーションを最初に開始するときにのみ、IoT Hub 所有者の接続文字列を指定します。 この接続文字列は暗号化され、プラットフォームの証明書ストアに格納されます。

後続の呼び出しでは、この接続文字列がプラットフォームの証明書ストアから読み取られ、再利用されます。 開始時に接続文字列を指定する場合は、IoT Hub デバイス レジストリ内のデバイスは毎回削除され再作成されます。

### <a name="native-on-windows"></a>Windows ネイティブ

Windows でネイティブ アプリケーションを実行するには、Visual Studio 2017 で `OpcPublisher.sln` プロジェクトを開き、ソリューションを構築して発行します。 次のコマンドを使用して、発行先の**ターゲット ディレクトリ**でアプリケーションを開始できます。

```cmd
dotnet OpcPublisher.dll <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="use-a-self-built-container"></a>自作コンテナーの使用

自作コンテナーでアプリケーションを実行するには、独自のコンテナーを構築してから開始します。

```cmd/sh
docker run <your-container-name> <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="use-a-container-from-hubdockercom"></a>hub.docker.com のコンテナーの使用

DockerHub にある構築済みのコンテナーを使用できます。 開始するには、次のコマンドを実行します。

```cmd/sh
docker run microsoft/iot-edge-opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="important-when-using-a-container"></a>コンテナー使用時の重要事項

#### <a name="access-to-the-publisher-opc-ua-server"></a>Publisher OPC UA サーバーへのアクセス

Publisher OPC UA サーバーは、既定でポート 62222 をリッスンします。 コンテナーのこの受信ポートを公開するには、`docker run` の `-p` オプションを使用する必要があります。

```cmd/sh
docker run -p 62222:62222 microsoft/iot-edge-opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

#### <a name="enable-intercontainer-name-resolution"></a>コンテナー間の名前解決の有効化

コンテナー内から他のコンテナーへの名前解決を有効にするには、次のことを行う必要があります。

- ユーザー定義の Docker ブリッジ ネットワークを作成する。
- `--network` オプションを使用して、コンテナーをネットワークに接続する。
- `--name` オプションを使用して、コンテナーに名前を割り当てる。

次の例では、これらの構成オプションを示します。

```cmd/sh
docker network create -d bridge iot_edge
docker run --network iot_edge --name publisher microsoft/iot-edge-opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

`publisher` という名前を使用して、このコンテナーにはネットワーク経由で他のコンテナーからアクセスできるようになりました。

#### <a name="assign-a-hostname"></a>ホスト名の割り当て

パブリッシャーは、証明書とエンドポイントを生成するために、実行中のマシンのホスト名を使用します。 `-h` オプションを使用してホスト名を設定しないかぎり、Docker はランダム ホスト名を選択します。 コンテナーの内部のホスト名を `publisher` に設定する例を次に示します。

```cmd/sh
docker run -h publisher microsoft/iot-edge-opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

#### <a name="using-bind-mounts-shared-filesystem"></a>バインド マウント (共有ファイル システム) の使用

コンテナー ファイル システムを使用せずに、ホスト上の場所から構成情報を読み込んだり、ホスト上の場所にログ ファイルを書き込んだりしたい場合があります。 この動作を構成するには、バインド マウント モードで `docker run` の `-v` オプションを使用します。 例: 

```cmd/sh
-v //D/docker:/build/out/Logs
-v //D/docker:/build/out/CertificateStores
-v //D/docker:/shared
-v //D/docker:/root/.dotnet/corefx/cryptography/x509stores
```

#### <a name="store-for-x509-certificates"></a>X509 証明書の格納

バインド マウント モードでは X509 証明書を格納できません。これは、ユーザーに対するストアへのパスのアクセス許可が、`rw` である必要があるためです。 代わりに、ボリューム モードで `docker run` の `-v` オプションを使用する必要があります。

## <a name="debug-the-application"></a>アプリケーションのデバッグ

### <a name="native-on-windows"></a>Windows ネイティブ

Visual Studio 2017 で `OpcPublisher.sln` プロジェクトを開き、F5 キーを押してアプリのデバッグを開始します。

### <a name="in-a-docker-container"></a>Docker コンテナー内で

Visual Studio 2017 では、`docker-compose` を使用した、Docker コンテナー内でのアプリケーションのデバッグがサポートされています。 ただし、この方法ではコマンド ライン パラメーターを渡すことができません。

VS2017 でサポートされている別のデバッグ オプションは、`ssh` を使用したデバッグです。 リポジトリのルートにある Docker ビルド構成ファイルの `Dockerfile.ssh` を使用して、SSH を有効化したコンテナーを作成できます。

```cmd/sh
docker build -f .\Dockerfile.ssh -t publisherssh .
```

コンテナーを開始して、パブリッシャーをデバッグできるようになりました。

```cmd/sh
docker run -it publisherssh
```

コンテナーでは、手動で **ssh** デーモンを開始する必要があります。

```cmd/sh
service ssh start
```

この時点では、パスワード `Passw0rd` を使用して、`root` ユーザーとして **ssh** セッションを作成できます。

コンテナー内でアプリケーションをデバッグする準備をするには、次の追加手順を実行します。

1. ホスト側で、`launch.json` ファイルを作成します。

    ```json
    {
      "version": "0.2.0",
      "adapter": "<path>\\plink.exe",
      "adapterArgs": "root@localhost -pw Passw0rd -batch -T ~/vsdbg/vsdbg --interpreter=vscode",
      "languageMappings": {
        "C#": {
          "languageId": "3F5162F8-07C6-11D3-9053-00C04FA302A1",
          "extensions": [ "*" ]
        }
      },
      "exceptionCategoryMappings": {
        "CLR": "449EC4CC-30D2-4032-9256-EE18EB41B62B",
        "MDA": "6ECE07A9-0EDE-45C4-8296-818D8FC401D4"
      },
      "configurations": [
        {
          "name": ".NET Core Launch",
          "type": "coreclr",
          "cwd": "~/publisher",
          "program": "Opc.Ua.Publisher.dll",
          "args": "<put-the-publisher-command-line-options-here>",

          "request": "launch"
        }
      ]
    }
    ```

1. プロジェクトを構築し、任意のディレクトリにそのプロジェクトを発行します。

1. `WinSCP` などのツールを使用して、発行済みファイルをコンテナー内の `/root/publisher` ディレクトリにコピーします。 別のディレクトリを使用する場合は、`launch.json` ファイルの `cdw` プロパティを更新します。

Visual Studio のコマンド ウィンドウで次のコマンドを使用して、デバッグを開始できます。

```cmd
DebugAdapterHost.Launch /LaunchJson:"<path-to-the-launch.json-file-you-saved>"
```

## <a name="next-steps"></a>次の手順

おすすめの次のステップは、[構成済みのコネクテッド ファクトリ ソリューション用のゲートウェイを Windows または Linux 上にデプロイする](iot-suite-connected-factory-gateway-deployment.md)方法の学習です。