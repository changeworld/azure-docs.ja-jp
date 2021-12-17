---
title: gRPC 推論サーバーを開発およびデプロイする - Azure Video Analyzer
description: この記事では、Azure Video Analyzer で使用する gRPC 推論サーバーを開発してデプロイする方法について説明します。
ms.service: azure-video-analyzer
ms.topic: how-to
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 344f4772bc2c48612c6f8ee62cac9d22a35f321b
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132284534"
---
# <a name="develop-and-deploy-grpc-inference-server"></a>gRPC 推論サーバーを開発およびデプロイする

[!INCLUDE [header](includes/edge-env.md)]

この記事では、パイプライン拡張機能を介して Azure Video Analyzer (AVA) と統合できるように、gRPC 推論サーバー内で任意の AI モデルをラップする方法を示します。

## <a name="suggested-pre-reading"></a>先に読んでおくことが推奨される記事

* パイプライン拡張機能
* gRPC 拡張プロトコル
* 推論メタデータ スキーマ
* [gRPC の概要](https://www.grpc.io/docs/what-is-grpc/introduction/)
* [proto3 言語ガイド](https://developers.google.com/protocol-buffers/docs/proto3)

## <a name="prerequisites"></a>前提条件

* [サポートされている Linux オペレーティング システム](../../../iot-edge/support.md#operating-systems)のいずれかを実行している x86-64 または ARM64 デバイス、または Windows マシン。
* マシンに [Docker がインストール](https://docs.docker.com/desktop/#download-and-install)されている。
* [IoT Edge ランタイム](../../../iot-edge/how-to-install-iot-edge.md?tabs=linux)がインストールされている。

## <a name="grpc-implementation-steps"></a>gRPC の実装手順

gRPC 推論サーバーを作成し、それを拡張機能として Video Analyzer と共に実装するには、次の手順を使用します。

### <a name="setup-video-analyzer-module"></a>Video Analyzer モジュールを設定する

Video Analyzer モジュールを IoT Edge デバイスにデプロイして動作させるために必要な手順を行います。

### <a name="high-level-implementation-steps"></a>基本的な導入手順

1. C#、C++、Dart、Go、Java、Node、Objective-C、PHP、Python、Ruby など、gRPC でサポートされている多くの言語からいずれかを選択します。
1. [proto3 ファイル](https://github.com/Azure/video-analyzer/tree/main/contracts/grpc)を使用して Video Analyzer と通信する gRPC サーバーを実装します。

    :::image type="content" source="./media/develop-deploy-grpc-inference-srv/inference-srv-container-process.svg" alt-text="proto3 ファイルを使用して Video Analyzer と通信する gRPC サーバー":::

    このサービス内:
    1. サーバーとクライアント間のセッション説明メッセージの交換を処理します。
    1. [サンプル メッセージ](https://github.com/Azure/video-analyzer/tree/main/contracts/grpc/extension.proto)を処理し、結果を返します。

        1. トレーニング済みのモデルを使用して受信メッセージを推論する推論エンジンを呼び出します。
        1. エンジンから推論の結果を受信し、それらをメディア サンプルとしてパッケージ化して、[inferencing.proto](https://github.com/Azure/video-analyzer/tree/main/contracts/grpc/inferencing.proto) ファイルを使用して Video Analyzer に送り返します。

            または、メディア変換機能をメディア サンプルに呼び出します。
    1. gRPC サーバー実装をデプロイします。 これを行うには、次の 2 つの方法があります。

        1. Video Analyzer モジュールと併置されている IoT モジュールとしてデプロイする
        1. Video Analyzer モジュールとデータを交換できる、ネットワーク アクセス可能なノード (オンプレミスまたはクラウド上) への IoT モジュールとしてデプロイします。
    1. Video Analyzers モジュールを使用して Video Analyzer パイプライン トポロジを構成し、それを gRPC サーバーに向けます。

### <a name="recommendation"></a>推奨

同じノードで併置すると、`shared memory` を使用して、最適なパフォーマンスを達成できます。 これには、プログラミング言語/環境によって公開された Linux 共有メモリ機能を使用する必要があります。

1. Linux 共有メモリ ハンドルを開きます。
1. フレームを受信したときに、共有メモリ内のアドレス オフセットにアクセスします。
1. Video Analyzer でメモリを再利用できるように、フレーム処理の完了を確認します。

> [!NOTE]
> 共有メモリがある推論に gRPC 拡張モジュールを使用する場合、Video Analyzer エッジ モジュールと拡張モジュールの両方を同じ[ユーザーとグループ](https://docs.docker.com/engine/reference/builder/#user)で実行する必要があります。

## <a name="create-a-grpc-inference-server"></a>gRPC 推論サーバーを作成する

次に、共有メモリ経由で [protobuf](https://github.com/Azure/video-analyzer/tree/main/contracts/grpc) メッセージを使用して Video Analyzer からビデオ フレームを受け入れる IoT Edge モジュール (外部 AI) を構築し、フレームを **濃色** または **薄色** として分類し、推論メタデータ スキーマを使って、Video Analyzer の IoT Hub メッセージ シンクに推論結果を返します。

:::image type="content" source="./media/develop-deploy-grpc-inference-srv/external-ai.png" alt-text="IoT Edge モジュール (外部 AI) を構築する":::

この gRPC 推論サーバーは、Video Analyzer とカスタム AI の間で送信される [protobuf](https://github.com/Azure/video-analyzer/tree/main/contracts/grpc) メッセージを処理するために構築された .NET Core コンソール アプリケーションです。 以下に、Video Analyzer と gRPC 推論サーバーとの間のメッセージ フローを示します。

1. Video Analyzer により、メディア ストリーム情報を定義するメディア ストリーム記述子が送信されます ([extension.proto](https://github.com/Azure/video-analyzer/tree/main/contracts/grpc/extension.proto) を参照)。この情報にはビデオ フレームが続いて、gRPC ストリーム セッションで [protobuf](https://github.com/Azure/video-analyzer/tree/main/contracts/grpc) メッセージとしてサーバーに送信されます。
1. サーバーは、ストリーム記述子の検証と確認応答を行い、目的のデータ転送方法を設定します。
1. その後、Video Analyzer では、ビデオ フレームを含む MediaSample ファイルの送信を開始します。
1. サーバーでは、受信時にビデオ フレームが分析され、ユーザーが定義したイメージ プロセッサを使用してそれらの処理が開始されます。
1. サーバーは続いて、推論結果が使用可能になるとすぐに、[protobuf](https://github.com/Azure/video-analyzer/tree/main/contracts/grpc) メッセージとしてこれを返します。

    :::image type="content" source="./media/develop-deploy-grpc-inference-srv/grpc-external-srv.png" alt-text="gRPC 推論サーバーを作成する":::

ビデオ フレームは、[共有メモリ](https://en.wikipedia.org/wiki/Shared_memory#:~:text=In%20computer%20science%2C%20shared%20memory,of%20passing%20data%20between%20programs.)で転送することも、protobuf メッセージに埋め込むこともできます。 データ転送モードは、フレームがどのように転送されるかを特定するように、AVA パイプライン トポロジで構成できます。 これを実現するには、以下に示すように、`GrpcExtension` プロパティの **dataTransfer** 要素を構成します。

埋め込み:

```
"dataTransfer": {
              "mode": "Embedded"
            }
```

共有メモリ:

```
"dataTransfer": {
              "mode": "sharedMemory",
              "SharedMemorySizeMiB": "20"
            }
```

> [!NOTE]
> 共有メモリで通信する場合は、IpcMode の値を **shareable** に設定し、gRPC サーバー モジュールで IpcMode の値を **container:{CONTAINER_NAME}** に設定する必要があります。 これらの設定は、モジュールを Azure IoT Hub にデプロイするために使用される配置マニフェスト ファイルで行います。 次に、IoT Edge モジュールを設定するときに使用するコンテナー オプションの例を示します。

Azure Video Analyzer モジュール:

```
{
    "HostConfig": {
        "LogConfig": {
            "Config": {
                "max-size": "10m",
                "max-file": "10"
            }
        },
        "IpcMode": "shareable"
    }
}
```

gRPC 拡張機能モジュール:

```
{
    "HostConfig": {
        "LogConfig": {
            "Config": {
                "max-size": "10m",
                "max-file": "10"
            }
        },
        "IpcMode": "container:avaedge"
    }
}
```

> [!NOTE]
> grpcExtension 内の **container:avaedge** の共有メモリ領域に確実にアクセスできるようにします。

## <a name="sample-grpc-server"></a>gRPC サーバーのサンプル

gRPC サーバーを開発する方法の詳細を理解するには、コード サンプルを参照してください。

1. GitHub リンク [https://github.com/Azure-Samples/video-analyzer-iot-edge-csharp](https://github.com/Azure-Samples/video-analyzer-iot-edge-csharp) からリポジトリをクローンします。
1. VSCode を起動し、/src/edge/modules/grpcExtension フォルダーに移動します。
1. ファイルの簡単なチュートリアルを実行してみましょう。

    1. **Program.cs**: これはアプリケーションのエントリ ポイントです。 これは、ホストとして機能する gRPC サーバーの初期化と管理を担当します。 このサンプルでは、gRPC クライアント (Azure Video Analyzer など) からの受信 gRPC メッセージをリッスンするポートは、appConfig.json の grpcBindings 構成要素で指定されています。

        ```json
        {
          "grpcBinding": "tcp://0.0.0.0:5001"
        }
        ```
    1. **Services\PipelineExtensionService.cs**: このクラスは、[protobuf](https://github.com/Azure/video-analyzer/tree/main/contracts/grpc) メッセージの処理を担当します。 メッセージ内のフレームが読み取られ、ImageProcessor が呼び出され、推論結果が書き込まれます。
      gRPC サーバーのポート接続を構成し初期化したので、次は着信 gRPC メッセージを処理する方法について説明します。

        1. gRPC セッションが確立されると、gRPC サーバーによりクライアント (Azure Video Analyzer) から受信される最初のメッセージ は、[extension.proto](https://github.com/Azure/video-analyzer/tree/main/contracts/grpc/extension.proto) ファイルで定義されている MediaStreamDescriptor となります。

            ```
            message MediaStreamDescriptor {
              oneof stream_identifier {
                GraphIdentifier graph_identifier = 1;     // Media Stream graph identifier
                string extension_identifier = 2;          // Media Stream extension identifier
              }
              MediaDescriptor media_descriptor = 5;       // Session media information.
              // Additional data transfer properties. If none is set, it is assumed
              // that all content will be transferred through messages (embedded transfer).
              oneof data_transfer_properties {
                SharedMemoryBufferTransferProperties shared_memory_buffer_transfer_properties = 10;
              }
            }
            ```
        1. このサーバー実装では、メソッド `ProcessMediaStreamDescriptor` によって、ビデオ ファイルの MediaStreamDescriptor の MediaDescriptor プロパティが検証され、トポロジでの指定内容と使用されるデプロイ テンプレート ファイルに応じて、データ転送モード (共有メモリを使用するか、埋め込みフレーム転送モードを使用するか) が設定されます。
        1. gRPC サーバーは、メッセージを受信し、データ転送モードを正しく設定すると、クライアントに確認応答として MediaStreamDescriptor メッセージを返すので、サーバーとクライアント間の接続が確立されます。
        1. Video Analyzer では、受信確認を受け取った後、メディア ストリームを gRPC サーバーに転送し始めます。 このサーバー実装では、メソッド `ProcessMediaStream` が受信 MediaStreamMessage を処理します。 また、MediaStreamMessage は [extension.proto](https://github.com/Azure/video-analyzer/tree/main/contracts/grpc/extension.proto) で定義されています。

            ```
            message MediaStreamMessage {
              uint64 sequence_number = 1;       // Monotonically increasing directional message identifier starting from 1 when the gRPC connection is created
              uint64 ack_sequence_number = 2;   // 0 if this message is not referencing any sent message.


              // Possible payloads are strongly defined by the contract below
              oneof payload {
                MediaStreamDescriptor media_stream_descriptor = 5;
                MediaSample media_sample = 6;
              }
            }
            ```
        1. *appconfig.json* の `batchSize` の値に応じて、サーバーではメッセージを受信し続け、ビデオ フレームを一覧に格納します。 batchSize の制限に達すると、この関数は、イメージを処理する関数またはファイルを呼び出します。 ここでは、メソッドで **BatchImageProcessor.cs** というファイルを呼び出します。
    1. **Processors\BatchImageProcessor.cs**:このクラスは、イメージの処理を担当します。 このサンプルでは、イメージ分類モデルを使用しました。 処理されるすべてのイメージに対して次のアルゴリズムが使用されます。

        1. 処理のために、バイト配列内のイメージを変換します。 次のメソッドを参照してください: `GetBytes(Bitmap image)`

            現在使用しているサンプル プロセッサは、JPG でエンコードされたイメージ フレームのみをサポートしており、ピクセル形式ではサポートしていません。 カスタム プロセッサが別のエンコードや形式をサポートしている場合は、プロセッサ クラスの `IsMediaFormatSupported` メソッドを更新します。
        1. [ColorMatrix クラス](/dotnet/api/system.drawing.imaging.colormatrix?preserve-view=true&view=dotnet-plat-ext-3.1)を使用して、画像をグレー スケールに変換します。 次のメソッドを参照してください: `ToGrayScale(Image source)`。
        1. グレー スケールの画像を取得した後、グレー スケールの平均バイト数を計算します。
        1. 平均値が 127 未満の場合は、画像を "濃色" として分類し、それ以外の場合は、信頼度の値を 1.0 として "薄色" として分類します。 次のメソッドを参照してください: `ProcessImage(List<Image> images)`。

    独自のプロセッサ ロジックを追加するには、このクラスを変更するか、新しいクラスを追加してメソッドを実装します。

    ```
    IEnumerable<Inference> ProcessImage(List<Image> images)
    ```

    新しいクラスを追加したら、**PipelineExtensionService.cs** を更新してクラスをインスタンス化し、ProcessImage メソッドを呼び出して処理ロジックを実行する必要があります。

## <a name="connect-with-video-analyzer-module"></a>Video Analyzer モジュールと接続する

gRPC 拡張モジュールを作成したので、次はパイプライン トポロジを作成してデプロイします。

1. Visual Studio Code を使用し、[こちらの手順](../../../iot-edge/tutorial-develop-for-linux.md#build-and-push-your-solution)に従って Docker にサインインします。
1. Visual Studio Code で、*src/edge* に移動します。 *.env* ファイルと、いくつかの展開テンプレート ファイルがあります。

    **展開テンプレート** では、エッジ デバイスの配置マニフェストが参照されます。 そこには、いくつかのプレースホルダー値が含まれています。 .env ファイルには、それらの変数の値が格納されています。

    次に、 **[Build and Push IoT Edge Solution]\(IoT Edge ソリューションをビルドしてプッシュする\)** を選択します。 この手順では *src/edge/deployment.grpc.template.json* を使用します。

    :::image type="content" source="./media/develop-deploy-grpc-inference-srv/build-push-iot-edge-solution.png" alt-text="Video Analyzer モジュールと接続する":::

    このアクションにより、gRPC サーバー モジュールがビルドされ、お使いの Azure Container Registry に画像がプッシュされます。
    *.env* ファイルに環境変数 `CONTAINER_REGISTRY_USERNAME_myacr` および `CONTAINER_REGISTRY_PASSWORD_myacr` が定義されていることを確認します。
1. *src/cloud-to-device-console-app* フォルダーに移動します。 ここには、*appsettings.json* ファイルと、他にいくつかのファイルがあります。

    * **c2d-console-app.csproj** - Visual Studio Code のプロジェクト ファイルです。
    * **operations.json** - プログラムで実行する操作のリストです。
    * **Program.cs** - サンプル プログラム コードです。 このコードによって以下が行われます。

        * アプリ設定を読み込みます。
        * Azure Video Analyzer モジュールによって公開されているダイレクト メソッドを呼び出します。 このモジュールを使用し、そのダイレクト メソッドを呼び出すことで、ライブ ビデオ ストリームを分析できます。
        * プログラムの出力を [ターミナル] ウィンドウで調べたり、モジュールによって生成されたイベントを [出力] ウィンドウで調べたりできるように、一時停止します。
        * ダイレクト メソッドを呼び出して、リソースをクリーンアップします。
1. operations.json ファイルを編集します。

    * パイプライン トポロジへのリンクを変更します。

        * `"topologyUrl" : https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/evr-grpcExtension-video-sink/topology.json`
        * `livePipelineSet` で、前のリンクの値と一致するようにパイプライン トポロジの名前を編集します。<br/>`"topologyName": "EVRtoVideoSinkByGrpcExtension"`
        * `pipelineTopologyDelete` で、名前を編集します。<br/>`"name": "EVRtoVideoSinkByGrpcExtension"`

    トポロジでは拡張機能アドレスを定義する必要があります。 たとえば、パラメーターを作成し、GrpcExtension ノード内でそれを使用できます。
    * 拡張機能アドレス パラメーター

        ```
        {
            "name": "grpcExtensionAddress",
            "type": "String",
            "description": "gRPC AVA Extension Address",
            "default": "https://<REPLACE-WITH-IP-OR-CONTAINER-NAME>/score"
        },
        ```
    * 構成

        ```
        {
            "@apiVersion": "1.1",
            "name": "TopologyName",
            "properties": {
            "processors": [
              {
                "@type": "#Microsoft.VideoAnalyzer.GrpcExtension",
                "name": "grpcExtension",
                "endpoint": {
                  "@type": "#Microsoft.VideoAnalyzer.UnsecuredEndpoint",
                  "url": "${grpcExtensionAddress}",
                  "credentials": {
                    "@type": "#Microsoft.VideoAnalyzer.UsernamePasswordCredentials",
                    "username": "${grpcExtensionUserName}",
                    "password": "${grpcExtensionPassword}"
                  }
                },
                "dataTransfer": {
                        "mode": "sharedMemory",
                        "SharedMemorySizeMiB": "75"
                },
                "image": {
                  "scale": {
                    "mode": "${imageScaleMode}",
                    "width": "${frameWidth}",
                    "height": "${frameHeight}"
                  },
                  "format": {
                    "@type": "#Microsoft.VideoAnalyzer.ImageFormatEncoded",
                    "encoding": "${imageEncoding}",
                    "quality": "${imageQuality}"
                  }
                }
            ]
          }
        }
        ```

## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>IoT Edge の配置マニフェストを生成してデプロイする

エッジ デバイスにデプロイされるモジュールとそれらのモジュールの構成設定は、配置マニフェストによって定義されます。 マニフェストをテンプレート ファイルから生成して、エッジ デバイスにデプロイするには、次の手順に従います。
1. この手順により、*src/edge/config/deployment.grpc.amd64.json* に IoT Edge 配置マニフェストが作成されます。 そのファイルを右クリックし、 **[単一デバイスのデプロイを作成する]** を選択します。

    :::image type="content" source="./media/develop-deploy-grpc-inference-srv/create-deployment-single-device.png" alt-text="IoT Edge の配置マニフェストを生成してデプロイする":::

1. 次に、IoT Hub デバイスを選択するように Visual Studio Code から求められます。 ご自分の IoT Hub デバイス (`avasample-iot-edge-device`) を選択します。
この段階で、お使いの IoT Edge デバイスへのエッジ モジュールのデプロイが開始されました。 30 秒ほど経過したら、Visual Studio Code の左下のセクションで Azure IoT Hub を最新の情報に更新してください。 avaextension という名前の新しいモジュールがデプロイされたことがわかるはずです。

:::image type="content" source="./media/develop-deploy-grpc-inference-srv/devices.png" alt-text="avaextension という名前の新しいモジュールがデプロイされた":::

## <a name="next-steps"></a>次の手順

* お使いのモデルでのライブ ビデオ クイックスタートで述べられた **イベントの監視の準備をする** 手順に従って、サンプルを実行し、結果を解釈します。
* [gRPCExtension](https://github.com/Azure/video-analyzer/tree/main/pipelines/live/topologies/grpcExtensionOpenVINO/topology.json)、[CVRWithGrpcExtension](https://github.com/Azure/video-analyzer/tree/main/pipelines/live/topologies/cvr-with-grpcExtension/topology.json)、[EVRtoAssetsByGrpcExtension](https://github.com/Azure/video-analyzer/tree/main/pipelines/live/topologies/evr-grpcExtension-video-sink/topology.json)、[EVROnMotionPlusGrpcExtension](https://github.com/Azure/video-analyzer/tree/main/pipelines/live/topologies/motion-with-grpcExtension/topology.json) などの gRPC トポロジのサンプルを確認してください。
